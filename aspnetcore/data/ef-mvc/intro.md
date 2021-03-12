---
title: Учебник. Начало работы с EF Core в веб-приложении MVC ASP.NET
description: Это первый учебник из серии, в котором описывается создание примера приложения EF/MVC для университета Contoso.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 5bb1a05f5e81730d0785c1d6a69a8cafb68f85ab
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586687"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="c17b9-103">Учебник. Начало работы с EF Core в веб-приложении MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="c17b9-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="c17b9-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="c17b9-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="c17b9-105">На примере веб-приложения для университета Contoso демонстрируется процесс создания веб-приложений ASP.NET Core MVC с помощью Entity Framework (EF) Core и Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c17b9-105">The Contoso University sample web app demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="c17b9-106">В этом примере приложения реализуется веб-сайт вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="c17b9-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="c17b9-107">На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="c17b9-108">Это первый учебник из серии, в котором описывается создание примера приложения для университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="c17b9-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c17b9-109">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="c17b9-109">Prerequisites</span></span>

* <span data-ttu-id="c17b9-110">Если у вас нет опыта работы с ASP.NET Core MVC, ознакомьтесь с серией учебников по [началу работы с ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc), прежде чем приступать к изучению этого учебника.</span><span class="sxs-lookup"><span data-stu-id="c17b9-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="c17b9-111">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="c17b9-111">Database engines</span></span>

<span data-ttu-id="c17b9-112">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="c17b9-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="c17b9-113">Решение проблем и устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="c17b9-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="c17b9-114">Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="c17b9-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="c17b9-115">Список распространенных ошибок и способы их устранения см. в [разделе "Устранение неполадок" последнего руководства серии](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="c17b9-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="c17b9-116">Если вам не удалось найти нужную информацию, вы можете задать вопрос на сайте StackOverflow.com в разделах, посвященных [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="c17b9-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="c17b9-117">Эта серия включает в себя 10 учебников, содержание каждого из которых базируется на предыдущих учебниках.</span><span class="sxs-lookup"><span data-stu-id="c17b9-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="c17b9-118">После успешного завершения каждого руководства рекомендуется сохранять копию проекта.</span><span class="sxs-lookup"><span data-stu-id="c17b9-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="c17b9-119">Таким образом, при возникновении проблем вы сможете вернуться к предыдущему учебнику, а не к началу серии.</span><span class="sxs-lookup"><span data-stu-id="c17b9-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="c17b9-120">Веб-приложение Contoso University</span><span class="sxs-lookup"><span data-stu-id="c17b9-120">Contoso University web app</span></span>

<span data-ttu-id="c17b9-121">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="c17b9-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="c17b9-122">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="c17b9-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="c17b9-123">Вот несколько экранов в приложении:</span><span class="sxs-lookup"><span data-stu-id="c17b9-123">Here are a few of the screens in the app:</span></span>

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="c17b9-126">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="c17b9-126">Create web app</span></span>

1. <span data-ttu-id="c17b9-127">Запустите Visual Studio и щелкните **Создать проект**</span><span class="sxs-lookup"><span data-stu-id="c17b9-127">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="c17b9-128">В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-128">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="c17b9-129">В диалоговом окне **Настроить новый проект** введите `ContosoUniversity` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-129">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="c17b9-130">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен (`namespace`) совпадали при копировании кода.</span><span class="sxs-lookup"><span data-stu-id="c17b9-130">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="c17b9-131">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-131">Select **Create**.</span></span>
1. <span data-ttu-id="c17b9-132">В диалоговом окне **Создайте веб-приложение ASP.NET Core** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c17b9-132">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="c17b9-133">В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-133">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="c17b9-134">Щелкните **ASP.NET Core Web App (Model-View-Controller)** (Веб-приложение ASP.NET Core (модель — представление — контроллер)).</span><span class="sxs-lookup"><span data-stu-id="c17b9-134">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="c17b9-135">Нажмите кнопку **Создать**.
      ![Диалоговое окно создания проекта ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="c17b9-135">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="c17b9-136">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="c17b9-136">Set up the site style</span></span>

<span data-ttu-id="c17b9-137">Выполните небольшую базовую настройку меню, макета и домашней страницы сайта.</span><span class="sxs-lookup"><span data-stu-id="c17b9-137">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="c17b9-138">Откройте файл *Views/Shared/_Layout.cshtml* и внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="c17b9-138">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="c17b9-139">Измените каждое вхождение `ContosoUniversity` на `Contoso University`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-139">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="c17b9-140">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="c17b9-140">There are three occurrences.</span></span>
* <span data-ttu-id="c17b9-141">Добавьте пункты меню **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Privacy** (Конфиденциальность).</span><span class="sxs-lookup"><span data-stu-id="c17b9-141">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="c17b9-142">Предыдущие изменения выделены в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="c17b9-142">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="c17b9-143">Замените содержимое файла *Views/Home/Index.cshtml* следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="c17b9-143">In *Views/Home/Index.cshtml*, replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="c17b9-144">Нажмите клавиши CTRL+F5, чтобы запустить проект, и выберите в меню **Отладка > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-144">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="c17b9-145">Домашняя страница отображается с вкладками для страниц, созданных при работе с этим учебником.</span><span class="sxs-lookup"><span data-stu-id="c17b9-145">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Домашняя страница университета Contoso](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="c17b9-147">Пакеты NuGet EF Core</span><span class="sxs-lookup"><span data-stu-id="c17b9-147">EF Core NuGet packages</span></span>

<span data-ttu-id="c17b9-148">В этом учебнике используется SQL Server, для которого требуется пакет поставщика [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="c17b9-148">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="c17b9-149">Этот пакет EF SQL Server и его зависимости, `Microsoft.EntityFrameworkCore` и `Microsoft.EntityFrameworkCore.Relational`, обеспечивают поддержку среды выполнения для платформы EF.</span><span class="sxs-lookup"><span data-stu-id="c17b9-149">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="c17b9-150">Добавьте пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) и пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="c17b9-150">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="c17b9-151">Введите в консоли диспетчера пакетов (PMC) следующие команды, чтобы добавить пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="c17b9-151">In the Package Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="c17b9-152">Пакет NuGet `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` предоставляет ПО промежуточного слоя ASP.NET Core для страниц ошибок EF Core.</span><span class="sxs-lookup"><span data-stu-id="c17b9-152">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="c17b9-153">Это ПО промежуточного слоя помогает обнаруживать и диагностировать ошибки с помощью миграций EF Core.</span><span class="sxs-lookup"><span data-stu-id="c17b9-153">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="c17b9-154">Дополнительные сведения о других поставщиках баз данных, которые доступны для платформы EF Core, см. в разделе [Поставщики баз данных](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="c17b9-154">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="c17b9-155">Создание модели данных</span><span class="sxs-lookup"><span data-stu-id="c17b9-155">Create the data model</span></span>

<span data-ttu-id="c17b9-156">Для этого приложения будут созданы следующие классы сущностей:</span><span class="sxs-lookup"><span data-stu-id="c17b9-156">The following entity classes are created for this app:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="c17b9-158">У приведенных выше сущностей есть следующие отношения.</span><span class="sxs-lookup"><span data-stu-id="c17b9-158">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="c17b9-159">Между сущностями `Student` и `Enrollment` действует связь "один ко многим".</span><span class="sxs-lookup"><span data-stu-id="c17b9-159">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="c17b9-160">Учащегося можно зачислить на любое число курсов.</span><span class="sxs-lookup"><span data-stu-id="c17b9-160">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="c17b9-161">Между сущностями `Course` и `Enrollment` действует связь "один ко многим".</span><span class="sxs-lookup"><span data-stu-id="c17b9-161">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="c17b9-162">На курс может быть зачислено любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="c17b9-162">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="c17b9-163">В следующих разделах создается класс для каждой из этих сущностей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-163">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="c17b9-164">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="c17b9-164">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

<span data-ttu-id="c17b9-166">В папке *Models* создайте класс `Student`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="c17b9-166">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="c17b9-167">Свойство `ID` используется в качестве столбца первичного ключа (**ПК**) в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="c17b9-167">The `ID` property is the primary key (**PK**) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="c17b9-168">По умолчанию платформа EF интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-168">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="c17b9-169">Например, ПК может называться `StudentID`, а не `ID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-169">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="c17b9-170">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="c17b9-170">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="c17b9-171">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="c17b9-171">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="c17b9-172">Свойство `Enrollments` сущности `Student`:</span><span class="sxs-lookup"><span data-stu-id="c17b9-172">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="c17b9-173">Содержит все сущности `Enrollment`, связанные с этой сущностью `Student`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-173">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="c17b9-174">Если определенная строка `Student` в базе данных содержит две связанные строки `Enrollment`:</span><span class="sxs-lookup"><span data-stu-id="c17b9-174">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="c17b9-175">Свойство навигации `Enrollments` сущности `Student` содержит эти две сущности `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-175">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="c17b9-176">Строки `Enrollment` содержат значение первичного ключа учащегося в столбце внешнего ключа (**ВК**) `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-176">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key (**FK**) column.</span></span>

<span data-ttu-id="c17b9-177">Если свойство навигации содержит несколько сущностей:</span><span class="sxs-lookup"><span data-stu-id="c17b9-177">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="c17b9-178">Тип должен быть списком, например `ICollection<T>`, `List<T>` или `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-178">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="c17b9-179">Сущности можно добавлять, удалять и обновлять.</span><span class="sxs-lookup"><span data-stu-id="c17b9-179">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="c17b9-180">Связи "многие ко многим" и "один ко многим" могут содержать несколько сущностей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-180">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="c17b9-181">Если используется `ICollection<T>`, платформа EF по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-181">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="c17b9-182">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="c17b9-182">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="c17b9-184">В папке *Models* создайте класс `Enrollment`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="c17b9-184">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="c17b9-185">Свойство `EnrollmentID` — это ПК.</span><span class="sxs-lookup"><span data-stu-id="c17b9-185">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="c17b9-186">Эта сущность использует шаблон `classnameID` вместо самого по себе `ID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-186">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="c17b9-187">Сущность `Student` использовала шаблон `ID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-187">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="c17b9-188">Некоторые разработчики предпочитают использовать один шаблон во всей модели данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-188">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="c17b9-189">В этом учебнике демонстрируется возможность использования любого из шаблонов.</span><span class="sxs-lookup"><span data-stu-id="c17b9-189">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="c17b9-190">В [одном из следующих учебников](inheritance.md) показано, за счет чего использование `ID` без имени класса позволяет упростить реализацию наследования в модели данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-190">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="c17b9-191">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-191">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="c17b9-192">Знак `?` после объявления типа `Grade` указывает, что свойство `Grade` [допускает значение NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span><span class="sxs-lookup"><span data-stu-id="c17b9-192">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="c17b9-193">Оценка со значением `null` отличается от нулевой оценки.</span><span class="sxs-lookup"><span data-stu-id="c17b9-193">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="c17b9-194">При значении `null` оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="c17b9-194">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="c17b9-195">Свойство `StudentID` представляет собой внешний ключ (ВК). Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-195">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="c17b9-196">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит только отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-196">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="c17b9-197">Она отличается от свойства навигации `Student.Enrollments`, которое содержит несколько сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-197">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="c17b9-198">Свойство `CourseID` представляет собой ВК. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-198">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="c17b9-199">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-199">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="c17b9-200">Entity Framework интерпретирует свойство как свойство ВК, если оно называется `<`имя_свойства_навигации`><`имя_свойства_первичного_ключа`>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-200">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="c17b9-201">Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет значение ПК `ID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-201">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="c17b9-202">Свойства ВК также могут называться `<`имя_свойства_первичного_ключа`>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-202">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="c17b9-203">Например, `CourseID`, так как сущность `Course` имеет значение ПК `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-203">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="c17b9-204">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="c17b9-204">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="c17b9-206">В папке *Models* создайте класс `Course`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="c17b9-206">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="c17b9-207">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="c17b9-207">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="c17b9-208">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-208">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="c17b9-209">Атрибут [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) описан в [следующем учебнике](complex-data-model.md).</span><span class="sxs-lookup"><span data-stu-id="c17b9-209">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="c17b9-210">Этот атрибут позволяет ввести ПК для курса, а не использовать базу данных, чтобы создать его.</span><span class="sxs-lookup"><span data-stu-id="c17b9-210">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="c17b9-211">Создание контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c17b9-211">Create the database context</span></span>

<span data-ttu-id="c17b9-212">Контекст базы данных <xref:Microsoft.EntityFrameworkCore.DbContext> — это основной класс, который координирует функциональные возможности EF для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-212">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="c17b9-213">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-213">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="c17b9-214">Производный класс `DbContext` указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-214">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="c17b9-215">Некоторые расширения функциональности EF можно настроить.</span><span class="sxs-lookup"><span data-stu-id="c17b9-215">Some EF behaviors can be customized.</span></span> <span data-ttu-id="c17b9-216">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-216">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="c17b9-217">В папке проекта создайте папку `Data`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-217">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="c17b9-218">В папке *Data* создайте файл `SchoolContext`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="c17b9-218">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="c17b9-219">Представленный выше код создает свойство `DbSet` для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-219">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="c17b9-220">Терминология EF:</span><span class="sxs-lookup"><span data-stu-id="c17b9-220">In EF terminology:</span></span>

* <span data-ttu-id="c17b9-221">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-221">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="c17b9-222">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="c17b9-222">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c17b9-223">Инструкции `DbSet<Enrollment>` и `DbSet<Course>` можно опустить. Это не нарушит функциональность.</span><span class="sxs-lookup"><span data-stu-id="c17b9-223">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="c17b9-224">EF включает их неявно, так как</span><span class="sxs-lookup"><span data-stu-id="c17b9-224">EF would include them implicitly because:</span></span>

* <span data-ttu-id="c17b9-225">сущность `Student` ссылается на сущность `Enrollment`,</span><span class="sxs-lookup"><span data-stu-id="c17b9-225">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="c17b9-226">а сущность `Enrollment` ссылается на сущность `Course`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-226">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="c17b9-227">При создании базы данных платформа EF создает таблицы с именами, соответствующими именам свойств в `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-227">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="c17b9-228">Имена свойств для коллекций обычно указаны во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="c17b9-228">Property names for collections are typically plural.</span></span> <span data-ttu-id="c17b9-229">Например, используйте идентификатор `Students` вместо `Student`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-229">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="c17b9-230">В среде разработчиков нет единого мнения о том, следует ли использовать имена таблиц во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="c17b9-230">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="c17b9-231">В этих учебниках вместо принятого по умолчанию способа таблицам в `DbContext` присваиваются имена в единственном числе.</span><span class="sxs-lookup"><span data-stu-id="c17b9-231">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="c17b9-232">Чтобы сделать это, добавьте выделенный ниже код после последнего свойства DbSet.</span><span class="sxs-lookup"><span data-stu-id="c17b9-232">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="c17b9-233">Зарегистрируйте `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-233">Register the `SchoolContext`</span></span>

<span data-ttu-id="c17b9-234">ASP.NET Core поддерживает [внедрение зависимостей](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="c17b9-234">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="c17b9-235">С помощью внедрения зависимостей службы, например контекст базы данных EF, регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="c17b9-235">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="c17b9-236">Затем компоненты, которые используют эти службы, например контроллеры MVC, обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="c17b9-236">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="c17b9-237">Код конструктора контроллера, который получает экземпляр контекста, будет приведен позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="c17b9-237">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="c17b9-238">Чтобы зарегистрировать `SchoolContext` как службу, откройте файл *Startup.cs* и добавьте выделенные строки в метод `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-238">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="c17b9-239">Имя строки подключения передается в контекст путем вызова метода для объекта `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-239">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="c17b9-240">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="c17b9-240">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="c17b9-241">Откройте файл *appsettings.json* и добавьте строку подключения, как показано в следующей разметке:</span><span class="sxs-lookup"><span data-stu-id="c17b9-241">Open the *appsettings.json* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="c17b9-242">Добавление фильтра исключений базы данных</span><span class="sxs-lookup"><span data-stu-id="c17b9-242">Add the database exception filter</span></span>

<span data-ttu-id="c17b9-243">Добавьте <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> в `ConfigureServices`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="c17b9-243">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=6)]

<span data-ttu-id="c17b9-244">`AddDatabaseDeveloperPageExceptionFilter` предоставляет полезные сведения об ошибках в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="c17b9-244">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="c17b9-245">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="c17b9-245">SQL Server Express LocalDB</span></span>

<span data-ttu-id="c17b9-246">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="c17b9-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="c17b9-247">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="c17b9-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="c17b9-248">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="c17b9-248">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="c17b9-249">По умолчанию LocalDB создает файлы базы данных *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-249">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="c17b9-250">Инициализация базы данных с тестовыми данными</span><span class="sxs-lookup"><span data-stu-id="c17b9-250">Initialize DB with test data</span></span>

<span data-ttu-id="c17b9-251">EF создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-251">EF creates an empty database.</span></span> <span data-ttu-id="c17b9-252">В этом разделе добавляется метод, который вызывается после создания базы данных и заполняет ее тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="c17b9-252">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="c17b9-253">Метод `EnsureCreated` будет использоваться для автоматического создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-253">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="c17b9-254">В [одном из следующих учебников](migrations.md) вы узнаете, как обрабатывать изменения модели с использованием Code First Migrations, что позволяет изменять схему базы данных вместо того, чтобы удалять и повторно создавать ее.</span><span class="sxs-lookup"><span data-stu-id="c17b9-254">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="c17b9-255">В папке *Data* создайте файл с именем `DbInitializer`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="c17b9-255">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="c17b9-256">Предыдущий код проверяет, существует ли база данных:</span><span class="sxs-lookup"><span data-stu-id="c17b9-256">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="c17b9-257">если база данных не найдена,</span><span class="sxs-lookup"><span data-stu-id="c17b9-257">If the database is not found;</span></span>
  * <span data-ttu-id="c17b9-258">она создается и загружается вместе с тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="c17b9-258">It is created and loaded with test data.</span></span> <span data-ttu-id="c17b9-259">Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-259">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="c17b9-260">Если база данных найдена, никакие действия не выполняются.</span><span class="sxs-lookup"><span data-stu-id="c17b9-260">If the database if found, it takes no action.</span></span>

<span data-ttu-id="c17b9-261">Обновите файл *Program.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="c17b9-261">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="c17b9-262">Файл *Program.cs* выполняет следующие действия при запуске приложения:</span><span class="sxs-lookup"><span data-stu-id="c17b9-262">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="c17b9-263">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-263">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="c17b9-264">Вызовите метод `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-264">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="c17b9-265">Удалите контекст по завершении работы метода `Initialize`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="c17b9-265">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="c17b9-266">При первом запуске приложения создается база данных, которая заполняется тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="c17b9-266">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="c17b9-267">Каждый раз при изменении модели данных:</span><span class="sxs-lookup"><span data-stu-id="c17b9-267">Whenever the data model changes:</span></span>

* <span data-ttu-id="c17b9-268">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="c17b9-268">Delete the database.</span></span>
* <span data-ttu-id="c17b9-269">Обновите метод заполнения и запустите заново с новой базой данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-269">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="c17b9-270">В последующих учебниках описывается, как изменить базу данных при изменении модели данных, не прибегая к ее удалению и повторному созданию.</span><span class="sxs-lookup"><span data-stu-id="c17b9-270">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="c17b9-271">При изменении модели данных данные не теряются.</span><span class="sxs-lookup"><span data-stu-id="c17b9-271">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="c17b9-272">Создание контроллера и представлений</span><span class="sxs-lookup"><span data-stu-id="c17b9-272">Create controller and views</span></span>

<span data-ttu-id="c17b9-273">Используйте подсистему формирования шаблонов Visual Studio для добавления контроллера и представлений MVC, которые будут использовать платформу EF для запроса данных и их сохранения.</span><span class="sxs-lookup"><span data-stu-id="c17b9-273">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="c17b9-274">Автоматическое создание методов и представлений операций [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (создание, чтение, обновление и удаление) называется формированием шаблонов.</span><span class="sxs-lookup"><span data-stu-id="c17b9-274">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="c17b9-275">В **обозревателе решений** щелкните правой кнопкой мыши папку `Controllers` и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-275">In **Solution Explorer**, right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="c17b9-276">В диалоговом окне **Добавление шаблона**:</span><span class="sxs-lookup"><span data-stu-id="c17b9-276">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="c17b9-277">Выберите **Контроллер MVC с представлениями, использующий Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-277">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="c17b9-278">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-278">Click **Add**.</span></span> <span data-ttu-id="c17b9-279">Откроется диалоговое окно **добавления контроллера MVC с представлениями с использованием Entity Framework**. ![Шаблон Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="c17b9-279">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="c17b9-280">В разделе **Класс модели** выберите **Student**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-280">In **Model class**, select **Student**.</span></span>
  * <span data-ttu-id="c17b9-281">В разделе **Класс контекста данных** выберите **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-281">In **Data context class**, select **SchoolContext**.</span></span>
  * <span data-ttu-id="c17b9-282">Оставьте предлагаемое по умолчанию имя **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-282">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="c17b9-283">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-283">Click **Add**.</span></span>

<span data-ttu-id="c17b9-284">Подсистема формирования шаблонов Visual Studio создает файл `StudentsController.cs` и набор представлений (файлы `*.cshtml`), которые будут работать с контроллером.</span><span class="sxs-lookup"><span data-stu-id="c17b9-284">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="c17b9-285">Обратите внимание, что контроллер принимает `SchoolContext` в качестве параметра конструктора.</span><span class="sxs-lookup"><span data-stu-id="c17b9-285">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="c17b9-286">Технология внедрения зависимостей ASP.NET Core обеспечивает передачу экземпляра `SchoolContext` в контроллер.</span><span class="sxs-lookup"><span data-stu-id="c17b9-286">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="c17b9-287">Вы настраиваете ее в классе `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-287">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="c17b9-288">Контроллер содержит метод действия `Index`, который отображает всех учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-288">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="c17b9-289">Этот метод получает список учащихся из набора сущностей Students, считывая свойство `Students` экземпляра контекста базы данных:</span><span class="sxs-lookup"><span data-stu-id="c17b9-289">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="c17b9-290">Элементы асинхронного программирования в этом коде рассматриваются далее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="c17b9-290">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="c17b9-291">Представление *Views/Students/Index.cshtml* отображает этот список в таблице:</span><span class="sxs-lookup"><span data-stu-id="c17b9-291">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="c17b9-292">Нажмите клавиши CTRL+F5, чтобы запустить проект, и выберите в меню **Отладка > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-292">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="c17b9-293">Перейдите на вкладку Students (Учащиеся), чтобы просмотреть тестовые данные, добавленные методом `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-293">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="c17b9-294">В зависимости от размеров окна браузера ссылку на вкладку `Students` можно найти вверху страницы или щелкнув значок навигации в правом верхнем углу.</span><span class="sxs-lookup"><span data-stu-id="c17b9-294">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Уменьшенное окно с домашней страницей университета Contoso](intro/_static/home-page-narrow.png)

![Страница указателя учащихся](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="c17b9-297">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="c17b9-297">View the database</span></span>

<span data-ttu-id="c17b9-298">При запуске приложения метод `DbInitializer.Initialize` вызывает `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-298">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="c17b9-299">Платформа EF определила, что база данных отсутствует,</span><span class="sxs-lookup"><span data-stu-id="c17b9-299">EF saw that there was no database:</span></span>

* <span data-ttu-id="c17b9-300">поэтому создала базу данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-300">So it created a database.</span></span>
* <span data-ttu-id="c17b9-301">Код метода `Initialize` заполняет базу данных данными.</span><span class="sxs-lookup"><span data-stu-id="c17b9-301">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="c17b9-302">Для просмотра базы данных в Visual Studio используйте **Обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c17b9-302">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="c17b9-303">Выберите **Обозреватель объектов SQL Server** из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c17b9-303">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="c17b9-304">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-304">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="c17b9-305">Выберите запись `ContosoUniversity1` для имени базы данных, которая находится в строке подключения в файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="c17b9-305">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="c17b9-306">Разверните узел **Таблицы**, чтобы просмотреть представленные в базе таблицы.</span><span class="sxs-lookup"><span data-stu-id="c17b9-306">Expand the **Tables** node to see the tables in the database.</span></span>

![Таблицы в SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="c17b9-308">Щелкните правой кнопкой мыши таблицу **Student** и выберите пункт **Просмотреть данные**, чтобы просмотреть данные в таблице.</span><span class="sxs-lookup"><span data-stu-id="c17b9-308">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![Таблица Student в SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="c17b9-310">Файлы базы данных с расширением `*.mdf` и `*.ldf` располагаются в папке *C:\Users\\\<username>* .</span><span class="sxs-lookup"><span data-stu-id="c17b9-310">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="c17b9-311">Так как `EnsureCreated` вызывается в методе инициализатора, который выполняется при запуске приложения, можно сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="c17b9-311">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="c17b9-312">Внести изменение в класс `Student`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-312">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="c17b9-313">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="c17b9-313">Delete the database.</span></span>
* <span data-ttu-id="c17b9-314">Остановить, а затем запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c17b9-314">Stop, then start the app.</span></span> <span data-ttu-id="c17b9-315">База данных автоматически создается повторно в соответствии с изменением.</span><span class="sxs-lookup"><span data-stu-id="c17b9-315">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="c17b9-316">Например, при добавлении свойства `EmailAddress` в класс `Student` во вновь созданной таблице появится новый столбец `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-316">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="c17b9-317">В представлении класса не будет отображаться новое свойство `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-317">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="c17b9-318">Соглашения</span><span class="sxs-lookup"><span data-stu-id="c17b9-318">Conventions</span></span>

<span data-ttu-id="c17b9-319">Объем кода, написанного для создания полной базы данных платформой EF, сведен к минимуму благодаря использованию соглашений EF.</span><span class="sxs-lookup"><span data-stu-id="c17b9-319">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="c17b9-320">В качестве имен таблиц используются имена свойств `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-320">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="c17b9-321">Для сущностей, на которые не ссылается свойство `DbSet`, в качестве имен таблиц используются имена классов сущностей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-321">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="c17b9-322">В качестве имен столбцов используются имена свойств сущностей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-322">Entity property names are used for column names.</span></span>
* <span data-ttu-id="c17b9-323">Свойства сущности с именами `ID` или `classnameID` распознаются как свойства ПК.</span><span class="sxs-lookup"><span data-stu-id="c17b9-323">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="c17b9-324">Свойство интерпретируется как свойство ВК, если оно называется `<`имя_свойства_навигации`><`имя_свойства_первичного_ключа`>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-324">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="c17b9-325">Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет значение ПК `ID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-325">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="c17b9-326">Свойства ВК также могут называться `<`имя_свойства_первичного_ключа`>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-326">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="c17b9-327">Например, `EnrollmentID`, так как сущность `Enrollment` имеет ПК `EnrollmentID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-327">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="c17b9-328">Стандартное поведение можно переопределить.</span><span class="sxs-lookup"><span data-stu-id="c17b9-328">Conventional behavior can be overridden.</span></span> <span data-ttu-id="c17b9-329">Например, можно явно задать имена таблиц, как было показано ранее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="c17b9-329">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="c17b9-330">Имена столбцов и любое свойство можно задать в качестве ПК или ВК.</span><span class="sxs-lookup"><span data-stu-id="c17b9-330">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="c17b9-331">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="c17b9-331">Asynchronous code</span></span>

<span data-ttu-id="c17b9-332">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="c17b9-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="c17b9-333">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="c17b9-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="c17b9-334">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="c17b9-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="c17b9-335">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="c17b9-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="c17b9-336">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="c17b9-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="c17b9-337">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="c17b9-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="c17b9-338">Во время выполнения асинхронный код использует немного больше служебных ресурсов, однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="c17b9-338">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="c17b9-339">В следующем коде `async`, `Task<T>`, `await` и `ToListAsync` обеспечивают асинхронное выполнение кода.</span><span class="sxs-lookup"><span data-stu-id="c17b9-339">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="c17b9-340">Ключевое слово `async` указывает компилятору создавать обратные вызовы для частей тела метода и автоматически создавать возвращаемый объект `Task<IActionResult>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-340">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="c17b9-341">Тип возвращаемого значения `Task<IActionResult>` представляет текущую операцию с помощью результата типа `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-341">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="c17b9-342">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="c17b9-342">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="c17b9-343">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="c17b9-343">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="c17b9-344">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="c17b9-344">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="c17b9-345">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-345">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="c17b9-346">При написании асинхронного кода, который использует EF, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="c17b9-346">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="c17b9-347">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="c17b9-347">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="c17b9-348">К ним относятся, например, `ToListAsync`, `SingleOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-348">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="c17b9-349">В их число не входят, например, инструкции, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-349">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="c17b9-350">Контекст EF не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="c17b9-350">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="c17b9-351">При вызове любого асинхронного метода EF всегда используйте ключевое слово `await`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-351">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="c17b9-352">Чтобы воспользоваться преимуществами в производительности, которые обеспечивает асинхронный код, убедитесь, что все используемые пакеты библиотек также используют асинхронный код при вызове любых методов EF, выполняющих запросы к базе данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-352">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="c17b9-353">Дополнительные сведения об асинхронных методах программирования в .NET см. в разделе [Обзор асинхронной модели](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="c17b9-353">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="c17b9-354">Ограничение полученных сущностей</span><span class="sxs-lookup"><span data-stu-id="c17b9-354">Limit entities fetched</span></span>

<span data-ttu-id="c17b9-355">Сведения об ограничении числа сущностей, возвращаемых в запросе, см. в статье [Важные замечания о производительности](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="c17b9-355">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="c17b9-356">В следующем учебнике описано, как выполнять основные операции CRUD (создание, чтение, обновление и удаление).</span><span class="sxs-lookup"><span data-stu-id="c17b9-356">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c17b9-357">Реализация базовых функций CRUD</span><span class="sxs-lookup"><span data-stu-id="c17b9-357">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="c17b9-358">На примере веб-приложения Contoso University показано, как создавать веб-приложения MVC ASP.NET Core 2.2 с помощью Entity Framework (EF) Core 2.2 и Visual Studio 2017 или 2019.</span><span class="sxs-lookup"><span data-stu-id="c17b9-358">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="c17b9-359">Это руководство не было обновлено для использования с версией ASP.NET Core 3.1.</span><span class="sxs-lookup"><span data-stu-id="c17b9-359">This tutorial has not been updated for ASP.NET Core 3.1.</span></span> <span data-ttu-id="c17b9-360">Оно обновлено для использования с [ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0).</span><span class="sxs-lookup"><span data-stu-id="c17b9-360">It has been updated for [ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0).</span></span>

<span data-ttu-id="c17b9-361">В этом примере приложения реализуется веб-сайт вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="c17b9-361">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="c17b9-362">На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-362">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="c17b9-363">Это первый учебник из серии, в котором с самого начала описывается построение примера приложения для университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="c17b9-363">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c17b9-364">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="c17b9-364">Prerequisites</span></span>

* [<span data-ttu-id="c17b9-365">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="c17b9-365">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="c17b9-366">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с указанными ниже рабочими нагрузками.</span><span class="sxs-lookup"><span data-stu-id="c17b9-366">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="c17b9-367">Рабочая нагрузка **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="c17b9-367">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="c17b9-368">Рабочая нагрузка **Кроссплатформенная разработка .NET Core**</span><span class="sxs-lookup"><span data-stu-id="c17b9-368">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="c17b9-369">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="c17b9-369">Troubleshooting</span></span>

<span data-ttu-id="c17b9-370">Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="c17b9-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="c17b9-371">Список распространенных ошибок и способы их устранения см. в [разделе "Устранение неполадок" последнего руководства серии](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="c17b9-371">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="c17b9-372">Если вам не удалось найти нужную информацию, вы можете задать вопрос на сайте StackOverflow.com в разделах, посвященных [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="c17b9-372">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="c17b9-373">Эта серия включает в себя 10 учебников, содержание каждого из которых базируется на предыдущих учебниках.</span><span class="sxs-lookup"><span data-stu-id="c17b9-373">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="c17b9-374">После успешного завершения каждого руководства рекомендуется сохранять копию проекта.</span><span class="sxs-lookup"><span data-stu-id="c17b9-374">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="c17b9-375">Таким образом, при возникновении проблем вы сможете вернуться к предыдущему учебнику, а не к началу серии.</span><span class="sxs-lookup"><span data-stu-id="c17b9-375">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="c17b9-376">Веб-приложение Contoso University</span><span class="sxs-lookup"><span data-stu-id="c17b9-376">Contoso University web app</span></span>

<span data-ttu-id="c17b9-377">В рамках этих учебников вы будете создавать приложение, которое представляет собой простой веб-сайт университета.</span><span class="sxs-lookup"><span data-stu-id="c17b9-377">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="c17b9-378">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="c17b9-378">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="c17b9-379">Будет создано несколько экранов.</span><span class="sxs-lookup"><span data-stu-id="c17b9-379">Here are a few of the screens you'll create.</span></span>

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="c17b9-382">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="c17b9-382">Create web app</span></span>

* <span data-ttu-id="c17b9-383">Запустите Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c17b9-383">Open Visual Studio.</span></span>

* <span data-ttu-id="c17b9-384">В меню **Файл** выберите пункт **Создать > Проект**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-384">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="c17b9-385">В области слева выберите **Установленные > Visual C# > Интернет**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-385">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="c17b9-386">Выберите шаблон проекта **Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-386">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="c17b9-387">Введите имя **ContosoUniversity** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-387">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Диалоговое окно создания нового проекта](intro/_static/new-project2.png)

* <span data-ttu-id="c17b9-389">Дождитесь появления диалогового окна **Создание веб-приложения ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-389">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="c17b9-390">Выберите **.NET Core**, **ASP.NET Core 2.2** и шаблон **Веб-приложение (модель — представление — контроллер)** .</span><span class="sxs-lookup"><span data-stu-id="c17b9-390">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="c17b9-391">Убедитесь, что для параметра **Проверка подлинности** задано значение **Без проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-391">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="c17b9-392">Нажмите кнопку **ОК**</span><span class="sxs-lookup"><span data-stu-id="c17b9-392">Select **OK**</span></span>

  ![Диалоговое окно "Создание проекта ASP.NET Core"](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="c17b9-394">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="c17b9-394">Set up the site style</span></span>

<span data-ttu-id="c17b9-395">Выполните незначительную настройку меню, макета и домашней страницы сайта.</span><span class="sxs-lookup"><span data-stu-id="c17b9-395">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="c17b9-396">Откройте файл *Views/Shared/_Layout.cshtml* и внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="c17b9-396">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="c17b9-397">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="c17b9-397">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="c17b9-398">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="c17b9-398">There are three occurrences.</span></span>

* <span data-ttu-id="c17b9-399">Добавьте пункты меню **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Privacy** (Конфиденциальность).</span><span class="sxs-lookup"><span data-stu-id="c17b9-399">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="c17b9-400">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="c17b9-400">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="c17b9-401">Замените содержимое файла *Views/Home/Index.cshtml* следующим кодом, который заменяет текст о ASP.NET и MVC описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="c17b9-401">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="c17b9-402">Нажмите клавиши CTRL+F5, чтобы запустить проект, и выберите в меню **Отладка > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-402">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="c17b9-403">Откроется домашняя страница, на которой будут представлены созданные в рамках этих учебников вкладки.</span><span class="sxs-lookup"><span data-stu-id="c17b9-403">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Домашняя страница университета Contoso](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="c17b9-405">Сведения о пакетах NuGet EF Core</span><span class="sxs-lookup"><span data-stu-id="c17b9-405">About EF Core NuGet packages</span></span>

<span data-ttu-id="c17b9-406">Чтобы реализовать поддержку EF Core в проекте, установите поставщик целевой базы данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-406">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="c17b9-407">В этом учебнике используется SQL Server, для которого требуется пакет поставщика [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="c17b9-407">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="c17b9-408">Этот пакет входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), поэтому ссылаться на него не нужно.</span><span class="sxs-lookup"><span data-stu-id="c17b9-408">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="c17b9-409">Этот пакет EF SQL Server и его зависимости (`Microsoft.EntityFrameworkCore` и `Microsoft.EntityFrameworkCore.Relational`) обеспечивают поддержку среды выполнения для платформы EF.</span><span class="sxs-lookup"><span data-stu-id="c17b9-409">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="c17b9-410">Пакет средств будет добавлен позднее в рамках учебника [Миграции](migrations.md).</span><span class="sxs-lookup"><span data-stu-id="c17b9-410">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="c17b9-411">Дополнительные сведения о других поставщиках баз данных, которые доступны для платформы Entity Framework Core, см. в разделе [Поставщики баз данных](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="c17b9-411">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="c17b9-412">Создание модели данных</span><span class="sxs-lookup"><span data-stu-id="c17b9-412">Create the data model</span></span>

<span data-ttu-id="c17b9-413">Теперь необходимо создать классы сущностей для приложения университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="c17b9-413">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="c17b9-414">Для начала создаются следующие три сущности.</span><span class="sxs-lookup"><span data-stu-id="c17b9-414">You'll start with the following three entities.</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="c17b9-416">Между сущностями `Student` и `Enrollment`, а также между сущностями `Course` и `Enrollment` существует отношение "один ко многим".</span><span class="sxs-lookup"><span data-stu-id="c17b9-416">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="c17b9-417">Другими словами, учащийся может быть зарегистрирован в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="c17b9-417">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="c17b9-418">В следующих разделах создаются классы для каждой из этих сущностей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-418">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="c17b9-419">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="c17b9-419">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

<span data-ttu-id="c17b9-421">В папке *Models* создайте файл класса с именем *Student.cs* и замените код шаблона следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="c17b9-421">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="c17b9-422">Свойство `ID` будет использоваться в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="c17b9-422">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="c17b9-423">По умолчанию платформа Entity Framework интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-423">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="c17b9-424">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="c17b9-424">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="c17b9-425">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="c17b9-425">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="c17b9-426">В этом случае свойство `Enrollments` сущности `Student entity` содержит все сущности `Enrollment`, которые связаны с этой сущностью `Student`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-426">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="c17b9-427">Другими словами, если со строкой `Student` в базе данных связаны две строки `Enrollment` (строки, в столбце внешнего ключа StudentID которых содержится первичный ключ этого учащегося), в этой сущности `Student` свойство навигации `Enrollments` будет содержать две этих сущности `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-427">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="c17b9-428">Если свойство навигации может содержать несколько сущностей (как в отношениях "многие ко многим" или "один ко многим"), оно должно иметь тип списка, допускающий добавление, удаление и обновление записей, такой как `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-428">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="c17b9-429">Вы можете указать тип `ICollection<T>` либо, например, тип `List<T>` или `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-429">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="c17b9-430">Если указан тип `ICollection<T>`, платформа EF по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-430">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="c17b9-431">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="c17b9-431">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="c17b9-433">В папке *Models* создайте файл *Enrollment.cs* и замените существующий код следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c17b9-433">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="c17b9-434">Свойство `EnrollmentID` будет использоваться в качестве первичного ключа. В этой сущности используется шаблон `classnameID` вместо `ID`, как в сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-434">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="c17b9-435">Как правило, следует выбирать один шаблон, который будет использоваться в рамках всей модели данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-435">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="c17b9-436">В этом случае демонстрируется возможность использования любого из шаблонов.</span><span class="sxs-lookup"><span data-stu-id="c17b9-436">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="c17b9-437">В [одном из следующих учебников](inheritance.md) вы узнаете, за счет чего использование идентификатора без имени класса позволяет упростить реализацию наследования в модели данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-437">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="c17b9-438">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-438">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="c17b9-439">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade` допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="c17b9-439">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="c17b9-440">Оценка со значением null отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="c17b9-440">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="c17b9-441">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-441">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="c17b9-442">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому это свойство может содержать одну сущность `Student` (в отличие от представленного ранее свойства навигации `Student.Enrollments`, которое может содержать несколько сущностей `Enrollment`).</span><span class="sxs-lookup"><span data-stu-id="c17b9-442">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="c17b9-443">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-443">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="c17b9-444">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-444">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="c17b9-445">Платформа Entity Framework интерпретирует свойство как свойство внешнего ключа, если оно имеет имя `<navigation property name><primary key property name>` (например, `StudentID` для свойства навигации `Student`, поскольку сущность `Student` имеет первичный ключ `ID`).</span><span class="sxs-lookup"><span data-stu-id="c17b9-445">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="c17b9-446">Свойства внешнего ключа также могут называться просто `<primary key property name>` (например, `CourseID` поскольку сущность `Course` имеет первичный ключ `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="c17b9-446">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="c17b9-447">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="c17b9-447">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="c17b9-449">В папке *Models* создайте файл *Course.cs* и замените существующий код следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c17b9-449">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="c17b9-450">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="c17b9-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="c17b9-451">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="c17b9-452">Более подробное описание атрибута `DatabaseGenerated` будет приведено в [одном из следующих учебников](complex-data-model.md) этой серии.</span><span class="sxs-lookup"><span data-stu-id="c17b9-452">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="c17b9-453">Фактически, этот атрибут позволяет ввести первичный ключ для курса, а не использовать базу данных, чтобы создать его.</span><span class="sxs-lookup"><span data-stu-id="c17b9-453">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="c17b9-454">Создание контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c17b9-454">Create the database context</span></span>

<span data-ttu-id="c17b9-455">Контекст базы данных — это основной класс, который координирует функциональные возможности Entity Framework для заданной модели данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-455">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="c17b9-456">Этот класс создается путем наследования от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-456">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="c17b9-457">В коде указываются сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-457">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="c17b9-458">Также вы можете настроить реакцию платформы Entity Framework на некоторые события.</span><span class="sxs-lookup"><span data-stu-id="c17b9-458">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="c17b9-459">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-459">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="c17b9-460">В папке проекта создайте папку *Data*.</span><span class="sxs-lookup"><span data-stu-id="c17b9-460">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="c17b9-461">В папке *Data* создайте новый файл класса с именем *SchoolContext.cs* и замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c17b9-461">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="c17b9-462">Этот код создает свойство `DbSet` для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-462">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="c17b9-463">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="c17b9-463">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c17b9-464">Вы можете опустить инструкции `DbSet<Enrollment>` и `DbSet<Course>`. Это не нарушит функциональность.</span><span class="sxs-lookup"><span data-stu-id="c17b9-464">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="c17b9-465">Платформа Entity Framework включает эти инструкции неявно, поскольку сущность `Student` ссылается на сущность `Enrollment`, а `Enrollment` ссылается на сущность `Course`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-465">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="c17b9-466">При создании базы данных платформа EF создает таблицы с именами, соответствующими именам свойств в `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-466">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="c17b9-467">Имена свойств для коллекций, как правило, задаются во множественном числе (например, Students вместо Student), однако единого мнения по поводу присвоения имен во множественном числе таблицам среди разработчиков не существует.</span><span class="sxs-lookup"><span data-stu-id="c17b9-467">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="c17b9-468">В этих учебниках вместо принятого по умолчанию способа таблицам в DbContext присваиваются имена в единственном числе.</span><span class="sxs-lookup"><span data-stu-id="c17b9-468">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="c17b9-469">Чтобы сделать это, добавьте выделенный ниже код после последнего свойства DbSet.</span><span class="sxs-lookup"><span data-stu-id="c17b9-469">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="c17b9-470">Выполните сборку проекта, чтобы проверить его на ошибки компиляции.</span><span class="sxs-lookup"><span data-stu-id="c17b9-470">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="c17b9-471">Регистрация SchoolContext</span><span class="sxs-lookup"><span data-stu-id="c17b9-471">Register the SchoolContext</span></span>

<span data-ttu-id="c17b9-472">ASP.NET Core по умолчанию реализует технологию [внедрения зависимостей](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="c17b9-472">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="c17b9-473">С помощью внедрения зависимостей службы (например, контекст базы данных EF) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="c17b9-473">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="c17b9-474">Затем компоненты, которые используют эти службы (например, контроллеры MVC), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="c17b9-474">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c17b9-475">Код конструктора контроллера, который получает экземпляр контекста, будет приведен позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="c17b9-475">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="c17b9-476">Чтобы зарегистрировать `SchoolContext` как службу, откройте файл *Startup.cs* и добавьте выделенные строки в метод `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-476">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="c17b9-477">Имя строки подключения передается в контекст путем вызова метода для объекта `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-477">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="c17b9-478">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="c17b9-478">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="c17b9-479">Добавьте инструкции `using` для пространств имен `ContosoUniversity.Data` и `Microsoft.EntityFrameworkCore`, после чего выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="c17b9-479">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="c17b9-480">Откройте файл *appsettings.json* и добавьте строку подключения, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="c17b9-480">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="c17b9-481">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="c17b9-481">SQL Server Express LocalDB</span></span>

<span data-ttu-id="c17b9-482">Строка подключения указывает на базу данных SQL Server LocalDB.</span><span class="sxs-lookup"><span data-stu-id="c17b9-482">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="c17b9-483">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в производственной среде.</span><span class="sxs-lookup"><span data-stu-id="c17b9-483">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="c17b9-484">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="c17b9-484">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="c17b9-485">По умолчанию база данных LocalDB создает файлы *.mdf* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-485">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="c17b9-486">Инициализация базы данных с тестовыми данными</span><span class="sxs-lookup"><span data-stu-id="c17b9-486">Initialize DB with test data</span></span>

<span data-ttu-id="c17b9-487">Платформа Entity Framework создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-487">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="c17b9-488">В этом разделе вы напишете метод, который вызывается после создания базы данных и заполняет ее тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="c17b9-488">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="c17b9-489">Здесь будет использоваться метод `EnsureCreated` для автоматического создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-489">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="c17b9-490">В [одном из следующих учебников](migrations.md) вы узнаете, как обрабатывать изменения модели с использованием Code First Migrations, что позволяет изменять схему базы данных вместо того, чтобы удалять и повторно создавать ее.</span><span class="sxs-lookup"><span data-stu-id="c17b9-490">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="c17b9-491">В папке *Data* создайте новый файл класса с именем *DbInitializer.cs* и замените код шаблона следующим кодом, который обеспечивает создание базы данных и загрузку в нее тестовых данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-491">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="c17b9-492">Этот код проверяет, добавлены ли в базу данных учащиеся. Если нет, база данных считается новой и заполняется тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="c17b9-492">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="c17b9-493">Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-493">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="c17b9-494">В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее поведение при запуске приложения:</span><span class="sxs-lookup"><span data-stu-id="c17b9-494">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="c17b9-495">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-495">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="c17b9-496">Вызов метода инициализации с передачей ему контекста.</span><span class="sxs-lookup"><span data-stu-id="c17b9-496">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="c17b9-497">Высвобождение контекста после завершения работы метода заполнения.</span><span class="sxs-lookup"><span data-stu-id="c17b9-497">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="c17b9-498">При первом запуске приложения будет создана и заполнена тестовыми данными необходимая для работы база данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-498">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="c17b9-499">При каждом изменении модели данных:</span><span class="sxs-lookup"><span data-stu-id="c17b9-499">Whenever you change the data model:</span></span>

 * <span data-ttu-id="c17b9-500">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="c17b9-500">Delete the database.</span></span>
 * <span data-ttu-id="c17b9-501">Обновите метод заполнения и запустите заново с новой базой данных таким же образом.</span><span class="sxs-lookup"><span data-stu-id="c17b9-501">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="c17b9-502">В следующих учебниках вы узнаете, как изменить базу данных при изменении модели данных, не прибегая к ее удалению и повторному созданию.</span><span class="sxs-lookup"><span data-stu-id="c17b9-502">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="c17b9-503">Создание контроллера и представлений</span><span class="sxs-lookup"><span data-stu-id="c17b9-503">Create controller and views</span></span>

<span data-ttu-id="c17b9-504">В этом разделе подсистема формирования шаблонов Visual Studio используется для добавления контроллера и представлений MVC, которые будут использовать платформу EF для запроса данных и их сохранения.</span><span class="sxs-lookup"><span data-stu-id="c17b9-504">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="c17b9-505">Автоматическое создание методов и представлений операций CRUD (создание, чтение, обновление и удаление) называется формированием шаблонов.</span><span class="sxs-lookup"><span data-stu-id="c17b9-505">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="c17b9-506">Формирование шаблонов отличается от создания кода тем, что шаблонный код является отправной точкой и может изменяться в соответствии с потребностями, тогда как сформированный код обычно не изменяется.</span><span class="sxs-lookup"><span data-stu-id="c17b9-506">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="c17b9-507">В тех случаях, когда требуется настроить созданный код в соответствии с внесенными изменениями, вы можете использовать разделяемые классы или повторно создать код.</span><span class="sxs-lookup"><span data-stu-id="c17b9-507">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="c17b9-508">Щелкните правой кнопкой мыши папку **Контроллеры** в **обозревателе решений** и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-508">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="c17b9-509">В диалоговом окне **Добавление шаблона**:</span><span class="sxs-lookup"><span data-stu-id="c17b9-509">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="c17b9-510">Выберите **Контроллер MVC с представлениями, использующий Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-510">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="c17b9-511">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-511">Click **Add**.</span></span> <span data-ttu-id="c17b9-512">Откроется диалоговое окно **добавления контроллера MVC с представлениями с использованием Entity Framework**. ![Шаблон Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="c17b9-512">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="c17b9-513">В разделе **Класс модели** выберите **Student**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-513">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="c17b9-514">В разделе **Класс контекста данных** выберите **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-514">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="c17b9-515">Оставьте предлагаемое по умолчанию имя **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-515">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="c17b9-516">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-516">Click **Add**.</span></span>

<span data-ttu-id="c17b9-517">Подсистема формирования шаблонов Visual Studio создает файл *StudentsController.cs* и набор представлений (файлы с расширением *.cshtml*), которые будут работать с контроллером.</span><span class="sxs-lookup"><span data-stu-id="c17b9-517">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="c17b9-518">Обратите внимание, что контроллер принимает `SchoolContext` в качестве параметра конструктора.</span><span class="sxs-lookup"><span data-stu-id="c17b9-518">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="c17b9-519">Технология внедрения зависимостей ASP.NET Core обеспечивает передачу экземпляра `SchoolContext` в контроллер.</span><span class="sxs-lookup"><span data-stu-id="c17b9-519">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="c17b9-520">Она настроена в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c17b9-520">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="c17b9-521">Контроллер содержит метод действия `Index`, который отображает всех учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-521">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="c17b9-522">Этот метод получает список учащихся из набора сущностей Students, считывая свойство `Students` экземпляра контекста базы данных:</span><span class="sxs-lookup"><span data-stu-id="c17b9-522">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="c17b9-523">Позднее в этом учебнике будут описаны элементы асинхронного программирования в этом коде.</span><span class="sxs-lookup"><span data-stu-id="c17b9-523">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="c17b9-524">Представление *Views/Students/Index.cshtml* отображает этот список в таблице:</span><span class="sxs-lookup"><span data-stu-id="c17b9-524">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="c17b9-525">Нажмите клавиши CTRL+F5, чтобы запустить проект, и выберите в меню **Отладка > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="c17b9-525">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="c17b9-526">Перейдите на вкладку Students (Учащиеся), чтобы просмотреть тестовые данные, добавленные методом `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-526">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="c17b9-527">В зависимости от размеров окна браузера ссылку на вкладку `Students` можно найти вверху страницы или щелкнув значок навигации в правом верхнем углу.</span><span class="sxs-lookup"><span data-stu-id="c17b9-527">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Уменьшенное окно с домашней страницей университета Contoso](intro/_static/home-page-narrow.png)

![Страница указателя учащихся](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="c17b9-530">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="c17b9-530">View the database</span></span>

<span data-ttu-id="c17b9-531">При запуске приложения метод `DbInitializer.Initialize` вызывает метод `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-531">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="c17b9-532">Платформа EF определяет, что база данных отсутствует, и создает ее, после чего код в оставшейся части метода `Initialize` заполняет базу данными.</span><span class="sxs-lookup"><span data-stu-id="c17b9-532">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="c17b9-533">Для просмотра базы данных в Visual Studio можно использовать **обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c17b9-533">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="c17b9-534">Закройте браузер.</span><span class="sxs-lookup"><span data-stu-id="c17b9-534">Close the browser.</span></span>

<span data-ttu-id="c17b9-535">Если окно SSOX не открылось, выберите его в меню **Вид** Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c17b9-535">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="c17b9-536">В окне SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных**, а затем щелкните запись базы данных, имя которой указано в строке подключения в вашем файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="c17b9-536">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>

<span data-ttu-id="c17b9-537">Разверните узел **Таблицы**, чтобы просмотреть представленные в базе таблицы.</span><span class="sxs-lookup"><span data-stu-id="c17b9-537">Expand the **Tables** node to see the tables in the database.</span></span>

![Таблицы в SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="c17b9-539">Щелкните правой кнопкой мыши таблицу **Student** и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в базу данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-539">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Таблица Student в SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="c17b9-541">Файлы базы данных с расширением *MDF* и *LDF* располагаются в папке *C:\Users\\\<username>* .</span><span class="sxs-lookup"><span data-stu-id="c17b9-541">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="c17b9-542">Поскольку вы вызываете метод `EnsureCreated` в методе инициализатора, который выполняется при запуске приложения, теперь вы можете внести изменения в класс `Student`, удалить базу данных и снова запустить приложение. После этого база данных будет создана повторно в соответствии с внесенными изменениями.</span><span class="sxs-lookup"><span data-stu-id="c17b9-542">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="c17b9-543">Например, при добавлении свойства `EmailAddress` в класс `Student` во вновь созданной таблице появится столбец `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-543">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="c17b9-544">Соглашения</span><span class="sxs-lookup"><span data-stu-id="c17b9-544">Conventions</span></span>

<span data-ttu-id="c17b9-545">Чтобы платформа Entity Framework автоматически создавала полную базу данных на основе принятых соглашений и допущений, потребуется написать минимальный объем кода.</span><span class="sxs-lookup"><span data-stu-id="c17b9-545">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="c17b9-546">В качестве имен таблиц используются имена свойств `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-546">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="c17b9-547">Для сущностей, на которые не ссылается свойство `DbSet`, в качестве имен таблиц используются имена классов сущностей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-547">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="c17b9-548">В качестве имен столбцов используются имена свойств сущностей.</span><span class="sxs-lookup"><span data-stu-id="c17b9-548">Entity property names are used for column names.</span></span>
* <span data-ttu-id="c17b9-549">Свойства сущностей с именем ID или classnameID распознаются как свойства первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="c17b9-549">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="c17b9-550">Свойство интерпретируется как свойство внешнего ключа, если оно имеет имя *\<navigation property name>\<primary key property name>* (например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`).</span><span class="sxs-lookup"><span data-stu-id="c17b9-550">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="c17b9-551">Свойства внешнего ключа также могут называться просто *\<primary key property name>* (например `EnrollmentID`, поскольку сущность `Enrollment` имеет первичный ключ `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="c17b9-551">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="c17b9-552">Стандартное поведение можно переопределить.</span><span class="sxs-lookup"><span data-stu-id="c17b9-552">Conventional behavior can be overridden.</span></span> <span data-ttu-id="c17b9-553">Например, можно явно задать имена таблиц, как было показано ранее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="c17b9-553">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="c17b9-554">Также можно указать имена столбцов и задать любое свойство в качестве первичного или внешнего ключа, как будет показано в [одном из следующих учебников](complex-data-model.md) этой серии.</span><span class="sxs-lookup"><span data-stu-id="c17b9-554">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="c17b9-555">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="c17b9-555">Asynchronous code</span></span>

<span data-ttu-id="c17b9-556">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="c17b9-556">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="c17b9-557">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="c17b9-557">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="c17b9-558">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="c17b9-558">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="c17b9-559">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="c17b9-559">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="c17b9-560">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="c17b9-560">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="c17b9-561">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="c17b9-561">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="c17b9-562">Во время выполнения асинхронный код использует немного больше служебных ресурсов, однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="c17b9-562">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="c17b9-563">В следующем коде для асинхронного выполнения используются ключевое слово `async`, возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-563">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="c17b9-564">Ключевое слово `async` указывает компилятору создавать обратные вызовы для частей тела метода и автоматически создавать возвращаемый объект `Task<IActionResult>`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-564">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="c17b9-565">Тип возвращаемого значения `Task<IActionResult>` представляет текущую операцию с помощью результата типа `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-565">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="c17b9-566">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="c17b9-566">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="c17b9-567">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="c17b9-567">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="c17b9-568">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="c17b9-568">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="c17b9-569">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-569">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="c17b9-570">При написании асинхронного кода, который использует Entity Framework, необходимо учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="c17b9-570">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="c17b9-571">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="c17b9-571">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="c17b9-572">К ним относятся, например, `ToListAsync`, `SingleOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-572">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="c17b9-573">В их число не входят, например, инструкции, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-573">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="c17b9-574">Контекст EF не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="c17b9-574">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="c17b9-575">При вызове любого асинхронного метода EF всегда используйте ключевое слово `await`.</span><span class="sxs-lookup"><span data-stu-id="c17b9-575">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="c17b9-576">Если вы хотите использовать преимущества в производительности, которые обеспечивает асинхронный код, убедитесь, что все используемые пакеты библиотек (например, для разбиения на страницы) также используют асинхронный код при вызове любых методов Entity Framework, выполняющих запросы к базе данных.</span><span class="sxs-lookup"><span data-stu-id="c17b9-576">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="c17b9-577">Дополнительные сведения об асинхронных методах программирования в .NET см. в разделе [Обзор асинхронной модели](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="c17b9-577">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c17b9-578">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="c17b9-578">Next steps</span></span>

<span data-ttu-id="c17b9-579">В следующем учебнике описано, как выполнять основные операции CRUD (создание, чтение, обновление и удаление).</span><span class="sxs-lookup"><span data-stu-id="c17b9-579">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c17b9-580">Реализация базовых функций CRUD</span><span class="sxs-lookup"><span data-stu-id="c17b9-580">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
