---
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
ms.openlocfilehash: 76dbf3cae1c264fa474101bc4398da28f45a1c10
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100254388"
---
Вложенные компоненты обычно привязываются к данным с помощью *цепочки привязки*, как описано в статье <xref:blazor/components/data-binding>. Вложенные и невложенные компоненты могут иметь общий доступ к данным с помощью зарегистрированного контейнера состояния в памяти. Пользовательский класс контейнера состояния может использовать назначаемое действие <xref:System.Action> для уведомления компонентов в других частях приложения об изменениях состояния. В следующем примере:

* Пара компонентов использует контейнер состояния для отслеживания свойства.
* Компоненты в примере являются вложенными, но для этого подхода вложение не является обязательным.

`StateContainer.cs`:

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

В `Program.Main` (Blazor WebAssembly):

```csharp
builder.Services.AddSingleton<StateContainer>();
```

В `Startup.ConfigureServices` (Blazor Server):

```csharp
services.AddSingleton<StateContainer>();
```

`Pages/Component1.razor`:

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1: {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

`Shared/Component2.razor`:

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2: {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

Предыдущие компоненты реализуют <xref:System.IDisposable>, а подписка на делегаты `OnChange` отменяется в методах `Dispose`, которые вызываются платформой при удалении компонентов. Для получения дополнительной информации см. <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.
