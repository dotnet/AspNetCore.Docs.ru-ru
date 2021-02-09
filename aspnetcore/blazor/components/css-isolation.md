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
# <a name="aspnet-core-blazor-css-isolation"></a>Изоляция CSS в ASP.NET Core Blazor

Автор: [Дейв Брок](https://twitter.com/daveabrock) (Dave Brock)

Изоляция CSS упрощает использование CSS в приложении, предотвращая зависимости от глобальных стилей, и помогает избежать конфликтов стилей между компонентами и библиотеками.

## <a name="enable-css-isolation"></a>Включение изоляции CSS 

Чтобы определить стили, относящиеся к компоненту, создайте файл `.razor.css`, соответствующий имени файла `.razor` для компонента в той же папке. Этот файл `.razor.css` будет *файлом CSS с назначенной областью*. 

Для компонента `Example` в файле `Example.razor` создайте файл рядом с компонентом с именем `Example.razor.css`. Файл `Example.razor.css` должен находиться в той же папке, что и компонент `Example` (`Example.razor`). В базовом имени `Example` файла регистр **не** учитывается.

`Pages/Example.razor`:

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

`Pages/Example.razor.css`:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

**Стили, определенные в файле `Example.razor.css`, применяются только к отображаемым выходным данным компонента `Example`.** Изоляция CSS применяется к элементам HTML в соответствующем файле Razor. Все объявления CSS `h1`, определенные в других областях приложения, не будут конфликтовать со стилями компонента `Example`.

> [!NOTE]
> Чтобы обеспечить изоляцию стиля при выполнении объединения, импорт CSS в блоки кода Razor не поддерживается.

## <a name="css-isolation-bundling"></a>Объединение изоляций CSS

Изоляция CSS выполняется во время сборки. Во время этого процесса Blazor переписывает селекторы CSS в соответствии с разметкой, отображаемой компонентом. Эти переписанные стили CSS объединяются и создаются как статические ресурсы в `{PROJECT NAME}.styles.css`, где заполнитель `{PROJECT NAME}` является названием пакета или продукта, на который указывает ссылка.

По умолчанию эти статические файлы ищутся от корневого пути приложения. В приложении ссылайтесь на объединенный файл, проверив ссылку внутри тега `<head>` созданного HTML:

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

В объединенном файле каждый компонент связан с идентификатором области. Для каждого компонента, имеющего стиль, атрибут HTML добавляется в формате `b-<10-character-string>`. Идентификатор уникален и отличается для каждого приложения. В отображаемом компоненте `Counter` Blazor добавляет идентификатор области к элементу `h1`:

```html
<h1 b-3xxtam6d07>
```

Файл `ProjectName.styles.css` использует идентификатор области для группирования объявления стиля с его компонентом. В следующем примере представлен стиль для предыдущего элемента `<h1>`:

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

Во время сборки создается пакет проектов с использованием соглашения `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, где заполнитель `{STATIC WEB ASSETS BASE PATH}` является базовым путем к статическим веб-ресурсам.

Если используются другие проекты, такие как пакеты NuGet или [библиотеки классов Razor](xref:blazor/components/class-libraries), то объединенный файл:

* ссылается на стили с помощью импорта CSS;
* не публикуется как статический веб-ресурс приложения, использующего стили.

## <a name="child-component-support"></a>Поддержка дочерних компонентов

По умолчанию изоляция CSS применяется только к компоненту, привязанному с помощью формата `{COMPONENT NAME}.razor.css`, где заполнитель `{COMPONENT NAME}` обычно является именем компонента. Чтобы применить изменения к дочернему компоненту, используйте объединение `::deep` для всех элементов-потомков в файле `.razor.css` родительского компонента. Объединение `::deep` выбирает элементы, которые являются *потомками* идентификатора области, созданного элементом. 

В следующем примере показан родительский компонент с именем `Parent` и дочерний компонент с именем `Child`.

`Pages/Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Shared/Child.razor`:

```razor
<h1>Child Component</h1>
```

Измените объявление `h1` в `Parent.razor.css`, добавив объединение `::deep` для указания того, что объявление стиля `h1` должно применяться к родительскому компоненту и его дочерним элементам.

`Pages/Parent.razor.css`:

```css
::deep h1 { 
    color: red;
}
```

Стиль `h1` теперь применяется к компонентам `Parent` и `Child` без необходимости создания отдельного CSS-файла с областью действия для дочернего компонента.

Объединение `::deep` работает только с элементами-потомками. Следующая разметка должным образом применяет стили `h1` к компонентам. Идентификатор области родительского компонента применяется к элементу `div`, поэтому браузеру известно, что нужно наследовать стили от родительского компонента.

`Pages/Parent.razor`:

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

При этом исключение элемента `div` удаляет отношение потомков. В следующем примере стиль **не** применяется к дочернему компоненту.

`Pages/Parent.razor`:

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a>Поддержка препроцессоров CSS

Препроцессоры CSS полезны для улучшения разработки CSS с помощью таких функций, как переменные, вложенность, модули, примеси и наследование. Хотя изоляция CSS изначально не поддерживает препроцессоры CSS, такие как Sass и Less, интеграция препроцессоров CSS происходит прозрачно, поскольку компиляция препроцессора выполняется до того, как Blazor перезаписывает селекторы CSS в процессе сборки. С помощью Visual Studio, например, настройте существующую компиляцию препроцессора в качестве задачи **перед сборкой** в диспетчере выполнения задач Visual Studio.

Многие сторонние пакеты NuGet, такие как [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), могут компилировать файлы SASS и SCSS в начале процесса сборки перед выполнением изоляции CSS, и дополнительная настройка не требуется.

## <a name="css-isolation-configuration"></a>Конфигурация изоляции CSS

Изоляция CSS может работать без дополнительных настроек, но она предоставляет возможности конфигурации для некоторых сложных сценариев, например при наличии зависимостей от существующих инструментов или рабочих процессов.

### <a name="customize-scope-identifier-format"></a>Настройка формата идентификатора области

По умолчанию для идентификаторов областей используется формат `b-<10-character-string>`. Чтобы настроить формат идентификатора области, измените шаблон в файле проекта:

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

В предыдущем примере CSS, сформированный для `Example.Razor.css`, изменяет свой идентификатор области с `b-<10-character-string>` на `my-custom-scope-identifier`.

Используйте идентификаторы областей, чтобы обеспечить наследование файлов CSS с назначенной областью. В следующем примере файла проекта файл `BaseComponent.razor.css` содержит общие стили для компонентов. Файл `DerivedComponent.razor.css` наследует эти стили.

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

Используйте оператор подстановочного знака (`*`), чтобы предоставить идентификаторы областей для нескольких файлов:

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a>Изменение базового пути для статических веб-ресурсов

Файл `scoped.styles.css` создается в корне приложения. Чтобы изменить путь по умолчанию, используйте свойство `StaticWebAssetBasePath` в файле проекта. В следующем примере файл `scoped.styles.css` и остальные ресурсы приложения размещаются по пути `_content`:

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>Отключение автоматического объединения

Чтобы отказаться от того, как Blazor публикует и загружает файлы с заданной областью во время выполнения, используйте свойство `DisableScopedCssBundling`. При использовании этого свойства за получение изолированных файлов CSS из каталога `obj` и их публикацию и загрузку во время выполнения отвечают другие средства или процессы:

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a>Поддержка библиотеки классов Razor (RCL)

Когда [библиотека классов Razor (RCL)](xref:razor-pages/ui-class) предоставляет изолированные стили, атрибут `href` тега `<link>` указывает на `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` со следующими заполнителями:

* `{STATIC WEB ASSET BASE PATH}`: базовый путь к статическому веб-ресурсу.
* `{ASSEMBLY NAME}`: имя сборки библиотеки классов.

В следующем примере:

* Базовый путь к статическому веб-ресурсу — `_content/ClassLib`.
* Имя сборки библиотеки классов — `ClassLib`.

`wwwroot/index.html` (Blazor WebAssembly) или `Pages_Host.cshtml` (Blazor Server):

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

Дополнительные сведения о RCL и библиотеках компонентов см. в следующих статьях:

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>.
