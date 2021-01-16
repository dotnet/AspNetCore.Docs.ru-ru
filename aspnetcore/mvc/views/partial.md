---
title: Частичные представления в ASP.NET Core
author: ardalis
description: Узнайте, как с помощью частичных представлений разбить большие файлы разметки на части и предотвратить дублирование стандартных блоков разметки на веб-страницах приложений ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: 3a7b054adc50be57028dfa0364f80ae8733b02f7
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252712"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="ce6fc-103">Частичные представления в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce6fc-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="ce6fc-104">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Махер Джендуби](https://twitter.com/maherjend) (Maher JENDOUBI), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Скотт Саубер](https://twitter.com/scottsauber) (Scott Sauber)</span><span class="sxs-lookup"><span data-stu-id="ce6fc-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="ce6fc-105">Частичное представление — это [Razor](xref:mvc/views/razor) файл разметки (*. cshtml*) без [`@page`](xref:mvc/views/razor#page) директивы, которая отображает выходные данные HTML *в* виде выходных данных, отображаемых в другом файле разметки.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ce6fc-106">Термин *частичное представление* используется при разработке приложения MVC, где файлы разметки называются *представлениями*, или в Razor приложении страниц, где файлы разметки называются *страницами*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="ce6fc-107">В этом разделе обобщенно рассматриваются представления MVC и страницы Razor страниц в виде *файлов разметки*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="ce6fc-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ce6fc-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="ce6fc-109">Когда следует использовать частичные представления</span><span class="sxs-lookup"><span data-stu-id="ce6fc-109">When to use partial views</span></span>

<span data-ttu-id="ce6fc-110">Частичные представления позволяют эффективно выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="ce6fc-111">Разбить большие файлы разметки на мелкие компоненты.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="ce6fc-112">Если используется крупный и сложный файл разметки, состоящий из нескольких логических частей, будет удобнее работать с этими частями как с отдельными частичными представлениями.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="ce6fc-113">Код в файле разметки сократится до разумного размера и будет содержать только общую структуру страницы и ссылки на частичные представления.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="ce6fc-114">Сократить дублирование элементов разметки в разных файлах разметки.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="ce6fc-115">Если во многих файлах разметки используются одинаковые элементы, частичное представление переносит такое дублируемое содержимого в отдельный файл частичного представления.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="ce6fc-116">Так, когда частичное представление изменится, автоматически обновятся выводимые данные для всех файлов разметки, использующих это частичное представление.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="ce6fc-117">Частичные представления не следует использовать для выделения стандартных элементов макета.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="ce6fc-118">Повторяющиеся элементы макета следует определять в файле [_Layout.cshtml](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="ce6fc-119">Не используйте частичное представление там, где требуется сложная логика визуализации или выполнение кода для визуализации разметки.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="ce6fc-120">В этой ситуации лучше применить [компонент представления](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="ce6fc-121">Объявление частичных представлений</span><span class="sxs-lookup"><span data-stu-id="ce6fc-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ce6fc-122">Частичное представление — это файл разметки *. cshtml* без [`@page`](xref:mvc/views/razor#page) директивы, поддерживаемой в папке *views* (MVC) или папке *pages* ( Razor страницы).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-122">A partial view is a *.cshtml* markup file without an [`@page`](xref:mvc/views/razor#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="ce6fc-123">В модели ASP.NET Core MVC элемент контроллера <xref:Microsoft.AspNetCore.Mvc.ViewResult> может возвращать представление или частичное представление.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="ce6fc-124">На Razor страницах объект <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> может возвращать частичное представление, представленное в виде <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> объекта.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-124">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="ce6fc-125">См. дополнительные сведения о [создании ссылок на частичные представления и их отображении](#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="ce6fc-126">В отличие от представления MVC и отображения страниц, частичное представление не выполняет файл *_ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="ce6fc-127">Дополнительные сведения о файле *_ViewStart.cshtml*, см. здесь: <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-127">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="ce6fc-128">Имена файлов частичного представления обычно начинаются с символа подчеркивания (`_`).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="ce6fc-129">Это соглашение об именовании не является обязательным требованием, но помогает отличать частичные представления от обычных представлений и страниц.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ce6fc-130">Частичное представление — это файл разметки *.cshtml*, размещенный в папке *Views*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="ce6fc-131">Элемент контроллера <xref:Microsoft.AspNetCore.Mvc.ViewResult> может возвращать представление или частичное представление.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="ce6fc-132">См. дополнительные сведения о [создании ссылок на частичные представления и их отображении](#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="ce6fc-133">В отличие от представления MVC, частичное представление не выполняет файл *_ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="ce6fc-134">Дополнительные сведения о файле *_ViewStart.cshtml*, см. здесь: <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-134">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="ce6fc-135">Имена файлов частичного представления обычно начинаются с символа подчеркивания (`_`).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="ce6fc-136">Это соглашение об именовании не является обязательным требованием, но помогает отличать частичные представления от обычных представлений.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="ce6fc-137">Ссылка на частичное представление</span><span class="sxs-lookup"><span data-stu-id="ce6fc-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a><span data-ttu-id="ce6fc-138">Использование частичного представления на Razor страницах PageModel</span><span class="sxs-lookup"><span data-stu-id="ce6fc-138">Use a partial view in a Razor Pages PageModel</span></span>

<span data-ttu-id="ce6fc-139">В ASP.NET Core 2,0 или 2,1 следующий метод обработчика преобразует частичное представление *\_ аусорпартиалрп. cshtml* в ответ:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ce6fc-140">В ASP.NET Core 2.2 или более поздней версии метод обработчика может вызвать метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*>, чтобы выдать объект `PartialViewResult`:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="ce6fc-141">Использование частичного представления в файле разметки</span><span class="sxs-lookup"><span data-stu-id="ce6fc-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ce6fc-142">В файле разметки частичное представление может указываться несколькими способами.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="ce6fc-143">Мы рекомендуем использовать в приложениях один из следующих методов асинхронного отображения:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="ce6fc-144">Вспомогательная функция тега частичного представления</span><span class="sxs-lookup"><span data-stu-id="ce6fc-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="ce6fc-145">Асинхронное вспомогательное приложение HTML</span><span class="sxs-lookup"><span data-stu-id="ce6fc-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="ce6fc-146">В файле разметки частичное представление может указываться двумя способами.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="ce6fc-147">Асинхронное вспомогательное приложение HTML</span><span class="sxs-lookup"><span data-stu-id="ce6fc-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="ce6fc-148">Синхронное вспомогательное приложение HTML</span><span class="sxs-lookup"><span data-stu-id="ce6fc-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="ce6fc-149">Мы рекомендуем использовать в приложениях [асинхронное вспомогательное приложение HTML](#asynchronous-html-helper).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="ce6fc-150">Вспомогательная функция тега частичного представления</span><span class="sxs-lookup"><span data-stu-id="ce6fc-150">Partial Tag Helper</span></span>

<span data-ttu-id="ce6fc-151">Для [вспомогательной функции тега частичного представления](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) требуется ASP.NET Core 2.1 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="ce6fc-152">Вспомогательная функция тега частичного представления синхронно отображает содержимое, используя близкий к HTML синтаксис:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="ce6fc-153">Если присутствует расширение файла, вспомогательная функция тега ссылается на частичное представление, которое необходимо разместить в той же папке, что и вызывающий его файл разметки:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="ce6fc-154">В следующем примере есть ссылка на частичное представление из корня приложения.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="ce6fc-155">Пути, начинающиеся с тильды и косой черты (`~/`) или с косой черты (`/`), используют корневой каталог приложения:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="ce6fc-156">**Razor См**</span><span class="sxs-lookup"><span data-stu-id="ce6fc-156">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="ce6fc-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="ce6fc-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="ce6fc-158">В следующем примере есть ссылка на частичное представление с относительным путем.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="ce6fc-159">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="ce6fc-160">Асинхронный вспомогательный метод HTML</span><span class="sxs-lookup"><span data-stu-id="ce6fc-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="ce6fc-161">Если вы используете вспомогательное приложение HTML, мы рекомендуем использовать <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="ce6fc-162">`PartialAsync` возвращает тип <xref:Microsoft.AspNetCore.Html.IHtmlContent> в оболочке <xref:System.Threading.Tasks.Task%601>.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="ce6fc-163">Для указания метода имя ожидающего вызова дополняется префиксом `@`.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="ce6fc-164">Если присутствует расширение файла, вспомогательная функция HTML ссылается на частичное представление, которое необходимо разместить в той же папке, что и вызывающий его файл разметки:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="ce6fc-165">В следующем примере есть ссылка на частичное представление из корня приложения.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="ce6fc-166">Пути, начинающиеся с тильды и косой черты (`~/`) или с косой черты (`/`), используют корневой каталог приложения:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ce6fc-167">**Razor См**</span><span class="sxs-lookup"><span data-stu-id="ce6fc-167">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="ce6fc-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="ce6fc-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="ce6fc-169">В следующем примере есть ссылка на частичное представление с относительным путем.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="ce6fc-170">Вы также можете выполнить преобразование для просмотра частичного представления с помощью <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="ce6fc-171">Этот метод не возвращает <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="ce6fc-172">Он передает выводимые данные непосредственно в ответ в потоковом режиме.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="ce6fc-173">Поскольку метод не возвращает результат, он должен быть вызван в Razor блоке кода:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-173">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="ce6fc-174">Так как `RenderPartialAsync` выполняет потоковую передачу отображаемого содержимого, его производительность в некоторых сценариях выше.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="ce6fc-175">В системах критической важности следует замерить время отображения страницы при использовании двух подходов и использовать тот из них, который создает ответ быстрее.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="ce6fc-176">Синхронный вспомогательный метод HTML</span><span class="sxs-lookup"><span data-stu-id="ce6fc-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="ce6fc-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> и <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> — это синхронные эквиваленты `PartialAsync` и `RenderPartialAsync` соответственно.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="ce6fc-178">Мы не рекомендуем использовать синхронные эквиваленты, так как в некоторых случаях они приводят к взаимоблокировке.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="ce6fc-179">Синхронные методы будут удалены в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ce6fc-180">Если вам нужно выполнять код, используйте [компонент представления](xref:mvc/views/view-components) вместо частичного представления.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ce6fc-181">Вызов `Partial` или `RenderPartial` генерирует предупреждение анализатора Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="ce6fc-182">Например, наличие `Partial` приведет к появлению такого сообщения:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="ce6fc-183">Использование IHtmlHelper.Partial может привести к взаимоблокировкам приложения.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="ce6fc-184">Попробуйте использовать &lt;частичное&gt; вспомогательное приложение тега или IHtmlHelper.PartialAsync.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="ce6fc-185">Вместо вызовов `@Html.Partial` используйте `@await Html.PartialAsync` или [вспомогательное приложение тега частичного представления](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="ce6fc-186">Дополнительные сведения о переносе вспомогательной функции тега частичного представления см. в разделе [Перенос из вспомогательного метода HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span><span class="sxs-lookup"><span data-stu-id="ce6fc-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="ce6fc-187">Обнаружение частичного представления</span><span class="sxs-lookup"><span data-stu-id="ce6fc-187">Partial view discovery</span></span>

<span data-ttu-id="ce6fc-188">Если ссылка на частичное представление указывает только имя файла, без расширения, просматриваются следующие расположения в указанном порядке:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ce6fc-189">**Razor См**</span><span class="sxs-lookup"><span data-stu-id="ce6fc-189">**Razor Pages**</span></span>

1. <span data-ttu-id="ce6fc-190">папка текущей выполняемой страницы;</span><span class="sxs-lookup"><span data-stu-id="ce6fc-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="ce6fc-191">граф каталога на уровень выше папки страницы.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="ce6fc-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="ce6fc-192">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="ce6fc-193">К обнаружению частичного представления применяются следующие соглашения:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="ce6fc-194">допускается использовать разные частичные представления с одним именем файла, если они расположены в разных папках;</span><span class="sxs-lookup"><span data-stu-id="ce6fc-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="ce6fc-195">если ссылка на частичное представление содержит имя файла без расширения, а частичные представления с таким именем одновременно присутствуют в папке вызывающего объекта и в папке *Shared*, используется частичное представление из папки вызывающего объекта.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="ce6fc-196">Если нужное частичное представление отсутствует в папке вызывающего объекта, используется частичное представление из папки *Shared*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="ce6fc-197">Частичных представления в папке *Shared* именуются *общие частичные представления* или *частичные представления по умолчанию*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="ce6fc-198">Частичные представления могут быть объединены в *цепочку* &mdash; . частичное представление может вызывать другое частичное представление, если циклическая ссылка не сформирована вызовами.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="ce6fc-199">Относительные пути всегда указываются относительно расположения текущего файла, но не корневого или родительского каталога.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="ce6fc-200">Объект, [Razor](xref:mvc/views/razor) `section` определенный в частичном представлении, невидим для родительских файлов разметки.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-200">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="ce6fc-201">Объект `section` видим только для частичного представления, в котором он определен.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="ce6fc-202">Доступ к данным из частичных представлений</span><span class="sxs-lookup"><span data-stu-id="ce6fc-202">Access data from partial views</span></span>

<span data-ttu-id="ce6fc-203">Когда создается экземпляр частичного представления, он получает *копию* словаря `ViewData` из родительского представления.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="ce6fc-204">Изменения, вносимые в данные в частичном представлении, не сохраняются в родительском представлении.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="ce6fc-205">Изменения объекта `ViewData` в частичном представлении утрачиваются при возврате этого представления.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="ce6fc-206">В следующем примере показано, как передать экземпляр [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) в частичное представление:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="ce6fc-207">В частичное представление можно передать модель.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="ce6fc-208">Модель может являться пользовательским объектом.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-208">The model can be a custom object.</span></span> <span data-ttu-id="ce6fc-209">Модель можно передать с помощью `PartialAsync` (передает блок содержимого вызывающему объекту) или `RenderPartialAsync` (выполняет потоковую передачу содержимого в выходные данные):</span><span class="sxs-lookup"><span data-stu-id="ce6fc-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ce6fc-210">**Razor См**</span><span class="sxs-lookup"><span data-stu-id="ce6fc-210">**Razor Pages**</span></span>

<span data-ttu-id="ce6fc-211">Следующая разметка для примера приложения взята со страницы *Pages/ArticlesRP/ReadRP.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="ce6fc-212">Эта страница содержит два частичных представления.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-212">The page contains two partial views.</span></span> <span data-ttu-id="ce6fc-213">Второе частичное представление передает модель и объект `ViewData` в первое частичное представление.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="ce6fc-214">Используйте перегрузку конструктора `ViewDataDictionary`, чтобы передать новый словарь `ViewData` и при этом сохранить существующий словарь `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="ce6fc-215">*Pages/Shared/_AuthorPartialRP.cshtml* — это первое частичное представление, указанное в файле разметки *ReadRP.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="ce6fc-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* — это второе частичное представление, указанное в файле разметки *ReadRP.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="ce6fc-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="ce6fc-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="ce6fc-218">В приведенной ниже разметке для примера приложения показано представление *Views/Articles/Read.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="ce6fc-219">Это представление содержит два частичных представления.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-219">The view contains two partial views.</span></span> <span data-ttu-id="ce6fc-220">Второе частичное представление передает модель и объект `ViewData` в первое частичное представление.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="ce6fc-221">Используйте перегрузку конструктора `ViewDataDictionary`, чтобы передать новый словарь `ViewData` и при этом сохранить существующий словарь `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="ce6fc-222">*Views/Shared/_AuthorPartial.cshtml* — это первое частичное представление, указанное в файле разметки *Read.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="ce6fc-223">*Views/Shared/_ArticleSection.cshtml* — это второе частичное представление, указанное в файле разметки *Read.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="ce6fc-224">Во время выполнения частичные представления встраиваются в выходные данные родительского представления, которое также преобразовывается для просмотра в общем макете *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="ce6fc-225">Первое частичное представление отображает имя автора статьи и дату публикации:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="ce6fc-226">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="ce6fc-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="ce6fc-227">Это частичное представление из &lt;общего каталога файлов частичного представления&gt;.</span><span class="sxs-lookup"><span data-stu-id="ce6fc-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="ce6fc-228">11/19/1863 12:00:00 AM</span><span class="sxs-lookup"><span data-stu-id="ce6fc-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="ce6fc-229">Второе частичное представление отображает разделы статьи:</span><span class="sxs-lookup"><span data-stu-id="ce6fc-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="ce6fc-230">Section One Index: 0</span><span class="sxs-lookup"><span data-stu-id="ce6fc-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="ce6fc-231">Four score and seven years ago...</span><span class="sxs-lookup"><span data-stu-id="ce6fc-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="ce6fc-232">Section Two Index: 1</span><span class="sxs-lookup"><span data-stu-id="ce6fc-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="ce6fc-233">Now we are engaged in a great civil war, testing...</span><span class="sxs-lookup"><span data-stu-id="ce6fc-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="ce6fc-234">Section Three Index: 2</span><span class="sxs-lookup"><span data-stu-id="ce6fc-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="ce6fc-235">But, in a larger sense, we can not dedicate...</span><span class="sxs-lookup"><span data-stu-id="ce6fc-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce6fc-236">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ce6fc-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="ce6fc-237">Razor Справочник по синтаксису для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce6fc-237">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* [<span data-ttu-id="ce6fc-238">Razor Справочник по синтаксису для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce6fc-238">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
