---
title: Изоляция CSS в ASP.NET Core Blazor
author: daveabrock
description: Узнайте, как изоляция CSS позволяет ограничить область применения CSS к компонентам, что позволяет упростить CSS и избежать конфликтов с другими компонентами или библиотеками.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529986"
---
# <a name="aspnet-core-blazor-css-isolation"></a><span data-ttu-id="5ce82-103">Изоляция CSS в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5ce82-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="5ce82-104">Автор: [Дейв Брок](https://twitter.com/daveabrock) (Dave Brock)</span><span class="sxs-lookup"><span data-stu-id="5ce82-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="5ce82-105">Изоляция CSS упрощает использование CSS в приложении, предотвращая зависимости от глобальных стилей, и помогает избежать конфликтов стилей между компонентами и библиотеками.</span><span class="sxs-lookup"><span data-stu-id="5ce82-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="5ce82-106">Включение изоляции CSS</span><span class="sxs-lookup"><span data-stu-id="5ce82-106">Enable CSS isolation</span></span> 

<span data-ttu-id="5ce82-107">Чтобы определить стили, относящиеся к компоненту, создайте файл `.razor.css`, соответствующий имени файла `.razor` для компонента в той же папке.</span><span class="sxs-lookup"><span data-stu-id="5ce82-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component in the same folder.</span></span> <span data-ttu-id="5ce82-108">Этот файл `.razor.css` будет *файлом CSS с назначенной областью*.</span><span class="sxs-lookup"><span data-stu-id="5ce82-108">The `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="5ce82-109">Для компонента `Example` в файле `Example.razor` создайте файл рядом с компонентом с именем `Example.razor.css`.</span><span class="sxs-lookup"><span data-stu-id="5ce82-109">For an `Example` component in an `Example.razor` file, create a file alongside the component named `Example.razor.css`.</span></span> <span data-ttu-id="5ce82-110">Файл `Example.razor.css` должен находиться в той же папке, что и компонент `Example` (`Example.razor`).</span><span class="sxs-lookup"><span data-stu-id="5ce82-110">The `Example.razor.css` file must reside in the same folder as the `Example` component (`Example.razor`).</span></span> <span data-ttu-id="5ce82-111">В базовом имени `Example` файла регистр **не** учитывается.</span><span class="sxs-lookup"><span data-stu-id="5ce82-111">The "`Example`" base name of the file is **not** case-sensitive.</span></span>

<span data-ttu-id="5ce82-112">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-112">`Pages/Example.razor`:</span></span>

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

<span data-ttu-id="5ce82-113">`Pages/Example.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-113">`Pages/Example.razor.css`:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="5ce82-114">**Стили, определенные в файле `Example.razor.css`, применяются только к отображаемым выходным данным компонента `Example`.**</span><span class="sxs-lookup"><span data-stu-id="5ce82-114">**The styles defined in `Example.razor.css` are only applied to the rendered output of the `Example` component.**</span></span> <span data-ttu-id="5ce82-115">Изоляция CSS применяется к элементам HTML в соответствующем файле Razor.</span><span class="sxs-lookup"><span data-stu-id="5ce82-115">CSS isolation is applied to HTML elements in the matching Razor file.</span></span> <span data-ttu-id="5ce82-116">Все объявления CSS `h1`, определенные в других областях приложения, не будут конфликтовать со стилями компонента `Example`.</span><span class="sxs-lookup"><span data-stu-id="5ce82-116">Any `h1` CSS declarations defined elsewhere in the app don't conflict with the `Example` component's styles.</span></span>

> [!NOTE]
> <span data-ttu-id="5ce82-117">Чтобы обеспечить изоляцию стиля при выполнении объединения, импорт CSS в блоки кода Razor не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="5ce82-117">In order to guarantee style isolation when bundling occurs, importing CSS in Razor code blocks isn't supported.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="5ce82-118">Объединение изоляций CSS</span><span class="sxs-lookup"><span data-stu-id="5ce82-118">CSS isolation bundling</span></span>

<span data-ttu-id="5ce82-119">Изоляция CSS выполняется во время сборки.</span><span class="sxs-lookup"><span data-stu-id="5ce82-119">CSS isolation occurs at build time.</span></span> <span data-ttu-id="5ce82-120">Во время этого процесса Blazor переписывает селекторы CSS в соответствии с разметкой, отображаемой компонентом.</span><span class="sxs-lookup"><span data-stu-id="5ce82-120">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="5ce82-121">Эти переписанные стили CSS объединяются и создаются как статические ресурсы в `{PROJECT NAME}.styles.css`, где заполнитель `{PROJECT NAME}` является названием пакета или продукта, на который указывает ссылка.</span><span class="sxs-lookup"><span data-stu-id="5ce82-121">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="5ce82-122">По умолчанию эти статические файлы ищутся от корневого пути приложения.</span><span class="sxs-lookup"><span data-stu-id="5ce82-122">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="5ce82-123">В приложении ссылайтесь на объединенный файл, проверив ссылку внутри тега `<head>` созданного HTML:</span><span class="sxs-lookup"><span data-stu-id="5ce82-123">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="5ce82-124">В объединенном файле каждый компонент связан с идентификатором области.</span><span class="sxs-lookup"><span data-stu-id="5ce82-124">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="5ce82-125">Для каждого компонента, имеющего стиль, атрибут HTML добавляется в формате `b-<10-character-string>`.</span><span class="sxs-lookup"><span data-stu-id="5ce82-125">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="5ce82-126">Идентификатор уникален и отличается для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="5ce82-126">The identifier is unique and different for each app.</span></span> <span data-ttu-id="5ce82-127">В отображаемом компоненте `Counter` Blazor добавляет идентификатор области к элементу `h1`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-127">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="5ce82-128">Файл `ProjectName.styles.css` использует идентификатор области для группирования объявления стиля с его компонентом.</span><span class="sxs-lookup"><span data-stu-id="5ce82-128">The `ProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="5ce82-129">В следующем примере представлен стиль для предыдущего элемента `<h1>`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-129">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="5ce82-130">Во время сборки создается пакет проектов с использованием соглашения `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, где заполнитель `{STATIC WEB ASSETS BASE PATH}` является базовым путем к статическим веб-ресурсам.</span><span class="sxs-lookup"><span data-stu-id="5ce82-130">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="5ce82-131">Если используются другие проекты, такие как пакеты NuGet или [библиотеки классов Razor](xref:blazor/components/class-libraries), то объединенный файл:</span><span class="sxs-lookup"><span data-stu-id="5ce82-131">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="5ce82-132">ссылается на стили с помощью импорта CSS;</span><span class="sxs-lookup"><span data-stu-id="5ce82-132">References the styles using CSS imports.</span></span>
* <span data-ttu-id="5ce82-133">не публикуется как статический веб-ресурс приложения, использующего стили.</span><span class="sxs-lookup"><span data-stu-id="5ce82-133">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="5ce82-134">Поддержка дочерних компонентов</span><span class="sxs-lookup"><span data-stu-id="5ce82-134">Child component support</span></span>

<span data-ttu-id="5ce82-135">По умолчанию изоляция CSS применяется только к компоненту, привязанному с помощью формата `{COMPONENT NAME}.razor.css`, где заполнитель `{COMPONENT NAME}` обычно является именем компонента.</span><span class="sxs-lookup"><span data-stu-id="5ce82-135">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="5ce82-136">Чтобы применить изменения к дочернему компоненту, используйте объединение `::deep` для всех элементов-потомков в файле `.razor.css` родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="5ce82-136">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="5ce82-137">Объединение `::deep` выбирает элементы, которые являются *потомками* идентификатора области, созданного элементом.</span><span class="sxs-lookup"><span data-stu-id="5ce82-137">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="5ce82-138">В следующем примере показан родительский компонент с именем `Parent` и дочерний компонент с именем `Child`.</span><span class="sxs-lookup"><span data-stu-id="5ce82-138">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="5ce82-139">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-139">`Pages/Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="5ce82-140">`Shared/Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-140">`Shared/Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="5ce82-141">Измените объявление `h1` в `Parent.razor.css`, добавив объединение `::deep` для указания того, что объявление стиля `h1` должно применяться к родительскому компоненту и его дочерним элементам.</span><span class="sxs-lookup"><span data-stu-id="5ce82-141">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children.</span></span>

<span data-ttu-id="5ce82-142">`Pages/Parent.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-142">`Pages/Parent.razor.css`:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="5ce82-143">Стиль `h1` теперь применяется к компонентам `Parent` и `Child` без необходимости создания отдельного CSS-файла с областью действия для дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="5ce82-143">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

<span data-ttu-id="5ce82-144">Объединение `::deep` работает только с элементами-потомками.</span><span class="sxs-lookup"><span data-stu-id="5ce82-144">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="5ce82-145">Следующая разметка должным образом применяет стили `h1` к компонентам.</span><span class="sxs-lookup"><span data-stu-id="5ce82-145">The following markup applies the `h1` styles to components as expected.</span></span> <span data-ttu-id="5ce82-146">Идентификатор области родительского компонента применяется к элементу `div`, поэтому браузеру известно, что нужно наследовать стили от родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="5ce82-146">The parent component's scope identifier is applied to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>

<span data-ttu-id="5ce82-147">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-147">`Pages/Parent.razor`:</span></span>

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

<span data-ttu-id="5ce82-148">При этом исключение элемента `div` удаляет отношение потомков.</span><span class="sxs-lookup"><span data-stu-id="5ce82-148">However, excluding the `div` element removes the descendant relationship.</span></span> <span data-ttu-id="5ce82-149">В следующем примере стиль **не** применяется к дочернему компоненту.</span><span class="sxs-lookup"><span data-stu-id="5ce82-149">In the following example, the style is **not** applied to the child component.</span></span>

<span data-ttu-id="5ce82-150">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-150">`Pages/Parent.razor`:</span></span>

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a><span data-ttu-id="5ce82-151">Поддержка препроцессоров CSS</span><span class="sxs-lookup"><span data-stu-id="5ce82-151">CSS preprocessor support</span></span>

<span data-ttu-id="5ce82-152">Препроцессоры CSS полезны для улучшения разработки CSS с помощью таких функций, как переменные, вложенность, модули, примеси и наследование.</span><span class="sxs-lookup"><span data-stu-id="5ce82-152">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="5ce82-153">Хотя изоляция CSS изначально не поддерживает препроцессоры CSS, такие как Sass и Less, интеграция препроцессоров CSS происходит прозрачно, поскольку компиляция препроцессора выполняется до того, как Blazor перезаписывает селекторы CSS в процессе сборки.</span><span class="sxs-lookup"><span data-stu-id="5ce82-153">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="5ce82-154">С помощью Visual Studio, например, настройте существующую компиляцию препроцессора в качестве задачи **перед сборкой** в диспетчере выполнения задач Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5ce82-154">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="5ce82-155">Многие сторонние пакеты NuGet, такие как [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), могут компилировать файлы SASS и SCSS в начале процесса сборки перед выполнением изоляции CSS, и дополнительная настройка не требуется.</span><span class="sxs-lookup"><span data-stu-id="5ce82-155">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="5ce82-156">Конфигурация изоляции CSS</span><span class="sxs-lookup"><span data-stu-id="5ce82-156">CSS isolation configuration</span></span>

<span data-ttu-id="5ce82-157">Изоляция CSS может работать без дополнительных настроек, но она предоставляет возможности конфигурации для некоторых сложных сценариев, например при наличии зависимостей от существующих инструментов или рабочих процессов.</span><span class="sxs-lookup"><span data-stu-id="5ce82-157">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="5ce82-158">Настройка формата идентификатора области</span><span class="sxs-lookup"><span data-stu-id="5ce82-158">Customize scope identifier format</span></span>

<span data-ttu-id="5ce82-159">По умолчанию для идентификаторов областей используется формат `b-<10-character-string>`.</span><span class="sxs-lookup"><span data-stu-id="5ce82-159">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="5ce82-160">Чтобы настроить формат идентификатора области, измените шаблон в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="5ce82-160">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="5ce82-161">В предыдущем примере CSS, сформированный для `Example.Razor.css`, изменяет свой идентификатор области с `b-<10-character-string>` на `my-custom-scope-identifier`.</span><span class="sxs-lookup"><span data-stu-id="5ce82-161">In the preceding example, the CSS generated for `Example.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

<span data-ttu-id="5ce82-162">Используйте идентификаторы областей, чтобы обеспечить наследование файлов CSS с назначенной областью.</span><span class="sxs-lookup"><span data-stu-id="5ce82-162">Use scope identifiers to achieve inheritance with scoped CSS files.</span></span> <span data-ttu-id="5ce82-163">В следующем примере файла проекта файл `BaseComponent.razor.css` содержит общие стили для компонентов.</span><span class="sxs-lookup"><span data-stu-id="5ce82-163">In the following project file example, a `BaseComponent.razor.css` file contains common styles across components.</span></span> <span data-ttu-id="5ce82-164">Файл `DerivedComponent.razor.css` наследует эти стили.</span><span class="sxs-lookup"><span data-stu-id="5ce82-164">A `DerivedComponent.razor.css` file inherits these styles.</span></span>

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="5ce82-165">Используйте оператор подстановочного знака (`*`), чтобы предоставить идентификаторы областей для нескольких файлов:</span><span class="sxs-lookup"><span data-stu-id="5ce82-165">Use the wildcard (`*`) operator to share scope identifiers across multiple files:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="5ce82-166">Изменение базового пути для статических веб-ресурсов</span><span class="sxs-lookup"><span data-stu-id="5ce82-166">Change base path for static web assets</span></span>

<span data-ttu-id="5ce82-167">Файл `scoped.styles.css` создается в корне приложения.</span><span class="sxs-lookup"><span data-stu-id="5ce82-167">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="5ce82-168">Чтобы изменить путь по умолчанию, используйте свойство `StaticWebAssetBasePath` в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="5ce82-168">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="5ce82-169">В следующем примере файл `scoped.styles.css` и остальные ресурсы приложения размещаются по пути `_content`:</span><span class="sxs-lookup"><span data-stu-id="5ce82-169">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="5ce82-170">Отключение автоматического объединения</span><span class="sxs-lookup"><span data-stu-id="5ce82-170">Disable automatic bundling</span></span>

<span data-ttu-id="5ce82-171">Чтобы отказаться от того, как Blazor публикует и загружает файлы с заданной областью во время выполнения, используйте свойство `DisableScopedCssBundling`.</span><span class="sxs-lookup"><span data-stu-id="5ce82-171">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="5ce82-172">При использовании этого свойства за получение изолированных файлов CSS из каталога `obj` и их публикацию и загрузку во время выполнения отвечают другие средства или процессы:</span><span class="sxs-lookup"><span data-stu-id="5ce82-172">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a><span data-ttu-id="5ce82-173">Поддержка библиотеки классов Razor (RCL)</span><span class="sxs-lookup"><span data-stu-id="5ce82-173">Razor class library (RCL) support</span></span>

<span data-ttu-id="5ce82-174">Когда [библиотека классов Razor (RCL)](xref:razor-pages/ui-class) предоставляет изолированные стили, атрибут `href` тега `<link>` указывает на `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` со следующими заполнителями:</span><span class="sxs-lookup"><span data-stu-id="5ce82-174">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="5ce82-175">`{STATIC WEB ASSET BASE PATH}`: базовый путь к статическому веб-ресурсу.</span><span class="sxs-lookup"><span data-stu-id="5ce82-175">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="5ce82-176">`{ASSEMBLY NAME}`: имя сборки библиотеки классов.</span><span class="sxs-lookup"><span data-stu-id="5ce82-176">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="5ce82-177">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="5ce82-177">In the following example:</span></span>

* <span data-ttu-id="5ce82-178">Базовый путь к статическому веб-ресурсу — `_content/ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="5ce82-178">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="5ce82-179">Имя сборки библиотеки классов — `ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="5ce82-179">The class library's assembly name is `ClassLib`.</span></span>

<span data-ttu-id="5ce82-180">`wwwroot/index.html` (Blazor WebAssembly) или `Pages_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="5ce82-180">`wwwroot/index.html` (Blazor WebAssembly) or `Pages_Host.cshtml` (Blazor Server):</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="5ce82-181">Дополнительные сведения о RCL и библиотеках компонентов см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="5ce82-181">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="5ce82-182"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="5ce82-182"><xref:blazor/components/class-libraries>.</span></span>
