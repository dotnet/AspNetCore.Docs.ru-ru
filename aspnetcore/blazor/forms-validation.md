---
title: Формы и проверка ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать сценарии проверки форм и полей в Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: 1287ab5ce61e58848329c96393c3ee8c37610245
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658694"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>Формы и проверка ASP.NET Core Blazor

Авторы: [Дэниел Рот](https://github.com/danroth27) (Daniel Roth), [Реми Бургарель](https://remibou.github.io/) (Rémi Bourgarel) и [Люк Лэтем](https://github.com/guardrex) (Luke Latham)

Формы и проверка поддерживаются в Blazor с помощью [заметок к данным](xref:mvc/models/validation).

Следующий тип `ExampleModel` определяет логику проверки с помощью заметок к данным:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Форма определяется с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.EditForm>. В следующей форме показаны типичные элементы, компоненты и код Razor:

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

В предшествующем примере:

* Форма проверяет введенные пользователем данные в поле `name`, используя проверку, определенную в типе `ExampleModel`. Модель создается в блоке `@code` компонента и хранится в частном поле (`exampleModel`). Поле назначается атрибуту `Model` элемента `<EditForm>`.
* Элемент `@bind-Value` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText> привязывает:
  * Свойство модели (`exampleModel.Name`) к свойству `Value` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText>. См. сведения о привязке свойств здесь: <xref:blazor/components/data-binding#binding-with-component-parameters>.
  * Делегат события изменения к свойству `ValueChanged` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText>.
* [Компонент проверяющего элемента управления](#validator-components) <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> привязывает поддержку проверки с помощью заметок к данным.
* Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> обобщает сообщения проверки.
* `HandleValidSubmit` активируется, когда форма успешно отправляется (проходит проверку).

## <a name="built-in-forms-components"></a>Встроенные компоненты форм

Для получения и проверки вводимых пользователем данных доступен набор встроенных компонентов. Входные данные проверяются при их изменении и отправке формы. В приведенной ниже таблице показаны доступные компоненты ввода.

::: moniker range=">= aspnetcore-5.0"

| Компонент ввода | Отображается как &hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| Компонент ввода | Отображается как &hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> Компоненты `InputRadio` и `InputRadioGroup` доступны в ASP.NET Core 5.0 и более поздних версий. Для получения дополнительных сведений выберите вариант этой статьи для версии 5.0 или более поздней.

::: moniker-end

Все компоненты ввода, включая <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, поддерживают произвольные атрибуты. В отрисованный HTML-элемент добавляется любой атрибут, который не соответствует параметру компонента.

Компоненты ввода обеспечивают поведение проверки по умолчанию при изменении поля, включая изменение класса CSS поля в соответствии с состоянием поля. Некоторые компоненты включают в себя полезную логику синтаксического анализа. Например, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> и <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> обрабатывают не анализируемые значения надлежащим образом, регистрируя их как ошибки проверки. Типы, которые могут принимать значения NULL, также поддерживают допустимость значений NULL для целевого поля (например, `int?`).

Следующий тип `Starship` определяет логику проверки, используя более широкий набор свойств и заметок к данным, чем предыдущий `ExampleModel`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

В предыдущем примере `Description` является необязательным, так как заметки к данным отсутствуют.

Следующая форма проверяет введенные пользователем данные, используя проверку, определенную в модели `Starship`:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.Forms.EditForm> создает <xref:Microsoft.AspNetCore.Components.Forms.EditContext> в виде [каскадного значения](xref:blazor/components/cascading-values-and-parameters), которое отслеживает метаданные процесса редактирования, включая измененные поля и текущие сообщения проверки.

Назначайте <xref:Microsoft.AspNetCore.Components.Forms.EditForm> **либо** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, **либо** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType>. Назначение обоих значений не поддерживается, и возникает **ошибка времени выполнения**.

<xref:Microsoft.AspNetCore.Components.Forms.EditForm> предоставляет удобные события для допустимой и недопустимой отправки формы.

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

Используйте <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>, чтобы применять пользовательский код для активации проверки и выполнения проверки значений полей.

В следующем примере:

* Метод `HandleSubmit` выполняется при нажатии кнопки **`Submit`** .
* Форма проверяется путем вызова <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.
* В зависимости от результата проверки выполняется дополнительный код. Поместите бизнес-логику в метод, назначенный <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> API платформы для очистки сообщений проверки непосредственно от <xref:Microsoft.AspNetCore.Components.Forms.EditContext> не существует. Поэтому в целом не рекомендуется добавлять сообщения проверки в новый класс <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> в форме. Для управления сообщениями проверки используйте [компонент проверяющего элемента управления](#validator-components) с [кодом проверки бизнес-логики](#business-logic-validation), как описано в этой статье.

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a>Поддержка отображаемого имени

*Этот раздел относится к ASP.NET Core в релизе-кандидате 1 (RC1) платформы .NET 5 или более поздней версии.*

Следующие встроенные компоненты поддерживают отображаемые имена с помощью параметра `DisplayName`.

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

В следующем примере компонента `InputDate`:

* Отображаемое имя (`DisplayName`) задается как `birthday`.
* Компонент привязан к свойству `BirthDate` как тип `DateTime`.

```razor
<InputDate @bind-Value="BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

Если пользователь предоставляет не дату, возникает следующая ошибка проверки.

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a>Компоненты проверяющего элемента управления

Компоненты проверяющего элемента управления поддерживают проверку форм путем управления <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> для класса <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы.

Платформа Blazor предоставляет компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> для прикрепления к формам поддержки проверки на основе [атрибутов проверки (заметок к данным)](xref:mvc/models/validation#validation-attributes). Создайте настраиваемые компоненты проверяющего элемента управления, чтобы обрабатывать сообщения проверки для различных форм на одной странице или одной и той же формы на разных этапах обработки, например проверку на стороне клиента с последующей проверкой на стороне сервера. Показанный в этом разделе пример компонента проверяющего элемента управления (`CustomValidator`) используется в следующих разделах этой статьи.

* [Проверка бизнес-логики](#business-logic-validation)
* [Серверная проверка](#server-validation)

> [!NOTE]
> Во многих случаях вместо настраиваемых компонентов проверяющего элемента управления можно использовать настраиваемые атрибуты проверки заметок к данным. Настраиваемые атрибуты, применяемые к модели формы, активируются с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. При использовании вместе с проверкой на стороне сервера любые настраиваемые атрибуты, применяемые к модели, должны быть исполняемыми на сервере. Дополнительные сведения см. в разделе <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

Создайте компонент проверяющего элемента управления из класса <xref:Microsoft.AspNetCore.Components.ComponentBase>.

* Класс <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы является [каскадным параметром](xref:blazor/components/cascading-values-and-parameters) компонента.
* При инициализации компонента проверяющего элемента управления создается новый класс <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> для поддержки текущего списка ошибок формы.
* Хранилище сообщений получает ошибки, когда код разработчика в компоненте формы вызывает метод `DisplayErrors`. Ошибки передаются в метод `DisplayErrors` в классе [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2). В словаре ключом является имя поля формы, в котором есть одна ошибка или несколько. Значением является список ошибок.
* Сообщения очищаются, когда происходит любое из следующих событий.
  * В <xref:Microsoft.AspNetCore.Components.Forms.EditContext> запрашивается проверка при возникновении события <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested>. Все ошибки очищаются.
  * В форме изменяется поле при возникновении события <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>. Очищаются только ошибки для этого поля.
  * В коде разработчика вызывается метод `ClearErrors`. Все ошибки очищаются.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

public class CustomValidator : ComponentBase
{
    private ValidationMessageStore messageStore;

    [CascadingParameter]
    private EditContext CurrentEditContext { get; set; }

    protected override void OnInitialized()
    {
        if (CurrentEditContext == null)
        {
            throw new InvalidOperationException(
                $"{nameof(CustomValidator)} requires a cascading " +
                $"parameter of type {nameof(EditContext)}. " +
                $"For example, you can use {nameof(CustomValidator)} " +
                $"inside an {nameof(EditForm)}.");
        }

        messageStore = new ValidationMessageStore(CurrentEditContext);

        CurrentEditContext.OnValidationRequested += (s, e) => 
            messageStore.Clear();
        CurrentEditContext.OnFieldChanged += (s, e) => 
            messageStore.Clear(e.FieldIdentifier);
    }

    public void DisplayErrors(Dictionary<string, List<string>> errors)
    {
        foreach (var err in errors)
        {
            messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
        }

        CurrentEditContext.NotifyValidationStateChanged();
    }

    public void ClearErrors()
    {
        messageStore.Clear();
        CurrentEditContext.NotifyValidationStateChanged();
    }
}
```

## <a name="business-logic-validation"></a>Проверка бизнес-логики

Проверку бизнес-логики можно выполнить с помощью [компонента проверяющего элемента управления](#validator-components), который получает ошибки формы в словаре.

В следующем примере:

* Используется компонент `CustomValidator` из раздела [Компоненты проверяющего элемента управления](#validator-components) этой статьи.
* Проверка требует значение для описания доставки (`Description`), если пользователь выбирает классификацию доставки `Defense` (`Classification`).

Когда сообщения проверки задаются в компоненте, они добавляются в класс <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> проверяющего элемента управления и отображаются в классе <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> Вместо [компонентов проверки](#validator-components) можно использовать атрибуты проверки заметок к данным. Настраиваемые атрибуты, применяемые к модели формы, активируются с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. При использовании вместе с проверкой на стороне сервера атрибуты должны быть исполняемыми на сервере. Дополнительные сведения см. в разделе <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

## <a name="server-validation"></a>Серверная проверка

Серверную проверку можно выполнить с помощью [компонента проверяющего элемента управления](#validator-components) сервера.

* Обрабатывайте проверку на стороне клиента в форме с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.
* Когда форма проходит проверку на стороне клиента (вызывается <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>), отправляйте <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> в API внутреннего сервера для обработки формы.
* Обрабатывайте проверку модели на сервере.
* Серверный API включает как встроенную платформенную проверку заметок к данным, так и настраиваемую логику проверки, предоставленную разработчиком. Если проверка на сервере пройдена, выполните обработку формы и отправьте обратно код состояния успеха (*200 — OK*). Если проверка завершается неудачно, возвращайте код состояния сбоя (*400 — неверный запрос*) и ошибки проверки полей.
* Отключите форму в случае успешного выполнения или отобразите ошибки.

Основу приведенного ниже примера составляют следующие компоненты.

* Размещенное решение Blazor, созданное [шаблоном проекта Blazor Hosted](xref:blazor/hosting-models#blazor-webassembly). Этот пример можно использовать с любыми защищенными размещенными решениями Blazor, описанными в [документации по безопасности и Identity](xref:blazor/security/webassembly/index#implementation-guidance).
* Пример формы *Starfleet Starship Database* из предыдущего раздела [Встроенные компоненты форм](#built-in-forms-components).
* Компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> платформы Blazor.
* Компонент `CustomValidator`, показанный в разделе [Компоненты проверяющего элемента управления](#validator-components).

В следующем примере серверный API проверяет, предоставлено ли значение для описания доставки (`Description`), если пользователь выбирает классификацию (`Classification`) доставки `Defense`.

Поместите модель `Starship` в проект `Shared` решения, чтобы эту модель могли использовать клиентские и серверные приложения. Так как для модели требуются заметки к данным, добавьте ссылку на пакет для [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) в файл проекта `Shared`.

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

Чтобы определить последнюю версию пакета, не являющуюся предварительной, просмотрите **журнал версий** пакета по адресу [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).

В проекте серверного API добавьте контроллер для обработки запросов проверки Starship (`Controllers/StarshipValidation.cs`) и возвращайте сообщения о неудачной проверке.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace {ASSEMBLY NAME}.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

Заполнитель `{ASSEMBLY NAME}` в предыдущем примере — это имя сборки приложения (например, `BlazorSample.Server`).

Если на сервере возникает ошибка проверки привязки модели, то [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) обычно возвращает [ответ о неверном запросе по умолчанию](xref:web-api/index#default-badrequest-response) с <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. Этот ответ, помимо ошибок проверки, содержит дополнительные данные, как показано в следующем примере, когда все поля формы *Starfleet Starship Database* не отправляются и форма не проходит проверку.

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

Если серверный API возвращает предыдущий ответ JSON по умолчанию, клиент может выполнить анализ ответа, чтобы получить дочерние элементы узла `errors`. Однако анализировать этот файл неудобно. Для выполнения анализа JSON необходимо добавить дополнительный код после вызова <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>, чтобы создавать [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) ошибок при обработке ошибок проверки форм. В идеале серверный API должен возвращать только ошибки проверки.

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

Чтобы изменить ответ серверного API так, чтобы он возвращал только ошибки проверки, измените делегат, который вызывается для действий, помеченных <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> в `Startup.ConfigureServices`. Для конечной точки API (`/StarshipValidation`) возвращайте <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> с помощью <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>. Для других конечных точек API сохраните поведение по умолчанию, возвращая результат объекта с помощью нового <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

Дополнительные сведения см. в разделе <xref:web-api/handle-errors#validation-failure-error-response>.

В клиентском проекте добавьте компонент проверяющего элемента управления, показанный в разделе [Компоненты проверяющего элемента управления](#validator-components).

В клиентском проекте форма *Starfleet Starship Database* обновляется для отображения ошибок серверной проверки с помощью компонента `CustomValidator`. Когда серверный API возвращает сообщения проверки, они добавляются в <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> компонента `CustomValidator`. Эти ошибки можно вывести из <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы с помощью <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> формы.

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> Вместо [компонентов проверки](#validator-components) можно использовать атрибуты проверки заметок к данным. Настраиваемые атрибуты, применяемые к модели формы, активируются с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. При использовании вместе с проверкой на стороне сервера атрибуты должны быть исполняемыми на сервере. Дополнительные сведения см. в разделе <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

> [!NOTE]
> Метод проверки на стороне сервера в этом разделе подходит для любого из примеров размещенного решения Blazor WebAssembly в этом наборе документации.
>
> * [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Сервер Identity](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a>InputText на основе события ввода

Используйте компонент <xref:Microsoft.AspNetCore.Components.Forms.InputText>, чтобы создать пользовательский компонент, использующий событие `input`, а не событие `change`.

В следующем примере компонент `CustomInputText` наследует компонент `InputText` платформы и задает привязку для события `oninput`.

`Shared/CustomInputText.razor`:

```razor
@inherits InputText

<input @attributes="AdditionalAttributes" class="@CssClass" 
    @bind="CurrentValueAsString" @bind:event="oninput" />
```

Компонент `CustomInputText` можно использовать везде, где используется <xref:Microsoft.AspNetCore.Components.Forms.InputText>:

`Pages/TestForm.razor`:

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a>Переключатели

::: moniker range=">= aspnetcore-5.0"

Используйте компоненты `InputRadio` с компонентом `InputRadioGroup`, чтобы создать группу переключателей. В следующем примере свойства добавляются в модель `Starship`, описанную в разделе [Встроенные компоненты форм](#built-in-forms-components).

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

Добавьте в приложение указанные ниже перечисления (`enums`). Создайте файл для хранения `enums` или добавьте `enums` в файл `Starship.cs`. Сделайте `enums` доступным для модели `Starship` и формы *Starfleet Starship Database*.

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

Обновите форму *Starfleet Starship Database*, описанную в разделе [Встроенные компоненты форм](#built-in-forms-components). Добавьте компоненты для создания следующих элементов:

* группы переключателей для выбора производителя корабля;
* группы переключателей для выбора цвета и типа двигателя корабля.

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> Если опустить `Name`, компоненты `InputRadio` будут группироваться по последнему предку.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

При работе с переключателями в форме привязка данных обрабатывается иначе, чем другие элементы, так как переключатели оцениваются как группа. Значение каждого переключателя является фиксированным, но значение группы переключателей является значением выбранного переключателя. В приведенном ниже примере показано, как выполнить следующие задачи.

* Обработка привязки данных для группы переключателей.
* Поддержка проверки с помощью пользовательского компонента `InputRadio`.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

Следующий <xref:Microsoft.AspNetCore.Components.Forms.EditForm> использует предыдущий компонент `InputRadio` для получения и проверки оценки пользователя:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a>Привязка параметров элемента `<select>` к значениям `null` объекта C#

Не существует целесообразного способа представить значение параметра элемента `<select>` в виде значения `null` объекта C# по следующим причинам:

* Атрибуты HTML не могут иметь значение `null`. Ближайшим эквивалентом `null` в HTML является отсутствие атрибута HTML `value` в элементе `<option>`.
* При выборе `<option>` без атрибута `value` браузер обрабатывает такое значение как *текстовое содержимое* элемента `<option>`.

Платформа Blazor не пытается блокировать поведение по умолчанию, так как для этого потребуется:

* создать цепочку специальных решений в платформе;
* кардинально изменить текущее поведение платформы.

::: moniker range=">= aspnetcore-5.0"

Приемлемым эквивалентом `null` в HTML является *пустая строка* (`value`). Платформа Blazor преобразует `null` в пустую строку для двусторонней привязки к значению `<select>`.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Платформа Blazor не выполняет автоматическое преобразование `null` в пустую строку при попытке двусторонней привязки к значению элемента `<select>`. Дополнительные сведения см. в разделе [Исправление привязки `<select>` к значению NULL (dotnet/aspnetcore 23221)](https://github.com/dotnet/aspnetcore/pull/23221).

::: moniker-end

## <a name="validation-support"></a>Поддержка проверки

Компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> привязывает поддержку проверки с помощью заметок к данным к каскадному <xref:Microsoft.AspNetCore.Components.Forms.EditContext>. Чтобы включить поддержку проверки с помощью заметок к данным, требуется это явное действие. Чтобы использовать другую систему проверки, а не заметки к данным, замените <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> пользовательской реализацией. Реализация ASP.NET Core доступна для проверки в эталонном источнике: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs). Предыдущие ссылки на источник ссылки предоставляют код из ветви `master` репозитория, которая представляет текущую разработку единицы продукта для следующего выпуска ASP.NET Core. Чтобы выбрать ветвь для другого выпуска, используйте селектор ветвей GitHub (например `release/3.1`).

Blazor выполняет два типа проверки данных:

* *Проверка поля* выполняется, когда пользователь выходит за пределы поля. Во время проверки поля компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> связывает все результаты проверки с полем.
* *Проверка модели* выполняется, когда пользователь отправляет форму. Во время проверки модели компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> пытается определить поле на основе имени члена из результатов проверки. Результаты проверки, не связанные с отдельным элементом, связаны с моделью, а не с полем.

### <a name="validation-summary-and-validation-message-components"></a>Сводка проверки и компоненты сообщений о проверке

Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> содержит обзор всех сообщений проверки аналогично [вспомогательному методу тега сводки проверки](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Выходные сообщения проверки для конкретной модели с параметром `Model`:
  
```razor
<ValidationSummary Model="@starship" />
```

Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> отображает сообщения проверки для определенного поля, которые похожи на [вспомогательный метод тега сообщения проверки](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Укажите поле для проверки с помощью атрибута `For` и лямбда-выражения с именем свойства модели:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Компоненты <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> и <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> поддерживают произвольные атрибуты. В созданный элемент `<div>` или `<ul>` добавляется любой атрибут, который не соответствует параметру компонента.

Вы можете управлять стилем сообщений проверки с помощью таблицы стилей приложения (`wwwroot/css/app.css` или `wwwroot/css/site.css`). Класс `validation-message` по умолчанию задает для сообщений проверки красный цвет текста:

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>Пользовательские атрибуты проверки

Чтобы убедиться, что результат проверки правильно связан с полем при использовании [настраиваемого атрибута проверки](xref:mvc/models/validation#custom-attributes), передайте <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> контекста проверки при создании <xref:System.ComponentModel.DataAnnotations.ValidationResult>:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> имеет значение `null`. Внедрение служб для проверки в методе `IsValid` не поддерживается.

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a>Настраиваемые атрибуты класса проверки

Имена настраиваемых классов проверки полезны при интеграции с платформами CSS, такими как [Bootstrap](https://getbootstrap.com/). Чтобы указать имена настраиваемых классов проверки, создайте класс, производный от `FieldCssClassProvider`, и задайте класс в экземпляре <xref:Microsoft.AspNetCore.Components.Forms.EditContext>:

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a>Пакет проверки заметок к данным в Blazor

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) — это пакет, который выполняет проверку пропусков заполнения с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. В настоящее время пакет является *экспериментальным*.

> [!NOTE]
> Пакет [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) имеет последнюю версию *RC* на [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). В настоящее время продолжайте использовать *экспериментальный* пакет RC. В будущем выпуске сборка пакета может быть перемещена в платформу или в среду выполнения. Дополнительные обновления смотрите в [репозитории объявлений GitHub](https://github.com/aspnet/Announcements), [репозитории GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore) или в этом разделе.

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a>Атрибут `[CompareProperty]`

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> плохо работает с компонентом <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>, так как он не связывает результат проверки с конкретным элементом. Это может привести к несогласованному поведению при проверке на уровне полей и при проверке всей модели при отправке. *Экспериментальный* пакет [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) содержит дополнительный атрибут проверки `ComparePropertyAttribute`, который обходит эти ограничения. В приложении Blazor объект `[CompareProperty]` является непосредственной заменой атрибута [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute).

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a>Вложенные модели, типы коллекций и сложные типы

Blazor обеспечивает поддержку проверки входных данных формы с помощью заметок к данным со встроенным <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. Однако <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> проверяет только свойства верхнего уровня модели, привязанной к форме, которые не являются свойствами типа коллекции или сложного типа.

Чтобы проверить весь граф объектов привязанной модели, включая свойства типа коллекции и сложного типа, используйте `ObjectGraphDataAnnotationsValidator`, предоставляемый *экспериментальным* пакетом [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation):

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Делайте заметки для свойств модели с помощью `[ValidateComplexType]`. В следующих классах модели класс `ShipDescription` содержит дополнительные заметки к данным для проверки, когда модель привязана к форме:

`Starship.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

`ShipDescription.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }

    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Предоставление кнопки "Отправить" на основе проверки формы

Чтобы включить или отключить кнопку "Отправить" на основе проверки формы, выполните следующие действия:

* Используйте <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы, чтобы назначить модель при инициализации компонента.
* Проверьте форму в обратном вызове <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> контекста, чтобы включить и отключить кнопку "Отправить".
* Отсоедините обработчик событий в методе `Dispose`. Дополнительные сведения см. в разделе <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

> [!NOTE]
> При использовании <xref:Microsoft.AspNetCore.Components.Forms.EditContext> не следует также назначать <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> классу <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

В примерах выше установите для параметра `formInvalid` значение `false`, если:

* Форма предварительно загружена с допустимыми значениями по умолчанию.
* Вы хотите, чтобы кнопка отправки была включена при загрузке формы.

Побочным эффектом предыдущего подхода является то, что компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> заполняется недопустимыми полями после того, как пользователь взаимодействует с одним полем. Этот сценарий можно решить одним из следующих способов:

* Не используйте компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> в форме.
* Сделайте компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> видимым при нажатии кнопки "Отправить" (например, в методе `HandleValidSubmit`).

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a>Диагностика

> InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both (Для EditForm требуется указать параметр Model или EditContext, но не оба).

Убедитесь, что <xref:Microsoft.AspNetCore.Components.Forms.EditForm> имеет <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **или** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>. Не используйте оба варианта для одной и той же формы.

При назначении <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> форме убедитесь, что создается экземпляр типа модели, как показано в следующем примере:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/file-uploads>

::: moniker-end
