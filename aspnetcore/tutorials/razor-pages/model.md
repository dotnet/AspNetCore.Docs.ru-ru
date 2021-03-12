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
ms.openlocfilehash: 3677cd6fe5c2ff901a17c9dccdc749d8eb2709f2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588670"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="2a53c-104">Часть 2. Добавление модели в приложение Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a53c-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="2a53c-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="2a53c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="2a53c-106">В этом разделе добавляются классы для управления фильмами в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="2a53c-107">Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="2a53c-108">EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="2a53c-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="2a53c-109">Вы напишете классы моделей, а затем EF Core создаст базу данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="2a53c-110">Эти классы моделей называются классами POCO (от "**P** lain-**O** ld **C** LR **O** bjects" — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="2a53c-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="2a53c-111">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="2a53c-112">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2a53c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="2a53c-113">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2a53c-115">В **Обозревателе решений** щелкните правой кнопкой мыши проект *RazorPagesMovie* и выберите пункт **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="2a53c-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2a53c-116">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="2a53c-117">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="2a53c-118">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="2a53c-119">Присвойте классу имя *Movie*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="2a53c-120">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="2a53c-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2a53c-121">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="2a53c-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="2a53c-122">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2a53c-123">`[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2a53c-124">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="2a53c-124">With this attribute:</span></span>

  * <span data-ttu-id="2a53c-125">Пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="2a53c-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2a53c-126">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="2a53c-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a53c-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a53c-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2a53c-128">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="2a53c-129">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="2a53c-130">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="2a53c-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2a53c-131">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="2a53c-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="2a53c-132">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2a53c-133">`[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2a53c-134">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="2a53c-134">With this attribute:</span></span>

  * <span data-ttu-id="2a53c-135">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="2a53c-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2a53c-136">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="2a53c-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="2a53c-137">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="2a53c-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="2a53c-138">Добавление класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-138">Add a database context class</span></span>

1. <span data-ttu-id="2a53c-139">В проекте *RazorPagesMovie* создайте папку с именем *Data*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="2a53c-140">В папке *Data* добавьте файл с именем *RazorPagesMovieContext.cs* с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="2a53c-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="2a53c-141">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="2a53c-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="2a53c-142">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="2a53c-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="2a53c-143">Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.</span><span class="sxs-lookup"><span data-stu-id="2a53c-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="2a53c-144">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-144">Add a database connection string</span></span>

<span data-ttu-id="2a53c-145">Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="2a53c-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2a53c-146">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-146">Register the database context</span></span>

1. <span data-ttu-id="2a53c-147">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="2a53c-148">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2a53c-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-149">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2a53c-150">В **окне инструментов решения** щелкните проект *RazorPagesMovie*, а затем выберите **Add**(Добавить)> **Создать папку...** . Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="2a53c-151">Щелкните папку *Models* и выберите пункт **Add** (Добавить)>**Создать файл...** .</span><span class="sxs-lookup"><span data-stu-id="2a53c-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="2a53c-152">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2a53c-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="2a53c-153">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="2a53c-154">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="2a53c-155">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="2a53c-156">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="2a53c-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2a53c-157">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="2a53c-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="2a53c-158">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2a53c-159">`[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2a53c-160">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="2a53c-160">With this attribute:</span></span>

  * <span data-ttu-id="2a53c-161">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="2a53c-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2a53c-162">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="2a53c-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="2a53c-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="2a53c-164">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2a53c-165">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="2a53c-165">Scaffold the movie model</span></span>

<span data-ttu-id="2a53c-166">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="2a53c-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2a53c-167">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="2a53c-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2a53c-169">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="2a53c-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="2a53c-170">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="2a53c-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="2a53c-171">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="2a53c-172">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="2a53c-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/5/sca.png)

1. <span data-ttu-id="2a53c-174">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

1. <span data-ttu-id="2a53c-176">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2a53c-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="2a53c-177">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="2a53c-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="2a53c-178">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="2a53c-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="2a53c-179">В диалоговом окне **Добавление контекста данных** будет сгенерировано имя класса `RazorPagesMovie.Data.RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-179">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="2a53c-180">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-180">Select **Add**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

<span data-ttu-id="2a53c-182">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a53c-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a53c-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2a53c-184">Откройте в командной оболочке папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="2a53c-185">**Для Windows**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2a53c-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2a53c-186">**Для macOS и Linux**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2a53c-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="2a53c-187">В следующей таблице представлены параметры генератора кода ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2a53c-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="2a53c-188">Параметр</span><span class="sxs-lookup"><span data-stu-id="2a53c-188">Option</span></span>               | <span data-ttu-id="2a53c-189">Описание:</span><span class="sxs-lookup"><span data-stu-id="2a53c-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="2a53c-190">Имя модели</span><span class="sxs-lookup"><span data-stu-id="2a53c-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="2a53c-191">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="2a53c-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="2a53c-192">Использование макета по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2a53c-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="2a53c-193">Относительный путь к папке выходных данных для создания представлений</span><span class="sxs-lookup"><span data-stu-id="2a53c-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="2a53c-194">Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания</span><span class="sxs-lookup"><span data-stu-id="2a53c-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="2a53c-195">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:</span><span class="sxs-lookup"><span data-stu-id="2a53c-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="2a53c-196">См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="2a53c-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="2a53c-197">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="2a53c-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="2a53c-198">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="2a53c-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="2a53c-199">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="2a53c-200">`IWebHostEnvironment` внедрен, поэтому приложение может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="2a53c-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-201">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2a53c-202">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="2a53c-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="2a53c-203">Щелкните папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="2a53c-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="2a53c-204">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="2a53c-205">Щелкните папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolding...** (Создать шаблон...).</span><span class="sxs-lookup"><span data-stu-id="2a53c-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

1. <span data-ttu-id="2a53c-207">В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="2a53c-209">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2a53c-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="2a53c-210">В строке **Класс DbContext для использования** присвойте классу имя `RazorPagesMovie.Data.RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-210">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="2a53c-211">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-211">Select **Finish**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/5/arpMac.png)

<span data-ttu-id="2a53c-213">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="2a53c-214">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="2a53c-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="2a53c-215">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="2a53c-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="2a53c-216">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="2a53c-217">`IWebHostEnvironment` внедрен, поэтому приложение может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="2a53c-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="2a53c-218">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="2a53c-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a53c-220">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="2a53c-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2a53c-221">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="2a53c-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2a53c-222">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2a53c-223">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="2a53c-223">Updated</span></span>

* <span data-ttu-id="2a53c-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-224">*Startup.cs*</span></span>

<span data-ttu-id="2a53c-225">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a53c-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a53c-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a53c-227">В процессе формирования шаблонов создаются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="2a53c-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="2a53c-228">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="2a53c-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="2a53c-229">В следующем разделе приводится описание созданных файлов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-230">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a53c-231">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="2a53c-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2a53c-232">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="2a53c-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2a53c-233">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2a53c-234">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="2a53c-234">Updated</span></span>

* <span data-ttu-id="2a53c-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-235">*Startup.cs*</span></span>

<span data-ttu-id="2a53c-236">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="2a53c-237">Создание начальной схемы базы данных с помощью функции миграции EF</span><span class="sxs-lookup"><span data-stu-id="2a53c-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="2a53c-238">Функция миграции в Entity Framework Core предоставляет следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="2a53c-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="2a53c-239">Создание начальной схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-239">Create the initial database schema.</span></span>
* <span data-ttu-id="2a53c-240">Поэтапное обновление схемы базы данных, чтобы она соответствовала модели данных приложения.</span><span class="sxs-lookup"><span data-stu-id="2a53c-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="2a53c-241">Существующие данные в базе данных сохраняются.</span><span class="sxs-lookup"><span data-stu-id="2a53c-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a53c-243">В этом разделе окно **Консоль диспетчера пакетов** (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="2a53c-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="2a53c-244">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-244">Add an initial migration.</span></span>
* <span data-ttu-id="2a53c-245">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="2a53c-246">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Меню PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="2a53c-248">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2a53c-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-249">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="2a53c-250">Выполните следующие команды интерфейса командной строки .NET:</span><span class="sxs-lookup"><span data-stu-id="2a53c-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="2a53c-251">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="2a53c-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2a53c-252">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2a53c-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2a53c-253">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="2a53c-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2a53c-254">Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="2a53c-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="2a53c-255">Команда `migrations` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2a53c-256">Схема создается на основе модели, указанной в `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="2a53c-257">Аргумент `InitialCreate` используется для присвоения имен миграциям.</span><span class="sxs-lookup"><span data-stu-id="2a53c-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="2a53c-258">Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="2a53c-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="2a53c-259">Команда `update` выполняет метод `Up` в миграциях, которые не были применены.</span><span class="sxs-lookup"><span data-stu-id="2a53c-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="2a53c-260">В этом случае `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2a53c-262">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="2a53c-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2a53c-263">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2a53c-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2a53c-264">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="2a53c-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2a53c-265">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="2a53c-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2a53c-266">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2a53c-267">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="2a53c-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2a53c-268">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2a53c-269">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="2a53c-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2a53c-270">`RazorPagesMovieContext` координирует функции EF Core, такие как Create (создание), Read (чтение), Update (обновление) и Delete (удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="2a53c-271">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="2a53c-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="2a53c-272">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2a53c-273">Представленный выше код создает свойство [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="2a53c-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="2a53c-274">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2a53c-275">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="2a53c-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2a53c-276">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="2a53c-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="2a53c-277">При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2a53c-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-278">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2a53c-279">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="2a53c-280">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="2a53c-280">Test the app</span></span>

1. <span data-ttu-id="2a53c-281">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="2a53c-282">Если вы получаете следующую ошибку:</span><span class="sxs-lookup"><span data-stu-id="2a53c-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="2a53c-283">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2a53c-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="2a53c-284">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-284">Test the **Create** link.</span></span>

   ![Страница "Создать"](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="2a53c-286">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="2a53c-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2a53c-287">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2a53c-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2a53c-288">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="2a53c-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="2a53c-289">Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2a53c-290">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a53c-291">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2a53c-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2a53c-292">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2a53c-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="2a53c-293">В этом разделе добавляются классы для управления фильмами.</span><span class="sxs-lookup"><span data-stu-id="2a53c-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="2a53c-294">Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="2a53c-295">EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="2a53c-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="2a53c-296">Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="2a53c-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2a53c-297">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="2a53c-298">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2a53c-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="2a53c-299">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a53c-301">Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2a53c-302">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-302">Name the folder *Models*.</span></span>

<span data-ttu-id="2a53c-303">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="2a53c-304">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="2a53c-305">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-305">Name the class **Movie**.</span></span>

<span data-ttu-id="2a53c-306">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="2a53c-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2a53c-307">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="2a53c-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="2a53c-308">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2a53c-309">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2a53c-310">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="2a53c-310">With this attribute:</span></span>

  * <span data-ttu-id="2a53c-311">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="2a53c-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2a53c-312">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="2a53c-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2a53c-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a53c-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a53c-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a53c-315">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2a53c-316">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="2a53c-317">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="2a53c-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2a53c-318">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="2a53c-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="2a53c-319">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2a53c-320">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2a53c-321">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="2a53c-321">With this attribute:</span></span>

  * <span data-ttu-id="2a53c-322">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="2a53c-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2a53c-323">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="2a53c-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2a53c-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="2a53c-325">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="2a53c-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="2a53c-326">Добавление класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-326">Add a database context class</span></span>

* <span data-ttu-id="2a53c-327">В проекте *RazorPagesMovie* создайте папку с именем *Data*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="2a53c-328">Добавьте следующий класс `RazorPagesMovieContext` в папку *Data*:</span><span class="sxs-lookup"><span data-stu-id="2a53c-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2a53c-329">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="2a53c-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="2a53c-330">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="2a53c-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="2a53c-331">Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.</span><span class="sxs-lookup"><span data-stu-id="2a53c-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="2a53c-332">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-332">Add a database connection string</span></span>

<span data-ttu-id="2a53c-333">Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="2a53c-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2a53c-334">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-334">Register the database context</span></span>

<span data-ttu-id="2a53c-335">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="2a53c-336">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-337">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a53c-338">В **окне инструментов решения** щелкните проект **RazorPagesMovie**, а затем выберите **Add**(Добавить)> **Создать папку...** . Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="2a53c-339">Щелкните папку *Models* правой кнопкой мыши и выберите пункт **Добавить**>**Новый файл...**</span><span class="sxs-lookup"><span data-stu-id="2a53c-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="2a53c-340">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2a53c-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2a53c-341">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2a53c-342">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2a53c-343">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="2a53c-344">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="2a53c-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2a53c-345">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="2a53c-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="2a53c-346">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2a53c-347">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2a53c-348">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="2a53c-348">With this attribute:</span></span>

  * <span data-ttu-id="2a53c-349">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="2a53c-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2a53c-350">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="2a53c-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="2a53c-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="2a53c-352">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2a53c-353">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="2a53c-353">Scaffold the movie model</span></span>

<span data-ttu-id="2a53c-354">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="2a53c-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2a53c-355">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="2a53c-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a53c-357">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="2a53c-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2a53c-358">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="2a53c-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2a53c-359">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="2a53c-360">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="2a53c-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/sca.png)

<span data-ttu-id="2a53c-362">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

<span data-ttu-id="2a53c-364">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2a53c-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2a53c-365">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="2a53c-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2a53c-366">В строке **Класс контекста данных** щелкните значок плюса **+** и измените сгенерированное имя RazorPagesMovie.**Models**.RazorPagesMovieContext на RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="2a53c-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2a53c-367">[Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется.</span><span class="sxs-lookup"><span data-stu-id="2a53c-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2a53c-368">Оно создает класс контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="2a53c-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2a53c-369">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-369">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

<span data-ttu-id="2a53c-371">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a53c-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a53c-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2a53c-373">Откройте в командном окне папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="2a53c-374">**Для Windows**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2a53c-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2a53c-375">**Для macOS и Linux**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2a53c-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="2a53c-376">В следующей таблице представлены параметры генератора кода ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2a53c-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="2a53c-377">Параметр</span><span class="sxs-lookup"><span data-stu-id="2a53c-377">Option</span></span>               | <span data-ttu-id="2a53c-378">Описание:</span><span class="sxs-lookup"><span data-stu-id="2a53c-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="2a53c-379">Имя модели</span><span class="sxs-lookup"><span data-stu-id="2a53c-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="2a53c-380">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="2a53c-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="2a53c-381">Использование макета по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2a53c-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="2a53c-382">Относительный путь к папке выходных данных для создания представлений</span><span class="sxs-lookup"><span data-stu-id="2a53c-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="2a53c-383">Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания</span><span class="sxs-lookup"><span data-stu-id="2a53c-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="2a53c-384">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:</span><span class="sxs-lookup"><span data-stu-id="2a53c-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="2a53c-385">См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="2a53c-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="2a53c-386">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="2a53c-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="2a53c-387">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="2a53c-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="2a53c-388">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="2a53c-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="2a53c-389">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="2a53c-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-390">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a53c-391">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="2a53c-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2a53c-392">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="2a53c-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2a53c-393">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="2a53c-394">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolding...** (Создать шаблон...).</span><span class="sxs-lookup"><span data-stu-id="2a53c-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

<span data-ttu-id="2a53c-396">В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2a53c-398">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2a53c-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2a53c-399">В раскрывающемся списке **Класс модели** выберите или введите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="2a53c-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2a53c-400">В строке **Класс контекста данных** введите имя нового класса RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="2a53c-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2a53c-401">[Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется.</span><span class="sxs-lookup"><span data-stu-id="2a53c-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2a53c-402">Оно создает класс контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="2a53c-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2a53c-403">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-403">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

<span data-ttu-id="2a53c-405">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="2a53c-406">Добавление средств EF</span><span class="sxs-lookup"><span data-stu-id="2a53c-406">Add EF tools</span></span>

<span data-ttu-id="2a53c-407">Выполните следующую команду .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="2a53c-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="2a53c-408">Приведенная выше команда добавляет средства Entity Framework Core для .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="2a53c-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="2a53c-409">Дополнительные сведения см. в статье [Справочник по средствам Entity Framework Core в .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="2a53c-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="2a53c-410">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="2a53c-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="2a53c-411">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="2a53c-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="2a53c-412">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="2a53c-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="2a53c-413">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="2a53c-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="2a53c-414">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="2a53c-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a53c-416">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="2a53c-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2a53c-417">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="2a53c-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2a53c-418">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2a53c-419">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="2a53c-419">Updated</span></span>

* <span data-ttu-id="2a53c-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-420">*Startup.cs*</span></span>

<span data-ttu-id="2a53c-421">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-422">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a53c-423">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="2a53c-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2a53c-424">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="2a53c-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2a53c-425">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2a53c-426">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="2a53c-426">Updated</span></span>

* <span data-ttu-id="2a53c-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-427">*Startup.cs*</span></span>

<span data-ttu-id="2a53c-428">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a53c-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a53c-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a53c-430">В процессе формирования шаблонов создаются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="2a53c-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="2a53c-431">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="2a53c-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="2a53c-432">В следующем разделе приводится описание созданных файлов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2a53c-433">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="2a53c-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a53c-435">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="2a53c-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2a53c-436">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-436">Add an initial migration.</span></span>
* <span data-ttu-id="2a53c-437">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="2a53c-438">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2a53c-440">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2a53c-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-441">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="2a53c-442">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="2a53c-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="2a53c-443">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="2a53c-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2a53c-444">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2a53c-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2a53c-445">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="2a53c-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2a53c-446">Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="2a53c-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="2a53c-447">Команда миграции формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="2a53c-448">Схема создается на основе модели, указанной в `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="2a53c-449">Аргумент `InitialCreate` используется для присвоения имен миграциям.</span><span class="sxs-lookup"><span data-stu-id="2a53c-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="2a53c-450">Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="2a53c-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="2a53c-451">Команда `update` выполняет метод `Up` в миграциях, которые не были применены.</span><span class="sxs-lookup"><span data-stu-id="2a53c-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="2a53c-452">В этом случае `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2a53c-454">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="2a53c-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2a53c-455">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2a53c-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2a53c-456">С помощью внедрения зависимостей службы, например контекст базы данных EF Core, регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="2a53c-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2a53c-457">Затем компоненты, которые используют эти службы, например Razor Pages, обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="2a53c-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="2a53c-458">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2a53c-459">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="2a53c-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2a53c-460">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2a53c-461">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="2a53c-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2a53c-462">`RazorPagesMovieContext` координирует функции EF Core, такие как Create (создание), Read (чтение), Update (обновление) и Delete (удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="2a53c-463">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="2a53c-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="2a53c-464">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2a53c-465">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="2a53c-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2a53c-466">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2a53c-467">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="2a53c-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2a53c-468">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="2a53c-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="2a53c-469">При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2a53c-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-470">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2a53c-471">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2a53c-472">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="2a53c-472">Test the app</span></span>

* <span data-ttu-id="2a53c-473">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2a53c-474">Если возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="2a53c-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2a53c-475">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2a53c-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2a53c-476">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-476">Test the **Create** link.</span></span>

  ![Страница "Создать"](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="2a53c-478">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="2a53c-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2a53c-479">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2a53c-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2a53c-480">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="2a53c-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2a53c-481">Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2a53c-482">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a53c-483">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2a53c-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2a53c-484">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2a53c-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a53c-485">В этом разделе добавляются классы для управления фильмами в кроссплатформенной [базе данных SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2a53c-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="2a53c-486">Приложения, созданные на основе шаблона ASP.NET Core, используют базу данных SQLite.</span><span class="sxs-lookup"><span data-stu-id="2a53c-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="2a53c-487">Эти классы этой модели приложений используются в [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="2a53c-488">EF Core —это платформа объектно-реляционного сопоставления (ORM), которая упрощает получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="2a53c-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="2a53c-489">Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="2a53c-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2a53c-490">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="2a53c-491">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2a53c-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="2a53c-492">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a53c-494">Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2a53c-495">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-495">Name the folder *Models*.</span></span>

<span data-ttu-id="2a53c-496">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="2a53c-497">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="2a53c-498">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-498">Name the class **Movie**.</span></span>

<span data-ttu-id="2a53c-499">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="2a53c-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2a53c-500">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="2a53c-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="2a53c-501">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2a53c-502">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2a53c-503">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="2a53c-503">With this attribute:</span></span>

  * <span data-ttu-id="2a53c-504">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="2a53c-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2a53c-505">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="2a53c-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2a53c-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a53c-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a53c-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a53c-508">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2a53c-509">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="2a53c-510">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="2a53c-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2a53c-511">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="2a53c-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="2a53c-512">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2a53c-513">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2a53c-514">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="2a53c-514">With this attribute:</span></span>

  * <span data-ttu-id="2a53c-515">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="2a53c-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2a53c-516">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="2a53c-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2a53c-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="2a53c-518">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="2a53c-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="2a53c-519">Добавление класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-519">Add a database context class</span></span>

<span data-ttu-id="2a53c-520">В проекте RazorPagesMovie создайте папку с именем *Data*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="2a53c-521">Добавьте следующий класс `RazorPagesMovieContext` в папку *Data*:</span><span class="sxs-lookup"><span data-stu-id="2a53c-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2a53c-522">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="2a53c-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="2a53c-523">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="2a53c-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="2a53c-524">Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.</span><span class="sxs-lookup"><span data-stu-id="2a53c-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="2a53c-525">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-525">Add a database connection string</span></span>

<span data-ttu-id="2a53c-526">Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="2a53c-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="2a53c-527">Добавьте необходимые пакеты NuGet</span><span class="sxs-lookup"><span data-stu-id="2a53c-527">Add required NuGet packages</span></span>

<span data-ttu-id="2a53c-528">Выполните следующую команду .NET Core CLI, чтобы добавить в проект SQLite и CodeGeneration.Design:</span><span class="sxs-lookup"><span data-stu-id="2a53c-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="2a53c-529">Пакет `Microsoft.VisualStudio.Web.CodeGeneration.Design` необходим для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2a53c-530">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="2a53c-530">Register the database context</span></span>

<span data-ttu-id="2a53c-531">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="2a53c-532">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="2a53c-533">Соберите проект как проверку на ошибки.</span><span class="sxs-lookup"><span data-stu-id="2a53c-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-534">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a53c-535">В **окне инструментов решения** щелкните проект *RazorPagesMovie*, а затем выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="2a53c-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="2a53c-536">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="2a53c-537">Щелкните папку *Models* и выберите пункт **Add** (Добавить) > **Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="2a53c-538">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2a53c-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2a53c-539">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2a53c-540">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2a53c-541">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="2a53c-542">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="2a53c-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2a53c-543">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="2a53c-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="2a53c-544">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2a53c-545">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2a53c-546">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="2a53c-546">With this attribute:</span></span>

  * <span data-ttu-id="2a53c-547">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="2a53c-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2a53c-548">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="2a53c-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2a53c-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="2a53c-550">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2a53c-551">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="2a53c-551">Scaffold the movie model</span></span>

<span data-ttu-id="2a53c-552">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="2a53c-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2a53c-553">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="2a53c-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a53c-555">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="2a53c-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2a53c-556">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="2a53c-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2a53c-557">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="2a53c-558">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="2a53c-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/sca.png)

<span data-ttu-id="2a53c-560">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

<span data-ttu-id="2a53c-562">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2a53c-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="2a53c-563">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="2a53c-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2a53c-564">В строке **Класс контекста данных** нажмите на значок плюса **+** и примите сгенерированное имя **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2a53c-565">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-565">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arp.png)

<span data-ttu-id="2a53c-567">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a53c-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a53c-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2a53c-569">Откройте в командном окне папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="2a53c-570">**Для Windows**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2a53c-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2a53c-571">**Для macOS и Linux**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2a53c-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="2a53c-572">В следующей таблице представлены параметры генератора кода ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2a53c-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="2a53c-573">Параметр</span><span class="sxs-lookup"><span data-stu-id="2a53c-573">Option</span></span>               | <span data-ttu-id="2a53c-574">Описание:</span><span class="sxs-lookup"><span data-stu-id="2a53c-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="2a53c-575">Имя модели</span><span class="sxs-lookup"><span data-stu-id="2a53c-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="2a53c-576">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="2a53c-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="2a53c-577">Использование макета по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2a53c-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="2a53c-578">Относительный путь к папке выходных данных для создания представлений</span><span class="sxs-lookup"><span data-stu-id="2a53c-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="2a53c-579">Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания</span><span class="sxs-lookup"><span data-stu-id="2a53c-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="2a53c-580">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:</span><span class="sxs-lookup"><span data-stu-id="2a53c-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="2a53c-581">См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="2a53c-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-582">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a53c-583">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="2a53c-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2a53c-584">Щелкните папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="2a53c-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2a53c-585">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="2a53c-586">Щелкните папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="2a53c-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

<span data-ttu-id="2a53c-588">В диалоговом окне **Add New Scaffolding** (Добавление нового шаблона) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2a53c-590">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2a53c-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2a53c-591">В раскрывающемся списке **Класс модели** выберите или введите **Фильм**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="2a53c-592">В строке **Data context class** (Класс контекста данных) введите или выберите **RazorPagesMovieContext**. Это приведет к созданию класса контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="2a53c-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="2a53c-593">В этом случае он будет **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2a53c-594">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-594">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

<span data-ttu-id="2a53c-596">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="2a53c-597">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="2a53c-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2a53c-598">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="2a53c-598">Files created</span></span>

* <span data-ttu-id="2a53c-599">*Pages/Movies*: Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="2a53c-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2a53c-600">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="2a53c-601">Обновляемые файлы</span><span class="sxs-lookup"><span data-stu-id="2a53c-601">File updated</span></span>

* <span data-ttu-id="2a53c-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2a53c-602">*Startup.cs*</span></span>

<span data-ttu-id="2a53c-603">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2a53c-604">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="2a53c-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a53c-606">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="2a53c-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2a53c-607">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-607">Add an initial migration.</span></span>
* <span data-ttu-id="2a53c-608">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="2a53c-609">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2a53c-611">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2a53c-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="2a53c-612">Команда `Add-Migration` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2a53c-613">Схема создается на основе модели, указанной в `DbContext`в файле *RazorPagesMovieContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="2a53c-614">Аргумент `InitialCreate` используется для присвоения имен миграции.</span><span class="sxs-lookup"><span data-stu-id="2a53c-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="2a53c-615">Можно использовать любое имя, однако по соглашению используется имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="2a53c-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="2a53c-616">Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="2a53c-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="2a53c-617">Команда `Update-Database` выполняет метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="2a53c-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2a53c-618">Метод `Up` создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-619">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="2a53c-620">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="2a53c-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="2a53c-621">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="2a53c-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2a53c-622">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2a53c-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2a53c-623">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="2a53c-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2a53c-624">Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="2a53c-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a53c-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a53c-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2a53c-626">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="2a53c-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2a53c-627">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2a53c-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2a53c-628">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="2a53c-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2a53c-629">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="2a53c-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2a53c-630">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="2a53c-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2a53c-631">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="2a53c-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2a53c-632">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2a53c-633">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="2a53c-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="2a53c-634">`RazorPagesMovieContext` координирует функции EF Core, такие как Create (создание), Read (чтение), Update (обновление) и Delete (удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="2a53c-635">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="2a53c-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="2a53c-636">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2a53c-637">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="2a53c-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2a53c-638">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="2a53c-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2a53c-639">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="2a53c-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2a53c-640">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="2a53c-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="2a53c-641">При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2a53c-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a53c-642">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2a53c-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2a53c-643">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="2a53c-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2a53c-644">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="2a53c-644">Test the app</span></span>

* <span data-ttu-id="2a53c-645">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="2a53c-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2a53c-646">Если возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="2a53c-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2a53c-647">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2a53c-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2a53c-648">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-648">Test the **Create** link.</span></span>

  ![Страница "Создать"](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2a53c-650">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="2a53c-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2a53c-651">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2a53c-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2a53c-652">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="2a53c-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2a53c-653">Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="2a53c-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2a53c-654">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="2a53c-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a53c-655">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2a53c-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2a53c-656">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2a53c-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
