---
title: Виртуализация компонентов ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать виртуализацию компонентов в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 920a23aee0d0555e93c829142700709d5881afd2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753102"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>Виртуализация компонентов ASP.NET Core Blazor

Автор: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Повысьте воспринимаемую производительность отрисовки компонентов с помощью встроенной поддержки виртуализации платформы Blazor. Виртуализация — это метод отображения только видимых в данный момент частей пользовательского интерфейса. Например, виртуализация удобна в случае, когда в приложении должен быть отрисован длинный список элементов и в любой конкретный момент времени должно быть видимым только подмножество элементов. Blazor предоставляет `Virtualize` компонент, который можно использовать для добавления виртуализации в компоненты приложения.

Без виртуализации обычный список может использовать цикл C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) для отрисовки каждого элемента в списке:

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

Если список содержит тысячи элементов, его отрисовка может занять много времени. При этом не исключена заметная задержка отображения пользовательского интерфейса.

Вместо отрисовки каждого элемента списка по отдельности замените цикл [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) компонентом `Virtualize` и укажите источник фиксированного элемента с помощью `Items`. Отрисовываются только элементы, видимые в данный момент:

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Вместо указания контекста для компонента с помощью `Context` можно использовать значение `context` (`@context.{PROPERTY}`) в шаблоне содержимого элемента:

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

Компонент `Virtualize` вычисляет количество подлежащих отрисовке элементов на основе высоты контейнера и размера отображаемых элементов.

Содержимое элемента для компонента `Virtualize` может включать в себя следующее:

* обычный код HTML и код Razor, как показано в предыдущем примере;
* один или несколько компонентов Razor;
* сочетание компонентов HTML/Razor и Razor.

## <a name="item-provider-delegate"></a>Делегат поставщика элементов

Если вы не хотите загружать все элементы в память, можно указать метод делегата поставщика элементов для параметра `ItemsProvider` компонента, который асинхронно извлекает запрошенные элементы по запросу:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Поставщик элементов получает `ItemsProviderRequest`, который указывает необходимое количество элементов, начиная с заданного начального индекса. Затем поставщик элементов извлекает запрошенные элементы из базы данных или другой службы и возвращает их в виде `ItemsProviderResult<TItem>` вместе с количеством всех элементов. Поставщик элементов может извлекать элементы с каждым запросом или кэшировать их, чтобы они были доступны.

Компонент `Virtualize` может принимать только **один источник элемента** из параметров, поэтому не пытайтесь одновременно использовать поставщик элементов и назначить коллекцию для `Items`. Если назначаются оба значения, создается <xref:System.InvalidOperationException>, когда параметры компонента задаются во время выполнения.

В следующем примере показана загрузка сотрудников из `EmployeeService`:

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a>Заполнитель

Поскольку запрос элементов из удаленного источника данных может занимать некоторое время, можно отрисовать заполнитель (`<Placeholder>...</Placeholder>`), пока не будут доступны данные элемента:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a>Размер элемента

Размер каждого элемента в пикселях можно задать с помощью `ItemSize` (по умолчанию: 50 пикс.):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Количество элементов в нерабочей области

`OverscanCount` определяет количество дополнительных элементов, отрисовываемых до и после видимой области. Этот параметр позволяет уменьшить частоту отрисовки во время прокрутки. Однако более высокие значения приводят к отображению большего числа элементов на странице (по умолчанию: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Изменения состояния

При внесении изменений в элементы, отрисовываемые компонентом `Virtualize`, вызовите метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для принудительной оценки и отрисовки компонента.
