---
title: Сложные сценарии ASP.NET Core Blazor
author: guardrex
description: Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: ba2bf91f3318225383ec9d164c34be9124aa311b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280855"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>Сложные сценарии ASP.NET Core Blazor

## <a name="blazor-server-circuit-handler"></a>Обработчик цепи Blazor Server

Blazor Server позволяет коду определить *обработчик канала*, чтобы выполнять код при изменении состояния канала пользователя. Обработчик канала реализуется путем наследования от `CircuitHandler` и регистрации класса в контейнере службы приложения. В следующем примере обработчик канала отслеживает открытые соединения SignalR:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

Обработчики канала регистрируются с помощью DI. Экземпляры с заданной областью создаются для каждого экземпляра канала. С помощью `TrackingCircuitHandler` в предыдущем примере создается отдельная служба, поскольку необходимо отслеживать состояние всех каналов:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Если методы пользовательского обработчика канала создают необработанное исключение, это исключение является неустранимым для канала Blazor Server. Чтобы допускать исключения в коде обработчика или вызываемых методах, заключите код в один или несколько операторов [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.

Когда канал завершается из-за того, что пользователь отключился и платформа очищает состояние канала, платформа удаляет область DI канала. При удалении области удаляются все службы DI, ограниченные каналом и реализующие <xref:System.IDisposable?displayProperty=fullName>. Если любая служба DI вызывает необработанное исключение во время удаления, платформа регистрирует исключение.

## <a name="manual-rendertreebuilder-logic"></a>Выполняемая вручную логика RenderTreeBuilder

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> предоставляет методы для управления компонентами и элементами, включая создание компонентов вручную в коде C#.

> [!NOTE]
> Использование <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> для создания компонентов является сложным сценарием. Неправильно сформированный компонент (например, незакрытый тег разметки) может привести к неопределенному поведению.

Рассмотрим следующий компонент `PetDetails`, который можно вручную встроить в другой компонент:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

В следующем примере цикл в методе `CreateComponent` создает три компонента `PetDetails`. В методах <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> с порядковым номером порядковые номера представляют собой номера строк исходного кода. Алгоритм сравнения Blazor использует порядковые номера, соответствующие отдельным строкам кода, а не отдельным вызовам. При создании компонента с помощью методов <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> жестко задавайте аргументы для порядковых номеров. **Использование вычисления или счетчика для формирования порядкового номера может привести к снижению производительности.** Дополнительные сведения см. в разделе [Порядковые номера относятся к номерам строк кода, а не порядку выполнения](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).

Компонент `BuiltContent`:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Типы в <xref:Microsoft.AspNetCore.Components.RenderTree> позволяют обрабатывать *результаты* операций отрисовки. Это внутренние сведения реализации платформы Blazor. Эти типы следует считать *нестабильными*. Они могут измениться в будущих выпусках.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Порядковые номера относятся к номерам строк кода, а не порядку выполнения

Файлы компонентов Razor (`.razor`) всегда компилируются. Компиляция потенциально лучше, чем интерпретация кода, поскольку шаг компиляции можно использовать для вставки сведений, повышающих производительность приложения во время выполнения.

Ключевой пример этих усовершенствований связан с *порядковыми номерами*. Порядковые номера указывают среде выполнения, какие выходные данные поступили из отдельных и упорядоченных строк кода. Среда выполнения использует эти сведения для эффективного сравнения деревьев в линейном времени, а это гораздо быстрее, чем при использовании общего алгоритма сравнения деревьев.

Рассмотрим следующий файл компонента Razor (`.razor`):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Предыдущий код компилируется примерно следующим образом:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Когда код выполняется в первый раз, если `someFlag` имеет значение `true`, построитель получит следующее:

| Последовательность | Тип      | Данные   |
| :------: | --------- | :----: |
| 0        | Текстовый узел | First  |
| 1        | Текстовый узел | Second |

Представьте, что `someFlag` становится `false`, и разметка снова преобразуется для просмотра. На этот раз построитель получает:

| Последовательность | Тип       | Данные   |
| :------: | ---------- | :----: |
| 1        | Текстовый узел  | Second |

Когда среда выполнения выполняет сравнение, она видит, что элемент в последовательности `0` был удален, поэтому создает следующий тривиальный *сценарий изменения*:

* Удаление первого текстового узла.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Проблема с созданием порядковых номеров программным способом

Представьте себе, что вместо этого вы написали следующую логику отрисовки построителя деревьев:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

И теперь первыми выходными данными будет:

| Последовательность | Тип      | Данные   |
| :------: | --------- | :----: |
| 0        | Текстовый узел | First  |
| 1        | Текстовый узел | Second |

Этот результат идентичен предыдущему случаю, поэтому проблемы не возникают. `someFlag` имеет значение `false` во второй отрисовке, а выходные данные следующие:

| Последовательность | Тип      | Данные   |
| :------: | --------- | ------ |
| 0        | Текстовый узел | Second |

На этот раз алгоритм сравнения видит, что произошло *два* изменения, и создает следующий скрипт редактирования:

* Изменение значения первого текстового узла на `Second`.
* Удаление второго текстового узла.

При формировании порядковых номеров теряются все полезные сведения о том, где в исходном коде находятся циклы и ветви `if/else`. Это приводит к тому, что сравнение становится **в два раза больше**.

Это упрощенный пример. В более реалистичных случаях со сложными и глубокими вложенными структурами, особенно с циклами, затраты на производительность обычно выше. Вместо того чтобы сразу определять, какие блоки или ветви цикла были вставлены или удалены, алгоритм сравнения должен выполнять рекурсивный глубокий переход к деревьям отрисовки. В результате, как правило, приходится создавать более длинные скрипты изменений, поскольку алгоритм сравнения не знает всей информации о том, как старые и новые структуры связаны друг с другом.

### <a name="guidance-and-conclusions"></a>Рекомендации и выводы

* Производительность приложения снижается, если порядковые номера создаются динамически.
* Платформа не может автоматически создавать собственные порядковые номера во время выполнения, поскольку необходимая информация не существует, если только она не получена во время компиляции.
* Не записывайте длинные блоки логики <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, реализуемой вручную. Используйте файлы `.razor` и дайте компилятору обрабатывать порядковые номера. Если не удается избежать реализуемой вручную логики <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, разделите длинные блоки кода на более мелкие части, заключенные в вызовы <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A>. Каждый регион имеет собственное отдельное пространство порядковых номеров, поэтому вы можете снова начинать с нуля (или любого другого произвольного числа) внутри каждого региона.
* Если порядковые номера задаются жестко, то для алгоритма сравнения требуется только, чтобы порядковые номера увеличивались. Начальное значение и пропуски несущественны. Кроме того, можно использовать номер строки кода в качестве порядкового номера или начать с нуля и увеличивать значение на единицы или сотни (или выбрать любой другой интервал). 
* Blazor использует порядковые номера, а другие платформы пользовательского интерфейса для сравнения деревьев не используют их. Сравнение выполняется гораздо быстрее, если используются порядковые номера, и Blazor имеет преимущество этапа компиляции, который автоматически обрабатывает порядковые номера для разработчиков, создающих файлы `.razor`.
