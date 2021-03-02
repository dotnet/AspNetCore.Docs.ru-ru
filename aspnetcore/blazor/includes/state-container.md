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
<span data-ttu-id="6accb-101">Вложенные компоненты обычно привязываются к данным с помощью *цепочки привязки*, как описано в статье <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="6accb-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="6accb-102">Вложенные и невложенные компоненты могут иметь общий доступ к данным с помощью зарегистрированного контейнера состояния в памяти.</span><span class="sxs-lookup"><span data-stu-id="6accb-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="6accb-103">Пользовательский класс контейнера состояния может использовать назначаемое действие <xref:System.Action> для уведомления компонентов в других частях приложения об изменениях состояния.</span><span class="sxs-lookup"><span data-stu-id="6accb-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="6accb-104">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6accb-104">In the following example:</span></span>

* <span data-ttu-id="6accb-105">Пара компонентов использует контейнер состояния для отслеживания свойства.</span><span class="sxs-lookup"><span data-stu-id="6accb-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="6accb-106">Компоненты в примере являются вложенными, но для этого подхода вложение не является обязательным.</span><span class="sxs-lookup"><span data-stu-id="6accb-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="6accb-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="6accb-107">`StateContainer.cs`:</span></span>

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

<span data-ttu-id="6accb-108">В `Program.Main` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="6accb-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="6accb-109">В `Startup.ConfigureServices` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="6accb-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="6accb-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="6accb-110">`Pages/Component1.razor`:</span></span>

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

<span data-ttu-id="6accb-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="6accb-111">`Shared/Component2.razor`:</span></span>

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

<span data-ttu-id="6accb-112">Предыдущие компоненты реализуют <xref:System.IDisposable>, а подписка на делегаты `OnChange` отменяется в методах `Dispose`, которые вызываются платформой при удалении компонентов.</span><span class="sxs-lookup"><span data-stu-id="6accb-112">The preceding components implement <xref:System.IDisposable>, and the `OnChange` delegates are unsubscribed in the `Dispose` methods, which are called by the framework when the components are disposed.</span></span> <span data-ttu-id="6accb-113">Для получения дополнительной информации см. <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="6accb-113">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
