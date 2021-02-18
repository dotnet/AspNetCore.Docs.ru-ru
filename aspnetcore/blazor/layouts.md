---
title: Макеты Blazor в ASP.NET Core
author: guardrex
description: Узнайте, как создавать многократно используемые компоненты макета для Blazor приложений.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: d1f3e2028ca120b5901aca0b24802d872ae52597
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279736"
---
# <a name="aspnet-core-blazor-layouts"></a>Макеты Blazor в ASP.NET Core

Некоторые элементы приложения, такие как меню, сообщения об авторских правах и логотипы компании, обычно являются частью общего макета приложения и используются каждым компонентом в приложении. Копирование кода этих элементов во все компоненты приложения не является эффективным подходом. Каждый раз, когда одному из элементов требуется обновление, нужно обновлять каждый компонент. Такое дублирование сложно поддерживать, и это может привести к несогласованному содержимому с течением времени. Для решения этой проблемы используются *макеты*.

Технически макет представляет собой просто другой компонент. Макет определяется в шаблоне Razor или в коде C# и может использовать [привязки данных](xref:blazor/components/data-binding), [внедрения зависимостей](xref:blazor/fundamentals/dependency-injection) и другие сценарии компонентов. Макеты применяются только к машрутизируемым компонентам Razor, которые имеют директивы [`@page`](xref:mvc/views/razor#page).

Чтобы преобразовать компонент в макет, сделайте следующее:

* Создайте компонент, производный от <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>. <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> определяет свойство <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> для отображаемого содержимого внутри макета.
* Использует синтаксис `@Body` Razor для указания расположения в разметке макета, в которой отображается содержимое.

В следующем примере кода показан шаблон Razor компонента макета `MainLayout.razor`. Макет наследует <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> и задает `@Body` между панелью навигации и нижним колонтитулом:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a>`MainLayout`

В приложении на основе одного из шаблонов проектов Blazor компонент `MainLayout` (`MainLayout.razor`) находится в папке `Shared` приложения:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a>Макет по умолчанию

Укажите макет приложения по умолчанию в компоненте <xref:Microsoft.AspNetCore.Components.Routing.Router> в файле `App.razor` приложения. Следующий компонент <xref:Microsoft.AspNetCore.Components.Routing.Router>, предоставляемый шаблонами Blazor по умолчанию, задает для макета по умолчанию компонент `MainLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Чтобы предоставить макет по умолчанию для содержимого <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, укажите <xref:Microsoft.AspNetCore.Components.LayoutView> для содержимого <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Дополнительные сведения о компоненте <xref:Microsoft.AspNetCore.Components.Routing.Router> см. в разделе <xref:blazor/fundamentals/routing>.

Указание макета в качестве макета по умолчанию в маршрутизаторе достаточно полезно, так как в этом случае его можно переопределить отдельно для каждого компонента или папки. Для настройки макета приложения по умолчанию предпочтительнее использовать маршрутизатор, поскольку это наиболее общий способ.

## <a name="specify-a-layout-in-a-component"></a>Указание макета в компоненте

Используйте директиву [`@layout`](xref:mvc/views/razor#layout) Razor, чтобы применить макет к маршрутизируемому компоненту Razor, который также имеет директиву [`@page`](xref:mvc/views/razor#page). Компилятор преобразует `@layout` в <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, который применяется к классу компонента.

Содержимое следующего компонента `MasterList` вставляется в `MasterLayout` в позиции `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Указание макета непосредственно в компоненте переопределяет *макет по умолчанию*, заданный в маршрутизаторе, или директиву `@layout`, импортированную из файла `_Imports.razor`.

## <a name="centralized-layout-selection"></a>Централизованный выбор макетов

В каждой папке приложения может дополнительно содержаться файл шаблона с именем `_Imports.razor`. Компилятор включает директивы, указанные в файле импорта, во все шаблоны Razor в одной и той же папке и рекурсивно во всех ее вложенных папках. Таким образом, файл `_Imports.razor`, содержащий `@layout MyCoolLayout`, гарантирует, что все компоненты в папке используют `MyCoolLayout`. Нет необходимости многократно добавлять `@layout MyCoolLayout` во все файлы `.razor` в папке и вложенных папках. Директивы `@using` применяются к компонентам таким же образом.

Следующий файл `_Imports.razor` импортирует следующее:

* `MyCoolLayout`.
* Все компоненты Razor в одной и той же папке и во всех вложенных папках.
* Пространство имен `BlazorApp1.Data` .
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Файл `_Imports.razor` аналогичен файлу [_ViewImports.cshtml для представлений и страниц Razor](xref:mvc/views/layout#importing-shared-directives), однако он применяется специально к файлам компонентов Razor.

Указание макета в файле `_Imports.razor` переопределяет макет, указанный в качестве *макета по умолчанию* маршрутизатора.

> [!WARNING]
> **Не** добавляйте директиву Razor `@layout` к корневому файлу `_Imports.razor`, так как это приведет к формированию бесконечного цикла макетов в файле. Чтобы управлять макетом приложения по умолчанию, укажите макет в компоненте `Router`. Дополнительные сведения см. в разделе [Макет по умолчанию](#default-layout).

> [!NOTE]
> Директива [`@layout`](xref:mvc/views/razor#layout) Razor применяет макет только к маршрутизируемым Razor компонентам с директивами [`@page`](xref:mvc/views/razor#page).

## <a name="nested-layouts"></a>Вложенные макеты

Приложения могут состоять из вложенных макетов. Компонент может ссылаться на макет, который, в свою очередь, ссылается на другой макет. Например, для создания структуры многоуровневого меню используются вложенные макеты.

В следующем примере показано использование вложенных макетов. Файл `EpisodesComponent.razor` является отображаемым компонентом. Компонент ссылается на `MasterListLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Файл `MasterListLayout.razor` предоставляет `MasterListLayout`. Макет ссылается на другой макет, `MasterLayout`, где он преобразуется для просмотра. `EpisodesComponent` отображается там, где находится `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Наконец, `MasterLayout` в файле `MasterLayout.razor` содержит элементы макета верхнего уровня, такие как заголовок, главное меню и нижний колонтитул. `MasterListLayout` с `EpisodesComponent` отображается там, где находится `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Совместное использование макета Razor Pages с интегрированными компонентами

Если маршрутизируемые компоненты интегрированы в приложение Razor Pages, общий макет приложения можно использовать с компонентами. Для получения дополнительной информации см. <xref:blazor/components/prerendering-and-integration>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/views/layout>
