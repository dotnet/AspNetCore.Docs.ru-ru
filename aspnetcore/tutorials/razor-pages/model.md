---
title: Часть 2. Добавление модели
author: rick-anderson
description: Часть 2 серии руководств по Razor Pages. В этом разделе содержатся сведения о добавлении классов моделей.
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 92bfda330399b43871b3ae0e6b609726f7ad4a91
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564044"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="e2ce8-104">Часть 2. Добавление модели в приложение Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2ce8-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="e2ce8-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="e2ce8-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e2ce8-106">В этом разделе добавляются классы для управления фильмами в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="e2ce8-107">Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="e2ce8-108">EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="e2ce8-109">Вы напишете классы моделей, а затем EF Core создаст базу данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="e2ce8-110">Эти классы моделей называются классами POCO (от "**P** lain-**O** ld **C** LR **O** bjects" — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="e2ce8-111">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e2ce8-112">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e2ce8-113">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e2ce8-115">В **Обозревателе решений** щелкните правой кнопкой мыши проект *RazorPagesMovie* и выберите пункт **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e2ce8-116">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="e2ce8-117">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="e2ce8-118">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="e2ce8-119">Присвойте классу имя *Movie*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="e2ce8-120">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e2ce8-121">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="e2ce8-122">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e2ce8-123">`[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e2ce8-124">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-124">With this attribute:</span></span>

  * <span data-ttu-id="e2ce8-125">Пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e2ce8-126">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2ce8-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2ce8-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e2ce8-128">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="e2ce8-129">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e2ce8-130">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e2ce8-131">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="e2ce8-132">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e2ce8-133">`[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e2ce8-134">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-134">With this attribute:</span></span>

  * <span data-ttu-id="e2ce8-135">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e2ce8-136">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e2ce8-137">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="e2ce8-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e2ce8-138">Добавление класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-138">Add a database context class</span></span>

1. <span data-ttu-id="e2ce8-139">В проекте *RazorPagesMovie* создайте папку с именем *Data*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="e2ce8-140">В папке *Data* добавьте файл с именем *RazorPagesMovieContext.cs* с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="e2ce8-141">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e2ce8-142">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e2ce8-143">Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e2ce8-144">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-144">Add a database connection string</span></span>

<span data-ttu-id="e2ce8-145">Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e2ce8-146">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-146">Register the database context</span></span>

1. <span data-ttu-id="e2ce8-147">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="e2ce8-148">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-149">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e2ce8-150">В **окне инструментов решения** щелкните проект *RazorPagesMovie*, а затем выберите **Add**(Добавить)> **Создать папку...** . Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="e2ce8-151">Щелкните папку *Models* и выберите пункт **Add** (Добавить)>**Создать файл...** .</span><span class="sxs-lookup"><span data-stu-id="e2ce8-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="e2ce8-152">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="e2ce8-153">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="e2ce8-154">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="e2ce8-155">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="e2ce8-156">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e2ce8-157">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="e2ce8-158">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e2ce8-159">`[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e2ce8-160">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-160">With this attribute:</span></span>

  * <span data-ttu-id="e2ce8-161">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e2ce8-162">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="e2ce8-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="e2ce8-164">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e2ce8-165">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="e2ce8-165">Scaffold the movie model</span></span>

<span data-ttu-id="e2ce8-166">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e2ce8-167">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e2ce8-169">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="e2ce8-170">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="e2ce8-171">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="e2ce8-172">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/5/sca.png)

1. <span data-ttu-id="e2ce8-174">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

1. <span data-ttu-id="e2ce8-176">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e2ce8-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="e2ce8-177">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e2ce8-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="e2ce8-178">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="e2ce8-179">В диалоговом окне **Добавление контекста данных** будет сгенерировано имя класса `RazorPagesMovie.Data.RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-179">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="e2ce8-180">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-180">Select **Add**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

<span data-ttu-id="e2ce8-182">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2ce8-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2ce8-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e2ce8-184">Откройте в командной оболочке папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="e2ce8-185">**Для Windows**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e2ce8-186">**Для macOS и Linux**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e2ce8-187">В следующей таблице представлены параметры генератора кода ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="e2ce8-188">Параметр</span><span class="sxs-lookup"><span data-stu-id="e2ce8-188">Option</span></span>               | <span data-ttu-id="e2ce8-189">Описание:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e2ce8-190">Имя модели</span><span class="sxs-lookup"><span data-stu-id="e2ce8-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e2ce8-191">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e2ce8-192">Использование макета по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e2ce8-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e2ce8-193">Относительный путь к папке выходных данных для создания представлений</span><span class="sxs-lookup"><span data-stu-id="e2ce8-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e2ce8-194">Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания</span><span class="sxs-lookup"><span data-stu-id="e2ce8-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e2ce8-195">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e2ce8-196">См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e2ce8-197">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="e2ce8-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e2ce8-198">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e2ce8-199">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="e2ce8-200">`IWebHostEnvironment` внедрен, поэтому приложение может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-201">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e2ce8-202">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="e2ce8-203">Щелкните папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="e2ce8-204">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="e2ce8-205">Щелкните папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolding...** (Создать шаблон...).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

1. <span data-ttu-id="e2ce8-207">В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="e2ce8-209">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e2ce8-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="e2ce8-210">В строке **Класс DbContext для использования** присвойте классу имя `RazorPagesMovie.Data.RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-210">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="e2ce8-211">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-211">Select **Finish**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/5/arpMac.png)

<span data-ttu-id="e2ce8-213">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e2ce8-214">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="e2ce8-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e2ce8-215">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e2ce8-216">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="e2ce8-217">`IWebHostEnvironment` внедрен, поэтому приложение может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="e2ce8-218">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="e2ce8-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2ce8-220">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e2ce8-221">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e2ce8-222">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e2ce8-223">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="e2ce8-223">Updated</span></span>

* <span data-ttu-id="e2ce8-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-224">*Startup.cs*</span></span>

<span data-ttu-id="e2ce8-225">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2ce8-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2ce8-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e2ce8-227">В процессе формирования шаблонов создаются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e2ce8-228">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e2ce8-229">В следующем разделе приводится описание созданных файлов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-230">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2ce8-231">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e2ce8-232">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e2ce8-233">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e2ce8-234">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="e2ce8-234">Updated</span></span>

* <span data-ttu-id="e2ce8-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-235">*Startup.cs*</span></span>

<span data-ttu-id="e2ce8-236">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="e2ce8-237">Создание начальной схемы базы данных с помощью функции миграции EF</span><span class="sxs-lookup"><span data-stu-id="e2ce8-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="e2ce8-238">Функция миграции в Entity Framework Core предоставляет следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="e2ce8-239">Создание начальной схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-239">Create the initial database schema.</span></span>
* <span data-ttu-id="e2ce8-240">Поэтапное обновление схемы базы данных, чтобы она соответствовала модели данных приложения.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="e2ce8-241">Существующие данные в базе данных сохраняются.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2ce8-243">В этом разделе окно **Консоль диспетчера пакетов** (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="e2ce8-244">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-244">Add an initial migration.</span></span>
* <span data-ttu-id="e2ce8-245">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="e2ce8-246">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Меню PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="e2ce8-248">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-249">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="e2ce8-250">Выполните следующие команды интерфейса командной строки .NET:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="e2ce8-251">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e2ce8-252">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e2ce8-253">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="e2ce8-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e2ce8-254">Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="e2ce8-255">Команда `migrations` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e2ce8-256">Схема создается на основе модели, указанной в `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e2ce8-257">Аргумент `InitialCreate` используется для присвоения имен миграциям.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e2ce8-258">Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e2ce8-259">Команда `update` выполняет метод `Up` в миграциях, которые не были применены.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e2ce8-260">В этом случае `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e2ce8-262">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="e2ce8-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e2ce8-263">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e2ce8-264">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e2ce8-265">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e2ce8-266">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e2ce8-267">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e2ce8-268">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e2ce8-269">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e2ce8-270">`RazorPagesMovieContext` координирует функции EF Core, такие как Create (создание), Read (чтение), Update (обновление) и Delete (удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="e2ce8-271">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e2ce8-272">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e2ce8-273">Представленный выше код создает свойство [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="e2ce8-274">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e2ce8-275">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e2ce8-276">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e2ce8-277">При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e2ce8-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-278">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e2ce8-279">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="e2ce8-280">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="e2ce8-280">Test the app</span></span>

1. <span data-ttu-id="e2ce8-281">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="e2ce8-282">Если вы получаете следующую ошибку:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="e2ce8-283">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="e2ce8-284">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-284">Test the **Create** link.</span></span>

   ![Страница "Создать"](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="e2ce8-286">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e2ce8-287">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e2ce8-288">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="e2ce8-289">Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e2ce8-290">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2ce8-291">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e2ce8-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e2ce8-292">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e2ce8-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e2ce8-293">В этом разделе добавляются классы для управления фильмами.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="e2ce8-294">Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="e2ce8-295">EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="e2ce8-296">Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e2ce8-297">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e2ce8-298">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e2ce8-299">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2ce8-301">Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e2ce8-302">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-302">Name the folder *Models*.</span></span>

<span data-ttu-id="e2ce8-303">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="e2ce8-304">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="e2ce8-305">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-305">Name the class **Movie**.</span></span>

<span data-ttu-id="e2ce8-306">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e2ce8-307">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="e2ce8-308">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e2ce8-309">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e2ce8-310">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-310">With this attribute:</span></span>

  * <span data-ttu-id="e2ce8-311">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e2ce8-312">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e2ce8-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2ce8-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2ce8-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e2ce8-315">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e2ce8-316">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e2ce8-317">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e2ce8-318">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="e2ce8-319">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e2ce8-320">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e2ce8-321">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-321">With this attribute:</span></span>

  * <span data-ttu-id="e2ce8-322">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e2ce8-323">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e2ce8-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e2ce8-325">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="e2ce8-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e2ce8-326">Добавление класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-326">Add a database context class</span></span>

* <span data-ttu-id="e2ce8-327">В проекте *RazorPagesMovie* создайте папку с именем *Data*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="e2ce8-328">Добавьте следующий класс `RazorPagesMovieContext` в папку *Data*:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e2ce8-329">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e2ce8-330">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e2ce8-331">Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e2ce8-332">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-332">Add a database connection string</span></span>

<span data-ttu-id="e2ce8-333">Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e2ce8-334">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-334">Register the database context</span></span>

<span data-ttu-id="e2ce8-335">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e2ce8-336">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-337">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e2ce8-338">В **окне инструментов решения** щелкните проект **RazorPagesMovie**, а затем выберите **Add**(Добавить)> **Создать папку...** . Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="e2ce8-339">Щелкните папку *Models* правой кнопкой мыши и выберите пункт **Добавить**>**Новый файл...**</span><span class="sxs-lookup"><span data-stu-id="e2ce8-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="e2ce8-340">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e2ce8-341">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e2ce8-342">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e2ce8-343">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="e2ce8-344">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e2ce8-345">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="e2ce8-346">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e2ce8-347">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e2ce8-348">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-348">With this attribute:</span></span>

  * <span data-ttu-id="e2ce8-349">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e2ce8-350">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="e2ce8-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="e2ce8-352">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e2ce8-353">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="e2ce8-353">Scaffold the movie model</span></span>

<span data-ttu-id="e2ce8-354">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e2ce8-355">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2ce8-357">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e2ce8-358">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e2ce8-359">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="e2ce8-360">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/sca.png)

<span data-ttu-id="e2ce8-362">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

<span data-ttu-id="e2ce8-364">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e2ce8-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e2ce8-365">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e2ce8-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e2ce8-366">В строке **Класс контекста данных** щелкните значок плюса **+** и измените сгенерированное имя RazorPagesMovie.**Models**.RazorPagesMovieContext на RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e2ce8-367">[Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e2ce8-368">Оно создает класс контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e2ce8-369">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-369">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

<span data-ttu-id="e2ce8-371">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2ce8-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2ce8-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e2ce8-373">Откройте в командном окне папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="e2ce8-374">**Для Windows**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e2ce8-375">**Для macOS и Linux**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e2ce8-376">В следующей таблице представлены параметры генератора кода ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="e2ce8-377">Параметр</span><span class="sxs-lookup"><span data-stu-id="e2ce8-377">Option</span></span>               | <span data-ttu-id="e2ce8-378">Описание:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e2ce8-379">Имя модели</span><span class="sxs-lookup"><span data-stu-id="e2ce8-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e2ce8-380">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e2ce8-381">Использование макета по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e2ce8-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e2ce8-382">Относительный путь к папке выходных данных для создания представлений</span><span class="sxs-lookup"><span data-stu-id="e2ce8-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e2ce8-383">Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания</span><span class="sxs-lookup"><span data-stu-id="e2ce8-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e2ce8-384">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e2ce8-385">См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e2ce8-386">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="e2ce8-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e2ce8-387">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e2ce8-388">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="e2ce8-389">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-390">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2ce8-391">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e2ce8-392">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e2ce8-393">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="e2ce8-394">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolding...** (Создать шаблон...).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

<span data-ttu-id="e2ce8-396">В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e2ce8-398">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e2ce8-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e2ce8-399">В раскрывающемся списке **Класс модели** выберите или введите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e2ce8-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e2ce8-400">В строке **Класс контекста данных** введите имя нового класса RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e2ce8-401">[Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e2ce8-402">Оно создает класс контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e2ce8-403">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-403">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

<span data-ttu-id="e2ce8-405">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="e2ce8-406">Добавление средств EF</span><span class="sxs-lookup"><span data-stu-id="e2ce8-406">Add EF tools</span></span>

<span data-ttu-id="e2ce8-407">Выполните следующую команду .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="e2ce8-408">Приведенная выше команда добавляет средства Entity Framework Core для .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="e2ce8-409">Дополнительные сведения см. в статье [Справочник по средствам Entity Framework Core в .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e2ce8-410">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="e2ce8-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e2ce8-411">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e2ce8-412">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="e2ce8-413">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="e2ce8-414">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="e2ce8-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2ce8-416">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e2ce8-417">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e2ce8-418">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e2ce8-419">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="e2ce8-419">Updated</span></span>

* <span data-ttu-id="e2ce8-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-420">*Startup.cs*</span></span>

<span data-ttu-id="e2ce8-421">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-422">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2ce8-423">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e2ce8-424">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e2ce8-425">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e2ce8-426">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="e2ce8-426">Updated</span></span>

* <span data-ttu-id="e2ce8-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-427">*Startup.cs*</span></span>

<span data-ttu-id="e2ce8-428">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2ce8-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2ce8-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e2ce8-430">В процессе формирования шаблонов создаются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e2ce8-431">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e2ce8-432">В следующем разделе приводится описание созданных файлов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e2ce8-433">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="e2ce8-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2ce8-435">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e2ce8-436">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-436">Add an initial migration.</span></span>
* <span data-ttu-id="e2ce8-437">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="e2ce8-438">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e2ce8-440">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-441">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="e2ce8-442">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="e2ce8-443">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e2ce8-444">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e2ce8-445">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="e2ce8-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e2ce8-446">Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="e2ce8-447">Команда миграции формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="e2ce8-448">Схема создается на основе модели, указанной в `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e2ce8-449">Аргумент `InitialCreate` используется для присвоения имен миграциям.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e2ce8-450">Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e2ce8-451">Команда `update` выполняет метод `Up` в миграциях, которые не были применены.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e2ce8-452">В этом случае `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e2ce8-454">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="e2ce8-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e2ce8-455">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e2ce8-456">С помощью внедрения зависимостей службы, например контекст базы данных EF Core, регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e2ce8-457">Затем компоненты, которые используют эти службы, например Razor Pages, обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="e2ce8-458">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e2ce8-459">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e2ce8-460">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e2ce8-461">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e2ce8-462">`RazorPagesMovieContext` координирует функции EF Core, такие как Create (создание), Read (чтение), Update (обновление) и Delete (удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="e2ce8-463">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e2ce8-464">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e2ce8-465">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e2ce8-466">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e2ce8-467">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e2ce8-468">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e2ce8-469">При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e2ce8-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-470">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e2ce8-471">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e2ce8-472">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="e2ce8-472">Test the app</span></span>

* <span data-ttu-id="e2ce8-473">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e2ce8-474">Если возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e2ce8-475">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e2ce8-476">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-476">Test the **Create** link.</span></span>

  ![Страница "Создать"](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="e2ce8-478">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e2ce8-479">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e2ce8-480">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e2ce8-481">Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e2ce8-482">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2ce8-483">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e2ce8-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e2ce8-484">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e2ce8-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e2ce8-485">В этом разделе добавляются классы для управления фильмами в кроссплатформенной [базе данных SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e2ce8-486">Приложения, созданные на основе шаблона ASP.NET Core, используют базу данных SQLite.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e2ce8-487">Эти классы этой модели приложений используются в [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e2ce8-488">EF Core —это платформа объектно-реляционного сопоставления (ORM), которая упрощает получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e2ce8-489">Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e2ce8-490">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e2ce8-491">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e2ce8-492">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2ce8-494">Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e2ce8-495">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-495">Name the folder *Models*.</span></span>

<span data-ttu-id="e2ce8-496">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="e2ce8-497">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="e2ce8-498">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-498">Name the class **Movie**.</span></span>

<span data-ttu-id="e2ce8-499">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e2ce8-500">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="e2ce8-501">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e2ce8-502">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e2ce8-503">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-503">With this attribute:</span></span>

  * <span data-ttu-id="e2ce8-504">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e2ce8-505">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e2ce8-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2ce8-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2ce8-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e2ce8-508">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e2ce8-509">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e2ce8-510">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e2ce8-511">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="e2ce8-512">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e2ce8-513">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e2ce8-514">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-514">With this attribute:</span></span>

  * <span data-ttu-id="e2ce8-515">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e2ce8-516">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e2ce8-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e2ce8-518">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="e2ce8-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e2ce8-519">Добавление класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-519">Add a database context class</span></span>

<span data-ttu-id="e2ce8-520">В проекте RazorPagesMovie создайте папку с именем *Data*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="e2ce8-521">Добавьте следующий класс `RazorPagesMovieContext` в папку *Data*:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e2ce8-522">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e2ce8-523">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e2ce8-524">Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e2ce8-525">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-525">Add a database connection string</span></span>

<span data-ttu-id="e2ce8-526">Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="e2ce8-527">Добавьте необходимые пакеты NuGet</span><span class="sxs-lookup"><span data-stu-id="e2ce8-527">Add required NuGet packages</span></span>

<span data-ttu-id="e2ce8-528">Выполните следующую команду .NET Core CLI, чтобы добавить в проект SQLite и CodeGeneration.Design:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="e2ce8-529">Пакет `Microsoft.VisualStudio.Web.CodeGeneration.Design` необходим для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e2ce8-530">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e2ce8-530">Register the database context</span></span>

<span data-ttu-id="e2ce8-531">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e2ce8-532">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="e2ce8-533">Соберите проект как проверку на ошибки.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-534">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e2ce8-535">В **окне инструментов решения** щелкните проект *RazorPagesMovie*, а затем выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e2ce8-536">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="e2ce8-537">Щелкните папку *Models* и выберите пункт **Add** (Добавить) > **Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e2ce8-538">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e2ce8-539">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e2ce8-540">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e2ce8-541">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="e2ce8-542">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e2ce8-543">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="e2ce8-544">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e2ce8-545">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e2ce8-546">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-546">With this attribute:</span></span>

  * <span data-ttu-id="e2ce8-547">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e2ce8-548">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e2ce8-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="e2ce8-550">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e2ce8-551">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="e2ce8-551">Scaffold the movie model</span></span>

<span data-ttu-id="e2ce8-552">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e2ce8-553">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2ce8-555">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e2ce8-556">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e2ce8-557">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="e2ce8-558">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/sca.png)

<span data-ttu-id="e2ce8-560">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

<span data-ttu-id="e2ce8-562">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e2ce8-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="e2ce8-563">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e2ce8-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e2ce8-564">В строке **Класс контекста данных** нажмите на значок плюса **+** и примите сгенерированное имя **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e2ce8-565">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-565">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arp.png)

<span data-ttu-id="e2ce8-567">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2ce8-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2ce8-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e2ce8-569">Откройте в командном окне папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="e2ce8-570">**Для Windows**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e2ce8-571">**Для macOS и Linux**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e2ce8-572">В следующей таблице представлены параметры генератора кода ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="e2ce8-573">Параметр</span><span class="sxs-lookup"><span data-stu-id="e2ce8-573">Option</span></span>               | <span data-ttu-id="e2ce8-574">Описание:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e2ce8-575">Имя модели</span><span class="sxs-lookup"><span data-stu-id="e2ce8-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e2ce8-576">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e2ce8-577">Использование макета по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e2ce8-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e2ce8-578">Относительный путь к папке выходных данных для создания представлений</span><span class="sxs-lookup"><span data-stu-id="e2ce8-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e2ce8-579">Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания</span><span class="sxs-lookup"><span data-stu-id="e2ce8-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e2ce8-580">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e2ce8-581">См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-582">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2ce8-583">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e2ce8-584">Щелкните папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e2ce8-585">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="e2ce8-586">Щелкните папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

<span data-ttu-id="e2ce8-588">В диалоговом окне **Add New Scaffolding** (Добавление нового шаблона) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e2ce8-590">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e2ce8-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e2ce8-591">В раскрывающемся списке **Класс модели** выберите или введите **Фильм**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="e2ce8-592">В строке **Data context class** (Класс контекста данных) введите или выберите **RazorPagesMovieContext**. Это приведет к созданию класса контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="e2ce8-593">В этом случае он будет **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e2ce8-594">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-594">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

<span data-ttu-id="e2ce8-596">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="e2ce8-597">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e2ce8-598">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="e2ce8-598">Files created</span></span>

* <span data-ttu-id="e2ce8-599">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e2ce8-600">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="e2ce8-601">Обновляемые файлы</span><span class="sxs-lookup"><span data-stu-id="e2ce8-601">File updated</span></span>

* <span data-ttu-id="e2ce8-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e2ce8-602">*Startup.cs*</span></span>

<span data-ttu-id="e2ce8-603">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e2ce8-604">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="e2ce8-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2ce8-606">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e2ce8-607">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-607">Add an initial migration.</span></span>
* <span data-ttu-id="e2ce8-608">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="e2ce8-609">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e2ce8-611">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="e2ce8-612">Команда `Add-Migration` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e2ce8-613">Схема создается на основе модели, указанной в `DbContext`в файле *RazorPagesMovieContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="e2ce8-614">Аргумент `InitialCreate` используется для присвоения имен миграции.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="e2ce8-615">Можно использовать любое имя, однако по соглашению используется имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="e2ce8-616">Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="e2ce8-617">Команда `Update-Database` выполняет метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e2ce8-618">Метод `Up` создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-619">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="e2ce8-620">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="e2ce8-621">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e2ce8-622">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e2ce8-623">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="e2ce8-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e2ce8-624">Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ce8-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ce8-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e2ce8-626">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="e2ce8-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e2ce8-627">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e2ce8-628">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e2ce8-629">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e2ce8-630">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e2ce8-631">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e2ce8-632">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e2ce8-633">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="e2ce8-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="e2ce8-634">`RazorPagesMovieContext` координирует функции EF Core, такие как Create (создание), Read (чтение), Update (обновление) и Delete (удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="e2ce8-635">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e2ce8-636">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e2ce8-637">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e2ce8-638">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e2ce8-639">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e2ce8-640">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e2ce8-641">При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e2ce8-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e2ce8-642">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e2ce8-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e2ce8-643">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e2ce8-644">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="e2ce8-644">Test the app</span></span>

* <span data-ttu-id="e2ce8-645">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e2ce8-646">Если возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e2ce8-647">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e2ce8-648">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-648">Test the **Create** link.</span></span>

  ![Страница "Создать"](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e2ce8-650">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e2ce8-651">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e2ce8-652">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e2ce8-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e2ce8-653">Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e2ce8-654">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="e2ce8-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2ce8-655">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e2ce8-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e2ce8-656">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e2ce8-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
