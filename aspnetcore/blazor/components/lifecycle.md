---
title: Жизненный цикл ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать методы жизненного цикла компонента Razor в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 3591ba18351b89e2d5dfaef796777273c97ce98b
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751620"
---
# <a name="aspnet-core-blazor-lifecycle"></a>Жизненный цикл ASP.NET Core Blazor

Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Платформа Blazor содержит синхронные и асинхронные методы жизненного цикла. Переопределяйте методы жизненного цикла для выполнения дополнительных операций с компонентами во время инициализации и отрисовки компонента.

На приведенных ниже схемах показан жизненный цикл Blazor. Определение методов жизненного цикла с примерами приводится в следующих разделах этой статьи.

События жизненного цикла компонента

1. Если компонент отрисовывается в первый раз по запросу:
   * Создайте экземпляр компонента.
   * Выполните внедрение свойств. Выполните [`SetParametersAsync`](#before-parameters-are-set) .
   * Вызовите [`OnInitialized{Async}`](#component-initialization-methods). Если возвращается значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается. Если значение <xref:System.Threading.Tasks.Task> не возвращается, компонент отрисовывается сразу.
1. Вызовите метод [`OnParametersSet{Async}`](#after-parameters-are-set) и выполните отрисовку компонента. Если метод `OnParametersSetAsync` возвращает значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.

![События жизненного цикла компонента Razor в Blazor](lifecycle/_static/lifecycle1.png)

Обработка событий модели DOM

1. Выполняется обработчик событий.
1. Если возвращается значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается. Если значение <xref:System.Threading.Tasks.Task> не возвращается, компонент отрисовывается сразу.

![Обработка событий модели DOM](lifecycle/_static/lifecycle2.png)

Жизненный цикл `Render`

1. Прекращает последующие операции отрисовки для компонента:
   * После первой отрисовки.
   * Если [`ShouldRender`](#suppress-ui-refreshing) имеет значение `false`.
1. Выполните сборку отличий дерева отрисовки и отрисуйте компонент.
1. Подождите, пока модель DOM обновится.
1. Вызовите [`OnAfterRender{Async}`](#after-component-render).

![Жизненный цикл процесса отрисовки](lifecycle/_static/lifecycle3.png)

Вызовы [`StateHasChanged`](#state-changes) разработчиком приводят к отрисовке. Для получения дополнительной информации см. <xref:blazor/components/rendering>.

## <a name="lifecycle-methods"></a>Методы жизненного цикла

### <a name="before-parameters-are-set"></a>До указания параметров

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> задает параметры, предоставляемые родительским элементом компонента в дереве отрисовки или из параметров маршрута. Переопределяя метод, код разработчика может напрямую взаимодействовать с параметрами <xref:Microsoft.AspNetCore.Components.ParameterView>.

В следующем примере <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> присваивает `value` значение параметра`Param`, если анализ параметра маршрута для `Param` выполнен успешно. Если `value` не равно `null`, значение отображается компонентом `SetParametersAsyncExample`.

`Pages/SetParametersAsyncExample.razor`:

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            message = $"The value of 'Param' is {value}.";
        }
        else 
        {
            message = "The value of 'Param' is null.";
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> содержит набор значений параметров для компонента при каждом вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.

Реализация <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> по умолчанию задает значение каждого свойства с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) или [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), имеющим соответствующее значение в <xref:Microsoft.AspNetCore.Components.ParameterView>. Параметры, у которых нет соответствующего значения в <xref:Microsoft.AspNetCore.Components.ParameterView>, остаются неизменными.

Если [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) не вызывается, пользовательский код может интерпретировать значение входящих параметров любым необходимым образом. Например, нет необходимости назначать входящие параметры свойствам класса.

Если настроены какие-либо обработчики событий, отсоедините их при удалении. Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).

### <a name="component-initialization-methods"></a>Методы инициализации компонента

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> вызываются, когда компонент инициализируется после получения начальных параметров от родительского компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>. 

Используйте <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, когда компонент выполняет асинхронную операцию и должен обновляться после завершения операции.

Для синхронной операции переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Чтобы выполнить асинхронную операцию, переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и используйте в операции оператор [`await`](/dotnet/csharp/language-reference/operators/await).

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Приложения Blazor Server, которые [предварительно отрисовывают свое содержимое](xref:blazor/fundamentals/additional-scenarios#render-mode), вызывают <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *дважды*:

* Один раз, когда компонент изначально отрисовывается статически как часть страницы.
* Второй раз, когда браузер устанавливает соединение с сервером.

Чтобы предотвратить выполнение кода разработчика в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> дважды, см. раздел [Повторное подключение с отслеживанием состояния после предварительной отрисовки](#stateful-reconnection-after-prerendering).

Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов в JavaScript, невозможны, так как подключение к браузеру не установлено. При предварительной отрисовке компоненты могут отрисовываться иначе. Дополнительные сведения см. в разделе [Обнаружение предварительной отрисовки в приложении](#detect-when-the-app-is-prerendering).

Если настроены какие-либо обработчики событий, отсоедините их при удалении. Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).

### <a name="after-parameters-are-set"></a>После указания параметров

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> вызываются:

* После инициализации компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.
* Когда родительский компонент повторно отрисовывается и предоставляет:
  * Только известные примитивные неизменяемые типы, у которых изменился по крайней мере один параметр.
  * Любые параметры сложных типов. Платформа не может определить, изменились ли значения параметров сложного типа внутри, поэтому она рассматривает набор параметров как измененный.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Асинхронная работа при применении параметров и значений свойств должна происходить во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Если настроены какие-либо обработчики событий, отсоедините их при удалении. Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).

### <a name="after-component-render"></a>После отрисовки компонента

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> вызываются после завершения отрисовки компонента. В этот момент указываются ссылки на элементы и компоненты. Используйте этот этап для выполнения дополнительных шагов инициализации с помощью отрисованного содержимого, например для активации сторонних библиотек JavaScript, которые работают с отрисованными элементами DOM.

Параметр `firstRender` для <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:

* Устанавливается в значение `true` при первой отрисовке экземпляра компонента.
* Может использоваться, чтобы гарантировать однократное выполнение инициализации.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> Асинхронная работа сразу после отрисовки должна произойти во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.
>
> Даже если вы возвращаете <xref:System.Threading.Tasks.Task> из <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, платформа не планирует дальнейший цикл отрисовки компонента после завершения задачи. Это позволяет избежать бесконечного цикла отрисовки. Это поведение отличается от других методов жизненного цикла, которые планируют дальнейший цикл отрисовки после завершения возвращаемой задачи.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *не вызываются при предварительной отрисовке на сервере*. Методы вызываются, когда компонент отрисовывается в интерактивном режиме после завершения предварительной отрисовки. При предварительной отрисовке приложения происходит следующее.

1. Компонент выполняется на сервере для создания статической HTML-разметки в HTTP-ответе. На этом этапе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> не вызываются.
1. При запуске `blazor.server.js` или `blazor.webassembly.js` в браузере компонент перезапускается в режиме интерактивной отрисовки. После перезапуска компонента **вызываются** методы <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, так как приложение больше не находится на этапе предварительной отрисовки.

Если настроены какие-либо обработчики событий, отсоедините их при удалении. Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).

### <a name="suppress-ui-refreshing"></a>Подавление обновления пользовательского интерфейса

Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы отключить обновление пользовательского интерфейса. Если реализация возвращает `true`, пользовательский интерфейс обновляется:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> вызывается каждый раз при отрисовке компонента.

Даже при переопределении <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> компонент всегда проходит первоначальную отрисовку.

Для получения дополнительной информации см. <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.

## <a name="state-changes"></a>Изменения состояния

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> уведомляет компонент о том, что его состояние изменилось. В соответствующих случаях вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> приводит к повторной отрисовке компонента.

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для методов <xref:Microsoft.AspNetCore.Components.EventCallback>. Для получения дополнительной информации см. <xref:blazor/components/event-handling#eventcallback>.

Для получения дополнительной информации см. <xref:blazor/components/rendering>.

## <a name="handle-incomplete-async-actions-at-render"></a>Обработка незавершенных асинхронных действий при отрисовке

Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до отрисовки компонента. Во время выполнения метода жизненного цикла объекты могут быть `null` или заполнены данными не полностью. Предоставьте логику отрисовки для подтверждения инициализации объектов. Отрисуйте элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты имеют значение `null`.

В компоненте `FetchData` шаблонов Blazor <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> переопределяется для асинхронного получения данных прогноза (`forecasts`). Если `forecasts` имеет значение `null`, пользователю выводится сообщение о загрузке. Когда элемент `Task`, возвращенный <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, завершается, компонент отрисовывается повторно с обновленным состоянием.

`Pages/FetchData.razor` в шаблоне Blazor Server:

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a>Обработка ошибок

Сведения об обработке ошибок во время выполнения метода жизненного цикла см. в разделе <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.

## <a name="stateful-reconnection-after-prerendering"></a>Повторное подключение с отслеживанием состояния после предварительной отрисовки

В приложении Blazor Server, если <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> имеет значение <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, компонент изначально отрисовывается статически как часть страницы. После того как браузер установит соединение с сервером, компонент отрисовывается *снова* и становится интерактивным. Если метод жизненного цикла [`OnInitialized{Async}`](#component-initialization-methods) для инициализации компонента присутствует, он выполняется *дважды*:

* Когда компонент предварительно отрисовывается статически.
* После установления соединения с сервером.

Это может привести к заметному изменению данных, отображаемых в пользовательском интерфейсе, когда компонент отрисовывается окончательно.

Чтобы избежать сценария двойной отрисовки в приложении Blazor Server, выполните следующие действия:

* Передайте идентификатор, который можно использовать для кэширования состояния во время предварительной отрисовки и получения состояния после перезапуска приложения.
* Используйте идентификатор во время предварительной отрисовки, чтобы сохранить состояние компонента.
* Используйте идентификатор после предварительной отрисовки, чтобы получить кэшированное состояние.

В следующем коде показан обновленный элемент `WeatherForecastService` в приложении Blazor Server на основе шаблона без двойной отрисовки:

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

Дополнительные сведения об элементе <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> см. в разделе <xref:blazor/fundamentals/additional-scenarios#render-mode>.

## <a name="detect-when-the-app-is-prerendering"></a>Обнаружение предварительной отрисовки приложения

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a>Удаление компонента с использованием `IDisposable`

Если компонент реализует <xref:System.IDisposable>, платформа вызывает [метод удаления](/dotnet/standard/garbage-collection/implementing-dispose) при удалении компонента из пользовательского интерфейса, и вы можете освободить неуправляемые ресурсы. Удаление можно выполнить в любое время, в том числе при [инициализации компонента](#component-initialization-methods). Следующий компонент реализует <xref:System.IDisposable> с использованием директивы Razor [`@implements`](xref:mvc/views/razor#implements):

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

Для задач асинхронной реализации используйте `DisposeAsync` вместо `Dispose` в предыдущем примере:

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> Вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> в `Dispose` не поддерживается. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> может вызываться в процессе уничтожения отрисовщика, поэтому запрос обновлений пользовательского интерфейса на этом этапе не поддерживается.

Отмена подписки на обработчики событий из событий .NET. В следующих примерах [формы Blazor](xref:blazor/forms-validation) показано, как отсоединить обработчик событий в методе `Dispose`.

* Подход с использованием частного поля и лямбда-выражения

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* Подход с использованием частного метода

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]
  
Дополнительные сведения см. в статье [Очистка неуправляемых ресурсов](/dotnet/standard/garbage-collection/unmanaged) и следующих разделах, в которых описана реализация методов `Dispose` и `DisposeAsync`.

## <a name="cancelable-background-work"></a>Отменяемая фоновая операция

Компоненты часто выполняют длительные фоновые операции, например осуществление сетевых вызовов (<xref:System.Net.Http.HttpClient>) и взаимодействие с базами данных. В целях экономии системных ресурсов в ряде ситуаций желательно отключить выполнение фоновых операций. Например, фоновые асинхронные операции не останавливаются автоматически, когда пользователь выходит из компонента.

Ниже перечислены другие причины, по которым может потребоваться отмена фоновых рабочих элементов.

* Выполнение фоновой задачи было начато с ошибочными входными данными или параметрами обработки.
* Текущий набор выполняемых фоновых рабочих элементов должен быть заменен новым набором.
* Необходимо изменить приоритет выполняемых в данный момент задач.
* Необходимо завершить работу приложения, чтобы повторно развернуть его на сервере.
* Ресурсы сервера становятся ограниченными, в связи с чем возникает необходимость в пересмотре графика выполнения фоновых рабочих элементов.

Чтобы реализовать механизм отменяемой фоновой операции в компоненте, выполните следующие действия.

* Используйте <xref:System.Threading.CancellationTokenSource> и <xref:System.Threading.CancellationToken>.
* При [удалении компонента](#component-disposal-with-idisposable) и в любой момент, когда требуется выполнить отмену путем отмены токена вручную, вызовите [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A), чтобы сообщить о необходимости отмены фоновой операции.
* После возврата асинхронного вызова вызовите <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> для токена.

В следующем примере:

* `await Task.Delay(5000, cts.Token);` представляет длительную асинхронную фоновую операцию.
* `BackgroundResourceMethod` представляет длительный фоновый метод, который не должен запускаться, если `Resource` удаляется перед вызовом метода.

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```

## <a name="blazor-server-reconnection-events"></a>События повторного подключения Blazor Server

События жизненного цикла компонента, описываемые в этой статье, работают отдельно от [обработчиков событий повторного подключения Blazor Server](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui). Когда приложение Blazor Server теряет подключение SignalR к клиенту, прерываются только операции обновления пользовательского интерфейса. Они возобновляются при восстановлении подключения. Дополнительные сведения о конфигурации и событиях обработчика канала см. в статье <xref:blazor/fundamentals/additional-scenarios>.
