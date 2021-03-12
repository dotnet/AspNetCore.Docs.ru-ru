---
title: Вспомогательные функции тегов в формах в ASP.NET Core
author: rick-anderson
description: Сведения о встроенных вспомогательных функциях тегов, используемых в формах.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/working-with-forms
ms.openlocfilehash: 51e5f2f74493e7f4c18273c8589ed0424a1f2cac
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585985"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>Вспомогательные функции тегов в формах в ASP.NET Core

Авторы: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT), [Н. Тейлор Маллен (N. Taylor Mullen)](https://github.com/NTaylorMullen), [Дейв Пакетт (Dave Paquette)](https://twitter.com/Dave_Paquette) и [Джерри Пелсер (Jerrie Pelser)](https://github.com/jerriep)

В этом документе приводятся сведения о работе с формами и элементами HTML, часто используемыми в формах. Элемент HTML [форма](https://www.w3.org/TR/html401/interact/forms.html) предоставляет основной механизм, используемый веб-приложениями для отправки данных на сервер. В большей части этого документа описываются [вспомогательные функции тегов](tag-helpers/intro.md) и их применение для создания надежных форм HTML. Перед прочтением этого документа рекомендуется изучить статью [Общие сведения о вспомогательных функциях тегов](tag-helpers/intro.md).

Во многих случаях вспомогательные методы HTML располагают альтернативными вариантами для определенной вспомогательной функции тега, но следует отметить, что вспомогательные функции тегов не заменяют вспомогательные методы HTML и для каждого вспомогательного метода HTML не существует конкретной вспомогательной функции тега. Если есть альтернатива вспомогательному методу HTML, она будет указана.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>Вспомогательная функция тега формы

Вспомогательная функция тега [формы](https://www.w3.org/TR/html401/interact/forms.html) :

* Создает [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` значение атрибута HTML для действия контроллера MVC или именованного маршрута.

* Создает скрытый [токен проверки запроса](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) для предотвращения подделки межсайтовых запросов (при использовании с атрибутом `[ValidateAntiForgeryToken]` в методе действия HTTP Post).

* Предоставляет атрибут `asp-route-<Parameter Name>`, где `<Parameter Name>` добавляется в значения маршрута. Параметры `routeValues` для `Html.BeginForm` и `Html.BeginRouteForm` предоставляют аналогичные функциональные возможности.

* Располагает альтернативой вспомогательному методу HTML — `Html.BeginForm` и `Html.BeginRouteForm`.

Образец.

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

Приведенная выше вспомогательная функция тега формы создает следующий код HTML:

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

Среда выполнения MVC генерирует значение атрибута `action` на основе атрибутов вспомогательной функции тега формы `asp-controller` и `asp-action`. Вспомогательная функция тега формы также создает скрытый [токен проверки запроса](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) для предотвращения подделки межсайтовых запросов (при использовании с атрибутом `[ValidateAntiForgeryToken]` в методе действия HTTP Post). Защита чистой формы HTML от подделки межсайтовых запросов является трудной задачей, поэтому для ее решения используется вспомогательная функция тега формы.

### <a name="using-a-named-route"></a>Использование именованного маршрута

Атрибут `asp-route` вспомогательной функции тега также может создавать разметку для атрибута HTML `action`. Приложение с [маршрутом](../../fundamentals/routing.md) с именем `register` использует следующую разметку для страницы регистрации:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Многие представления в папке *Views/Account* (сформированные при создании веб-приложения с *учетными записями отдельных пользователей*) содержат атрибут [asp-route-returnurl](xref:mvc/views/working-with-forms):

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>При использовании встроенных шаблонов `returnUrl` заполняется автоматически только в случае, если вы пытаетесь получить доступ к авторизованному ресурсу, но не прошли проверку подлинности или авторизацию. При попытке несанкционированного доступа ПО безопасности промежуточного слоя перенаправит вас на страницу входа с заданным `returnUrl`.

## <a name="the-form-action-tag-helper"></a>Вспомогательная функция тега действий формы

Вспомогательная функция тега действий формы создает атрибут `formaction` в созданном теге `<button ...>` или `<input type="image" ...>`. Атрибут `formaction` определяет, куда форма отправляет свои данные. Он привязывается к [\<input>](https://www.w3.org/wiki/HTML/Elements/input) элементам типа `image` и [\<button>](https://www.w3.org/wiki/HTML/Elements/button) элементов. Вспомогательная функция тега действия "форма" позволяет использовать несколько атрибутов [анчортагхелпер](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` для управления тем, какая `formaction` ссылка создается для соответствующего элемента.

Ниже перечислены поддерживаемые атрибуты [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) для управления значением `formaction`.

|attribute|Описание|
|---|---|
|[asp-controller](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|Имя контроллера.|
|[asp-action](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|Имя метода действия.|
|[asp-area](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|Имя области.|
|[asp-page](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|Имя Razor страницы.|
|[asp-page-handler](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|Имя Razor обработчика страницы.|
|[asp-route](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|Имя маршрута.|
|[asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|Одно значение URL-адреса маршрута. Например, `asp-route-id="1234"`.|
|[asp-all-route-data](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|Все значения маршрута.|
|[asp-fragment](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|Фрагмент URL-адреса.|

### <a name="submit-to-controller-example"></a>Отправка формы в пример контроллера

Следующая разметка отправляет форму в действие `Index`, выполняемое `HomeController`, если выбран ввод или кнопка.

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

Предыдущая разметка создает следующий код HTML.

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a>Отправка формы в пример страницы

Следующая разметка отправляет форму на `About` Razor страницу:

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

Предыдущая разметка создает следующий код HTML.

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a>Отправка формы в пример маршрута

Рассмотрим конечную точку `/Home/Test`.

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

Следующая разметка отправляет форму в конечную точку `/Home/Test`.

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

Предыдущая разметка создает следующий код HTML.

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a>Вспомогательная функция тега входных данных

Вспомогательная функция тега input привязывает [\<input>](https://www.w3.org/wiki/HTML/Elements/input) элемент HTML к выражению модели в представлении Razor.

Синтаксис:

```cshtml
<input asp-for="<Expression Name>">
```

Вспомогательная функция тега входных данных:

* Создает атрибуты HTML `id` и `name` для имени выражения, указанного в атрибуте `asp-for`. `asp-for="Property1.Property2"` равно `m => m.Property1.Property2`. Имя выражения совпадает со значением атрибута `asp-for`. Дополнительные сведения см. в разделе [Имена выражений](#expression-names) .

* Задает значение атрибута HTML `type` на основе атрибутов типа модели и [заметок к данным](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter), примененным к свойству модели.

* Значение атрибута HTML `type` не перезаписывается, если оно указано.

* Создает атрибуты проверки [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) из атрибутов [заметок к данным](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter), примененным к свойствам модели.

* Располагает перекрытием вспомогательного метода HTML с `Html.TextBoxFor` и `Html.EditorFor`. Дополнительные сведения см. в разделе **Альтернативы вспомогательного метода HTML вспомогательной функции тега входных данных**.

* Обеспечивает строгую типизацию. Если после изменения имени свойства не выполнить обновление вспомогательной функции тега, возникнет ошибка следующего вида:

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

Вспомогательная функция тега `Input` задает атрибут HTML `type` на основе типа .NET. В следующей таблице перечислены некоторые распространенные типы .NET и созданный тип HTML (указаны не все типы .NET).

|Тип .NET|Тип входных данных|
|---|---|
|Bool|type="checkbox"|
|Строка|type="text"|
|Дата и время|type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Byte|type="number"|
|Int|type="number"|
|Single, Double|type="number"|

В следующей таблице приведены некоторые наиболее распространенные атрибуты [заметок к данным](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter), которые вспомогательная функция тега входных данных будет сопоставлять с определенными типами входных данных (указаны не все атрибуты проверки):

|attribute|Тип входных данных|
|---|---|
|[EmailAddress]|type="email"|
|[Url]|type="url"|
|[HiddenInput]|type="hidden"|
|[Phone]|type="tel"|
|[DataType(DataType.Password)]|type="password"|
|[DataType(DataType.Date)]|type="date"|
|[DataType(DataType.Time)]|type="time"|

Образец.

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

Приведенный выше код создает следующий HTML:

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

Заметки данных применяются к свойствам `Email` и `Password`, создающим метаданные для модели. Вспомогательная функция тега входных данных использует метаданные модели и создает атрибуты [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (см. статью [Проверка модели](../models/validation.md)). Эти атрибуты описывают проверяющие элементы управления, присоединяемые к полям входных данных. Это обеспечивает ненавязчивую проверку HTML5 и [jQuery](https://jquery.com/). Ненавязчивые атрибуты имеют формат `data-val-rule="Error Message"` , где Rule — это имя правила проверки (например,, и `data-val-required` `data-val-email` `data-val-maxlength` т. д.). Если в атрибуте указано сообщение об ошибке, оно отображается как значение для `data-val-rule` атрибута. Также существуют атрибуты формы `data-val-ruleName-argumentName="argumentValue"`, которые содержат дополнительные сведения о правиле, например `data-val-maxlength-max="1024"`.

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Альтернативы вспомогательного метода HTML вспомогательной функции тега входных данных

`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` и `Html.EditorFor` имеют функции, перекрывающиеся со вспомогательной функцией тега входных данных. Вспомогательная функция тега входных данных будет автоматически задавать атрибут `type`, а `Html.TextBox` и `Html.TextBoxFor` — нет. `Html.Editor` и `Html.EditorFor` обрабатывают коллекции, сложные объекты и шаблоны, а вспомогательная функция тега входных данных не делает этого. Вспомогательная функция тега входных данных, `Html.EditorFor` и `Html.TextBoxFor` являются строго типизированными (они используют лямбда-выражения); а `Html.TextBox` и `Html.Editor` не являются таковыми (они используют имена выражений).

### <a name="htmlattributes"></a>HtmlAttributes

При выполнении шаблонов по умолчанию `@Html.Editor()` и `@Html.EditorFor()` используют специальную запись `ViewDataDictionary` с именем `htmlAttributes`. Это поведение дополняется параметрами `additionalViewData`. Ключ "htmlAttributes" не учитывает регистр. Ключ "htmlAttributes" обрабатывается так же, как `htmlAttributes` объект, передаваемый во вспомогательные функции входных данных, такие как `@Html.TextBox()`.

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Имена выражений

Значением атрибута `asp-for` является `ModelExpression` и правая часть лямбда-выражения. Таким образом, `asp-for="Property1"` становится `m => m.Property1` в созданном коде, поэтому нет необходимости добавлять префикс `Model`. Чтобы начать встроенное выражение и переместить его перед `m.`, используется символ \@.

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

Выводится следующий результат:

```html
<input type="text" id="joe" name="joe" value="Joe">
```

При использовании свойств коллекции `asp-for="CollectionProperty[23].Member"` генерирует то же самое имя, что и `asp-for="CollectionProperty[i].Member"`, если `i` имеет значение `23`.

Когда MVC ASP.NET Core рассчитывает значение `ModelExpression`, он оценивает несколько источников, включая `ModelState`. Вы можете использовать `<input type="text" asp-for="@Name">`. Рассчитанный атрибут `value` является первым значением, отличным от NULL, из:

* записи `ModelState` с ключом "Name";
* результата выражения `Model.Name`.

### <a name="navigating-child-properties"></a>Навигация по дочерним свойствам

Для перехода к дочерним свойствам можно также использовать путь к свойству модели представления. Рассмотрим более сложный класс модели, который содержит дочернее свойство `Address`.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

В представлении выполняется привязка к `Address.AddressLine1`:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

Следующий HTML создан для `Address.AddressLine1`:

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a>Имена выражений и коллекций

Пример модели, содержащей массив `Colors`:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

Метод действия:

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

Ниже Razor показано, как получить доступ к определенному `Color` элементу.

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

Шаблон *Views/Shared/EditorTemplates/String.cshtml*:

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Пример с использованием `List<T>`:

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

Ниже Razor показано, как выполнить итерацию по коллекции.

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

Шаблон *Views/Shared/EditorTemplates/ToDoItem.cshtml*:

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

По возможности следует использовать `foreach`, когда значение будет применяться в эквивалентном контексте `asp-for` или `Html.DisplayFor`. Обычно лучше использовать `for`, чем `foreach` (если сценарий позволяет), так как ему не нужно выделять перечислитель. Тем не менее оценка индексатора в выражении LINQ может быть недешевой, поэтому ее нужно минимизировать.

&nbsp;

>[!NOTE]
>В приведенном выше комментированном коде показано, как заменить лямбда-выражение оператором `@`, чтобы получить доступ к каждому `ToDoItem` в списке.

## <a name="the-textarea-tag-helper"></a>Вспомогательная функция тега Textarea

Вспомогательная функция тега `Textarea Tag Helper`аналогична вспомогательной функции тега входных данных.

* Создает `id` атрибуты и `name` и атрибуты проверки данных из модели для [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) элемента.

* Обеспечивает строгую типизацию.

* Располагает альтернативой вспомогательному методу HTML — `Html.TextAreaFor`.

Образец.

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Создается следующий HTML:

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a>Вспомогательная функция тега метки

* Создает заголовок и атрибут метки `for` [\<label>](https://www.w3.org/wiki/HTML/Elements/label) элемента для имени выражения.

* Располагает альтернативой вспомогательному методу HTML — `Html.LabelFor`.

`Label Tag Helper` предоставляет следующие преимущества по сравнению с чистым элементом метки:

* Вы автоматически получаете значение описательной метки из `Display` атрибута. Предполагаемое отображаемое имя может изменяться с течением времени, а сочетание атрибута `Display` и вспомогательной функции тега метки будет применять атрибут `Display` везде, где он используется.

* Меньше разметки в исходном коде.

* Строгая типизация со свойством модели.

Образец.

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

Для элемента `<label>` создан следующий HTML:

```html
<label for="Email">Email Address</label>
```

Вспомогательная функция тега метки сгенерировала для атрибута `for` значение "Email", представляющее собой идентификатор, связанный с элементом `<input>`. Вспомогательные функции тегов создают согласованные элементы `id` и `for`, чтобы обеспечить их правильное связывание. Заголовок в этом примере взят из атрибута `Display`. Если модель не содержит атрибут `Display`, заголовком будет имя свойства выражения.

## <a name="the-validation-tag-helpers"></a>Вспомогательные функции тегов проверки

Существует две вспомогательные функции тегов проверки. `Validation Message Tag Helper` отображает сообщение проверки для одного свойства в модели, `Validation Summary Tag Helper` отображает сводку ошибок проверки. `Input Tag Helper` добавляет клиентские атрибуты проверки HTML5 в элементы входных данных на основе атрибутов заметок к данным в классах модели. Проверка также выполняется на сервере. Вспомогательная функция тега проверки отображает эти сообщения об ошибках при возникновении ошибки проверки.

### <a name="the-validation-message-tag-helper"></a>Вспомогательная функция тега сообщения о проверке

* Добавляет атрибут [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` к элементу [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) , который прикрепляет сообщения об ошибках проверки в поле ввода указанного свойства модели.   При возникновении ошибки проверки на стороне клиента [jQuery](https://jquery.com/) отображает сообщение об ошибке в элементе `<span>`.

* Проверка также выполняется на сервере. На клиентах может быть отключена поддержка JavaScript, поэтому некоторые проверки выполняются только на стороне сервера.

* Располагает альтернативой вспомогательному методу HTML — `Html.ValidationMessageFor`.

`Validation Message Tag Helper` используется с атрибутом `asp-validation-for` в элементе HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span).

```cshtml
<span asp-validation-for="Email"></span>
```

Вспомогательная функция тега сообщения о проверке создает следующий HTML:

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

Как правило, `Validation Message Tag Helper` используется после вспомогательной функции тега `Input` для одного и того же свойства. В этом случае сообщения об ошибках проверки отображаются рядом с входными данными, вызвавшими ошибку.

> [!NOTE]
> Для проверки на стороне клиента необходимо иметь представление с правильными ссылками на скрипты JavaScript и [jQuery](https://jquery.com/). Дополнительные сведения см. в статье о [проверке модели](../models/validation.md).

При возникновении ошибки проверки на стороне сервера (например, если выполняется пользовательская проверка на стороне сервера или проверка на стороне клиент отключена) MVC размещает сообщение об ошибке в тексте элемента `<span>`.

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Вспомогательная функция тега сводки по проверке

* Работает с элементами `<div>`, имеющими атрибут `asp-validation-summary`.

* Располагает альтернативой вспомогательному методу HTML — `@Html.ValidationSummary`.

`Validation Summary Tag Helper` используется для отображения сводки по сообщениям проверки. Значением атрибута `asp-validation-summary` может быть любое из следующих:

|asp-validation-summary|Отображаемые сообщения о проверке|
|--- |--- |
|ValidationSummary.All|Свойство и уровень модели|
|ValidationSummary.ModelOnly|Моделирование|
|ValidationSummary.None|Нет|

### <a name="sample"></a>Образец

В следующем примере модель данных имеет атрибуты `DataAnnotation`, в результате чего создаются сообщения об ошибках проверки для элемента `<input>`.  При возникновении ошибки проверки вспомогательная функция тега проверки отображает следующее сообщение об ошибке:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

Созданный HTML (если модель является допустимой):

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a>Вспомогательная функция тега Select

* Создает элемент [select](https://www.w3.org/wiki/HTML/Elements/select) и связанные элементы [option](https://www.w3.org/wiki/HTML/Elements/option) для свойств модели.

* Располагает альтернативой вспомогательному методу HTML — `Html.DropDownListFor` и `Html.ListBoxFor`.

`Select Tag Helper` `asp-for` указывает имя свойства модели для элемента [select](https://www.w3.org/wiki/HTML/Elements/select), а `asp-items` указывает элементы [option](https://www.w3.org/wiki/HTML/Elements/option).  Пример:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Образец.

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

Метод `Index` инициализирует `CountryViewModel`, задает выбранную страну и передает их в представление `Index`.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

Метод HTTP POST `Index` отображает выбор:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

Представление `Index`:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Создается следующий HTML (с выбранным значением "CA"):

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> С вспомогательной функцией тега Select не рекомендуется использовать `ViewBag` или `ViewData`. Модель представления более надежна в процессе предоставления метаданных MVC и, как правило, менее проблематична.

Значение атрибута `asp-for` является особым случаем и не требует префикса `Model`, тогда как он необходим другим атрибутам вспомогательной функции тега (например, `asp-items`).

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Привязка перечисления

Часто бывает удобно использовать `<select>` со свойством `enum` и создавать элементы `SelectListItem` из значений `enum`.

Образец.

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

Метод `GetEnumSelectList` создает объект `SelectList` для перечисления.

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Список перечислителя можно пометить атрибутом `Display` для формирования пользовательского интерфейса с более широкими функциональными возможностями:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Создается следующий HTML:

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a>Группа параметров

Элемент HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) создается, когда модель представления содержит один или несколько `SelectListGroup` объектов.

`CountryViewModelGroup` группирует элементы `SelectListItem` в группы "North America" и "Europe":

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Ниже приведены две группы:

![пример группы параметров](working-with-forms/_static/grp.png)

Созданный HTML:

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a>Множественный выбор

Вспомогательная функция Select автоматически создаст атрибут [multiple = "multiple"](https://w3c.github.io/html-reference/select.html), если свойство, указанное в атрибуте `asp-for`, имеет значение `IEnumerable`. Допустим, имеется такая модель:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

Со следующим представлением:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Генерирует следующий HTML:

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a>Не выбрано

Если вы используете параметр "not specified" (не выбрано) на нескольких страницах, можно создать шаблон, чтобы исключить повторяющийся HTML:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

Шаблон *Views/Shared/EditorTemplates/CountryViewModel.cshtml*:

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

Добавление HTML- [\<option>](https://www.w3.org/wiki/HTML/Elements/option) элементов не ограничено вариантом *без выбора* . Например, следующее представление и метод действия создадут HTML, аналогичный приведенному выше коду:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

В зависимости от текущего значения `Country` будет выбран соответствующий элемент `<option>` (содержащий атрибут `selected="selected"`).

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/views/tag-helpers/intro>
* [Элемент формы HTML](https://www.w3.org/TR/html401/interact/forms.html)
* [Токен проверки запроса](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [Интерфейс IAttributeAdapter](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [Фрагменты кода для этого документа](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/working-with-forms/sample/final)
