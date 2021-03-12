---
title: ASP.NET Core и Entity Framework 6
author: rick-anderson
description: Entity Framework версии 6.3 и последующих поддерживает работу с ASP.NET Core версии 3.1 и последующих.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 44211ac7fa2acc7a7a9471ef362cff02f94fa2b6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588273"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="b87e0-103">ASP.NET Core и Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="b87e0-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b87e0-104">Автор: [Патрик Гуд](https://github.com/attrib75) (Patrick Goode)</span><span class="sxs-lookup"><span data-stu-id="b87e0-104">By [Patrick Goode](https://github.com/attrib75)</span></span>

## <a name="using-entity-framework-6-with-aspnet-core"></a><span data-ttu-id="b87e0-105">Использование Entity Framework 6 с ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b87e0-105">Using Entity Framework 6 with ASP.NET Core</span></span>

<span data-ttu-id="b87e0-106">Для новых разработок следует использовать [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="b87e0-106">[Entity Framework Core](/ef/) should be used for new development.</span></span> <span data-ttu-id="b87e0-107">В [загружаемом примере](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/3.xsample) используется [Entity Framework 6 (EF6)](/ef/ef6), который можно использовать для переноса существующих приложений в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b87e0-107">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/3.xsample) uses [Entity Framework 6 (EF6)](/ef/ef6), which can be used to migrate exiting apps to ASP.NET Core.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b87e0-108">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b87e0-108">Additional resources</span></span>

* [<span data-ttu-id="b87e0-109">Entity Framework — конфигурация на основе кода</span><span class="sxs-lookup"><span data-stu-id="b87e0-109">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b87e0-110">Авторы: [Павел Грудзень](https://github.com/pgrudzien12) (Paweł Grudzień), [Дэмиен Понтифекс](https://github.com/DamienPontifex) (Damien Pontifex) и [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra)</span><span class="sxs-lookup"><span data-stu-id="b87e0-110">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b87e0-111">В этой статье показано, как использовать платформу Entity Framework 6 в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b87e0-111">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="b87e0-112">Обзор</span><span class="sxs-lookup"><span data-stu-id="b87e0-112">Overview</span></span> 

<span data-ttu-id="b87e0-113">Чтобы использовать платформу Entity Framework 6, проект необходимо компилировать на основе .NET Framework, поскольку Entity Framework 6 не поддерживает .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b87e0-113">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="b87e0-114">Если вам требуются какие-либо кроссплатформенные функции, необходимо выполнить обновление до [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="b87e0-114">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="b87e0-115">Рекомендуемый способ использования платформы Entity Framework 6 в приложении ASP.NET Core заключается в помещении контекста и классов модели EF6 в проект библиотеки классов, который предназначен для .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b87e0-115">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="b87e0-116">Добавьте ссылку на библиотеку классов из проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b87e0-116">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="b87e0-117">См. пример [решения Visual Studio с проектами EF6 и ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="b87e0-117">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/).</span></span>    

<span data-ttu-id="b87e0-118">Контекст EF6 нельзя поместить в проект ASP.NET Core, поскольку проекты .NET Core поддерживают не все функции, используемые командами EF6, например *Enable-Migrations*.</span><span class="sxs-lookup"><span data-stu-id="b87e0-118">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="b87e0-119">Независимо от типа проекта, в котором размещается контекст EF6, в этом контексте работают только средства командной строки EF6.</span><span class="sxs-lookup"><span data-stu-id="b87e0-119">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="b87e0-120">Например, команда `Scaffold-DbContext` доступна только в Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b87e0-120">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="b87e0-121">Дополнительные сведения о реконструировании базы данных в модель EF6 см. в разделе <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span><span class="sxs-lookup"><span data-stu-id="b87e0-121">If you need to do reverse engineering of a database into an EF6 model, see <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span></span>    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="b87e0-122">Использование ссылок на полную платформу и EF6 в проекте ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b87e0-122">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="b87e0-123">Проект ASP.NET Core должен быть предназначен для платформы .NET Framework и ссылаться на EF6.</span><span class="sxs-lookup"><span data-stu-id="b87e0-123">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="b87e0-124">Например, файл *CSPROJ* вашего проекта ASP.NET Core будет иметь вид, схожий с показанным в следующем примере (показаны только соответствующие части файла).</span><span class="sxs-lookup"><span data-stu-id="b87e0-124">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="b87e0-125">При создании нового проекта используйте шаблон **Веб-приложение ASP.NET Core (.NET Framework)** .</span><span class="sxs-lookup"><span data-stu-id="b87e0-125">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="b87e0-126">Обработка строк подключения</span><span class="sxs-lookup"><span data-stu-id="b87e0-126">Handle connection strings</span></span>    

<span data-ttu-id="b87e0-127">Средства командной строки EF6, которые используются в проекте библиотеки классов EF6, требуют наличия конструктора по умолчанию, с помощью которого они могут создавать экземпляр контекста.</span><span class="sxs-lookup"><span data-stu-id="b87e0-127">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="b87e0-128">Тем не менее, возможно, вам потребуется задать строку подключения для использования в проекте ASP.NET Core. В этом случае конструктор контекста должен иметь параметр, в котором передается строка подключения.</span><span class="sxs-lookup"><span data-stu-id="b87e0-128">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="b87e0-129">Ниже приведен пример.</span><span class="sxs-lookup"><span data-stu-id="b87e0-129">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="b87e0-130">Поскольку в контексте EF6 отсутствует конструктор без параметров, в проекте EF6 необходимо предоставить реализацию <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span><span class="sxs-lookup"><span data-stu-id="b87e0-130">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span></span> <span data-ttu-id="b87e0-131">Средства командной строки EF6 будут находить и использовать эту реализацию для создания экземпляра контекста.</span><span class="sxs-lookup"><span data-stu-id="b87e0-131">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="b87e0-132">Рассмотрим пример.</span><span class="sxs-lookup"><span data-stu-id="b87e0-132">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="b87e0-133">В этом примере кода реализация `IDbContextFactory` передает жестко запрограммированную строку подключения.</span><span class="sxs-lookup"><span data-stu-id="b87e0-133">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="b87e0-134">Эта строка подключения будет использоваться средствами командной строки.</span><span class="sxs-lookup"><span data-stu-id="b87e0-134">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="b87e0-135">В некоторых случаях требуется реализовать стратегию, в которой библиотека классов использует ту же строку подключения, что и вызывающее приложение.</span><span class="sxs-lookup"><span data-stu-id="b87e0-135">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="b87e0-136">Например, вы можете получать значение из переменной среды в обоих проектах.</span><span class="sxs-lookup"><span data-stu-id="b87e0-136">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="b87e0-137">Настройка внедрения зависимостей в проекте ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b87e0-137">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="b87e0-138">В файле *Startup.cs* проекта Core настройте контекст EF6 для внедрения зависимостей в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b87e0-138">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="b87e0-139">Объекты контекста EF должны иметь время существования, соответствующее запросу.</span><span class="sxs-lookup"><span data-stu-id="b87e0-139">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="b87e0-140">После этого можно получить экземпляр контекста в контроллерах посредством внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b87e0-140">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="b87e0-141">Этот код похож на тот, который вы написали для контекста EF Core:</span><span class="sxs-lookup"><span data-stu-id="b87e0-141">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="b87e0-142">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="b87e0-142">Sample application</span></span>   

<span data-ttu-id="b87e0-143">Рабочий пример приложения см. в разделе [пример решения Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/), который прилагается к этой статье.</span><span class="sxs-lookup"><span data-stu-id="b87e0-143">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>    

<span data-ttu-id="b87e0-144">Этот пример можно создать с нуля, выполнив следующие действия в Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b87e0-144">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="b87e0-145">Создайте решение.</span><span class="sxs-lookup"><span data-stu-id="b87e0-145">Create a solution.</span></span>    

* <span data-ttu-id="b87e0-146">**Добавить** > **Новый проект** > **Интернет** > **Веб-приложение ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="b87e0-146">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="b87e0-147">В диалоговом окне для выбора шаблона проекта укажите версию API и .NET Framework в раскрывающемся списке.</span><span class="sxs-lookup"><span data-stu-id="b87e0-147">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="b87e0-148">**Добавить** > **Новый проект** > **Рабочий стол Windows** > **Библиотека классов (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="b87e0-148">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="b87e0-149">В **консоли диспетчера пакетов** для обоих проектов выполните команду `Install-Package Entityframework`.</span><span class="sxs-lookup"><span data-stu-id="b87e0-149">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="b87e0-150">В проекте библиотеки классов создайте классы модели данных и класс контекста, а также реализацию `IDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="b87e0-150">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="b87e0-151">В консоли диспетчера пакетов для проекта библиотеки классов выполните команды `Enable-Migrations` и `Add-Migration Initial`.</span><span class="sxs-lookup"><span data-stu-id="b87e0-151">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="b87e0-152">Если проект ASP.NET Core настроен как запускаемый, добавьте `-StartupProjectName EF6` в эти команды.</span><span class="sxs-lookup"><span data-stu-id="b87e0-152">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="b87e0-153">В проекте Core добавьте ссылку на проект библиотеки классов.</span><span class="sxs-lookup"><span data-stu-id="b87e0-153">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="b87e0-154">В проекте Core в файле *Startup.cs* зарегистрируйте контекст для внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b87e0-154">In the Core project, in *Startup.cs*, register the context for DI.</span></span>    

* <span data-ttu-id="b87e0-155">В проекте Core в файле *appsettings.json* добавьте строку подключения.</span><span class="sxs-lookup"><span data-stu-id="b87e0-155">In the Core project, in *appsettings.json*, add the connection string.</span></span>  

* <span data-ttu-id="b87e0-156">В проекте Core добавьте контроллер и представление (или представления), чтобы проверить возможность чтения и записи данных.</span><span class="sxs-lookup"><span data-stu-id="b87e0-156">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="b87e0-157">(Обратите внимание, что функция формирования шаблонов ASP.NET Core MVC не будет работать с контекстом EF6, на который указывает ссылка из библиотеки классов.)</span><span class="sxs-lookup"><span data-stu-id="b87e0-157">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
