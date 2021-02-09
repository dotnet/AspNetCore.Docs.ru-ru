---
title: Привязка к данным в ASP.NET Core Blazor
author: guardrex
description: Сведения о функциях привязки данных для компонентов и элементов модели DOM в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 67a63f1b4f705a4857dea2e6d1a942d4f21469f5
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057100"
---
# <a name="aspnet-core-blazor-data-binding"></a>Привязка к данным в ASP.NET Core Blazor

Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Стив Сандерсон](https://github.com/SteveSandersonMS) (Steve Sanderson)

Компоненты Razor реализуют функции привязки данных для поля, свойства или значения выражения Razor с помощью атрибута HTML-элемента [`@bind`](xref:mvc/views/razor#bind).

В следующем примере выполняется привязка элемента `<input>` к полю `currentValue`, а элемента `<input>` — к свойству `CurrentValue`.

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

Когда один из элементов теряет фокус, привязанное поле или свойство обновляется.

Текстовое поле обновляется в пользовательском интерфейсе только при отрисовке компонента, а не в ответ на изменение значения поля или свойства. Так как компоненты отрисовываются после выполнения кода обработчика событий, изменения полей и свойств *обычно* отражаются в пользовательском интерфейсе сразу после активации обработчика событий.

Использование [`@bind`](xref:mvc/views/razor#bind) со свойством `CurrentValue` (`<input @bind="CurrentValue" />`) фактически эквивалентно следующему:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

Когда компонент отрисовывается, значение `value` элемента input берется из свойства `CurrentValue`. Когда пользователь вводит данные в текстовом поле, а затем переводит фокус, инициируется событие `onchange`, и свойству `CurrentValue` присваивается измененное значение. На практике код обычно сложнее этого, так как [`@bind`](xref:mvc/views/razor#bind) применяется в случаях, когда выполняется преобразование типов. Как правило, [`@bind`](xref:mvc/views/razor#bind) связывает текущее значение выражения с атрибутом `value` и обрабатывает изменения с помощью зарегистрированного обработчика.

Чтобы привязать свойство или поле к другим событиям, можно использовать атрибут `@bind:event` с параметром `event`. В следующем примере свойство `CurrentValue` привязывается к событию `oninput`:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

В отличие от события `onchange`, которое происходит, когда элемент теряет фокус, событие `oninput` происходит при изменении значения в текстовом поле.

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

Привязка атрибута учитывает регистр:

* `@bind` является допустимым;
* `@Bind` и `@BIND` является недопустимым.

## <a name="unparsable-values"></a>Значения, не поддающиеся анализу

Когда пользователь присваивает не поддающееся анализу значение элементу, привязанному к данным, при активации события привязки автоматически восстанавливается предыдущее значение.

Рассмотрим следующий сценарий.

* Элемент `<input>` привязан к типу `int` с начальным значением `123`:

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* Пользователь изменяет значение элемента на `123.45` на странице и переводит фокус.

В приведенном выше сценарии восстанавливается значение элемента `123`. Когда значение `123.45` отклоняется и вместо него используется исходное значение `123`, пользователь понимает, что его значение не было принято.

По умолчанию привязка применяется к событию `onchange` элемента (`@bind="{PROPERTY OR FIELD}"`). Чтобы выполнить привязку к другому событию, используйте `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`. Для события `oninput` (`@bind:event="oninput"`) исходное значение восстанавливается после любого нажатия клавиши, которое приводит к вводу не поддающегося анализу значения. При привязке типа `int` к событию `oninput` пользователь не может ввести символ `.`. Символ `.` немедленно удаляется, так что пользователь сразу понимает, что допустимы только целые числа. В некоторых ситуациях восстанавливать исходное значение при возникновении события `oninput` нежелательно, например, когда пользователю следует дать возможность самому удалять не поддающиеся анализу значения `<input>`. Ниже представлены возможные альтернативы.

* Не используйте событие `oninput`. Используйте вместо этого событие `onchange` по умолчанию (укажите просто `@bind="{PROPERTY OR FIELD}"`). В этом случае недопустимое значение не будет меняться на исходное, пока элемент не потеряет фокус.
* Выполните привязку к типу, допускающему значение NULL, например `int?` или `string`, и предоставьте пользовательскую логику для обработки недопустимых значений.
* Используйте [компонент для проверки формы](xref:blazor/forms-validation), например <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> или <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>. Компоненты для проверки форм имеют встроенную поддержку управления недопустимыми входными данными. Дополнительные сведения см. в разделе <xref:blazor/forms-validation>. Компоненты для проверки форм:
  * позволяют пользователю вводить недопустимые данные и получать ошибки проверки в соответствующем контексте <xref:Microsoft.AspNetCore.Components.Forms.EditContext>;
  * выводят ошибки проверки в пользовательском интерфейсе, не мешая пользователю вводить дополнительные данные в веб-форме.

## <a name="format-strings"></a>Строки формата

Привязка данных работает со строками формата <xref:System.DateTime> с использованием `@bind:format`. Другие выражения форматирования, например денежные или числовые форматы, в настоящее время недоступны.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

В приведенном выше коде типом поля элемента `<input>` (`type`) по умолчанию является `text`. `@bind:format` поддерживается для привязки следующих типов .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Атрибут `@bind:format` указывает формат даты, применяемый к значению `value` элемента `<input>`. Формат также используется для синтаксического анализа значения при возникновении события `onchange`.

Указывать формат для типа поля `date` не рекомендуется, так как в Blazor есть встроенная поддержка форматирования дат. Если все же для типа поля `date` указывается формат, для правильной работы привязки используйте только формат даты `yyyy-MM-dd`.

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a>Привязка с помощью параметров компонентов

Распространенным сценарием является привязка свойства дочернего компонента к свойству родительского компонента. Такой сценарий называется *цепочкой привязки*, так как одновременно имеется несколько уровней привязки.

[Параметры компонента](xref:blazor/components/index#component-parameters) позволяют привязать свойства родительского компонента с помощью синтаксиса `@bind-{PROPERTY}`.

Цепочку привязки нельзя реализовать с помощью синтаксиса [`@bind`](xref:mvc/views/razor#bind) в дочернем компоненте. Обработчик событий и значение должны быть указаны отдельно для поддержки изменения свойства родительского компонента из дочернего.

Родительский компонент по-прежнему использует синтаксис [`@bind`](xref:mvc/views/razor#bind) для настройки привязки данных к дочернему компоненту.

Следующий компонент `Child` (`Shared/Child.razor`) имеет параметр компонента `Year` и обратный вызов `YearChanged`.

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

Имя обратного вызова (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) должно включать имя параметра компонента, за которым следует суффикс "`Changed`" (`{PARAMETER NAME}Changed`). В предыдущем примере обратный вызов назывался `YearChanged`. <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> вызывает делегат, связанный с привязкой с указанным аргументом, и отправляет уведомление о событии для измененного свойства.

В следующем компоненте `Parent` (`Parent.razor`) поле `year` привязано к параметру `Year` дочернего компонента.

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

Параметр `Year` допускает привязку, так как он имеет сопутствующее событие `YearChanged`, соответствующее типу параметра `Year`.

По соглашению свойство можно привязать к соответствующему обработчику событий, включив атрибут `@bind-{PROPERTY}:event`, назначенный обработчику. Запись `<Child @bind-Year="year" />` эквивалентна следующей.

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

В более сложных и реальных примерах следующий компонент `PasswordField`(`PasswordField.razor`):

* присваивает элементу `<input>` значение поля `password`;
* сообщает об изменениях свойства `Password` родительскому компоненту с помощью вызова [`EventCallback`](xref:blazor/components/event-handling#eventcallback), который передает текущее значение поля `password` дочернего компонента в качестве аргумента;
* использует событие `onclick` для активации метода `ToggleShowPassword`. Для получения дополнительной информации см. <xref:blazor/components/event-handling>.

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

Компонент `PasswordField` используется в другом компоненте:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Выполняйте проверки или перехватывайте ошибки в методе, который вызывает делегат привязки. В следующем примере пользователю немедленно сообщается о том, что в пароле есть пробел:

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

Дополнительные сведения о методе <xref:Microsoft.AspNetCore.Components.EventCallback%601> см. в разделе <xref:blazor/components/event-handling#eventcallback>.

## <a name="bind-across-more-than-two-components"></a>Привязка через более чем два компонента

Привязку можно выполнять через любое число вложенных компонентов, но необходимо соблюдать односторонний поток данных:

* уведомления об изменениях *передаются вверх по иерархии*;
* новые значения параметров *передаются вниз по иерархии*.

Распространенный и рекомендуемый подход заключается в хранении базовых данных только в родительском компоненте, что позволяет избежать путаницы в отношении того, какое состояние необходимо обновить.

Эти принципы демонстрируются на примере следующих компонентов:

`ParentComponent.razor`:

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

`ChildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

`GrandchildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

Описание альтернативного подхода, подходящего для совместного использования данных в памяти компонентами, которые не обязательно являются вложенными, см. в разделе *Служба контейнеров состояния в памяти* статьи <xref:blazor/state-management>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Привязка к переключателям в форме](xref:blazor/forms-validation#radio-buttons)
* [Привязка параметров элемента `<select>` к значению объекта C# `null` в форме](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
