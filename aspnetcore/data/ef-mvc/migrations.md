---
title: Учебник. Часть 5. Применение миграций к примеру приложения университета Contoso
description: Часть 5 серии руководств по университету Contoso. Используйте функцию миграций EF Core для управления изменениями модели данных в приложении ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: contperf-fy21q2
ms.date: 11/13/2020
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: aebbc3f29b0356c7993abd83869ab21d3613bf61
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589352"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="9ef82-104">Учебник. Часть 5. Применение миграций к примеру приложения университета Contoso</span><span class="sxs-lookup"><span data-stu-id="9ef82-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="9ef82-105">В этом руководстве вы начинаете использовать функцию миграций EF Core для управления изменениями модели данных.</span><span class="sxs-lookup"><span data-stu-id="9ef82-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="9ef82-106">В последующих руководствах вы добавите дополнительные миграции по мере изменения модели данных.</span><span class="sxs-lookup"><span data-stu-id="9ef82-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="9ef82-107">Изучив это руководство, вы:</span><span class="sxs-lookup"><span data-stu-id="9ef82-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9ef82-108">Дополнительные сведения о миграциях</span><span class="sxs-lookup"><span data-stu-id="9ef82-108">Learn about migrations</span></span>
> * <span data-ttu-id="9ef82-109">Создание первоначальной миграции</span><span class="sxs-lookup"><span data-stu-id="9ef82-109">Create an initial migration</span></span>
> * <span data-ttu-id="9ef82-110">Обзор методов Up и Down</span><span class="sxs-lookup"><span data-stu-id="9ef82-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="9ef82-111">Дополнительные сведения о моментальном снимке модели данных</span><span class="sxs-lookup"><span data-stu-id="9ef82-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="9ef82-112">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="9ef82-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9ef82-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="9ef82-113">Prerequisites</span></span>

* [<span data-ttu-id="9ef82-114">Сортировка, фильтрация и разбиение на страницы</span><span class="sxs-lookup"><span data-stu-id="9ef82-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="9ef82-115">Сведения о миграциях</span><span class="sxs-lookup"><span data-stu-id="9ef82-115">About migrations</span></span>

<span data-ttu-id="9ef82-116">При разработке нового приложения ваша модель данных часто меняется, и при каждом таком изменении она нарушает синхронизацию с базой данных.</span><span class="sxs-lookup"><span data-stu-id="9ef82-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="9ef82-117">Вы начали работу с этими руководствами, настроив Entity Framework для создания базы данных, если она еще не существует.</span><span class="sxs-lookup"><span data-stu-id="9ef82-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="9ef82-118">Затем при каждом изменении модели данных — добавлении, удалении или изменении классов сущностей или изменении класса DbContext — вы можете удалить базу данных, после чего EF создает новую, которая соответствует данной модели, и заполняет ее тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="9ef82-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="9ef82-119">Этот способ для обеспечения синхронизации базы данных с моделью данных хорошо работает до развертывания приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="9ef82-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="9ef82-120">Когда приложение выполняется в рабочей среде, оно обычно хранит данные, которые вы хотите сохранить, и вам нежелательно терять все при каждом изменении, например при добавлении нового столбца.</span><span class="sxs-lookup"><span data-stu-id="9ef82-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="9ef82-121">Функция миграций EF Core решает эту проблему, позволяя EF обновить схему базы данных вместо создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="9ef82-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="9ef82-122">Для миграции можно использовать **консоль диспетчера пакетов** (PMC) или CLI.</span><span class="sxs-lookup"><span data-stu-id="9ef82-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="9ef82-123">Эти руководства демонстрируют использование команд интерфейса командной строки.</span><span class="sxs-lookup"><span data-stu-id="9ef82-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="9ef82-124">Дополнительные сведения о PMC [приведены в конце этого руководства](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9ef82-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="9ef82-125">Удалите базу данных:</span><span class="sxs-lookup"><span data-stu-id="9ef82-125">Drop the database</span></span>

<span data-ttu-id="9ef82-126">Установите средства EF Core в качестве [глобальных средств](/ef/core/miscellaneous/cli/dotnet) и удалите базу данных:</span><span class="sxs-lookup"><span data-stu-id="9ef82-126">Install EF Core tools as a [global tool](/ef/core/miscellaneous/cli/dotnet) and delete the database:</span></span>

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

<span data-ttu-id="9ef82-127">Следующий раздел описывает выполнение команд интерфейса командной строки.</span><span class="sxs-lookup"><span data-stu-id="9ef82-127">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="9ef82-128">Создание первоначальной миграции</span><span class="sxs-lookup"><span data-stu-id="9ef82-128">Create an initial migration</span></span>

<span data-ttu-id="9ef82-129">Сохраните изменения и выполните сборку проекта.</span><span class="sxs-lookup"><span data-stu-id="9ef82-129">Save your changes and build the project.</span></span> <span data-ttu-id="9ef82-130">После этого откройте командное окно и в папку проекта.</span><span class="sxs-lookup"><span data-stu-id="9ef82-130">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="9ef82-131">Вот как это можно сделать быстро:</span><span class="sxs-lookup"><span data-stu-id="9ef82-131">Here's a quick way to do that:</span></span>

* <span data-ttu-id="9ef82-132">Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите в контекстном меню пункт **Открыть папку в проводнике**.</span><span class="sxs-lookup"><span data-stu-id="9ef82-132">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Элемент меню "Открыть в проводнике"](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="9ef82-134">Введите "cmd" в адресной строке и нажмите клавишу ВВОД.</span><span class="sxs-lookup"><span data-stu-id="9ef82-134">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Открытие командного окна](migrations/_static/open-command-window.png)

<span data-ttu-id="9ef82-136">Введите в командном окне следующую команду:</span><span class="sxs-lookup"><span data-stu-id="9ef82-136">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="9ef82-137">Приведенные выше команды выводят результат наподобие следующего:</span><span class="sxs-lookup"><span data-stu-id="9ef82-137">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="9ef82-138">Если отображается сообщение об ошибке "*Доступ к файлу... ContosoUniversity.dll невозможен, так как файл используется другим процессом*", найдите значок IIS Express в области уведомлений Windows, щелкните его правой кнопкой мыши, а затем выберите **ContosoUniversity > Остановить сайт**.</span><span class="sxs-lookup"><span data-stu-id="9ef82-138">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="9ef82-139">Обзор методов Up и Down</span><span class="sxs-lookup"><span data-stu-id="9ef82-139">Examine Up and Down methods</span></span>

<span data-ttu-id="9ef82-140">При выполнении команды `migrations add` система EF сформировала код, который создаст базу данных с нуля.</span><span class="sxs-lookup"><span data-stu-id="9ef82-140">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="9ef82-141">Этот код находится в файле *\<timestamp>_InitialCreate.cs* внутри папки *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="9ef82-141">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="9ef82-142">Метод `Up` класса `InitialCreate` создает таблицы базы данных, которые соответствуют наборам сущностей модели данных, а метод `Down` удаляет их, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9ef82-142">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="9ef82-143">Функция миграций вызывает метод `Up`, чтобы реализовать изменения модели данных для миграции.</span><span class="sxs-lookup"><span data-stu-id="9ef82-143">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="9ef82-144">При вводе команды для отката обновления функция миграций вызывает метод `Down`.</span><span class="sxs-lookup"><span data-stu-id="9ef82-144">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="9ef82-145">Этот пример кода предназначен для первоначальной миграции, созданной при вводе команды `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="9ef82-145">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="9ef82-146">Параметр имени миграции (в примере это "InitialCreate") используется в качестве имени файла и может быть любым.</span><span class="sxs-lookup"><span data-stu-id="9ef82-146">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="9ef82-147">Рекомендуется выбрать слово или фразу, которые кратко описывают назначение миграции.</span><span class="sxs-lookup"><span data-stu-id="9ef82-147">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="9ef82-148">Например, последнюю миграцию можно назвать "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="9ef82-148">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="9ef82-149">Если вы создали первоначальную миграцию, когда база данных уже существовала, код для создания базы данных формируется, но выполнять его не требуется, так как база данных уже соответствует модели данных.</span><span class="sxs-lookup"><span data-stu-id="9ef82-149">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="9ef82-150">Однако при развертывании приложения в другой среде, где база данных еще не существует, этот код будет выполняться для создания базы данных, поэтому рекомендуется сначала его протестировать.</span><span class="sxs-lookup"><span data-stu-id="9ef82-150">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="9ef82-151">Вот почему ранее вы изменили имя базы данных в строке подключения — это позволяет функции миграций создать базу данных с нуля.</span><span class="sxs-lookup"><span data-stu-id="9ef82-151">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="9ef82-152">Моментальный снимок модели данных</span><span class="sxs-lookup"><span data-stu-id="9ef82-152">The data model snapshot</span></span>

<span data-ttu-id="9ef82-153">Функция миграций создает *моментальный снимок* текущей схемы базы данных в *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="9ef82-153">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="9ef82-154">При добавлении миграции EF определяет, что именно изменилось, сравнивая модель данных с файлом моментального снимка.</span><span class="sxs-lookup"><span data-stu-id="9ef82-154">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="9ef82-155">Для удаления миграции используйте команду [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="9ef82-155">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="9ef82-156">`dotnet ef migrations remove` удаляет миграцию и гарантирует корректный сброс моментального снимка.</span><span class="sxs-lookup"><span data-stu-id="9ef82-156">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="9ef82-157">В случае сбоя `dotnet ef migrations remove` используйте `dotnet ef migrations remove -v`, чтобы получить сведения об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9ef82-157">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="9ef82-158">Дополнительные сведения об использовании файла моментального снимка см. в разделе [Миграции Core EF в средах групп](/ef/core/managing-schemas/migrations/teams).</span><span class="sxs-lookup"><span data-stu-id="9ef82-158">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="9ef82-159">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="9ef82-159">Apply the migration</span></span>

<span data-ttu-id="9ef82-160">Введите следующую команду в командном окне, чтобы создать базу данных и таблицы в ней.</span><span class="sxs-lookup"><span data-stu-id="9ef82-160">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="9ef82-161">Выходные данные команды аналогичны команде `migrations add`, за исключением того, что вы видите журналы для команд SQL, настраивающих базу данных.</span><span class="sxs-lookup"><span data-stu-id="9ef82-161">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="9ef82-162">В приведенном ниже примере выходных данных большинство журналов опущено.</span><span class="sxs-lookup"><span data-stu-id="9ef82-162">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="9ef82-163">Если вам не нужен такой уровень детализации сообщений журнала, можно изменить уровень ведения журнала в файле *appsettings.Development.json*.</span><span class="sxs-lookup"><span data-stu-id="9ef82-163">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="9ef82-164">Дополнительные сведения см. в разделе <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="9ef82-164">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

<span data-ttu-id="9ef82-165">Используйте **обозреватель объектов SQL Server** для проверки базы данных, как описано в первом руководстве.</span><span class="sxs-lookup"><span data-stu-id="9ef82-165">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="9ef82-166">Вы заметите добавление таблицы \_\_EFMigrationsHistory, отслеживающей миграции, которые были применены к базе данных.</span><span class="sxs-lookup"><span data-stu-id="9ef82-166">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="9ef82-167">Просмотрев данные в этой таблице, вы увидите одну строку для первой миграции.</span><span class="sxs-lookup"><span data-stu-id="9ef82-167">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="9ef82-168">(Последний журнал в предыдущем примере выходных данных интерфейса командной строки показывает оператор INSERT, создающий эту строку.)</span><span class="sxs-lookup"><span data-stu-id="9ef82-168">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="9ef82-169">Запустите приложение, чтобы убедиться, что все работает, как и раньше.</span><span class="sxs-lookup"><span data-stu-id="9ef82-169">Run the application to verify that everything still works the same as before.</span></span>

![Страница указателя учащихся](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="9ef82-171">Сравнение CLI и PMC</span><span class="sxs-lookup"><span data-stu-id="9ef82-171">Compare CLI and PMC</span></span>

<span data-ttu-id="9ef82-172">Средства EF для управления миграциями доступны в виде команд интерфейса командной строки .NET Core или командлетов PowerShell в окне **консоли диспетчера пакетов** Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9ef82-172">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="9ef82-173">Это руководство описывает использование интерфейса командной строки, но вы можете использовать и консоль диспетчера пакетов.</span><span class="sxs-lookup"><span data-stu-id="9ef82-173">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="9ef82-174">Команды EF для команд консоли диспетчера пакетов находятся в пакете [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools).</span><span class="sxs-lookup"><span data-stu-id="9ef82-174">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="9ef82-175">Этот пакет входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), поэтому если приложение уже содержит ссылку на пакет `Microsoft.AspNetCore.App`, добавлять ссылку на пакет не нужно.</span><span class="sxs-lookup"><span data-stu-id="9ef82-175">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="9ef82-176">**Внимание!** Это не тот же пакет, который вы устанавливаете для CLI, изменив файл *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="9ef82-176">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="9ef82-177">Его имя заканчивается на `Tools`, а имя пакета интерфейса командной строки — на `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="9ef82-177">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="9ef82-178">Дополнительные сведения о командах интерфейса командной строки см. в разделе [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="9ef82-178">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="9ef82-179">Дополнительные сведения о командах консоли диспетчера пакетов см. в разделе [Консоль диспетчера пакетов (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="9ef82-179">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="9ef82-180">Получение кода</span><span class="sxs-lookup"><span data-stu-id="9ef82-180">Get the code</span></span>

[<span data-ttu-id="9ef82-181">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="9ef82-181">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="9ef82-182">Следующий шаг</span><span class="sxs-lookup"><span data-stu-id="9ef82-182">Next step</span></span>

<span data-ttu-id="9ef82-183">В следующем учебнике описаны более сложные вопросы, связанные с развертыванием модели данных.</span><span class="sxs-lookup"><span data-stu-id="9ef82-183">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="9ef82-184">Попутно вы создадите и примените дополнительные миграции.</span><span class="sxs-lookup"><span data-stu-id="9ef82-184">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9ef82-185">Создание и применение дополнительных миграций</span><span class="sxs-lookup"><span data-stu-id="9ef82-185">Create and apply additional migrations</span></span>](complex-data-model.md)
