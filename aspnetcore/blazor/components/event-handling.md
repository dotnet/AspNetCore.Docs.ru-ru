---
title: Обработка событий Blazor в ASP.NET Core
author: guardrex
description: Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/event-handling
ms.openlocfilehash: f6a93eb9d95182d29a60cc1a5c48122b9166aa84
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280152"
---
# <a name="aspnet-core-blazor-event-handling"></a>Обработка событий Blazor в ASP.NET Core

Компоненты Razor предоставляют функции обработки событий. Для атрибута HTML-элемента с именем [`@on{EVENT}`](xref:mvc/views/razor#onevent) (например, `@onclick`) и значением типа делегата компонент Razor рассматривает значение атрибута как обработчик событий.

Следующий код вызывает метод `UpdateHeading`, когда в пользовательском интерфейсе выбрана кнопка:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Следующий код вызывает метод `CheckChanged`, когда в пользовательском интерфейсе установлен флажок:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Обработчики событий также могут быть асинхронными и возвращать <xref:System.Threading.Tasks.Task>. Нет необходимости вручную вызывать [StateHasChanged](xref:blazor/components/lifecycle#state-changes). Исключения регистрируются при их возникновении.

В следующем примере `UpdateHeading` вызывается асинхронно при выборе кнопки:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a>Типы аргументов событий

Для некоторых событий разрешены типы аргументов событий. Указание параметра события в определении метода события является необязательным и требуется только в том случае, если тип события используется в методе. В следующем примере аргумент события `MouseEventArgs` используется в методе `ShowMessage` для задания текста сообщения:

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

Поддерживаемые параметры <xref:System.EventArgs> приведены в следующей таблице.

::: moniker range=">= aspnetcore-5.0"

| Событие            | Класс  | События DOM и примечания |
| ---------------- | ------ | -------------------- |
| Буфер обмена        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| Перетаскивание             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> и <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> содержат данные перетаскиваемого элемента.<br><br>Реализуйте перетаскивание в приложениях Blazor с помощью [взаимодействия JS](xref:blazor/call-javascript-from-dotnet) с [API перетаскивания для HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API). |
| Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| Событие            | <xref:System.EventArgs> | *Общие сведения*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Буфер обмена*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Ввод*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Носитель*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> содержит атрибуты для настройки сопоставлений между именами событий и типами аргументов событий. |
| Фокус            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Не включает поддержку `relatedTarget`. |
| Входные данные            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| Клавиатура         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| Мышь            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Указатель мыши    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Колесико мыши      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| Ход выполнения         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Сенсорные технологии            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> представляет одну точку касания на устройстве с сенсорным вводом. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| Событие            | Класс | События DOM и примечания |
| ---------------- | ----- | -------------------- |
| Буфер обмена        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| Перетаскивание             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> и <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> содержат данные перетаскиваемого элемента.<br><br>Реализуйте перетаскивание в приложениях Blazor с помощью [взаимодействия JS](xref:blazor/call-javascript-from-dotnet) с [API перетаскивания для HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API). |
| Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| Событие            | <xref:System.EventArgs> | *Общие сведения*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Буфер обмена*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Ввод*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Носитель*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> содержит атрибуты для настройки сопоставлений между именами событий и типами аргументов событий. |
| Фокус            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Не включает поддержку `relatedTarget`. |
| Входные данные            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| Клавиатура         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| Мышь            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Указатель мыши    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Колесико мыши      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| Ход выполнения         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Сенсорные технологии            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> представляет одну точку касания на устройстве с сенсорным вводом. |

::: moniker-end

Дополнительные сведения см. в следующих ресурсах:

* [Классы `EventArgs` в источнике ссылки на ASP.NET Core (ветвь DotNet/aspnetcore `master`)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web). Ветвь `master` представляет разрабатываемый API для *следующего* выпуска ASP.NET Core. В текущем выпуске выберите соответствующую ветвь репозитория GitHub (например, `release/3.1`).
* [Веб-документы MDN: GlobalEventHandlers.](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) Содержит сведения о том, какие элементы HTML поддерживают каждое из событий DOM.

## <a name="lambda-expressions"></a>Лямбда-выражения

Также можно использовать [лямбда-выражения](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Часто бывает удобно закрывать дополнительные значения, например при переборе набора элементов. В следующем примере создаются три кнопки, каждая из которых вызывает `UpdateHeading` для передачи аргумента события (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) и номера кнопки (`buttonNumber`) при выборе в пользовательском интерфейсе:

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> **Не** используйте переменную цикла непосредственно в лямбда-выражении, например `i` в предыдущем примере цикла `for`. В противном случае одна и та же переменная будет использоваться во всех лямбда-выражениях, в результате чего значение будет одинаковым во всех лямбда-выражениях. Всегда записывайте значение переменной в локальную переменную, а затем используйте ее. В предыдущем примере переменная цикла `i` назначается `buttonNumber`.

## <a name="eventcallback"></a>EventCallback

Распространенным сценарием с вложенными компонентами является запуск метода родительского компонента при возникновении события дочернего компонента, например, когда в дочернем элементе возникает событие `onclick`. Чтобы обеспечить доступ к событиям в компонентах, используйте <xref:Microsoft.AspNetCore.Components.EventCallback>. Родительский компонент может назначить метод обратного вызова <xref:Microsoft.AspNetCore.Components.EventCallback> дочернего компонента.

В `ChildComponent` в примере приложения (`Components/ChildComponent.razor`) показано, как обработчик `onclick` кнопки настроен на получение делегата <xref:Microsoft.AspNetCore.Components.EventCallback> из `ParentComponent` образца. <xref:Microsoft.AspNetCore.Components.EventCallback> вводится с `MouseEventArgs`, что подходит для события `onclick` от периферийного устройства:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` задает для <xref:Microsoft.AspNetCore.Components.EventCallback%601> дочернего компонента (`OnClickCallback`) его метод `ShowMessage`.

`Pages/ParentComponent.razor`:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

При выборе кнопки в `ChildComponent`:

* Вызывается метод `ShowMessage` `ParentComponent`. `messageText` обновляется и отображается в `ParentComponent`.
* Вызов [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) не требуется в методе обратного вызова (`ShowMessage`). <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для повторной отрисовки `ParentComponent`, так же как и дочерние события запускают повторную отрисовку компонента в обработчиках событий, которые выполняются в дочернем элементе. Для получения дополнительной информации см. <xref:blazor/components/rendering>.

<xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> разрешают выполнять асинхронные делегаты. Структура <xref:Microsoft.AspNetCore.Components.EventCallback> слабо типизирована и позволяет передавать любой аргумент типа в `InvokeAsync(Object)`. Структура <xref:Microsoft.AspNetCore.Components.EventCallback%601> сильно типизирована и требует передачи аргумента `T` в `InvokeAsync(T)`, который можно назначить `TValue`.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Вызов <xref:Microsoft.AspNetCore.Components.EventCallback> или <xref:Microsoft.AspNetCore.Components.EventCallback%601> с <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> и ожидание <xref:System.Threading.Tasks.Task>:

```csharp
await OnClickCallback.InvokeAsync(arg);
```

Используйте <xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> для обработки событий и параметров компонента привязки.

Предпочтительнее использовать строго типизированный <xref:Microsoft.AspNetCore.Components.EventCallback%601> вместо <xref:Microsoft.AspNetCore.Components.EventCallback>. <xref:Microsoft.AspNetCore.Components.EventCallback%601> обеспечивает более эффективное реагирование на ошибки для пользователей компонента. Как и в случае с другими обработчиками событий пользовательского интерфейса, указание параметра события является необязательным. Используйте <xref:Microsoft.AspNetCore.Components.EventCallback>, если в обратный вызов не было передано значение.

## <a name="prevent-default-actions"></a>Запрет действий по умолчанию

Используйте атрибут директивы [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), чтобы запретить выполнение действия по умолчанию для события.

Если на устройстве ввода выбран ключ, а фокус находится на текстовом поле, то в браузере в текстовом поле обычно отображается символ ключа. В следующем примере поведение по умолчанию запрещено путем указания атрибута директивы `@onkeypress:preventDefault`. Счетчик увеличивается, а ключ **+** не записывается в значение элемента `<input>`:

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

Указание атрибута `@on{EVENT}:preventDefault` без значения эквивалентно `@on{EVENT}:preventDefault="true"`.

Значение атрибута также может быть выражением. В следующем примере `shouldPreventDefault` является полем `bool`, для которого задано значение `true` или `false`:

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a>Остановка распространения событий

Используйте атрибут директивы [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), чтобы остановить распространение событий.

В следующем примере при установке флажка события щелчка мышью из второго дочернего элемента `<div>` перестают распространяться в родительский элемент `<div>`:

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a>Установка фокуса на элемент

Вызовите `FocusAsync` на [ссылке на элемент](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), чтобы установить фокус на элементе в коде:

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
