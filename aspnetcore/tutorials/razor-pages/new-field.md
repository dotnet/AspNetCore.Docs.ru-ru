---
title: Часть 7. Добавление нового поля
author: rick-anderson
description: Часть 7 серии руководств по Razor Pages.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101150"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="b4839-103">Часть 7. Добавление нового поля на страницу Razor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4839-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="b4839-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="b4839-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b4839-105">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b4839-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b4839-106">В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="b4839-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="b4839-107">Добавление нового поля в модель.</span><span class="sxs-lookup"><span data-stu-id="b4839-107">Add a new field to the model.</span></span>
* <span data-ttu-id="b4839-108">Перенос изменений в схеме нового поля в базу данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="b4839-109">Если вы используете EF Code First для автоматического создания базы данных, Code First:</span><span class="sxs-lookup"><span data-stu-id="b4839-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="b4839-110">Добавляет в базу данных таблицу [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table), которая позволяет отслеживать синхронизацию схемы базы данных с классами модели, на основе которой она была создана.</span><span class="sxs-lookup"><span data-stu-id="b4839-110">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="b4839-111">Если классы модели не синхронизированы с базой данных, EF выдает исключение.</span><span class="sxs-lookup"><span data-stu-id="b4839-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="b4839-112">Автоматическая проверка синхронизации схемы и модели упрощает поиск несогласованных проблем в коде базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="b4839-113">Добавление свойства Rating в модель Movie</span><span class="sxs-lookup"><span data-stu-id="b4839-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="b4839-114">Откройте файл *Models/Movie.cs* и добавьте свойство `Rating`:</span><span class="sxs-lookup"><span data-stu-id="b4839-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="b4839-115">Построение приложения.</span><span class="sxs-lookup"><span data-stu-id="b4839-115">Build the app.</span></span>

1. <span data-ttu-id="b4839-116">Измените файл *Pages/Movies/Index.cshtml* и добавьте поле `Rating`:</span><span class="sxs-lookup"><span data-stu-id="b4839-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="b4839-117">Обновите следующие страницы:</span><span class="sxs-lookup"><span data-stu-id="b4839-117">Update the following pages:</span></span>
   1. <span data-ttu-id="b4839-118">Добавьте поле `Rating` на страницы "Delete" (Удаление) и "Details" (Сведения).</span><span class="sxs-lookup"><span data-stu-id="b4839-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="b4839-119">Обновите файл [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml), добавив в него поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="b4839-120">Добавьте поле `Rating` на страницу "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="b4839-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="b4839-121">Для работы приложения необходимо обновить базу данных, включив в нее новое поле.</span><span class="sxs-lookup"><span data-stu-id="b4839-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="b4839-122">При запуске приложения без обновления базы данных возникает `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="b4839-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="b4839-123">Исключение `SqlException` связано с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в базе данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="b4839-124">В таблице базы данных нет столбца `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="b4839-125">Устранить эту ошибку можно несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="b4839-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="b4839-126">Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели.</span><span class="sxs-lookup"><span data-stu-id="b4839-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="b4839-127">Этот подход удобен на ранних стадиях цикла разработки. В этом случае развитие модели и схемы базы данных осуществляется одновременно.</span><span class="sxs-lookup"><span data-stu-id="b4839-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="b4839-128">Недостатком такого подхода является потеря существующих данных в базе.</span><span class="sxs-lookup"><span data-stu-id="b4839-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="b4839-129">В рабочей базе данных применять этот подход не следует!</span><span class="sxs-lookup"><span data-stu-id="b4839-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="b4839-130">При разработке приложения часто выполняется удаление базы данных при изменении схемы, для чего используется инициализатор для автоматического заполнения базы тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="b4839-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="b4839-131">Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели.</span><span class="sxs-lookup"><span data-stu-id="b4839-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="b4839-132">Преимущество подхода в том, что он сохраняет данные.</span><span class="sxs-lookup"><span data-stu-id="b4839-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="b4839-133">Внесите это изменение вручную либо путем создания скрипта изменения для базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="b4839-134">Можно обновить схему базы данных с помощью Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="b4839-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="b4839-135">В этом руководстве используется Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="b4839-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="b4839-136">Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца.</span><span class="sxs-lookup"><span data-stu-id="b4839-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="b4839-137">Ниже приведен пример изменения, которое необходимо реализовать для каждого блока `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="b4839-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="b4839-138">См. [готовый файл SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="b4839-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="b4839-139">Постройте решение.</span><span class="sxs-lookup"><span data-stu-id="b4839-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b4839-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4839-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="b4839-141">Добавление миграции для поля Rating</span><span class="sxs-lookup"><span data-stu-id="b4839-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="b4839-142">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="b4839-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="b4839-143">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="b4839-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="b4839-144">Команда `Add-Migration` задает следующие инструкции для платформы:</span><span class="sxs-lookup"><span data-stu-id="b4839-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="b4839-145">Сравните модель `Movie` со схемой базы данных `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b4839-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="b4839-146">Создайте код для переноса схемы базы данных в новую модель.</span><span class="sxs-lookup"><span data-stu-id="b4839-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="b4839-147">В качестве имени файла переноса используется произвольное имя "Rating".</span><span class="sxs-lookup"><span data-stu-id="b4839-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="b4839-148">Рекомендуется присваивать этому файлу понятное имя.</span><span class="sxs-lookup"><span data-stu-id="b4839-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="b4839-149">Команда `Update-Database` указывает платформе, что к базе данных нужно применить изменения схемы, а также сохранить существующие данные.</span><span class="sxs-lookup"><span data-stu-id="b4839-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="b4839-150">Если удалить все записи из базы данных, при инициализации она будет заполнена начальными значениями и в нее будет включено поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="b4839-151">Это можно сделать с помощью ссылок удаления в браузере или из [обозревателя объектов SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b4839-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="b4839-152">Другой вариант — удалить базу данных и использовать миграции для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="b4839-153">Удаление базы данных в SSOX:</span><span class="sxs-lookup"><span data-stu-id="b4839-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="b4839-154">Выберите базу данных в SSOX.</span><span class="sxs-lookup"><span data-stu-id="b4839-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="b4839-155">Щелкните базу данных правой кнопкой мыши и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="b4839-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="b4839-156">Выберите **Закрыть существующие соединения**.</span><span class="sxs-lookup"><span data-stu-id="b4839-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="b4839-157">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="b4839-157">Select **OK**.</span></span>
1. <span data-ttu-id="b4839-158">Обновите базу данных в [PMC](xref:tutorials/razor-pages/new-field#pmc).</span><span class="sxs-lookup"><span data-stu-id="b4839-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b4839-159">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b4839-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="b4839-160">Удаление и повторное создание базы данных</span><span class="sxs-lookup"><span data-stu-id="b4839-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="b4839-161">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="b4839-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="b4839-162">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="b4839-163">Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="b4839-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="b4839-164">Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="b4839-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="b4839-165">Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="b4839-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="b4839-166">Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite.</span><span class="sxs-lookup"><span data-stu-id="b4839-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="b4839-167">Вместо этого при изменении схемы нужно удалить и снова создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="b4839-168">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="b4839-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="b4839-169">Перестроение таблицы включает в себя:</span><span class="sxs-lookup"><span data-stu-id="b4839-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="b4839-170">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="b4839-170">Creating a new table.</span></span>
>* <span data-ttu-id="b4839-171">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="b4839-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="b4839-172">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="b4839-172">Dropping the old table.</span></span>
>* <span data-ttu-id="b4839-173">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="b4839-173">Renaming the new table.</span></span>
>
><span data-ttu-id="b4839-174">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="b4839-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="b4839-175">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="b4839-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="b4839-176">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="b4839-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="b4839-177">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="b4839-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="b4839-178">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="b4839-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="b4839-179">Удалите папку миграции.</span><span class="sxs-lookup"><span data-stu-id="b4839-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="b4839-180">Используйте следующие команды, чтобы заново создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="b4839-181">Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="b4839-182">Если база данных не заполнена начальными значениями, задайте точку останова в методе `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="b4839-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4839-183">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b4839-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b4839-184">[Предыдущая статья. Добавление поиска](xref:tutorials/razor-pages/search)
> [Следующая статья. Добавление проверки](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="b4839-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="b4839-185">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b4839-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b4839-186">В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="b4839-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="b4839-187">Добавление нового поля в модель.</span><span class="sxs-lookup"><span data-stu-id="b4839-187">Add a new field to the model.</span></span>
* <span data-ttu-id="b4839-188">Перенос изменений в схеме нового поля в базу данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="b4839-189">Если вы используете EF Code First для автоматического создания базы данных, Code First:</span><span class="sxs-lookup"><span data-stu-id="b4839-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="b4839-190">Добавляет в базу данных таблицу [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table), которая позволяет отслеживать синхронизацию схемы базы данных с классами модели, на основе которой она была создана.</span><span class="sxs-lookup"><span data-stu-id="b4839-190">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="b4839-191">Если классы модели не синхронизированы с базой данных, EF выдает исключение.</span><span class="sxs-lookup"><span data-stu-id="b4839-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="b4839-192">Автоматическая проверка синхронизации схемы и модели упрощает поиск несогласованных проблем в коде базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="b4839-193">Добавление свойства Rating в модель Movie</span><span class="sxs-lookup"><span data-stu-id="b4839-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="b4839-194">Откройте файл *Models/Movie.cs* и добавьте свойство `Rating`:</span><span class="sxs-lookup"><span data-stu-id="b4839-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="b4839-195">Построение приложения.</span><span class="sxs-lookup"><span data-stu-id="b4839-195">Build the app.</span></span>

1. <span data-ttu-id="b4839-196">Измените файл *Pages/Movies/Index.cshtml* и добавьте поле `Rating`:</span><span class="sxs-lookup"><span data-stu-id="b4839-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="b4839-197">Обновите следующие страницы:</span><span class="sxs-lookup"><span data-stu-id="b4839-197">Update the following pages:</span></span>
   1. <span data-ttu-id="b4839-198">Добавьте поле `Rating` на страницы "Delete" (Удаление) и "Details" (Сведения).</span><span class="sxs-lookup"><span data-stu-id="b4839-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="b4839-199">Обновите файл [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml), добавив в него поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="b4839-200">Добавьте поле `Rating` на страницу "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="b4839-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="b4839-201">Для работы приложения необходимо обновить базу данных, включив в нее новое поле.</span><span class="sxs-lookup"><span data-stu-id="b4839-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="b4839-202">При запуске приложения без обновления базы данных возникает `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="b4839-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="b4839-203">Исключение `SqlException` связано с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в базе данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="b4839-204">В таблице базы данных нет столбца `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="b4839-205">Устранить эту ошибку можно несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="b4839-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="b4839-206">Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели.</span><span class="sxs-lookup"><span data-stu-id="b4839-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="b4839-207">Этот подход удобен на ранних стадиях цикла разработки. В этом случае развитие модели и схемы базы данных осуществляется одновременно.</span><span class="sxs-lookup"><span data-stu-id="b4839-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="b4839-208">Недостатком такого подхода является потеря существующих данных в базе.</span><span class="sxs-lookup"><span data-stu-id="b4839-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="b4839-209">В рабочей базе данных применять этот подход не следует!</span><span class="sxs-lookup"><span data-stu-id="b4839-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="b4839-210">При разработке приложения часто выполняется удаление базы данных при изменении схемы, для чего используется инициализатор для автоматического заполнения базы тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="b4839-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="b4839-211">Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели.</span><span class="sxs-lookup"><span data-stu-id="b4839-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="b4839-212">Преимущество подхода в том, что он сохраняет данные.</span><span class="sxs-lookup"><span data-stu-id="b4839-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="b4839-213">Внесите это изменение вручную либо путем создания скрипта изменения для базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="b4839-214">Можно обновить схему базы данных с помощью Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="b4839-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="b4839-215">В этом руководстве используется Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="b4839-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="b4839-216">Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца.</span><span class="sxs-lookup"><span data-stu-id="b4839-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="b4839-217">Ниже приведен пример изменения, которое необходимо реализовать для каждого блока `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="b4839-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="b4839-218">См. [готовый файл SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="b4839-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="b4839-219">Постройте решение.</span><span class="sxs-lookup"><span data-stu-id="b4839-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b4839-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4839-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="b4839-221">Добавление миграции для поля Rating</span><span class="sxs-lookup"><span data-stu-id="b4839-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="b4839-222">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="b4839-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="b4839-223">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="b4839-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="b4839-224">Команда `Add-Migration` задает следующие инструкции для платформы:</span><span class="sxs-lookup"><span data-stu-id="b4839-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="b4839-225">Сравните модель `Movie` со схемой базы данных `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b4839-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="b4839-226">Создайте код для переноса схемы базы данных в новую модель.</span><span class="sxs-lookup"><span data-stu-id="b4839-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="b4839-227">В качестве имени файла переноса используется произвольное имя "Rating".</span><span class="sxs-lookup"><span data-stu-id="b4839-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="b4839-228">Рекомендуется присваивать этому файлу понятное имя.</span><span class="sxs-lookup"><span data-stu-id="b4839-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="b4839-229">Команда `Update-Database` указывает платформе, что к базе данных нужно применить изменения схемы, а также сохранить существующие данные.</span><span class="sxs-lookup"><span data-stu-id="b4839-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="b4839-230">Если удалить все записи из базы данных, при инициализации она будет заполнена начальными значениями и в нее будет включено поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="b4839-231">Это можно сделать с помощью ссылок удаления в браузере или из [обозревателя объектов SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b4839-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="b4839-232">Другой вариант — удалить базу данных и использовать миграции для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="b4839-233">Удаление базы данных в SSOX:</span><span class="sxs-lookup"><span data-stu-id="b4839-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="b4839-234">Выберите базу данных в SSOX.</span><span class="sxs-lookup"><span data-stu-id="b4839-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="b4839-235">Щелкните базу данных правой кнопкой мыши и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="b4839-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="b4839-236">Выберите **Закрыть существующие соединения**.</span><span class="sxs-lookup"><span data-stu-id="b4839-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="b4839-237">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="b4839-237">Select **OK**.</span></span>
* <span data-ttu-id="b4839-238">Обновите базу данных в [PMC](xref:tutorials/razor-pages/new-field#pmc).</span><span class="sxs-lookup"><span data-stu-id="b4839-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b4839-239">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b4839-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="b4839-240">Удаление и повторное создание базы данных</span><span class="sxs-lookup"><span data-stu-id="b4839-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="b4839-241">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="b4839-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="b4839-242">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="b4839-243">Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="b4839-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="b4839-244">Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="b4839-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="b4839-245">Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="b4839-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="b4839-246">Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite.</span><span class="sxs-lookup"><span data-stu-id="b4839-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="b4839-247">Вместо этого при изменении схемы нужно удалить и снова создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="b4839-248">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="b4839-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="b4839-249">Перестроение таблицы включает в себя:</span><span class="sxs-lookup"><span data-stu-id="b4839-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="b4839-250">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="b4839-250">Creating a new table.</span></span>
>* <span data-ttu-id="b4839-251">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="b4839-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="b4839-252">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="b4839-252">Dropping the old table.</span></span>
>* <span data-ttu-id="b4839-253">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="b4839-253">Renaming the new table.</span></span>
>
><span data-ttu-id="b4839-254">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="b4839-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="b4839-255">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="b4839-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="b4839-256">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="b4839-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="b4839-257">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="b4839-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="b4839-258">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="b4839-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="b4839-259">Удалите папку миграции.</span><span class="sxs-lookup"><span data-stu-id="b4839-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="b4839-260">Используйте следующие команды, чтобы заново создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="b4839-261">Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="b4839-262">Если база данных не заполнена начальными значениями, задайте точку останова в методе `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="b4839-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4839-263">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b4839-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b4839-264">[Предыдущая статья. Добавление поиска](xref:tutorials/razor-pages/search)
> [Следующая статья. Добавление проверки](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="b4839-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b4839-265">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b4839-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b4839-266">В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="b4839-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="b4839-267">Добавление нового поля в модель.</span><span class="sxs-lookup"><span data-stu-id="b4839-267">Add a new field to the model.</span></span>
* <span data-ttu-id="b4839-268">Перенос изменений в схеме нового поля в базу данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="b4839-269">Если вы используете EF Code First для автоматического создания базы данных, Code First:</span><span class="sxs-lookup"><span data-stu-id="b4839-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="b4839-270">Добавляет в базу данных таблицу [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table), которая позволяет отслеживать синхронизацию схемы базы данных с классами модели, на основе которой она была создана.</span><span class="sxs-lookup"><span data-stu-id="b4839-270">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="b4839-271">Если классы модели не синхронизированы с базой данных, EF выдает исключение.</span><span class="sxs-lookup"><span data-stu-id="b4839-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="b4839-272">Автоматическая проверка синхронизации схемы и модели упрощает поиск несогласованных проблем в коде базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="b4839-273">Добавление свойства Rating в модель Movie</span><span class="sxs-lookup"><span data-stu-id="b4839-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="b4839-274">Откройте файл *Models/Movie.cs* и добавьте свойство `Rating`:</span><span class="sxs-lookup"><span data-stu-id="b4839-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="b4839-275">Построение приложения.</span><span class="sxs-lookup"><span data-stu-id="b4839-275">Build the app.</span></span>

<span data-ttu-id="b4839-276">Измените файл *Pages/Movies/Index.cshtml* и добавьте поле `Rating`:</span><span class="sxs-lookup"><span data-stu-id="b4839-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="b4839-277">Обновите следующие страницы:</span><span class="sxs-lookup"><span data-stu-id="b4839-277">Update the following pages:</span></span>

* <span data-ttu-id="b4839-278">Добавьте поле `Rating` на страницы "Delete" (Удаление) и "Details" (Сведения).</span><span class="sxs-lookup"><span data-stu-id="b4839-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="b4839-279">Обновите файл [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml), добавив в него поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="b4839-280">Добавьте поле `Rating` на страницу "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="b4839-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="b4839-281">Для работы приложения необходимо обновить базу данных, включив в нее новое поле.</span><span class="sxs-lookup"><span data-stu-id="b4839-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="b4839-282">Если приложение выполняется, возникнет исключение `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="b4839-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="b4839-283">Эта ошибка связана с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в базе данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="b4839-284">В таблице базы данных нет столбца `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="b4839-285">Устранить эту ошибку можно несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="b4839-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="b4839-286">Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели.</span><span class="sxs-lookup"><span data-stu-id="b4839-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="b4839-287">Этот подход удобен на ранних стадиях цикла разработки. В этом случае развитие модели и схемы базы данных осуществляется одновременно.</span><span class="sxs-lookup"><span data-stu-id="b4839-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="b4839-288">Недостатком такого подхода является потеря существующих данных в базе.</span><span class="sxs-lookup"><span data-stu-id="b4839-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="b4839-289">В рабочей базе данных применять этот подход не следует!</span><span class="sxs-lookup"><span data-stu-id="b4839-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="b4839-290">При разработке приложения часто выполняется удаление базы данных при изменении схемы, для чего используется инициализатор для автоматического заполнения базы тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="b4839-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="b4839-291">Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели.</span><span class="sxs-lookup"><span data-stu-id="b4839-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="b4839-292">Преимущество подхода в том, что он сохраняет данные.</span><span class="sxs-lookup"><span data-stu-id="b4839-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="b4839-293">Внесите это изменение вручную либо путем создания скрипта изменения для базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="b4839-294">Можно обновить схему базы данных с помощью Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="b4839-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="b4839-295">В этом руководстве используется Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="b4839-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="b4839-296">Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца.</span><span class="sxs-lookup"><span data-stu-id="b4839-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="b4839-297">Ниже приведен пример изменения, которое необходимо реализовать для каждого блока `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="b4839-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="b4839-298">См. [готовый файл SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="b4839-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="b4839-299">Постройте решение.</span><span class="sxs-lookup"><span data-stu-id="b4839-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b4839-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4839-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="b4839-301">Добавление миграции для поля Rating</span><span class="sxs-lookup"><span data-stu-id="b4839-301">Add a migration for the rating field</span></span>

<span data-ttu-id="b4839-302">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="b4839-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="b4839-303">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="b4839-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="b4839-304">Команда `Add-Migration` задает следующие инструкции для платформы:</span><span class="sxs-lookup"><span data-stu-id="b4839-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="b4839-305">Сравните модель `Movie` со схемой базы данных `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b4839-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="b4839-306">Создайте код для переноса схемы базы данных в новую модель.</span><span class="sxs-lookup"><span data-stu-id="b4839-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="b4839-307">В качестве имени файла переноса используется произвольное имя "Rating".</span><span class="sxs-lookup"><span data-stu-id="b4839-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="b4839-308">Рекомендуется присваивать этому файлу понятное имя.</span><span class="sxs-lookup"><span data-stu-id="b4839-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="b4839-309">Команда `Update-Database` указывает платформе, что нужно применить изменения схемы к базе данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="b4839-310">Если удалить все записи из базы данных, при инициализации она будет заполнена начальными значениями и в нее будет включено поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="b4839-311">Это можно сделать с помощью ссылок удаления в браузере или из [обозревателя объектов SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b4839-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="b4839-312">Другой вариант — удалить базу данных и использовать миграции для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="b4839-313">Удаление базы данных в SSOX:</span><span class="sxs-lookup"><span data-stu-id="b4839-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="b4839-314">Выберите базу данных в SSOX.</span><span class="sxs-lookup"><span data-stu-id="b4839-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="b4839-315">Щелкните базу данных правой кнопкой мыши и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="b4839-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="b4839-316">Выберите **Закрыть существующие соединения**.</span><span class="sxs-lookup"><span data-stu-id="b4839-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="b4839-317">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="b4839-317">Select **OK**.</span></span>
* <span data-ttu-id="b4839-318">Обновите базу данных в [PMC](xref:tutorials/razor-pages/new-field#pmc).</span><span class="sxs-lookup"><span data-stu-id="b4839-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b4839-319">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b4839-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="b4839-320">Удаление и повторное создание базы данных</span><span class="sxs-lookup"><span data-stu-id="b4839-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="b4839-321">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="b4839-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="b4839-322">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="b4839-323">Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="b4839-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="b4839-324">Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="b4839-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="b4839-325">Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="b4839-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="b4839-326">Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite.</span><span class="sxs-lookup"><span data-stu-id="b4839-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="b4839-327">Вместо этого при изменении схемы нужно удалить и снова создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="b4839-328">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="b4839-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="b4839-329">Перестроение таблицы включает в себя:</span><span class="sxs-lookup"><span data-stu-id="b4839-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="b4839-330">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="b4839-330">Creating a new table.</span></span>
>* <span data-ttu-id="b4839-331">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="b4839-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="b4839-332">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="b4839-332">Dropping the old table.</span></span>
>* <span data-ttu-id="b4839-333">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="b4839-333">Renaming the new table.</span></span>
>
><span data-ttu-id="b4839-334">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="b4839-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="b4839-335">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="b4839-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="b4839-336">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="b4839-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="b4839-337">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="b4839-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="b4839-338">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="b4839-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="b4839-339">Удалите базу данных и используйте миграции для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="b4839-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="b4839-340">Чтобы удалить базу данных, удалите файл базы данных (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="b4839-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="b4839-341">Затем выполните команду `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="b4839-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="b4839-342">Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`.</span><span class="sxs-lookup"><span data-stu-id="b4839-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="b4839-343">Если база данных не заполнена начальными значениями, задайте точку останова в методе `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="b4839-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4839-344">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b4839-344">Additional resources</span></span>

* [<span data-ttu-id="b4839-345">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="b4839-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="b4839-346">[Предыдущая статья. Добавление поиска](xref:tutorials/razor-pages/search)
> [Следующая статья. Добавление проверки](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="b4839-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end