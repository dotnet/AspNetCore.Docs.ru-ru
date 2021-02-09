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
ms.openlocfilehash: 72b33bc3c2861380551915b1e8caab49122e8fab
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529921"
---
# <a name="aspnet-core-blazor-component-virtualization"></a>Виртуализация компонентов ASP.NET Core Blazor

Автор: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Повысьте воспринимаемую производительность отрисовки компонентов с помощью встроенной поддержки виртуализации платформы Blazor. Виртуализация — это метод отображения только видимых в данный момент частей пользовательского интерфейса. Например, виртуализация удобна в случае, когда в приложении должен быть отрисован длинный список элементов и в любой конкретный момент времени должно быть видимым только подмножество элементов. Blazor предоставляет [компонент `Virtualize`](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601), который можно использовать для добавления виртуализации в компоненты приложения.

Компонент `Virtualize` можно использовать в следующих случаях:

* при отрисовке набора элементов данных в цикле;
* если большинство элементов не видны из-за настроек прокрутки;
* если все отображаемые элементы имеют одинаковый размер. Когда пользователь прокручивает список до произвольной точки, этот компонент вычисляет все видимые элементы, чтобы отобразить их.

Без виртуализации обычный список может использовать цикл C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) для отрисовки каждого элемента в списке:

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

Если список содержит тысячи элементов, его отрисовка может занять много времени. При этом не исключена заметная задержка отображения пользовательского интерфейса.

Вместо отрисовки каждого элемента списка по отдельности замените цикл [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) компонентом `Virtualize` и укажите источник фиксированного элемента с помощью <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>. Отрисовываются только элементы, видимые в данный момент:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

Вместо указания контекста для компонента с помощью `Context` можно использовать значение `context` в шаблоне содержимого элемента.

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> Процесс сопоставления объектов модели с элементами и компонентами можно контролировать с помощью атрибута [`@key`](xref:mvc/views/razor#key) директивы. `@key` гарантирует, что алгоритм сравнения сохранит элементы или компоненты на основе значения ключа.
>
> Дополнительные сведения см. в следующих статьях:
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [Справочник по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor#key)

Компонент `Virtualize`:

* вычисляет количество подлежащих отрисовке элементов на основе высоты контейнера и размера отображаемых элементов;
* пересчитывает и повторно отрисовывает элементы при прокрутке пользователем;
* извлекает из внешнего API только тот срез записей, который соответствует текущей видимой области, не скачивая все данные из коллекции.

Содержимое элемента для компонента `Virtualize` может включать в себя следующее:

* обычный код HTML и код Razor, как показано в предыдущем примере;
* один или несколько компонентов Razor;
* сочетание компонентов HTML/Razor и Razor.

## <a name="item-provider-delegate"></a>Делегат поставщика элементов

Если вы не хотите загружать все элементы в память, можно указать метод делегата поставщика элементов для параметра <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> компонента, который асинхронно извлекает запрошенные элементы по запросу. В следующем примере метод `LoadEmployees` предоставляет элементы компоненту `Virtualize`.

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Поставщик элементов получает <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, который указывает необходимое количество элементов, начиная с заданного начального индекса. Затем поставщик элементов извлекает запрошенные элементы из базы данных или другой службы и возвращает их в виде <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> вместе с количеством всех элементов. Поставщик элементов может извлекать элементы с каждым запросом или кэшировать их, чтобы они были доступны.

Компонент `Virtualize` может принимать только **один источник элемента** из параметров, поэтому не пытайтесь одновременно использовать поставщик элементов и назначить коллекцию для `Items`. Если назначаются оба значения, создается <xref:System.InvalidOperationException>, когда параметры компонента задаются во время выполнения.

В следующем примере метода `LoadEmployees` демонстрируется загрузка сотрудников из `EmployeeService` (не показано).

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

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> указывает компоненту на необходимость повторного запроса данных из <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>. Это полезно в тех случаях, когда внешние данные изменяются. Вызывать этот метод при использовании <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> не требуется.

## <a name="placeholder"></a>Заполнитель

Поскольку запрос элементов из удаленного источника данных может занимать некоторое время, можно отрисовать заполнитель с содержимым элемента.

* Используйте <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) для отображения содержимого до тех пор, пока не будут доступны данные элемента.
* Чтобы задать шаблон элемента для списка, используйте <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType>.

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

Высоту каждого элемента в пикселях можно задать с помощью <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (по умолчанию: 50):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Количество элементов в нерабочей области

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> определяет количество дополнительных элементов, отрисовываемых до и после видимой области. Этот параметр позволяет уменьшить частоту отрисовки во время прокрутки. Однако более высокие значения приводят к отображению большего числа элементов на странице (по умолчанию: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Изменения состояния

При внесении изменений в элементы, отрисовываемые компонентом `Virtualize`, вызовите метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для принудительной оценки и отрисовки компонента. Для получения дополнительной информации см. <xref:blazor/components/rendering>.
