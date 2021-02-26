---
title: Часть 3. Добавление представления в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 3 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 01/28/2021
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804614"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="b6d09-103">Часть 3. Добавление представления в приложение MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6d09-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="b6d09-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="b6d09-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b6d09-105">В этом разделе вы внесете изменения в класс `HelloWorldController` для использования файлов представления [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="b6d09-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="b6d09-106">Это позволяет аккуратно инкапсулировать процесс создания HTML-ответов в клиент.</span><span class="sxs-lookup"><span data-stu-id="b6d09-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="b6d09-107">Шаблоны представлений создаются с помощью Razor.</span><span class="sxs-lookup"><span data-stu-id="b6d09-107">View templates are created using Razor.</span></span> <span data-ttu-id="b6d09-108">Шаблоны представлений на основе Razor:</span><span class="sxs-lookup"><span data-stu-id="b6d09-108">Razor-based view templates:</span></span>

* <span data-ttu-id="b6d09-109">имеют расширение *CSHTML*;</span><span class="sxs-lookup"><span data-stu-id="b6d09-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="b6d09-110">реализуют удобный способ для создания выходных данных HTML с помощью C#.</span><span class="sxs-lookup"><span data-stu-id="b6d09-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="b6d09-111">Сейчас метод `Index` возвращает строку с сообщением в классе контроллера.</span><span class="sxs-lookup"><span data-stu-id="b6d09-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="b6d09-112">В классе `HelloWorldController` замените метод `Index` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="b6d09-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="b6d09-113">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="b6d09-113">The preceding code:</span></span>

* <span data-ttu-id="b6d09-114">вызывает метод <xref:Microsoft.AspNetCore.Mvc.Controller.View*> контроллера;</span><span class="sxs-lookup"><span data-stu-id="b6d09-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="b6d09-115">использует шаблон представления для создания HTML-ответа.</span><span class="sxs-lookup"><span data-stu-id="b6d09-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="b6d09-116">Методы контроллера:</span><span class="sxs-lookup"><span data-stu-id="b6d09-116">Controller methods:</span></span>

* <span data-ttu-id="b6d09-117">называются *методами действий*;</span><span class="sxs-lookup"><span data-stu-id="b6d09-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="b6d09-118">например, метод действия `Index` в предыдущем коде;</span><span class="sxs-lookup"><span data-stu-id="b6d09-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="b6d09-119">обычно возвращают <xref:Microsoft.AspNetCore.Mvc.IActionResult> или класс, производный от <xref:Microsoft.AspNetCore.Mvc.ActionResult>, а не тип, например `string`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="b6d09-120">Добавление представления</span><span class="sxs-lookup"><span data-stu-id="b6d09-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6d09-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6d09-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6d09-122">Щелкните правой кнопкой мыши папку *Views*, а затем выберите **Добавить > Новая папка**. Назовите папку *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="b6d09-123">Щелкните правой кнопкой мыши папку *Views/HelloWorld*, а затем выберите **Добавить > Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="b6d09-124">В диалоговом окне **Добавление нового элемента MvcMovie** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b6d09-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="b6d09-125">В поле поиска в правом верхнем углу введите *view* (представление).</span><span class="sxs-lookup"><span data-stu-id="b6d09-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="b6d09-126">Выберите **Представление Razor** .</span><span class="sxs-lookup"><span data-stu-id="b6d09-126">Select **Razor View**</span></span>
* <span data-ttu-id="b6d09-127">В поле *Имя* оставьте значение **Index.cshtml**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="b6d09-128">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="b6d09-128">Select **Add**</span></span>

![Диалоговое окно ''Добавление нового элемента''](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6d09-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6d09-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6d09-131">Добавьте представление `Index` для `HelloWorldController`:</span><span class="sxs-lookup"><span data-stu-id="b6d09-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="b6d09-132">Добавьте новую папку с именем *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="b6d09-133">Добавьте новый файл *Index.cshtml* в папку *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b6d09-134">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b6d09-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b6d09-135">Щелкните папку *Views*, удерживая нажатой клавишу CONTROL, а затем выберите **Добавить > Новая папка**. Назовите папку *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="b6d09-136">Щелкните папку *Views/HelloWorld*, удерживая нажатой клавишу CONTROL, а затем выберите **Добавить > Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="b6d09-137">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b6d09-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="b6d09-138">Выберите **ASP.NET Core** в области слева.</span><span class="sxs-lookup"><span data-stu-id="b6d09-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="b6d09-139">В области в центре выберите **Представление Razor — пусто**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="b6d09-140">В поле **Имя** введите *Index*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="b6d09-141">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-141">Select **New**.</span></span>

  ![Диалоговое окно ''Добавление нового элемента''](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="b6d09-143">Замените содержимое файла представления Razor *Views/HelloWorld/Index.cshtml* следующим.</span><span class="sxs-lookup"><span data-stu-id="b6d09-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="b6d09-144">Перейдите к `https://localhost:{PORT}/HelloWorld`:</span><span class="sxs-lookup"><span data-stu-id="b6d09-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="b6d09-145">Метод `Index` в `HelloWorldController` выполнил оператор `return View();`, который указал, что метод должен использовать файл шаблона представления для отображения ответа в браузере.</span><span class="sxs-lookup"><span data-stu-id="b6d09-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="b6d09-146">Имя файла шаблона представления не указано, MVC по умолчанию использует файл представления по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b6d09-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="b6d09-147">Если имя файла представления не указано, возвращается представление по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b6d09-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="b6d09-148">В этом примере представление по умолчанию имеет то же имя, что и метод действия `Index`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="b6d09-149">Используется шаблон представления */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="b6d09-150">На рисунке ниже показана строка "Hello from our View Template!",</span><span class="sxs-lookup"><span data-stu-id="b6d09-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="b6d09-151">которая жестко задана в представлении:</span><span class="sxs-lookup"><span data-stu-id="b6d09-151">hard-coded in the view:</span></span>

  ![Окно браузера](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="b6d09-153">Изменение представлений и страниц макета</span><span class="sxs-lookup"><span data-stu-id="b6d09-153">Change views and layout pages</span></span>

<span data-ttu-id="b6d09-154">Выберите ссылки в меню (**MvcMovie**, **Домашняя страница** и **Конфиденциальность**).</span><span class="sxs-lookup"><span data-stu-id="b6d09-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="b6d09-155">Меню на каждой странице имеют одинаковый макет.</span><span class="sxs-lookup"><span data-stu-id="b6d09-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="b6d09-156">Макет меню реализован в файле *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b6d09-157">Откройте файл *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b6d09-158">Шаблоны [макета](xref:mvc/views/layout) позволяют:</span><span class="sxs-lookup"><span data-stu-id="b6d09-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="b6d09-159">указать макет контейнера HTML сайта в одном месте;</span><span class="sxs-lookup"><span data-stu-id="b6d09-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="b6d09-160">применять макета контейнера HTML на нескольких страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="b6d09-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="b6d09-161">Найдите строку `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b6d09-162">`RenderBody` — это заполнитель, в котором отображаются все создаваемые страницы для определенных представлений, *упакованные* на странице макета.</span><span class="sxs-lookup"><span data-stu-id="b6d09-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b6d09-163">Например, если щелкнуть ссылку **Конфиденциальность**, представление **Views/Home/Privacy.cshtml** отображается в методе `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="b6d09-164">Изменение заголовка, нижнего колонтитула и ссылки меню в файле макета</span><span class="sxs-lookup"><span data-stu-id="b6d09-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="b6d09-165">Замените содержимое файла *Views/Shared/_Layout.cshtml* следующей разметкой.</span><span class="sxs-lookup"><span data-stu-id="b6d09-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="b6d09-166">Изменения выделены:</span><span class="sxs-lookup"><span data-stu-id="b6d09-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b6d09-167">Приведенная выше разметка вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="b6d09-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="b6d09-168">замена трех вхождений `MvcMovie` на `Movie App`;</span><span class="sxs-lookup"><span data-stu-id="b6d09-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="b6d09-169">Замена элемента привязки `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` на `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="b6d09-170">В приведенной выше разметке [атрибут вспомогательной функции тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-area=""` и значение атрибута были опущены, так как это приложение не использует области ([Areas](xref:mvc/controllers/areas)).</span><span class="sxs-lookup"><span data-stu-id="b6d09-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="b6d09-171">**Примечание**. Контроллер `Movies` не был реализован.</span><span class="sxs-lookup"><span data-stu-id="b6d09-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="b6d09-172">На этом этапе ссылка `Movie App` не работает.</span><span class="sxs-lookup"><span data-stu-id="b6d09-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="b6d09-173">Сохраните изменения и нажмите ссылку **Конфиденциальность**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="b6d09-174">Обратите внимание, что заголовок вкладки браузера изменяется на **Политика конфиденциальности — Movie App** вместо **Политика конфиденциальности — Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="b6d09-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Вкладка "Конфиденциальность"](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="b6d09-176">Щелкните ссылку **Home** (На главную).</span><span class="sxs-lookup"><span data-stu-id="b6d09-176">Select the **Home** link.</span></span>

<span data-ttu-id="b6d09-177">Обратите внимание, что в заголовке и тексте привязки также отображается **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="b6d09-178">Внеся одно изменение в шаблон макета, мы изменили заголовок и текст ссылки на всех страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="b6d09-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="b6d09-179">Просмотрите файл *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b6d09-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="b6d09-180">Файл *Views/_ViewStart.cshtml* предоставляет файл *Views/Shared/_Layout.cshtml* для каждого представления.</span><span class="sxs-lookup"><span data-stu-id="b6d09-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="b6d09-181">Свойство `Layout` может задавать другое представление макета или иметь значение `null`, при котором макет не используется.</span><span class="sxs-lookup"><span data-stu-id="b6d09-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="b6d09-182">Откройте файл представления *Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="b6d09-183">Измените заголовок и элемент `<h2>`, как выделено ниже:</span><span class="sxs-lookup"><span data-stu-id="b6d09-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="b6d09-184">Заголовок и элемент `<h2>` немного отличаются, чтобы вы видели, какой именно фрагмент кода изменяет отображение.</span><span class="sxs-lookup"><span data-stu-id="b6d09-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="b6d09-185">`ViewData["Title"] = "Movie List";` в приведенном выше коде присваивает свойству `Title` словаря `ViewData` значение "Movie List".</span><span class="sxs-lookup"><span data-stu-id="b6d09-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="b6d09-186">Свойство `Title` используется в элементе HTML `<title>` на странице макета:</span><span class="sxs-lookup"><span data-stu-id="b6d09-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="b6d09-187">Сохраните изменения и перейдите к `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="b6d09-188">Обратите внимание, что были изменены следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="b6d09-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="b6d09-189">Заголовок браузера.</span><span class="sxs-lookup"><span data-stu-id="b6d09-189">Browser title.</span></span>
* <span data-ttu-id="b6d09-190">Основной заголовок.</span><span class="sxs-lookup"><span data-stu-id="b6d09-190">Primary heading.</span></span>
* <span data-ttu-id="b6d09-191">Дополнительные заголовки.</span><span class="sxs-lookup"><span data-stu-id="b6d09-191">Secondary headings.</span></span>

<span data-ttu-id="b6d09-192">Если в браузере изменения не отображаются, это может означать, что содержимое кэшировано.</span><span class="sxs-lookup"><span data-stu-id="b6d09-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="b6d09-193">В этом случае нажмите в браузере клавиши CTRL+F5 для принудительной загрузки ответа сервера.</span><span class="sxs-lookup"><span data-stu-id="b6d09-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="b6d09-194">Заголовок браузера создается с помощью атрибута `ViewData["Title"]`, который задается в шаблоне представления *Index.cshtml* и дополнительной строки "- Movie App", добавляемой в файл макета.</span><span class="sxs-lookup"><span data-stu-id="b6d09-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="b6d09-195">Содержимое шаблона представления *Index.cshtml* объединяется с шаблоном представления *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="b6d09-196">В браузер отправляется один HTML-ответ.</span><span class="sxs-lookup"><span data-stu-id="b6d09-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="b6d09-197">Шаблоны макетов позволяют легко вносить изменения, применяемые ко всем страницам в приложении.</span><span class="sxs-lookup"><span data-stu-id="b6d09-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="b6d09-198">Дополнительные сведения см. в разделе [Макет](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="b6d09-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Представление списка фильмов](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="b6d09-200">Небольшой фрагмент данных (сообщение "Hello from our View Template!")</span><span class="sxs-lookup"><span data-stu-id="b6d09-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="b6d09-201">жестко задан в коде.</span><span class="sxs-lookup"><span data-stu-id="b6d09-201">message, is hard-coded however.</span></span> <span data-ttu-id="b6d09-202">Приложение MVC предоставляет представление, вы реализуете контроллер, однако модели на данный момент еще нет.</span><span class="sxs-lookup"><span data-stu-id="b6d09-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="b6d09-203">Передача данных из контроллера в представление</span><span class="sxs-lookup"><span data-stu-id="b6d09-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="b6d09-204">Действия контроллера вызываются в ответ на входящий запрос URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="b6d09-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="b6d09-205">Код, обрабатывающий входящие запросы браузера, добавляется в класс контроллера.</span><span class="sxs-lookup"><span data-stu-id="b6d09-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="b6d09-206">Контроллер извлекает данные из источника данных и определяет тип ответа, который будет отправлен в браузер.</span><span class="sxs-lookup"><span data-stu-id="b6d09-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="b6d09-207">Контроллер может использовать шаблоны представлений для создания и форматирования ответа HTML, отправляемого браузеру.</span><span class="sxs-lookup"><span data-stu-id="b6d09-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="b6d09-208">Контроллер отвечает за предоставление данных, необходимых шаблону представления для отображения ответа.</span><span class="sxs-lookup"><span data-stu-id="b6d09-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="b6d09-209">Шаблоны представлений **не** должны:</span><span class="sxs-lookup"><span data-stu-id="b6d09-209">View templates should **not**:</span></span>

* <span data-ttu-id="b6d09-210">выполнять бизнес-логику;</span><span class="sxs-lookup"><span data-stu-id="b6d09-210">Do business logic</span></span>
* <span data-ttu-id="b6d09-211">непосредственно взаимодействовать с базой данных.</span><span class="sxs-lookup"><span data-stu-id="b6d09-211">Interact with a database directly.</span></span>

<span data-ttu-id="b6d09-212">Вместо этого они должны работать только с данными, которые им предоставляет контроллер.</span><span class="sxs-lookup"><span data-stu-id="b6d09-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="b6d09-213">Подобное разделение сфер ответственности позволяет обеспечить максимальную оптимизацию кода, а также удобство его</span><span class="sxs-lookup"><span data-stu-id="b6d09-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="b6d09-214">очистки;</span><span class="sxs-lookup"><span data-stu-id="b6d09-214">Clean.</span></span>
* <span data-ttu-id="b6d09-215">тестирования;</span><span class="sxs-lookup"><span data-stu-id="b6d09-215">Testable.</span></span>
* <span data-ttu-id="b6d09-216">обслуживания.</span><span class="sxs-lookup"><span data-stu-id="b6d09-216">Maintainable.</span></span>

<span data-ttu-id="b6d09-217">В настоящее время метод `Welcome` в классе `HelloWorldController` принимает параметры `name` и `ID`, после чего выводит значения непосредственно в браузер.</span><span class="sxs-lookup"><span data-stu-id="b6d09-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="b6d09-218">Вместо отображения ответа в виде строки настройте контроллер для использования шаблона представления.</span><span class="sxs-lookup"><span data-stu-id="b6d09-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="b6d09-219">Шаблон представления создает динамический ответ, для получения которого необходимо передать соответствующие данные из контроллера в представление.</span><span class="sxs-lookup"><span data-stu-id="b6d09-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="b6d09-220">Для этого контроллер может поместить динамические данные (параметры), которые требуются шаблону представления, в словарь `ViewData`,</span><span class="sxs-lookup"><span data-stu-id="b6d09-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="b6d09-221">к которому впоследствии будет обращаться этот шаблон за динамическими данными.</span><span class="sxs-lookup"><span data-stu-id="b6d09-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="b6d09-222">В файле *HelloWorldController.cs* измените метод `Welcome` так, чтобы добавлять значения `Message` и `NumTimes` в словарь `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="b6d09-223">Словарь `ViewData` представляет собой динамический объект, а это означает, что можно использовать любой тип.</span><span class="sxs-lookup"><span data-stu-id="b6d09-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="b6d09-224">Объект `ViewData` не имеет определенных свойств, пока не будет добавлен какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="b6d09-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="b6d09-225">[Система привязки модели MVC](xref:mvc/models/model-binding) автоматически сопоставляет именованные параметры `name` и `numTimes` из строки запроса с параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="b6d09-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="b6d09-226">Полный `HelloWorldController`:</span><span class="sxs-lookup"><span data-stu-id="b6d09-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="b6d09-227">Объект словаря `ViewData` содержит данные, которые будут передаваться в представление.</span><span class="sxs-lookup"><span data-stu-id="b6d09-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="b6d09-228">Создайте шаблон представления экрана приветствия с именем *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="b6d09-229">В шаблоне представления *Welcome.cshtml* создайте цикл, который будет отображать строку "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="b6d09-230">Замените содержимое файла *Views/HelloWorld/Welcome.cshtml* следующим:</span><span class="sxs-lookup"><span data-stu-id="b6d09-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="b6d09-231">Сохраните изменения и перейдите по следующему URL-адресу:</span><span class="sxs-lookup"><span data-stu-id="b6d09-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="b6d09-232">Данные извлекаются по URL-адресу и передаются в контроллер с помощью [средства привязки модели MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="b6d09-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="b6d09-233">Контроллер упаковывает данные в словарь `ViewData` и передает этот объект в представление.</span><span class="sxs-lookup"><span data-stu-id="b6d09-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="b6d09-234">Представление отображает данные в формате HTML в браузере.</span><span class="sxs-lookup"><span data-stu-id="b6d09-234">The view then renders the data as HTML to the browser.</span></span>

![Представление конфиденциальности, в котором отображается приветственный заголовок и четыре раза выводится фраза Hello Rick.](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="b6d09-236">В примере выше мы использовали словарь `ViewData` для передачи данных из контроллера в представление.</span><span class="sxs-lookup"><span data-stu-id="b6d09-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="b6d09-237">Далее в этом руководстве для передачи данных из контроллера в представление мы будем использовать модель представления.</span><span class="sxs-lookup"><span data-stu-id="b6d09-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="b6d09-238">Подход к передаче данных на основе модели представления является предпочтительным относительно применения словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="b6d09-239">В следующем руководстве создается база данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="b6d09-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b6d09-240">[Назад: Добавление контроллера](adding-controller.md)
> [Далее: Добавление модели](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="b6d09-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b6d09-241">В этом разделе вы измените класс `HelloWorldController`, чтобы использовать файлы представления [Razor](xref:mvc/views/razor) для четкой инкапсуляции процесса создания HTML-ответов в клиент.</span><span class="sxs-lookup"><span data-stu-id="b6d09-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="b6d09-242">Файл шаблона представления создается с помощью Razor.</span><span class="sxs-lookup"><span data-stu-id="b6d09-242">You create a view template file using Razor.</span></span> <span data-ttu-id="b6d09-243">Шаблоны представления на основе Razor имеют расширение файла *CSHTML*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="b6d09-244">Они реализуют удобный способ для создания выходных данных HTML с помощью C#.</span><span class="sxs-lookup"><span data-stu-id="b6d09-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="b6d09-245">Сейчас метод `Index` возвращает строку с сообщением, которое жестко задано в классе контроллера.</span><span class="sxs-lookup"><span data-stu-id="b6d09-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="b6d09-246">В классе `HelloWorldController` замените метод `Index` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="b6d09-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="b6d09-247">Предыдущий код вызывает метод <xref:Microsoft.AspNetCore.Mvc.Controller.View*> контроллера.</span><span class="sxs-lookup"><span data-stu-id="b6d09-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="b6d09-248">В нем используется шаблон представления для создания HTML-ответа.</span><span class="sxs-lookup"><span data-stu-id="b6d09-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="b6d09-249">Методы контроллера (также называются *методами действия*), такие как приведенный выше метод `Index`, обычно возвращают <xref:Microsoft.AspNetCore.Mvc.IActionResult> (или производный от <xref:Microsoft.AspNetCore.Mvc.ActionResult> класс), а не типы, такие как `string`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="b6d09-250">Добавление представления</span><span class="sxs-lookup"><span data-stu-id="b6d09-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6d09-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6d09-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6d09-252">Щелкните правой кнопкой мыши папку *Views*, а затем выберите **Добавить > Новая папка**. Назовите папку *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="b6d09-253">Щелкните правой кнопкой мыши папку *Views/HelloWorld*, а затем выберите **Добавить > Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="b6d09-254">В диалоговом окне **Добавление нового элемента MvcMovie** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b6d09-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="b6d09-255">В поле поиска в правом верхнем углу введите *view* (представление).</span><span class="sxs-lookup"><span data-stu-id="b6d09-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="b6d09-256">Выберите **Представление Razor** .</span><span class="sxs-lookup"><span data-stu-id="b6d09-256">Select **Razor View**</span></span>

  * <span data-ttu-id="b6d09-257">В поле *Имя* оставьте значение **Index.cshtml**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="b6d09-258">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="b6d09-258">Select **Add**</span></span>

![Диалоговое окно ''Добавление нового элемента''](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6d09-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6d09-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6d09-261">Добавьте представление `Index` для `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="b6d09-262">Добавьте новую папку с именем *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="b6d09-263">Добавьте новый файл *Index.cshtml* в папку *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b6d09-264">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b6d09-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b6d09-265">Щелкните правой кнопкой мыши папку *Views*, а затем выберите **Добавить > Новая папка**. Назовите папку *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="b6d09-266">Щелкните правой кнопкой мыши папку *Views/HelloWorld*, а затем выберите **Добавить > Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="b6d09-267">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b6d09-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b6d09-268">В левой области выберите **Веб**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="b6d09-269">В центральной области выберите **Пустой HTML-файл**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="b6d09-270">В поле **Имя** введите *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="b6d09-271">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-271">Select **New**.</span></span>

![Диалоговое окно ''Добавление нового элемента''](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="b6d09-273">Замените содержимое файла представления Razor *Views/HelloWorld/Index.cshtml* следующим.</span><span class="sxs-lookup"><span data-stu-id="b6d09-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="b6d09-274">Перейдите к `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b6d09-275">Метод `Index` в `HelloWorldController` сделал совсем мало. Он выполнил оператор `return View();`, который указал, что метод должен использовать файл шаблона представления для отображения ответа в браузере.</span><span class="sxs-lookup"><span data-stu-id="b6d09-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="b6d09-276">Так как имя файла шаблона представления не указано, MVC по умолчанию использует файл представления по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b6d09-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="b6d09-277">Файл представления по умолчанию имеет имя, совпадающее с именем метода (`Index`), поэтому используется файл */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="b6d09-278">На рисунке ниже показана строка "Hello from our View Template!",</span><span class="sxs-lookup"><span data-stu-id="b6d09-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="b6d09-279">которая жестко задана в представлении.</span><span class="sxs-lookup"><span data-stu-id="b6d09-279">hard-coded in the view.</span></span>

![Окно браузера](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="b6d09-281">Изменение представлений и страниц макета</span><span class="sxs-lookup"><span data-stu-id="b6d09-281">Change views and layout pages</span></span>

<span data-ttu-id="b6d09-282">Выберите ссылки в меню (**MvcMovie**, **Домашняя страница** и **Конфиденциальность**).</span><span class="sxs-lookup"><span data-stu-id="b6d09-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="b6d09-283">Меню на каждой странице имеют одинаковый макет.</span><span class="sxs-lookup"><span data-stu-id="b6d09-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="b6d09-284">Макет меню реализован в файле *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b6d09-285">Откройте файл *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b6d09-286">С помощью шаблонов [макета](xref:mvc/views/layout) можно в одном месте задать макет контейнера HTML для всего сайта и затем использовать его на разных страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="b6d09-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="b6d09-287">Найдите строку `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b6d09-288">`RenderBody` — это заполнитель, в котором отображаются все создаваемые страницы для определенных представлений, *упакованные* на странице макета.</span><span class="sxs-lookup"><span data-stu-id="b6d09-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b6d09-289">Например, если щелкнуть ссылку **Конфиденциальность**, представление **Views/Home/Privacy.cshtml** отображается в методе `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="b6d09-290">Изменение заголовка, нижнего колонтитула и ссылки меню в файле макета</span><span class="sxs-lookup"><span data-stu-id="b6d09-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="b6d09-291">В элементах заголовка и нижнего колонтитула измените `MvcMovie` на `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="b6d09-292">Измените элемент привязки `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` на `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="b6d09-293">Эти изменения выделены в следующей разметке:</span><span class="sxs-lookup"><span data-stu-id="b6d09-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="b6d09-294">В приведенной выше разметке [атрибут вспомогательной функции тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-area` был опущен, так как это приложение не использует [области](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="b6d09-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="b6d09-295">**Примечание**. Контроллер `Movies` не был реализован.</span><span class="sxs-lookup"><span data-stu-id="b6d09-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="b6d09-296">На этом этапе ссылка `Movie App` не работает.</span><span class="sxs-lookup"><span data-stu-id="b6d09-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="b6d09-297">Сохраните изменения и нажмите на ссылку **Конфиденциальность**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="b6d09-298">Обратите внимание, что заголовок вкладки браузера изменяется на **Политика конфиденциальности — Movie App** вместо **Политика конфиденциальности — Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="b6d09-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Вкладка "Конфиденциальность"](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="b6d09-300">Выберите ссылку **Домашняя страница** и убедитесь, что в заголовке и тексте привязки также отображается **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="b6d09-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="b6d09-301">Таким образом, внеся одно изменение в шаблон макета, мы изменили заголовок и текст ссылки на всех страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="b6d09-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="b6d09-302">Просмотрите файл *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b6d09-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="b6d09-303">Файл *Views/_ViewStart.cshtml* предоставляет файл *Views/Shared/_Layout.cshtml* для каждого представления.</span><span class="sxs-lookup"><span data-stu-id="b6d09-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="b6d09-304">Свойство `Layout` может задавать другое представление макета или иметь значение `null`, при котором макет не используется.</span><span class="sxs-lookup"><span data-stu-id="b6d09-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="b6d09-305">Измените заголовок и элемент `<h2>` файла представления *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b6d09-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="b6d09-306">Заголовок и элемент `<h2>` немного отличаются, чтобы вы видели, какой именно фрагмент кода изменяет отображение.</span><span class="sxs-lookup"><span data-stu-id="b6d09-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="b6d09-307">`ViewData["Title"] = "Movie List";` в приведенном выше коде присваивает свойству `Title` словаря `ViewData` значение "Movie List".</span><span class="sxs-lookup"><span data-stu-id="b6d09-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="b6d09-308">Свойство `Title` используется в элементе HTML `<title>` на странице макета:</span><span class="sxs-lookup"><span data-stu-id="b6d09-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="b6d09-309">Сохраните изменения и перейдите к `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b6d09-310">Обратите внимание, что основной и дополнительный заголовки браузера изменились.</span><span class="sxs-lookup"><span data-stu-id="b6d09-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="b6d09-311">(Если изменения не отображаются, возможно, вы просматриваете кэшированное содержимое.</span><span class="sxs-lookup"><span data-stu-id="b6d09-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="b6d09-312">В этом случае нажмите в браузере клавиши CTRL+F5 для принудительной загрузки ответа сервера.) Заголовок браузера создается с помощью атрибута `ViewData["Title"]`, который задается в шаблоне представления *Index.cshtml* и дополнительной строки "- Movie App", добавляемой в файл макета.</span><span class="sxs-lookup"><span data-stu-id="b6d09-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="b6d09-313">Также обратите внимание, что содержимое шаблона представления *Index.cshtml* объединяется с шаблоном представления *Views/Shared/_Layout.cshtml*, а в браузер отправляется один ответ HTML.</span><span class="sxs-lookup"><span data-stu-id="b6d09-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="b6d09-314">С помощью шаблонов макета можно легко вносить изменения, которые применяются ко всем страницам приложения.</span><span class="sxs-lookup"><span data-stu-id="b6d09-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="b6d09-315">Дополнительные сведения см. в разделе [Макет](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="b6d09-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Представление списка фильмов](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="b6d09-317">В этом примере небольшой фрагмент данных (сообщение "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="b6d09-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="b6d09-318">) жестко задан в коде.</span><span class="sxs-lookup"><span data-stu-id="b6d09-318">message) is hard-coded, though.</span></span> <span data-ttu-id="b6d09-319">Приложение MVC предоставляет представление, вы реализуете контроллер, однако модели на данный момент еще нет.</span><span class="sxs-lookup"><span data-stu-id="b6d09-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="b6d09-320">Передача данных из контроллера в представление</span><span class="sxs-lookup"><span data-stu-id="b6d09-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="b6d09-321">Действия контроллера вызываются в ответ на входящий запрос URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="b6d09-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="b6d09-322">Код, обрабатывающий входящие запросы браузера, добавляется в класс контроллера.</span><span class="sxs-lookup"><span data-stu-id="b6d09-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="b6d09-323">Контроллер извлекает данные из источника данных и определяет тип ответа, который будет отправлен в браузер.</span><span class="sxs-lookup"><span data-stu-id="b6d09-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="b6d09-324">Контроллер может использовать шаблоны представлений для создания и форматирования ответа HTML, отправляемого браузеру.</span><span class="sxs-lookup"><span data-stu-id="b6d09-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="b6d09-325">Контроллер отвечает за предоставление данных, необходимых шаблону представления для отображения ответа.</span><span class="sxs-lookup"><span data-stu-id="b6d09-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="b6d09-326">Рекомендации: шаблоны представлений **не должны** выполнять бизнес-логику или напрямую взаимодействовать с базой данных.</span><span class="sxs-lookup"><span data-stu-id="b6d09-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="b6d09-327">Вместо этого они должны работать только с данными, которые им предоставляет контроллер.</span><span class="sxs-lookup"><span data-stu-id="b6d09-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="b6d09-328">Подобное разделение сфер ответственности позволяет обеспечить максимальную оптимизацию кода, а также удобство его тестирования и поддержки.</span><span class="sxs-lookup"><span data-stu-id="b6d09-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="b6d09-329">В настоящее время метод `Welcome` в классе `HelloWorldController` принимает параметры `name` и `ID`, после чего выводит значения непосредственно в браузер.</span><span class="sxs-lookup"><span data-stu-id="b6d09-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="b6d09-330">Вместо отображения ответа в виде строки настройте контроллер для использования шаблона представления.</span><span class="sxs-lookup"><span data-stu-id="b6d09-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="b6d09-331">Шаблон представления создает динамический ответ, для получения которого необходимо передать соответствующие фрагменты данных из контроллера в представление.</span><span class="sxs-lookup"><span data-stu-id="b6d09-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="b6d09-332">Для этого контроллер может поместить динамические данные (параметры), которые требуются шаблону представления, в словарь `ViewData`, к которому впоследствии будет обращаться этот шаблон.</span><span class="sxs-lookup"><span data-stu-id="b6d09-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="b6d09-333">В файле *HelloWorldController.cs* измените метод `Welcome` так, чтобы добавлять значения `Message` и `NumTimes` в словарь `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="b6d09-334">Словарь `ViewData` является динамическим объектом, то есть можно использовать любой тип. Объект `ViewData` не имеет определенных свойств, пока в него не будут добавлены какие-либо данные.</span><span class="sxs-lookup"><span data-stu-id="b6d09-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="b6d09-335">[Система привязки модели MVC](xref:mvc/models/model-binding) автоматически сопоставляет именованные параметры (`name` и `numTimes`) из строк запроса в адресной строке с параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="b6d09-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="b6d09-336">Полный файл *HelloWorldController.cs* выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="b6d09-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="b6d09-337">Объект словаря `ViewData` содержит данные, которые будут передаваться в представление.</span><span class="sxs-lookup"><span data-stu-id="b6d09-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="b6d09-338">Создайте шаблон представления экрана приветствия с именем *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b6d09-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="b6d09-339">В шаблоне представления *Welcome.cshtml* создайте цикл, который будет отображать строку "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="b6d09-340">Замените содержимое файла *Views/HelloWorld/Welcome.cshtml* следующим:</span><span class="sxs-lookup"><span data-stu-id="b6d09-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="b6d09-341">Сохраните изменения и перейдите по следующему URL-адресу:</span><span class="sxs-lookup"><span data-stu-id="b6d09-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="b6d09-342">Данные извлекаются по URL-адресу и передаются в контроллер с помощью [средства привязки модели MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="b6d09-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="b6d09-343">Контроллер упаковывает данные в словарь `ViewData` и передает этот объект в представление.</span><span class="sxs-lookup"><span data-stu-id="b6d09-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="b6d09-344">Представление отображает данные в формате HTML в браузере.</span><span class="sxs-lookup"><span data-stu-id="b6d09-344">The view then renders the data as HTML to the browser.</span></span>

![Представление конфиденциальности, в котором отображается приветственный заголовок и четыре раза выводится фраза Hello Rick.](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="b6d09-346">В примере выше мы использовали словарь `ViewData` для передачи данных из контроллера в представление.</span><span class="sxs-lookup"><span data-stu-id="b6d09-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="b6d09-347">Далее в этом руководстве для передачи данных из контроллера в представление мы будем использовать модель представления.</span><span class="sxs-lookup"><span data-stu-id="b6d09-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="b6d09-348">Подход к передаче данных на основе модели представления является предпочтительным относительно применения словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="b6d09-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="b6d09-349">Дополнительные сведения см. в статье об [использовании ViewBag, ViewData или TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="b6d09-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="b6d09-350">В следующем руководстве создается база данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="b6d09-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b6d09-351">[Назад](adding-controller.md)
> [Вперед](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="b6d09-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
