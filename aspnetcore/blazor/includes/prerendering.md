Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов в JavaScript, невозможны, так как подключение к браузеру не установлено. При предварительной отрисовке компоненты могут отрисовываться иначе.

Чтобы отложить вызовы взаимодействия с JavaScript до установки подключения к браузеру, можно использовать [событие жизненного цикла компонента OnAfterRenderAsync](xref:blazor/components/lifecycle#after-component-render). Это событие вызывается только после полной отрисовки приложения и установки клиентского подключения.

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

Для предыдущего примера кода предоставьте функцию JavaScript `setElementText` внутри элемента `<head>` файла `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server). Функция вызывается с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> и не возвращает значение:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> В предыдущем примере прямое изменение модели DOM показано только в демонстрационных целях. В большинстве сценариев выполнять непосредственное изменение модели DOM с помощью JavaScript не рекомендуется, поскольку JavaScript может повлиять на отслеживание изменений в Blazor.

В следующем компоненте показано, как использовать взаимодействие с JavaScript в составе логики инициализации компонента, совместимое с предварительной отрисовкой. Компонент показывает, что обновление отрисовки можно активировать из <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>. В этом сценарии разработчику следует избегать создания бесконечного цикла.

При вызове <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> `ElementRef` используется только в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, а не в предыдущем методе жизненного цикла, так как элемент JavaScript появляется только после отрисовки компонента.

Метод [StateHasChanged](xref:blazor/components/lifecycle#state-changes) вызывается для повторной отрисовки компонента с новым состоянием, полученным из вызова взаимодействия с JavaScript (дополнительные сведения см. в разделе <xref:blazor/components/rendering>). Код не создает бесконечный цикл, так как метод `StateHasChanged` вызывается, только если `infoFromJs` имеет значение `null`.

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

Для предыдущего примера кода предоставьте функцию JavaScript `setElementText` внутри элемента `<head>` файла `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server). Функция вызывается с помощью метода <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> и возвращает значение:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> В предыдущем примере прямое изменение модели DOM показано только в демонстрационных целях. В большинстве сценариев выполнять непосредственное изменение модели DOM с помощью JavaScript не рекомендуется, поскольку JavaScript может повлиять на отслеживание изменений в Blazor.
