---
title: Вспомогательная функция тега привязки в ASP.NET Core MVC
author: pkellner
description: Обнаруживайте атрибуты вспомогательной функции тега привязки ASP.NET Core и роль, которую играет каждый атрибут в расширении поведения тега привязки HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
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
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 2e49c545b0d343475ce44a636a6ae66324f9d3bf
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587207"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="6ff65-103">Вспомогательная функция тега привязки в ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6ff65-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="6ff65-104">Авторы: [Питер Кельнер (Peter Kellner)](https://peterkellner.net) и [Скотт Эдди](https://github.com/scottaddie) (Scott Addie).</span><span class="sxs-lookup"><span data-stu-id="6ff65-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="6ff65-105">[Вспомогательная функция тега привязки](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) повышает эффективность стандартного тега привязки HTML (`<a ... ></a>`) путем добавления новых атрибутов.</span><span class="sxs-lookup"><span data-stu-id="6ff65-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="6ff65-106">Как правило, все имена атрибутов начинаются с `asp-`.</span><span class="sxs-lookup"><span data-stu-id="6ff65-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="6ff65-107">Отображаемое значение атрибута `href` элемента привязки определяется значениями атрибутов `asp-`.</span><span class="sxs-lookup"><span data-stu-id="6ff65-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="6ff65-108">Общие сведения о вспомогательных функциях тегов см. здесь: <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="6ff65-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="6ff65-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ff65-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6ff65-110">В примерах в этом документе используется *SpeakerController*</span><span class="sxs-lookup"><span data-stu-id="6ff65-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="6ff65-111">Атрибуты вспомогательной функции тега привязки</span><span class="sxs-lookup"><span data-stu-id="6ff65-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="6ff65-112">asp-controller</span><span class="sxs-lookup"><span data-stu-id="6ff65-112">asp-controller</span></span>

<span data-ttu-id="6ff65-113">Атрибут [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) назначает контроллер, используемый для создания URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="6ff65-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="6ff65-114">Следующий элемент перечисляет всех говорящих:</span><span class="sxs-lookup"><span data-stu-id="6ff65-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="6ff65-115">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="6ff65-116">Если атрибут `asp-controller` указан, а атрибут `asp-action` — нет, действием контроллера, связанным с выполняющимся представлением, будет заданное по умолчанию значение `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="6ff65-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="6ff65-117">Если `asp-action` исключается из предыдущего исправления, а в представлении *Index* (*/Home*) *HomeController* используется вспомогательная функция тега привязки, создается следующий код HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="6ff65-118">asp-action</span><span class="sxs-lookup"><span data-stu-id="6ff65-118">asp-action</span></span>

<span data-ttu-id="6ff65-119">Значение атрибута [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) представляет имя действия контроллера, включенное в созданный атрибут `href`.</span><span class="sxs-lookup"><span data-stu-id="6ff65-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="6ff65-120">Следующий элемент задает созданное значение атрибута `href` на странице динамика оценок:</span><span class="sxs-lookup"><span data-stu-id="6ff65-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="6ff65-121">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="6ff65-122">Если атрибут `asp-controller` не указан, будет использоваться контроллер по умолчанию, вызывающий представление при выполнении текущего представления.</span><span class="sxs-lookup"><span data-stu-id="6ff65-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="6ff65-123">Если атрибут `asp-action` имеет значение `Index`, действия не добавляются к URL-адресу и вызывается метод `Index` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6ff65-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="6ff65-124">В контроллере, на который есть ссылка в `asp-controller`, должно существовать указанное действие (или заданное по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="6ff65-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="6ff65-125">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="6ff65-125">asp-route-{value}</span></span>

<span data-ttu-id="6ff65-126">Атрибут [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) включает подстановочный префикс маршрута.</span><span class="sxs-lookup"><span data-stu-id="6ff65-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="6ff65-127">Любое значение, подставляемое вместо `{value}`, рассматривается как потенциальный параметр маршрута.</span><span class="sxs-lookup"><span data-stu-id="6ff65-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="6ff65-128">Если маршрут по умолчанию не найден, этот префикс маршрута будет добавлен к созданному атрибуту `href` в виде параметра запроса и значения.</span><span class="sxs-lookup"><span data-stu-id="6ff65-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="6ff65-129">В противном случае он будет заменен в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="6ff65-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="6ff65-130">Рассмотрим следующее действие контроллера:</span><span class="sxs-lookup"><span data-stu-id="6ff65-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="6ff65-131">С шаблоном маршрута по умолчанию, определенным в *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="6ff65-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="6ff65-132">Представление MVC использует модель, предоставляемую действием:</span><span class="sxs-lookup"><span data-stu-id="6ff65-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="6ff65-133">Заполнитель `{id?}` маршрута по умолчанию соответствует требуемому.</span><span class="sxs-lookup"><span data-stu-id="6ff65-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="6ff65-134">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="6ff65-135">Предположим, префикс маршрута не входит в состав соответствующего шаблона маршрутизации, как в случае со следующим представлением MVC:</span><span class="sxs-lookup"><span data-stu-id="6ff65-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="6ff65-136">Следующий код HTML будет создан, так как элемент `speakerid` не найден в соответствующем маршруте:</span><span class="sxs-lookup"><span data-stu-id="6ff65-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="6ff65-137">Если атрибут `asp-controller` или `asp-action` не указан, применяется та же обработка по умолчанию, что и в атрибуте `asp-route`.</span><span class="sxs-lookup"><span data-stu-id="6ff65-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="6ff65-138">asp-route</span><span class="sxs-lookup"><span data-stu-id="6ff65-138">asp-route</span></span>

<span data-ttu-id="6ff65-139">Атрибут [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) используется для связывания URL-адреса непосредственно с именованным маршрутом.</span><span class="sxs-lookup"><span data-stu-id="6ff65-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="6ff65-140">С помощью [атрибутов маршрутизации](xref:mvc/controllers/routing#attribute-routing) маршруту можно присвоить имя, как показано в классе `SpeakerController` и используется в его действии `Evaluations`:</span><span class="sxs-lookup"><span data-stu-id="6ff65-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="6ff65-141">В следующем элементе атрибут `asp-route` ссылается на именованный маршрут:</span><span class="sxs-lookup"><span data-stu-id="6ff65-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="6ff65-142">Вспомогательная функция тега привязки создает маршрут непосредственно к этому методу контроллера, используя */Speaker/Evaluations* URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="6ff65-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="6ff65-143">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="6ff65-144">Если наряду с атрибутом `asp-route` указаны атрибут `asp-controller` или `asp-action`, созданный маршрут может отличаться от ожидаемого.</span><span class="sxs-lookup"><span data-stu-id="6ff65-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="6ff65-145">Во избежание конфликта маршрута `asp-route` нельзя использовать вместе с атрибутами `asp-controller` или `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="6ff65-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="6ff65-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="6ff65-146">asp-all-route-data</span></span>

<span data-ttu-id="6ff65-147">Атрибут [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) поддерживает создание словаря пар "ключ-значение".</span><span class="sxs-lookup"><span data-stu-id="6ff65-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="6ff65-148">Ключ является именем параметра, а значение — значением параметра.</span><span class="sxs-lookup"><span data-stu-id="6ff65-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="6ff65-149">В следующем примере словарь инициализируется и передается в Razor представление.</span><span class="sxs-lookup"><span data-stu-id="6ff65-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="6ff65-150">Кроме того, данные могут быть переданы с помощью модели.</span><span class="sxs-lookup"><span data-stu-id="6ff65-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="6ff65-151">Предыдущий код вызывает следующий код HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="6ff65-152">Словарь `asp-all-route-data` предназначен для создания строки запроса, соответствующей требованиям перегруженного действия `Evaluations`:</span><span class="sxs-lookup"><span data-stu-id="6ff65-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="6ff65-153">Если ключи в словаре совпадают с параметрами маршрута, эти значения будут соответствующим образом заменены в маршруте.</span><span class="sxs-lookup"><span data-stu-id="6ff65-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="6ff65-154">В качестве параметров запроса будут созданы другие несовпадающие значения.</span><span class="sxs-lookup"><span data-stu-id="6ff65-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="6ff65-155">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="6ff65-155">asp-fragment</span></span>

<span data-ttu-id="6ff65-156">Атрибут [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) определяет фрагмент URL-адреса, добавляемый к URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="6ff65-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="6ff65-157">Вспомогательная функция тега привязки добавляет символ решетки (#).</span><span class="sxs-lookup"><span data-stu-id="6ff65-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="6ff65-158">Рассмотрим следующий элемент:</span><span class="sxs-lookup"><span data-stu-id="6ff65-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="6ff65-159">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="6ff65-160">Хэштеги полезны при создании приложений на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="6ff65-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="6ff65-161">Например, их можно использовать для простоты пометки и поиска на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ff65-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="6ff65-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="6ff65-162">asp-area</span></span>

<span data-ttu-id="6ff65-163">Атрибут [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) указывает имя области, используемое платформой для определения соответствующего маршрута.</span><span class="sxs-lookup"><span data-stu-id="6ff65-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="6ff65-164">Ниже приведен пример того, как атрибут `asp-area` приводит к повторному сопоставлению маршрутов.</span><span class="sxs-lookup"><span data-stu-id="6ff65-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-razor-pages"></a><span data-ttu-id="6ff65-165">Использование на Razor страницах</span><span class="sxs-lookup"><span data-stu-id="6ff65-165">Usage in Razor Pages</span></span>

<span data-ttu-id="6ff65-166">Razor Области страниц поддерживаются в ASP.NET Core 2,1 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="6ff65-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="6ff65-167">Пусть имеется следующая иерархия каталогов:</span><span class="sxs-lookup"><span data-stu-id="6ff65-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="6ff65-168">**{Имя проекта}**</span><span class="sxs-lookup"><span data-stu-id="6ff65-168">**{Project name}**</span></span>
  * <span data-ttu-id="6ff65-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="6ff65-169">**wwwroot**</span></span>
  * <span data-ttu-id="6ff65-170">**Области**</span><span class="sxs-lookup"><span data-stu-id="6ff65-170">**Areas**</span></span>
    * <span data-ttu-id="6ff65-171">**Сеансы**</span><span class="sxs-lookup"><span data-stu-id="6ff65-171">**Sessions**</span></span>
      * <span data-ttu-id="6ff65-172">**Страницы**</span><span class="sxs-lookup"><span data-stu-id="6ff65-172">**Pages**</span></span>
        * <span data-ttu-id="6ff65-173">*\_Виевстарт. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6ff65-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="6ff65-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6ff65-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="6ff65-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6ff65-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="6ff65-176">**Страницы**</span><span class="sxs-lookup"><span data-stu-id="6ff65-176">**Pages**</span></span>

<span data-ttu-id="6ff65-177">Разметка для ссылки на страницу *индекса* области *сеансов* Razor :</span><span class="sxs-lookup"><span data-stu-id="6ff65-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="6ff65-178">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="6ff65-179">Для поддержки областей в Razor приложении для страниц выполните одно из следующих `Startup.ConfigureServices` действий в.</span><span class="sxs-lookup"><span data-stu-id="6ff65-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="6ff65-180">Задайте [версию совместимости](xref:mvc/compatibility-version) 2.1 или более позднюю.</span><span class="sxs-lookup"><span data-stu-id="6ff65-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="6ff65-181">Задайте для свойства [ Razor Пажесоптионс. алловареас](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) значение `true` :</span><span class="sxs-lookup"><span data-stu-id="6ff65-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="6ff65-182">Использование в MVC</span><span class="sxs-lookup"><span data-stu-id="6ff65-182">Usage in MVC</span></span>

<span data-ttu-id="6ff65-183">Пусть имеется следующая иерархия каталогов:</span><span class="sxs-lookup"><span data-stu-id="6ff65-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="6ff65-184">**{Имя проекта}**</span><span class="sxs-lookup"><span data-stu-id="6ff65-184">**{Project name}**</span></span>
  * <span data-ttu-id="6ff65-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="6ff65-185">**wwwroot**</span></span>
  * <span data-ttu-id="6ff65-186">**Области**</span><span class="sxs-lookup"><span data-stu-id="6ff65-186">**Areas**</span></span>
    * <span data-ttu-id="6ff65-187">**Блоги**</span><span class="sxs-lookup"><span data-stu-id="6ff65-187">**Blogs**</span></span>
      * <span data-ttu-id="6ff65-188">**Контроллеры**</span><span class="sxs-lookup"><span data-stu-id="6ff65-188">**Controllers**</span></span>
        * <span data-ttu-id="6ff65-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="6ff65-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="6ff65-190">**Представления**</span><span class="sxs-lookup"><span data-stu-id="6ff65-190">**Views**</span></span>
        * <span data-ttu-id="6ff65-191">**Корневая папка**</span><span class="sxs-lookup"><span data-stu-id="6ff65-191">**Home**</span></span>
          * <span data-ttu-id="6ff65-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6ff65-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="6ff65-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6ff65-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="6ff65-194">*\_Виевстарт. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6ff65-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="6ff65-195">**Контроллеры**</span><span class="sxs-lookup"><span data-stu-id="6ff65-195">**Controllers**</span></span>

<span data-ttu-id="6ff65-196">При установке для атрибута `asp-area` значения Blogs перед маршрутами связанных контроллеров и представлений для этого тега привязки добавляется каталог *Areas/Blogs*.</span><span class="sxs-lookup"><span data-stu-id="6ff65-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="6ff65-197">Исправления для ссылки на представление *AboutBlog*:</span><span class="sxs-lookup"><span data-stu-id="6ff65-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="6ff65-198">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="6ff65-199">Для поддержки областей в приложении MVC в шаблон маршрута необходимо включить ссылку на область, если она существует.</span><span class="sxs-lookup"><span data-stu-id="6ff65-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="6ff65-200">Этот шаблон представлен вторым параметром `routes.MapRoute` вызова метода в *Startup.Configключать*:</span><span class="sxs-lookup"><span data-stu-id="6ff65-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="6ff65-201">asp-protocol</span><span class="sxs-lookup"><span data-stu-id="6ff65-201">asp-protocol</span></span>

<span data-ttu-id="6ff65-202">Атрибут [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) предназначен для указания протокола (например, `https`) в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="6ff65-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="6ff65-203">Пример:</span><span class="sxs-lookup"><span data-stu-id="6ff65-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="6ff65-204">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="6ff65-205">Имя узла в примере — localhost.</span><span class="sxs-lookup"><span data-stu-id="6ff65-205">The host name in the example is localhost.</span></span> <span data-ttu-id="6ff65-206">При формировании URL-адреса вспомогательная функция тега привязки использует общедоступный домен веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="6ff65-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="6ff65-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="6ff65-207">asp-host</span></span>

<span data-ttu-id="6ff65-208">Атрибут [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) предназначен для определения имени узла в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="6ff65-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="6ff65-209">Пример:</span><span class="sxs-lookup"><span data-stu-id="6ff65-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="6ff65-210">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="6ff65-211">asp-page</span><span class="sxs-lookup"><span data-stu-id="6ff65-211">asp-page</span></span>

<span data-ttu-id="6ff65-212">Атрибут [ASP-Page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) используется со Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="6ff65-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="6ff65-213">Используйте его для определения значения атрибута `href` тега привязки для определенной страницы.</span><span class="sxs-lookup"><span data-stu-id="6ff65-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="6ff65-214">Чтобы создать URL-адрес, перед именем страницы следует ввести символ косой черты (/).</span><span class="sxs-lookup"><span data-stu-id="6ff65-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="6ff65-215">Следующий пример указывает на страницу «участник» Razor :</span><span class="sxs-lookup"><span data-stu-id="6ff65-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="6ff65-216">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="6ff65-217">Атрибут `asp-page` является взаимоисключающим с атрибутами `asp-route`, `asp-controller` и `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="6ff65-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="6ff65-218">Тем не менее `asp-page` можно использовать с `asp-route-{value}` для управления маршрутизацией, как показано в следующем элементе:</span><span class="sxs-lookup"><span data-stu-id="6ff65-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="6ff65-219">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="6ff65-220">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="6ff65-220">asp-page-handler</span></span>

<span data-ttu-id="6ff65-221">Атрибут [ASP-Page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) используется со Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="6ff65-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="6ff65-222">Он предназначен для связывания с обработчиками определенных страниц.</span><span class="sxs-lookup"><span data-stu-id="6ff65-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="6ff65-223">Рассмотрим следующий обработчик страниц:</span><span class="sxs-lookup"><span data-stu-id="6ff65-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="6ff65-224">Связанный элемент модели страницы ссылается на обработчик страниц `OnGetProfile`.</span><span class="sxs-lookup"><span data-stu-id="6ff65-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="6ff65-225">Обратите внимание, что префикс `On<Verb>` имени метода обработчика страниц опущен в значении атрибута `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="6ff65-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="6ff65-226">Когда метод является асинхронным, суффикс `Async` также опускается.</span><span class="sxs-lookup"><span data-stu-id="6ff65-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="6ff65-227">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="6ff65-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="6ff65-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6ff65-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
