---
title: 'Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8'
author: rick-anderson
description: Сведения о том, как создать приложение Razor Pages с помощью Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 55fcc2883dac31fc22bad1b5f9367e20879b6c43
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586427"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="49718-103">Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8</span><span class="sxs-lookup"><span data-stu-id="49718-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="49718-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="49718-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="49718-105">Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="49718-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="49718-106">В учебниках создается веб-сайт для вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="49718-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="49718-107">На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="49718-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="49718-108">В этом руководстве используется подход Code First.</span><span class="sxs-lookup"><span data-stu-id="49718-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="49718-109">См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="49718-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="49718-110">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="49718-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="49718-111">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="49718-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49718-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="49718-112">Prerequisites</span></span>

* <span data-ttu-id="49718-113">Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.</span><span class="sxs-lookup"><span data-stu-id="49718-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="49718-116">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="49718-116">Database engines</span></span>

<span data-ttu-id="49718-117">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="49718-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="49718-118">В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.</span><span class="sxs-lookup"><span data-stu-id="49718-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="49718-119">Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="49718-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="49718-120">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="49718-120">Troubleshooting</span></span>

<span data-ttu-id="49718-121">Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="49718-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="49718-122">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="49718-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="49718-123">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="49718-123">The sample app</span></span>

<span data-ttu-id="49718-124">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="49718-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="49718-125">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="49718-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="49718-126">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="49718-126">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

<span data-ttu-id="49718-129">Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов.</span><span class="sxs-lookup"><span data-stu-id="49718-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="49718-130">Это руководство посвящено использованию EF Core с ASP.NET Core, а не настройке пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="49718-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="49718-131">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="49718-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="49718-133">Запустите Visual Studio и щелкните **Создать проект**</span><span class="sxs-lookup"><span data-stu-id="49718-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="49718-134">В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="49718-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="49718-135">В диалоговом окне **Настроить новый проект** введите `ContosoUniversity` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="49718-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="49718-136">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен (`namespace`) совпадали при копировании кода.</span><span class="sxs-lookup"><span data-stu-id="49718-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="49718-137">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="49718-137">Select **Create**.</span></span>
1. <span data-ttu-id="49718-138">В диалоговом окне **Создайте веб-приложение ASP.NET Core** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="49718-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="49718-139">В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="49718-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="49718-140">**Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="49718-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="49718-141">Нажмите кнопку **Создать**.
      ![Диалоговое окно создания проекта ASP.NET Core](~/data/ef-rp/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="49718-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-rp/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49718-143">В терминале перейдите в папку, в которой следует создать папку проекта.</span><span class="sxs-lookup"><span data-stu-id="49718-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="49718-144">Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:</span><span class="sxs-lookup"><span data-stu-id="49718-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="49718-145">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="49718-145">Set up the site style</span></span>

<span data-ttu-id="49718-146">Скопируйте следующий код и вставьте его в файл *Pages/Shared/_Layout.cshtml*: </span><span class="sxs-lookup"><span data-stu-id="49718-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="49718-147">Файл макета задает заголовок, нижний колонтитул и меню для сайта.</span><span class="sxs-lookup"><span data-stu-id="49718-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="49718-148">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="49718-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="49718-149">Заменяет все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="49718-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="49718-150">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="49718-150">There are three occurrences.</span></span>
* <span data-ttu-id="49718-151">Удаляются пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность).</span><span class="sxs-lookup"><span data-stu-id="49718-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="49718-152">Добавляются пункты меню **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="49718-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="49718-153">Замените содержимое файла *Pages/Index.cshtml* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="49718-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="49718-154">Приведенный выше код заменяет текст об ASP.NET Core текстом об этом приложении.</span><span class="sxs-lookup"><span data-stu-id="49718-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="49718-155">Запустите приложение, чтобы проверить правильность отображения домашней страницы.</span><span class="sxs-lookup"><span data-stu-id="49718-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="49718-156">Модель данных</span><span class="sxs-lookup"><span data-stu-id="49718-156">The data model</span></span>

<span data-ttu-id="49718-157">В следующих разделах создается модель данных.</span><span class="sxs-lookup"><span data-stu-id="49718-157">The following sections create a data model:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="49718-159">Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="49718-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="49718-160">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="49718-160">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

* <span data-ttu-id="49718-162">Создайте папку *Models* (Модели) в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="49718-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="49718-163">Создайте файл *Models/Student.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="49718-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="49718-164">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="49718-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="49718-165">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="49718-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="49718-166">Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="49718-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="49718-167">Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)</span><span class="sxs-lookup"><span data-stu-id="49718-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="49718-168">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="49718-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="49718-169">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="49718-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="49718-170">В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся.</span><span class="sxs-lookup"><span data-stu-id="49718-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="49718-171">Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment.</span><span class="sxs-lookup"><span data-stu-id="49718-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="49718-172">В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося.</span><span class="sxs-lookup"><span data-stu-id="49718-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="49718-173">Например, предположим, что строка Student содержит идентификатор 1.</span><span class="sxs-lookup"><span data-stu-id="49718-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="49718-174">Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1.</span><span class="sxs-lookup"><span data-stu-id="49718-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="49718-175">StudentID — это *внешний ключ* в таблице Enrollment.</span><span class="sxs-lookup"><span data-stu-id="49718-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="49718-176">Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment.</span><span class="sxs-lookup"><span data-stu-id="49718-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="49718-177">Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="49718-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="49718-178">Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="49718-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="49718-179">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="49718-179">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="49718-181">Создайте файл *Models/Enrollment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="49718-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="49718-182">Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`.</span><span class="sxs-lookup"><span data-stu-id="49718-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="49718-183">Для рабочей модели данных выберите один шаблон и используйте только его.</span><span class="sxs-lookup"><span data-stu-id="49718-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="49718-184">В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу.</span><span class="sxs-lookup"><span data-stu-id="49718-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="49718-185">Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.</span><span class="sxs-lookup"><span data-stu-id="49718-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="49718-186">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="49718-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="49718-187">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="49718-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="49718-188">Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="49718-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="49718-189">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="49718-190">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="49718-191">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="49718-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="49718-192">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="49718-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="49718-193">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="49718-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="49718-194">Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="49718-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="49718-195">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="49718-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="49718-196">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="49718-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="49718-197">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="49718-197">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="49718-199">Создайте файл *Models/Course.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="49718-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="49718-200">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="49718-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="49718-201">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="49718-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="49718-202">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="49718-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="49718-203">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="49718-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="49718-204">Формирование шаблона для страниц Student</span><span class="sxs-lookup"><span data-stu-id="49718-204">Scaffold Student pages</span></span>

<span data-ttu-id="49718-205">В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.</span><span class="sxs-lookup"><span data-stu-id="49718-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="49718-206">Класс `DbContext` EF Core.</span><span class="sxs-lookup"><span data-stu-id="49718-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="49718-207">Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="49718-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="49718-208">Он является производным от класса <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="49718-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="49718-209">Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49718-211">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="49718-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="49718-212">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="49718-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="49718-213">В диалоговом окне **Добавление нового элемента шаблона** выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="49718-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="49718-214">На левой вкладке выберите **Установленные > Общие > Страницы Razor** .</span><span class="sxs-lookup"><span data-stu-id="49718-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="49718-215">Выберите **Razor Pages на основе Entity Framework (CRUD)**  > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="49718-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="49718-216">В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="49718-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="49718-217">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="49718-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="49718-218">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="49718-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="49718-219">Измените имя контекста данных так, чтобы оно заканчивалось на `SchoolContext`, а не на `ContosoUniversityContext`.</span><span class="sxs-lookup"><span data-stu-id="49718-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="49718-220">Новое имя контекста: `ContosoUniversity.Data.SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="49718-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
    * <span data-ttu-id="49718-221">Выберите **Добавить**, чтобы завершить добавление класса контекста данных.</span><span class="sxs-lookup"><span data-stu-id="49718-221">Select **Add** to finish adding the data context class.</span></span>
   * <span data-ttu-id="49718-222">Выберите **Добавить**, чтобы закрыть диалоговое окно **Добавление Razor Pages**.</span><span class="sxs-lookup"><span data-stu-id="49718-222">Select **Add** to finish the **Add Razor Pages** dialog.</span></span>

<span data-ttu-id="49718-223">Следующие пакеты устанавливаются автоматически:</span><span class="sxs-lookup"><span data-stu-id="49718-223">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49718-225">Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="49718-225">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="49718-226">Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="49718-226">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="49718-227">Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.</span><span class="sxs-lookup"><span data-stu-id="49718-227">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="49718-228">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="49718-228">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="49718-229">Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="49718-229">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="49718-230">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.</span><span class="sxs-lookup"><span data-stu-id="49718-230">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="49718-231">**В Windows**</span><span class="sxs-lookup"><span data-stu-id="49718-231">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="49718-232">**В macOS или Linux**</span><span class="sxs-lookup"><span data-stu-id="49718-232">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="49718-233">Если предыдущий шаг завершился сбоем, выполните сборку проекта и повторите шаг формирования шаблона.</span><span class="sxs-lookup"><span data-stu-id="49718-233">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="49718-234">В процессе формирования шаблона выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="49718-234">The scaffolding process:</span></span>

* <span data-ttu-id="49718-235">создаются страницы Razor в папке *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="49718-235">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="49718-236">*Create.cshtml* и *Create.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-236">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="49718-237">*Delete.cshtml* и *Delete.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-237">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="49718-238">*Details.cshtml* и *Details.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-238">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="49718-239">*Edit.cshtml* и *Edit.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-239">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="49718-240">*Index.cshtml* и *Index.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-240">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="49718-241">создается файл *Data/SchoolContext.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-241">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="49718-242">добавляется контекст для внедрения зависимостей в файле *Startup.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-242">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="49718-243">добавляет строку подключения к базе данных в файл *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="49718-243">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="49718-244">Строка подключения к базе данных</span><span class="sxs-lookup"><span data-stu-id="49718-244">Database connection string</span></span>

<span data-ttu-id="49718-245">Средство формирования шаблонов создает строку подключения в файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="49718-245">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49718-247">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="49718-247">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="49718-248">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="49718-248">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="49718-249">По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="49718-249">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49718-251">Сократите строку подключения SQLite до *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="49718-251">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="49718-252">Обновление класса контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-252">Update the database context class</span></span>

<span data-ttu-id="49718-253">Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="49718-253">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="49718-254">Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="49718-254">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="49718-255">Контекст указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="49718-255">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="49718-256">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="49718-256">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="49718-257">Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="49718-257">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="49718-258">Приведенный выше код изменяет форму слова `DbSet<Student> Student` на множественную: `DbSet<Student> Students`.</span><span class="sxs-lookup"><span data-stu-id="49718-258">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="49718-259">Чтобы код Razor Pages соответствовал новому имени `DBSet`, произведите глобальное изменение `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="49718-259">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="49718-260">на `_context.Students.`.</span><span class="sxs-lookup"><span data-stu-id="49718-260">to: `_context.Students.`</span></span>

<span data-ttu-id="49718-261">Всего это имя встречается 8 раз.</span><span class="sxs-lookup"><span data-stu-id="49718-261">There are 8 occurrences.</span></span>

<span data-ttu-id="49718-262">Так как набор сущностей содержит несколько сущностей, многие разработчики предпочитают имена свойств `DBSet` во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="49718-262">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="49718-263">Выделенный код:</span><span class="sxs-lookup"><span data-stu-id="49718-263">The highlighted code:</span></span>

* <span data-ttu-id="49718-264">создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="49718-264">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="49718-265">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="49718-265">In EF Core terminology:</span></span>
  * <span data-ttu-id="49718-266">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="49718-266">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="49718-267">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="49718-267">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="49718-268">Вызывает <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="49718-268">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="49718-269">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="49718-269">`OnModelCreating`:</span></span>
  * <span data-ttu-id="49718-270">вызывается, когда контекст `SchoolContext` был инициализирован, но прежде чем модель была заблокирована и использована для инициализации контекста;</span><span class="sxs-lookup"><span data-stu-id="49718-270">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="49718-271">является обязательным, так как далее в руководстве сущность `Student` будет содержать ссылки на другие сущности.</span><span class="sxs-lookup"><span data-stu-id="49718-271">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="49718-272">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="49718-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="49718-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="49718-273">Startup.cs</span></span>

<span data-ttu-id="49718-274">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49718-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="49718-275">С помощью внедрения зависимостей службы, например `SchoolContext`, регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="49718-275">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="49718-276">Затем компоненты, которые используют эти службы, например Razor Pages, обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="49718-276">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="49718-277">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="49718-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="49718-278">Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="49718-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-279">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49718-280">Следующие выделенные строки были добавлены средством формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="49718-280">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49718-282">Убедитесь в том, что код, добавленный средством формирования шаблонов, вызывает `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="49718-282">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="49718-283">Сведения об использовании рабочей базы данных см. в статье [Использование SQLite для среды разработки и SQL Server для рабочей среды](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).</span><span class="sxs-lookup"><span data-stu-id="49718-283">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="49718-284">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="49718-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="49718-285">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="49718-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="49718-286">Добавление фильтра исключений базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-286">Add the database exception filter</span></span>

<span data-ttu-id="49718-287">Добавьте <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> в `ConfigureServices`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="49718-287">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-288">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="49718-289">Добавьте пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="49718-289">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="49718-290">Чтобы добавить пакет NuGet, в PMC введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="49718-290">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="49718-292">Пакет NuGet `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` предоставляет ПО промежуточного слоя ASP.NET Core для страниц ошибок Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="49718-292">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="49718-293">Это ПО промежуточного слоя помогает обнаруживать и диагностировать ошибки с помощью миграций Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="49718-293">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="49718-294">`AddDatabaseDeveloperPageExceptionFilter` предоставляет полезные сведения об ошибках в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="49718-294">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="49718-295">Создание базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-295">Create the database</span></span>

<span data-ttu-id="49718-296">Обновите файл *Program.cs*, чтобы создать базу данных, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="49718-296">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="49718-297">Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует.</span><span class="sxs-lookup"><span data-stu-id="49718-297">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="49718-298">Если база данных не существует, она создается вместе со схемой.</span><span class="sxs-lookup"><span data-stu-id="49718-298">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="49718-299">`EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.</span><span class="sxs-lookup"><span data-stu-id="49718-299">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="49718-300">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="49718-300">Delete the database.</span></span> <span data-ttu-id="49718-301">Все существующие данные теряются.</span><span class="sxs-lookup"><span data-stu-id="49718-301">Any existing data is lost.</span></span>
* <span data-ttu-id="49718-302">Модель данных изменяется.</span><span class="sxs-lookup"><span data-stu-id="49718-302">Change the data model.</span></span> <span data-ttu-id="49718-303">Например, добавляется поле `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="49718-303">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="49718-304">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="49718-304">Run the app.</span></span>
* <span data-ttu-id="49718-305">Метод `EnsureCreated` создает базу данных с новой схемой.</span><span class="sxs-lookup"><span data-stu-id="49718-305">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="49718-306">Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется.</span><span class="sxs-lookup"><span data-stu-id="49718-306">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="49718-307">Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной.</span><span class="sxs-lookup"><span data-stu-id="49718-307">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="49718-308">В таком случае используйте перенос.</span><span class="sxs-lookup"><span data-stu-id="49718-308">When that is the case, use migrations.</span></span>

<span data-ttu-id="49718-309">Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос.</span><span class="sxs-lookup"><span data-stu-id="49718-309">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="49718-310">Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.</span><span class="sxs-lookup"><span data-stu-id="49718-310">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="49718-311">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="49718-311">Test the app</span></span>

* <span data-ttu-id="49718-312">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="49718-312">Run the app.</span></span>
* <span data-ttu-id="49718-313">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="49718-313">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="49718-314">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="49718-314">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="49718-315">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-315">Seed the database</span></span>

<span data-ttu-id="49718-316">Метод `EnsureCreated` создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-316">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="49718-317">В этом разделе добавляется код, который заполняет базу данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="49718-317">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="49718-318">Создайте файл *Data/DbInitializer.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="49718-318">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="49718-319">Этот код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="49718-319">The code checks if there are any students in the database.</span></span> <span data-ttu-id="49718-320">Если учащихся нет, в базу данных добавляются тестовые данные.</span><span class="sxs-lookup"><span data-stu-id="49718-320">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="49718-321">Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="49718-321">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="49718-322">В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="49718-322">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="49718-323">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-323">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49718-324">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="49718-324">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="49718-325">Выберите `Y`, чтобы удалить базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-325">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-326">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-326">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49718-327">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="49718-327">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="49718-328">Перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="49718-328">Restart the app.</span></span>
* <span data-ttu-id="49718-329">Выберите страницу учащихся, чтобы увидеть заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="49718-329">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="49718-330">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-330">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-331">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49718-332">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="49718-332">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="49718-333">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="49718-333">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="49718-334">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="49718-334">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="49718-335">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="49718-335">Expand the **Tables** node.</span></span>
* <span data-ttu-id="49718-336">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="49718-336">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="49718-337">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код**, чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-337">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-338">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-338">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49718-339">Используйте средство SQLite для просмотра схемы базы данных и заполненных данных.</span><span class="sxs-lookup"><span data-stu-id="49718-339">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="49718-340">Файл базы данных называется *CU.db* и находится в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="49718-340">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="49718-341">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="49718-341">Asynchronous code</span></span>

<span data-ttu-id="49718-342">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="49718-342">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="49718-343">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="49718-343">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="49718-344">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="49718-344">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="49718-345">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="49718-345">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="49718-346">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="49718-346">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="49718-347">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="49718-347">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="49718-348">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="49718-348">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="49718-349">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="49718-349">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="49718-350">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="49718-350">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="49718-351">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="49718-351">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="49718-352">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="49718-352">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="49718-353">создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="49718-353">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="49718-354">Тип возвращаемого значения `Task` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="49718-354">The `Task` return type represents ongoing work.</span></span>
* <span data-ttu-id="49718-355">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="49718-355">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="49718-356">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="49718-356">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="49718-357">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="49718-357">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="49718-358">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="49718-358">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="49718-359">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="49718-359">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="49718-360">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="49718-360">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="49718-361">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="49718-361">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="49718-362">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="49718-362">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="49718-363">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="49718-363">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="49718-364">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-364">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="49718-365">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="49718-365">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="49718-366">Вопросы производительности</span><span class="sxs-lookup"><span data-stu-id="49718-366">Performance considerations</span></span>

<span data-ttu-id="49718-367">Как правило, веб-страница не должна загружать произвольное число строк.</span><span class="sxs-lookup"><span data-stu-id="49718-367">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="49718-368">Запрос должен использовать разбиение на страницы или применять ограничение.</span><span class="sxs-lookup"><span data-stu-id="49718-368">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="49718-369">Например, предыдущий запрос может использовать `Take` для ограничения количества возвращаемых строк:</span><span class="sxs-lookup"><span data-stu-id="49718-369">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="49718-370">Если в процессе перечисления большой таблицы в представлении возникло исключение базы данных, может быть возвращен ответ HTTP 200 с сообщением о частично сформированных данных.</span><span class="sxs-lookup"><span data-stu-id="49718-370">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="49718-371">Значение <xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> по умолчанию равно 1024.</span><span class="sxs-lookup"><span data-stu-id="49718-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="49718-372">Следующий код задает `MaxModelBindingCollectionSize`:</span><span class="sxs-lookup"><span data-stu-id="49718-372">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="49718-373">Разбиение на страницы рассматривается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="49718-373">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="49718-374">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="49718-374">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="49718-375">Следующий учебник</span><span class="sxs-lookup"><span data-stu-id="49718-375">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="49718-376">Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="49718-376">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="49718-377">В учебниках создается веб-сайт для вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="49718-377">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="49718-378">На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="49718-378">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="49718-379">В этом руководстве используется подход Code First.</span><span class="sxs-lookup"><span data-stu-id="49718-379">The tutorial uses the code first approach.</span></span> <span data-ttu-id="49718-380">См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="49718-380">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="49718-381">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="49718-381">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="49718-382">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="49718-382">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49718-383">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="49718-383">Prerequisites</span></span>

* <span data-ttu-id="49718-384">Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.</span><span class="sxs-lookup"><span data-stu-id="49718-384">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-385">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-385">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="49718-387">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="49718-387">Database engines</span></span>

<span data-ttu-id="49718-388">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="49718-388">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="49718-389">В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.</span><span class="sxs-lookup"><span data-stu-id="49718-389">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="49718-390">Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="49718-390">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="49718-391">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="49718-391">Troubleshooting</span></span>

<span data-ttu-id="49718-392">Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="49718-392">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="49718-393">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="49718-393">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="49718-394">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="49718-394">The sample app</span></span>

<span data-ttu-id="49718-395">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="49718-395">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="49718-396">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="49718-396">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="49718-397">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="49718-397">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

<span data-ttu-id="49718-400">Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов.</span><span class="sxs-lookup"><span data-stu-id="49718-400">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="49718-401">Это руководство посвящено использованию EF Core, а не настройке пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="49718-401">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="49718-402">Чтобы получить исходный код готового проекта, перейдите по ссылке в верхней части страницы.</span><span class="sxs-lookup"><span data-stu-id="49718-402">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="49718-403">В папке *cu30* содержится код для версии учебника по ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="49718-403">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="49718-404">Файлы, отражающие состояние кода для учебников 1–7, находятся в папке *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="49718-404">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-405">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49718-406">Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="49718-406">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="49718-407">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="49718-407">Build the project.</span></span>
* <span data-ttu-id="49718-408">В консоли диспетчера пакетов (PMC) выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="49718-408">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="49718-409">Запустите проект, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-409">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49718-411">Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="49718-411">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="49718-412">Удалите файл *ContosoUniversity.csproj*, а файл *ContosoUniversitySQLite.csproj* переименуйте в *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="49718-412">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="49718-413">В файле *Program.cs* закомментируйте `#define Startup`, чтобы использовать `StartupSQLite`.</span><span class="sxs-lookup"><span data-stu-id="49718-413">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="49718-414">Удалите файл *appSettings.json*, а файл *appSettingsSQLite.json* переименуйте в *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="49718-414">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="49718-415">Удалите папку *Migrations*, а папку *MigrationsSQL* переименуйте в *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="49718-415">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="49718-416">Выполните глобальный поиск `#if SQLiteVersion` и удалите `#if SQLiteVersion` и связанную инструкцию `#endif`.</span><span class="sxs-lookup"><span data-stu-id="49718-416">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="49718-417">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="49718-417">Build the project.</span></span>
* <span data-ttu-id="49718-418">В командной строке в папке проекта выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="49718-418">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="49718-419">В средстве SQLite выполните следующую инструкцию SQL:</span><span class="sxs-lookup"><span data-stu-id="49718-419">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="49718-420">Запустите проект, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-420">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="49718-421">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="49718-421">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49718-423">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="49718-423">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49718-424">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="49718-424">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="49718-425">Назовите проект *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="49718-425">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="49718-426">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен совпадали при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="49718-426">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="49718-427">Выберите в раскрывающихся списках пункты **.NET Core** и **ASP.NET Core 3.0**, а затем выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="49718-427">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-428">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-428">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49718-429">В терминале перейдите в папку, в которой следует создать папку проекта.</span><span class="sxs-lookup"><span data-stu-id="49718-429">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="49718-430">Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:</span><span class="sxs-lookup"><span data-stu-id="49718-430">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="49718-431">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="49718-431">Set up the site style</span></span>

<span data-ttu-id="49718-432">Настройте заголовок сайта, нижний колонтитул и меню, внеся изменения в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="49718-432">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="49718-433">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="49718-433">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="49718-434">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="49718-434">There are three occurrences.</span></span>

* <span data-ttu-id="49718-435">Удалите пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность). Добавьте пункты **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="49718-435">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="49718-436">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="49718-436">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="49718-437">Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст об ASP.NET Core описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="49718-437">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="49718-438">Запустите приложение, чтобы проверить правильность отображения домашней страницы.</span><span class="sxs-lookup"><span data-stu-id="49718-438">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="49718-439">Модель данных</span><span class="sxs-lookup"><span data-stu-id="49718-439">The data model</span></span>

<span data-ttu-id="49718-440">В следующих разделах создается модель данных.</span><span class="sxs-lookup"><span data-stu-id="49718-440">The following sections create a data model:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="49718-442">Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="49718-442">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="49718-443">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="49718-443">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

* <span data-ttu-id="49718-445">Создайте папку *Models* (Модели) в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="49718-445">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="49718-446">Создайте файл *Models/Student.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="49718-446">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="49718-447">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="49718-447">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="49718-448">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="49718-448">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="49718-449">Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="49718-449">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="49718-450">Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)</span><span class="sxs-lookup"><span data-stu-id="49718-450">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="49718-451">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="49718-451">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="49718-452">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="49718-452">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="49718-453">В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся.</span><span class="sxs-lookup"><span data-stu-id="49718-453">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="49718-454">Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment.</span><span class="sxs-lookup"><span data-stu-id="49718-454">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="49718-455">В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося.</span><span class="sxs-lookup"><span data-stu-id="49718-455">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="49718-456">Например, предположим, что строка Student содержит идентификатор 1.</span><span class="sxs-lookup"><span data-stu-id="49718-456">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="49718-457">Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1.</span><span class="sxs-lookup"><span data-stu-id="49718-457">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="49718-458">StudentID — это *внешний ключ* в таблице Enrollment.</span><span class="sxs-lookup"><span data-stu-id="49718-458">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="49718-459">Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment.</span><span class="sxs-lookup"><span data-stu-id="49718-459">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="49718-460">Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="49718-460">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="49718-461">Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="49718-461">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="49718-462">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="49718-462">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="49718-464">Создайте файл *Models/Enrollment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="49718-464">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="49718-465">Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`.</span><span class="sxs-lookup"><span data-stu-id="49718-465">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="49718-466">Для рабочей модели данных выберите один шаблон и используйте только его.</span><span class="sxs-lookup"><span data-stu-id="49718-466">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="49718-467">В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу.</span><span class="sxs-lookup"><span data-stu-id="49718-467">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="49718-468">Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.</span><span class="sxs-lookup"><span data-stu-id="49718-468">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="49718-469">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="49718-469">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="49718-470">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="49718-470">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="49718-471">Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="49718-471">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="49718-472">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-472">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="49718-473">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-473">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="49718-474">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="49718-474">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="49718-475">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="49718-475">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="49718-476">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="49718-476">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="49718-477">Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="49718-477">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="49718-478">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="49718-478">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="49718-479">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="49718-479">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="49718-480">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="49718-480">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="49718-482">Создайте файл *Models/Course.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="49718-482">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="49718-483">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="49718-483">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="49718-484">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="49718-484">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="49718-485">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="49718-485">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="49718-486">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="49718-486">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="49718-487">Формирование шаблона для страниц Student</span><span class="sxs-lookup"><span data-stu-id="49718-487">Scaffold Student pages</span></span>

<span data-ttu-id="49718-488">В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.</span><span class="sxs-lookup"><span data-stu-id="49718-488">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="49718-489">Класс *контекста* EF Core.</span><span class="sxs-lookup"><span data-stu-id="49718-489">An EF Core *context* class.</span></span> <span data-ttu-id="49718-490">Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="49718-490">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="49718-491">Он является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="49718-491">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="49718-492">Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-492">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-493">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49718-494">В папке *Pages* создайте папку *Students*.</span><span class="sxs-lookup"><span data-stu-id="49718-494">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="49718-495">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="49718-495">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="49718-496">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="49718-496">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="49718-497">В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="49718-497">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="49718-498">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="49718-498">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="49718-499">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="49718-499">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="49718-500">Измените имя контекста данных с *ContosoUniversity.Models.ContosoUniversityContext* на *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="49718-500">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="49718-501">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="49718-501">Select **Add**.</span></span>

<span data-ttu-id="49718-502">Следующие пакеты устанавливаются автоматически:</span><span class="sxs-lookup"><span data-stu-id="49718-502">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-503">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-503">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49718-504">Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="49718-504">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="49718-505">Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="49718-505">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="49718-506">Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.</span><span class="sxs-lookup"><span data-stu-id="49718-506">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="49718-507">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="49718-507">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="49718-508">Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="49718-508">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="49718-509">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.</span><span class="sxs-lookup"><span data-stu-id="49718-509">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="49718-510">**В Windows**</span><span class="sxs-lookup"><span data-stu-id="49718-510">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="49718-511">**В macOS или Linux**</span><span class="sxs-lookup"><span data-stu-id="49718-511">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="49718-512">Если в предыдущем шаге возникает проблема, выполните сборку проекта и повторите шаг формирования шаблона.</span><span class="sxs-lookup"><span data-stu-id="49718-512">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="49718-513">В процессе формирования шаблона выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="49718-513">The scaffolding process:</span></span>

* <span data-ttu-id="49718-514">создаются страницы Razor в папке *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="49718-514">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="49718-515">*Create.cshtml* и *Create.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-515">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="49718-516">*Delete.cshtml* и *Delete.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-516">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="49718-517">*Details.cshtml* и *Details.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-517">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="49718-518">*Edit.cshtml* и *Edit.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-518">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="49718-519">*Index.cshtml* и *Index.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-519">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="49718-520">создается файл *Data/SchoolContext.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-520">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="49718-521">добавляется контекст для внедрения зависимостей в файле *Startup.cs*;</span><span class="sxs-lookup"><span data-stu-id="49718-521">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="49718-522">добавляет строку подключения к базе данных в файл *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="49718-522">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="49718-523">Строка подключения к базе данных</span><span class="sxs-lookup"><span data-stu-id="49718-523">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-524">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-524">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49718-525">В файле *appsettings.json* указывается строка подключения для [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="49718-525">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="49718-526">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="49718-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="49718-527">По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="49718-527">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-528">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-528">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49718-529">Измените строку подключения так, чтобы она указывала на файл базы данных SQLite с именем *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="49718-529">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="49718-530">Обновление класса контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-530">Update the database context class</span></span>

<span data-ttu-id="49718-531">Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="49718-531">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="49718-532">Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="49718-532">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="49718-533">Контекст указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="49718-533">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="49718-534">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="49718-534">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="49718-535">Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="49718-535">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="49718-536">Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="49718-536">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="49718-537">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="49718-537">In EF Core terminology:</span></span>

* <span data-ttu-id="49718-538">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="49718-538">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="49718-539">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="49718-539">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="49718-540">Так как набор сущностей содержит несколько сущностей, свойства DBSet должны иметь имена во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="49718-540">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="49718-541">Так как средство формирования шаблонов создало DBSet `Student`, в этом шаге его имя меняется на имя во множественном числе: `Students`.</span><span class="sxs-lookup"><span data-stu-id="49718-541">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="49718-542">Чтобы код Razor Pages соответствовал новому имени DBSet, измените `_context.Student` на `_context.Students` во всем проекте.</span><span class="sxs-lookup"><span data-stu-id="49718-542">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="49718-543">Всего это имя встречается 8 раз.</span><span class="sxs-lookup"><span data-stu-id="49718-543">There are 8 occurrences.</span></span>

<span data-ttu-id="49718-544">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="49718-544">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="49718-545">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="49718-545">Startup.cs</span></span>

<span data-ttu-id="49718-546">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49718-546">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="49718-547">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="49718-547">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="49718-548">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="49718-548">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="49718-549">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="49718-549">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="49718-550">Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="49718-550">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-551">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-551">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49718-552">Выделенные строки в `ConfigureServices` были добавлены средством формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="49718-552">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-553">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-553">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49718-554">Убедитесь в том, что код, добавленный средством формирования шаблонов в `ConfigureServices`, вызывает `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="49718-554">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="49718-555">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="49718-555">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="49718-556">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="49718-556">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="49718-557">Создание базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-557">Create the database</span></span>

<span data-ttu-id="49718-558">Обновите файл *Program.cs*, чтобы создать базу данных, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="49718-558">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="49718-559">Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует.</span><span class="sxs-lookup"><span data-stu-id="49718-559">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="49718-560">Если база данных не существует, она создается вместе со схемой.</span><span class="sxs-lookup"><span data-stu-id="49718-560">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="49718-561">`EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.</span><span class="sxs-lookup"><span data-stu-id="49718-561">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="49718-562">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="49718-562">Delete the database.</span></span> <span data-ttu-id="49718-563">Все существующие данные теряются.</span><span class="sxs-lookup"><span data-stu-id="49718-563">Any existing data is lost.</span></span>
* <span data-ttu-id="49718-564">Модель данных изменяется.</span><span class="sxs-lookup"><span data-stu-id="49718-564">Change the data model.</span></span> <span data-ttu-id="49718-565">Например, добавляется поле `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="49718-565">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="49718-566">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="49718-566">Run the app.</span></span>
* <span data-ttu-id="49718-567">Метод `EnsureCreated` создает базу данных с новой схемой.</span><span class="sxs-lookup"><span data-stu-id="49718-567">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="49718-568">Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется.</span><span class="sxs-lookup"><span data-stu-id="49718-568">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="49718-569">Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной.</span><span class="sxs-lookup"><span data-stu-id="49718-569">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="49718-570">В таком случае используйте перенос.</span><span class="sxs-lookup"><span data-stu-id="49718-570">When that is the case, use migrations.</span></span>

<span data-ttu-id="49718-571">Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос.</span><span class="sxs-lookup"><span data-stu-id="49718-571">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="49718-572">Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.</span><span class="sxs-lookup"><span data-stu-id="49718-572">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="49718-573">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="49718-573">Test the app</span></span>

* <span data-ttu-id="49718-574">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="49718-574">Run the app.</span></span>
* <span data-ttu-id="49718-575">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="49718-575">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="49718-576">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="49718-576">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="49718-577">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-577">Seed the database</span></span>

<span data-ttu-id="49718-578">Метод `EnsureCreated` создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-578">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="49718-579">В этом разделе добавляется код, который заполняет базу данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="49718-579">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="49718-580">Создайте файл *Data/DbInitializer.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="49718-580">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="49718-581">Этот код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="49718-581">The code checks if there are any students in the database.</span></span> <span data-ttu-id="49718-582">Если учащихся нет, в базу данных добавляются тестовые данные.</span><span class="sxs-lookup"><span data-stu-id="49718-582">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="49718-583">Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="49718-583">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="49718-584">В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="49718-584">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="49718-585">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-585">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49718-586">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="49718-586">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-587">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-587">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49718-588">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="49718-588">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="49718-589">Перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="49718-589">Restart the app.</span></span>

* <span data-ttu-id="49718-590">Выберите страницу учащихся, чтобы увидеть заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="49718-590">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="49718-591">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-591">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-592">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-592">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49718-593">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="49718-593">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="49718-594">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="49718-594">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="49718-595">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="49718-595">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="49718-596">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="49718-596">Expand the **Tables** node.</span></span>
* <span data-ttu-id="49718-597">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="49718-597">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="49718-598">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код**, чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-598">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-599">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-599">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49718-600">Используйте средство SQLite для просмотра схемы базы данных и заполненных данных.</span><span class="sxs-lookup"><span data-stu-id="49718-600">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="49718-601">Файл базы данных называется *CU.db* и находится в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="49718-601">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="49718-602">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="49718-602">Asynchronous code</span></span>

<span data-ttu-id="49718-603">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="49718-603">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="49718-604">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="49718-604">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="49718-605">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="49718-605">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="49718-606">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="49718-606">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="49718-607">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="49718-607">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="49718-608">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="49718-608">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="49718-609">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="49718-609">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="49718-610">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="49718-610">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="49718-611">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="49718-611">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="49718-612">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="49718-612">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="49718-613">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="49718-613">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="49718-614">создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="49718-614">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="49718-615">Тип возвращаемого значения `Task<T>` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="49718-615">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="49718-616">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="49718-616">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="49718-617">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="49718-617">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="49718-618">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="49718-618">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="49718-619">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="49718-619">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="49718-620">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="49718-620">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="49718-621">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="49718-621">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="49718-622">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="49718-622">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="49718-623">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="49718-623">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="49718-624">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="49718-624">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="49718-625">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-625">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="49718-626">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="49718-626">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="49718-627">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="49718-627">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="49718-628">Следующий учебник</span><span class="sxs-lookup"><span data-stu-id="49718-628">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="49718-629">На примере веб-приложения Contoso University показано, как создать веб-приложение Razor Pages ASP.NET Core с помощью Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="49718-629">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="49718-630">В этом примере приложения реализуется веб-сайт вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="49718-630">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="49718-631">На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="49718-631">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="49718-632">Это первое руководство из серии, в котором описывается создание примера приложения для университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="49718-632">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="49718-633">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="49718-633">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="49718-634">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="49718-634">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49718-635">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="49718-635">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-636">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-636">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-637">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-637">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="49718-638">Опыт работы с [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="49718-638">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="49718-639">Начинающие программисты должны изучить статью [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к этой серии.</span><span class="sxs-lookup"><span data-stu-id="49718-639">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="49718-640">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="49718-640">Troubleshooting</span></span>

<span data-ttu-id="49718-641">Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="49718-641">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="49718-642">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) в разделе, посвященном [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="49718-642">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="49718-643">Веб-приложение университета Contoso</span><span class="sxs-lookup"><span data-stu-id="49718-643">The Contoso University web app</span></span>

<span data-ttu-id="49718-644">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="49718-644">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="49718-645">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="49718-645">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="49718-646">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="49718-646">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

<span data-ttu-id="49718-649">Стиль пользовательского интерфейса для этого сайта близок к обеспечиваемому встроенными шаблонами.</span><span class="sxs-lookup"><span data-stu-id="49718-649">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="49718-650">Это руководство посвящено использованию EF Core с Razor Pages, а не работе с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="49718-650">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="49718-651">Создание веб-приложения Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="49718-651">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-652">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-652">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49718-653">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="49718-653">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49718-654">Создайте новое веб-приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49718-654">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="49718-655">Назовите проект **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="49718-655">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="49718-656">Очень важно, чтобы проект имел имя *ContosoUniversity*, чтобы совпадали пространства имен при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="49718-656">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="49718-657">Выберите в раскрывающемся списке **ASP.NET Core 2.1**, а затем **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="49718-657">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="49718-658">Изображения для приведенных выше шагов см. в разделе [Создание веб-приложения Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="49718-658">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="49718-659">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="49718-659">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-660">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-660">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="49718-661">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="49718-661">Set up the site style</span></span>

<span data-ttu-id="49718-662">Выполните небольшую настройку меню, макета и домашней страницы сайта.</span><span class="sxs-lookup"><span data-stu-id="49718-662">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="49718-663">Внесите следующие изменения в файл *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="49718-663">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="49718-664">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="49718-664">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="49718-665">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="49718-665">There are three occurrences.</span></span>

* <span data-ttu-id="49718-666">Добавьте пункты меню **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Contact** (Контакты).</span><span class="sxs-lookup"><span data-stu-id="49718-666">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="49718-667">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="49718-667">The changes are highlighted.</span></span> <span data-ttu-id="49718-668">(Вся разметка *не* отображается.)</span><span class="sxs-lookup"><span data-stu-id="49718-668">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="49718-669">Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст о ASP.NET и MVC описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="49718-669">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="49718-670">Создание модели данных</span><span class="sxs-lookup"><span data-stu-id="49718-670">Create the data model</span></span>

<span data-ttu-id="49718-671">Создайте классы сущностей для приложения университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="49718-671">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="49718-672">Начните со следующих трех сущностей:</span><span class="sxs-lookup"><span data-stu-id="49718-672">Start with the following three entities:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="49718-674">Между сущностями `Student` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="49718-674">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="49718-675">Между сущностями `Course` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="49718-675">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="49718-676">Студент может записаться на любое число курсов.</span><span class="sxs-lookup"><span data-stu-id="49718-676">A student can enroll in any number of courses.</span></span> <span data-ttu-id="49718-677">На курс может быть зачислено любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="49718-677">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="49718-678">В следующих разделах создается класс для каждой из этих сущностей.</span><span class="sxs-lookup"><span data-stu-id="49718-678">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="49718-679">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="49718-679">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

<span data-ttu-id="49718-681">Создайте папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="49718-681">Create a *Models* folder.</span></span> <span data-ttu-id="49718-682">В папке *Models* создайте файл класса с именем *Student.cs*, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="49718-682">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="49718-683">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="49718-683">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="49718-684">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="49718-684">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="49718-685">В `classnameID``classname` — это имя класса.</span><span class="sxs-lookup"><span data-stu-id="49718-685">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="49718-686">Альтернативным автоматически распознаваемым первичным ключом является `StudentID` в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="49718-686">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="49718-687">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="49718-687">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="49718-688">Свойства навигации ссылаются на другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="49718-688">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="49718-689">В этом случае свойство `Enrollments` сущности `Student entity` содержит все сущности `Enrollment`, которые связаны с этой сущностью `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-689">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="49718-690">Например, если строка "Student" (Учащийся) в базе данных имеет две связанные строки "Enrollment" (зачисление), свойство навигации `Enrollments` содержит две этих сущности `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="49718-690">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="49718-691">Связанная строка `Enrollment` содержит значение первичного ключа для этого учащегося в столбце `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="49718-691">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="49718-692">Предположим, например, что учащийся с идентификатором 1 имеет две строки в таблице `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="49718-692">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="49718-693">Таблица `Enrollment` содержит две строки с `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="49718-693">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="49718-694">`StudentID` является внешним ключом в таблице `Enrollment`, указывающим учащегося в таблице `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-694">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="49718-695">Если свойство навигации может содержать несколько сущностей, оно должно иметь тип списка, такой как `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="49718-695">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="49718-696">Можно указать `ICollection<T>` либо тип, такой как `List<T>` или `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="49718-696">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="49718-697">Если используется `ICollection<T>`, платформа EF Core по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="49718-697">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="49718-698">Свойства навигации, содержащие несколько сущностей, поступают по связям многие ко многим и один ко многим.</span><span class="sxs-lookup"><span data-stu-id="49718-698">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="49718-699">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="49718-699">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="49718-701">В папке *Models* создайте файл *Enrollment.cs*, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="49718-701">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="49718-702">Свойство `EnrollmentID` является первичным ключом.</span><span class="sxs-lookup"><span data-stu-id="49718-702">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="49718-703">Эта сущность использует шаблон `classnameID` вместо `ID`, как сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-703">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="49718-704">Обычно разработчики выбирают один шаблон и используют его в рамках всей модели данных.</span><span class="sxs-lookup"><span data-stu-id="49718-704">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="49718-705">В одном из следующих руководств показано, за счет чего использование идентификатора без имени класса позволяет упростить реализацию наследования в модели данных.</span><span class="sxs-lookup"><span data-stu-id="49718-705">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="49718-706">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="49718-706">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="49718-707">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade` допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="49718-707">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="49718-708">Оценка со значением null отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="49718-708">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="49718-709">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-709">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="49718-710">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="49718-710">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="49718-711">Сущность `Student` отличается от свойства навигации `Student.Enrollments`, которое содержит несколько сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="49718-711">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="49718-712">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="49718-712">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="49718-713">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="49718-713">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="49718-714">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="49718-714">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="49718-715">Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="49718-715">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="49718-716">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="49718-716">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="49718-717">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="49718-717">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="49718-718">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="49718-718">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="49718-720">В папке *Models* создайте файл *Course.cs*, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="49718-720">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="49718-721">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="49718-721">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="49718-722">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="49718-722">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="49718-723">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="49718-723">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="49718-724">Формирование шаблона для модели Student</span><span class="sxs-lookup"><span data-stu-id="49718-724">Scaffold the student model</span></span>

<span data-ttu-id="49718-725">В этом разделе формируется шаблон для модели Student.</span><span class="sxs-lookup"><span data-stu-id="49718-725">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="49718-726">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели Student.</span><span class="sxs-lookup"><span data-stu-id="49718-726">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="49718-727">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="49718-727">Build the project.</span></span>
* <span data-ttu-id="49718-728">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="49718-728">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49718-729">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-729">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49718-730">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="49718-730">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="49718-731">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="49718-731">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="49718-732">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="49718-732">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="49718-733">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="49718-733">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="49718-734">В строке **Класс контекста данных** щелкните значок плюса ( **+** ) и измените автоматически присвоенное имя на **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="49718-734">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="49718-735">В раскрывающемся списке **Класс контекста данных** выберите **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="49718-735">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="49718-736">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="49718-736">Select **Add**.</span></span>

![Диалоговое окно CRUD](intro/_static/s1.png)

<span data-ttu-id="49718-738">Если на предыдущем шаге у вас возникли проблемы, см. раздел [Создание модели фильма](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="49718-738">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-739">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-739">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49718-740">Выполните следующую команду, чтобы сформировать шаблон для модели Student.</span><span class="sxs-lookup"><span data-stu-id="49718-740">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="49718-741">В процессе формирования шаблонов были созданы и изменены следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="49718-741">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="49718-742">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="49718-742">Files created</span></span>

* <span data-ttu-id="49718-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="49718-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="49718-744">*Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="49718-744">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="49718-745">Обновления файла</span><span class="sxs-lookup"><span data-stu-id="49718-745">File updates</span></span>

* <span data-ttu-id="49718-746">*Startup.cs*: изменения в этом файле подробно описываются в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="49718-746">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="49718-747">*appsettings.json* : добавляется строка подключения, используемая для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="49718-747">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="49718-748">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="49718-748">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="49718-749">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49718-749">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="49718-750">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="49718-750">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="49718-751">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="49718-751">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="49718-752">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="49718-752">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="49718-753">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="49718-753">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="49718-754">Проверьте метод `ConfigureServices` в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="49718-754">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="49718-755">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="49718-755">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="49718-756">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="49718-756">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="49718-757">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="49718-757">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="49718-758">Обновление метода Main</span><span class="sxs-lookup"><span data-stu-id="49718-758">Update main</span></span>

<span data-ttu-id="49718-759">В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее:</span><span class="sxs-lookup"><span data-stu-id="49718-759">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="49718-760">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="49718-760">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="49718-761">Вызовите [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="49718-761">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="49718-762">Высвободите контекст после завершения работы метода `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="49718-762">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="49718-763">В следующем примере кода показан обновленный файл *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="49718-763">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="49718-764">`EnsureCreated` позволяет проверить существование базы данных для контекста.</span><span class="sxs-lookup"><span data-stu-id="49718-764">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="49718-765">Если контекст существует, никаких действий не предпринимается.</span><span class="sxs-lookup"><span data-stu-id="49718-765">If it exists, no action is taken.</span></span> <span data-ttu-id="49718-766">Если контекст не существует, создаются база данных и вся ее схема.</span><span class="sxs-lookup"><span data-stu-id="49718-766">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="49718-767">`EnsureCreated` не использует миграции для создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="49718-767">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="49718-768">Созданную с помощью `EnsureCreated` базу данных впоследствии нельзя обновить, используя миграции.</span><span class="sxs-lookup"><span data-stu-id="49718-768">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="49718-769">`EnsureCreated` вызывается при запуске приложения, что обеспечивает следующий рабочий процесс:</span><span class="sxs-lookup"><span data-stu-id="49718-769">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="49718-770">Удалите базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-770">Delete the DB.</span></span>
* <span data-ttu-id="49718-771">Измените схему базы данных (например, добавьте поле `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="49718-771">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="49718-772">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="49718-772">Run the app.</span></span>
* <span data-ttu-id="49718-773">`EnsureCreated` создает базу данных со столбцом `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="49718-773">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="49718-774">`EnsureCreated` удобно использовать на ранних стадиях разработки, когда схема часто меняется.</span><span class="sxs-lookup"><span data-stu-id="49718-774">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="49718-775">Далее в этом учебнике база данных удаляется и используются миграции.</span><span class="sxs-lookup"><span data-stu-id="49718-775">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="49718-776">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="49718-776">Test the app</span></span>

<span data-ttu-id="49718-777">Запустите приложение и примите политику использования файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="49718-777">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="49718-778">Это приложение не хранит персональные данные.</span><span class="sxs-lookup"><span data-stu-id="49718-778">This app doesn't keep personal information.</span></span> <span data-ttu-id="49718-779">Сведения о политике использования файлов cookie можно прочитать в разделе [Поддержка общего регламента по защите данных ЕС (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="49718-779">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="49718-780">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="49718-780">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="49718-781">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="49718-781">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="49718-782">Проверка контекста базы данных SchoolContext</span><span class="sxs-lookup"><span data-stu-id="49718-782">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="49718-783">Контекст базы данных — это класс main, который координирует функциональные возможности EF Core для заданной модели данных.</span><span class="sxs-lookup"><span data-stu-id="49718-783">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="49718-784">Контекст данных получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="49718-784">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="49718-785">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="49718-785">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="49718-786">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="49718-786">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="49718-787">Обновите файл *SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="49718-787">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="49718-788">Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="49718-788">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="49718-789">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="49718-789">In EF Core terminology:</span></span>

* <span data-ttu-id="49718-790">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="49718-790">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="49718-791">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="49718-791">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="49718-792">`DbSet<Enrollment>` и `DbSet<Course>` можно опустить.</span><span class="sxs-lookup"><span data-stu-id="49718-792">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="49718-793">Платформа EF Core включает эти операторы неявно, так как сущность `Student` ссылается на сущность `Enrollment`, а `Enrollment` ссылается на сущность `Course`.</span><span class="sxs-lookup"><span data-stu-id="49718-793">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="49718-794">В рамках данного руководства оставьте `DbSet<Enrollment>` и `DbSet<Course>` в `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="49718-794">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="49718-795">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="49718-795">SQL Server Express LocalDB</span></span>

<span data-ttu-id="49718-796">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="49718-796">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="49718-797">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="49718-797">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="49718-798">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="49718-798">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="49718-799">По умолчанию LocalDB создает файлы базы данных *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="49718-799">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="49718-800">Добавление кода для инициализации базы данных с использованием тестовых данных</span><span class="sxs-lookup"><span data-stu-id="49718-800">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="49718-801">EF Core создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-801">EF Core creates an empty DB.</span></span> <span data-ttu-id="49718-802">В этом разделе создается метод `Initialize`, предназначенный для заполнения тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="49718-802">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="49718-803">В папке *Data* создайте файл класса с именем *DbInitializer.cs* и добавьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="49718-803">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="49718-804">Примечание. Предыдущий код использует `Models` для пространства имен (`namespace ContosoUniversity.Models`) вместо `Data`.</span><span class="sxs-lookup"><span data-stu-id="49718-804">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="49718-805">`Models` согласуется с кодом, созданным средством формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="49718-805">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="49718-806">См. дополнительные сведения о [проблеме с формированием шаблонов на GitHub ](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="49718-806">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="49718-807">Код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="49718-807">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="49718-808">Если учащихся в базе данных нет, она заполняется тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="49718-808">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="49718-809">Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="49718-809">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="49718-810">Метод `EnsureCreated` автоматически создает базу данных для контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="49718-810">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="49718-811">Если такая база данных существует, `EnsureCreated` возвращает управление без изменения базы данных.</span><span class="sxs-lookup"><span data-stu-id="49718-811">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="49718-812">В файле *Program.cs* измените метод `Main`, чтобы реализовать вызов `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="49718-812">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="49718-813">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49718-813">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49718-814">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="49718-814">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="49718-815">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49718-815">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49718-816">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="49718-816">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="49718-817">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="49718-817">View the DB</span></span>

<span data-ttu-id="49718-818">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="49718-818">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="49718-819">Таким образом, имя базы данных будет SchoolContext-{GUID}.</span><span class="sxs-lookup"><span data-stu-id="49718-819">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="49718-820">GUID будет отличаться для каждого пользователя.</span><span class="sxs-lookup"><span data-stu-id="49718-820">The GUID will be different for each user.</span></span>
<span data-ttu-id="49718-821">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="49718-821">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="49718-822">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="49718-822">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="49718-823">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="49718-823">Expand the **Tables** node.</span></span>

<span data-ttu-id="49718-824">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="49718-824">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="49718-825">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="49718-825">Asynchronous code</span></span>

<span data-ttu-id="49718-826">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="49718-826">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="49718-827">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="49718-827">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="49718-828">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="49718-828">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="49718-829">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="49718-829">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="49718-830">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="49718-830">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="49718-831">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="49718-831">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="49718-832">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="49718-832">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="49718-833">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="49718-833">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="49718-834">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="49718-834">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="49718-835">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="49718-835">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="49718-836">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="49718-836">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="49718-837">автоматически создавать возвращаемый объект [Task](/dotnet/api/system.threading.tasks.task).</span><span class="sxs-lookup"><span data-stu-id="49718-837">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="49718-838">Дополнительные сведения см. в разделе [Тип возвращаемых значений задач](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="49718-838">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="49718-839">Неявный тип возвращаемого значения `Task` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="49718-839">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="49718-840">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="49718-840">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="49718-841">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="49718-841">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="49718-842">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="49718-842">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="49718-843">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="49718-843">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="49718-844">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="49718-844">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="49718-845">Асинхронно выполняются только те операторы, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="49718-845">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="49718-846">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="49718-846">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="49718-847">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="49718-847">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="49718-848">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="49718-848">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="49718-849">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="49718-849">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="49718-850">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="49718-850">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="49718-851">Следующее руководство посвящено базовым операциям CRUD (создание, чтение, обновление, удаление).</span><span class="sxs-lookup"><span data-stu-id="49718-851">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="49718-852">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="49718-852">Additional resources</span></span>

* [<span data-ttu-id="49718-853">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="49718-853">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="49718-854">Вперед</span><span class="sxs-lookup"><span data-stu-id="49718-854">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
