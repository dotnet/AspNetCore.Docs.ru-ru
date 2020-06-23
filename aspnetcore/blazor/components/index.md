---
title: Создание и использование компонентов Razor ASP.NET Core
author: guardrex
description: Сведения о том, как создавать и использовать компоненты Razor, включая способы привязки к данным, обработки событий и управления жизненным циклом компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/index
ms.openlocfilehash: a97fbe07251a61b30985695e3d207f0e3c3a777b
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103159"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="13f1a-103">Создание и использование компонентов Razor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="13f1a-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="13f1a-104">Авторы: [Люк Латэм (Luke Latham)](https://github.com/guardrex), [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и Тобиас Бартщ [(Tobias Bartsch)](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="13f1a-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="13f1a-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/../common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13f1a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/../common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="13f1a-106">Приложения Blazor создаются с использованием *компонентов*.</span><span class="sxs-lookup"><span data-stu-id="13f1a-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="13f1a-107">Компонент — это автономный блок пользовательского интерфейса, такой как страница, диалоговое окно или форма.</span><span class="sxs-lookup"><span data-stu-id="13f1a-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="13f1a-108">Компонент включает разметку HTML и логику обработки, необходимую для внедрения данных или реагирования на события пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="13f1a-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="13f1a-109">Компоненты являются гибкими и облегченными.</span><span class="sxs-lookup"><span data-stu-id="13f1a-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="13f1a-110">Их можно вкладывать, использовать повторно и сразу в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="13f1a-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="13f1a-111">Классы компонентов</span><span class="sxs-lookup"><span data-stu-id="13f1a-111">Component classes</span></span>

<span data-ttu-id="13f1a-112">Компоненты реализуются в файлах компонентов [Razor](xref:mvc/views/razor) ( *.razor*) с помощью комбинации разметки HTML и C#.</span><span class="sxs-lookup"><span data-stu-id="13f1a-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="13f1a-113">Компонент в Blazor формально называется *компонентом Razor* .</span><span class="sxs-lookup"><span data-stu-id="13f1a-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="13f1a-114">Синтаксис Razor</span><span class="sxs-lookup"><span data-stu-id="13f1a-114">Razor syntax</span></span>

<span data-ttu-id="13f1a-115">В компонентах Razor приложений Blazor часто используется синтаксис Razor.</span><span class="sxs-lookup"><span data-stu-id="13f1a-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="13f1a-116">Если вы не знакомы с языком разметки Razor, сначала прочтите статью <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="13f1a-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="13f1a-117">При доступе к содержимому с синтаксисом Razor обратите особое внимание на следующее:</span><span class="sxs-lookup"><span data-stu-id="13f1a-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="13f1a-118">[Директивы](xref:mvc/views/razor#directives) — это зарезервированные ключевые слова с префиксом `@`, которые обычно меняют то, как разметка компонента анализируется и функционирует.</span><span class="sxs-lookup"><span data-stu-id="13f1a-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="13f1a-119">[Атрибуты директивы](xref:mvc/views/razor#directive-attributes) — зарезервированные ключевые слова с префиксом `@`, которые обычно меняют то, как разметка компонента анализируется и функционирует.</span><span class="sxs-lookup"><span data-stu-id="13f1a-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="13f1a-120">Имена</span><span class="sxs-lookup"><span data-stu-id="13f1a-120">Names</span></span>

<span data-ttu-id="13f1a-121">Имя компонента должно начинаться с заглавной буквы.</span><span class="sxs-lookup"><span data-stu-id="13f1a-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="13f1a-122">Например, *MyCoolComponent.razor* является допустимым, а *myCoolComponent.razor* нет.</span><span class="sxs-lookup"><span data-stu-id="13f1a-122">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="13f1a-123">Маршрутизация</span><span class="sxs-lookup"><span data-stu-id="13f1a-123">Routing</span></span>

<span data-ttu-id="13f1a-124">Маршрутизация в Blazor достигается путем предоставления шаблона маршрута каждому доступному компоненту в приложении.</span><span class="sxs-lookup"><span data-stu-id="13f1a-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="13f1a-125">При компиляции файла Razor с директивой [`@page`][9] созданному классу предоставляется атрибут <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, указывающий шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="13f1a-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="13f1a-126">Во время выполнения маршрутизатор ищет классы компонентов с <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> и отображает любой компонент, шаблон маршрута которого соответствует запрошенному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="13f1a-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="13f1a-127">Для получения дополнительной информации см. <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="13f1a-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="13f1a-128">разметку</span><span class="sxs-lookup"><span data-stu-id="13f1a-128">Markup</span></span>

<span data-ttu-id="13f1a-129">Пользовательский интерфейс для компонента определяется с помощью HTML.</span><span class="sxs-lookup"><span data-stu-id="13f1a-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="13f1a-130">Логика динамического отображения (например, выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием *Razor* .</span><span class="sxs-lookup"><span data-stu-id="13f1a-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="13f1a-131">Во время компиляции приложения разметка HTML и логика отрисовки C# преобразуются в класс компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="13f1a-132">Имя создаваемого класса соответствует имени файла.</span><span class="sxs-lookup"><span data-stu-id="13f1a-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="13f1a-133">Элементы класса компонента определяются в блоке [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="13f1a-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="13f1a-134">В блоке [`@code`][1] указываются состояние (свойства, поля) компонента и методы для обработки событий или определения другой логики компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="13f1a-135">Допускается использование нескольких блоков [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="13f1a-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="13f1a-136">Элементы компонента можно использовать как часть логики отрисовки компонента с использованием выражений C#, начинающихся с `@`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="13f1a-137">Например, поле C# отрисовывается путем добавления `@` к имени поля.</span><span class="sxs-lookup"><span data-stu-id="13f1a-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="13f1a-138">В следующем примере вычисляется и отрисовывается следующее:</span><span class="sxs-lookup"><span data-stu-id="13f1a-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="13f1a-139">`headingFontStyle` в значении свойства CSS для `font-style`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="13f1a-140">`headingText` в содержимом элемента `<h1>`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="13f1a-141">После первоначальной отрисовки компонента он повторно создает дерево отрисовки в ответ на события.</span><span class="sxs-lookup"><span data-stu-id="13f1a-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="13f1a-142">Затем Blazor сравнивает новое и прежнее дерево отрисовки и применяет все изменения в модели DOM браузера.</span><span class="sxs-lookup"><span data-stu-id="13f1a-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="13f1a-143">Компоненты являются обычными классами C# и могут размещаться в любом месте внутри проекта.</span><span class="sxs-lookup"><span data-stu-id="13f1a-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="13f1a-144">Компоненты, создающие веб-страницы, обычно находятся в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="13f1a-144">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="13f1a-145">Компоненты, не связанные со страницами, часто находятся в папке *Shared* или настраиваемой папке, добавленной в проект.</span><span class="sxs-lookup"><span data-stu-id="13f1a-145">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="13f1a-146">Пространства имен</span><span class="sxs-lookup"><span data-stu-id="13f1a-146">Namespaces</span></span>

<span data-ttu-id="13f1a-147">Как правило, пространство имен компонента является производным от корневого пространства имен приложения и расположения компонента (папки) в приложении.</span><span class="sxs-lookup"><span data-stu-id="13f1a-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="13f1a-148">Если корневым пространством имен приложения является `BlazorApp`, а компонент `Counter` находится в папке *Pages*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-148">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="13f1a-149">Пространством имен компонента `Counter` является `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-149">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="13f1a-150">Полным именем компонента является `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-150">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="13f1a-151">При использовании пользовательских папок, содержащих компоненты, добавьте директиву [`@using`][2] в родительский компонент или в файл *_Imports.razor* приложения.</span><span class="sxs-lookup"><span data-stu-id="13f1a-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="13f1a-152">В следующем примере становятся доступными компоненты в папке *Компоненты*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-152">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="13f1a-153">На компоненты также можно ссылаться с помощью полных имен, для чего не требуется директива [`@using`][2]:</span><span class="sxs-lookup"><span data-stu-id="13f1a-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="13f1a-154">Пространство имен компонента, созданного с помощью Razor, основано на следующем (в порядке приоритета).</span><span class="sxs-lookup"><span data-stu-id="13f1a-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="13f1a-155">Назначение [`@namespace`][8] в разметке файла Razor ( *.razor*) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="13f1a-155">[`@namespace`][8] designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="13f1a-156">`RootNamespace` проекта в файле проекта (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="13f1a-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="13f1a-157">Имя проекта, полученное из имени файла проекта (*CSPROJ*), и путь из корневого каталога проекта к компоненту.</span><span class="sxs-lookup"><span data-stu-id="13f1a-157">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="13f1a-158">Например, платформа разрешает *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) в пространство имен `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-158">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="13f1a-159">Компоненты соответствуют правилам привязки имен C#.</span><span class="sxs-lookup"><span data-stu-id="13f1a-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="13f1a-160">Для компонента `Index` в этом примере компонентами в области действия являются все компоненты:</span><span class="sxs-lookup"><span data-stu-id="13f1a-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="13f1a-161">в той же папке *Pages*;</span><span class="sxs-lookup"><span data-stu-id="13f1a-161">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="13f1a-162">в корневой папке проекта, которая не задает другое пространство имен явным образом.</span><span class="sxs-lookup"><span data-stu-id="13f1a-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="13f1a-163">Квалификация `global::` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="13f1a-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="13f1a-164">Импорт компонентов с инструкциями [using](/dotnet/csharp/language-reference/keywords/using-statement) с псевдонимами (например, `@using Foo = Bar`) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="13f1a-164">Importing components with aliased [using](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="13f1a-165">Частично определенные имена не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="13f1a-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="13f1a-166">Например, добавление `@using BlazorSample` и ссылка на компонент `NavMenu` (`NavMenu.razor`) с помощью `<Shared.NavMenu></Shared.NavMenu>` не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="13f1a-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="13f1a-167">Поддержка разделяемых классов</span><span class="sxs-lookup"><span data-stu-id="13f1a-167">Partial class support</span></span>

<span data-ttu-id="13f1a-168">Компоненты Razor создаются как разделяемые классы.</span><span class="sxs-lookup"><span data-stu-id="13f1a-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="13f1a-169">Создавать компоненты Razor можно одним из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="13f1a-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="13f1a-170">Код C# определяется в блоке [`@code`][1] с разметкой HTML и кодом Razor в одном файле.</span><span class="sxs-lookup"><span data-stu-id="13f1a-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="13f1a-171">Шаблоны Blazor определяют свои компоненты Razor с помощью этого подхода.</span><span class="sxs-lookup"><span data-stu-id="13f1a-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="13f1a-172">Код C# помещается в файл кода программной части, определенный как разделяемый класс.</span><span class="sxs-lookup"><span data-stu-id="13f1a-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="13f1a-173">В следующем примере показан компонент `Counter` по умолчанию с блоком [`@code`][1] в приложении, созданном из шаблона Blazor.</span><span class="sxs-lookup"><span data-stu-id="13f1a-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="13f1a-174">Разметка HTML, код Razor и код C# находятся в одном файле.</span><span class="sxs-lookup"><span data-stu-id="13f1a-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="13f1a-175">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-175">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="13f1a-176">Компонент `Counter` также можно создать, используя файл кода программной части с разделяемым классом:</span><span class="sxs-lookup"><span data-stu-id="13f1a-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="13f1a-177">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-177">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="13f1a-178">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-178">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="13f1a-179">При необходимости добавьте в файл разделяемого класса нужные пространства имен.</span><span class="sxs-lookup"><span data-stu-id="13f1a-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="13f1a-180">К типичным пространствам имен, используемым компонентами Razor, относятся следующие.</span><span class="sxs-lookup"><span data-stu-id="13f1a-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

### <a name="specify-a-base-class"></a><span data-ttu-id="13f1a-181">Указание базового класса</span><span class="sxs-lookup"><span data-stu-id="13f1a-181">Specify a base class</span></span>

<span data-ttu-id="13f1a-182">Директиву [`@inherits`][6] можно использовать для указания базового класса для компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-182">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="13f1a-183">В следующем примере показано, как компонент может наследовать базовый класс `BlazorRocksBase`, чтобы предоставить свойства и методы компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-183">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="13f1a-184">Базовый класс должен быть производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="13f1a-184">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="13f1a-185">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-185">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="13f1a-186">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-186">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="13f1a-187">Использование компонентов</span><span class="sxs-lookup"><span data-stu-id="13f1a-187">Use components</span></span>

<span data-ttu-id="13f1a-188">Компоненты могут включать другие компоненты, объявляя их с помощью синтаксиса HTML-элементов.</span><span class="sxs-lookup"><span data-stu-id="13f1a-188">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="13f1a-189">Разметка для использования компонента выглядит как тег HTML с именем, соответствующем типу компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-189">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="13f1a-190">Следующая разметка в *Index.razor* визуализирует экземпляр `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="13f1a-190">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="13f1a-191">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-191">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="13f1a-192">Если компонент содержит HTML-элемент с первой заглавной буквой, который не соответствует имени компонента, выдается предупреждение о том, что элемент имеет непредвиденное имя.</span><span class="sxs-lookup"><span data-stu-id="13f1a-192">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="13f1a-193">Добавление директивы [`@using`][2] для пространства имен компонента делает компонент доступным, что позволяет устранить это предупреждение.</span><span class="sxs-lookup"><span data-stu-id="13f1a-193">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="13f1a-194">Параметры</span><span class="sxs-lookup"><span data-stu-id="13f1a-194">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="13f1a-195">Параметры маршрута</span><span class="sxs-lookup"><span data-stu-id="13f1a-195">Route parameters</span></span>

<span data-ttu-id="13f1a-196">Компоненты могут принимать параметры маршрута из шаблона маршрута, указанного в директиве [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="13f1a-196">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="13f1a-197">Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-197">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="13f1a-198">*Pages/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-198">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="13f1a-199">Необязательные параметры не поддерживаются, поэтому в предыдущем примере применяются две директивы [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="13f1a-199">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="13f1a-200">Первая позволяет переходить к компоненту без параметра.</span><span class="sxs-lookup"><span data-stu-id="13f1a-200">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="13f1a-201">Вторая директива [`@page`][9] принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-201">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="13f1a-202">Синтаксис *универсального* параметра (`*`/`**`) **не** поддерживается в компонентах Razor ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="13f1a-202">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="13f1a-203">Параметры компонентов</span><span class="sxs-lookup"><span data-stu-id="13f1a-203">Component parameters</span></span>

<span data-ttu-id="13f1a-204">Компоненты могут иметь *параметры*, определяемые с помощью открытых свойств в классе компонента с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="13f1a-204">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="13f1a-205">Используйте атрибуты, чтобы указать аргументы для компонента в разметке.</span><span class="sxs-lookup"><span data-stu-id="13f1a-205">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="13f1a-206">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-206">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="13f1a-207">В следующем примере из примера приложения `ParentComponent` задает значение свойства `Title` для `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-207">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="13f1a-208">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-208">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="13f1a-209">Не создавайте компоненты, записывающие их в собственные *параметры компонентов*, — используйте вместо этого закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="13f1a-209">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="13f1a-210">Дополнительные сведения см. в разделе [Не создавать компоненты, которые записываются в собственные свойства параметров](#dont-create-components-that-write-to-their-own-parameter-properties).</span><span class="sxs-lookup"><span data-stu-id="13f1a-210">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="13f1a-211">Дочернее содержимое</span><span class="sxs-lookup"><span data-stu-id="13f1a-211">Child content</span></span>

<span data-ttu-id="13f1a-212">Компоненты могут задавать содержимое другого компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-212">Components can set the content of another component.</span></span> <span data-ttu-id="13f1a-213">Назначение компонента задает содержимое между тегами, указывающими принимающий компонент.</span><span class="sxs-lookup"><span data-stu-id="13f1a-213">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="13f1a-214">В следующем примере `ChildComponent` имеет свойство `ChildContent`, представляющее <xref:Microsoft.AspNetCore.Components.RenderFragment>, который представляет сегмент пользовательского интерфейса для отрисовки.</span><span class="sxs-lookup"><span data-stu-id="13f1a-214">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="13f1a-215">Значение `ChildContent` находится в том месте разметки компонента, где должно быть визуализировано содержимое.</span><span class="sxs-lookup"><span data-stu-id="13f1a-215">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="13f1a-216">Значение `ChildContent` принимается от родительского компонента и визуализируется внутри `panel-body` панели начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="13f1a-216">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="13f1a-217">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-217">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="13f1a-218">Свойству, принимающему содержимое <xref:Microsoft.AspNetCore.Components.RenderFragment>, по соглашению необходимо присвоить имя `ChildContent`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-218">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="13f1a-219">`ParentComponent` в примере приложения может предоставить содержимое для отрисовки `ChildComponent`, заключив содержимое в теги `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-219">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="13f1a-220">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-220">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="13f1a-221">Сплаттинг атрибутов и произвольные параметры</span><span class="sxs-lookup"><span data-stu-id="13f1a-221">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="13f1a-222">Компоненты могут записывать и визуализировать дополнительные атрибуты в дополнение к объявленным параметрам компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-222">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="13f1a-223">Можно записать дополнительные атрибуты в словарь, а затем выполнить *сплаттинг* для элемента при подготовке отрисовки компонента с помощью директивы [`@attributes`][3] Razor.</span><span class="sxs-lookup"><span data-stu-id="13f1a-223">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="13f1a-224">Этот сценарий полезен при определении компонента, который создает элемент разметки, поддерживающий разнообразные настройки.</span><span class="sxs-lookup"><span data-stu-id="13f1a-224">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="13f1a-225">Например, может оказаться утомительным по отдельности определять атрибуты для `<input>`, поддерживающего много параметров.</span><span class="sxs-lookup"><span data-stu-id="13f1a-225">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="13f1a-226">В следующем примере первый элемент `<input>` (`id="useIndividualParams"`) использует отдельные параметры компонента, а второй элемент `<input>` (`id="useAttributesDict"`) использует сплаттинг атрибутов:</span><span class="sxs-lookup"><span data-stu-id="13f1a-226">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="13f1a-227">Тип параметра должен реализовывать `IEnumerable<KeyValuePair<string, object>>` с ключами строки.</span><span class="sxs-lookup"><span data-stu-id="13f1a-227">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="13f1a-228">В этом сценарии также можно использовать `IReadOnlyDictionary<string, object>`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-228">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="13f1a-229">При использовании обоих подходов получаются идентичные отрисованные элементы `<input>`:</span><span class="sxs-lookup"><span data-stu-id="13f1a-229">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="13f1a-230">Чтобы принять произвольные атрибуты, определите параметр компонента с помощью атрибута [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) со свойством <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>, имеющим значение `true`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-230">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="13f1a-231">Свойство <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> в [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) позволяет параметру соответствовать всем атрибутам, которые не соответствуют никакому другому параметру.</span><span class="sxs-lookup"><span data-stu-id="13f1a-231">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="13f1a-232">Компонент может определять только один параметр с <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span><span class="sxs-lookup"><span data-stu-id="13f1a-232">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="13f1a-233">Тип свойства, используемый с <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>, должен быть назначаемым из `Dictionary<string, object>` с ключами строки.</span><span class="sxs-lookup"><span data-stu-id="13f1a-233">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="13f1a-234">В этом сценарии также можно использовать `IEnumerable<KeyValuePair<string, object>>` или `IReadOnlyDictionary<string, object>`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-234">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="13f1a-235">Расположение [`@attributes`][3] относительно положения атрибутов элемента имеет значение.</span><span class="sxs-lookup"><span data-stu-id="13f1a-235">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="13f1a-236">Когда выполняется сплаттинг [`@attributes`][3] для элемента, атрибуты обрабатываются справа налево (от последнего к первому).</span><span class="sxs-lookup"><span data-stu-id="13f1a-236">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="13f1a-237">Рассмотрим следующий пример компонента, использующего компонент `Child`:</span><span class="sxs-lookup"><span data-stu-id="13f1a-237">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="13f1a-238">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-238">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="13f1a-239">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-239">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="13f1a-240">Атрибут `extra` компонента `Child` стоит справа от [`@attributes`][3].</span><span class="sxs-lookup"><span data-stu-id="13f1a-240">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="13f1a-241">`<div>`, визуализируемый компонентом `Parent`, содержит `extra="5"` при передаче через дополнительный атрибут, так как атрибуты обрабатываются справа налево (от последнего к первому):</span><span class="sxs-lookup"><span data-stu-id="13f1a-241">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="13f1a-242">В следующем примере порядок `extra` и [`@attributes`][3] в `<div>` компонента `Child` изменен на противоположный:</span><span class="sxs-lookup"><span data-stu-id="13f1a-242">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="13f1a-243">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-243">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="13f1a-244">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-244">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="13f1a-245">Визуализируемый `<div>` в компоненте `Parent` содержит `extra="10"` при передаче через дополнительный атрибут:</span><span class="sxs-lookup"><span data-stu-id="13f1a-245">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="13f1a-246">Запись ссылок на компоненты</span><span class="sxs-lookup"><span data-stu-id="13f1a-246">Capture references to components</span></span>

<span data-ttu-id="13f1a-247">Ссылки на компоненты позволяют ссылаться на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра, например `Show` или `Reset`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-247">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="13f1a-248">Чтобы записать ссылку на компонент, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="13f1a-248">To capture a component reference:</span></span>

* <span data-ttu-id="13f1a-249">Добавьте к дочернему компоненту атрибут [`@ref`][4].</span><span class="sxs-lookup"><span data-stu-id="13f1a-249">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="13f1a-250">Определите поле с тем же типом, что и у дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-250">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="13f1a-251">При отрисовке компонента поле `loginDialog` заполняется экземпляром дочернего компонента `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-251">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="13f1a-252">Затем можно вызывать методы .NET в экземпляре компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-252">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="13f1a-253">Переменная `loginDialog` заполняется только после отрисовки компонента, а ее выходные данные включают элемент `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-253">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="13f1a-254">До этого момента ссылаться не на что.</span><span class="sxs-lookup"><span data-stu-id="13f1a-254">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="13f1a-255">Для управления ссылками на компоненты после завершения отрисовки компонента используйте [методы OnAfterRenderAsync или OnAfterRender](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="13f1a-255">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="13f1a-256">Сведения о ссылках на компоненты в цикле см. в разделе [Получение ссылок на несколько схожих дочерних компонентов (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="13f1a-256">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="13f1a-257">При записи ссылок на компоненты используется аналогичный синтаксис для [записи ссылок на элементы](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), это не функция взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="13f1a-257">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="13f1a-258">Ссылки на компоненты не передаются в код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="13f1a-258">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="13f1a-259">Они используются только в коде .NET.</span><span class="sxs-lookup"><span data-stu-id="13f1a-259">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="13f1a-260">**Не** используйте ссылки на компоненты для изменения состояния дочерних компонентов.</span><span class="sxs-lookup"><span data-stu-id="13f1a-260">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="13f1a-261">Вместо этого используйте обычные декларативные параметры для передачи данных дочерним компонентам.</span><span class="sxs-lookup"><span data-stu-id="13f1a-261">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="13f1a-262">Использование обычных декларативных параметров приводит к тому, что дочерние компоненты автоматически визуализируются в нужное время.</span><span class="sxs-lookup"><span data-stu-id="13f1a-262">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="13f1a-263">Внешний вызов методов компонента для изменения состояния</span><span class="sxs-lookup"><span data-stu-id="13f1a-263">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="13f1a-264"> использует контекст синхронизации (<xref:System.Threading.SynchronizationContext>) для принудительного использования одного логического потока выполнения.</span><span class="sxs-lookup"><span data-stu-id="13f1a-264"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="13f1a-265">[Методы жизненного цикла ](xref:blazor/components/lifecycle) компонента и все обратные вызовы событий, сделанные Blazor, выполняются в этом контексте синхронизации.</span><span class="sxs-lookup"><span data-stu-id="13f1a-265">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="13f1a-266">Контекст синхронизации Blazor Server пытается эмулировать однопоточную среду таким образом, чтобы она точно соответствовала модели WebAssembly в браузере, которая является однопоточной.</span><span class="sxs-lookup"><span data-stu-id="13f1a-266">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="13f1a-267">В любой момент времени работа выполняется только в одном потоке, что создает впечатление единого логического потока.</span><span class="sxs-lookup"><span data-stu-id="13f1a-267">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="13f1a-268">Две операции не могут выполняться одновременно.</span><span class="sxs-lookup"><span data-stu-id="13f1a-268">No two operations execute concurrently.</span></span>

<span data-ttu-id="13f1a-269">Если компонент нужно изменить на основе внешнего события, такого как таймер или другие уведомления, используйте метод `InvokeAsync`, который выполняет отправку в контекст синхронизации Blazor.</span><span class="sxs-lookup"><span data-stu-id="13f1a-269">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="13f1a-270">Например, рассмотрим *службу уведомителя*, которая может уведомлять любой компонент, ожидающий передачи данных, об измененном состоянии:</span><span class="sxs-lookup"><span data-stu-id="13f1a-270">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="13f1a-271">Зарегистрируйте службу `NotifierService` как одноэлементную:</span><span class="sxs-lookup"><span data-stu-id="13f1a-271">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="13f1a-272">В Blazor WebAssembly зарегистрируйте службу в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="13f1a-272">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="13f1a-273">В Blazor Server зарегистрируйте службу в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="13f1a-273">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="13f1a-274">Используйте `NotifierService` для изменения компонента:</span><span class="sxs-lookup"><span data-stu-id="13f1a-274">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="13f1a-275">В предыдущем примере `NotifierService` вызывает метод `OnNotify` компонента вне контекста синхронизации Blazor.</span><span class="sxs-lookup"><span data-stu-id="13f1a-275">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="13f1a-276">`InvokeAsync` используется для переключения на подходящий контекст и постановки отрисовки в очередь.</span><span class="sxs-lookup"><span data-stu-id="13f1a-276">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="13f1a-277">Использование \@key для управления сохранением элементов и компонентов</span><span class="sxs-lookup"><span data-stu-id="13f1a-277">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="13f1a-278">При отрисовке списка элементов или компонентов и последующем изменении элементов или компонентов алгоритм сравнения Blazor должен решить, какие из предыдущих элементов или компонентов можно оставить и как следует сопоставить с ними объекты модели.</span><span class="sxs-lookup"><span data-stu-id="13f1a-278">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="13f1a-279">Обычно этот процесс выполняется автоматически, и его можно игнорировать, но в некоторых случаях может потребоваться управлять данным процессом.</span><span class="sxs-lookup"><span data-stu-id="13f1a-279">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="13f1a-280">Рассмотрим следующий пример.</span><span class="sxs-lookup"><span data-stu-id="13f1a-280">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="13f1a-281">Содержимое коллекции `People` может изменяться при вставке, удалении или повторном упорядочении записей.</span><span class="sxs-lookup"><span data-stu-id="13f1a-281">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="13f1a-282">Когда компонент отрисовывается повторно, компонент `<DetailsEditor>` может измениться на получение других значений параметра `Details`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-282">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="13f1a-283">Это может усложнить повторную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="13f1a-283">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="13f1a-284">В некоторых случаях повторная отрисовка может привести к появлению заметных различий в поведении, таких как потеря фокуса элемента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-284">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="13f1a-285">Процесс сопоставления можно контролировать с помощью атрибута директивы [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="13f1a-285">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="13f1a-286">[`@key`][5] гарантирует, что алгоритм сравнения сохранит элементы или компоненты на основе значения ключа:</span><span class="sxs-lookup"><span data-stu-id="13f1a-286">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="13f1a-287">При изменении коллекции `People` алгоритм сравнения сохраняет связь между экземплярами `<DetailsEditor>` и экземплярами `person`:</span><span class="sxs-lookup"><span data-stu-id="13f1a-287">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="13f1a-288">Если `Person` удаляется из списка `People`, то из пользовательского интерфейса удаляется только соответствующий экземпляр `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-288">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="13f1a-289">Другие экземпляры остаются без изменений.</span><span class="sxs-lookup"><span data-stu-id="13f1a-289">Other instances are left unchanged.</span></span>
* <span data-ttu-id="13f1a-290">Если в какой-либо позиции в списке вставляется `Person`, то в соответствующей позиции вставляется один новый экземпляр `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-290">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="13f1a-291">Другие экземпляры остаются без изменений.</span><span class="sxs-lookup"><span data-stu-id="13f1a-291">Other instances are left unchanged.</span></span>
* <span data-ttu-id="13f1a-292">При переупорядочении записей `Person` соответствующие экземпляры `<DetailsEditor>` сохраняются и переупорядочиваются в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="13f1a-292">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="13f1a-293">В некоторых сценариях использование [`@key`][5] уменьшает сложность повторной отрисовки и предотвращает потенциальные проблемы с изменяющимися элементами модели DOM с отслеживанием состояния, например положением фокуса.</span><span class="sxs-lookup"><span data-stu-id="13f1a-293">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="13f1a-294">Ключи являются локальными для каждого компонента или элемента контейнера.</span><span class="sxs-lookup"><span data-stu-id="13f1a-294">Keys are local to each container element or component.</span></span> <span data-ttu-id="13f1a-295">Ключи не сравниваются глобально по всему документу.</span><span class="sxs-lookup"><span data-stu-id="13f1a-295">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="13f1a-296">Условия для использования \@key</span><span class="sxs-lookup"><span data-stu-id="13f1a-296">When to use \@key</span></span>

<span data-ttu-id="13f1a-297">Как правило, [`@key`][5] имеет смысл использовать при отрисовке списка (например, в блоке [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)) и при наличии подходящего значения для определения [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="13f1a-297">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="13f1a-298">Можно также использовать [`@key`][5], чтобы запретить Blazor сохранять поддерево элементов или компонентов при изменении объекта:</span><span class="sxs-lookup"><span data-stu-id="13f1a-298">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="13f1a-299">Если `@currentPerson` меняется, директива атрибута [`@key`][5] принуждает Blazor отменить весь блок элемента `<div>` с потомками и перестроить поддерево в пользовательском интерфейсе с использованием новых элементов и компонентов.</span><span class="sxs-lookup"><span data-stu-id="13f1a-299">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="13f1a-300">Это может быть полезно, если нужно гарантировать, что при изменении `@currentPerson` состояние пользовательского интерфейса не сохраняется.</span><span class="sxs-lookup"><span data-stu-id="13f1a-300">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="13f1a-301">Условия для отказа от использования \@key</span><span class="sxs-lookup"><span data-stu-id="13f1a-301">When not to use \@key</span></span>

<span data-ttu-id="13f1a-302">Сравнение с использованием [`@key`][5] подразумевает определенное снижение производительности.</span><span class="sxs-lookup"><span data-stu-id="13f1a-302">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="13f1a-303">Это снижение производительности незначительно, но указывать [`@key`][5] следует только в том случае, если управление правилами сохранения элементов или компонентов выгодно для приложения.</span><span class="sxs-lookup"><span data-stu-id="13f1a-303">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="13f1a-304">Даже если [`@key`][5] не используется, Blazor сохраняет экземпляры дочерних элементов и компонентов в максимально возможной степени.</span><span class="sxs-lookup"><span data-stu-id="13f1a-304">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="13f1a-305">Единственным преимуществом использования [`@key`][5] является контроль над тем, *как* экземпляры модели сопоставляются с сохраненными экземплярами компонентов, вместо выбора сопоставления с помощью алгоритма сравнения.</span><span class="sxs-lookup"><span data-stu-id="13f1a-305">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="13f1a-306">Значения, которые следует использовать для \@key</span><span class="sxs-lookup"><span data-stu-id="13f1a-306">What values to use for \@key</span></span>

<span data-ttu-id="13f1a-307">Как правило, для [`@key`][5] имеет смысл указать значение одного из следующих видов:</span><span class="sxs-lookup"><span data-stu-id="13f1a-307">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="13f1a-308">Экземпляры объектов модели (например, экземпляр `Person`, как в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="13f1a-308">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="13f1a-309">Это гарантирует сохранение на основе равенства ссылок на объекты.</span><span class="sxs-lookup"><span data-stu-id="13f1a-309">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="13f1a-310">Уникальные идентификаторы (например, значения первичного ключа типа `int`, `string` или `Guid`).</span><span class="sxs-lookup"><span data-stu-id="13f1a-310">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="13f1a-311">Убедитесь, что значения, используемые для [`@key`][5], не конфликтуют.</span><span class="sxs-lookup"><span data-stu-id="13f1a-311">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="13f1a-312">Если в одном родительском элементе обнаруживаются конфликтующие значения, Blazor выдает исключение, поскольку не может детерминированно сопоставлять старые элементы или компоненты с новыми.</span><span class="sxs-lookup"><span data-stu-id="13f1a-312">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="13f1a-313">Используйте только уникальные значения, такие как экземпляры объекта или значения первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="13f1a-313">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="13f1a-314">Не создавать компоненты, которые записываются в собственные свойства параметров</span><span class="sxs-lookup"><span data-stu-id="13f1a-314">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="13f1a-315">Параметры перезаписываются при следующих условиях.</span><span class="sxs-lookup"><span data-stu-id="13f1a-315">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="13f1a-316">Содержимое дочернего компонента подготавливается к просмотру с помощью <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span><span class="sxs-lookup"><span data-stu-id="13f1a-316">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="13f1a-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается в родительском компоненте.</span><span class="sxs-lookup"><span data-stu-id="13f1a-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="13f1a-318">Параметры сбрасываются, так как родительский компонент перерисовывается при вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, а новые значения параметров передаются дочернему компоненту.</span><span class="sxs-lookup"><span data-stu-id="13f1a-318">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="13f1a-319">Рассмотрим следующий компонент `Expander`, который:</span><span class="sxs-lookup"><span data-stu-id="13f1a-319">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="13f1a-320">преобразует дочернее содержимое;</span><span class="sxs-lookup"><span data-stu-id="13f1a-320">Renders child content.</span></span>
* <span data-ttu-id="13f1a-321">переключает отображение дочернего содержимого с помощью параметра компонента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-321">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="13f1a-322">Компонент `Expander` добавляется в родительский компонент, который может вызывать <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span><span class="sxs-lookup"><span data-stu-id="13f1a-322">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="13f1a-323">Изначально компоненты `Expander` работают независимо, когда их свойства `Expanded` переключаются.</span><span class="sxs-lookup"><span data-stu-id="13f1a-323">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="13f1a-324">Дочерние компоненты сохраняют свои состояния, как и ожидалось.</span><span class="sxs-lookup"><span data-stu-id="13f1a-324">The child components maintain their states as expected.</span></span> <span data-ttu-id="13f1a-325">Когда в родительском элементе вызывается <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, параметр `Expanded` первого дочернего компонента сбрасывается обратно к первоначальному значению (`true`).</span><span class="sxs-lookup"><span data-stu-id="13f1a-325">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="13f1a-326">Значение `Expanded` второго компонента `Expander` не сбрасывается, так как во втором компоненте не отображается дочернее содержимое.</span><span class="sxs-lookup"><span data-stu-id="13f1a-326">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="13f1a-327">Чтобы сохранить состояние в предыдущем сценарии, используйте *закрытое поле* в компоненте `Expander`, чтобы сохранить состояние переключения.</span><span class="sxs-lookup"><span data-stu-id="13f1a-327">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="13f1a-328">Приведенный ниже компонент `Expander` делает следующее.</span><span class="sxs-lookup"><span data-stu-id="13f1a-328">The following `Expander` component:</span></span>

* <span data-ttu-id="13f1a-329">Принимает значение параметра компонента `Expanded` из родительского элемента.</span><span class="sxs-lookup"><span data-stu-id="13f1a-329">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="13f1a-330">Присваивает значение параметра компонента *закрытому полю* (`expanded`) при [событии OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="13f1a-330">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="13f1a-331">Использует закрытое поле для поддержания внутреннего состояния переключения.</span><span class="sxs-lookup"><span data-stu-id="13f1a-331">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="apply-an-attribute"></a><span data-ttu-id="13f1a-332">Применение атрибута</span><span class="sxs-lookup"><span data-stu-id="13f1a-332">Apply an attribute</span></span>

<span data-ttu-id="13f1a-333">Атрибуты можно применять к компонентам Razor с помощью директивы [`@attribute`][7].</span><span class="sxs-lookup"><span data-stu-id="13f1a-333">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="13f1a-334">В следующем примере атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) применяется к классу компонентов:</span><span class="sxs-lookup"><span data-stu-id="13f1a-334">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="13f1a-335">Условные атрибуты элемента HTML</span><span class="sxs-lookup"><span data-stu-id="13f1a-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="13f1a-336">Атрибуты элемента HTML условно визуализируются в зависимости от значения .NET.</span><span class="sxs-lookup"><span data-stu-id="13f1a-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="13f1a-337">Если значение равно `false` или `null`, то атрибут не визуализируется.</span><span class="sxs-lookup"><span data-stu-id="13f1a-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="13f1a-338">Если значение равно `true`, атрибут визуализируется в свернутом виде.</span><span class="sxs-lookup"><span data-stu-id="13f1a-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="13f1a-339">В следующем примере `IsCompleted` определяет, визуализируется ли `checked` в разметке элемента:</span><span class="sxs-lookup"><span data-stu-id="13f1a-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="13f1a-340">Если `IsCompleted` имеет значение `true`, флажок визуализируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="13f1a-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="13f1a-341">Если `IsCompleted` имеет значение `false`, флажок визуализируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="13f1a-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="13f1a-342">Для получения дополнительной информации см. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="13f1a-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="13f1a-343">Некоторые атрибуты HTML, такие как [, aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), работают неправильно, если типом .NET является `bool`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="13f1a-344">В этих случаях используйте тип `string` вместо `bool`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="13f1a-345">Необработанный HTML-код</span><span class="sxs-lookup"><span data-stu-id="13f1a-345">Raw HTML</span></span>

<span data-ttu-id="13f1a-346">Строки обычно визуализируются с помощью текстовых узлов модели DOM, что означает, что любая разметка, которую они могут содержать, игнорируется и обрабатывается как текстовый литерал.</span><span class="sxs-lookup"><span data-stu-id="13f1a-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="13f1a-347">Для отрисовки необработанного HTML-кода заключите HTML-содержимое в значение `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="13f1a-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="13f1a-348">Это значение анализируется как HTML или SVG и вставляется в модель DOM.</span><span class="sxs-lookup"><span data-stu-id="13f1a-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="13f1a-349">Отрисовка необработанного HTML-кода, созданного из любого ненадежного источника, является **угрозой безопасности**, и ее следует избегать!</span><span class="sxs-lookup"><span data-stu-id="13f1a-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="13f1a-350">В следующем примере показано использование типа `MarkupString` для добавления блока статического HTML-содержимого в визуализируемые выходные данные компонента:</span><span class="sxs-lookup"><span data-stu-id="13f1a-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="13f1a-351">Шаблоны Razor</span><span class="sxs-lookup"><span data-stu-id="13f1a-351">Razor templates</span></span>

<span data-ttu-id="13f1a-352">Фрагменты отрисовки можно определить с помощью синтаксиса шаблонов Razor.</span><span class="sxs-lookup"><span data-stu-id="13f1a-352">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="13f1a-353">Шаблоны Razor позволяют определить фрагмент кода пользовательского интерфейса и подразумевают следующий формат.</span><span class="sxs-lookup"><span data-stu-id="13f1a-353">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="13f1a-354">В следующем примере показано, как указать значения <xref:Microsoft.AspNetCore.Components.RenderFragment> и <xref:Microsoft.AspNetCore.Components.RenderFragment%601> и визуализировать шаблоны непосредственно в компоненте.</span><span class="sxs-lookup"><span data-stu-id="13f1a-354">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="13f1a-355">Фрагменты отрисовки также могут передаваться в качестве аргументов в [шаблонные компоненты](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="13f1a-355">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="13f1a-356">Визуализированные выходные данные предыдущего кода:</span><span class="sxs-lookup"><span data-stu-id="13f1a-356">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="13f1a-357">Статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="13f1a-357">Static assets</span></span>

Blazor<span data-ttu-id="13f1a-358"> соответствует соглашению для приложений ASP.NET Core о размещении статических ресурсов в [корневом веб-каталоге (wwwroot)](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="13f1a-358"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="13f1a-359">Используйте базовый относительный путь (`/`) для ссылки на корневой веб-каталог статического ресурса.</span><span class="sxs-lookup"><span data-stu-id="13f1a-359">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="13f1a-360">В следующем примере *logo.png* физически находится в папке *{PROJECT ROOT}/wwwroot/images*:</span><span class="sxs-lookup"><span data-stu-id="13f1a-360">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="13f1a-361">Компоненты Razor **не** поддерживают нотацию тильды с косой чертой (`~/`).</span><span class="sxs-lookup"><span data-stu-id="13f1a-361">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="13f1a-362">Сведения о настройке базового пути приложения см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="13f1a-362">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="13f1a-363">Вспомогательные функции тегов в компонентах не поддерживаются</span><span class="sxs-lookup"><span data-stu-id="13f1a-363">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="13f1a-364">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) не поддерживаются в компонентах Razor (файлы *.razor*).</span><span class="sxs-lookup"><span data-stu-id="13f1a-364">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="13f1a-365">Чтобы обеспечить функциональные возможности, аналогичные вспомогательным функциям тегов, в Blazor, создайте компонент с теми же функциональными возможностями, что и вспомогательная функция тега, и используйте его вместо нее.</span><span class="sxs-lookup"><span data-stu-id="13f1a-365">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="13f1a-366">Изображения SVG</span><span class="sxs-lookup"><span data-stu-id="13f1a-366">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="13f1a-367">Так как Blazor отображает визуализирует HTML-код, поддерживаемые браузером изображения, включая изображения *SVG*, поддерживаются с помощью тега `<img>`:</span><span class="sxs-lookup"><span data-stu-id="13f1a-367">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="13f1a-368">Аналогичным образом изображения SVG поддерживаются в правилах CSS файла таблицы стилей (*CSS*):</span><span class="sxs-lookup"><span data-stu-id="13f1a-368">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="13f1a-369">Однако встроенная разметка SVG не поддерживается во всех сценариях.</span><span class="sxs-lookup"><span data-stu-id="13f1a-369">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="13f1a-370">Если поместить тег `<svg>` непосредственно в файл компонента (*RAZOR*), то базовая отрисовка изображений доступна, но многие расширенные сценарии пока не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="13f1a-370">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="13f1a-371">Например, теги `<use>` сейчас не учитываются, а с некоторыми тегами SVG невозможно использовать [`@bind`][10].</span><span class="sxs-lookup"><span data-stu-id="13f1a-371">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="13f1a-372">Дополнительные сведения см. в [справке по SVG в Blazor (dotnet/aspnetcore#18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="13f1a-372">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13f1a-373">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="13f1a-373">Additional resources</span></span>

* <span data-ttu-id="13f1a-374"><xref:blazor/security/server/threat-mitigation>. Содержит рекомендации по созданию приложений Blazor Server, которые должны соперничать в условиях нехватки ресурсов.</span><span class="sxs-lookup"><span data-stu-id="13f1a-374"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>