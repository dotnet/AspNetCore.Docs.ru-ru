---
title: Отрисовка компонента Blazor ASP.NET Core
author: guardrex
description: Сведения об отрисовке компонента Razor в приложениях ASP.NET Core Blazor, в том числе о моменте, когда необходимо вызывать метод StateHasChanged.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: 1a4d4116b8a6d9266bbacbbdd8f20dc49b4e1db0
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253861"
---
# <a name="aspnet-core-no-locblazor-component-rendering"></a>Отрисовка компонента Blazor ASP.NET Core

Автор: [Стив Сандерсон](https://github.com/SteveSandersonMS) (Steve Sanderson)

Компоненты *должны* отрисовываться при первом добавлении в иерархию компонентов их родительским компонентом. Это единственный случай, когда отрисовка компонента строго обязательна.

Компоненты, *могут* отрисовываться в любое другое время в соответствии с собственной логикой и соглашениями.

## <a name="conventions-for-componentbase"></a>Соглашения для `ComponentBase`

По умолчанию компоненты Razor (`.razor`) наследуются от базового класса <xref:Microsoft.AspNetCore.Components.ComponentBase>, который содержит логику, вызывающую повторную отрисовку в следующие моменты времени:

* После применения обновленного набора параметров из родительского компонента.
* После применения обновленного значения для каскадного параметра.
* После уведомления о событии и вызова одного из его собственных обработчиков событий.
* После вызова его собственного метода <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.

Компоненты, унаследованные от <xref:Microsoft.AspNetCore.Components.ComponentBase> пропускают повторную отрисовку при обновлении параметров в том случае, если выполняется одно из следующих условий:

* Все значения параметров имеют известные неизменяемые примитивные типы (например, `int`, `string`, `DateTime`) и не изменялись с момента установки предыдущего набора параметров.
* Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> компонента возвращает `false`.

Дополнительные сведения о методе <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> см. в разделе <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.

## <a name="control-the-rendering-flow"></a>Управление потоком отрисовки

В большинстве случаев соглашения для <xref:Microsoft.AspNetCore.Components.ComponentBase> определяют корректное подмножество повторных отрисовок компонента после наступления события. Разработчикам как правило не требуется предоставлять вручную логику, указывающую платформе, какие компоненты и когда следует повторно отрисовывать. В целом, соглашения для платформы определяют, что получающий событие компонент повторно отрисовывает себя. В этом случае инициируется рекурсивная повторная отрисовка компонентов-потомков, значения параметров которых могли измениться.

Дополнительные сведения о том, как соглашения для платформы влияют на производительность и как оптимизировать иерархию компонентов приложения, см. в разделе <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.

## <a name="when-to-call-statehaschanged"></a>Когда следует вызывать метод `StateHasChanged`

Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> позволяет в любое время запускать отрисовку. Тем не менее, метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> не следует вызывать без необходимости. Это распространенная ошибка, которая влечет за собой ненужные затраты ресурсов на отрисовку.

Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> *не требуется* вызывать в следующих случаях:

* Стандартная синхронная или асинхронная обработка событий, поскольку <xref:Microsoft.AspNetCore.Components.ComponentBase> запускает отрисовку для большинства стандартных обработчиков событий.
* Реализация типовой синхронной или асинхронной логики жизненного цикла, например [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) или [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), поскольку <xref:Microsoft.AspNetCore.Components.ComponentBase> запускает отрисовку для типовых событий жизненного цикла.

Тем не менее, вызов метода <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> может требоваться в случаях, которые описываются в следующих разделах:

* [Использование нескольких асинхронных стадий в асинхронном обработчике](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [Получение вызова извне к системе обработки событий Blazor](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [Отрисовка компонента за пределами поддерева, которое повторно отрисовывается в связи с определенным событием](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>Использование нескольких асинхронных стадий в асинхронном обработчике

Рассмотрим следующий компонент `Counter`, который обновляет счетчик четыре раза для каждого щелчка.

`Pages/Counter.razor`:

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

Из-за особенностей определения задач в .NET получатель <xref:System.Threading.Tasks.Task> может наблюдать только за его окончательным завершением, а не за промежуточными асинхронными состояниями. Таким образом, <xref:Microsoft.AspNetCore.Components.ComponentBase> может активировать повторную отрисовку только при первом возвращении <xref:System.Threading.Tasks.Task> и при завершении <xref:System.Threading.Tasks.Task>. Информацию о необходимости повторной отрисовки в промежуточных точках он не получает. Соответственно, если требуется повторная отрисовка в промежуточных точках, следует использовать <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a>Получение вызова извне к системе обработки событий Blazor

<xref:Microsoft.AspNetCore.Components.ComponentBase> имеет сведения только о собственных методах жизненного цикла и событиях, вызываемых Blazor. Соответственно <xref:Microsoft.AspNetCore.Components.ComponentBase> не располагает информацией о других событиях, которые могут возникать в вашем коде. Например, Blazor не имеет сведений о любых событиях C#, вызываемых пользовательским хранилищем данных. Чтобы такие события вызывали повторную отрисовку для отображения обновленных значений в пользовательском интерфейсе, используйте метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.

Рассмотрим также следующий компонент `Counter`, который использует <xref:System.Timers.Timer?displayProperty=fullName> для обновления счетчика с установленным интервалом и вызывает метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для обновления пользовательского интерфейса.

`Pages/Counter.razor`:

```razor
@page "/counter"
@using System.Timers
@implements IDisposable

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    void IDisposable.Dispose() => timer.Dispose();
}
```

В предыдущем примере `OnTimerCallback` должен вызвать метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, поскольку Blazor не знает об изменениях `currentCount` в обратном вызове. `OnTimerCallback` выполняется за пределами управляемого потока отрисовки или уведомления о событии Blazor.

Аналогичным образом, поскольку обратный вызов выполняется вне контекста синхронизации Blazor, необходимо упаковать логику в <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType>, чтобы переместить ее в контекст синхронизации модуля отрисовки. Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> может вызываться только из контекста синхронизации модуля отрисовки и в противном случае приводит к возникновению исключения. Это поведение эквивалентно маршалингу потока пользовательского интерфейса на других платформах пользовательского интерфейса.

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>Отрисовка компонента за пределами поддерева, которое повторно отрисовывается в связи с определенным событием

В пользовательском интерфейсе возможны отправка события в один компонент, изменение некоторого состояния и возникновение необходимости в повторной отрисовке совершенно другого компонента, который не является потомком компонента, получающего событие.

Одним из способов работы в таких сценариях является *управление состоянием*, например с использованием службы DI, внедренной в несколько компонентов. Когда один компонент вызывает метод в диспетчере состояний, диспетчер состояний может вызвать событие C#, которое затем получается независимым компонентом.

Поскольку эти события C# выходят за пределы конвейера отрисовки Blazor, следует вызвать метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для других компонентов, которые необходимо обработать в ответ на события диспетчера состояний.

Это аналогично приведенному ранее варианту с <xref:System.Timers.Timer?displayProperty=fullName> в [предыдущем разделе](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system). Поскольку стек вызовов выполнения как правило остается в контексте синхронизации модуля отрисовки, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> обычно не требуется. <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> требуется только в том случае, если логика выходит из контекста синхронизации, например в результате вызова <xref:System.Threading.Tasks.Task.ContinueWith%2A> для <xref:System.Threading.Tasks.Task> или ожидания <xref:System.Threading.Tasks.Task> с [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).
