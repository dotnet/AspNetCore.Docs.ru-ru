---
title: Часть 7. Добавление поиска в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 7 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: dbd970dcf8877d1bb7dc99ba5d31bd311f8a7663
ms.sourcegitcommit: 2fea9bfe6127bbbdbb438406c82529b2bc331944
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065549"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="3484d-103">Часть 7. Добавление поиска в приложение MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3484d-103">Part 7, add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="3484d-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="3484d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3484d-105">В этом разделе вы добавите в метод действия `Index` возможности поиска, которые позволяют выполнять поиск фильмов по *жанру* или *названию*.</span><span class="sxs-lookup"><span data-stu-id="3484d-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="3484d-106">Обновите метод `Index`, находящийся в файле *Controllers/ MoviesController.cs*, добавив следующий код:</span><span class="sxs-lookup"><span data-stu-id="3484d-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="3484d-107">В первой строке метода действия `Index` создается запрос [LINQ](/dotnet/standard/using-linq) для выбора фильмов:</span><span class="sxs-lookup"><span data-stu-id="3484d-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="3484d-108">Этот запрос *только* определяется в этой точке и **не** выполняется для базы данных.</span><span class="sxs-lookup"><span data-stu-id="3484d-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="3484d-109">Если параметр `searchString` содержит строку, запрос фильмов изменяется для фильтрации по значению в строке поиска:</span><span class="sxs-lookup"><span data-stu-id="3484d-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="3484d-110">Приведенный выше код `s => s.Title.Contains()` представляет собой [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="3484d-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="3484d-111">Лямбда-выражения используются в запросах [LINQ](/dotnet/standard/using-linq) на основе методов в качестве аргументов стандартных методов операторов запроса, таких как метод [Where](/dotnet/api/system.linq.enumerable.where) или `Contains` (используется в приведенном выше коде).</span><span class="sxs-lookup"><span data-stu-id="3484d-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="3484d-112">Запросы LINQ не выполняются, если они определяются или изменяются путем вызова метода, например `Where`, `Contains` или `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="3484d-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="3484d-113">Вместо этого выполнение запроса откладывается.</span><span class="sxs-lookup"><span data-stu-id="3484d-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="3484d-114">Это означает, что вычисление выражения откладывается до тех пор, пока не будет выполнена итерация его реализованного значения или пока не будет вызван метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="3484d-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="3484d-115">Дополнительные сведения об отложенном и немедленном выполнении запросов см. в разделе [Выполнение запроса](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="3484d-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="3484d-116">Примечание. Метод [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) выполняется в базе данных, а не в коде C#, приведенном выше.</span><span class="sxs-lookup"><span data-stu-id="3484d-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="3484d-117">Регистр символов запроса учитывается в зависимости от параметров базы данных и сортировки.</span><span class="sxs-lookup"><span data-stu-id="3484d-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="3484d-118">В SQL Server метод[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) сопоставляется с [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), в котором регистр символов не учитывается.</span><span class="sxs-lookup"><span data-stu-id="3484d-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="3484d-119">В SQLite при параметрах сортировки по умолчанию регистр символов учитывается.</span><span class="sxs-lookup"><span data-stu-id="3484d-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="3484d-120">Перейдите к `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="3484d-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="3484d-121">Добавьте в URL-адрес строку запроса, например `?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="3484d-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="3484d-122">Отображаются отфильтрованные фильмы.</span><span class="sxs-lookup"><span data-stu-id="3484d-122">The filtered movies are displayed.</span></span>

![Представление Index](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="3484d-124">Если вы изменили сигнатуру метода `Index` и включили в нее параметр с именем `id`, параметр `id` будет соответствовать необязательному заполнителю `{id}` для маршрутов по умолчанию, который задан в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3484d-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?highlight=5&name=snippet_route)]

<span data-ttu-id="3484d-125">Измените параметр на `id`, а все вхождения `searchString` — на `id`.</span><span class="sxs-lookup"><span data-stu-id="3484d-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="3484d-126">Предыдущий метод `Index`:</span><span class="sxs-lookup"><span data-stu-id="3484d-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="3484d-127">Обновленный метод `Index` с параметром `id`:</span><span class="sxs-lookup"><span data-stu-id="3484d-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="3484d-128">Теперь можно передать заголовок поиска в качестве данных маршрута (сегмент URL-адреса) вместо значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="3484d-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Представление Index, в URL-адрес которого добавлено слово ghost, возвращает два фильма: Ghostbusters и Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="3484d-130">Тем не менее пользователи вряд ли будут каждый раз изменять URL-адрес для поиска фильмов.</span><span class="sxs-lookup"><span data-stu-id="3484d-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="3484d-131">Итак, теперь вам необходимо добавить элементы пользовательского интерфейса для удобства фильтрации фильмов.</span><span class="sxs-lookup"><span data-stu-id="3484d-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="3484d-132">Если вы изменили сигнатуру метода `Index` для тестирования передачи параметра `ID` с привязкой к маршруту, измените ее снова, чтобы она снова принимала параметр `searchString`:</span><span class="sxs-lookup"><span data-stu-id="3484d-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="3484d-133">Откройте файл *Views/Movies/Index.cshtml* и добавьте разметку `<form>`, которая выделена ниже:</span><span class="sxs-lookup"><span data-stu-id="3484d-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="3484d-134">Тег HTML `<form>` использует [вспомогательную функцию тега Form](xref:mvc/views/working-with-forms), чтобы при отправке формы строка фильтра передавалась в действие `Index` контроллера movies.</span><span class="sxs-lookup"><span data-stu-id="3484d-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="3484d-135">Сохраните изменения и протестируйте фильтр.</span><span class="sxs-lookup"><span data-stu-id="3484d-135">Save your changes and then test the filter.</span></span>

![Представление Index со словом ghost в текстовом поле фильтра по названию](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="3484d-137">Вопреки ожиданиям, перегрузка `[HttpPost]` для метода `Index` отсутствует.</span><span class="sxs-lookup"><span data-stu-id="3484d-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="3484d-138">Она не нужна, поскольку метод не изменяет состояние приложения и просто выполняет фильтрацию данных.</span><span class="sxs-lookup"><span data-stu-id="3484d-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="3484d-139">Можно добавить следующий метод `[HttpPost] Index`.</span><span class="sxs-lookup"><span data-stu-id="3484d-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="3484d-140">Параметр `notUsed` используется для создания перегрузки метода `Index`.</span><span class="sxs-lookup"><span data-stu-id="3484d-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="3484d-141">Это мы обсудим далее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="3484d-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="3484d-142">При добавлении этого метода вызывающий метод действия будет сопоставлять метод `[HttpPost] Index`, а метод `[HttpPost] Index` будет выполняться, как показано на рисунке ниже.</span><span class="sxs-lookup"><span data-stu-id="3484d-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Окно браузера с ответом приложения From HttpPost Index: фильтр по слову ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="3484d-144">Тем не менее при добавлении этой версии `[HttpPost]` метода `Index` существует ограничение на общую реализацию.</span><span class="sxs-lookup"><span data-stu-id="3484d-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="3484d-145">Допустим, вам необходимо добавить в закладки конкретный поиск или отправить друзьям ссылку, по которой они могут просмотреть аналогичный отфильтрованный список фильмов.</span><span class="sxs-lookup"><span data-stu-id="3484d-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="3484d-146">Обратите внимание, что URL-адрес запроса HTTP POST совпадает с URL-адресом запроса GET (localhost:{ПОРТ}/Movies/Index) — в URL-адресе отсутствуют сведения о поиске.</span><span class="sxs-lookup"><span data-stu-id="3484d-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="3484d-147">Данные строки поиска отправляются на сервер в виде [значения поля формы](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="3484d-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="3484d-148">Вы можете проверить это с помощью средств разработчика для браузера или [инструмента Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="3484d-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="3484d-149">На рисунке ниже показаны средства разработчика для браузера Chrome:</span><span class="sxs-lookup"><span data-stu-id="3484d-149">The image below shows the Chrome browser Developer tools:</span></span>

![Вкладка "Сеть" средств разработчика в Microsoft Edge с телом запроса со значением searchString, равным ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="3484d-151">В теле запроса отображается параметр поиска и маркер [XSRF](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="3484d-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="3484d-152">Обратите внимание, что, как описывается в предыдущем руководстве, [вспомогательная функция тега Form](xref:mvc/views/working-with-forms) создает маркер защиты от подделки [XSRF](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="3484d-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="3484d-153">Поскольку мы не изменяем данные, проверять маркер безопасности в методе контроллера не нужно.</span><span class="sxs-lookup"><span data-stu-id="3484d-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="3484d-154">Так как параметр поиска находится в теле запроса, а не в URL-адресе, эти сведения о поиске нельзя добавить в закладки или открыть для общего доступа.</span><span class="sxs-lookup"><span data-stu-id="3484d-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="3484d-155">Для устранения этой проблемы укажите, что запрос типа `HTTP GET` должен находиться в файле *Views/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3484d-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="3484d-156">После отправки поиска URL-адрес содержит строку поискового запроса.</span><span class="sxs-lookup"><span data-stu-id="3484d-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="3484d-157">Поиск также переносится в метод `HttpGet Index`, даже если у вас определен метод `HttpPost Index`.</span><span class="sxs-lookup"><span data-stu-id="3484d-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Окно браузера с фрагментом searchString=ghost в URL-адресе, которое возвращает фильмы Ghostbusters и Ghostbusters 2, с текстом ghost в названии](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="3484d-159">В следующем примере разметки показано изменение тега `form`:</span><span class="sxs-lookup"><span data-stu-id="3484d-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="3484d-160">Добавление поиска по жанру</span><span class="sxs-lookup"><span data-stu-id="3484d-160">Add Search by genre</span></span>

<span data-ttu-id="3484d-161">Добавьте следующий класс `MovieGenreViewModel` в папку *Models*:</span><span class="sxs-lookup"><span data-stu-id="3484d-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="3484d-162">Модель представления фильмов по жанру будет содержать:</span><span class="sxs-lookup"><span data-stu-id="3484d-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="3484d-163">Список фильмов.</span><span class="sxs-lookup"><span data-stu-id="3484d-163">A list of movies.</span></span>
* <span data-ttu-id="3484d-164">Объект `SelectList` со списком жанров.</span><span class="sxs-lookup"><span data-stu-id="3484d-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="3484d-165">В этом списке пользователь может выбрать жанр фильма.</span><span class="sxs-lookup"><span data-stu-id="3484d-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="3484d-166">Объект `MovieGenre`, содержащий выбранный жанр.</span><span class="sxs-lookup"><span data-stu-id="3484d-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="3484d-167">`SearchString`, содержащий текст, который пользователи вводят в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="3484d-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="3484d-168">Замените метод `Index` в файле `MoviesController.cs` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="3484d-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="3484d-169">Следующий код определяет запрос `LINQ`, который извлекает все жанры из базы данных.</span><span class="sxs-lookup"><span data-stu-id="3484d-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="3484d-170">Объект `SelectList` со списком жанров создается путем проецирования отдельных жанров (это необходимо, чтобы исключить повторяющиеся жанры).</span><span class="sxs-lookup"><span data-stu-id="3484d-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="3484d-171">Когда пользователь выполняет поиск элемента, значение поиска сохраняется в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="3484d-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="3484d-172">Добавление поиска по жанру в представление индекса</span><span class="sxs-lookup"><span data-stu-id="3484d-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="3484d-173">Обновите файл `Index.cshtml`, находящийся в папке *Views/Movies/* , следующим образом:</span><span class="sxs-lookup"><span data-stu-id="3484d-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="3484d-174">Проверьте лямбда-выражение, которое используется в следующем вспомогательном методе HTML:</span><span class="sxs-lookup"><span data-stu-id="3484d-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="3484d-175">В предыдущем коде вспомогательный метод HTML `DisplayNameFor` проверяет свойство `Title`, указанное в лямбда-выражении, и определяет отображаемое имя.</span><span class="sxs-lookup"><span data-stu-id="3484d-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="3484d-176">Поскольку лямбда-выражение проверяется, а не вычисляется, в том случае, если `model`, `model.Movies` или `model.Movies[0]` имеют значение `null` или пусты, не происходит нарушение прав доступа.</span><span class="sxs-lookup"><span data-stu-id="3484d-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="3484d-177">При вычислении лямбда-выражения (например, `@Html.DisplayFor(modelItem => item.Title)`) вычисляются значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="3484d-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="3484d-178">Проверьте работу приложения, выполнив поиск по жанру, по названию фильма и по обоим этим параметрам:</span><span class="sxs-lookup"><span data-stu-id="3484d-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Окно браузера с результатами https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="3484d-180">[Назад](controller-methods-views.md)
> [Вперед](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="3484d-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
