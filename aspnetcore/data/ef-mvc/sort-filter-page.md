---
title: Учебник. Использование ASP.NET MVC с EF Core. Добавление сортировки, фильтрации и разбиения на страницы
description: Из этого руководства вы узнаете, как добавить на страницу указателя учащихся сортировку, фильтрацию и разбиение на страницы. Здесь также описывается создание страницы с простой группировкой.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: ca4bce2274233f49e1c382dbd8d5c45615006b9c
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587649"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="44cb2-104">Учебник. Использование ASP.NET MVC с EF Core. Добавление сортировки, фильтрации и разбиения на страницы</span><span class="sxs-lookup"><span data-stu-id="44cb2-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="44cb2-105">В предыдущем руководстве был создан набор веб-страниц для основных операций CRUD для сущностей Student.</span><span class="sxs-lookup"><span data-stu-id="44cb2-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="44cb2-106">Из этого руководства вы узнаете, как добавить на страницу указателя учащихся сортировку, фильтрацию и разбиение на страницы.</span><span class="sxs-lookup"><span data-stu-id="44cb2-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="44cb2-107">Здесь также описывается создание страницы с простой группировкой.</span><span class="sxs-lookup"><span data-stu-id="44cb2-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="44cb2-108">На следующем рисунке изображен вид страницы после выполнения задания.</span><span class="sxs-lookup"><span data-stu-id="44cb2-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="44cb2-109">Заголовки столбцов являются ссылками, при нажатии на которые происходит сортировка по этому столбцу.</span><span class="sxs-lookup"><span data-stu-id="44cb2-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="44cb2-110">При повторном нажатии на заголовок происходит переключение между сортировкой по возрастанию и убыванию.</span><span class="sxs-lookup"><span data-stu-id="44cb2-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Страница указателя учащихся](sort-filter-page/_static/paging.png)

<span data-ttu-id="44cb2-112">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="44cb2-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="44cb2-113">Добавление ссылок для сортировки столбцов</span><span class="sxs-lookup"><span data-stu-id="44cb2-113">Add column sort links</span></span>
> * <span data-ttu-id="44cb2-114">Добавление поля поиска</span><span class="sxs-lookup"><span data-stu-id="44cb2-114">Add a Search box</span></span>
> * <span data-ttu-id="44cb2-115">Добавление разбиения по страницам в указатель учащихся</span><span class="sxs-lookup"><span data-stu-id="44cb2-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="44cb2-116">Добавление разбиения по страницам в метод Index</span><span class="sxs-lookup"><span data-stu-id="44cb2-116">Add paging to Index method</span></span>
> * <span data-ttu-id="44cb2-117">Добавление ссылок для разбиения по страницам</span><span class="sxs-lookup"><span data-stu-id="44cb2-117">Add paging links</span></span>
> * <span data-ttu-id="44cb2-118">Создание страницы сведений</span><span class="sxs-lookup"><span data-stu-id="44cb2-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="44cb2-119">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="44cb2-119">Prerequisites</span></span>

* [<span data-ttu-id="44cb2-120">Реализация функциональности CRUD</span><span class="sxs-lookup"><span data-stu-id="44cb2-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="44cb2-121">Добавление ссылок для сортировки столбцов</span><span class="sxs-lookup"><span data-stu-id="44cb2-121">Add column sort links</span></span>

<span data-ttu-id="44cb2-122">Для добавления сортировки на страницу указателя учащихся изменим метод `Index` контроллера Students и добавим код в представление указателя учащихся.</span><span class="sxs-lookup"><span data-stu-id="44cb2-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="44cb2-123">Добавление сортировки в метод Index</span><span class="sxs-lookup"><span data-stu-id="44cb2-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="44cb2-124">В файле *StudentsController.cs* замените код метода `Index` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="44cb2-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="44cb2-125">Этот код принимает параметр `sortOrder` из строки запроса в URL.</span><span class="sxs-lookup"><span data-stu-id="44cb2-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="44cb2-126">Значение строки запроса в ASP.NET Core MVC передается как параметр метода действия.</span><span class="sxs-lookup"><span data-stu-id="44cb2-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="44cb2-127">Имя параметра представляет собой строку, состоящую из "Name" или "Date" с возможным добавлением знака подчеркивания и строки "desc" для указания убывающего порядка сортировки.</span><span class="sxs-lookup"><span data-stu-id="44cb2-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="44cb2-128">По умолчанию используется порядок сортировки по возрастанию.</span><span class="sxs-lookup"><span data-stu-id="44cb2-128">The default sort order is ascending.</span></span>

<span data-ttu-id="44cb2-129">При первом запросе страницы Index строка запроса отсутствует.</span><span class="sxs-lookup"><span data-stu-id="44cb2-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="44cb2-130">Список студентов отсортирован по фамилиям по возрастанию, порядок сортировки по умолчанию задается в выражении `switch`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="44cb2-131">Когда пользователь щелкает гиперссылку заголовка столбца, в строку запроса подставляется соответствующее значение параметра `sortOrder`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="44cb2-132">Для формирования гиперссылок в заголовках столбцов в представлении используются два элемента `ViewData` (NameSortParm и DateSortParm) с соответствующими значениями строки запроса.</span><span class="sxs-lookup"><span data-stu-id="44cb2-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="44cb2-133">Это тернарные условные операторы.</span><span class="sxs-lookup"><span data-stu-id="44cb2-133">These are ternary statements.</span></span> <span data-ttu-id="44cb2-134">Первое выражение означает, что если параметр `sortOrder` пуст или равен null, то параметр NameSortParm должен принять значение "name_desc", в противном случае параметру NameSortParm присваивается пустая строка.</span><span class="sxs-lookup"><span data-stu-id="44cb2-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="44cb2-135">Следующие два оператора устанавливают гиперссылки в заголовках столбцов в представлении следующим образом:</span><span class="sxs-lookup"><span data-stu-id="44cb2-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="44cb2-136">Текущий порядок сортировки</span><span class="sxs-lookup"><span data-stu-id="44cb2-136">Current sort order</span></span>  | <span data-ttu-id="44cb2-137">Гиперссылка "Last Name" (Фамилия)</span><span class="sxs-lookup"><span data-stu-id="44cb2-137">Last Name Hyperlink</span></span> | <span data-ttu-id="44cb2-138">Гиперссылка "Date" (Дата)</span><span class="sxs-lookup"><span data-stu-id="44cb2-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="44cb2-139">"Last Name" (Фамилия) по возрастанию</span><span class="sxs-lookup"><span data-stu-id="44cb2-139">Last Name ascending</span></span>  | <span data-ttu-id="44cb2-140">descending</span><span class="sxs-lookup"><span data-stu-id="44cb2-140">descending</span></span>          | <span data-ttu-id="44cb2-141">ascending</span><span class="sxs-lookup"><span data-stu-id="44cb2-141">ascending</span></span>      |
| <span data-ttu-id="44cb2-142">"Last Name" (Фамилия) по убыванию</span><span class="sxs-lookup"><span data-stu-id="44cb2-142">Last Name descending</span></span> | <span data-ttu-id="44cb2-143">ascending</span><span class="sxs-lookup"><span data-stu-id="44cb2-143">ascending</span></span>           | <span data-ttu-id="44cb2-144">ascending</span><span class="sxs-lookup"><span data-stu-id="44cb2-144">ascending</span></span>      |
| <span data-ttu-id="44cb2-145">"Date" (Дата) по возрастанию</span><span class="sxs-lookup"><span data-stu-id="44cb2-145">Date ascending</span></span>       | <span data-ttu-id="44cb2-146">ascending</span><span class="sxs-lookup"><span data-stu-id="44cb2-146">ascending</span></span>           | <span data-ttu-id="44cb2-147">descending</span><span class="sxs-lookup"><span data-stu-id="44cb2-147">descending</span></span>     |
| <span data-ttu-id="44cb2-148">"Date" (Дата) по убыванию</span><span class="sxs-lookup"><span data-stu-id="44cb2-148">Date descending</span></span>      | <span data-ttu-id="44cb2-149">ascending</span><span class="sxs-lookup"><span data-stu-id="44cb2-149">ascending</span></span>           | <span data-ttu-id="44cb2-150">ascending</span><span class="sxs-lookup"><span data-stu-id="44cb2-150">ascending</span></span>      |

<span data-ttu-id="44cb2-151">Для указания столбца, по которому выполняется сортировка, этот метод использует LINQ to Entities.</span><span class="sxs-lookup"><span data-stu-id="44cb2-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="44cb2-152">Данный код создает переменную `IQueryable` перед оператором switch, изменяет ее значение внутри оператора switch и вызывает метод `ToListAsync` после `switch`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="44cb2-153">После создания и изменения переменных `IQueryable` запрос в базу данных не отправляется.</span><span class="sxs-lookup"><span data-stu-id="44cb2-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="44cb2-154">Запрос не выполнится, пока вы не преобразуете объект `IQueryable` в коллекцию, вызвав соответствующий метод, такой как `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="44cb2-155">Таким образом, этот код создает одиночный запрос, который не будет выполнен до выполнения выражения `return View`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="44cb2-156">Этот код можно расширить на случай большого числа столбцов.</span><span class="sxs-lookup"><span data-stu-id="44cb2-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="44cb2-157">[В последнем руководстве серии](advanced.md#dynamic-linq) вы найдете пример кода, который позволяет передавать имя столбца `OrderBy` в строковой переменной.</span><span class="sxs-lookup"><span data-stu-id="44cb2-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="44cb2-158">Добавление гиперссылок для заголовков столбцов в представлении индекса учащихся</span><span class="sxs-lookup"><span data-stu-id="44cb2-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="44cb2-159">Для добавления гиперссылок в заголовки столбцов замените код в файле *Views/Students/Index.cshtml* следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="44cb2-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="44cb2-160">Измененные строки выделены.</span><span class="sxs-lookup"><span data-stu-id="44cb2-160">The changed lines are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="44cb2-161">Для формирования гиперссылок с соответствующей строкой запроса этот код использует информацию из свойств `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="44cb2-162">Для проверки работы сортировки запустите приложение, выберите вкладку **Students** и нажимайте на заголовки столбцов **Last Name** и **Enrollment Date**.</span><span class="sxs-lookup"><span data-stu-id="44cb2-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Страница указателя учащихся с отсортированным по фамилиям списком](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="44cb2-164">Добавление поля поиска</span><span class="sxs-lookup"><span data-stu-id="44cb2-164">Add a Search box</span></span>

<span data-ttu-id="44cb2-165">Для добавления фильтра на страницу указателя учащихся необходимо добавить в представление текстовое поле и кнопку отправки и внести изменения в метод `Index`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="44cb2-166">Текстовое поле необходимо для ввода строки для поиска в полях имени и фамилии.</span><span class="sxs-lookup"><span data-stu-id="44cb2-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="44cb2-167">Добавление функций фильтрации в метод Index</span><span class="sxs-lookup"><span data-stu-id="44cb2-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="44cb2-168">В файле *StudentsController.cs* замените метод `Index` следующим кодом (изменения выделены).</span><span class="sxs-lookup"><span data-stu-id="44cb2-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="44cb2-169">Мы добавили в метод `Index` параметр `searchString`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="44cb2-170">Значение строки поиска получается из текстового поля, которое мы добавили в представление Index.</span><span class="sxs-lookup"><span data-stu-id="44cb2-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="44cb2-171">Мы также добавили в запрос LINQ предложение where, которое отбирает только студентов, чье имя или фамилия содержат строку поиска.</span><span class="sxs-lookup"><span data-stu-id="44cb2-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="44cb2-172">Выражение с предложением where выполняется только в том случае, если задано значение для поиска.</span><span class="sxs-lookup"><span data-stu-id="44cb2-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="44cb2-173">В этом коде мы вызываем метод `Where` объекта `IQueryable`, при этом фильтр будет обработан на сервере.</span><span class="sxs-lookup"><span data-stu-id="44cb2-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="44cb2-174">В некоторых случаях может потребоваться вызов метода `Where` как метода расширения коллекции в памяти.</span><span class="sxs-lookup"><span data-stu-id="44cb2-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="44cb2-175">(Предположим, например, что вы изменили ссылку на `_context.Students` таким образом, что вместо объекта EF `DbSet` она ссылается на метод репозитория, который возвращает коллекцию `IEnumerable`.) Обычно результат остается прежним, но в некоторых случаях он может отличаться.</span><span class="sxs-lookup"><span data-stu-id="44cb2-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="44cb2-176">Например, в .NET Framework метод `Contains` по умолчанию выполняет сравнение с учетом регистра, а в SQL Server это определяется параметром сортировки конкретного экземпляра SQL сервера.</span><span class="sxs-lookup"><span data-stu-id="44cb2-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="44cb2-177">По умолчанию параметр установлен на сравнение без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="44cb2-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="44cb2-178">Можно вызвать метод `ToUpper`, чтобы явно настроить тест не учитывать регистр:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())* .</span><span class="sxs-lookup"><span data-stu-id="44cb2-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="44cb2-179">Это гарантирует, что поведение программы не изменится, если вы измените код на использование репозитория, который возвращает `IEnumerable`, а не объект `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="44cb2-180">(При вызове метода `Contains` коллекции `IEnumerable` выполняется реализация .NET Framework; при вызове этого же метода у объекта `IQueryable` выполняется реализация поставщика базы данных.) Однако такое решение снижает производительность.</span><span class="sxs-lookup"><span data-stu-id="44cb2-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="44cb2-181">Метод `ToUpper` добавляет функцию в предложение WHERE TSQL-выражения SELECT.</span><span class="sxs-lookup"><span data-stu-id="44cb2-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="44cb2-182">Это не позволяет оптимизатору использовать индекс.</span><span class="sxs-lookup"><span data-stu-id="44cb2-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="44cb2-183">Учитывая, что SQL обычно настраивается на то, чтобы не учитывать регистр, рекомендуется не использовать код `ToUpper` до миграции на хранилище данных, учитывающее регистр.</span><span class="sxs-lookup"><span data-stu-id="44cb2-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="44cb2-184">Добавление поля поиска на страницу индекса учащихся</span><span class="sxs-lookup"><span data-stu-id="44cb2-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="44cb2-185">В файле *Views/Student/Index.cshtml* добавьте выделенный код непосредственно перед открывающим тегом table для создания заголовка, текстового поля и кнопки **Search**.</span><span class="sxs-lookup"><span data-stu-id="44cb2-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="44cb2-186">Для добавления кнопки и поля поиска этот код использует [вспомогательную функцию тега](xref:mvc/views/tag-helpers/intro) `<form>`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="44cb2-187">По умолчанию вспомогательная функция тега `<form>` отправляет данные формы с помощью запроса POST, это означает, что параметры передаются в теле сообщения HTTP, а не в URL-адресе в виде строки запросов.</span><span class="sxs-lookup"><span data-stu-id="44cb2-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="44cb2-188">При указании метода HTTP GET данные формы передаются в URL-адресе в виде строк запроса, что позволяет добавлять URL-адреса в закладки.</span><span class="sxs-lookup"><span data-stu-id="44cb2-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="44cb2-189">Руководства консорциума W3C рекомендуют использовать метод GET, когда действие не приводит к обновлению.</span><span class="sxs-lookup"><span data-stu-id="44cb2-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="44cb2-190">Для проверки работы фильтра запустите приложение, выберите вкладку **Students**, введите строку поиска и нажмите Search.</span><span class="sxs-lookup"><span data-stu-id="44cb2-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Страница указателя учащихся с фильтрацией](sort-filter-page/_static/filtering.png)

<span data-ttu-id="44cb2-192">Обратите внимание, что URL-адрес содержит строку поиска.</span><span class="sxs-lookup"><span data-stu-id="44cb2-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="44cb2-193">Если вы добавите эту страницу в закладки, то при открытии закладки будет открываться уже отфильтрованный список.</span><span class="sxs-lookup"><span data-stu-id="44cb2-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="44cb2-194">Формирование строки запроса обеспечивает добавление `method="get"` в тег `form`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="44cb2-195">На данном этапе, если нажать ссылку сортировки в заголовке столбца, то значение фильтра, которое мы ввели в поле **Search**, будет потеряно.</span><span class="sxs-lookup"><span data-stu-id="44cb2-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="44cb2-196">Мы исправим это в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="44cb2-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="44cb2-197">Добавление разбиения по страницам в указатель учащихся</span><span class="sxs-lookup"><span data-stu-id="44cb2-197">Add paging to Students Index</span></span>

<span data-ttu-id="44cb2-198">Чтобы добавить на страницу указателя учащихся разбиение на страницы, следует создать класс `PaginatedList`, который использует операторы `Skip` и `Take` для фильтрации данных на сервере вместо того, чтобы каждый раз получать все строки таблицы.</span><span class="sxs-lookup"><span data-stu-id="44cb2-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="44cb2-199">Затем мы внесем дополнительные изменения в метод `Index` и добавим в представление `Index` кнопки перелистывания страниц.</span><span class="sxs-lookup"><span data-stu-id="44cb2-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="44cb2-200">На следующем рисунке показаны кнопки перелистывания.</span><span class="sxs-lookup"><span data-stu-id="44cb2-200">The following illustration shows the paging buttons.</span></span>

![Страница указателя учащихся со ссылками для перелистывания](sort-filter-page/_static/paging.png)

<span data-ttu-id="44cb2-202">В папке проекта создайте файл `PaginatedList.cs` и замените код шаблона на следующий код.</span><span class="sxs-lookup"><span data-stu-id="44cb2-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="44cb2-203">В этом коде метод `CreateAsync` принимает размер и номер страницы и вызывает соответствующие методы `Skip` и `Take` объекта `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="44cb2-204">Метод `ToListAsync` объекта `IQueryable` при вызове возвратит список, содержащий только запрошенную страницу.</span><span class="sxs-lookup"><span data-stu-id="44cb2-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="44cb2-205">Для включения и отключения кнопок перелистывания страниц **Previous** и **Next** можно использовать свойства `HasPreviousPage` и `HasNextPage`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="44cb2-206">Для создания объекта `PaginatedList<T>` вместо конструктора используется метод `CreateAsync`, поскольку конструкторы не могут выполнять асинхронный код.</span><span class="sxs-lookup"><span data-stu-id="44cb2-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="44cb2-207">Добавление разбиения по страницам в метод Index</span><span class="sxs-lookup"><span data-stu-id="44cb2-207">Add paging to Index method</span></span>

<span data-ttu-id="44cb2-208">В файле *StudentsController.cs* замените код метода `Index` следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="44cb2-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="44cb2-209">Этот код добавляет к сигнатуре метода параметры с номером страницы, текущим порядком сортировки и текущим фильтром.</span><span class="sxs-lookup"><span data-stu-id="44cb2-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="44cb2-210">При первом отображении страницы или если пользователь еще не нажимал на ссылки сортировки и перелистывания, все параметры будут иметь значение null.</span><span class="sxs-lookup"><span data-stu-id="44cb2-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="44cb2-211">При нажатии на кнопку перелистывания переменная page будет содержать номер страницы для отображения.</span><span class="sxs-lookup"><span data-stu-id="44cb2-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="44cb2-212">Элемент `ViewData` с именем CurrentSort передает в представление порядок сортировки, поскольку он должен быть включен в ссылки перелистывания, чтобы сохранить порядок сортировки при переходе по страницам.</span><span class="sxs-lookup"><span data-stu-id="44cb2-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="44cb2-213">Элемент `ViewData` с именем CurrentFilter передает в представление текущую строку фильтра.</span><span class="sxs-lookup"><span data-stu-id="44cb2-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="44cb2-214">Это значение необходимо включить в ссылки для перелистывания, чтобы при смене страницы сохранить настройки фильтра, кроме того, необходимо восстановить значение фильтра в текстовом поле после обновления страницы.</span><span class="sxs-lookup"><span data-stu-id="44cb2-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="44cb2-215">Если строка поиска изменяется во время перелистывания, то номер страницы должен быть сброшен на 1, так как с новым фильтром изменится состав отображаемых данных.</span><span class="sxs-lookup"><span data-stu-id="44cb2-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="44cb2-216">Изменение строки поиска происходит при вводе в текстовое поле значения и нажатии на кнопку отправки.</span><span class="sxs-lookup"><span data-stu-id="44cb2-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="44cb2-217">В этом случае значение параметра `searchString` не null.</span><span class="sxs-lookup"><span data-stu-id="44cb2-217">In that case, the `searchString` parameter isn't null.</span></span>

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

<span data-ttu-id="44cb2-218">В конце метода `Index` метод `PaginatedList.CreateAsync` преобразует результат запроса студентов в страницу коллекции, поддерживающую разбиение на страницы.</span><span class="sxs-lookup"><span data-stu-id="44cb2-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="44cb2-219">Это страница со студентами затем передается в представление.</span><span class="sxs-lookup"><span data-stu-id="44cb2-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="44cb2-220">Метод `PaginatedList.CreateAsync` принимает номер страницы.</span><span class="sxs-lookup"><span data-stu-id="44cb2-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="44cb2-221">Два вопросительных знака являются оператором объединения с null.</span><span class="sxs-lookup"><span data-stu-id="44cb2-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="44cb2-222">Этот оператор определяет значение по умолчанию для значения null; выражение `(pageNumber ?? 1)` возвращает значение переменной `pageNumber`, если она имеет значение, и возвращает 1, если переменная `pageNumber` имеет значение null.</span><span class="sxs-lookup"><span data-stu-id="44cb2-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="44cb2-223">Добавление ссылок для разбиения по страницам</span><span class="sxs-lookup"><span data-stu-id="44cb2-223">Add paging links</span></span>

<span data-ttu-id="44cb2-224">Замените код в файле *Views/Students/Index.cshtml* следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="44cb2-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="44cb2-225">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="44cb2-225">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="44cb2-226">Оператор `@model` в начале страницы указывает на то, что теперь представление принимает объект `PaginatedList<T>`, а не объект `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="44cb2-227">Ссылки в заголовках столбцов передают в контроллер при помощи строки запроса текущее значение строки поиска, чтобы пользователь мог сортировать отфильтрованные данные:</span><span class="sxs-lookup"><span data-stu-id="44cb2-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="44cb2-228">Кнопки перелистывания отображаются вспомогательными функциями тегов:</span><span class="sxs-lookup"><span data-stu-id="44cb2-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="44cb2-229">Запустите приложение и перейдите на страницу Students.</span><span class="sxs-lookup"><span data-stu-id="44cb2-229">Run the app and go to the Students page.</span></span>

![Страница указателя учащихся со ссылками для перелистывания](sort-filter-page/_static/paging.png)

<span data-ttu-id="44cb2-231">Чтобы убедиться, что постраничный просмотр работает, нажимайте кнопки перелистывания при различном порядке сортировки.</span><span class="sxs-lookup"><span data-stu-id="44cb2-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="44cb2-232">Затем введите строку поиска и повторите перелистывание, чтобы убедиться, что разбиение на страницы работает корректно вместе с сортировкой и фильтрацией.</span><span class="sxs-lookup"><span data-stu-id="44cb2-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="44cb2-233">Создание страницы сведений</span><span class="sxs-lookup"><span data-stu-id="44cb2-233">Create an About page</span></span>

<span data-ttu-id="44cb2-234">На странице **About** веб-сайта "Университет Contoso" будет отображаться количество зачисленных студентов по дням.</span><span class="sxs-lookup"><span data-stu-id="44cb2-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="44cb2-235">Для этого понадобится группировка и выполнение простых расчетов в группах.</span><span class="sxs-lookup"><span data-stu-id="44cb2-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="44cb2-236">Для выполнения этой задачи нам потребуется следующее:</span><span class="sxs-lookup"><span data-stu-id="44cb2-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="44cb2-237">Создать класс модели представления для данных, которые необходимо передать в представление.</span><span class="sxs-lookup"><span data-stu-id="44cb2-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="44cb2-238">Создать метод About в контроллере Home.</span><span class="sxs-lookup"><span data-stu-id="44cb2-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="44cb2-239">Создать представление About.</span><span class="sxs-lookup"><span data-stu-id="44cb2-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="44cb2-240">Создание модели представления</span><span class="sxs-lookup"><span data-stu-id="44cb2-240">Create the view model</span></span>

<span data-ttu-id="44cb2-241">Создайте папку *SchoolViewModels* в папке *Models*.</span><span class="sxs-lookup"><span data-stu-id="44cb2-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="44cb2-242">В новой папке добавьте файл класса *EnrollmentDateGroup.cs* и замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="44cb2-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="44cb2-243">Изменение контроллера Home</span><span class="sxs-lookup"><span data-stu-id="44cb2-243">Modify the Home Controller</span></span>

<span data-ttu-id="44cb2-244">Добавьте следующие директивы using в начало файла *HomeController.cs*:</span><span class="sxs-lookup"><span data-stu-id="44cb2-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="44cb2-245">Добавьте переменную класса для контекста базы данных сразу же после открывающей фигурной скобки описания класса и получите экземпляр контекста из ASP.NET Core DI:</span><span class="sxs-lookup"><span data-stu-id="44cb2-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="44cb2-246">Добавьте метод `About` со следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="44cb2-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="44cb2-247">Запрос LINQ группирует записи из таблицы студентов по дате зачисления, вычисляет число записей в каждой группе и сохраняет результаты в коллекцию объектов моделей представления `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="44cb2-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="44cb2-248">Создание представления About</span><span class="sxs-lookup"><span data-stu-id="44cb2-248">Create the About View</span></span>

<span data-ttu-id="44cb2-249">Добавьте файл *Views/Home/About.cshtml* со следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="44cb2-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="44cb2-250">Запустите приложение и перейдите на страницу About.</span><span class="sxs-lookup"><span data-stu-id="44cb2-250">Run the app and go to the About page.</span></span> <span data-ttu-id="44cb2-251">Количество зачисленных студентов по дням отображается в таблице.</span><span class="sxs-lookup"><span data-stu-id="44cb2-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="44cb2-252">Получите код</span><span class="sxs-lookup"><span data-stu-id="44cb2-252">Get the code</span></span>

[<span data-ttu-id="44cb2-253">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="44cb2-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="44cb2-254">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="44cb2-254">Next steps</span></span>

<span data-ttu-id="44cb2-255">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="44cb2-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="44cb2-256">Добавление ссылок для сортировки столбцов</span><span class="sxs-lookup"><span data-stu-id="44cb2-256">Added column sort links</span></span>
> * <span data-ttu-id="44cb2-257">Добавление поля поиска</span><span class="sxs-lookup"><span data-stu-id="44cb2-257">Added a Search box</span></span>
> * <span data-ttu-id="44cb2-258">Добавление разбиения по страницам в указатель учащихся</span><span class="sxs-lookup"><span data-stu-id="44cb2-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="44cb2-259">Добавление разбиения по страницам в метод Index</span><span class="sxs-lookup"><span data-stu-id="44cb2-259">Added paging to Index method</span></span>
> * <span data-ttu-id="44cb2-260">Добавление ссылок для разбиения по страницам</span><span class="sxs-lookup"><span data-stu-id="44cb2-260">Added paging links</span></span>
> * <span data-ttu-id="44cb2-261">Создание страницы сведений</span><span class="sxs-lookup"><span data-stu-id="44cb2-261">Created an About page</span></span>

<span data-ttu-id="44cb2-262">В следующем учебнике описано, как с помощью миграций обрабатывать изменения в модели данных.</span><span class="sxs-lookup"><span data-stu-id="44cb2-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="44cb2-263">Далее: Обработка изменений модели данных</span><span class="sxs-lookup"><span data-stu-id="44cb2-263">Next: Handle data model changes</span></span>](migrations.md)
