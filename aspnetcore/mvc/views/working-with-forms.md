---
title: Вспомогательные функции тегов в формах в ASP.NET Core
author: rick-anderson
description: Сведения о встроенных вспомогательных функциях тегов, используемых в формах.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/working-with-forms
ms.openlocfilehash: 51e5f2f74493e7f4c18273c8589ed0424a1f2cac
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585985"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="2aff3-103">Вспомогательные функции тегов в формах в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2aff3-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="2aff3-104">Авторы: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT), [Н. Тейлор Маллен (N. Taylor Mullen)](https://github.com/NTaylorMullen), [Дейв Пакетт (Dave Paquette)](https://twitter.com/Dave_Paquette) и [Джерри Пелсер (Jerrie Pelser)](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="2aff3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="2aff3-105">В этом документе приводятся сведения о работе с формами и элементами HTML, часто используемыми в формах.</span><span class="sxs-lookup"><span data-stu-id="2aff3-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="2aff3-106">Элемент HTML [форма](https://www.w3.org/TR/html401/interact/forms.html) предоставляет основной механизм, используемый веб-приложениями для отправки данных на сервер.</span><span class="sxs-lookup"><span data-stu-id="2aff3-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="2aff3-107">В большей части этого документа описываются [вспомогательные функции тегов](tag-helpers/intro.md) и их применение для создания надежных форм HTML.</span><span class="sxs-lookup"><span data-stu-id="2aff3-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="2aff3-108">Перед прочтением этого документа рекомендуется изучить статью [Общие сведения о вспомогательных функциях тегов](tag-helpers/intro.md).</span><span class="sxs-lookup"><span data-stu-id="2aff3-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="2aff3-109">Во многих случаях вспомогательные методы HTML располагают альтернативными вариантами для определенной вспомогательной функции тега, но следует отметить, что вспомогательные функции тегов не заменяют вспомогательные методы HTML и для каждого вспомогательного метода HTML не существует конкретной вспомогательной функции тега.</span><span class="sxs-lookup"><span data-stu-id="2aff3-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="2aff3-110">Если есть альтернатива вспомогательному методу HTML, она будет указана.</span><span class="sxs-lookup"><span data-stu-id="2aff3-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="2aff3-111">Вспомогательная функция тега формы</span><span class="sxs-lookup"><span data-stu-id="2aff3-111">The Form Tag Helper</span></span>

<span data-ttu-id="2aff3-112">Вспомогательная функция тега [формы](https://www.w3.org/TR/html401/interact/forms.html) :</span><span class="sxs-lookup"><span data-stu-id="2aff3-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="2aff3-113">Создает [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` значение атрибута HTML для действия контроллера MVC или именованного маршрута.</span><span class="sxs-lookup"><span data-stu-id="2aff3-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="2aff3-114">Создает скрытый [токен проверки запроса](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) для предотвращения подделки межсайтовых запросов (при использовании с атрибутом `[ValidateAntiForgeryToken]` в методе действия HTTP Post).</span><span class="sxs-lookup"><span data-stu-id="2aff3-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="2aff3-115">Предоставляет атрибут `asp-route-<Parameter Name>`, где `<Parameter Name>` добавляется в значения маршрута.</span><span class="sxs-lookup"><span data-stu-id="2aff3-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="2aff3-116">Параметры `routeValues` для `Html.BeginForm` и `Html.BeginRouteForm` предоставляют аналогичные функциональные возможности.</span><span class="sxs-lookup"><span data-stu-id="2aff3-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="2aff3-117">Располагает альтернативой вспомогательному методу HTML — `Html.BeginForm` и `Html.BeginRouteForm`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="2aff3-118">Образец.</span><span class="sxs-lookup"><span data-stu-id="2aff3-118">Sample:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="2aff3-119">Приведенная выше вспомогательная функция тега формы создает следующий код HTML:</span><span class="sxs-lookup"><span data-stu-id="2aff3-119">The Form Tag Helper above generates the following HTML:</span></span>

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="2aff3-120">Среда выполнения MVC генерирует значение атрибута `action` на основе атрибутов вспомогательной функции тега формы `asp-controller` и `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="2aff3-121">Вспомогательная функция тега формы также создает скрытый [токен проверки запроса](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) для предотвращения подделки межсайтовых запросов (при использовании с атрибутом `[ValidateAntiForgeryToken]` в методе действия HTTP Post).</span><span class="sxs-lookup"><span data-stu-id="2aff3-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="2aff3-122">Защита чистой формы HTML от подделки межсайтовых запросов является трудной задачей, поэтому для ее решения используется вспомогательная функция тега формы.</span><span class="sxs-lookup"><span data-stu-id="2aff3-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="2aff3-123">Использование именованного маршрута</span><span class="sxs-lookup"><span data-stu-id="2aff3-123">Using a named route</span></span>

<span data-ttu-id="2aff3-124">Атрибут `asp-route` вспомогательной функции тега также может создавать разметку для атрибута HTML `action`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="2aff3-125">Приложение с [маршрутом](../../fundamentals/routing.md) с именем `register` использует следующую разметку для страницы регистрации:</span><span class="sxs-lookup"><span data-stu-id="2aff3-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="2aff3-126">Многие представления в папке *Views/Account* (сформированные при создании веб-приложения с *учетными записями отдельных пользователей*) содержат атрибут [asp-route-returnurl](xref:mvc/views/working-with-forms):</span><span class="sxs-lookup"><span data-stu-id="2aff3-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="2aff3-127">При использовании встроенных шаблонов `returnUrl` заполняется автоматически только в случае, если вы пытаетесь получить доступ к авторизованному ресурсу, но не прошли проверку подлинности или авторизацию.</span><span class="sxs-lookup"><span data-stu-id="2aff3-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="2aff3-128">При попытке несанкционированного доступа ПО безопасности промежуточного слоя перенаправит вас на страницу входа с заданным `returnUrl`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="2aff3-129">Вспомогательная функция тега действий формы</span><span class="sxs-lookup"><span data-stu-id="2aff3-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="2aff3-130">Вспомогательная функция тега действий формы создает атрибут `formaction` в созданном теге `<button ...>` или `<input type="image" ...>`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="2aff3-131">Атрибут `formaction` определяет, куда форма отправляет свои данные.</span><span class="sxs-lookup"><span data-stu-id="2aff3-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="2aff3-132">Он привязывается к [\<input>](https://www.w3.org/wiki/HTML/Elements/input) элементам типа `image` и [\<button>](https://www.w3.org/wiki/HTML/Elements/button) элементов.</span><span class="sxs-lookup"><span data-stu-id="2aff3-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="2aff3-133">Вспомогательная функция тега действия "форма" позволяет использовать несколько атрибутов [анчортагхелпер](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` для управления тем, какая `formaction` ссылка создается для соответствующего элемента.</span><span class="sxs-lookup"><span data-stu-id="2aff3-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="2aff3-134">Ниже перечислены поддерживаемые атрибуты [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) для управления значением `formaction`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="2aff3-135">attribute</span><span class="sxs-lookup"><span data-stu-id="2aff3-135">Attribute</span></span>|<span data-ttu-id="2aff3-136">Описание</span><span class="sxs-lookup"><span data-stu-id="2aff3-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="2aff3-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="2aff3-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="2aff3-138">Имя контроллера.</span><span class="sxs-lookup"><span data-stu-id="2aff3-138">The name of the controller.</span></span>|
|[<span data-ttu-id="2aff3-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="2aff3-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="2aff3-140">Имя метода действия.</span><span class="sxs-lookup"><span data-stu-id="2aff3-140">The name of the action method.</span></span>|
|[<span data-ttu-id="2aff3-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="2aff3-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="2aff3-142">Имя области.</span><span class="sxs-lookup"><span data-stu-id="2aff3-142">The name of the area.</span></span>|
|[<span data-ttu-id="2aff3-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="2aff3-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="2aff3-144">Имя Razor страницы.</span><span class="sxs-lookup"><span data-stu-id="2aff3-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="2aff3-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="2aff3-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="2aff3-146">Имя Razor обработчика страницы.</span><span class="sxs-lookup"><span data-stu-id="2aff3-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="2aff3-147">asp-route</span><span class="sxs-lookup"><span data-stu-id="2aff3-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="2aff3-148">Имя маршрута.</span><span class="sxs-lookup"><span data-stu-id="2aff3-148">The name of the route.</span></span>|
|[<span data-ttu-id="2aff3-149">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="2aff3-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="2aff3-150">Одно значение URL-адреса маршрута.</span><span class="sxs-lookup"><span data-stu-id="2aff3-150">A single URL route value.</span></span> <span data-ttu-id="2aff3-151">Например, `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="2aff3-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="2aff3-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="2aff3-153">Все значения маршрута.</span><span class="sxs-lookup"><span data-stu-id="2aff3-153">All route values.</span></span>|
|[<span data-ttu-id="2aff3-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="2aff3-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="2aff3-155">Фрагмент URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="2aff3-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="2aff3-156">Отправка формы в пример контроллера</span><span class="sxs-lookup"><span data-stu-id="2aff3-156">Submit to controller example</span></span>

<span data-ttu-id="2aff3-157">Следующая разметка отправляет форму в действие `Index`, выполняемое `HomeController`, если выбран ввод или кнопка.</span><span class="sxs-lookup"><span data-stu-id="2aff3-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="2aff3-158">Предыдущая разметка создает следующий код HTML.</span><span class="sxs-lookup"><span data-stu-id="2aff3-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="2aff3-159">Отправка формы в пример страницы</span><span class="sxs-lookup"><span data-stu-id="2aff3-159">Submit to page example</span></span>

<span data-ttu-id="2aff3-160">Следующая разметка отправляет форму на `About` Razor страницу:</span><span class="sxs-lookup"><span data-stu-id="2aff3-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="2aff3-161">Предыдущая разметка создает следующий код HTML.</span><span class="sxs-lookup"><span data-stu-id="2aff3-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="2aff3-162">Отправка формы в пример маршрута</span><span class="sxs-lookup"><span data-stu-id="2aff3-162">Submit to route example</span></span>

<span data-ttu-id="2aff3-163">Рассмотрим конечную точку `/Home/Test`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="2aff3-164">Следующая разметка отправляет форму в конечную точку `/Home/Test`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="2aff3-165">Предыдущая разметка создает следующий код HTML.</span><span class="sxs-lookup"><span data-stu-id="2aff3-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="2aff3-166">Вспомогательная функция тега входных данных</span><span class="sxs-lookup"><span data-stu-id="2aff3-166">The Input Tag Helper</span></span>

<span data-ttu-id="2aff3-167">Вспомогательная функция тега input привязывает [\<input>](https://www.w3.org/wiki/HTML/Elements/input) элемент HTML к выражению модели в представлении Razor.</span><span class="sxs-lookup"><span data-stu-id="2aff3-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="2aff3-168">Синтаксис:</span><span class="sxs-lookup"><span data-stu-id="2aff3-168">Syntax:</span></span>

```cshtml
<input asp-for="<Expression Name>">
```

<span data-ttu-id="2aff3-169">Вспомогательная функция тега входных данных:</span><span class="sxs-lookup"><span data-stu-id="2aff3-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="2aff3-170">Создает атрибуты HTML `id` и `name` для имени выражения, указанного в атрибуте `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="2aff3-171">`asp-for="Property1.Property2"` равно `m => m.Property1.Property2`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="2aff3-172">Имя выражения совпадает со значением атрибута `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="2aff3-173">Дополнительные сведения см. в разделе [Имена выражений](#expression-names) .</span><span class="sxs-lookup"><span data-stu-id="2aff3-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="2aff3-174">Задает значение атрибута HTML `type` на основе атрибутов типа модели и [заметок к данным](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter), примененным к свойству модели.</span><span class="sxs-lookup"><span data-stu-id="2aff3-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="2aff3-175">Значение атрибута HTML `type` не перезаписывается, если оно указано.</span><span class="sxs-lookup"><span data-stu-id="2aff3-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="2aff3-176">Создает атрибуты проверки [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) из атрибутов [заметок к данным](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter), примененным к свойствам модели.</span><span class="sxs-lookup"><span data-stu-id="2aff3-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="2aff3-177">Располагает перекрытием вспомогательного метода HTML с `Html.TextBoxFor` и `Html.EditorFor`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="2aff3-178">Дополнительные сведения см. в разделе **Альтернативы вспомогательного метода HTML вспомогательной функции тега входных данных**.</span><span class="sxs-lookup"><span data-stu-id="2aff3-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="2aff3-179">Обеспечивает строгую типизацию.</span><span class="sxs-lookup"><span data-stu-id="2aff3-179">Provides strong typing.</span></span> <span data-ttu-id="2aff3-180">Если после изменения имени свойства не выполнить обновление вспомогательной функции тега, возникнет ошибка следующего вида:</span><span class="sxs-lookup"><span data-stu-id="2aff3-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

<span data-ttu-id="2aff3-181">Вспомогательная функция тега `Input` задает атрибут HTML `type` на основе типа .NET.</span><span class="sxs-lookup"><span data-stu-id="2aff3-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="2aff3-182">В следующей таблице перечислены некоторые распространенные типы .NET и созданный тип HTML (указаны не все типы .NET).</span><span class="sxs-lookup"><span data-stu-id="2aff3-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="2aff3-183">Тип .NET</span><span class="sxs-lookup"><span data-stu-id="2aff3-183">.NET type</span></span>|<span data-ttu-id="2aff3-184">Тип входных данных</span><span class="sxs-lookup"><span data-stu-id="2aff3-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="2aff3-185">Bool</span><span class="sxs-lookup"><span data-stu-id="2aff3-185">Bool</span></span>|<span data-ttu-id="2aff3-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="2aff3-186">type="checkbox"</span></span>|
|<span data-ttu-id="2aff3-187">Строка</span><span class="sxs-lookup"><span data-stu-id="2aff3-187">String</span></span>|<span data-ttu-id="2aff3-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="2aff3-188">type="text"</span></span>|
|<span data-ttu-id="2aff3-189">Дата и время</span><span class="sxs-lookup"><span data-stu-id="2aff3-189">DateTime</span></span>|<span data-ttu-id="2aff3-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="2aff3-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="2aff3-191">Byte</span><span class="sxs-lookup"><span data-stu-id="2aff3-191">Byte</span></span>|<span data-ttu-id="2aff3-192">type="number"</span><span class="sxs-lookup"><span data-stu-id="2aff3-192">type="number"</span></span>|
|<span data-ttu-id="2aff3-193">Int</span><span class="sxs-lookup"><span data-stu-id="2aff3-193">Int</span></span>|<span data-ttu-id="2aff3-194">type="number"</span><span class="sxs-lookup"><span data-stu-id="2aff3-194">type="number"</span></span>|
|<span data-ttu-id="2aff3-195">Single, Double</span><span class="sxs-lookup"><span data-stu-id="2aff3-195">Single, Double</span></span>|<span data-ttu-id="2aff3-196">type="number"</span><span class="sxs-lookup"><span data-stu-id="2aff3-196">type="number"</span></span>|

<span data-ttu-id="2aff3-197">В следующей таблице приведены некоторые наиболее распространенные атрибуты [заметок к данным](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter), которые вспомогательная функция тега входных данных будет сопоставлять с определенными типами входных данных (указаны не все атрибуты проверки):</span><span class="sxs-lookup"><span data-stu-id="2aff3-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="2aff3-198">attribute</span><span class="sxs-lookup"><span data-stu-id="2aff3-198">Attribute</span></span>|<span data-ttu-id="2aff3-199">Тип входных данных</span><span class="sxs-lookup"><span data-stu-id="2aff3-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="2aff3-200">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="2aff3-200">[EmailAddress]</span></span>|<span data-ttu-id="2aff3-201">type="email"</span><span class="sxs-lookup"><span data-stu-id="2aff3-201">type="email"</span></span>|
|<span data-ttu-id="2aff3-202">[Url]</span><span class="sxs-lookup"><span data-stu-id="2aff3-202">[Url]</span></span>|<span data-ttu-id="2aff3-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="2aff3-203">type="url"</span></span>|
|<span data-ttu-id="2aff3-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="2aff3-204">[HiddenInput]</span></span>|<span data-ttu-id="2aff3-205">type="hidden"</span><span class="sxs-lookup"><span data-stu-id="2aff3-205">type="hidden"</span></span>|
|<span data-ttu-id="2aff3-206">[Phone]</span><span class="sxs-lookup"><span data-stu-id="2aff3-206">[Phone]</span></span>|<span data-ttu-id="2aff3-207">type="tel"</span><span class="sxs-lookup"><span data-stu-id="2aff3-207">type="tel"</span></span>|
|<span data-ttu-id="2aff3-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="2aff3-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="2aff3-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="2aff3-209">type="password"</span></span>|
|<span data-ttu-id="2aff3-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="2aff3-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="2aff3-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="2aff3-211">type="date"</span></span>|
|<span data-ttu-id="2aff3-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="2aff3-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="2aff3-213">type="time"</span><span class="sxs-lookup"><span data-stu-id="2aff3-213">type="time"</span></span>|

<span data-ttu-id="2aff3-214">Образец.</span><span class="sxs-lookup"><span data-stu-id="2aff3-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="2aff3-215">Приведенный выше код создает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="2aff3-215">The code above generates the following HTML:</span></span>

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="2aff3-216">Заметки данных применяются к свойствам `Email` и `Password`, создающим метаданные для модели.</span><span class="sxs-lookup"><span data-stu-id="2aff3-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="2aff3-217">Вспомогательная функция тега входных данных использует метаданные модели и создает атрибуты [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (см. статью [Проверка модели](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="2aff3-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="2aff3-218">Эти атрибуты описывают проверяющие элементы управления, присоединяемые к полям входных данных.</span><span class="sxs-lookup"><span data-stu-id="2aff3-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="2aff3-219">Это обеспечивает ненавязчивую проверку HTML5 и [jQuery](https://jquery.com/).</span><span class="sxs-lookup"><span data-stu-id="2aff3-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="2aff3-220">Ненавязчивые атрибуты имеют формат `data-val-rule="Error Message"` , где Rule — это имя правила проверки (например,, и `data-val-required` `data-val-email` `data-val-maxlength` т. д.). Если в атрибуте указано сообщение об ошибке, оно отображается как значение для `data-val-rule` атрибута.</span><span class="sxs-lookup"><span data-stu-id="2aff3-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="2aff3-221">Также существуют атрибуты формы `data-val-ruleName-argumentName="argumentValue"`, которые содержат дополнительные сведения о правиле, например `data-val-maxlength-max="1024"`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="2aff3-222">Альтернативы вспомогательного метода HTML вспомогательной функции тега входных данных</span><span class="sxs-lookup"><span data-stu-id="2aff3-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="2aff3-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` и `Html.EditorFor` имеют функции, перекрывающиеся со вспомогательной функцией тега входных данных.</span><span class="sxs-lookup"><span data-stu-id="2aff3-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="2aff3-224">Вспомогательная функция тега входных данных будет автоматически задавать атрибут `type`, а `Html.TextBox` и `Html.TextBoxFor` — нет.</span><span class="sxs-lookup"><span data-stu-id="2aff3-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="2aff3-225">`Html.Editor` и `Html.EditorFor` обрабатывают коллекции, сложные объекты и шаблоны, а вспомогательная функция тега входных данных не делает этого.</span><span class="sxs-lookup"><span data-stu-id="2aff3-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="2aff3-226">Вспомогательная функция тега входных данных, `Html.EditorFor` и `Html.TextBoxFor` являются строго типизированными (они используют лямбда-выражения); а `Html.TextBox` и `Html.Editor` не являются таковыми (они используют имена выражений).</span><span class="sxs-lookup"><span data-stu-id="2aff3-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="2aff3-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="2aff3-227">HtmlAttributes</span></span>

<span data-ttu-id="2aff3-228">При выполнении шаблонов по умолчанию `@Html.Editor()` и `@Html.EditorFor()` используют специальную запись `ViewDataDictionary` с именем `htmlAttributes`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="2aff3-229">Это поведение дополняется параметрами `additionalViewData`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="2aff3-230">Ключ "htmlAttributes" не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="2aff3-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="2aff3-231">Ключ "htmlAttributes" обрабатывается так же, как `htmlAttributes` объект, передаваемый во вспомогательные функции входных данных, такие как `@Html.TextBox()`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="2aff3-232">Имена выражений</span><span class="sxs-lookup"><span data-stu-id="2aff3-232">Expression names</span></span>

<span data-ttu-id="2aff3-233">Значением атрибута `asp-for` является `ModelExpression` и правая часть лямбда-выражения.</span><span class="sxs-lookup"><span data-stu-id="2aff3-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="2aff3-234">Таким образом, `asp-for="Property1"` становится `m => m.Property1` в созданном коде, поэтому нет необходимости добавлять префикс `Model`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="2aff3-235">Чтобы начать встроенное выражение и переместить его перед `m.`, используется символ \@.</span><span class="sxs-lookup"><span data-stu-id="2aff3-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

<span data-ttu-id="2aff3-236">Выводится следующий результат:</span><span class="sxs-lookup"><span data-stu-id="2aff3-236">Generates the following:</span></span>

```html
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="2aff3-237">При использовании свойств коллекции `asp-for="CollectionProperty[23].Member"` генерирует то же самое имя, что и `asp-for="CollectionProperty[i].Member"`, если `i` имеет значение `23`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="2aff3-238">Когда MVC ASP.NET Core рассчитывает значение `ModelExpression`, он оценивает несколько источников, включая `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="2aff3-239">Вы можете использовать `<input type="text" asp-for="@Name">`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="2aff3-240">Рассчитанный атрибут `value` является первым значением, отличным от NULL, из:</span><span class="sxs-lookup"><span data-stu-id="2aff3-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="2aff3-241">записи `ModelState` с ключом "Name";</span><span class="sxs-lookup"><span data-stu-id="2aff3-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="2aff3-242">результата выражения `Model.Name`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="2aff3-243">Навигация по дочерним свойствам</span><span class="sxs-lookup"><span data-stu-id="2aff3-243">Navigating child properties</span></span>

<span data-ttu-id="2aff3-244">Для перехода к дочерним свойствам можно также использовать путь к свойству модели представления.</span><span class="sxs-lookup"><span data-stu-id="2aff3-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="2aff3-245">Рассмотрим более сложный класс модели, который содержит дочернее свойство `Address`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="2aff3-246">В представлении выполняется привязка к `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="2aff3-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="2aff3-247">Следующий HTML создан для `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="2aff3-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="2aff3-248">Имена выражений и коллекций</span><span class="sxs-lookup"><span data-stu-id="2aff3-248">Expression names and Collections</span></span>

<span data-ttu-id="2aff3-249">Пример модели, содержащей массив `Colors`:</span><span class="sxs-lookup"><span data-stu-id="2aff3-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="2aff3-250">Метод действия:</span><span class="sxs-lookup"><span data-stu-id="2aff3-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

<span data-ttu-id="2aff3-251">Ниже Razor показано, как получить доступ к определенному `Color` элементу.</span><span class="sxs-lookup"><span data-stu-id="2aff3-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="2aff3-252">Шаблон *Views/Shared/EditorTemplates/String.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2aff3-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="2aff3-253">Пример с использованием `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="2aff3-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="2aff3-254">Ниже Razor показано, как выполнить итерацию по коллекции.</span><span class="sxs-lookup"><span data-stu-id="2aff3-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="2aff3-255">Шаблон *Views/Shared/EditorTemplates/ToDoItem.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2aff3-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="2aff3-256">По возможности следует использовать `foreach`, когда значение будет применяться в эквивалентном контексте `asp-for` или `Html.DisplayFor`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="2aff3-257">Обычно лучше использовать `for`, чем `foreach` (если сценарий позволяет), так как ему не нужно выделять перечислитель. Тем не менее оценка индексатора в выражении LINQ может быть недешевой, поэтому ее нужно минимизировать.</span><span class="sxs-lookup"><span data-stu-id="2aff3-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="2aff3-258">В приведенном выше комментированном коде показано, как заменить лямбда-выражение оператором `@`, чтобы получить доступ к каждому `ToDoItem` в списке.</span><span class="sxs-lookup"><span data-stu-id="2aff3-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="2aff3-259">Вспомогательная функция тега Textarea</span><span class="sxs-lookup"><span data-stu-id="2aff3-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="2aff3-260">Вспомогательная функция тега `Textarea Tag Helper`аналогична вспомогательной функции тега входных данных.</span><span class="sxs-lookup"><span data-stu-id="2aff3-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="2aff3-261">Создает `id` атрибуты и `name` и атрибуты проверки данных из модели для [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) элемента.</span><span class="sxs-lookup"><span data-stu-id="2aff3-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="2aff3-262">Обеспечивает строгую типизацию.</span><span class="sxs-lookup"><span data-stu-id="2aff3-262">Provides strong typing.</span></span>

* <span data-ttu-id="2aff3-263">Располагает альтернативой вспомогательному методу HTML — `Html.TextAreaFor`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="2aff3-264">Образец.</span><span class="sxs-lookup"><span data-stu-id="2aff3-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="2aff3-265">Создается следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="2aff3-265">The following HTML is generated:</span></span>

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="2aff3-266">Вспомогательная функция тега метки</span><span class="sxs-lookup"><span data-stu-id="2aff3-266">The Label Tag Helper</span></span>

* <span data-ttu-id="2aff3-267">Создает заголовок и атрибут метки `for` [\<label>](https://www.w3.org/wiki/HTML/Elements/label) элемента для имени выражения.</span><span class="sxs-lookup"><span data-stu-id="2aff3-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="2aff3-268">Располагает альтернативой вспомогательному методу HTML — `Html.LabelFor`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="2aff3-269">`Label Tag Helper` предоставляет следующие преимущества по сравнению с чистым элементом метки:</span><span class="sxs-lookup"><span data-stu-id="2aff3-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="2aff3-270">Вы автоматически получаете значение описательной метки из `Display` атрибута.</span><span class="sxs-lookup"><span data-stu-id="2aff3-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="2aff3-271">Предполагаемое отображаемое имя может изменяться с течением времени, а сочетание атрибута `Display` и вспомогательной функции тега метки будет применять атрибут `Display` везде, где он используется.</span><span class="sxs-lookup"><span data-stu-id="2aff3-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="2aff3-272">Меньше разметки в исходном коде.</span><span class="sxs-lookup"><span data-stu-id="2aff3-272">Less markup in source code</span></span>

* <span data-ttu-id="2aff3-273">Строгая типизация со свойством модели.</span><span class="sxs-lookup"><span data-stu-id="2aff3-273">Strong typing with the model property.</span></span>

<span data-ttu-id="2aff3-274">Образец.</span><span class="sxs-lookup"><span data-stu-id="2aff3-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="2aff3-275">Для элемента `<label>` создан следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="2aff3-275">The following HTML is generated for the `<label>` element:</span></span>

```html
<label for="Email">Email Address</label>
```

<span data-ttu-id="2aff3-276">Вспомогательная функция тега метки сгенерировала для атрибута `for` значение "Email", представляющее собой идентификатор, связанный с элементом `<input>`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="2aff3-277">Вспомогательные функции тегов создают согласованные элементы `id` и `for`, чтобы обеспечить их правильное связывание.</span><span class="sxs-lookup"><span data-stu-id="2aff3-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="2aff3-278">Заголовок в этом примере взят из атрибута `Display`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="2aff3-279">Если модель не содержит атрибут `Display`, заголовком будет имя свойства выражения.</span><span class="sxs-lookup"><span data-stu-id="2aff3-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="2aff3-280">Вспомогательные функции тегов проверки</span><span class="sxs-lookup"><span data-stu-id="2aff3-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="2aff3-281">Существует две вспомогательные функции тегов проверки.</span><span class="sxs-lookup"><span data-stu-id="2aff3-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="2aff3-282">`Validation Message Tag Helper` отображает сообщение проверки для одного свойства в модели, `Validation Summary Tag Helper` отображает сводку ошибок проверки.</span><span class="sxs-lookup"><span data-stu-id="2aff3-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="2aff3-283">`Input Tag Helper` добавляет клиентские атрибуты проверки HTML5 в элементы входных данных на основе атрибутов заметок к данным в классах модели.</span><span class="sxs-lookup"><span data-stu-id="2aff3-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="2aff3-284">Проверка также выполняется на сервере.</span><span class="sxs-lookup"><span data-stu-id="2aff3-284">Validation is also performed on the server.</span></span> <span data-ttu-id="2aff3-285">Вспомогательная функция тега проверки отображает эти сообщения об ошибках при возникновении ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="2aff3-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="2aff3-286">Вспомогательная функция тега сообщения о проверке</span><span class="sxs-lookup"><span data-stu-id="2aff3-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="2aff3-287">Добавляет атрибут [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` к элементу [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) , который прикрепляет сообщения об ошибках проверки в поле ввода указанного свойства модели.  </span><span class="sxs-lookup"><span data-stu-id="2aff3-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="2aff3-288">При возникновении ошибки проверки на стороне клиента [jQuery](https://jquery.com/) отображает сообщение об ошибке в элементе `<span>`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="2aff3-289">Проверка также выполняется на сервере.</span><span class="sxs-lookup"><span data-stu-id="2aff3-289">Validation also takes place on the server.</span></span> <span data-ttu-id="2aff3-290">На клиентах может быть отключена поддержка JavaScript, поэтому некоторые проверки выполняются только на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="2aff3-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="2aff3-291">Располагает альтернативой вспомогательному методу HTML — `Html.ValidationMessageFor`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="2aff3-292">`Validation Message Tag Helper` используется с атрибутом `asp-validation-for` в элементе HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span).</span><span class="sxs-lookup"><span data-stu-id="2aff3-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```cshtml
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="2aff3-293">Вспомогательная функция тега сообщения о проверке создает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="2aff3-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="2aff3-294">Как правило, `Validation Message Tag Helper` используется после вспомогательной функции тега `Input` для одного и того же свойства.</span><span class="sxs-lookup"><span data-stu-id="2aff3-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="2aff3-295">В этом случае сообщения об ошибках проверки отображаются рядом с входными данными, вызвавшими ошибку.</span><span class="sxs-lookup"><span data-stu-id="2aff3-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="2aff3-296">Для проверки на стороне клиента необходимо иметь представление с правильными ссылками на скрипты JavaScript и [jQuery](https://jquery.com/).</span><span class="sxs-lookup"><span data-stu-id="2aff3-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="2aff3-297">Дополнительные сведения см. в статье о [проверке модели](../models/validation.md).</span><span class="sxs-lookup"><span data-stu-id="2aff3-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="2aff3-298">При возникновении ошибки проверки на стороне сервера (например, если выполняется пользовательская проверка на стороне сервера или проверка на стороне клиент отключена) MVC размещает сообщение об ошибке в тексте элемента `<span>`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="2aff3-299">Вспомогательная функция тега сводки по проверке</span><span class="sxs-lookup"><span data-stu-id="2aff3-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="2aff3-300">Работает с элементами `<div>`, имеющими атрибут `asp-validation-summary`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="2aff3-301">Располагает альтернативой вспомогательному методу HTML — `@Html.ValidationSummary`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="2aff3-302">`Validation Summary Tag Helper` используется для отображения сводки по сообщениям проверки.</span><span class="sxs-lookup"><span data-stu-id="2aff3-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="2aff3-303">Значением атрибута `asp-validation-summary` может быть любое из следующих:</span><span class="sxs-lookup"><span data-stu-id="2aff3-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="2aff3-304">asp-validation-summary</span><span class="sxs-lookup"><span data-stu-id="2aff3-304">asp-validation-summary</span></span>|<span data-ttu-id="2aff3-305">Отображаемые сообщения о проверке</span><span class="sxs-lookup"><span data-stu-id="2aff3-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="2aff3-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="2aff3-306">ValidationSummary.All</span></span>|<span data-ttu-id="2aff3-307">Свойство и уровень модели</span><span class="sxs-lookup"><span data-stu-id="2aff3-307">Property and model level</span></span>|
|<span data-ttu-id="2aff3-308">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="2aff3-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="2aff3-309">Моделирование</span><span class="sxs-lookup"><span data-stu-id="2aff3-309">Model</span></span>|
|<span data-ttu-id="2aff3-310">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="2aff3-310">ValidationSummary.None</span></span>|<span data-ttu-id="2aff3-311">Нет</span><span class="sxs-lookup"><span data-stu-id="2aff3-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="2aff3-312">Образец</span><span class="sxs-lookup"><span data-stu-id="2aff3-312">Sample</span></span>

<span data-ttu-id="2aff3-313">В следующем примере модель данных имеет атрибуты `DataAnnotation`, в результате чего создаются сообщения об ошибках проверки для элемента `<input>`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-313">In the following example, the data model has `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="2aff3-314">При возникновении ошибки проверки вспомогательная функция тега проверки отображает следующее сообщение об ошибке:</span><span class="sxs-lookup"><span data-stu-id="2aff3-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="2aff3-315">Созданный HTML (если модель является допустимой):</span><span class="sxs-lookup"><span data-stu-id="2aff3-315">The generated HTML (when the model is valid):</span></span>

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="2aff3-316">Вспомогательная функция тега Select</span><span class="sxs-lookup"><span data-stu-id="2aff3-316">The Select Tag Helper</span></span>

* <span data-ttu-id="2aff3-317">Создает элемент [select](https://www.w3.org/wiki/HTML/Elements/select) и связанные элементы [option](https://www.w3.org/wiki/HTML/Elements/option) для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="2aff3-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="2aff3-318">Располагает альтернативой вспомогательному методу HTML — `Html.DropDownListFor` и `Html.ListBoxFor`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="2aff3-319">`Select Tag Helper` `asp-for` указывает имя свойства модели для элемента [select](https://www.w3.org/wiki/HTML/Elements/select), а `asp-items` указывает элементы [option](https://www.w3.org/wiki/HTML/Elements/option).</span><span class="sxs-lookup"><span data-stu-id="2aff3-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="2aff3-320">Пример:</span><span class="sxs-lookup"><span data-stu-id="2aff3-320">For example:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="2aff3-321">Образец.</span><span class="sxs-lookup"><span data-stu-id="2aff3-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="2aff3-322">Метод `Index` инициализирует `CountryViewModel`, задает выбранную страну и передает их в представление `Index`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="2aff3-323">Метод HTTP POST `Index` отображает выбор:</span><span class="sxs-lookup"><span data-stu-id="2aff3-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="2aff3-324">Представление `Index`:</span><span class="sxs-lookup"><span data-stu-id="2aff3-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="2aff3-325">Создается следующий HTML (с выбранным значением "CA"):</span><span class="sxs-lookup"><span data-stu-id="2aff3-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="2aff3-326">С вспомогательной функцией тега Select не рекомендуется использовать `ViewBag` или `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="2aff3-327">Модель представления более надежна в процессе предоставления метаданных MVC и, как правило, менее проблематична.</span><span class="sxs-lookup"><span data-stu-id="2aff3-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="2aff3-328">Значение атрибута `asp-for` является особым случаем и не требует префикса `Model`, тогда как он необходим другим атрибутам вспомогательной функции тега (например, `asp-items`).</span><span class="sxs-lookup"><span data-stu-id="2aff3-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="2aff3-329">Привязка перечисления</span><span class="sxs-lookup"><span data-stu-id="2aff3-329">Enum binding</span></span>

<span data-ttu-id="2aff3-330">Часто бывает удобно использовать `<select>` со свойством `enum` и создавать элементы `SelectListItem` из значений `enum`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="2aff3-331">Образец.</span><span class="sxs-lookup"><span data-stu-id="2aff3-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="2aff3-332">Метод `GetEnumSelectList` создает объект `SelectList` для перечисления.</span><span class="sxs-lookup"><span data-stu-id="2aff3-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="2aff3-333">Список перечислителя можно пометить атрибутом `Display` для формирования пользовательского интерфейса с более широкими функциональными возможностями:</span><span class="sxs-lookup"><span data-stu-id="2aff3-333">You can mark your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="2aff3-334">Создается следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="2aff3-334">The following HTML is generated:</span></span>

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="2aff3-335">Группа параметров</span><span class="sxs-lookup"><span data-stu-id="2aff3-335">Option Group</span></span>

<span data-ttu-id="2aff3-336">Элемент HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) создается, когда модель представления содержит один или несколько `SelectListGroup` объектов.</span><span class="sxs-lookup"><span data-stu-id="2aff3-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="2aff3-337">`CountryViewModelGroup` группирует элементы `SelectListItem` в группы "North America" и "Europe":</span><span class="sxs-lookup"><span data-stu-id="2aff3-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="2aff3-338">Ниже приведены две группы:</span><span class="sxs-lookup"><span data-stu-id="2aff3-338">The two groups are shown below:</span></span>

![пример группы параметров](working-with-forms/_static/grp.png)

<span data-ttu-id="2aff3-340">Созданный HTML:</span><span class="sxs-lookup"><span data-stu-id="2aff3-340">The generated HTML:</span></span>

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="2aff3-341">Множественный выбор</span><span class="sxs-lookup"><span data-stu-id="2aff3-341">Multiple select</span></span>

<span data-ttu-id="2aff3-342">Вспомогательная функция Select автоматически создаст атрибут [multiple = "multiple"](https://w3c.github.io/html-reference/select.html), если свойство, указанное в атрибуте `asp-for`, имеет значение `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="2aff3-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="2aff3-343">Допустим, имеется такая модель:</span><span class="sxs-lookup"><span data-stu-id="2aff3-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="2aff3-344">Со следующим представлением:</span><span class="sxs-lookup"><span data-stu-id="2aff3-344">With the following view:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="2aff3-345">Генерирует следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="2aff3-345">Generates the following HTML:</span></span>

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="2aff3-346">Не выбрано</span><span class="sxs-lookup"><span data-stu-id="2aff3-346">No selection</span></span>

<span data-ttu-id="2aff3-347">Если вы используете параметр "not specified" (не выбрано) на нескольких страницах, можно создать шаблон, чтобы исключить повторяющийся HTML:</span><span class="sxs-lookup"><span data-stu-id="2aff3-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="2aff3-348">Шаблон *Views/Shared/EditorTemplates/CountryViewModel.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2aff3-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="2aff3-349">Добавление HTML- [\<option>](https://www.w3.org/wiki/HTML/Elements/option) элементов не ограничено вариантом *без выбора* .</span><span class="sxs-lookup"><span data-stu-id="2aff3-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="2aff3-350">Например, следующее представление и метод действия создадут HTML, аналогичный приведенному выше коду:</span><span class="sxs-lookup"><span data-stu-id="2aff3-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="2aff3-351">В зависимости от текущего значения `Country` будет выбран соответствующий элемент `<option>` (содержащий атрибут `selected="selected"`).</span><span class="sxs-lookup"><span data-stu-id="2aff3-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="2aff3-352">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2aff3-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="2aff3-353">Элемент формы HTML</span><span class="sxs-lookup"><span data-stu-id="2aff3-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="2aff3-354">Токен проверки запроса</span><span class="sxs-lookup"><span data-stu-id="2aff3-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="2aff3-355">Интерфейс IAttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="2aff3-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="2aff3-356">Фрагменты кода для этого документа</span><span class="sxs-lookup"><span data-stu-id="2aff3-356">Code snippets for this document</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/working-with-forms/sample/final)
