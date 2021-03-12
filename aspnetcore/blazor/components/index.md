---
title: Создание и использование компонентов Razor ASP.NET Core
author: guardrex
description: Сведения о том, как создавать и использовать компоненты Razor, включая способы привязки к данным, обработки событий и управления жизненным циклом компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
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
uid: blazor/components/index
ms.openlocfilehash: 7048fe7107fb71d632bf12df9d53e6f168d5a0db
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587467"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="7e174-103">Создание и использование компонентов Razor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7e174-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="7e174-104">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7e174-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7e174-105">Приложения Blazor создаются с использованием *компонентов*.</span><span class="sxs-lookup"><span data-stu-id="7e174-105">Blazor apps are built using *components*.</span></span> <span data-ttu-id="7e174-106">Компонент — это автономный блок пользовательского интерфейса, такой как страница, диалоговое окно или форма.</span><span class="sxs-lookup"><span data-stu-id="7e174-106">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="7e174-107">Компонент включает разметку HTML и логику обработки, необходимую для внедрения данных или реагирования на события пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="7e174-107">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="7e174-108">Компоненты являются гибкими и облегченными.</span><span class="sxs-lookup"><span data-stu-id="7e174-108">Components are flexible and lightweight.</span></span> <span data-ttu-id="7e174-109">Их можно вкладывать, использовать повторно и сразу в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="7e174-109">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="7e174-110">Классы компонентов</span><span class="sxs-lookup"><span data-stu-id="7e174-110">Component classes</span></span>

<span data-ttu-id="7e174-111">Компоненты реализуются в файлах компонентов [Razor](xref:mvc/views/razor) (`.razor`) с помощью комбинации разметки HTML и C#.</span><span class="sxs-lookup"><span data-stu-id="7e174-111">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="7e174-112">Компонент в Blazor формально называется *компонентом Razor* .</span><span class="sxs-lookup"><span data-stu-id="7e174-112">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="7e174-113">Синтаксис Razor</span><span class="sxs-lookup"><span data-stu-id="7e174-113">Razor syntax</span></span>

<span data-ttu-id="7e174-114">В компонентах Razor приложений Blazor часто используется синтаксис Razor.</span><span class="sxs-lookup"><span data-stu-id="7e174-114">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="7e174-115">Если вы не знакомы с языком разметки Razor, рекомендуем сначала ознакомиться со [справочником по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="7e174-115">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="7e174-116">При доступе к содержимому с синтаксисом Razor обратите особое внимание на следующее:</span><span class="sxs-lookup"><span data-stu-id="7e174-116">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="7e174-117">[Директивы](xref:mvc/views/razor#directives) — это зарезервированные ключевые слова с префиксом `@`, которые обычно меняют то, как разметка компонента анализируется и функционирует.</span><span class="sxs-lookup"><span data-stu-id="7e174-117">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="7e174-118">[Атрибуты директивы](xref:mvc/views/razor#directive-attributes) — зарезервированные ключевые слова с префиксом `@`, которые обычно меняют то, как разметка компонента анализируется и функционирует.</span><span class="sxs-lookup"><span data-stu-id="7e174-118">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="7e174-119">Имена</span><span class="sxs-lookup"><span data-stu-id="7e174-119">Names</span></span>

<span data-ttu-id="7e174-120">Имя компонента должно начинаться с заглавной буквы.</span><span class="sxs-lookup"><span data-stu-id="7e174-120">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="7e174-121">Например, `MyCoolComponent.razor` допустимо использовать, а `myCoolComponent.razor` нет.</span><span class="sxs-lookup"><span data-stu-id="7e174-121">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="7e174-122">Маршрутизация</span><span class="sxs-lookup"><span data-stu-id="7e174-122">Routing</span></span>

<span data-ttu-id="7e174-123">Маршрутизация в Blazor достигается путем предоставления шаблона маршрута каждому доступному компоненту в приложении.</span><span class="sxs-lookup"><span data-stu-id="7e174-123">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="7e174-124">При компиляции файла Razor с директивой [`@page`][9] созданному классу предоставляется атрибут <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, указывающий шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="7e174-124">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="7e174-125">Во время выполнения маршрутизатор ищет классы компонентов с <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> и отображает любой компонент, шаблон маршрута которого соответствует запрошенному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="7e174-125">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="7e174-126">Для получения дополнительной информации см. <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="7e174-126">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="7e174-127">разметку</span><span class="sxs-lookup"><span data-stu-id="7e174-127">Markup</span></span>

<span data-ttu-id="7e174-128">Пользовательский интерфейс для компонента определяется с помощью HTML.</span><span class="sxs-lookup"><span data-stu-id="7e174-128">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="7e174-129">Логика динамического отображения (например, выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием *Razor* .</span><span class="sxs-lookup"><span data-stu-id="7e174-129">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="7e174-130">Во время компиляции приложения разметка HTML и логика отрисовки C# преобразуются в класс компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-130">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="7e174-131">Имя создаваемого класса соответствует имени файла.</span><span class="sxs-lookup"><span data-stu-id="7e174-131">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="7e174-132">Элементы класса компонента определяются в блоке [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="7e174-132">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="7e174-133">В блоке [`@code`][1] указываются состояние (свойства, поля) компонента и методы для обработки событий или определения другой логики компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-133">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="7e174-134">Допускается использование нескольких блоков [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="7e174-134">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="7e174-135">Элементы компонента можно использовать как часть логики отрисовки компонента с использованием выражений C#, начинающихся с `@`.</span><span class="sxs-lookup"><span data-stu-id="7e174-135">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="7e174-136">Например, поле C# отрисовывается путем добавления `@` к имени поля.</span><span class="sxs-lookup"><span data-stu-id="7e174-136">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="7e174-137">В следующем примере вычисляется и отрисовывается следующее:</span><span class="sxs-lookup"><span data-stu-id="7e174-137">The following example evaluates and renders:</span></span>

* <span data-ttu-id="7e174-138">`headingFontStyle` в значении свойства CSS для `font-style`.</span><span class="sxs-lookup"><span data-stu-id="7e174-138">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="7e174-139">`headingText` в содержимом элемента `<h1>`.</span><span class="sxs-lookup"><span data-stu-id="7e174-139">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="7e174-140">После первоначальной отрисовки компонента он повторно создает дерево отрисовки в ответ на события.</span><span class="sxs-lookup"><span data-stu-id="7e174-140">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="7e174-141">Затем Blazor сравнивает новое и прежнее дерево отрисовки и применяет все изменения в модели DOM браузера.</span><span class="sxs-lookup"><span data-stu-id="7e174-141">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="7e174-142">Дополнительные сведения приведены в статье <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="7e174-142">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="7e174-143">Компоненты являются обычными классами C# и могут размещаться в любом месте внутри проекта.</span><span class="sxs-lookup"><span data-stu-id="7e174-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="7e174-144">Компоненты, создающие веб-страницы, обычно находятся в папке `Pages`.</span><span class="sxs-lookup"><span data-stu-id="7e174-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="7e174-145">Компоненты, не являющиеся страницами, часто находятся в папке `Shared` или пользовательской папке, добавленной в проект.</span><span class="sxs-lookup"><span data-stu-id="7e174-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="7e174-146">Пространства имен</span><span class="sxs-lookup"><span data-stu-id="7e174-146">Namespaces</span></span>

<span data-ttu-id="7e174-147">Как правило, пространство имен компонента является производным от корневого пространства имен приложения и расположения компонента (папки) в приложении.</span><span class="sxs-lookup"><span data-stu-id="7e174-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="7e174-148">Если пространством имен корня приложения является `BlazorSample`, а компонент `Counter` находится в папке `Pages`:</span><span class="sxs-lookup"><span data-stu-id="7e174-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="7e174-149">Пространством имен компонента `Counter` является `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="7e174-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="7e174-150">Полным именем компонента является `BlazorSample.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="7e174-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="7e174-151">При использовании пользовательских папок, содержащих компоненты, добавьте директиву [`@using`][2] в родительский компонент или в файл `_Imports.razor` приложения.</span><span class="sxs-lookup"><span data-stu-id="7e174-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="7e174-152">В следующем примере становятся доступными компоненты в папке `Components`.</span><span class="sxs-lookup"><span data-stu-id="7e174-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="7e174-153">На компоненты также можно ссылаться с помощью полных имен, для чего не требуется директива [`@using`][2]:</span><span class="sxs-lookup"><span data-stu-id="7e174-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="7e174-154">Пространство имен компонента, созданного с помощью Razor, основано на следующем (в порядке приоритета).</span><span class="sxs-lookup"><span data-stu-id="7e174-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="7e174-155">Назначение [`@namespace`][8] в разметке (`@namespace BlazorSample.MyNamespace`) файла Razor (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="7e174-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="7e174-156">`RootNamespace` проекта в файле проекта (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="7e174-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="7e174-157">Имя проекта, полученное из имени файла проекта (`.csproj`), и путь из корневого каталога проекта к компоненту.</span><span class="sxs-lookup"><span data-stu-id="7e174-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="7e174-158">Например, платформа разрешает `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) в пространство имен `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="7e174-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="7e174-159">Компоненты соответствуют правилам привязки имен C#.</span><span class="sxs-lookup"><span data-stu-id="7e174-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="7e174-160">Для компонента `Index` в этом примере компонентами в области действия являются все компоненты:</span><span class="sxs-lookup"><span data-stu-id="7e174-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="7e174-161">в этой же папке `Pages`;</span><span class="sxs-lookup"><span data-stu-id="7e174-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="7e174-162">в корневой папке проекта, которая не задает другое пространство имен явным образом.</span><span class="sxs-lookup"><span data-stu-id="7e174-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="7e174-163">Квалификация `global::` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="7e174-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="7e174-164">Импорт компонентов с инструкциями [`using`](/dotnet/csharp/language-reference/keywords/using-statement), содержащими псевдонимы, (например, `@using Foo = Bar`) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="7e174-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="7e174-165">Частично определенные имена не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="7e174-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="7e174-166">Например, добавление `@using BlazorSample` и ссылка на компонент `NavMenu` (`NavMenu.razor`) с помощью `<Shared.NavMenu></Shared.NavMenu>` не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="7e174-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="7e174-167">Поддержка разделяемых классов</span><span class="sxs-lookup"><span data-stu-id="7e174-167">Partial class support</span></span>

<span data-ttu-id="7e174-168">Компоненты Razor создаются как разделяемые классы.</span><span class="sxs-lookup"><span data-stu-id="7e174-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="7e174-169">Создавать компоненты Razor можно одним из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="7e174-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="7e174-170">Код C# определяется в блоке [`@code`][1] с разметкой HTML и кодом Razor в одном файле.</span><span class="sxs-lookup"><span data-stu-id="7e174-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="7e174-171">Шаблоны Blazor определяют свои компоненты Razor с помощью этого подхода.</span><span class="sxs-lookup"><span data-stu-id="7e174-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="7e174-172">Код C# помещается в файл кода программной части, определенный как разделяемый класс.</span><span class="sxs-lookup"><span data-stu-id="7e174-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="7e174-173">В следующем примере показан компонент `Counter` по умолчанию с блоком [`@code`][1] в приложении, созданном из шаблона Blazor.</span><span class="sxs-lookup"><span data-stu-id="7e174-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="7e174-174">Разметка HTML, код Razor и код C# находятся в одном файле.</span><span class="sxs-lookup"><span data-stu-id="7e174-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="7e174-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-175">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="7e174-176">Компонент `Counter` также можно создать, используя файл кода программной части с разделяемым классом:</span><span class="sxs-lookup"><span data-stu-id="7e174-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="7e174-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="7e174-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="7e174-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
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

<span data-ttu-id="7e174-179">При необходимости добавьте в файл разделяемого класса нужные пространства имен.</span><span class="sxs-lookup"><span data-stu-id="7e174-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="7e174-180">К типичным пространствам имен, используемым компонентами Razor, относятся следующие.</span><span class="sxs-lookup"><span data-stu-id="7e174-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="7e174-181">Директивы [`@using`][2] в файле `_Imports.razor` применяются только к файлам Razor (`.razor`), но не к файлам C# (`.cs`).</span><span class="sxs-lookup"><span data-stu-id="7e174-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="7e174-182">Указание базового класса</span><span class="sxs-lookup"><span data-stu-id="7e174-182">Specify a base class</span></span>

<span data-ttu-id="7e174-183">Директиву [`@inherits`][6] можно использовать для указания базового класса для компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="7e174-184">В следующем примере показано, как компонент может наследовать базовый класс `BlazorRocksBase`, чтобы предоставить свойства и методы компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="7e174-185">Базовый класс должен быть производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="7e174-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="7e174-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="7e174-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="7e174-187">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="7e174-188">Использование компонентов</span><span class="sxs-lookup"><span data-stu-id="7e174-188">Use components</span></span>

<span data-ttu-id="7e174-189">Компоненты могут включать другие компоненты, объявляя их с помощью синтаксиса HTML-элементов.</span><span class="sxs-lookup"><span data-stu-id="7e174-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="7e174-190">Разметка для использования компонента выглядит как тег HTML с именем, соответствующем типу компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="7e174-191">Следующая разметка в `Pages/Index.razor` преобразовывает экземпляр `HeadingComponent` для просмотра.</span><span class="sxs-lookup"><span data-stu-id="7e174-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="7e174-192">`Shared/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-192">`Shared/HeadingComponent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-index/HeadingComponent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-index/HeadingComponent.razor)]

::: moniker-end

<span data-ttu-id="7e174-193">Если компонент содержит HTML-элемент с первой заглавной буквой, который не соответствует имени компонента, выдается предупреждение о том, что элемент имеет непредвиденное имя.</span><span class="sxs-lookup"><span data-stu-id="7e174-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="7e174-194">Добавление директивы [`@using`][2] для пространства имен компонента делает компонент доступным, что позволяет устранить это предупреждение.</span><span class="sxs-lookup"><span data-stu-id="7e174-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="7e174-195">Параметры</span><span class="sxs-lookup"><span data-stu-id="7e174-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="7e174-196">Параметры маршрута</span><span class="sxs-lookup"><span data-stu-id="7e174-196">Route parameters</span></span>

<span data-ttu-id="7e174-197">Компоненты могут принимать параметры маршрута из шаблона маршрута, указанного в директиве [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="7e174-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="7e174-198">Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7e174-199">Поддерживаются необязательные параметры.</span><span class="sxs-lookup"><span data-stu-id="7e174-199">Optional parameters are supported.</span></span> <span data-ttu-id="7e174-200">В следующем примере необязательный параметр `text` назначает значение сегмента маршрута свойству `Text` компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="7e174-201">Если сегмента нет, для `Text` устанавливается значение `fantastic`.</span><span class="sxs-lookup"><span data-stu-id="7e174-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="7e174-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-index/RouteParameter.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="7e174-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-index/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="7e174-204">Необязательные параметры не поддерживаются, поэтому в предыдущем примере применяются две директивы [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="7e174-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="7e174-205">Первая позволяет переходить к компоненту без параметра.</span><span class="sxs-lookup"><span data-stu-id="7e174-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="7e174-206">Вторая директива [`@page`][9] принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.</span><span class="sxs-lookup"><span data-stu-id="7e174-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="7e174-207">Сведения об универсальных параметрах маршрута (`{*pageRoute}`), которые захватывают пути в нескольких папках, см. в разделе <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="7e174-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="7e174-208">Параметры компонентов</span><span class="sxs-lookup"><span data-stu-id="7e174-208">Component parameters</span></span>

<span data-ttu-id="7e174-209">Компоненты могут иметь *параметры*, определяемые с помощью открытых простых или сложных свойств в классе компонента с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="7e174-209">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span></span> <span data-ttu-id="7e174-210">Используйте атрибуты, чтобы указать аргументы для компонента в разметке.</span><span class="sxs-lookup"><span data-stu-id="7e174-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="7e174-211">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-211">`Shared/ChildComponent.razor`:</span></span>

```razor
<div class="panel panel-default">
    <div class="panel-heading">@Title</div>
    <div class="panel-body">@ChildContent</div>

    <button class="btn btn-primary" @onclick="OnClickCallback">
        Trigger a Parent component method
    </button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public EventCallback<MouseEventArgs> OnClickCallback { get; set; }
}
```

<span data-ttu-id="7e174-212">Параметрам компонента может быть присвоено значение по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="7e174-212">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="7e174-213">В следующем примере из примера приложения `ParentComponent` задает значение свойства `Title` для `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="7e174-213">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="7e174-214">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-214">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>
```

<span data-ttu-id="7e174-215">Назначьте полям, свойствам и методам C# параметры компонентов в качестве значений атрибутов HTML с помощью [зарезервированного символа `@` Razor](xref:mvc/views/razor#razor-syntax):</span><span class="sxs-lookup"><span data-stu-id="7e174-215">Assign C# fields, properties, and methods to component parameters as HTML attribute values using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="7e174-216">Чтобы назначить поле, свойство или метод родительского компонента параметру дочернего компонента, добавьте перед именем поля, свойства или метода символ `@`.</span><span class="sxs-lookup"><span data-stu-id="7e174-216">To assign a parent component's field, property, or method to a child component's parameter, prefix the field, property, or method name with the `@` symbol.</span></span> <span data-ttu-id="7e174-217">Чтобы назначить параметру результат [неявного выражения C#](xref:mvc/views/razor#implicit-razor-expressions), добавьте перед именем неявного выражения символ `@`.</span><span class="sxs-lookup"><span data-stu-id="7e174-217">To assign the result of an [implicit C# expression](xref:mvc/views/razor#implicit-razor-expressions) to a parameter, prefix the implicit expression with an `@` symbol.</span></span>

  <span data-ttu-id="7e174-218">Указанный ниже родительский компонент отображает четыре экземпляра предыдущего компонента `ChildComponent` и задает для их параметра `Title` следующие значения:</span><span class="sxs-lookup"><span data-stu-id="7e174-218">The following parent component displays four instances of the preceding `ChildComponent` component and sets their `Title` parameter values to:</span></span>

  * <span data-ttu-id="7e174-219">значение поля `title`;</span><span class="sxs-lookup"><span data-stu-id="7e174-219">The value of the `title` field.</span></span>
  * <span data-ttu-id="7e174-220">результат метода C# `GetTitle`;</span><span class="sxs-lookup"><span data-stu-id="7e174-220">The result of the `GetTitle` C# method.</span></span>
  * <span data-ttu-id="7e174-221">текущая местная дата в полном формате с использованием <xref:System.DateTime.ToLongDateString%2A>;</span><span class="sxs-lookup"><span data-stu-id="7e174-221">The current local date in long format with <xref:System.DateTime.ToLongDateString%2A>.</span></span>
  * <span data-ttu-id="7e174-222">свойство `Name` объекта `person`.</span><span class="sxs-lookup"><span data-stu-id="7e174-222">The `person` object's `Name` property.</span></span>

  <span data-ttu-id="7e174-223">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-223">`Pages/ParentComponent.razor`:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from field.
  </ChildComponent>
  
  <ChildComponent Title="@GetTitle()">
      Title from method.
  </ChildComponent>
  
  <ChildComponent Title="@DateTime.Now.ToLongDateString()">
      Title from implicit Razor expression.
  </ChildComponent>
  
  <ChildComponent Title="@person.Name">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title = "Panel Title from Parent";
      private Person person = new Person();
      
      private string GetTitle()
      {
          return "Panel Title from Parent";
      }
      
      private class Person
      {
          public string Name { get; set; } = "Dr. Who";
      }
  }
  ```
  
  <span data-ttu-id="7e174-224">В отличие от Razor Pages (`.cshtml`), Blazor не может выполнять асинхронные операции в выражении Razor при рендеринге компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-224">Unlike in Razor pages (`.cshtml`), Blazor can't perform asynchronous work in a Razor expression while rendering a component.</span></span> <span data-ttu-id="7e174-225">Это обусловлено тем, что Blazor предназначается для рендеринга интерактивных пользовательских интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="7e174-225">This is because Blazor is designed for rendering interactive UIs.</span></span> <span data-ttu-id="7e174-226">В интерактивном пользовательском интерфейсе на экране всегда должно что-то отображаться, поэтому нет смысла блокировать поток рендеринга.</span><span class="sxs-lookup"><span data-stu-id="7e174-226">In an interactive UI, the screen must always display something, so it doesn't make sense to block the rendering flow.</span></span> <span data-ttu-id="7e174-227">Вместо этого асинхронные операции выполняются во время одного из [событий асинхронного жизненного цикла](xref:blazor/components/lifecycle).</span><span class="sxs-lookup"><span data-stu-id="7e174-227">Instead, asynchronous work is performed during one of the [asynchronous lifecycle events](xref:blazor/components/lifecycle).</span></span> <span data-ttu-id="7e174-228">После каждого события асинхронного жизненного цикла для компонента может выполняться повторный рендеринг.</span><span class="sxs-lookup"><span data-stu-id="7e174-228">After each asynchronous lifecycle event, the component may render again.</span></span> <span data-ttu-id="7e174-229">Следующий синтаксис Razor **не** поддерживается:</span><span class="sxs-lookup"><span data-stu-id="7e174-229">The following Razor syntax is **not** supported:</span></span>
  
  ```razor
  <ChildComponent Title="@await ...">
      ...
  </ChildComponent>
  ```
  
  <span data-ttu-id="7e174-230">Если приложение создано, при выполнении кода из предыдущего примера происходит *ошибка компилятора*:</span><span class="sxs-lookup"><span data-stu-id="7e174-230">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="7e174-231">Оператор await можно использовать только в методах с модификатором async.</span><span class="sxs-lookup"><span data-stu-id="7e174-231">The 'await' operator can only be used within an async method.</span></span> <span data-ttu-id="7e174-232">Попробуйте пометить этот метод модификатором async и изменить тип его возвращаемого значения на Task.</span><span class="sxs-lookup"><span data-stu-id="7e174-232">Consider marking this method with the 'async' modifier and changing its return type to 'Task'.</span></span>

  <span data-ttu-id="7e174-233">Чтобы асинхронно получить значение для параметра `Title` в предыдущем примере, компонент может использовать [событие жизненного цикла `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="7e174-233">To obtain a value for the `Title` parameter in the preceding example asychronously, the component can use the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods), as the following example demonstrates:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title;
      
      protected override async Task OnInitializedAsync()
      {
          title = await ...;
      }
  }
  ```
  
* <span data-ttu-id="7e174-234">Чтобы назначить результат [явного выражения C#](xref:mvc/views/razor#explicit-razor-expressions) в родительском компоненте параметру дочернего компонента, заключите выражение в круглые скобки, добавив перед ним символ `@`.</span><span class="sxs-lookup"><span data-stu-id="7e174-234">To assign the result of an [explicit C# expression](xref:mvc/views/razor#explicit-razor-expressions) in the parent component to a child component's parameter, surround the expression in parentheses with an `@` symbol prefix.</span></span>

  <span data-ttu-id="7e174-235">Следующий дочерний компонент имеет параметр `ShowItemsSinceDate` компонента <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="7e174-235">The following child component has a <xref:System.DateTime> component parameter, `ShowItemsSinceDate`.</span></span>
  
  <span data-ttu-id="7e174-236">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-236">`Shared/ChildComponent.razor`:</span></span>
  
  ```razor
  <div class="panel panel-default">
      <div class="panel-heading">Explicit DateTime Expression Example</div>
      <div class="panel-body">
          <p>@ChildContent</p>
          <p>One week ago date: @ShowItemsSinceDate</p>
      </div>
  </div>

  @code {
      [Parameter]
      public DateTime ShowItemsSinceDate { get; set; }

      [Parameter]
      public RenderFragment ChildContent { get; set; }
  }
  ```
  
  <span data-ttu-id="7e174-237">Следующий родительский компонент вычисляет дату с помощью явного выражения C#. Это дата, наступившая за неделю до значения, назначенного параметру `ShowItemsSinceDate` дочернего элемента.</span><span class="sxs-lookup"><span data-stu-id="7e174-237">The following parent component calculates a date with an explicit C# expression that's one week in the past for assignment to the child's `ShowItemsSinceDate` parameter.</span></span>
  
  <span data-ttu-id="7e174-238">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-238">`Pages/ParentComponent.razor`:</span></span>

  ```razor
  <ChildComponent ShowItemsSinceDate="@(DateTime.Now - TimeSpan.FromDays(7))">
      Title from explicit Razor expression.
  </ChildComponent>
  ```

  <span data-ttu-id="7e174-239">Использование явного выражения для сцепления текста с результатом выражения для назначения полученного значения параметру **не** поддерживается.</span><span class="sxs-lookup"><span data-stu-id="7e174-239">Use of an explicit expression to concatenate text with an expression result for assignment to a parameter is **not** supported.</span></span> <span data-ttu-id="7e174-240">В следующем примере выполняется поиск для сцепления текста "SKU-" с инвентарным номером продукта (свойство `SKU` Stock Keeping Unit), предоставленным объектом `product` родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-240">The following example seeks to concatenate the text "SKU-" with a product stock number (`SKU` property, "Stock Keeping Unit") provided by a parent component's `product` object.</span></span> <span data-ttu-id="7e174-241">Razor Pages поддерживает этот синтаксис (`.cshtml`), но он недопустим для назначения параметру дочернего элемента `Title`.</span><span class="sxs-lookup"><span data-stu-id="7e174-241">Although this syntax is supported in a Razor page (`.cshtml`), it isn't valid for assignment to the child's `Title` parameter.</span></span>
  
  ```razor
  <ChildComponent Title="SKU-@(product.SKU)">
      Title from composed Razor expression. This doesn't compile.
  </ChildComponent>
  ```
  
  <span data-ttu-id="7e174-242">Если приложение создано, при выполнении кода из предыдущего примера происходит *ошибка компилятора*:</span><span class="sxs-lookup"><span data-stu-id="7e174-242">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="7e174-243">Атрибуты компонента не поддерживают сложное содержимое (смешанный C# и разметка).</span><span class="sxs-lookup"><span data-stu-id="7e174-243">Component attributes do not support complex content (mixed C# and markup).</span></span>
  
  <span data-ttu-id="7e174-244">Чтобы можно было назначить составное значение, используйте метод, поле или свойство.</span><span class="sxs-lookup"><span data-stu-id="7e174-244">To support the assignment of a composed value, use a method, field, or property.</span></span> <span data-ttu-id="7e174-245">В следующем примере "SKU-" сцепляется с инвентарным номером продукта в методе C# `GetTitle`:</span><span class="sxs-lookup"><span data-stu-id="7e174-245">The following example performs the concatination of "SKU-" and a product's stock number in the C# method `GetTitle`:</span></span>
  
  ```razor
  <ChildComponent Title="@GetTitle()">
      Composed title from method.
  </ChildComponent>
  
  @code {
      private Product product = new Product();

      private string GetTitle() => $"SKU-{product.SKU}";
      
      private class Product
      {
          public string SKU { get; set; } = "12345";
      }
  }
  ```
  
<span data-ttu-id="7e174-246">Дополнительные сведения см. в [справочнике по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="7e174-246">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="7e174-247">Не создавайте компоненты, записывающие их в собственные *параметры компонентов*, — используйте вместо этого закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="7e174-247">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="7e174-248">Дополнительные сведения см. в разделе [Перезаписанные параметры](#overwritten-parameters).</span><span class="sxs-lookup"><span data-stu-id="7e174-248">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

#### <a name="component-parameters-should-be-auto-properties"></a><span data-ttu-id="7e174-249">Параметры компонентов должны быть автосвойствами</span><span class="sxs-lookup"><span data-stu-id="7e174-249">Component parameters should be auto-properties</span></span>

<span data-ttu-id="7e174-250">Параметры компонента должны объявляться как *автосвойства*. Это означает, что такие параметры не должны содержать настраиваемую логику в методах получения или методах задания.</span><span class="sxs-lookup"><span data-stu-id="7e174-250">Component parameters should be declared as *auto-properties*, meaning that they shouldn't contain custom logic in their getters or setters.</span></span> <span data-ttu-id="7e174-251">Например, следующее свойство `StartData` является автосвойством:</span><span class="sxs-lookup"><span data-stu-id="7e174-251">For example, the following `StartData` property is an auto-property:</span></span>

```csharp
[Parameter]
public DateTime StartData { get; set; }
```

<span data-ttu-id="7e174-252">Не размещайте настраиваемую логику в методе доступа `get` или `set`, так как параметры компонента можно использовать исключительно как канал для перемещения информации из родительского компонента в дочерний.</span><span class="sxs-lookup"><span data-stu-id="7e174-252">Don't place custom logic in the `get` or `set` accessor because component parameters are purely intended for use as a channel for a parent component to flow information to a child component.</span></span> <span data-ttu-id="7e174-253">Если метод установки свойства дочернего компонента содержит логику, которая вызывает повторный рендеринг родительского компонента, в итоге создается бесконечный цикл рендеринга.</span><span class="sxs-lookup"><span data-stu-id="7e174-253">If a setter of a child component property contains logic that causes rerendering of the parent component, an infinite rendering loop results.</span></span>

<span data-ttu-id="7e174-254">Если необходимо преобразовать полученное значение параметра, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="7e174-254">If you need to transform a received parameter value:</span></span>

* <span data-ttu-id="7e174-255">Для представления предоставляемых необработанных данных оставьте для свойства параметра чистое автосвойство.</span><span class="sxs-lookup"><span data-stu-id="7e174-255">Leave the parameter property as a pure auto-property to represent the supplied raw data.</span></span>
* <span data-ttu-id="7e174-256">Создайте какое-то другое свойство или метод, который предоставляет преобразованные данные на основе свойства параметра.</span><span class="sxs-lookup"><span data-stu-id="7e174-256">Create some other property or method that supplies the transformed data based on the parameter property.</span></span>

<span data-ttu-id="7e174-257">Вы можете переопределить `OnParametersSetAsync`, если нужно преобразовывать полученный параметр при каждом получении новых данных.</span><span class="sxs-lookup"><span data-stu-id="7e174-257">You can override `OnParametersSetAsync` if you want to transform a received parameter each time new data is received.</span></span>

## <a name="child-content"></a><span data-ttu-id="7e174-258">Дочернее содержимое</span><span class="sxs-lookup"><span data-stu-id="7e174-258">Child content</span></span>

<span data-ttu-id="7e174-259">Компоненты могут задавать содержимое другого компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-259">Components can set the content of another component.</span></span> <span data-ttu-id="7e174-260">Назначение компонента задает содержимое между тегами, указывающими принимающий компонент.</span><span class="sxs-lookup"><span data-stu-id="7e174-260">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="7e174-261">В следующем примере `ChildComponent` имеет свойство `ChildContent`, представляющее <xref:Microsoft.AspNetCore.Components.RenderFragment>, который представляет сегмент пользовательского интерфейса для отрисовки.</span><span class="sxs-lookup"><span data-stu-id="7e174-261">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="7e174-262">Значение `ChildContent` находится в том месте разметки компонента, где должно быть визуализировано содержимое.</span><span class="sxs-lookup"><span data-stu-id="7e174-262">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="7e174-263">Значение `ChildContent` принимается от родительского компонента и визуализируется внутри `panel-body` панели начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="7e174-263">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="7e174-264">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-264">`Shared/ChildComponent.razor`:</span></span>

```razor
<div class="panel panel-default">
    <div class="panel-heading">@Title</div>
    <div class="panel-body">@ChildContent</div>

    <button class="btn btn-primary" @onclick="OnClickCallback">
        Trigger a Parent component method
    </button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public EventCallback<MouseEventArgs> OnClickCallback { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="7e174-265">Свойству, принимающему содержимое <xref:Microsoft.AspNetCore.Components.RenderFragment>, по соглашению необходимо присвоить имя `ChildContent`.</span><span class="sxs-lookup"><span data-stu-id="7e174-265">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="7e174-266">`ParentComponent` в примере приложения может предоставить содержимое для отрисовки `ChildComponent`, заключив содержимое в теги `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="7e174-266">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="7e174-267">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-267">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>
```

<span data-ttu-id="7e174-268">В связи с тем, как Blazor выполняет рендеринг дочернего содержимого, для компонентов рендеринга в цикле `for` требуется задать локальную переменную индекса, если в содержимом дочернего компонента используется переменная цикла приращения:</span><span class="sxs-lookup"><span data-stu-id="7e174-268">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="7e174-269">Вместо этого можно использовать цикл `foreach` с <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="7e174-269">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

<span data-ttu-id="7e174-270">Сведения о том, как использовать <xref:Microsoft.AspNetCore.Components.RenderFragment> в качестве шаблона для пользовательского интерфейса компонента Razor, см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="7e174-270">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for Razor component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="7e174-271">Сплаттинг атрибутов и произвольные параметры</span><span class="sxs-lookup"><span data-stu-id="7e174-271">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="7e174-272">Компоненты могут записывать и визуализировать дополнительные атрибуты в дополнение к объявленным параметрам компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-272">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="7e174-273">Можно записать дополнительные атрибуты в словарь, а затем выполнить *сплаттинг* для элемента при подготовке отрисовки компонента с помощью директивы [`@attributes`][3] Razor.</span><span class="sxs-lookup"><span data-stu-id="7e174-273">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="7e174-274">Этот сценарий полезен при определении компонента, который создает элемент разметки, поддерживающий разнообразные настройки.</span><span class="sxs-lookup"><span data-stu-id="7e174-274">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="7e174-275">Например, может оказаться утомительным по отдельности определять атрибуты для `<input>`, поддерживающего много параметров.</span><span class="sxs-lookup"><span data-stu-id="7e174-275">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="7e174-276">В следующем примере первый элемент `<input>` (`id="useIndividualParams"`) использует отдельные параметры компонента, а второй элемент `<input>` (`id="useAttributesDict"`) использует сплаттинг атрибутов:</span><span class="sxs-lookup"><span data-stu-id="7e174-276">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

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

<span data-ttu-id="7e174-277">Тип параметра должен реализовывать `IEnumerable<KeyValuePair<string, object>>` или `IReadOnlyDictionary<string, object>` с ключами строки.</span><span class="sxs-lookup"><span data-stu-id="7e174-277">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="7e174-278">При использовании обоих подходов получаются идентичные отрисованные элементы `<input>`:</span><span class="sxs-lookup"><span data-stu-id="7e174-278">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="7e174-279">Чтобы принять произвольные атрибуты, определите параметр компонента с помощью атрибута [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) со свойством <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>, имеющим значение `true`:</span><span class="sxs-lookup"><span data-stu-id="7e174-279">To accept arbitrary attributes, define a component parameter using the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute) with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="7e174-280">Свойство <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> в [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) позволяет параметру соответствовать всем атрибутам, которые не соответствуют никакому другому параметру.</span><span class="sxs-lookup"><span data-stu-id="7e174-280">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="7e174-281">Компонент может определять только один параметр с <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span><span class="sxs-lookup"><span data-stu-id="7e174-281">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="7e174-282">Тип свойства, используемый с <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>, должен быть назначаемым из `Dictionary<string, object>` с ключами строки.</span><span class="sxs-lookup"><span data-stu-id="7e174-282">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="7e174-283">В этом сценарии также можно использовать `IEnumerable<KeyValuePair<string, object>>` или `IReadOnlyDictionary<string, object>`.</span><span class="sxs-lookup"><span data-stu-id="7e174-283">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="7e174-284">Расположение [`@attributes`][3] относительно положения атрибутов элемента имеет значение.</span><span class="sxs-lookup"><span data-stu-id="7e174-284">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="7e174-285">Когда выполняется сплаттинг [`@attributes`][3] для элемента, атрибуты обрабатываются справа налево (от последнего к первому).</span><span class="sxs-lookup"><span data-stu-id="7e174-285">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="7e174-286">Рассмотрим следующий пример компонента, использующего компонент `Child`:</span><span class="sxs-lookup"><span data-stu-id="7e174-286">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="7e174-287">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-287">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="7e174-288">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-288">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="7e174-289">Атрибут `extra` компонента `Child` стоит справа от [`@attributes`][3].</span><span class="sxs-lookup"><span data-stu-id="7e174-289">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="7e174-290">`<div>`, визуализируемый компонентом `Parent`, содержит `extra="5"` при передаче через дополнительный атрибут, так как атрибуты обрабатываются справа налево (от последнего к первому):</span><span class="sxs-lookup"><span data-stu-id="7e174-290">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="7e174-291">В следующем примере порядок `extra` и [`@attributes`][3] в `<div>` компонента `Child` изменен на противоположный:</span><span class="sxs-lookup"><span data-stu-id="7e174-291">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="7e174-292">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-292">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="7e174-293">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7e174-293">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="7e174-294">Визуализируемый `<div>` в компоненте `Parent` содержит `extra="10"` при передаче через дополнительный атрибут:</span><span class="sxs-lookup"><span data-stu-id="7e174-294">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="7e174-295">Запись ссылок на компоненты</span><span class="sxs-lookup"><span data-stu-id="7e174-295">Capture references to components</span></span>

<span data-ttu-id="7e174-296">Ссылки на компоненты позволяют ссылаться на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра, например `Show` или `Reset`.</span><span class="sxs-lookup"><span data-stu-id="7e174-296">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="7e174-297">Чтобы записать ссылку на компонент, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="7e174-297">To capture a component reference:</span></span>

* <span data-ttu-id="7e174-298">Добавьте к дочернему компоненту атрибут [`@ref`][4].</span><span class="sxs-lookup"><span data-stu-id="7e174-298">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="7e174-299">Определите поле с тем же типом, что и у дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-299">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="7e174-300">При отрисовке компонента поле `loginDialog` заполняется экземпляром дочернего компонента `CustomLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="7e174-300">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="7e174-301">Затем можно вызывать методы .NET в экземпляре компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-301">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7e174-302">Переменная `loginDialog` заполняется только после отрисовки компонента, а ее выходные данные включают элемент `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="7e174-302">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="7e174-303">Пока компонент не будет преобразован для просмотра, ссылка на него не используется.</span><span class="sxs-lookup"><span data-stu-id="7e174-303">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="7e174-304">Для управления ссылками на компоненты после завершения отрисовки компонента используйте [методы `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="7e174-304">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="7e174-305">Чтобы использовать ссылочную переменную с обработчиком событий, используйте лямбда-выражение или назначьте делегат обработчика событий в [методах `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="7e174-305">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="7e174-306">Это гарантирует, что ссылочная переменная будет назначена до назначения обработчика событий.</span><span class="sxs-lookup"><span data-stu-id="7e174-306">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="7e174-307">Сведения о ссылках на компоненты в цикле см. в разделе [Получение ссылок на несколько схожих дочерних компонентов (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="7e174-307">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="7e174-308">При записи ссылок на компоненты используется аналогичный синтаксис для [записи ссылок на элементы](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), это не функция взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7e174-308">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="7e174-309">Ссылки на компоненты не передаются в код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7e174-309">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="7e174-310">Они используются только в коде .NET.</span><span class="sxs-lookup"><span data-stu-id="7e174-310">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="7e174-311">**Не** используйте ссылки на компоненты для изменения состояния дочерних компонентов.</span><span class="sxs-lookup"><span data-stu-id="7e174-311">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="7e174-312">Вместо этого используйте обычные декларативные параметры для передачи данных дочерним компонентам.</span><span class="sxs-lookup"><span data-stu-id="7e174-312">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="7e174-313">Использование обычных декларативных параметров приводит к тому, что дочерние компоненты автоматически визуализируются в нужное время.</span><span class="sxs-lookup"><span data-stu-id="7e174-313">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="7e174-314">Контекст синхронизации</span><span class="sxs-lookup"><span data-stu-id="7e174-314">Synchronization context</span></span>

<span data-ttu-id="7e174-315">Blazor использует контекст синхронизации (<xref:System.Threading.SynchronizationContext>) для принудительного использования одного логического потока выполнения.</span><span class="sxs-lookup"><span data-stu-id="7e174-315">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="7e174-316">[Методы жизненного цикла ](xref:blazor/components/lifecycle) компонента и все обратные вызовы событий, сделанные Blazor, выполняются в этом контексте синхронизации.</span><span class="sxs-lookup"><span data-stu-id="7e174-316">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="7e174-317">Контекст синхронизации Blazor Server пытается эмулировать однопоточную среду таким образом, чтобы она точно соответствовала модели WebAssembly в браузере, которая является однопоточной.</span><span class="sxs-lookup"><span data-stu-id="7e174-317">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="7e174-318">В любой момент времени работа выполняется только в одном потоке, что создает впечатление единого логического потока.</span><span class="sxs-lookup"><span data-stu-id="7e174-318">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="7e174-319">Две операции не могут выполняться одновременно.</span><span class="sxs-lookup"><span data-stu-id="7e174-319">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="7e174-320">Избегайте блокирующих вызовов</span><span class="sxs-lookup"><span data-stu-id="7e174-320">Avoid thread-blocking calls</span></span>

<span data-ttu-id="7e174-321">Как правило, не следует вызывать следующие методы.</span><span class="sxs-lookup"><span data-stu-id="7e174-321">Generally, don't call the following methods.</span></span> <span data-ttu-id="7e174-322">Следующие методы блокируют поток и, таким образом, блокируют возобновление работы приложения до тех пор, пока не завершится базовый <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="7e174-322">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="7e174-323">Внешний вызов методов компонента для изменения состояния</span><span class="sxs-lookup"><span data-stu-id="7e174-323">Invoke component methods externally to update state</span></span>

<span data-ttu-id="7e174-324">Если компонент нужно изменить на основе внешнего события, такого как таймер или другие уведомления, используйте метод `InvokeAsync`, который выполняет отправку в контекст синхронизации Blazor.</span><span class="sxs-lookup"><span data-stu-id="7e174-324">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="7e174-325">Например, рассмотрим *службу уведомителя*, которая может уведомлять любой компонент, ожидающий передачи данных, об измененном состоянии:</span><span class="sxs-lookup"><span data-stu-id="7e174-325">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="7e174-326">Зарегистрируйте `NotifierService`.</span><span class="sxs-lookup"><span data-stu-id="7e174-326">Register the `NotifierService`:</span></span>

* <span data-ttu-id="7e174-327">В Blazor WebAssembly зарегистрируйте службу как отдельную (singleton) в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="7e174-327">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="7e174-328">В Blazor Server зарегистрируйте службу с заданной областью (scoped) в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7e174-328">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="7e174-329">Используйте `NotifierService` для изменения компонента:</span><span class="sxs-lookup"><span data-stu-id="7e174-329">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="7e174-330">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="7e174-330">In the preceding example:</span></span>

* <span data-ttu-id="7e174-331">`NotifierService` вызывает метод `OnNotify` компонента вне контекста синхронизации Blazor.</span><span class="sxs-lookup"><span data-stu-id="7e174-331">`NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="7e174-332">`InvokeAsync` используется для переключения на подходящий контекст и постановки отрисовки в очередь.</span><span class="sxs-lookup"><span data-stu-id="7e174-332">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="7e174-333">Для получения дополнительной информации см. <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="7e174-333">For more information, see <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="7e174-334">Компонент реализует <xref:System.IDisposable>, а делегат `OnNotify` не подписан в методе `Dispose`, который вызывается платформой при удалении компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-334">The component implements <xref:System.IDisposable>, and the `OnNotify` delegate is unsubscribed in the `Dispose` method, which is called by the framework when the component is disposed.</span></span> <span data-ttu-id="7e174-335">Для получения дополнительной информации см. <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="7e174-335">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="7e174-336">Использование \@key для управления сохранением элементов и компонентов</span><span class="sxs-lookup"><span data-stu-id="7e174-336">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="7e174-337">При отрисовке списка элементов или компонентов и последующем изменении элементов или компонентов алгоритм сравнения Blazor должен решить, какие из предыдущих элементов или компонентов можно оставить и как следует сопоставить с ними объекты модели.</span><span class="sxs-lookup"><span data-stu-id="7e174-337">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="7e174-338">Обычно этот процесс выполняется автоматически, и его можно игнорировать, но в некоторых случаях может потребоваться управлять данным процессом.</span><span class="sxs-lookup"><span data-stu-id="7e174-338">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="7e174-339">Рассмотрим следующий пример.</span><span class="sxs-lookup"><span data-stu-id="7e174-339">Consider the following example:</span></span>

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

<span data-ttu-id="7e174-340">Содержимое коллекции `People` может изменяться при вставке, удалении или повторном упорядочении записей.</span><span class="sxs-lookup"><span data-stu-id="7e174-340">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="7e174-341">Когда компонент отрисовывается повторно, компонент `<DetailsEditor>` может измениться на получение других значений параметра `Details`.</span><span class="sxs-lookup"><span data-stu-id="7e174-341">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="7e174-342">Это может усложнить повторную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="7e174-342">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="7e174-343">В некоторых случаях повторная отрисовка может привести к появлению заметных различий в поведении, таких как потеря фокуса элемента.</span><span class="sxs-lookup"><span data-stu-id="7e174-343">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="7e174-344">Процесс сопоставления можно контролировать с помощью атрибута директивы [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="7e174-344">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="7e174-345">[`@key`][5] гарантирует, что алгоритм сравнения сохранит элементы или компоненты на основе значения ключа:</span><span class="sxs-lookup"><span data-stu-id="7e174-345">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="7e174-346">При изменении коллекции `People` алгоритм сравнения сохраняет связь между экземплярами `<DetailsEditor>` и экземплярами `person`:</span><span class="sxs-lookup"><span data-stu-id="7e174-346">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="7e174-347">Если `Person` удаляется из списка `People`, то из пользовательского интерфейса удаляется только соответствующий экземпляр `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="7e174-347">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="7e174-348">Другие экземпляры остаются без изменений.</span><span class="sxs-lookup"><span data-stu-id="7e174-348">Other instances are left unchanged.</span></span>
* <span data-ttu-id="7e174-349">Если в какой-либо позиции в списке вставляется `Person`, то в соответствующей позиции вставляется один новый экземпляр `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="7e174-349">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="7e174-350">Другие экземпляры остаются без изменений.</span><span class="sxs-lookup"><span data-stu-id="7e174-350">Other instances are left unchanged.</span></span>
* <span data-ttu-id="7e174-351">При переупорядочении записей `Person` соответствующие экземпляры `<DetailsEditor>` сохраняются и переупорядочиваются в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="7e174-351">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="7e174-352">В некоторых сценариях использование [`@key`][5] уменьшает сложность повторной отрисовки и предотвращает потенциальные проблемы с изменяющимися элементами модели DOM с отслеживанием состояния, например положением фокуса.</span><span class="sxs-lookup"><span data-stu-id="7e174-352">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7e174-353">Ключи являются локальными для каждого компонента или элемента контейнера.</span><span class="sxs-lookup"><span data-stu-id="7e174-353">Keys are local to each container element or component.</span></span> <span data-ttu-id="7e174-354">Ключи не сравниваются глобально по всему документу.</span><span class="sxs-lookup"><span data-stu-id="7e174-354">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="7e174-355">Условия для использования \@key</span><span class="sxs-lookup"><span data-stu-id="7e174-355">When to use \@key</span></span>

<span data-ttu-id="7e174-356">Как правило, [`@key`][5] имеет смысл использовать при отрисовке списка (например, в блоке [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)) и при наличии подходящего значения для определения [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="7e174-356">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="7e174-357">Можно также использовать [`@key`][5], чтобы запретить Blazor сохранять поддерево элементов или компонентов при изменении объекта:</span><span class="sxs-lookup"><span data-stu-id="7e174-357">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="7e174-358">Если `@currentPerson` меняется, директива атрибута [`@key`][5] принуждает Blazor отменить весь блок элемента `<div>` с потомками и перестроить поддерево в пользовательском интерфейсе с использованием новых элементов и компонентов.</span><span class="sxs-lookup"><span data-stu-id="7e174-358">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="7e174-359">Это может быть полезно, если нужно гарантировать, что при изменении `@currentPerson` состояние пользовательского интерфейса не сохраняется.</span><span class="sxs-lookup"><span data-stu-id="7e174-359">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="7e174-360">Условия для отказа от использования \@key</span><span class="sxs-lookup"><span data-stu-id="7e174-360">When not to use \@key</span></span>

<span data-ttu-id="7e174-361">Сравнение с использованием [`@key`][5] подразумевает определенное снижение производительности.</span><span class="sxs-lookup"><span data-stu-id="7e174-361">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="7e174-362">Это снижение производительности незначительно, но указывать [`@key`][5] следует только в том случае, если управление правилами сохранения элементов или компонентов выгодно для приложения.</span><span class="sxs-lookup"><span data-stu-id="7e174-362">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="7e174-363">Даже если [`@key`][5] не используется, Blazor сохраняет экземпляры дочерних элементов и компонентов в максимально возможной степени.</span><span class="sxs-lookup"><span data-stu-id="7e174-363">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="7e174-364">Единственным преимуществом использования [`@key`][5] является контроль над тем, *как* экземпляры модели сопоставляются с сохраненными экземплярами компонентов, вместо выбора сопоставления с помощью алгоритма сравнения.</span><span class="sxs-lookup"><span data-stu-id="7e174-364">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="7e174-365">Значения, которые следует использовать для \@key</span><span class="sxs-lookup"><span data-stu-id="7e174-365">What values to use for \@key</span></span>

<span data-ttu-id="7e174-366">Как правило, для [`@key`][5] имеет смысл указать значение одного из следующих видов:</span><span class="sxs-lookup"><span data-stu-id="7e174-366">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="7e174-367">Экземпляры объектов модели (например, экземпляр `Person`, как в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="7e174-367">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="7e174-368">Это гарантирует сохранение на основе равенства ссылок на объекты.</span><span class="sxs-lookup"><span data-stu-id="7e174-368">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="7e174-369">Уникальные идентификаторы (например, значения первичного ключа типа `int`, `string` или `Guid`).</span><span class="sxs-lookup"><span data-stu-id="7e174-369">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="7e174-370">Убедитесь, что значения, используемые для [`@key`][5], не конфликтуют.</span><span class="sxs-lookup"><span data-stu-id="7e174-370">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="7e174-371">Если в одном родительском элементе обнаруживаются конфликтующие значения, Blazor выдает исключение, поскольку не может детерминированно сопоставлять старые элементы или компоненты с новыми.</span><span class="sxs-lookup"><span data-stu-id="7e174-371">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="7e174-372">Используйте только уникальные значения, такие как экземпляры объекта или значения первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="7e174-372">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="7e174-373">Перезаписанные параметры</span><span class="sxs-lookup"><span data-stu-id="7e174-373">Overwritten parameters</span></span>

<span data-ttu-id="7e174-374">Платформа Blazor обычно позволяет безопасно назначить параметр с родительского компонента на дочерний:</span><span class="sxs-lookup"><span data-stu-id="7e174-374">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="7e174-375">Параметры не перезаписываются неожиданно.</span><span class="sxs-lookup"><span data-stu-id="7e174-375">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="7e174-376">Побочные эффекты сводятся к минимуму.</span><span class="sxs-lookup"><span data-stu-id="7e174-376">Side-effects are minimized.</span></span> <span data-ttu-id="7e174-377">Например, дополнительные отрисовки избегаются, так как они могут создавать бесконечные циклы отрисовки.</span><span class="sxs-lookup"><span data-stu-id="7e174-377">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="7e174-378">Дочерний компонент получает новые значения параметров, которые могут перезаписать существующие значения при повторной отрисовке родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-378">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="7e174-379">Случайно перезаписанные значения параметров в дочернем компоненте часто возникают при разработке компонента с одним или несколькими параметрами, привязанными к данным, а также если разработчик выполняет запись данных непосредственно в параметр в дочернем элементе:</span><span class="sxs-lookup"><span data-stu-id="7e174-379">Accidentally overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="7e174-380">Дочерний компонент отрисовывается с одним или несколькими значениями параметров из родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-380">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="7e174-381">Дочерний компонент выполняет запись непосредственно в значение параметра.</span><span class="sxs-lookup"><span data-stu-id="7e174-381">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="7e174-382">Родительский компонент отрисовывается повторно и перезаписывает значение параметра дочернего элемента.</span><span class="sxs-lookup"><span data-stu-id="7e174-382">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="7e174-383">Возможность перезаписи значений параметров также распространяется и на методы задания свойств дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-383">The potential for overwriting parameter values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="7e174-384">**В наших общих рекомендациях не описана процедура создания компонентов, которые непосредственно записываются в собственные параметры.**</span><span class="sxs-lookup"><span data-stu-id="7e174-384">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="7e174-385">Рассмотрим следующий компонент `Expander` с ошибкой, который</span><span class="sxs-lookup"><span data-stu-id="7e174-385">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="7e174-386">преобразует дочернее содержимое;</span><span class="sxs-lookup"><span data-stu-id="7e174-386">Renders child content.</span></span>
* <span data-ttu-id="7e174-387">переключает отображение дочернего содержимого с помощью параметра компонента (`Expanded`).</span><span class="sxs-lookup"><span data-stu-id="7e174-387">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="7e174-388">Компонент выполняет запись непосредственно в параметр `Expanded`, который демонстрирует проблему с перезаписанными параметрами. Его следует избегать.</span><span class="sxs-lookup"><span data-stu-id="7e174-388">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
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

<span data-ttu-id="7e174-389">Компонент `Expander` добавляется в родительский компонент, который может вызывать <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span><span class="sxs-lookup"><span data-stu-id="7e174-389">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="7e174-390">Изначально компоненты `Expander` работают независимо, когда их свойства `Expanded` переключаются.</span><span class="sxs-lookup"><span data-stu-id="7e174-390">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="7e174-391">Дочерние компоненты сохраняют свои состояния, как и ожидалось.</span><span class="sxs-lookup"><span data-stu-id="7e174-391">The child components maintain their states as expected.</span></span> <span data-ttu-id="7e174-392">Когда в родительском элементе вызывается <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, параметр `Expanded` первого дочернего компонента сбрасывается обратно к первоначальному значению (`true`).</span><span class="sxs-lookup"><span data-stu-id="7e174-392">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="7e174-393">Значение `Expanded` второго компонента `Expander` не сбрасывается, так как во втором компоненте не отображается дочернее содержимое.</span><span class="sxs-lookup"><span data-stu-id="7e174-393">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="7e174-394">Чтобы сохранить состояние в предыдущем сценарии, используйте *закрытое поле* в компоненте `Expander`, чтобы сохранить состояние переключения.</span><span class="sxs-lookup"><span data-stu-id="7e174-394">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="7e174-395">Следующий измененный компонент `Expander`:</span><span class="sxs-lookup"><span data-stu-id="7e174-395">The following revised `Expander` component:</span></span>

* <span data-ttu-id="7e174-396">Принимает значение параметра компонента `Expanded` из родительского элемента.</span><span class="sxs-lookup"><span data-stu-id="7e174-396">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="7e174-397">Присваивает значение параметра компонента *закрытому полю* (`expanded`) при [событии OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="7e174-397">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="7e174-398">Использует закрытое поле для поддержания внутреннего состояния переключения, в котором описывается, как избегать непосредственной записи в параметре.</span><span class="sxs-lookup"><span data-stu-id="7e174-398">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

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

<span data-ttu-id="7e174-399">Дополнительные сведения см. в статье [Ошибка двусторонней привязки Blazor (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="7e174-399">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="7e174-400">Применение атрибута</span><span class="sxs-lookup"><span data-stu-id="7e174-400">Apply an attribute</span></span>

<span data-ttu-id="7e174-401">Атрибуты можно применять к компонентам Razor с помощью директивы [`@attribute`][7].</span><span class="sxs-lookup"><span data-stu-id="7e174-401">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="7e174-402">В следующем примере атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) применяется к классу компонентов:</span><span class="sxs-lookup"><span data-stu-id="7e174-402">The following example applies the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="7e174-403">Условные атрибуты элемента HTML</span><span class="sxs-lookup"><span data-stu-id="7e174-403">Conditional HTML element attributes</span></span>

<span data-ttu-id="7e174-404">Атрибуты элемента HTML условно визуализируются в зависимости от значения .NET.</span><span class="sxs-lookup"><span data-stu-id="7e174-404">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="7e174-405">Если значение равно `false` или `null`, то атрибут не визуализируется.</span><span class="sxs-lookup"><span data-stu-id="7e174-405">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="7e174-406">Если значение равно `true`, атрибут визуализируется в свернутом виде.</span><span class="sxs-lookup"><span data-stu-id="7e174-406">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="7e174-407">В следующем примере `IsCompleted` определяет, визуализируется ли `checked` в разметке элемента:</span><span class="sxs-lookup"><span data-stu-id="7e174-407">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="7e174-408">Если `IsCompleted` имеет значение `true`, флажок визуализируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="7e174-408">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="7e174-409">Если `IsCompleted` имеет значение `false`, флажок визуализируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="7e174-409">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="7e174-410">Дополнительные сведения см. в [справочнике по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="7e174-410">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="7e174-411">Некоторые атрибуты HTML, такие как [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), работают неправильно, если типом .NET является `bool`.</span><span class="sxs-lookup"><span data-stu-id="7e174-411">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="7e174-412">В этих случаях используйте тип `string` вместо `bool`.</span><span class="sxs-lookup"><span data-stu-id="7e174-412">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="7e174-413">Необработанный HTML-код</span><span class="sxs-lookup"><span data-stu-id="7e174-413">Raw HTML</span></span>

<span data-ttu-id="7e174-414">Строки обычно визуализируются с помощью текстовых узлов модели DOM, что означает, что любая разметка, которую они могут содержать, игнорируется и обрабатывается как текстовый литерал.</span><span class="sxs-lookup"><span data-stu-id="7e174-414">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="7e174-415">Для отрисовки необработанного HTML-кода заключите HTML-содержимое в значение `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="7e174-415">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="7e174-416">Это значение анализируется как HTML или SVG и вставляется в модель DOM.</span><span class="sxs-lookup"><span data-stu-id="7e174-416">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="7e174-417">Отрисовка необработанного HTML-кода, созданного из любого ненадежного источника, является **угрозой безопасности**, и ее следует избегать!</span><span class="sxs-lookup"><span data-stu-id="7e174-417">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="7e174-418">В следующем примере показано использование типа `MarkupString` для добавления блока статического HTML-содержимого в визуализируемые выходные данные компонента:</span><span class="sxs-lookup"><span data-stu-id="7e174-418">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="7e174-419">Шаблоны Razor</span><span class="sxs-lookup"><span data-stu-id="7e174-419">Razor templates</span></span>

<span data-ttu-id="7e174-420">Фрагменты отрисовки можно определить с помощью синтаксиса шаблонов Razor.</span><span class="sxs-lookup"><span data-stu-id="7e174-420">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="7e174-421">Шаблоны Razor позволяют определить фрагмент кода пользовательского интерфейса и подразумевают следующий формат.</span><span class="sxs-lookup"><span data-stu-id="7e174-421">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="7e174-422">В следующем примере показано, как указать значения <xref:Microsoft.AspNetCore.Components.RenderFragment> и <xref:Microsoft.AspNetCore.Components.RenderFragment%601> и визуализировать шаблоны непосредственно в компоненте.</span><span class="sxs-lookup"><span data-stu-id="7e174-422">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="7e174-423">Фрагменты отрисовки также могут передаваться в качестве аргументов в [шаблонные компоненты](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="7e174-423">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="7e174-424">Визуализированные выходные данные предыдущего кода:</span><span class="sxs-lookup"><span data-stu-id="7e174-424">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="7e174-425">Статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="7e174-425">Static assets</span></span>

<span data-ttu-id="7e174-426">Blazor соответствует соглашению для приложений ASP.NET Core о размещении статических ресурсов в [папке `web root (wwwroot)`](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="7e174-426">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="7e174-427">Используйте базовый относительный путь (`/`) для ссылки на корневой веб-каталог статического ресурса.</span><span class="sxs-lookup"><span data-stu-id="7e174-427">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="7e174-428">В следующем примере `logo.png` физически находится в папке `{PROJECT ROOT}/wwwroot/images`.</span><span class="sxs-lookup"><span data-stu-id="7e174-428">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="7e174-429">Компоненты Razor **не** поддерживают нотацию тильды с косой чертой (`~/`).</span><span class="sxs-lookup"><span data-stu-id="7e174-429">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="7e174-430">Сведения о настройке базового пути приложения см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="7e174-430">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="7e174-431">Вспомогательные функции тегов в компонентах не поддерживаются</span><span class="sxs-lookup"><span data-stu-id="7e174-431">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="7e174-432">[`Tag Helpers`Razor не поддерживаются в компонентах ](xref:mvc/views/tag-helpers/intro) (файлы с расширением `.razor`).</span><span class="sxs-lookup"><span data-stu-id="7e174-432">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="7e174-433">Чтобы обеспечить функциональные возможности, аналогичные вспомогательным функциям тегов, в Blazor, создайте компонент с теми же функциональными возможностями, что и вспомогательная функция тега, и используйте его вместо нее.</span><span class="sxs-lookup"><span data-stu-id="7e174-433">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="7e174-434">Изображения SVG</span><span class="sxs-lookup"><span data-stu-id="7e174-434">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="7e174-435">Так как Blazor выполняет рендеринг HTML-кода, поддерживаемые браузером изображения, включая изображения SVG (`.svg`), поддерживаются при использовании тега `<img>`.</span><span class="sxs-lookup"><span data-stu-id="7e174-435">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="7e174-436">Аналогичным образом изображения SVG поддерживаются в правилах CSS файла таблицы стилей (`.css`).</span><span class="sxs-lookup"><span data-stu-id="7e174-436">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="7e174-437">Однако встроенная разметка SVG не поддерживается во всех сценариях.</span><span class="sxs-lookup"><span data-stu-id="7e174-437">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="7e174-438">Если поместить тег `<svg>` непосредственно в файл компонента (`.razor`), базовая отрисовка изображений будет доступной, но многие расширенные сценарии пока не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="7e174-438">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="7e174-439">Например, теги `<use>` сейчас не учитываются, а с некоторыми тегами SVG невозможно использовать [`@bind`][10].</span><span class="sxs-lookup"><span data-stu-id="7e174-439">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="7e174-440">Дополнительные сведения см. в [справке по SVG в Blazor (dotnet/aspnetcore#18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="7e174-440">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="7e174-441">Поведение при отрисовке пробелов</span><span class="sxs-lookup"><span data-stu-id="7e174-441">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7e174-442">Если директива [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) не используется со значением `true`, лишние пробелы будут удаляться по умолчанию, если:</span><span class="sxs-lookup"><span data-stu-id="7e174-442">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="7e174-443">Они находятся в начале или конце элемента.</span><span class="sxs-lookup"><span data-stu-id="7e174-443">Leading or trailing within an element.</span></span>
* <span data-ttu-id="7e174-444">Они находятся в начале или конце параметра `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="7e174-444">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="7e174-445">Например, когда дочернее содержимое передается другому компоненту.</span><span class="sxs-lookup"><span data-stu-id="7e174-445">For example, child content passed to another component.</span></span>
* <span data-ttu-id="7e174-446">Они находятся в начале или конце блока кода C#, например `@if` и `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="7e174-446">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="7e174-447">При использовании правила CSS, такого как `white-space: pre`, удаление пробелов может повлиять на отображаемые выходные данные.</span><span class="sxs-lookup"><span data-stu-id="7e174-447">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="7e174-448">Чтобы отключить эту оптимизацию производительности и сохранить пробелы, выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="7e174-448">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="7e174-449">Добавьте директиву `@preservewhitespace true` в начало  `.razor`-файла, чтобы применить предпочтение к конкретному компоненту.</span><span class="sxs-lookup"><span data-stu-id="7e174-449">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="7e174-450">Добавьте директиву `@preservewhitespace true` внутрь файла `_Imports.razor`, чтобы применить предпочтение ко всему подкаталогу или проекту целиком.</span><span class="sxs-lookup"><span data-stu-id="7e174-450">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="7e174-451">В большинстве случаев никаких действий не требуется, так как приложения, как правило, продолжают работать в обычном режиме (но быстрее).</span><span class="sxs-lookup"><span data-stu-id="7e174-451">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="7e174-452">Если удаление пробелов приводит к возникновению проблем в конкретном компоненте, используйте `@preservewhitespace true` в таком компоненте, чтобы отключить эту оптимизацию.</span><span class="sxs-lookup"><span data-stu-id="7e174-452">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="7e174-453">Пробел сохраняется в исходном коде компонента.</span><span class="sxs-lookup"><span data-stu-id="7e174-453">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="7e174-454">Текст, состоящий только из пробелов, отображается в модели DOM браузера даже при отсутствии визуального эффекта.</span><span class="sxs-lookup"><span data-stu-id="7e174-454">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="7e174-455">Рассмотрим следующий код компонента Razor:</span><span class="sxs-lookup"><span data-stu-id="7e174-455">Consider the following Razor component code:</span></span>

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

<span data-ttu-id="7e174-456">В предыдущем примере отображается следующий ненужный пробел:</span><span class="sxs-lookup"><span data-stu-id="7e174-456">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="7e174-457">за пределами блока кода `@foreach`;</span><span class="sxs-lookup"><span data-stu-id="7e174-457">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="7e174-458">вокруг элемента `<li>`;</span><span class="sxs-lookup"><span data-stu-id="7e174-458">Around the `<li>` element.</span></span>
* <span data-ttu-id="7e174-459">вокруг выходных данных `@item.Text`.</span><span class="sxs-lookup"><span data-stu-id="7e174-459">Around the `@item.Text` output.</span></span>

<span data-ttu-id="7e174-460">Для списка, содержащего 100 элементов, создается 402 области пробелов. При этом ни один из дополнительных пробелов не будет визуально влиять на отображаемые выходные данные.</span><span class="sxs-lookup"><span data-stu-id="7e174-460">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="7e174-461">При отображении статического кода HTML для компонентов пробелы внутри тега не сохраняются.</span><span class="sxs-lookup"><span data-stu-id="7e174-461">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="7e174-462">Например, просмотрите исходный код следующего компонента в отображаемых выходных данных:</span><span class="sxs-lookup"><span data-stu-id="7e174-462">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="7e174-463">Пробелы из предыдущей разметки Razor не сохраняются:</span><span class="sxs-lookup"><span data-stu-id="7e174-463">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7e174-464">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7e174-464">Additional resources</span></span>

* <span data-ttu-id="7e174-465"><xref:blazor/security/server/threat-mitigation>. Содержит рекомендации по созданию приложений Blazor Server, которые должны соперничать в условиях нехватки ресурсов.</span><span class="sxs-lookup"><span data-stu-id="7e174-465"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "Справочник по синтаксису Razor для ASP.NET Core"
[2]: <xref:mvc/views/razor#using> "Справочник по синтаксису Razor для ASP.NET Core"
[3]: <xref:mvc/views/razor#attributes> "Справочник по синтаксису Razor для ASP.NET Core"
[4]: <xref:mvc/views/razor#ref> "Справочник по синтаксису Razor для ASP.NET Core"
[5]: <xref:mvc/views/razor#key> "Справочник по синтаксису Razor для ASP.NET Core"
[6]: <xref:mvc/views/razor#inherits> "Справочник по синтаксису Razor для ASP.NET Core"
[7]: <xref:mvc/views/razor#attribute> "Справочник по синтаксису Razor для ASP.NET Core"
[8]: <xref:mvc/views/razor#namespace> "Справочник по синтаксису Razor для ASP.NET Core"
[9]: <xref:mvc/views/razor#page> "Справочник по синтаксису Razor для ASP.NET Core"
[10]: <xref:mvc/views/razor#bind> "Справочник по синтаксису Razor для ASP.NET Core"
