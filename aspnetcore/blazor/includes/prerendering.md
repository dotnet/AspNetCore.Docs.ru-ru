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
ms.openlocfilehash: c152524e0acd3803bd3b8078f667cce01180e25d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551900"
---
<span data-ttu-id="eaff5-101">Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов в JavaScript, невозможны, так как подключение к браузеру не установлено.</span><span class="sxs-lookup"><span data-stu-id="eaff5-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="eaff5-102">При предварительной отрисовке компоненты могут отрисовываться иначе.</span><span class="sxs-lookup"><span data-stu-id="eaff5-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="eaff5-103">Чтобы отложить вызовы взаимодействия с JavaScript до установки подключения к браузеру, можно использовать [событие жизненного цикла компонента OnAfterRenderAsync](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="eaff5-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="eaff5-104">Это событие вызывается только после полной отрисовки приложения и установки клиентского подключения.</span><span class="sxs-lookup"><span data-stu-id="eaff5-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="eaff5-105">Для предыдущего примера кода укажите функцию JavaScript `setElementText` в элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="eaff5-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="eaff5-106">Функция вызывается с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> и не возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="eaff5-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="eaff5-107">В предыдущем примере прямое изменение модели DOM показано только в демонстрационных целях.</span><span class="sxs-lookup"><span data-stu-id="eaff5-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="eaff5-108">В большинстве сценариев выполнять непосредственное изменение модели DOM с помощью JavaScript не рекомендуется, поскольку JavaScript может повлиять на отслеживание изменений Blazor.</span><span class="sxs-lookup"><span data-stu-id="eaff5-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="eaff5-109">В следующем компоненте показано, как использовать взаимодействие с JavaScript в составе логики инициализации компонента, совместимое с предварительной отрисовкой.</span><span class="sxs-lookup"><span data-stu-id="eaff5-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="eaff5-110">Компонент показывает, что обновление отрисовки можно активировать из <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="eaff5-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="eaff5-111">В этом сценарии разработчику следует избегать создания бесконечного цикла.</span><span class="sxs-lookup"><span data-stu-id="eaff5-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="eaff5-112">При вызове <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> `ElementRef` используется только в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, а не в предыдущем методе жизненного цикла, так как элемент JavaScript появляется только после отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="eaff5-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="eaff5-113">Метод [StateHasChanged](xref:blazor/components/lifecycle#state-changes) вызывается для повторной отрисовки компонента с новым состоянием, полученным из вызова взаимодействия с JavaScript (дополнительные сведения см. в разделе <xref:blazor/components/rendering>).</span><span class="sxs-lookup"><span data-stu-id="eaff5-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call (for more information, see <xref:blazor/components/rendering>).</span></span> <span data-ttu-id="eaff5-114">Код не создает бесконечный цикл, так как метод `StateHasChanged` вызывается, только если `infoFromJs` имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="eaff5-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="eaff5-115">Для предыдущего примера кода укажите функцию JavaScript `setElementText` в элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="eaff5-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="eaff5-116">Функция вызывается с помощью метода <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> и возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="eaff5-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="eaff5-117">В предыдущем примере прямое изменение модели DOM показано только в демонстрационных целях.</span><span class="sxs-lookup"><span data-stu-id="eaff5-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="eaff5-118">В большинстве сценариев выполнять непосредственное изменение модели DOM с помощью JavaScript не рекомендуется, поскольку JavaScript может повлиять на отслеживание изменений Blazor.</span><span class="sxs-lookup"><span data-stu-id="eaff5-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
