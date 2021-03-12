---
title: Введение в Razor Pages в ASP.NET Core
author: Rick-Anderson
description: Здесь приведены сведения о применении в ASP.NET Core функции Razor Pages, которая делает создание кодов сценариев для страниц проще и эффективнее по сравнению с MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: 78b192cb2240046d16b1b766954ed4ca5229d888
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586713"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="0eb74-103">Введение в Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0eb74-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="0eb74-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak)</span><span class="sxs-lookup"><span data-stu-id="0eb74-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="0eb74-105">Razor Pages делает создание кодов сценариев для страниц проще и эффективнее по сравнению с использованием контроллеров и представлений.</span><span class="sxs-lookup"><span data-stu-id="0eb74-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="0eb74-106">Если вам нужно руководство, использующее подход "модель-представление-контроллер", см. статью [Начало работы с MVC в ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="0eb74-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="0eb74-107">Этот документ содержит вводные сведения о Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0eb74-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="0eb74-108">Это не пошаговое руководство.</span><span class="sxs-lookup"><span data-stu-id="0eb74-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="0eb74-109">Если некоторые разделы покажутся вам слишком сложными, см. [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0eb74-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="0eb74-110">Общие сведения об ASP.NET Core см. в разделе [Введение в ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="0eb74-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0eb74-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="0eb74-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="0eb74-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb74-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eb74-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb74-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0eb74-114">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0eb74-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="0eb74-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb74-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eb74-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb74-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0eb74-117">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0eb74-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="0eb74-118">Создание проекта Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eb74-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb74-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0eb74-120">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0eb74-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eb74-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb74-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0eb74-122">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0eb74-123">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0eb74-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0eb74-124">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0eb74-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="0eb74-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-125">Razor Pages</span></span>

<span data-ttu-id="0eb74-126">Razor Pages активируется в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="0eb74-127">Рассмотрим простую страницу: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="0eb74-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="0eb74-128">Приведенный выше код выглядит как [Razor файл представления](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="0eb74-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="0eb74-129">Он отличается от него только директивой [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="0eb74-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="0eb74-130">Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер.</span><span class="sxs-lookup"><span data-stu-id="0eb74-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="0eb74-131">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="0eb74-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="0eb74-132">`@page` влияет на поведение всех остальных конструкций [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="0eb74-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="0eb74-133">Имена файлов Razor Pages имеют суффикс *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="0eb74-134">Похожая страница с использованием класса `PageModel` показана в следующих двух файлах.</span><span class="sxs-lookup"><span data-stu-id="0eb74-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="0eb74-135">Файл *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="0eb74-136">Модель страницы *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="0eb74-137">Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *CS*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="0eb74-138">Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="0eb74-139">Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="0eb74-140">Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе.</span><span class="sxs-lookup"><span data-stu-id="0eb74-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="0eb74-141">В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="0eb74-142">Имя файла и путь</span><span class="sxs-lookup"><span data-stu-id="0eb74-142">File name and path</span></span>               | <span data-ttu-id="0eb74-143">Соответствующий URL</span><span class="sxs-lookup"><span data-stu-id="0eb74-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="0eb74-144">*/Pages/index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="0eb74-145">`/` или `/Index`</span><span class="sxs-lookup"><span data-stu-id="0eb74-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="0eb74-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="0eb74-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="0eb74-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="0eb74-149">`/Store` или `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="0eb74-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="0eb74-150">Примечания.</span><span class="sxs-lookup"><span data-stu-id="0eb74-150">Notes:</span></span>

* <span data-ttu-id="0eb74-151">Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="0eb74-152">Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="0eb74-153">Создание простой формы</span><span class="sxs-lookup"><span data-stu-id="0eb74-153">Write a basic form</span></span>

<span data-ttu-id="0eb74-154">Razor Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения.</span><span class="sxs-lookup"><span data-stu-id="0eb74-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="0eb74-155">[Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0eb74-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="0eb74-156">Рассмотрим страницу с простой формой связи для модели `Contact`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="0eb74-157">В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="0eb74-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="0eb74-158">Для работы с базой данных в памяти требуется пакет NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="0eb74-159">Модель данных:</span><span class="sxs-lookup"><span data-stu-id="0eb74-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="0eb74-160">Контекст базы данных:</span><span class="sxs-lookup"><span data-stu-id="0eb74-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="0eb74-161">Файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="0eb74-162">Модель страницы *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="0eb74-163">Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.</span><span class="sxs-lookup"><span data-stu-id="0eb74-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="0eb74-164">Класс `PageModel` позволяет разделять логику страницы и ее представление.</span><span class="sxs-lookup"><span data-stu-id="0eb74-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="0eb74-165">Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации.</span><span class="sxs-lookup"><span data-stu-id="0eb74-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="0eb74-166">Это разделение позволяет:</span><span class="sxs-lookup"><span data-stu-id="0eb74-166">This separation allows:</span></span>

* <span data-ttu-id="0eb74-167">управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0eb74-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="0eb74-168">Модульное тестирование</span><span class="sxs-lookup"><span data-stu-id="0eb74-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="0eb74-169">Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму).</span><span class="sxs-lookup"><span data-stu-id="0eb74-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="0eb74-170">Можно добавить методы обработчика для любой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="0eb74-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="0eb74-171">Наиболее распространенные обработчики</span><span class="sxs-lookup"><span data-stu-id="0eb74-171">The most common handlers are:</span></span>

* <span data-ttu-id="0eb74-172">`OnGet` — инициализация необходимого для страницы состояния.</span><span class="sxs-lookup"><span data-stu-id="0eb74-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="0eb74-173">В приведенном выше коде метод `OnGet` отображает страницу Razor *CreateModel.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="0eb74-174">`OnPost` — обработка отправленных через форму данных.</span><span class="sxs-lookup"><span data-stu-id="0eb74-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="0eb74-175">Суффикс `Async` не является обязательным, но часто используется для асинхронных функций.</span><span class="sxs-lookup"><span data-stu-id="0eb74-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="0eb74-176">Этот код типичен для Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0eb74-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="0eb74-177">Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:</span><span class="sxs-lookup"><span data-stu-id="0eb74-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="0eb74-178">Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.</span><span class="sxs-lookup"><span data-stu-id="0eb74-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="0eb74-179">Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation) и результаты действий, одинаково работают с контроллерами и Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0eb74-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="0eb74-180">Предыдущий метод `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="0eb74-181">Простая схема `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="0eb74-182">Проверка на наличие ошибок проверки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-182">Check for validation errors.</span></span>

* <span data-ttu-id="0eb74-183">Если ошибок нет, сохранение данных и перенаправление.</span><span class="sxs-lookup"><span data-stu-id="0eb74-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="0eb74-184">Если есть ошибки, отображение страницы с сообщениями проверки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="0eb74-185">Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.</span><span class="sxs-lookup"><span data-stu-id="0eb74-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="0eb74-186">Файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="0eb74-187">Преобразованный HTML из *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="0eb74-188">В приведенном выше коде, разместив форму:</span><span class="sxs-lookup"><span data-stu-id="0eb74-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="0eb74-189">С допустимыми данными:</span><span class="sxs-lookup"><span data-stu-id="0eb74-189">With valid data:</span></span>

  * <span data-ttu-id="0eb74-190">Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="0eb74-191">`RedirectToPage` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="0eb74-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="0eb74-193">Является результатом действия.</span><span class="sxs-lookup"><span data-stu-id="0eb74-193">Is an action result.</span></span>
    * <span data-ttu-id="0eb74-194">Аналогичен `RedirectToAction` или `RedirectToRoute` (используется в контроллерах и представлениях).</span><span class="sxs-lookup"><span data-stu-id="0eb74-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="0eb74-195">Настраивается для страниц.</span><span class="sxs-lookup"><span data-stu-id="0eb74-195">Is customized for pages.</span></span> <span data-ttu-id="0eb74-196">В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="0eb74-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="0eb74-197">Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="0eb74-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="0eb74-198">С ошибками проверки, передаваемыми на сервер:</span><span class="sxs-lookup"><span data-stu-id="0eb74-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="0eb74-199">Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="0eb74-200">`Page` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="0eb74-201">Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="0eb74-202">`PageResult` — тип возвращаемого значения по умолчанию для метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="0eb74-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="0eb74-203">Метод обработчика, вернувший `void`, визуализирует страницу.</span><span class="sxs-lookup"><span data-stu-id="0eb74-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="0eb74-204">В предыдущем примере публикация формы без значения приводит к тому, что [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) возвращает значение false.</span><span class="sxs-lookup"><span data-stu-id="0eb74-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="0eb74-205">В этом примере на клиенте не отображаются ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="0eb74-206">Обработка ошибок проверки рассматривается далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="0eb74-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="0eb74-207">С ошибками проверки, обнаруженными при проверке на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="0eb74-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="0eb74-208">Данные **не** отправляются на сервер.</span><span class="sxs-lookup"><span data-stu-id="0eb74-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="0eb74-209">Проверка на стороне клиента описана далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="0eb74-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="0eb74-210">Для указания согласия на привязку модели в свойстве `Customer` используется атрибут [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute):</span><span class="sxs-lookup"><span data-stu-id="0eb74-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="0eb74-211">`[BindProperty]`**не** следует использовать в моделях, содержащих свойства, которые не должны изменяться клиентом.</span><span class="sxs-lookup"><span data-stu-id="0eb74-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="0eb74-212">Дополнительные сведения см. в [этом разделе](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="0eb74-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="0eb74-213">По умолчанию Razor привязывает свойства ко всем командам, кроме `GET`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="0eb74-214">Привязка к свойствам устраняет необходимость в написании кода для преобразования данных HTTP в тип модели.</span><span class="sxs-lookup"><span data-stu-id="0eb74-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="0eb74-215">Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.</span><span class="sxs-lookup"><span data-stu-id="0eb74-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="0eb74-216">Просмотр файла представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="0eb74-217">В приведенном выше коде [вспомогательная функция тега ввода](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` привязывает элемент `<input>` HTML к выражению модели `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="0eb74-218">Директива [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) обеспечивает доступность вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="0eb74-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="0eb74-219">Домашняя страница</span><span class="sxs-lookup"><span data-stu-id="0eb74-219">The home page</span></span>

<span data-ttu-id="0eb74-220">*Index.cshtml* — это домашняя страница:</span><span class="sxs-lookup"><span data-stu-id="0eb74-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="0eb74-221">Связанный класс `PageModel` (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="0eb74-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0eb74-222">Файл *index.cshtml* содержит следующую разметку:</span><span class="sxs-lookup"><span data-stu-id="0eb74-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="0eb74-223">[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования.</span><span class="sxs-lookup"><span data-stu-id="0eb74-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="0eb74-224">Эта ссылка содержит данные о маршруте с идентификатором контактного лица.</span><span class="sxs-lookup"><span data-stu-id="0eb74-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="0eb74-225">Например, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="0eb74-226">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="0eb74-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="0eb74-227">Файл *Index.cshtml* содержит разметку для создания кнопки удаления у каждого контакта клиента:</span><span class="sxs-lookup"><span data-stu-id="0eb74-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="0eb74-228">Отображаемый HTML:</span><span class="sxs-lookup"><span data-stu-id="0eb74-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="0eb74-229">Во время обработки кнопки удаления в HTML ее [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) включает параметры для следующего:</span><span class="sxs-lookup"><span data-stu-id="0eb74-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="0eb74-230">Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="0eb74-231">Параметр `handler`, указанный атрибутом `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="0eb74-232">При выборе кнопки на сервер отправляется запрос формы `POST`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="0eb74-233">По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="0eb74-234">Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="0eb74-235">Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="0eb74-236">Метод `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="0eb74-237">Получает `id` из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="0eb74-238">Отправляет в базу данных запрос контакта клиента с `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="0eb74-239">Если контакт клиента найден, он удаляется, и база данных обновляется.</span><span class="sxs-lookup"><span data-stu-id="0eb74-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="0eb74-240">Вызывает <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> для перенаправления на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="0eb74-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="0eb74-241">Файл Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="0eb74-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="0eb74-242">Первая строка содержит директиву `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="0eb74-243">Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="0eb74-244">Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="0eb74-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="0eb74-245">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="0eb74-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="0eb74-246">Файл *Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="0eb74-247">Проверка</span><span class="sxs-lookup"><span data-stu-id="0eb74-247">Validation</span></span>

<span data-ttu-id="0eb74-248">Правила проверки:</span><span class="sxs-lookup"><span data-stu-id="0eb74-248">Validation rules:</span></span>

* <span data-ttu-id="0eb74-249">Декларативно задаются в классе Model.</span><span class="sxs-lookup"><span data-stu-id="0eb74-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="0eb74-250">Применяются везде в приложении.</span><span class="sxs-lookup"><span data-stu-id="0eb74-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="0eb74-251">Пространство имен <xref:System.ComponentModel.DataAnnotations> предоставляет набор встроенных атрибутов проверки, которые декларативно применяются к классу или свойству.</span><span class="sxs-lookup"><span data-stu-id="0eb74-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="0eb74-252">Кроме того, DataAnnotations содержит атрибуты форматирования (такие как [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)), которые обеспечивают форматирование и не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="0eb74-253">Рассмотрим модель `Customer`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="0eb74-254">Используя следующий файл представления *Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="0eb74-255">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="0eb74-255">The preceding code:</span></span>

* <span data-ttu-id="0eb74-256">Включает jQuery и скрипты проверки jQuery.</span><span class="sxs-lookup"><span data-stu-id="0eb74-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="0eb74-257">Использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) `<div />` и `<span />` для следующих задач:</span><span class="sxs-lookup"><span data-stu-id="0eb74-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="0eb74-258">Проверка на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="0eb74-258">Client-side validation.</span></span>
  * <span data-ttu-id="0eb74-259">Отображение ошибок при проверке.</span><span class="sxs-lookup"><span data-stu-id="0eb74-259">Validation error rendering.</span></span>

* <span data-ttu-id="0eb74-260">Генерирует следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="0eb74-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="0eb74-261">При публикации формы создания без значения имени в форме отображается сообщение об ошибке: "Поле имени является обязательным".</span><span class="sxs-lookup"><span data-stu-id="0eb74-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="0eb74-262">в форме.</span><span class="sxs-lookup"><span data-stu-id="0eb74-262">on the form.</span></span> <span data-ttu-id="0eb74-263">Если на клиенте включен JavaScript, браузер отображает ошибку без отправки на сервер.</span><span class="sxs-lookup"><span data-stu-id="0eb74-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="0eb74-264">Атрибут `[StringLength(10)]` создает `data-val-length-max="10"` в отображаемом HTML-коде.</span><span class="sxs-lookup"><span data-stu-id="0eb74-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="0eb74-265">`data-val-length-max` не дает браузерам ввести больше заданной максимальной длины.</span><span class="sxs-lookup"><span data-stu-id="0eb74-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="0eb74-266">Если для изменения и воспроизведения записи используется средство, например [Fiddler](https://www.telerik.com/fiddler), выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="0eb74-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="0eb74-267">С именем, превышающим 10.</span><span class="sxs-lookup"><span data-stu-id="0eb74-267">With the name longer than 10.</span></span>
* <span data-ttu-id="0eb74-268">Возвращается сообщение об ошибке: "Имя поля должно быть строкой с максимальной длиной 10".</span><span class="sxs-lookup"><span data-stu-id="0eb74-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="0eb74-269">.</span><span class="sxs-lookup"><span data-stu-id="0eb74-269">is returned.</span></span>

<span data-ttu-id="0eb74-270">Рассмотрим следующую модель `Movie`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="0eb74-271">Атрибуты проверки определяют поведение для свойств модели, к которым они применяются:</span><span class="sxs-lookup"><span data-stu-id="0eb74-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="0eb74-272">Атрибуты `Required` и `MinimumLength` указывают, что свойство должно иметь значение. Тем не менее, чтобы удовлетворить требованиям проверки, пользователю достаточно ввести пробел.</span><span class="sxs-lookup"><span data-stu-id="0eb74-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="0eb74-273">Атрибут `RegularExpression` ограничивает набор допустимых для ввода символов.</span><span class="sxs-lookup"><span data-stu-id="0eb74-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="0eb74-274">В приведенном выше коде в Genre:</span><span class="sxs-lookup"><span data-stu-id="0eb74-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="0eb74-275">должны использоваться только буквы;</span><span class="sxs-lookup"><span data-stu-id="0eb74-275">Must only use letters.</span></span>
  * <span data-ttu-id="0eb74-276">первая буква должна быть прописной;</span><span class="sxs-lookup"><span data-stu-id="0eb74-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="0eb74-277">пробелы, цифры и специальные символы не допускаются.</span><span class="sxs-lookup"><span data-stu-id="0eb74-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="0eb74-278">В `RegularExpression` Rating:</span><span class="sxs-lookup"><span data-stu-id="0eb74-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="0eb74-279">первый символ должен быть прописной буквой;</span><span class="sxs-lookup"><span data-stu-id="0eb74-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="0eb74-280">допускаются специальные символы и цифры, а также последующие пробелы.</span><span class="sxs-lookup"><span data-stu-id="0eb74-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="0eb74-281">Значение "PG-13" допустимо для рейтинга, но недопустимо для жанра.</span><span class="sxs-lookup"><span data-stu-id="0eb74-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="0eb74-282">Атрибут `Range` ограничивает диапазон значений.</span><span class="sxs-lookup"><span data-stu-id="0eb74-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="0eb74-283">Атрибут `StringLength` задает максимальную и при необходимости минимальную длину строкового свойства.</span><span class="sxs-lookup"><span data-stu-id="0eb74-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="0eb74-284">Типы значений (например, `decimal`, `int`, `float`, `DateTime`) по своей природе являются обязательными и не требуют атрибута `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="0eb74-285">На странице создания для модели `Movie` отображаются ошибки с недопустимыми значениями:</span><span class="sxs-lookup"><span data-stu-id="0eb74-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Форма просмотра фильма с несколькими ошибками проверки jQuery на стороне клиента](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="0eb74-287">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="0eb74-287">For more information, see:</span></span>

* [<span data-ttu-id="0eb74-288">Добавление проверки в приложение Movie</span><span class="sxs-lookup"><span data-stu-id="0eb74-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="0eb74-289">[Проверка модели в ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="0eb74-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="0eb74-290">Обработка запросов HEAD с помощью вызова резервного обработчика OnGet</span><span class="sxs-lookup"><span data-stu-id="0eb74-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="0eb74-291">Запросы `HEAD` позволяют получать заголовки для определенного ресурса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="0eb74-292">В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.</span><span class="sxs-lookup"><span data-stu-id="0eb74-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="0eb74-293">Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="0eb74-294">Если обработчик `OnHead` не определен, Razor Pages выполнит вызов обработчика `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="0eb74-295">XSRF/CSRF и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="0eb74-296">В Razor Pages реализована [проверка для защиты от подделки](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="0eb74-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="0eb74-297">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) вставляет маркеры защиты от подделки в элементы HTML-форм.</span><span class="sxs-lookup"><span data-stu-id="0eb74-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="0eb74-298">Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="0eb74-299">Pages работает со всеми функциями подсистемы просмотра Razor.</span><span class="sxs-lookup"><span data-stu-id="0eb74-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="0eb74-300">Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.</span><span class="sxs-lookup"><span data-stu-id="0eb74-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="0eb74-301">Давайте упростим нашу страницу с помощью некоторых из этих функций.</span><span class="sxs-lookup"><span data-stu-id="0eb74-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="0eb74-302">Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="0eb74-303">Этот [макет](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="0eb74-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="0eb74-304">управляет макетом каждой страницы (кроме страниц с отказом от макета);</span><span class="sxs-lookup"><span data-stu-id="0eb74-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="0eb74-305">импортирует HTML-структуры, такие как JavaScript и таблицы стилей.</span><span class="sxs-lookup"><span data-stu-id="0eb74-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="0eb74-306">Содержимое страницы Razor отображается в том месте, где вызывается `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="0eb74-307">Дополнительные сведения см. [здесь](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="0eb74-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="0eb74-308">Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="0eb74-309">Макет хранится в папке *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="0eb74-310">Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница.</span><span class="sxs-lookup"><span data-stu-id="0eb74-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="0eb74-311">Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="0eb74-312">Файл макета следует поместить в папку *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="0eb74-313">Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="0eb74-314">*Views/Shared* — это шаблон представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="0eb74-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="0eb74-315">Razor Pages опирается на иерархию папок, а не на условные обозначения путей.</span><span class="sxs-lookup"><span data-stu-id="0eb74-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="0eb74-316">Поиск представлений в Razor Pages охватывает папку *Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="0eb74-317">Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.</span><span class="sxs-lookup"><span data-stu-id="0eb74-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="0eb74-318">Добавим файл *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="0eb74-319">`@namespace` описывается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="0eb74-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="0eb74-320">Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="0eb74-321">Директива `@namespace`, заданная на странице:</span><span class="sxs-lookup"><span data-stu-id="0eb74-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="0eb74-322">Директива `@namespace` задает пространство имен для страницы.</span><span class="sxs-lookup"><span data-stu-id="0eb74-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="0eb74-323">Включать пространство имен в директиву `@model` не требуется.</span><span class="sxs-lookup"><span data-stu-id="0eb74-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="0eb74-324">Если директива `@namespace` содержится в файле *_ViewImports.cshtml*, указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="0eb74-325">Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.</span><span class="sxs-lookup"><span data-stu-id="0eb74-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="0eb74-326">Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.</span><span class="sxs-lookup"><span data-stu-id="0eb74-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="0eb74-327">Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:</span><span class="sxs-lookup"><span data-stu-id="0eb74-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="0eb74-328">Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="0eb74-329">`@namespace` *также работает со стандартными представлениями Razor.*</span><span class="sxs-lookup"><span data-stu-id="0eb74-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="0eb74-330">Рассмотрим файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="0eb74-331">Обновленный файл представления *Pages/Create.cshtml* с *_ViewImports.cshtml* и предыдущим файлом макета:</span><span class="sxs-lookup"><span data-stu-id="0eb74-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="0eb74-332">В приведенном выше коде *_ViewImports. cshtml* импортировал пространство имен и вспомогательные функции тегов.</span><span class="sxs-lookup"><span data-stu-id="0eb74-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="0eb74-333">Файл макета импортировал файлы JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0eb74-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="0eb74-334">[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml*, который подключает проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="0eb74-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="0eb74-335">Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="0eb74-336">Формирование URL-адресов для страниц</span><span class="sxs-lookup"><span data-stu-id="0eb74-336">URL generation for Pages</span></span>

<span data-ttu-id="0eb74-337">На представленной выше странице `Create` используется `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="0eb74-338">Это приложение имеет следующую структуру файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="0eb74-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="0eb74-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="0eb74-339">*/Pages*</span></span>

  * <span data-ttu-id="0eb74-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="0eb74-341">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="0eb74-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="0eb74-342">*/Customers*</span></span>

    * <span data-ttu-id="0eb74-343">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="0eb74-344">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="0eb74-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-345">*Index.cshtml*</span></span>

<span data-ttu-id="0eb74-346">После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="0eb74-347">Строка `./Index` — это относительное имя страницы, используемое для доступа к предыдущей странице.</span><span class="sxs-lookup"><span data-stu-id="0eb74-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="0eb74-348">Эта строка позволяет создавать URL-адреса для страницы *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="0eb74-349">Пример:</span><span class="sxs-lookup"><span data-stu-id="0eb74-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="0eb74-350">С помощью абсолютного имени страницы `/Index` создаются URL-адреса страницы *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="0eb74-351">Пример:</span><span class="sxs-lookup"><span data-stu-id="0eb74-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="0eb74-352">Имя страницы — это путь к странице из корневой папки */Pages*, включая начальный символ `/` (например, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="0eb74-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="0eb74-353">Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="0eb74-354">Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.</span><span class="sxs-lookup"><span data-stu-id="0eb74-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="0eb74-355">Формирование URL-адресов для страниц поддерживает относительные имена.</span><span class="sxs-lookup"><span data-stu-id="0eb74-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="0eb74-356">В приведенной ниже таблице показано, какая страница индекса выбирается с помощью разных параметров `RedirectToPage` в *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="0eb74-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="0eb74-357">RedirectToPage(x)</span></span>| <span data-ttu-id="0eb74-358">Страница</span><span class="sxs-lookup"><span data-stu-id="0eb74-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="0eb74-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="0eb74-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="0eb74-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="0eb74-360">*Pages/Index*</span></span> |
| <span data-ttu-id="0eb74-361">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="0eb74-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="0eb74-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="0eb74-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="0eb74-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="0eb74-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="0eb74-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="0eb74-364">*Pages/Index*</span></span> |
| <span data-ttu-id="0eb74-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="0eb74-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="0eb74-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="0eb74-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="0eb74-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="0eb74-368">Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.</span><span class="sxs-lookup"><span data-stu-id="0eb74-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="0eb74-369">Привязка относительных имен полезна при создании сайтов со сложной структурой.</span><span class="sxs-lookup"><span data-stu-id="0eb74-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="0eb74-370">Если относительные имена используются для связи между страницами в папке:</span><span class="sxs-lookup"><span data-stu-id="0eb74-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="0eb74-371">Переименование папки не нарушает относительные ссылки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="0eb74-372">Ссылки не нарушаются, так как не содержат имя папки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="0eb74-373">Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:</span><span class="sxs-lookup"><span data-stu-id="0eb74-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="0eb74-374">Дополнительные сведения см. в разделах <xref:mvc/controllers/areas> и <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="0eb74-375">Атрибут ViewData</span><span class="sxs-lookup"><span data-stu-id="0eb74-375">ViewData attribute</span></span>

<span data-ttu-id="0eb74-376">Данные могут передаваться на страницу с помощью атрибута <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="0eb74-377">Значения свойств с атрибутом `[ViewData]` хранятся в <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> и загружаются из него.</span><span class="sxs-lookup"><span data-stu-id="0eb74-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="0eb74-378">В следующем примере `AboutModel` применяет атрибут `[ViewData]` к свойству `Title`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="0eb74-379">На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.</span><span class="sxs-lookup"><span data-stu-id="0eb74-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="0eb74-380">В макете заголовок считывается из словаря ViewData.</span><span class="sxs-lookup"><span data-stu-id="0eb74-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="0eb74-381">TempData</span><span class="sxs-lookup"><span data-stu-id="0eb74-381">TempData</span></span>

<span data-ttu-id="0eb74-382">ASP.NET Core предоставляет <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="0eb74-383">Это свойство хранит данные до тех пор, пока они не будут прочитаны.</span><span class="sxs-lookup"><span data-stu-id="0eb74-383">This property stores data until it's read.</span></span> <span data-ttu-id="0eb74-384">Для проверки данных без удаления можно использовать методы <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> и <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="0eb74-385">`TempData` удобно использовать для перенаправления, когда данные требуются больше, чем для одного запроса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="0eb74-386">В следующем коде значение `Message` задается с помощью `TempData`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="0eb74-387">Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="0eb74-388">Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="0eb74-389">Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="0eb74-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="0eb74-390">Несколько обработчиков на страницу</span><span class="sxs-lookup"><span data-stu-id="0eb74-390">Multiple handlers per page</span></span>

<span data-ttu-id="0eb74-391">Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="0eb74-392">Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="0eb74-393">Атрибут `asp-page-handler` является дополнением к `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="0eb74-394">Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей.</span><span class="sxs-lookup"><span data-stu-id="0eb74-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="0eb74-395">`asp-page` не задается, так как пример сопоставлен с текущей страницей.</span><span class="sxs-lookup"><span data-stu-id="0eb74-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="0eb74-396">Модель страницы</span><span class="sxs-lookup"><span data-stu-id="0eb74-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="0eb74-397">В представленном выше коде используются *именованные методы обработчика*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="0eb74-398">Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть).</span><span class="sxs-lookup"><span data-stu-id="0eb74-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="0eb74-399">В приведенном выше примере использовались методы страницы OnPost **JoinList** Async и OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="0eb74-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="0eb74-400">Если убрать *OnPost* и *Async*, имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="0eb74-401">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="0eb74-402">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="0eb74-403">Пользовательские маршруты</span><span class="sxs-lookup"><span data-stu-id="0eb74-403">Custom routes</span></span>

<span data-ttu-id="0eb74-404">С помощью директивы `@page` можно сделать следующее.</span><span class="sxs-lookup"><span data-stu-id="0eb74-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="0eb74-405">Указать пользовательский маршрут к странице.</span><span class="sxs-lookup"><span data-stu-id="0eb74-405">Specify a custom route to a page.</span></span> <span data-ttu-id="0eb74-406">Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="0eb74-407">Добавить сегменты к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0eb74-407">Append segments to a page's default route.</span></span> <span data-ttu-id="0eb74-408">Например, к такому маршруту можно добавить сегмент item: `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="0eb74-409">Добавить параметры к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0eb74-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="0eb74-410">Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="0eb74-411">Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути.</span><span class="sxs-lookup"><span data-stu-id="0eb74-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="0eb74-412">Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="0eb74-413">Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика.</span><span class="sxs-lookup"><span data-stu-id="0eb74-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="0eb74-414">Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="0eb74-415">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="0eb74-416">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="0eb74-417">Символ `?` после `handler` означает, что параметр маршрута является необязательным.</span><span class="sxs-lookup"><span data-stu-id="0eb74-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="0eb74-418">Расширенная конфигурация и параметры</span><span class="sxs-lookup"><span data-stu-id="0eb74-418">Advanced configuration and settings</span></span>

<span data-ttu-id="0eb74-419">Конфигурация и параметры в следующих разделах не требуются большинству приложений.</span><span class="sxs-lookup"><span data-stu-id="0eb74-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="0eb74-420">Для настройки расширенных параметров используйте перегрузку <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>, которая настраивает <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span><span class="sxs-lookup"><span data-stu-id="0eb74-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="0eb74-421">Используйте <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, чтобы задавать корневой каталог для страниц и добавлять для них соглашения моделей приложений.</span><span class="sxs-lookup"><span data-stu-id="0eb74-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="0eb74-422">Дополнительные сведения о соглашениях см. в разделе [Соглашения об авторизации Razor Pages](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="0eb74-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="0eb74-423">Сведения о предварительной компиляции представлений см. на странице [Компиляция представлений Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="0eb74-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="0eb74-424">Указание местонахождения Razor Pages в корне каталога</span><span class="sxs-lookup"><span data-stu-id="0eb74-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="0eb74-425">По умолчанию Razor Pages находится в корне каталога */Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="0eb74-426">Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*>, чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) приложения:</span><span class="sxs-lookup"><span data-stu-id="0eb74-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="0eb74-427">Указание местонахождения Razor Pages в пользовательском корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="0eb74-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="0eb74-428">Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>, чтобы указать, что Razor Pages находится в пользовательском корневом каталоге в приложении (укажите относительный путь):</span><span class="sxs-lookup"><span data-stu-id="0eb74-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="0eb74-429">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="0eb74-429">Additional resources</span></span>

* <span data-ttu-id="0eb74-430">Дополнительные общие сведения см. на странице [Учебник. Начало работы с Razor Pages в ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0eb74-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="0eb74-431">Атрибут authorize и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <span data-ttu-id="0eb74-432">[Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/index/3.0sample).</span><span class="sxs-lookup"><span data-stu-id="0eb74-432">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/index/3.0sample)</span></span>
* <xref:index>
* [<span data-ttu-id="0eb74-433">Справочник по синтаксису Razor для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0eb74-433">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="0eb74-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb74-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eb74-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb74-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0eb74-436">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0eb74-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="0eb74-437">Создание проекта Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-437">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eb74-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb74-438">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0eb74-439">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0eb74-439">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0eb74-440">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0eb74-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0eb74-441">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-441">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="0eb74-442">Откройте созданный файл *.csproj* в Visual Studio для Mac.</span><span class="sxs-lookup"><span data-stu-id="0eb74-442">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eb74-443">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb74-443">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0eb74-444">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-444">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="0eb74-445">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-445">Razor Pages</span></span>

<span data-ttu-id="0eb74-446">Razor Pages активируется в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-446">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="0eb74-447">Рассмотрим простую страницу: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="0eb74-447">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="0eb74-448">Приведенный выше код выглядит как [файл представления Razor](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="0eb74-448">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="0eb74-449">и отличается от него только директивой `@page`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-449">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="0eb74-450">Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер.</span><span class="sxs-lookup"><span data-stu-id="0eb74-450">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="0eb74-451">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="0eb74-451">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="0eb74-452">`@page` влияет на поведение всех остальных конструкций Razor.</span><span class="sxs-lookup"><span data-stu-id="0eb74-452">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="0eb74-453">Похожая страница с использованием класса `PageModel` показана в следующих двух файлах.</span><span class="sxs-lookup"><span data-stu-id="0eb74-453">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="0eb74-454">Файл *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-454">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="0eb74-455">Модель страницы *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-455">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="0eb74-456">Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *CS*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-456">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="0eb74-457">Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-457">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="0eb74-458">Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-458">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="0eb74-459">Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе.</span><span class="sxs-lookup"><span data-stu-id="0eb74-459">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="0eb74-460">В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-460">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="0eb74-461">Имя файла и путь</span><span class="sxs-lookup"><span data-stu-id="0eb74-461">File name and path</span></span>               | <span data-ttu-id="0eb74-462">Соответствующий URL</span><span class="sxs-lookup"><span data-stu-id="0eb74-462">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="0eb74-463">*/Pages/index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-463">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="0eb74-464">`/` или `/Index`</span><span class="sxs-lookup"><span data-stu-id="0eb74-464">`/` or `/Index`</span></span> |
| <span data-ttu-id="0eb74-465">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-465">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="0eb74-466">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-466">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="0eb74-467">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-467">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="0eb74-468">`/Store` или `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="0eb74-468">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="0eb74-469">Примечания.</span><span class="sxs-lookup"><span data-stu-id="0eb74-469">Notes:</span></span>

* <span data-ttu-id="0eb74-470">Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-470">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="0eb74-471">Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-471">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="0eb74-472">Создание простой формы</span><span class="sxs-lookup"><span data-stu-id="0eb74-472">Write a basic form</span></span>

<span data-ttu-id="0eb74-473">Razor Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения.</span><span class="sxs-lookup"><span data-stu-id="0eb74-473">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="0eb74-474">[Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0eb74-474">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="0eb74-475">Рассмотрим страницу с простой формой связи для модели `Contact`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-475">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="0eb74-476">В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="0eb74-476">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="0eb74-477">Модель данных:</span><span class="sxs-lookup"><span data-stu-id="0eb74-477">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="0eb74-478">Контекст базы данных:</span><span class="sxs-lookup"><span data-stu-id="0eb74-478">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="0eb74-479">Файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-479">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="0eb74-480">Модель страницы *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-480">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="0eb74-481">Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.</span><span class="sxs-lookup"><span data-stu-id="0eb74-481">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="0eb74-482">Класс `PageModel` позволяет разделять логику страницы и ее представление.</span><span class="sxs-lookup"><span data-stu-id="0eb74-482">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="0eb74-483">Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации.</span><span class="sxs-lookup"><span data-stu-id="0eb74-483">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="0eb74-484">Это разделение позволяет:</span><span class="sxs-lookup"><span data-stu-id="0eb74-484">This separation allows:</span></span>

* <span data-ttu-id="0eb74-485">управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0eb74-485">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="0eb74-486">[Модульное тестирование](xref:test/razor-pages-tests) страниц.</span><span class="sxs-lookup"><span data-stu-id="0eb74-486">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="0eb74-487">Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму).</span><span class="sxs-lookup"><span data-stu-id="0eb74-487">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="0eb74-488">Методы обработчика можно добавить для любой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="0eb74-488">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="0eb74-489">Наиболее распространенные обработчики</span><span class="sxs-lookup"><span data-stu-id="0eb74-489">The most common handlers are:</span></span>

* <span data-ttu-id="0eb74-490">`OnGet` — инициализация необходимого для страницы состояния.</span><span class="sxs-lookup"><span data-stu-id="0eb74-490">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="0eb74-491">Пример обработчика [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="0eb74-491">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="0eb74-492">`OnPost` — обработка отправленных через форму данных.</span><span class="sxs-lookup"><span data-stu-id="0eb74-492">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="0eb74-493">Суффикс `Async` не является обязательным, но часто используется для асинхронных функций.</span><span class="sxs-lookup"><span data-stu-id="0eb74-493">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="0eb74-494">Этот код типичен для Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0eb74-494">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="0eb74-495">Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:</span><span class="sxs-lookup"><span data-stu-id="0eb74-495">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="0eb74-496">Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.</span><span class="sxs-lookup"><span data-stu-id="0eb74-496">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="0eb74-497">Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation), [проверку](xref:mvc/models/validation) и результаты действий, являются общими.</span><span class="sxs-lookup"><span data-stu-id="0eb74-497">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="0eb74-498">Предыдущий метод `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-498">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="0eb74-499">Простая схема `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-499">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="0eb74-500">Проверка на наличие ошибок проверки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-500">Check for validation errors.</span></span>

* <span data-ttu-id="0eb74-501">Если ошибок нет, сохранение данных и перенаправление.</span><span class="sxs-lookup"><span data-stu-id="0eb74-501">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="0eb74-502">Если есть ошибки, отображение страницы с сообщениями проверки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-502">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="0eb74-503">Проверка на стороне клиента выполняется точно так же, как в традиционных приложениях MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0eb74-503">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="0eb74-504">Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.</span><span class="sxs-lookup"><span data-stu-id="0eb74-504">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="0eb74-505">После успешного ввода данных метод обработчика `OnPostAsync` вызывает метод обработчика `RedirectToPage`, чтобы получить экземпляр `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-505">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="0eb74-506">`RedirectToPage` — это новый результат действия, аналогичный `RedirectToAction` или `RedirectToRoute`, но настроенный для страниц.</span><span class="sxs-lookup"><span data-stu-id="0eb74-506">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="0eb74-507">В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="0eb74-507">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="0eb74-508">Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="0eb74-508">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="0eb74-509">Если в отправленной форме имеются ошибки проверки (переданные на сервер), метод обработчика `OnPostAsync` вызывает метод обработчика `Page`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-509">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="0eb74-510">`Page` возвращает экземпляр `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-510">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="0eb74-511">Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-511">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="0eb74-512">`PageResult` — тип возвращаемого значения по умолчанию для метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="0eb74-512">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="0eb74-513">Метод обработчика, вернувший `void`, визуализирует страницу.</span><span class="sxs-lookup"><span data-stu-id="0eb74-513">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="0eb74-514">Для указания согласия на привязку модели в свойстве `Customer` используется атрибут `[BindProperty]`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-514">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="0eb74-515">По умолчанию Razor Pages привязывает свойства ко всем командам, кроме `GET`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-515">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="0eb74-516">Привязка к свойствам позволяет сократить объем необходимого кода.</span><span class="sxs-lookup"><span data-stu-id="0eb74-516">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="0eb74-517">Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.</span><span class="sxs-lookup"><span data-stu-id="0eb74-517">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="0eb74-518">Домашняя страница (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="0eb74-518">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="0eb74-519">Связанный класс `PageModel` (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="0eb74-519">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="0eb74-520">Файл *Index.cshtml* содержит следующую разметку для создания ссылки на правку для каждого контактного лица.</span><span class="sxs-lookup"><span data-stu-id="0eb74-520">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="0eb74-521">[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования.</span><span class="sxs-lookup"><span data-stu-id="0eb74-521">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="0eb74-522">Эта ссылка содержит данные о маршруте с идентификатором контактного лица.</span><span class="sxs-lookup"><span data-stu-id="0eb74-522">The link contains route data with the contact ID.</span></span> <span data-ttu-id="0eb74-523">Например, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-523">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="0eb74-524">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="0eb74-524">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="0eb74-525">Вспомогательные функции тегов включены с помощью `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="0eb74-525">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="0eb74-526">Файл *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-526">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="0eb74-527">Первая строка содержит директиву `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-527">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="0eb74-528">Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-528">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="0eb74-529">Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="0eb74-529">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="0eb74-530">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="0eb74-530">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="0eb74-531">Файл *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-531">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="0eb74-532">Файл *Index.cshtml* также содержит разметку для создания кнопки удаления у каждого контакта клиента:</span><span class="sxs-lookup"><span data-stu-id="0eb74-532">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="0eb74-533">Во время обработки кнопки удаления в HTML ее `formaction` включает параметры для следующего:</span><span class="sxs-lookup"><span data-stu-id="0eb74-533">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="0eb74-534">Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-534">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="0eb74-535">Параметр `handler`, указанный атрибутом `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-535">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="0eb74-536">Пример отображенной кнопки удаления с идентификатором контакта клиента `1`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-536">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="0eb74-537">При выборе кнопки на сервер отправляется запрос формы `POST`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-537">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="0eb74-538">По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-538">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="0eb74-539">Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-539">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="0eb74-540">Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-540">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="0eb74-541">В приведенном ниже коде показан обработчик `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-541">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="0eb74-542">Метод `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-542">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="0eb74-543">Принимает `id` из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-543">Accepts the `id` from the query string.</span></span> <span data-ttu-id="0eb74-544">Если директива страницы *index.cshtml* содержит ограничение маршрутизации `"{id:int?}"`, то `id` будет получено из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="0eb74-544">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="0eb74-545">Данные маршрута для `id` указываются в универсальном коде ресурса (URI), например `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-545">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="0eb74-546">Отправляет в базу данных запрос контакта клиента с `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-546">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="0eb74-547">Если контакт клиента найден, он удаляется из списка контактов.</span><span class="sxs-lookup"><span data-stu-id="0eb74-547">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="0eb74-548">База данных обновляется.</span><span class="sxs-lookup"><span data-stu-id="0eb74-548">The database is updated.</span></span>
* <span data-ttu-id="0eb74-549">Вызывает `RedirectToPage` для перенаправления на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="0eb74-549">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="0eb74-550">Маркировка свойств страницы как обязательных</span><span class="sxs-lookup"><span data-stu-id="0eb74-550">Mark page properties as required</span></span>

<span data-ttu-id="0eb74-551">Свойства класса `PageModel` можно отметить атрибутом [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):</span><span class="sxs-lookup"><span data-stu-id="0eb74-551">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="0eb74-552">Дополнительные сведения см. в статье [Проверка модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="0eb74-552">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="0eb74-553">Обработка запросов HEAD с помощью вызова резервного обработчика OnGet</span><span class="sxs-lookup"><span data-stu-id="0eb74-553">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="0eb74-554">Запросы `HEAD` позволяют получать заголовки для определенного ресурса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-554">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="0eb74-555">В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.</span><span class="sxs-lookup"><span data-stu-id="0eb74-555">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="0eb74-556">Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-556">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="0eb74-557">Если в ASP.NET Core 2.1 или более поздней версии обработчик `OnHead` не определен, Razor Pages выполнит вызов резервного обработчика `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-557">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="0eb74-558">Это поведение включается путем вызова [SetCompatibilityVersion](xref:mvc/compatibility-version) в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-558">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="0eb74-559">Шаблоны по умолчанию создают вызов `SetCompatibilityVersion` в ASP.NET Core 2.1 и 2.2.</span><span class="sxs-lookup"><span data-stu-id="0eb74-559">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="0eb74-560">`SetCompatibilityVersion` задает для параметра `AllowMappingHeadRequestsToGetHandler` Razor Pages значение `true`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-560">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="0eb74-561">Вместо включения всех возможных поведений с помощью метода `SetCompatibilityVersion` вы можете задать *конкретное* поведение.</span><span class="sxs-lookup"><span data-stu-id="0eb74-561">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="0eb74-562">Следующий код позволяет сопоставлять запросы `HEAD` с обработчиком `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-562">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="0eb74-563">XSRF/CSRF и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-563">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="0eb74-564">Вам не придется писать отдельный код для [проверки подлинности запросов](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="0eb74-564">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="0eb74-565">Razor Pages включает создание и проверку маркеров защиты от подделок по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0eb74-565">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="0eb74-566">Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-566">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="0eb74-567">Pages работает со всеми функциями подсистемы просмотра Razor.</span><span class="sxs-lookup"><span data-stu-id="0eb74-567">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="0eb74-568">Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.</span><span class="sxs-lookup"><span data-stu-id="0eb74-568">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="0eb74-569">Давайте упростим нашу страницу с помощью некоторых из этих функций.</span><span class="sxs-lookup"><span data-stu-id="0eb74-569">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="0eb74-570">Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-570">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="0eb74-571">Этот [макет](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="0eb74-571">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="0eb74-572">управляет макетом каждой страницы (кроме страниц с отказом от макета);</span><span class="sxs-lookup"><span data-stu-id="0eb74-572">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="0eb74-573">импортирует HTML-структуры, такие как JavaScript и таблицы стилей.</span><span class="sxs-lookup"><span data-stu-id="0eb74-573">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="0eb74-574">Дополнительные сведения см. в статье о [макете](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="0eb74-574">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="0eb74-575">Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-575">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="0eb74-576">Макет хранится в папке *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-576">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="0eb74-577">Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница.</span><span class="sxs-lookup"><span data-stu-id="0eb74-577">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="0eb74-578">Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-578">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="0eb74-579">Файл макета следует поместить в папку *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-579">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="0eb74-580">Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-580">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="0eb74-581">*Views/Shared* — это шаблон представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="0eb74-581">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="0eb74-582">Razor Pages опирается на иерархию папок, а не на условные обозначения путей.</span><span class="sxs-lookup"><span data-stu-id="0eb74-582">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="0eb74-583">Поиск представлений в Razor Pages охватывает папку *Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-583">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="0eb74-584">Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.</span><span class="sxs-lookup"><span data-stu-id="0eb74-584">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="0eb74-585">Добавим файл *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-585">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="0eb74-586">`@namespace` описывается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="0eb74-586">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="0eb74-587">Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-587">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="0eb74-588">Явное применение директивы `@namespace` на странице:</span><span class="sxs-lookup"><span data-stu-id="0eb74-588">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="0eb74-589">Данная директива задает пространство имен для страницы.</span><span class="sxs-lookup"><span data-stu-id="0eb74-589">The directive sets the namespace for the page.</span></span> <span data-ttu-id="0eb74-590">Включать пространство имен в директиву `@model` не требуется.</span><span class="sxs-lookup"><span data-stu-id="0eb74-590">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="0eb74-591">Если директива `@namespace` содержится в файле *_ViewImports.cshtml*, указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-591">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="0eb74-592">Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.</span><span class="sxs-lookup"><span data-stu-id="0eb74-592">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="0eb74-593">Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.</span><span class="sxs-lookup"><span data-stu-id="0eb74-593">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="0eb74-594">Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:</span><span class="sxs-lookup"><span data-stu-id="0eb74-594">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="0eb74-595">Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-595">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="0eb74-596">`@namespace` *также работает со стандартными представлениями Razor.*</span><span class="sxs-lookup"><span data-stu-id="0eb74-596">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="0eb74-597">Исходный файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-597">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="0eb74-598">Обновленный файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0eb74-598">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="0eb74-599">[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml*, который подключает проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="0eb74-599">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="0eb74-600">Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-600">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="0eb74-601">Формирование URL-адресов для страниц</span><span class="sxs-lookup"><span data-stu-id="0eb74-601">URL generation for Pages</span></span>

<span data-ttu-id="0eb74-602">На представленной выше странице `Create` используется `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-602">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="0eb74-603">Это приложение имеет следующую структуру файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="0eb74-603">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="0eb74-604">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="0eb74-604">*/Pages*</span></span>

  * <span data-ttu-id="0eb74-605">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-605">*Index.cshtml*</span></span>
  * <span data-ttu-id="0eb74-606">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="0eb74-606">*/Customers*</span></span>

    * <span data-ttu-id="0eb74-607">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-607">*Create.cshtml*</span></span>
    * <span data-ttu-id="0eb74-608">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-608">*Edit.cshtml*</span></span>
    * <span data-ttu-id="0eb74-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0eb74-609">*Index.cshtml*</span></span>

<span data-ttu-id="0eb74-610">После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-610">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="0eb74-611">Строка `/Index` составляет часть URL-адреса для доступа к указанной выше странице.</span><span class="sxs-lookup"><span data-stu-id="0eb74-611">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="0eb74-612">Строка `/Index` позволяет создавать URL-адреса для страницы *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-612">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="0eb74-613">Пример:</span><span class="sxs-lookup"><span data-stu-id="0eb74-613">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="0eb74-614">Имя страницы — это путь к странице из корневой папки */Pages*, включая начальный символ `/` (например, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="0eb74-614">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="0eb74-615">Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-615">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="0eb74-616">Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.</span><span class="sxs-lookup"><span data-stu-id="0eb74-616">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="0eb74-617">Формирование URL-адресов для страниц поддерживает относительные имена.</span><span class="sxs-lookup"><span data-stu-id="0eb74-617">URL generation for pages supports relative names.</span></span> <span data-ttu-id="0eb74-618">В приведенной ниже таблице показано, какая страница индекса выбирается с каждым параметром `RedirectToPage` в *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-618">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="0eb74-619">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="0eb74-619">RedirectToPage(x)</span></span>| <span data-ttu-id="0eb74-620">Страница</span><span class="sxs-lookup"><span data-stu-id="0eb74-620">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="0eb74-621">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="0eb74-621">RedirectToPage("/Index")</span></span> | <span data-ttu-id="0eb74-622">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="0eb74-622">*Pages/Index*</span></span> |
| <span data-ttu-id="0eb74-623">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="0eb74-623">RedirectToPage("./Index");</span></span> | <span data-ttu-id="0eb74-624">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="0eb74-624">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="0eb74-625">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="0eb74-625">RedirectToPage("../Index")</span></span> | <span data-ttu-id="0eb74-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="0eb74-626">*Pages/Index*</span></span> |
| <span data-ttu-id="0eb74-627">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="0eb74-627">RedirectToPage("Index")</span></span>  | <span data-ttu-id="0eb74-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="0eb74-628">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="0eb74-629">`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-629">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="0eb74-630">Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.</span><span class="sxs-lookup"><span data-stu-id="0eb74-630">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="0eb74-631">Привязка относительных имен полезна при создании сайтов со сложной структурой.</span><span class="sxs-lookup"><span data-stu-id="0eb74-631">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="0eb74-632">Если для связи между страницами в определенной папке используются относительные имена, эту папку можно переименовать.</span><span class="sxs-lookup"><span data-stu-id="0eb74-632">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="0eb74-633">При этом все ссылки останутся рабочими (так как не включают имя папки).</span><span class="sxs-lookup"><span data-stu-id="0eb74-633">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="0eb74-634">Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:</span><span class="sxs-lookup"><span data-stu-id="0eb74-634">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="0eb74-635">Для получения дополнительной информации см. <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="0eb74-635">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="0eb74-636">Атрибут ViewData</span><span class="sxs-lookup"><span data-stu-id="0eb74-636">ViewData attribute</span></span>

<span data-ttu-id="0eb74-637">Данные могут передаваться на страницу с помощью атрибута [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="0eb74-637">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="0eb74-638">Свойства в контроллерах или моделях Razor Page, отмеченные атрибутом `[ViewData]`, обладают собственными значениями, загружаемыми из [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="0eb74-638">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="0eb74-639">В следующем примере класс `AboutModel` содержит свойство `Title`, отмеченное атрибутом `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-639">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="0eb74-640">Свойство `Title` задает заголовок страницы About.</span><span class="sxs-lookup"><span data-stu-id="0eb74-640">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="0eb74-641">На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.</span><span class="sxs-lookup"><span data-stu-id="0eb74-641">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="0eb74-642">В макете заголовок считывается из словаря ViewData.</span><span class="sxs-lookup"><span data-stu-id="0eb74-642">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="0eb74-643">TempData</span><span class="sxs-lookup"><span data-stu-id="0eb74-643">TempData</span></span>

<span data-ttu-id="0eb74-644">ASP.NET Core позволяет использовать свойство [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) в [контроллере](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="0eb74-644">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="0eb74-645">Это свойство хранит данные до тех пор, пока они не будут прочитаны.</span><span class="sxs-lookup"><span data-stu-id="0eb74-645">This property stores data until it's read.</span></span> <span data-ttu-id="0eb74-646">Для проверки данных без удаления можно использовать методы `Keep` и `Peek`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-646">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="0eb74-647">`TempData` удобно использовать для перенаправления, когда данные требуются больше чем для одного запроса.</span><span class="sxs-lookup"><span data-stu-id="0eb74-647">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="0eb74-648">В следующем коде значение `Message` задается с помощью `TempData`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-648">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="0eb74-649">Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-649">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="0eb74-650">Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-650">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="0eb74-651">Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="0eb74-651">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="0eb74-652">Несколько обработчиков на страницу</span><span class="sxs-lookup"><span data-stu-id="0eb74-652">Multiple handlers per page</span></span>

<span data-ttu-id="0eb74-653">Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="0eb74-653">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="0eb74-654">Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-654">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="0eb74-655">Атрибут `asp-page-handler` является дополнением к `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-655">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="0eb74-656">Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей.</span><span class="sxs-lookup"><span data-stu-id="0eb74-656">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="0eb74-657">`asp-page` не задается, так как пример сопоставлен с текущей страницей.</span><span class="sxs-lookup"><span data-stu-id="0eb74-657">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="0eb74-658">Модель страницы</span><span class="sxs-lookup"><span data-stu-id="0eb74-658">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="0eb74-659">В представленном выше коде используются *именованные методы обработчика*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-659">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="0eb74-660">Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть).</span><span class="sxs-lookup"><span data-stu-id="0eb74-660">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="0eb74-661">В приведенном выше примере использовались методы страницы OnPost **JoinList** Async и OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="0eb74-661">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="0eb74-662">Если убрать *OnPost* и *Async*, имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-662">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="0eb74-663">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-663">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="0eb74-664">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-664">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="0eb74-665">Пользовательские маршруты</span><span class="sxs-lookup"><span data-stu-id="0eb74-665">Custom routes</span></span>

<span data-ttu-id="0eb74-666">С помощью директивы `@page` можно сделать следующее.</span><span class="sxs-lookup"><span data-stu-id="0eb74-666">Use the `@page` directive to:</span></span>

* <span data-ttu-id="0eb74-667">Указать пользовательский маршрут к странице.</span><span class="sxs-lookup"><span data-stu-id="0eb74-667">Specify a custom route to a page.</span></span> <span data-ttu-id="0eb74-668">Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-668">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="0eb74-669">Добавить сегменты к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0eb74-669">Append segments to a page's default route.</span></span> <span data-ttu-id="0eb74-670">Например, к такому маршруту можно добавить сегмент item: `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-670">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="0eb74-671">Добавить параметры к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0eb74-671">Append parameters to a page's default route.</span></span> <span data-ttu-id="0eb74-672">Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-672">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="0eb74-673">Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути.</span><span class="sxs-lookup"><span data-stu-id="0eb74-673">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="0eb74-674">Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-674">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="0eb74-675">Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика.</span><span class="sxs-lookup"><span data-stu-id="0eb74-675">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="0eb74-676">Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="0eb74-676">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="0eb74-677">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-677">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="0eb74-678">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="0eb74-678">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="0eb74-679">Символ `?` после `handler` означает, что параметр маршрута является необязательным.</span><span class="sxs-lookup"><span data-stu-id="0eb74-679">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="0eb74-680">Конфигурация и параметры</span><span class="sxs-lookup"><span data-stu-id="0eb74-680">Configuration and settings</span></span>

<span data-ttu-id="0eb74-681">Чтобы настроить дополнительные параметры, воспользуйтесь методом расширения `AddRazorPagesOptions` в построителе MVC:</span><span class="sxs-lookup"><span data-stu-id="0eb74-681">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="0eb74-682">В настоящее время `RazorPagesOptions` позволяет задавать корневую папку для страниц и добавлять для них обозначения моделей приложений.</span><span class="sxs-lookup"><span data-stu-id="0eb74-682">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="0eb74-683">В дальнейшем мы расширим спектр его возможностей.</span><span class="sxs-lookup"><span data-stu-id="0eb74-683">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="0eb74-684">Сведения о предварительной компиляции представлений см. на странице [Компиляция представлений Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="0eb74-684">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="0eb74-685">[Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="0eb74-685">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="0eb74-686">Дополнительные общие сведения см. на странице [Учебник. Начало работы с Razor Pages в ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="0eb74-686">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="0eb74-687">Указание местонахождения Razor Pages в корне каталога</span><span class="sxs-lookup"><span data-stu-id="0eb74-687">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="0eb74-688">По умолчанию Razor Pages находится в корне каталога */Pages*.</span><span class="sxs-lookup"><span data-stu-id="0eb74-688">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="0eb74-689">Добавьте [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) приложения:</span><span class="sxs-lookup"><span data-stu-id="0eb74-689">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="0eb74-690">Указание местонахождения Razor Pages в пользовательском корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="0eb74-690">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="0eb74-691">Добавьте [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в пользовательской корневой папке приложения (укажите относительный путь):</span><span class="sxs-lookup"><span data-stu-id="0eb74-691">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="0eb74-692">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="0eb74-692">Additional resources</span></span>

* [<span data-ttu-id="0eb74-693">Атрибут authorize и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0eb74-693">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* [<span data-ttu-id="0eb74-694">Справочник по синтаксису Razor для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0eb74-694">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
