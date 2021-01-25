---
title: Библиотеки классов компонентов Razor в ASP.NET Core
author: guardrex
description: Сведения о включении компонентов в приложения Blazor из внешней библиотеки компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 14370f9bbf45079fd3654d3e55af4178691cf4f5
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252556"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="3e5a0-103">Библиотеки классов компонентов Razor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e5a0-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="3e5a0-104">Автор: [Саймон Тиммс](https://github.com/stimms) (Simon Timms)</span><span class="sxs-lookup"><span data-stu-id="3e5a0-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="3e5a0-105">Компоненты можно совместно использовать в проектах посредством [библиотеки классов Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="3e5a0-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="3e5a0-106">*Библиотека классов компонентов Razor* может включаться из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="3e5a0-107">другого проекта в решении;</span><span class="sxs-lookup"><span data-stu-id="3e5a0-107">Another project in the solution.</span></span>
* <span data-ttu-id="3e5a0-108">пакета NuGet;</span><span class="sxs-lookup"><span data-stu-id="3e5a0-108">A NuGet package.</span></span>
* <span data-ttu-id="3e5a0-109">связанной библиотеки .NET.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-109">A referenced .NET library.</span></span>

<span data-ttu-id="3e5a0-110">Так же как компоненты являются обычными типами .NET, компоненты, предоставляемые RCL, представляют собой обычные сборки .NET.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="3e5a0-111">Создание библиотеки RCL</span><span class="sxs-lookup"><span data-stu-id="3e5a0-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e5a0-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e5a0-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3e5a0-113">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-113">Create a new project.</span></span>
1. <span data-ttu-id="3e5a0-114">Выберите **Библиотека классов Razor** .</span><span class="sxs-lookup"><span data-stu-id="3e5a0-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="3e5a0-115">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-115">Select **Next**.</span></span>
1. <span data-ttu-id="3e5a0-116">В диалоговом окне **Создать библиотеку классов Razor** щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="3e5a0-117">В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3e5a0-118">В примерах в этой статье используется имя проекта `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="3e5a0-119">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-119">Select **Create**.</span></span>
1. <span data-ttu-id="3e5a0-120">Добавьте библиотеку RCL в решение.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="3e5a0-121">Щелкните решение правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-121">Right-click the solution.</span></span> <span data-ttu-id="3e5a0-122">Выберите **Добавить** > **Существующий проект**.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="3e5a0-123">Перейдите к файлу проекта RCL.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="3e5a0-124">Выберите файл проекта RCL (`.csproj`).</span><span class="sxs-lookup"><span data-stu-id="3e5a0-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="3e5a0-125">Добавьте ссылку на RCL из приложения:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-125">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="3e5a0-126">Щелкните проект приложения правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-126">Right-click the app project.</span></span> <span data-ttu-id="3e5a0-127">Выберите **Добавить** > **Ссылка**.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="3e5a0-128">Выберите проект RCL.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-128">Select the RCL project.</span></span> <span data-ttu-id="3e5a0-129">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="3e5a0-130">Если при создании библиотеки RCL на основе шаблона был установлен флажок **Представления и страницы поддержки**, также добавьте в корневой каталог созданного проекта файл `_Imports.razor` со следующим содержимым, чтобы включить разработку компонента Razor:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="3e5a0-131">Вручную добавьте файл в корневой каталог созданного проекта.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3e5a0-132">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e5a0-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="3e5a0-133">Используйте шаблон **Библиотека классов Razor** (`razorclasslib`) с командой [`dotnet new`](/dotnet/core/tools/dotnet-new) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="3e5a0-134">В приведенном ниже примере создается библиотека RCL с именем `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="3e5a0-135">Папка с `ComponentLibrary` создается автоматически при выполнении команды.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="3e5a0-136">Если при создании библиотеки RCL на основе шаблона используется параметр `-s|--support-pages-and-views`, также добавьте в корневой каталог созданного проекта файл `_Imports.razor` со следующим содержимым, чтобы включить разработку компонента Razor:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="3e5a0-137">Вручную добавьте файл в корневой каталог созданного проекта.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="3e5a0-138">Чтобы добавить библиотеку в существующий проект, используйте команду [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="3e5a0-139">В приведенном ниже примере в приложение добавляется библиотека RCL.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="3e5a0-140">Выполните следующую команду из папки проекта приложения, указав путь к библиотеке:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="3e5a0-141">Использование компонента из библиотеки</span><span class="sxs-lookup"><span data-stu-id="3e5a0-141">Consume a library component</span></span>

<span data-ttu-id="3e5a0-142">Чтобы использовать компоненты, определенные в библиотеке в другом проекте, используйте один из описанных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="3e5a0-143">Используйте полное имя типа с пространством имен.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="3e5a0-144">Используйте директиву [`@using`](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="3e5a0-145">Отдельные компоненты можно добавлять по имени.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-145">Individual components can be added by name.</span></span>

<span data-ttu-id="3e5a0-146">В приведенном ниже примере `ComponentLibrary` — это библиотека компонентов, содержащая компонент `Component1` (`Component1.razor`).</span><span class="sxs-lookup"><span data-stu-id="3e5a0-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="3e5a0-147">Компонент `Component1` — это пример компонента, автоматически добавляемого шаблоном проекта RCL при создании библиотеки.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="3e5a0-148">Сослаться на компонент `Component1`, используя его пространство имен, можно так:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="3e5a0-149">Кроме того, можно перенести библиотеку в область с директивой [`@using`](xref:mvc/views/razor#using) и использовать компонент без его пространства имен:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="3e5a0-150">Кроме того, чтобы сделать компоненты библиотеки доступными для всего проекта, включите директиву `@using ComponentLibrary` в файл `_Import.razor` верхнего уровня.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="3e5a0-151">Чтобы применить пространство имен к одному компоненту или набору компонентов в папке, добавьте директиву в файл `_Import.razor` на любом уровне.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3e5a0-152">Для компонентов библиотеки, которые используют [изоляцию CSS](xref:blazor/components/css-isolation), нет необходимости явно связывать таблицы стилей отдельных компонентов библиотеки в приложении, использующем библиотеку.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-152">For library components that use [CSS isolation](xref:blazor/components/css-isolation), there's no need to explicitly link the library's individual component stylesheets in the app that consumes the library.</span></span> <span data-ttu-id="3e5a0-153">Стили компонентов автоматически становятся доступными для использующего приложения.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-153">The component styles are automatically made available to the consuming app.</span></span>

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

<span data-ttu-id="3e5a0-154">Чтобы предоставить дополнительные стили компонентов библиотеки из таблиц стилей в папке `wwwroot` библиотеки, свяжите таблицы стилей в файле `wwwroot/index.html` (Blazor WebAssembly) или файле `Pages/_Host.cshtml` (Blazor Server) использующего приложения:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-154">To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets in the consuming app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3e5a0-155">Чтобы предоставить класс CSS `Component1` из `my-component`, свяжите его с таблицей стилей в файле `wwwroot/index.html` приложения (Blazor WebAssembly) или в файле `Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="3e5a0-155">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="3e5a0-156">Создание библиотеки классов компонентов Razor со статическими ресурсами</span><span class="sxs-lookup"><span data-stu-id="3e5a0-156">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="3e5a0-157">Библиотека RCL может включать в себя статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-157">An RCL can include static assets.</span></span> <span data-ttu-id="3e5a0-158">Такие ресурсы доступны любому приложению, использующему библиотеку.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-158">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="3e5a0-159">Для получения дополнительной информации см. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-159">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="3e5a0-160">Предоставление компонентов и статических ресурсов нескольким размещенным приложениям Blazor</span><span class="sxs-lookup"><span data-stu-id="3e5a0-160">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="3e5a0-161">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-161">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="3e5a0-162">Анализатор совместимости с браузерами для Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3e5a0-162">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="3e5a0-163">Приложения Blazor WebAssembly предназначены для использования в полной контактной зоне API .NET, но из-за ограничений песочницы браузера поддерживаются не все API-интерфейсы .NET.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-163">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="3e5a0-164">При выполнении в WebAssembly неподдерживаемые API-интерфейсы вызывают <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-164">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="3e5a0-165">Анализатор совместимости платформ предупреждает разработчика, когда приложение использует API-интерфейсы, которые не поддерживаются целевыми платформами приложения.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-165">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="3e5a0-166">Это означает, что в приложениях Blazor WebAssembly проверяется поддержка API-интерфейсов в браузерах.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-166">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="3e5a0-167">Добавление заметок к API-интерфейсам .NET Framework для анализатора совместимости является непрерывным процессом, поэтому в настоящее время снабжены заметками не все API-интерфейсы .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-167">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="3e5a0-168">Проекты библиотеки классов Blazor WebAssembly и Razor *автоматически* включают проверки совместимости браузера, добавляя `browser` в качестве поддерживаемой платформы с элементом MSBuild `SupportedPlatform`.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-168">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="3e5a0-169">Разработчики библиотек могут вручную добавить элемент `SupportedPlatform` в файл проекта библиотеки, чтобы включить эту функцию:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-169">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="3e5a0-170">При создании библиотеки укажите, что конкретный API не поддерживается в браузерах, задав для `browser` значение <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-170">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="3e5a0-171">Дополнительные сведения см. в статье [Аннотация API-интерфейсов как неподдерживаемых на конкретных платформах (репозиторий GitHub dotnet/designs](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span><span class="sxs-lookup"><span data-stu-id="3e5a0-171">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="3e5a0-172">Изоляция Blazor JavaScript и ссылки на объекты</span><span class="sxs-lookup"><span data-stu-id="3e5a0-172">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="3e5a0-173">Blazor реализует изоляцию JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="3e5a0-173">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="3e5a0-174">Изоляция JavaScript обеспечивает следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-174">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="3e5a0-175">Импортированный JavaScript больше не засоряет глобальное пространство имен.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-175">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="3e5a0-176">Пользователям библиотеки и компонентов не требуется импортировать связанный код JavaScript вручную.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-176">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="3e5a0-177">Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-177">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="3e5a0-178">Сборка, упаковка и отправка в NuGet</span><span class="sxs-lookup"><span data-stu-id="3e5a0-178">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="3e5a0-179">Так как библиотеки компонентов являются стандартными библиотеками .NET, их упаковка и передача в NuGet не отличается от упаковки и передачи любых других библиотек.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-179">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="3e5a0-180">Упаковка выполняется с помощью команды [`dotnet pack`](/dotnet/core/tools/dotnet-pack) в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="3e5a0-180">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="3e5a0-181">Чтобы отправить пакет в NuGet, используйте команду [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="3e5a0-181">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e5a0-182">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3e5a0-182">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="3e5a0-183">Добавление файла конфигурации фильтрации XML для промежуточного языка (IL) в библиотеку</span><span class="sxs-lookup"><span data-stu-id="3e5a0-183">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="3e5a0-184">Поддержка изоляции CSS с помощью библиотек классов Razor</span><span class="sxs-lookup"><span data-stu-id="3e5a0-184">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="3e5a0-185">Добавление файла конфигурации компоновщика XML для промежуточного языка (IL) в библиотеку</span><span class="sxs-lookup"><span data-stu-id="3e5a0-185">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
