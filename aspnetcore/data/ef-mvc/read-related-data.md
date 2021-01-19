---
title: Учебник. Использование ASP.NET MVC с EF Core. Чтение связанных данных
description: Из этого руководства вы узнаете, как читать и отображать связанные данные — данные, которые Entity Framework загружает в свойства навигации.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: b1adca435b48db22a176d33a216c07d6647a6695
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058315"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="cba84-103">Учебник. Использование ASP.NET MVC с EF Core. Чтение связанных данных</span><span class="sxs-lookup"><span data-stu-id="cba84-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="cba84-104">В предыдущем руководстве мы завершили разработку модели данных School.</span><span class="sxs-lookup"><span data-stu-id="cba84-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="cba84-105">Из этого руководства вы узнаете, как читать и отображать связанные данные — данные, которые Entity Framework загружает в свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="cba84-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="cba84-106">На следующих рисунках изображены страницы, с которыми вы будете работать.</span><span class="sxs-lookup"><span data-stu-id="cba84-106">The following illustrations show the pages that you'll work with.</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index.png)

![Страница индекса преподавателей](read-related-data/_static/instructors-index.png)

<span data-ttu-id="cba84-109">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="cba84-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cba84-110">Загрузка связанных данных</span><span class="sxs-lookup"><span data-stu-id="cba84-110">Learn how to load related data</span></span>
> * <span data-ttu-id="cba84-111">Создание страницы курсов</span><span class="sxs-lookup"><span data-stu-id="cba84-111">Create a Courses page</span></span>
> * <span data-ttu-id="cba84-112">Создание страницы преподавателей</span><span class="sxs-lookup"><span data-stu-id="cba84-112">Create an Instructors page</span></span>
> * <span data-ttu-id="cba84-113">Дополнительные сведения о явной загрузке</span><span class="sxs-lookup"><span data-stu-id="cba84-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cba84-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="cba84-114">Prerequisites</span></span>

* [<span data-ttu-id="cba84-115">Создание сложной модели данных</span><span class="sxs-lookup"><span data-stu-id="cba84-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="cba84-116">Загрузка связанных данных</span><span class="sxs-lookup"><span data-stu-id="cba84-116">Learn how to load related data</span></span>

<span data-ttu-id="cba84-117">Существует несколько способов, которыми программное обеспечение объектно-реляционного сопоставления (ORM), такое как Entity Framework, может загружать связанные данные в свойства навигации сущности:</span><span class="sxs-lookup"><span data-stu-id="cba84-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="cba84-118">Безотложная загрузка.</span><span class="sxs-lookup"><span data-stu-id="cba84-118">Eager loading.</span></span> <span data-ttu-id="cba84-119">При чтении сущности связанные данные извлекаются вместе с ней.</span><span class="sxs-lookup"><span data-stu-id="cba84-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="cba84-120">Обычно такая загрузка представляет собой одиночный запрос с соединением, который получает все необходимые данные.</span><span class="sxs-lookup"><span data-stu-id="cba84-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="cba84-121">Настроить Entity Framework Core на использование безотложной загрузки можно при помощи методов `Include` и `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="cba84-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Пример безотложной загрузки](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="cba84-123">Вы можете получить часть данных в отдельных запросах, и EF "исправит" свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="cba84-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="cba84-124">То есть EF автоматически добавляет раздельно извлеченные сущности к соответствующим свойствам навигации ранее извлеченных объектов.</span><span class="sxs-lookup"><span data-stu-id="cba84-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="cba84-125">Для запроса, получающего связанные данные, можно использовать метод `Load` вместо метода, который возвращает список или объект, такого как `ToList` или `Single`.</span><span class="sxs-lookup"><span data-stu-id="cba84-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Пример отдельных запросов](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="cba84-127">Явная загрузка.</span><span class="sxs-lookup"><span data-stu-id="cba84-127">Explicit loading.</span></span> <span data-ttu-id="cba84-128">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="cba84-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="cba84-129">Если требуется получение связанных данных, то пишется дополнительный код.</span><span class="sxs-lookup"><span data-stu-id="cba84-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="cba84-130">Как и в случае безотложной загрузки с отдельными запросами, явная загрузка представляет собой несколько запросов к базе данных.</span><span class="sxs-lookup"><span data-stu-id="cba84-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="cba84-131">Отличие заключается в том, что при явной загрузке в коде указывается, какие свойства навигации будут загружены.</span><span class="sxs-lookup"><span data-stu-id="cba84-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="cba84-132">В Entity Framework Core 1.1 для выполнения явной загрузки можно использовать метод `Load`.</span><span class="sxs-lookup"><span data-stu-id="cba84-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="cba84-133">Пример:</span><span class="sxs-lookup"><span data-stu-id="cba84-133">For example:</span></span>

  ![Пример явной загрузки](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="cba84-135">Отложенная загрузка.</span><span class="sxs-lookup"><span data-stu-id="cba84-135">Lazy loading.</span></span> <span data-ttu-id="cba84-136">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="cba84-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="cba84-137">Однако при первой попытке доступа к свойству навигации необходимые для этого свойства навигации данные извлекаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="cba84-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="cba84-138">Запрос к базе данных отправляется каждый раз, когда вы в первый раз пытаетесь получить данные из свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="cba84-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="cba84-139">Entity Framework Core 1.0 не поддерживает отложенную загрузку.</span><span class="sxs-lookup"><span data-stu-id="cba84-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="cba84-140">Особенности производительности</span><span class="sxs-lookup"><span data-stu-id="cba84-140">Performance considerations</span></span>

<span data-ttu-id="cba84-141">Если известно, что связанные данные потребуются для каждой полученной сущности, то безотложная загрузка обычно обеспечивает наилучшую производительность, поскольку одиночный запрос к базе данных обычно эффективнее нескольких отдельных запросов для каждой полученной сущности.</span><span class="sxs-lookup"><span data-stu-id="cba84-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="cba84-142">Пусть, например, на каждом факультете есть десять связанных курсов.</span><span class="sxs-lookup"><span data-stu-id="cba84-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="cba84-143">Безотложная загрузка всех связанных данных приведет к одиночному запросу (с соединением) и одним циклом приема-передачи данных из базы.</span><span class="sxs-lookup"><span data-stu-id="cba84-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="cba84-144">Отдельные запросы по курсам для каждого факультета приведут к одиннадцати циклам приема-передачи данных из базы.</span><span class="sxs-lookup"><span data-stu-id="cba84-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="cba84-145">При высокой задержке дополнительные циклы приема-передачи данных особенно сильно влияют на производительность.</span><span class="sxs-lookup"><span data-stu-id="cba84-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="cba84-146">С другой стороны, в некоторых случаях отдельные запросы более эффективны.</span><span class="sxs-lookup"><span data-stu-id="cba84-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="cba84-147">Безотложная загрузка всех связанных данных в одном запросе может привести к формированию очень сложного соединения, которое SQL сервер не сможет эффективно обработать.</span><span class="sxs-lookup"><span data-stu-id="cba84-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="cba84-148">Либо, если необходимо получить свойства навигации сущности только для подмножества обрабатываемого набора сущностей, отдельные запросы могут показать большую производительность, поскольку при безотложной загрузке всех данных будет получено больше данных, чем вам необходимо.</span><span class="sxs-lookup"><span data-stu-id="cba84-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="cba84-149">Если важна производительность, то для выбора наилучшего решения рекомендуется протестировать производительность для обоих случаев.</span><span class="sxs-lookup"><span data-stu-id="cba84-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="cba84-150">Создание страницы курсов</span><span class="sxs-lookup"><span data-stu-id="cba84-150">Create a Courses page</span></span>

<span data-ttu-id="cba84-151">Сущность Course включает свойство навигации, которое содержит сущность Department факультета, к которому привязан курс.</span><span class="sxs-lookup"><span data-stu-id="cba84-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="cba84-152">Чтобы отобразить в списке курсов название связанного факультета, необходимо получить свойство Name из сущности Department, находящейся в свойстве навигации `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="cba84-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="cba84-153">Создайте для типа сущности Course контроллер с именем CoursesController с теми же параметрами шаблона **Контроллер MVC с представлениями, использующий Entity Framework**, которые мы ранее задали для контроллера Students, как это показано на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="cba84-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Добавление контроллера Courses](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="cba84-155">Откройте файл *CoursesController.cs* и изучите метод `Index`.</span><span class="sxs-lookup"><span data-stu-id="cba84-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="cba84-156">В автоматически сформированном шаблоне установлена безотложная загрузка свойства навигации `Department` при помощи метода `Include`.</span><span class="sxs-lookup"><span data-stu-id="cba84-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="cba84-157">Замените метод `Index` следующим кодом, который использует более подходящее имя для `IQueryable`, возвращающего сущности Course (`courses` вместо `schoolContext`):</span><span class="sxs-lookup"><span data-stu-id="cba84-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="cba84-158">Откройте файл *Views/Courses/Index.cshtml* и замените код шаблона следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="cba84-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="cba84-159">Изменения выделены:</span><span class="sxs-lookup"><span data-stu-id="cba84-159">The changes are highlighted:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="cba84-160">Мы внесли следующие изменения в код шаблона:</span><span class="sxs-lookup"><span data-stu-id="cba84-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="cba84-161">Изменен заголовок с "Index" (Индекс) на "Courses" (Курсы).</span><span class="sxs-lookup"><span data-stu-id="cba84-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="cba84-162">Добавлен столбец **Number** (Номер), отображающий значение свойства `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="cba84-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="cba84-163">По умолчанию в шаблоне отсутствуют первичные ключи, поскольку для конечных пользователей они не имеют смысла.</span><span class="sxs-lookup"><span data-stu-id="cba84-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="cba84-164">Однако в нашем случае первичный ключ имеет смысл, и мы хотим его отобразить.</span><span class="sxs-lookup"><span data-stu-id="cba84-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="cba84-165">Изменен столбец **Department** (Кафедра) для отображения названия кафедры.</span><span class="sxs-lookup"><span data-stu-id="cba84-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="cba84-166">Код отображает свойство `Name` сущности Department, которая загружена в свойство навигации `Department`:</span><span class="sxs-lookup"><span data-stu-id="cba84-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="cba84-167">Для просмотра списка с названиями кафедр запустите приложение и выберите вкладку **Courses** (Курсы).</span><span class="sxs-lookup"><span data-stu-id="cba84-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="cba84-169">Создание страницы преподавателей</span><span class="sxs-lookup"><span data-stu-id="cba84-169">Create an Instructors page</span></span>

<span data-ttu-id="cba84-170">В этом разделе мы создадим контроллер и представление для сущности Instructor, чтобы отобразить страницу Instructors:</span><span class="sxs-lookup"><span data-stu-id="cba84-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Страница индекса преподавателей](read-related-data/_static/instructors-index.png)

<span data-ttu-id="cba84-172">Эта страница считывает и отображает связанные данные следующим образом:</span><span class="sxs-lookup"><span data-stu-id="cba84-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="cba84-173">Список преподавателей отображает связанные данные сущности OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="cba84-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="cba84-174">Сущности Instructor и OfficeAssignment связаны отношением один к нулю или к одному.</span><span class="sxs-lookup"><span data-stu-id="cba84-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="cba84-175">Для сущностей OfficeAssignment установлена безотложная загрузка.</span><span class="sxs-lookup"><span data-stu-id="cba84-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="cba84-176">Как упоминалось ранее, безотложная загрузка обычно эффективнее при получении связанных данных для всех строк главной таблицы.</span><span class="sxs-lookup"><span data-stu-id="cba84-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="cba84-177">В нашем случае мы хотим отобразить принадлежность к кабинету для каждого преподавателя.</span><span class="sxs-lookup"><span data-stu-id="cba84-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="cba84-178">Когда пользователь выбирает преподавателя, отображаются связанные сущности Course.</span><span class="sxs-lookup"><span data-stu-id="cba84-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="cba84-179">Сущности Instructor и Course находятся в отношении многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="cba84-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="cba84-180">Как мы видим, безотложная загрузка также установлена для сущностей Course и связанных с ними сущностями Department.</span><span class="sxs-lookup"><span data-stu-id="cba84-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="cba84-181">В этом случае отдельные запросы могут оказаться эффективнее, поскольку нам требуются курсы только для выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="cba84-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="cba84-182">Этот пример, однако, показывает, как использовать безотложную загрузку для свойств навигации сущностей, которые сами находятся в свойствах навигации.</span><span class="sxs-lookup"><span data-stu-id="cba84-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="cba84-183">Когда пользователь выбирает курс, отображаются связанные данные из набора сущностей Enrollments.</span><span class="sxs-lookup"><span data-stu-id="cba84-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="cba84-184">Сущности Course и Enrollment находятся в отношении один ко многим.</span><span class="sxs-lookup"><span data-stu-id="cba84-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="cba84-185">Мы используем отдельные запросы для сущностей Enrollment и связанных с ними сущностями Student.</span><span class="sxs-lookup"><span data-stu-id="cba84-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="cba84-186">Создание модели для представления индекса преподавателей</span><span class="sxs-lookup"><span data-stu-id="cba84-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="cba84-187">На странице "Instructors" (Преподаватели) отображаются данные из трех различных таблиц.</span><span class="sxs-lookup"><span data-stu-id="cba84-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="cba84-188">Таким образом, мы создаем модель представления, которая включает три свойства, каждое из которых содержит данные из одной таблицы.</span><span class="sxs-lookup"><span data-stu-id="cba84-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="cba84-189">Создайте в папке *SchoolViewModels* файл *InstructorIndexData.cs* и замените существующий код следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="cba84-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="cba84-190">Создание контроллера и представлений Instructor</span><span class="sxs-lookup"><span data-stu-id="cba84-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="cba84-191">Создайте контроллер Instructors с действиями чтения/записи Entity Framework, как показано на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="cba84-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Добавление контроллера Instructors](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="cba84-193">Откройте файл *InstructorsController.cs* и добавьте директиву using для пространства имен ViewModels:</span><span class="sxs-lookup"><span data-stu-id="cba84-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="cba84-194">Замените код Index следующим кодом для выполнения безотложной загрузки связанных данных и размещения их в модели представления.</span><span class="sxs-lookup"><span data-stu-id="cba84-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="cba84-195">Метод принимает необязательные данные маршрута (`id`) и строку запроса (`courseID`), которые содержат значения идентификатора выбранного преподавателя и выбранного курса.</span><span class="sxs-lookup"><span data-stu-id="cba84-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="cba84-196">Параметры передаются гиперссылками **Select** на странице.</span><span class="sxs-lookup"><span data-stu-id="cba84-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="cba84-197">Код начинается с создания экземпляра модели представления и помещения его в список преподавателей.</span><span class="sxs-lookup"><span data-stu-id="cba84-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="cba84-198">В коде задается безотложная загрузка для свойств навигации `Instructor.OfficeAssignment` и `Instructor.CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="cba84-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="cba84-199">Вместе со свойством `CourseAssignments` загружается свойство `Course`, с которым загружаются свойства `Enrollments` и `Department`, а с каждой сущностью `Enrollment` загружается свойство `Student`.</span><span class="sxs-lookup"><span data-stu-id="cba84-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="cba84-200">Так как для представления требуется сущность OfficeAssignment, значительно эффективнее извлекать ее в том же запросе.</span><span class="sxs-lookup"><span data-stu-id="cba84-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="cba84-201">Получение сущностей Course необходимо при выборе преподавателя на веб-странице, таким образом одиночный запрос окажется предпочтительнее нескольких запросов, только если страница отображается с курсами чаще, чем без них.</span><span class="sxs-lookup"><span data-stu-id="cba84-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="cba84-202">В коде повторяются `CourseAssignments` и `Course`, так как требуется получить два свойства из `Course`.</span><span class="sxs-lookup"><span data-stu-id="cba84-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="cba84-203">Первая строка `ThenInclude` вызывает получение `CourseAssignment.Course`, `Course.Enrollments` и `Enrollment.Student`.</span><span class="sxs-lookup"><span data-stu-id="cba84-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="cba84-204">В этой точке кода вызов метода `ThenInclude` получал бы свойства навигации `Student`, которые нам требуются.</span><span class="sxs-lookup"><span data-stu-id="cba84-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="cba84-205">Но вызов `Include` начнет заново получать свойства `Instructor`, поэтому нам придется еще раз выполнить последовательность команд, указав в этот раз `Course.Department` вместо `Course.Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="cba84-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="cba84-206">Следующий код выполняется при выборе преподавателя.</span><span class="sxs-lookup"><span data-stu-id="cba84-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="cba84-207">Выбранный преподаватель извлекается из списка преподавателей в модели представления.</span><span class="sxs-lookup"><span data-stu-id="cba84-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="cba84-208">Затем из свойства навигации `CourseAssignments` этого преподавателя получается свойство модели представления `Courses` вместе с сущностями Course.</span><span class="sxs-lookup"><span data-stu-id="cba84-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="cba84-209">Метод `Where` возвращает коллекцию, но с учетом переданных в метод условий в данном случае возвращается только одна сущность Instructor.</span><span class="sxs-lookup"><span data-stu-id="cba84-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="cba84-210">Метод `Single` преобразует коллекцию в отдельную сущность Instructor, что позволяет получить доступ к ее свойству `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="cba84-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="cba84-211">Свойство `CourseAssignments` содержит сущности `CourseAssignment`, из которых нам нужны только связанные сущности `Course`.</span><span class="sxs-lookup"><span data-stu-id="cba84-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="cba84-212">Использовать метод коллекции `Single` можно, если известно, что коллекция содержит только один элемент.</span><span class="sxs-lookup"><span data-stu-id="cba84-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="cba84-213">Метод Single вызывает исключение, если передаваемая ему коллекция пустая или содержит больше одного элемента.</span><span class="sxs-lookup"><span data-stu-id="cba84-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="cba84-214">Альтернативным вариантом является метод `SingleOrDefault`, который возвращает значение по умолчанию (в данном случае null), если коллекция пуста.</span><span class="sxs-lookup"><span data-stu-id="cba84-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="cba84-215">Однако в этом случае это все равно приведет к исключению (из-за попытки найти свойство `Courses` у указателя на null), и из сообщения об исключении нелегко будет понять причину проблемы.</span><span class="sxs-lookup"><span data-stu-id="cba84-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="cba84-216">При вызове метода `Single` вы можете также передать условие Where вместо отдельного вызова метода `Where`:</span><span class="sxs-lookup"><span data-stu-id="cba84-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="cba84-217">вместо следующего кода:</span><span class="sxs-lookup"><span data-stu-id="cba84-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="cba84-218">Далее, если был выбран курс, то он получается из списка курсов модели представления.</span><span class="sxs-lookup"><span data-stu-id="cba84-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="cba84-219">Затем из свойства навигации `Enrollments` этого курса получается свойство модели представления `Enrollments` вместе с сущностями Enrollment.</span><span class="sxs-lookup"><span data-stu-id="cba84-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="cba84-220">Изменение представления Instructor Index</span><span class="sxs-lookup"><span data-stu-id="cba84-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="cba84-221">В файле *Views/Instructors/Index.cshtml* замените код шаблона следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="cba84-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="cba84-222">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="cba84-222">The changes are highlighted.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-cshtml[](intro/samples/5cu-snap/Views/Instructors/Index.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

<span data-ttu-id="cba84-223">Мы внесли следующие изменения в существующий код:</span><span class="sxs-lookup"><span data-stu-id="cba84-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="cba84-224">Изменили класс модели на `InstructorIndexData`.</span><span class="sxs-lookup"><span data-stu-id="cba84-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="cba84-225">Изменили заголовок страницы с **Index** на **Instructors**.</span><span class="sxs-lookup"><span data-stu-id="cba84-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="cba84-226">Добавили столбец **Office**, отображающий `item.OfficeAssignment.Location` только тогда, когда `item.OfficeAssignment` не равно null.</span><span class="sxs-lookup"><span data-stu-id="cba84-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="cba84-227">(Так как здесь отношение один к нулю или к одному, то связанной сущности OfficeAssignment может не существовать.)</span><span class="sxs-lookup"><span data-stu-id="cba84-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```cshtml
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="cba84-228">Добавили столбец **Courses**, отображающий курсы, которые ведет конкретный преподаватель.</span><span class="sxs-lookup"><span data-stu-id="cba84-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="cba84-229">Дополнительные сведения см. в разделе [Явный перенос строки](xref:mvc/views/razor#explicit-line-transition) статьи по синтаксису Razor.</span><span class="sxs-lookup"><span data-stu-id="cba84-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="cba84-230">Добавлен код, который по условию добавляет класс CSS Bootstrap к элементу `tr` выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="cba84-230">Added code that conditionally adds a Bootstrap CSS class to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="cba84-231">Этот класс задает цвет фона для выделенной строки.</span><span class="sxs-lookup"><span data-stu-id="cba84-231">This class sets a background color for the selected row.</span></span>

* <span data-ttu-id="cba84-232">В каждой строке непосредственно перед другими ссылками добавили новую гиперссылку с меткой **Select**, которая отправляет идентификатор выбранного преподавателя в метод `Index`.</span><span class="sxs-lookup"><span data-stu-id="cba84-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```cshtml
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="cba84-233">Запустите приложение и выберите вкладку **Instructors**. На странице отображается свойство Location связанных сущностей OfficeAssignment либо пустая ячейка таблицы при отсутствии связанной сущности OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="cba84-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Страница индекса преподавателей, когда ничего не выбрано](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="cba84-235">В файле *Views/Instructors/Index.cshtml* после закрывающего таблицу элемента (в конце файла) добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="cba84-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="cba84-236">Этот код отображает список связанных с преподавателем курсов, когда преподаватель выбран.</span><span class="sxs-lookup"><span data-stu-id="cba84-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="cba84-237">Этот код считывает свойство `Courses` модели представления для отображения списка курсов.</span><span class="sxs-lookup"><span data-stu-id="cba84-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="cba84-238">Он также предоставляет гиперссылку **Select**, которая отправляет идентификатор выбранного курса в метод действия `Index`.</span><span class="sxs-lookup"><span data-stu-id="cba84-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="cba84-239">Обновите страницу и выберите преподавателя.</span><span class="sxs-lookup"><span data-stu-id="cba84-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="cba84-240">Вы увидите сетку, которая отображает курсы, назначенные выбранному преподавателю, и для каждого курса отобразится имя связанного факультета.</span><span class="sxs-lookup"><span data-stu-id="cba84-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Страница индекса преподавателей, выбран преподаватель](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="cba84-242">После только что добавленного блока кода добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="cba84-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="cba84-243">Он отображает список студентов, которые зачислены на курс при выборе этого курса.</span><span class="sxs-lookup"><span data-stu-id="cba84-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="cba84-244">Этот код считывает свойство Enrollments модели представления для отображения списка студентов, зачисленных на этот курс.</span><span class="sxs-lookup"><span data-stu-id="cba84-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="cba84-245">Снова обновите страницу и выберите преподавателя.</span><span class="sxs-lookup"><span data-stu-id="cba84-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="cba84-246">Затем выберите курс, чтобы увидеть список зачисленных студентов и их оценки.</span><span class="sxs-lookup"><span data-stu-id="cba84-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Страница индекса преподавателей, выбраны преподаватель и курс](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="cba84-248">Сведения о явной загрузке</span><span class="sxs-lookup"><span data-stu-id="cba84-248">About explicit loading</span></span>

<span data-ttu-id="cba84-249">При получении списка преподавателей в файле *InstructorsController.cs* мы указали безотложную загрузку для свойства навигации `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="cba84-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="cba84-250">Пусть ожидается, что пользователи лишь изредка будут просматривать список зачисления для выбранных преподавателя и курса.</span><span class="sxs-lookup"><span data-stu-id="cba84-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="cba84-251">В этом случае, возможно, потребуется загружать данные о зачислении, только когда они запрошены.</span><span class="sxs-lookup"><span data-stu-id="cba84-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="cba84-252">Чтобы продемонстрировать пример того, как выполнять явную загрузку, заменим метод `Index` следующим кодом, который удаляет упреждающую загрузку для Enrollments и загружает это свойство явно.</span><span class="sxs-lookup"><span data-stu-id="cba84-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="cba84-253">Изменения в коде выделены.</span><span class="sxs-lookup"><span data-stu-id="cba84-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="cba84-254">В новом коде из блока, который получает сущности преподавателей, удален вызов метода *ThenInclude*.</span><span class="sxs-lookup"><span data-stu-id="cba84-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="cba84-255">Также удаляется и `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="cba84-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="cba84-256">Если выбраны преподаватели и курс, выделенный код получает сущности Enrollment выбранного курса и сущности Student для каждой сущности Enrollment.</span><span class="sxs-lookup"><span data-stu-id="cba84-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="cba84-257">Запустите приложение, перейдите на страницу Instructors Index, и вы не увидите никаких изменений, несмотря на то, что мы изменили способ получения данных.</span><span class="sxs-lookup"><span data-stu-id="cba84-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="cba84-258">Получите код</span><span class="sxs-lookup"><span data-stu-id="cba84-258">Get the code</span></span>

[<span data-ttu-id="cba84-259">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="cba84-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="cba84-260">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="cba84-260">Next steps</span></span>

<span data-ttu-id="cba84-261">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="cba84-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cba84-262">Дополнительные сведения о загрузке связанных данных</span><span class="sxs-lookup"><span data-stu-id="cba84-262">Learned how to load related data</span></span>
> * <span data-ttu-id="cba84-263">Создание страницы курсов</span><span class="sxs-lookup"><span data-stu-id="cba84-263">Created a Courses page</span></span>
> * <span data-ttu-id="cba84-264">Создание страницы преподавателей</span><span class="sxs-lookup"><span data-stu-id="cba84-264">Created an Instructors page</span></span>
> * <span data-ttu-id="cba84-265">Дополнительные сведения о явной загрузке</span><span class="sxs-lookup"><span data-stu-id="cba84-265">Learned about explicit loading</span></span>

<span data-ttu-id="cba84-266">В следующем учебнике описано, как обновить связанные данные.</span><span class="sxs-lookup"><span data-stu-id="cba84-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="cba84-267">Обновление связанных данных</span><span class="sxs-lookup"><span data-stu-id="cba84-267">Update related data</span></span>](update-related-data.md)
