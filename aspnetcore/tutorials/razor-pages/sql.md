---
title: Часть 4. Работа с базой данных
author: rick-anderson
description: Часть 4 серии руководств по Razor Pages.
ms.author: riande
ms.date: 01/05/2021
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3133ff7a192f5e3bdec4f6f1e6e6d55f966d7e64
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589768"
---
# <a name="part-4-of-tutorial-series-on-razor-pages"></a><span data-ttu-id="44590-103">Часть 4 серии руководств по Razor Pages</span><span class="sxs-lookup"><span data-stu-id="44590-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="44590-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Джо Одетт](https://twitter.com/joeaudette) (Joe Audette)</span><span class="sxs-lookup"><span data-stu-id="44590-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="44590-105">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="44590-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="44590-106">Объект `RazorPagesMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="44590-107">Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="44590-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44590-109">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="44590-110">Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает ключ `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="44590-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="44590-111">Для разработки на локальном уровне конфигурация получает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="44590-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44590-113">Созданная строка подключения выглядит аналогично следующему коду JSON.</span><span class="sxs-lookup"><span data-stu-id="44590-113">The generated connection string is similar to the following JSON:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44590-114">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="44590-115">Если приложение развертывается на тестовом или рабочем сервере, можно задать строку подключения к тестовому или рабочему серверу базы данных с помощью переменной среды.</span><span class="sxs-lookup"><span data-stu-id="44590-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="44590-116">Дополнительные сведения см. в разделе [Конфигурация](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="44590-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="44590-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="44590-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="44590-119">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ.</span><span class="sxs-lookup"><span data-stu-id="44590-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="44590-120">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="44590-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="44590-121">По умолчанию база данных LocalDB создает файлы `*.mdf` в каталоге `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="44590-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="44590-122">В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="44590-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Меню "Вид"](sql/_static/5/ssox.png)

1. <span data-ttu-id="44590-124">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**:</span><span class="sxs-lookup"><span data-stu-id="44590-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Контекстные меню, открытые на таблице Movie](sql/_static/5/design.png)

   ![Таблицы Movie, открытые в конструкторе](sql/_static/dv.png)

   <span data-ttu-id="44590-127">Обратите внимание на значок с изображением ключа рядом с `ID`.</span><span class="sxs-lookup"><span data-stu-id="44590-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="44590-128">По умолчанию EF создает свойство с именем `ID` для первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="44590-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="44590-129">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотреть данные**:</span><span class="sxs-lookup"><span data-stu-id="44590-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![Открытая таблица Movie с отображением данных](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44590-131">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="44590-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="44590-132">SQLite</span></span>

<span data-ttu-id="44590-133">На веб-сайте [SQLite](https://www.sqlite.org/) указывается следующее:</span><span class="sxs-lookup"><span data-stu-id="44590-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="44590-134">SQLite — это автономная внедряемая полнофункциональная общедоступная система управления базами данных SQL с высокой степенью надежности.</span><span class="sxs-lookup"><span data-stu-id="44590-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="44590-135">На данный момент SQLite является самой популярной СУБД в мире.</span><span class="sxs-lookup"><span data-stu-id="44590-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="44590-136">Для просмотра базы данных SQLite, а также управления ей можно использовать множество самых разных сторонних инструментов.</span><span class="sxs-lookup"><span data-stu-id="44590-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="44590-137">На следующем рисунке показан [DB Browser для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="44590-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="44590-138">Если вы предпочитаете использовать другое средство для работы с SQLite, напишите нам в комментариях, чем именно оно нравится вам.</span><span class="sxs-lookup"><span data-stu-id="44590-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![DB Browser для SQLite с базой данных Movie](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="44590-140">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="44590-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="44590-141">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="44590-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="44590-142">Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="44590-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="44590-143">Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="44590-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="44590-144">Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="44590-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="44590-145">Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite.</span><span class="sxs-lookup"><span data-stu-id="44590-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="44590-146">Вместо этого при изменении схемы база данных удаляется и создается повторно.</span><span class="sxs-lookup"><span data-stu-id="44590-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="44590-147">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="44590-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="44590-148">Перестроение таблицы включает в себя:</span><span class="sxs-lookup"><span data-stu-id="44590-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="44590-149">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="44590-149">Creating a new table.</span></span>
>* <span data-ttu-id="44590-150">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="44590-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="44590-151">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="44590-151">Dropping the old table.</span></span>
>* <span data-ttu-id="44590-152">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="44590-152">Renaming the new table.</span></span>
>
><span data-ttu-id="44590-153">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="44590-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="44590-154">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="44590-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="44590-155">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="44590-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="44590-156">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="44590-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="44590-157">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="44590-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="44590-158">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="44590-158">Seed the database</span></span>

<span data-ttu-id="44590-159">Создайте класс `SeedData` в папке *Models* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="44590-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="44590-160">Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.</span><span class="sxs-lookup"><span data-stu-id="44590-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="44590-161">Добавление инициализатора заполнения</span><span class="sxs-lookup"><span data-stu-id="44590-161">Add the seed initializer</span></span>

<span data-ttu-id="44590-162">Замените содержимое файла *Program.cs* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="44590-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="44590-163">В приведенном выше коде метод `Main` изменен, чтобы сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="44590-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="44590-164">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="44590-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="44590-165">Вызовите метод `seedData.Initialize`, передав ему экземпляр контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="44590-166">Высвобождение контекста после завершения работы метода заполнения.</span><span class="sxs-lookup"><span data-stu-id="44590-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="44590-167">[Оператор using](/dotnet/csharp/language-reference/keywords/using-statement) гарантирует удаление контекста.</span><span class="sxs-lookup"><span data-stu-id="44590-167">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="44590-168">Если `Update-Database` не выполнялось, возникает следующее исключение:</span><span class="sxs-lookup"><span data-stu-id="44590-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="44590-169">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="44590-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="44590-171">Удалите все записи в базе данных.</span><span class="sxs-lookup"><span data-stu-id="44590-171">Delete all the records in the database.</span></span> <span data-ttu-id="44590-172">Используйте ссылки удаления в браузере или в [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="44590-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="44590-173">Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения.</span><span class="sxs-lookup"><span data-stu-id="44590-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="44590-174">Для этого следует остановить и перезапустить IIS Express.</span><span class="sxs-lookup"><span data-stu-id="44590-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="44590-175">Закройте и перезапустите службы IIS с помощью любого из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="44590-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="44590-176">Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выйти** или **Остановить сайт**.</span><span class="sxs-lookup"><span data-stu-id="44590-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![Значок IIS Express в области уведомлений](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Контекстное меню](sql/_static/stopIIS.png)

   1. <span data-ttu-id="44590-179">Если приложение работает в режиме без отладки, нажмите клавишу <kbd>F5</kbd> для запуска в режиме отладки.</span><span class="sxs-lookup"><span data-stu-id="44590-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="44590-180">Если приложение находится в режиме отладки, остановите отладчик и нажмите клавишу <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="44590-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44590-181">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="44590-182">Удалите все записи в базе данных для запуска метода Seed.</span><span class="sxs-lookup"><span data-stu-id="44590-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="44590-183">Остановите и запустите приложение, чтобы начать заполнение базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="44590-184">В приложении отображаются заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="44590-184">The app shows the seeded data:</span></span>

![Приложение Movie с данными по фильмам, открытое в браузере](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="44590-186">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44590-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="44590-187">[Предыдущая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)
> [Далее: Обновление страниц](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="44590-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="44590-188">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="44590-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="44590-189">Объект `RazorPagesMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="44590-190">Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="44590-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44590-192">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="44590-193">Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает ключ `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="44590-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="44590-194">Для разработки на локальном уровне конфигурация получает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="44590-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44590-196">Созданная строка подключения будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="44590-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44590-197">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="44590-198">Если приложение развертывается на тестовом или рабочем сервере, можно задать строку подключения к тестовому или рабочему серверу базы данных с помощью переменной среды.</span><span class="sxs-lookup"><span data-stu-id="44590-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="44590-199">Дополнительные сведения см. в статье [Конфигурация](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="44590-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="44590-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="44590-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="44590-202">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ.</span><span class="sxs-lookup"><span data-stu-id="44590-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="44590-203">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="44590-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="44590-204">По умолчанию база данных LocalDB создает файлы `*.mdf` в каталоге `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="44590-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="44590-205">В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="44590-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Меню "Вид"](sql/_static/ssox.png)

* <span data-ttu-id="44590-207">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**:</span><span class="sxs-lookup"><span data-stu-id="44590-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Контекстные меню, открытые на таблице Movie](sql/_static/design.png)

  ![Таблицы Movie, открытые в конструкторе](sql/_static/dv.png)

<span data-ttu-id="44590-210">Обратите внимание на значок с изображением ключа рядом с `ID`.</span><span class="sxs-lookup"><span data-stu-id="44590-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="44590-211">По умолчанию EF создает свойство с именем `ID` для первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="44590-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="44590-212">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотреть данные**:</span><span class="sxs-lookup"><span data-stu-id="44590-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Открытая таблица Movie с отображением данных](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44590-214">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="44590-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="44590-215">SQLite</span></span>

<span data-ttu-id="44590-216">На веб-сайте [SQLite](https://www.sqlite.org/) указывается следующее:</span><span class="sxs-lookup"><span data-stu-id="44590-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="44590-217">SQLite — это автономная внедряемая полнофункциональная общедоступная система управления базами данных SQL с высокой степенью надежности.</span><span class="sxs-lookup"><span data-stu-id="44590-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="44590-218">На данный момент SQLite является самой популярной СУБД в мире.</span><span class="sxs-lookup"><span data-stu-id="44590-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="44590-219">Для просмотра базы данных SQLite, а также управления ей можно использовать множество самых разных сторонних инструментов.</span><span class="sxs-lookup"><span data-stu-id="44590-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="44590-220">На следующем рисунке показан [DB Browser для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="44590-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="44590-221">Если вы предпочитаете использовать другое средство для работы с SQLite, напишите нам в комментариях, чем именно оно нравится вам.</span><span class="sxs-lookup"><span data-stu-id="44590-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![DB Browser для SQLite с базой данных Movie](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="44590-223">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="44590-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="44590-224">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="44590-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="44590-225">Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="44590-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="44590-226">Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="44590-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="44590-227">Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="44590-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="44590-228">Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite.</span><span class="sxs-lookup"><span data-stu-id="44590-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="44590-229">Вместо этого при изменении схемы база данных удаляется и создается повторно.</span><span class="sxs-lookup"><span data-stu-id="44590-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="44590-230">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="44590-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="44590-231">Перестроение таблицы включает в себя:</span><span class="sxs-lookup"><span data-stu-id="44590-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="44590-232">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="44590-232">Creating a new table.</span></span>
>* <span data-ttu-id="44590-233">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="44590-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="44590-234">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="44590-234">Dropping the old table.</span></span>
>* <span data-ttu-id="44590-235">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="44590-235">Renaming the new table.</span></span>
>
><span data-ttu-id="44590-236">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="44590-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="44590-237">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="44590-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="44590-238">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="44590-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="44590-239">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="44590-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="44590-240">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="44590-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="44590-241">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="44590-241">Seed the database</span></span>

<span data-ttu-id="44590-242">Создайте класс `SeedData` в папке *Models* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="44590-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="44590-243">Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.</span><span class="sxs-lookup"><span data-stu-id="44590-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="44590-244">Добавление инициализатора заполнения</span><span class="sxs-lookup"><span data-stu-id="44590-244">Add the seed initializer</span></span>

<span data-ttu-id="44590-245">Замените содержимое файла *Program.cs* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="44590-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="44590-246">В приведенном выше коде метод `Main` изменен, чтобы сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="44590-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="44590-247">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="44590-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="44590-248">Вызовите метод `seedData.Initialize`, передав ему экземпляр контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="44590-249">Высвобождение контекста после завершения работы метода заполнения.</span><span class="sxs-lookup"><span data-stu-id="44590-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="44590-250">[Оператор using](/dotnet/csharp/language-reference/keywords/using-statement) гарантирует удаление контекста.</span><span class="sxs-lookup"><span data-stu-id="44590-250">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="44590-251">Если `Update-Database` не выполнялось, возникает следующее исключение:</span><span class="sxs-lookup"><span data-stu-id="44590-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="44590-252">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="44590-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44590-254">Удалите все записи в базе данных.</span><span class="sxs-lookup"><span data-stu-id="44590-254">Delete all the records in the database.</span></span> <span data-ttu-id="44590-255">Используйте ссылки удаления в браузере или в [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="44590-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="44590-256">Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения.</span><span class="sxs-lookup"><span data-stu-id="44590-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="44590-257">Для этого следует остановить и перезапустить IIS Express.</span><span class="sxs-lookup"><span data-stu-id="44590-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="44590-258">Закройте и перезапустите службы IIS с помощью любого из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="44590-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="44590-259">Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выйти** или **Остановить сайт**.</span><span class="sxs-lookup"><span data-stu-id="44590-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Значок IIS Express в области уведомлений](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Контекстное меню](sql/_static/stopIIS.png)

    * <span data-ttu-id="44590-262">Если приложение работает в режиме без отладки, нажмите клавишу <kbd>F5</kbd> для запуска в режиме отладки.</span><span class="sxs-lookup"><span data-stu-id="44590-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="44590-263">Если приложение находится в режиме отладки, остановите отладчик и нажмите клавишу <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="44590-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44590-264">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="44590-265">Удалите все записи в базе данных для запуска метода Seed.</span><span class="sxs-lookup"><span data-stu-id="44590-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="44590-266">Остановите и запустите приложение, чтобы начать заполнение базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="44590-267">В приложении отображаются заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="44590-267">The app shows the seeded data:</span></span>

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="44590-269">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44590-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="44590-270">[Предыдущая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)
> [Далее: Обновление страниц](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="44590-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="44590-271">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="44590-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="44590-272">Объект `RazorPagesMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="44590-273">Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="44590-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="44590-275">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="44590-276">Дополнительные сведения о методах, которые используются в `ConfigureServices`, см.:</span><span class="sxs-lookup"><span data-stu-id="44590-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="44590-277">[Общий регламент по защите данных (GDPR), принятый в ЕС, в ASP.NET Core](xref:security/gdpr) для `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="44590-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="44590-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="44590-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="44590-279">Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает ключ `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="44590-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="44590-280">Для разработки на локальном уровне конфигурация получает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="44590-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44590-282">Созданная строка подключения будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="44590-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="44590-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44590-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44590-284">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="44590-285">Если приложение развертывается на тестовом или рабочем сервере, можно задать строку подключения к тестовому или рабочему серверу базы данных с помощью переменной среды.</span><span class="sxs-lookup"><span data-stu-id="44590-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="44590-286">Дополнительные сведения см. в статье [Конфигурация](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="44590-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="44590-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="44590-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="44590-289">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ.</span><span class="sxs-lookup"><span data-stu-id="44590-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="44590-290">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="44590-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="44590-291">По умолчанию база данных LocalDB создает файлы `*.mdf` в каталоге `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="44590-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="44590-292">В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="44590-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Меню "Вид"](sql/_static/ssox.png)

* <span data-ttu-id="44590-294">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**:</span><span class="sxs-lookup"><span data-stu-id="44590-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Контекстное меню, открытое на таблице Movie](sql/_static/design.png)

  ![Таблица Movie, открытая в конструкторе](sql/_static/dv.png)

<span data-ttu-id="44590-297">Обратите внимание на значок с изображением ключа рядом с `ID`.</span><span class="sxs-lookup"><span data-stu-id="44590-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="44590-298">По умолчанию EF создает свойство с именем `ID` для первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="44590-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="44590-299">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотреть данные**:</span><span class="sxs-lookup"><span data-stu-id="44590-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Открытая таблица Movie с отображением данных](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="44590-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44590-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44590-302">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="44590-303">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="44590-303">Seed the database</span></span>

<span data-ttu-id="44590-304">Создайте класс `SeedData` в папке *Models* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="44590-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="44590-305">Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.</span><span class="sxs-lookup"><span data-stu-id="44590-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="44590-306">Добавление инициализатора заполнения</span><span class="sxs-lookup"><span data-stu-id="44590-306">Add the seed initializer</span></span>

<span data-ttu-id="44590-307">Замените содержимое файла *Program.cs* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="44590-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="44590-308">В приведенном выше коде метод `Main` изменен, чтобы сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="44590-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="44590-309">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="44590-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="44590-310">Вызовите метод `seedData.Initialize`, передав ему экземпляр контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="44590-311">Высвобождение контекста после завершения работы метода заполнения.</span><span class="sxs-lookup"><span data-stu-id="44590-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="44590-312">[Оператор using](/dotnet/csharp/language-reference/keywords/using-statement) гарантирует удаление контекста.</span><span class="sxs-lookup"><span data-stu-id="44590-312">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="44590-313">Рабочее приложение не вызывает `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="44590-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="44590-314">Он добавляется в предыдущем коде, чтобы предотвратить следующее исключение, если `Update-Database` не был запущен.</span><span class="sxs-lookup"><span data-stu-id="44590-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="44590-315">SqlException: Не удается открыть базу данных "RazorPagesMovieContext-21", запрашиваемую именем входа.</span><span class="sxs-lookup"><span data-stu-id="44590-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="44590-316">Сбой при входе.</span><span class="sxs-lookup"><span data-stu-id="44590-316">The login failed.</span></span>
<span data-ttu-id="44590-317">Сбой при входе в систему пользователя user name.</span><span class="sxs-lookup"><span data-stu-id="44590-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="44590-318">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="44590-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44590-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44590-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44590-320">Удалите все записи в базе данных.</span><span class="sxs-lookup"><span data-stu-id="44590-320">Delete all the records in the database.</span></span> <span data-ttu-id="44590-321">Это можно сделать с помощью ссылок удаления в браузере или из [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="44590-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="44590-322">Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения.</span><span class="sxs-lookup"><span data-stu-id="44590-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="44590-323">Для этого следует остановить и перезапустить IIS Express.</span><span class="sxs-lookup"><span data-stu-id="44590-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="44590-324">Воспользуйтесь одним из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="44590-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="44590-325">Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выйти** или **Остановить сайт**.</span><span class="sxs-lookup"><span data-stu-id="44590-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Значок IIS Express в области уведомлений](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Контекстное меню](sql/_static/stopIIS.png)

    * <span data-ttu-id="44590-328">Если приложение работает в режиме без отладки, нажмите клавишу <kbd>F5</kbd> для запуска в режиме отладки.</span><span class="sxs-lookup"><span data-stu-id="44590-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="44590-329">Если приложение находится в режиме отладки, остановите отладчик и нажмите клавишу <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="44590-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="44590-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44590-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="44590-331">Удалите все записи в базе данных для запуска метода Seed.</span><span class="sxs-lookup"><span data-stu-id="44590-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="44590-332">Остановите и запустите приложение, чтобы начать заполнение базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44590-333">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="44590-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="44590-334">Удалите все записи в базе данных для запуска метода Seed.</span><span class="sxs-lookup"><span data-stu-id="44590-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="44590-335">Остановите и запустите приложение, чтобы начать заполнение базы данных.</span><span class="sxs-lookup"><span data-stu-id="44590-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="44590-336">В приложении отображаются заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="44590-336">The app shows the seeded data:</span></span>

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55https.png)

<span data-ttu-id="44590-338">В следующем учебнике будет улучшено представление данных.</span><span class="sxs-lookup"><span data-stu-id="44590-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44590-339">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44590-339">Additional resources</span></span>

* [<span data-ttu-id="44590-340">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="44590-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="44590-341">[Предыдущая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)
> [Далее: Обновление страниц](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="44590-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end