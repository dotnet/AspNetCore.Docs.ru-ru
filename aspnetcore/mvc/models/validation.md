---
title: Проверка модели в ASP.NET Core MVC
author: rick-anderson
description: Сведения о проверке модели в ASP.NET Core MVC и Razor страницах.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: 412f95e67fa35e952a907db328395183f1698b9e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587220"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a>Проверка модели в ASP.NET Core MVC и Razor страницах

::: moniker range=">= aspnetcore-3.0"

Автор: [Кирк Ларкин (Kirk Larkin)](https://github.com/serpent5)

В этой статье объясняется, как проверить ввод пользователя в ASP.NET Core приложении MVC или Razor pages.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Состояние модели

Состояние модели представляет ошибки, создаваемые двумя подсистемами: привязкой модели и проверкой модели. Ошибки [привязки модели](model-binding.md) обычно являются ошибками преобразования данных. Например, в целочисленном поле указывается "x". Проверка модели происходит после ее привязки. В процессе сообщается об ошибках несоответствия данных бизнес-правилам. Например, в поле, которое ожидает оценку от 1 до 5, указывается 0.

Привязка модели и проверка модели выполняются до выполнения действия контроллера или Razor метода обработчика страниц. Веб-приложение отвечает за проверку `ModelState.IsValid` и реагирует соответствующим образом. Веб-приложения обычно повторно отображают страницы с сообщением об ошибке.

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

Контроллерам веб-API не нужно проверять `ModelState.IsValid` при наличии атрибута `[ApiController]`. В этом случае автоматически возвращается ответ HTTP 400, содержащий сведения об ошибке, если состояние модели недопустимо. Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Перезапуск проверки

Проверка выполняется автоматически, однако может потребоваться повторить ее вручную. Например, можно вычислить значение свойства и повторно выполнить проверку после установки свойства в вычисляемое значение. Для повторной проверки вызовите метод `TryValidateModel`, как показано ниже.

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a>Атрибуты проверки

Атрибуты проверки позволяют задать правила проверки для свойств модели. В следующем примере из примера приложения показан класс модели, который помечается с помощью атрибутов проверки. `[ClassicMovie]` является настраиваемым атрибутом проверки; остальные являются встроенными. Не показан `[ClassicMovieWithClientValidator]`. `[ClassicMovieWithClientValidator]` демонстрирует альтернативный способ реализации настраиваемого атрибута.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a>Встроенные атрибуты

Ниже приведены некоторые из встроенных атрибутов проверки.

* `[CreditCard]`: Проверяет, что свойство имеет формат кредитной карты. Требует наличия [дополнительных методов проверки JQuery](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).
* `[Compare]`: Проверяет соответствие двух свойств в модели.
* `[EmailAddress]`: Проверяет, что свойство имеет формат электронной почты.
* `[Phone]`: Проверяет, что свойство имеет формат номера телефона.
* `[Range]`: Проверяет, попадает значение свойства в указанный диапазон.
* `[RegularExpression]`: Проверяет соответствие значения свойства указанному регулярному выражению.
* `[Required]`: Проверяет, что поле не имеет значение null. Дополнительные сведения о поведении этого атрибута см. в разделе [ `[Required]` Attribute](#required-attribute) .
* `[StringLength]`: Проверяет, что значение свойства строки не превышает заданное ограничение длины.
* `[Url]`: Проверяет, что свойство имеет формат URL-адреса.
* `[Remote]`: Проверяет входные данные на клиенте, вызывая метод действия на сервере. Дополнительные сведения о поведении этого атрибута см. в разделе [ `[Remote]` Attribute](#remote-attribute) .

Полный перечень атрибутов проверки можно найти в пространстве имен [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).

### <a name="error-messages"></a>Сообщения об ошибках

Атрибуты проверки позволяют указать сообщение об ошибке, которое будет отображаться, если входные данные недопустимы. Пример:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

На внутреннем уровне атрибуты вызывают `String.Format` с заполнителем для имени поля и иногда дополнительным заполнителями. Пример:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

При применении к свойству `Name` сообщение об ошибке, созданное в приведенном выше коде, имело бы вид Name length must be between 6 and 8 (Длина имени должна быть от 6 до 8).

Чтобы узнать, какие параметры передаются в `String.Format` для сообщения об ошибке определенного атрибута, см. раздел [Исходный код DataAnnotations](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="non-nullable-reference-types-and-required-attribute"></a>Ссылочные типы, не допускающие значения NULL, и атрибут [Required]

Система проверки рассматривает параметры, не допускающие значения NULL, или привязанные свойства так, как если бы они имели `[Required]` атрибут. При [включении `Nullable` контекстов](/dotnet/csharp/nullable-references#nullable-contexts)MVC неявно запускает проверку свойств или параметров, не допускающих значения NULL, как если бы они были помечены `[Required]` атрибутом. Рассмотрим следующий код.

```csharp
public class Person
{
    public string Name { get; set; }
}
```

Если приложение было создано с помощью `<Nullable>enable</Nullable>` , отсутствующее значение для `Name` в JSON или в форме POST приведет к ошибке проверки. Используйте ссылочный тип, допускающий значение null, чтобы указать NULL или отсутствующие значения для `Name` Свойства:

```csharp
public class Person
{
    public string? Name { get; set; }
}
```

. Это поведение можно отключить с помощью параметра <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> в `Startup.ConfigureServices`:

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a>Проверка [Required] на сервере

На сервере обязательное значение считается отсутствующим, если свойство имеет значение NULL. Поле, не допускающее значения NULL, всегда является допустимым, и сообщение об ошибке атрибута `[Required]` никогда не выводится.

Тем не менее привязка модели для ненулевого свойства может завершиться ошибкой, приводящей к сообщению об ошибке, например `The value '' is invalid`. Чтобы задать настраиваемое сообщение об ошибке во время проверки не допускающих значения NULL типов на стороне сервера, у вас есть следующие варианты.

* Сделать поле допускающим значение NULL (например, `decimal?` вместо `decimal`). [Допускает значения NULL \<T> ](/dotnet/csharp/programming-guide/nullable-types/) типы значений обрабатываются как стандартные типы, допускающие значение null.
* Указать сообщение об ошибке по умолчанию для использования в привязке модели, как показано в следующем примере.

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  Дополнительные сведения об ошибках привязки модели, у которых можно задать сообщение по умолчанию, см. в разделе <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.

### <a name="required-validation-on-the-client"></a>Проверка [Required] на клиенте

Типы и строки, не допускающие значение NULL, обрабатываются на клиенте не так, как на сервере. На клиенте:

* Значение считается присутствующим только в том случае, если для него вводятся данные. Таким образом, проверка на стороне клиента обрабатывает типы, не допускающие значение NULL, так же, как обнуляемые типы.
* Пробелы в поле строки считаются допустимыми входными данными при проверке методом jQuery [required](https://jqueryvalidation.org/required-method/). Проверка на стороне сервера считает, что обязательное строковое поле недопустимо, если введены только пробелы.

Как отмечалось ранее, не допускающие значение NULL типы рассматриваются как имеющие атрибут `[Required]`. Это означает, что вы получаете проверку на стороне клиента, даже если не применять атрибут `[Required]`. Но если вы не используете атрибут, вы получаете сообщение об ошибке по умолчанию. Чтобы задать настраиваемое сообщение об ошибке, используйте атрибут.

## <a name="remote-attribute"></a>Атрибут [Remote]

Атрибут `[Remote]` реализует проверку на стороне клиента, в ходе которой требуется вызвать метод на сервере, чтобы определить, является ли допустимым поле ввода. Например, приложению может потребоваться проверить, занято ли имя пользователя.

Для реализации удаленной проверки сделайте следующее.

1. Создайте для вызова из JavaScript метод действия.  [Удаленный](https://jqueryvalidation.org/remote-method/) метод проверки JQuery ждет ответа JSON:

   * `true` означает, что входные данные допустимы.
   * `false`, `undefined` или `null` означают, что входные данные недопустимы. Вывод стандартного сообщения об ошибке
   * Все прочие значения означают, что входные данные недопустимы. Вывод строки как настраиваемого сообщения об ошибке.

   Ниже приведен пример метода действия, который возвращает настраиваемое сообщение об ошибке.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. В классе модели пометьте свойство атрибутом `[Remote]`, указывающим метод действия проверки, как показано в следующем примере.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   Атрибут `[Remote]` находится в пространстве имен `Microsoft.AspNetCore.Mvc`.
   
### <a name="additional-fields"></a>Дополнительные поля

Свойство `AdditionalFields` атрибута `[Remote]` позволяет проверять сочетания полей с данными на сервере. Например, если бы в модели `User` были свойства `FirstName` и `LastName`, могла бы возникнуть необходимость проверить, нет ли уже пользователя с такой парой имен. В следующем примере показано использование `AdditionalFields`.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

`AdditionalFields` можно явно присвоить строкам "FirstName" и "LastName", но использование оператора [nameof](/dotnet/csharp/language-reference/keywords/nameof) упрощает дальнейший рефакторинг. Методу действия для этой проверки необходимо принимать аргументы `firstName` и `lastName`

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Когда пользователь вводит имя или фамилию, JavaScript выполняет удаленный вызов, чтобы увидеть, будет ли эта пара принята.

Чтобы проверить несколько дополнительных полей, их следует указывать в виде списка с разделителями-запятыми. Например, чтобы добавить в модель свойство `MiddleName`, задайте атрибут `[Remote]`, как показано в следующем примере.

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, как и все аргументы атрибутов, должен представлять собой константное выражение. Поэтому не следует использовать [интерполированную строку](/dotnet/csharp/language-reference/keywords/interpolated-strings) или вызов <xref:System.String.Join*>для инициализации `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Альтернативы для встроенных атрибутов

Если вам нужна проверка, которую не предоставляют встроенные атрибуты, вы можете следующее.

* [Создать настраиваемые атрибуты](#custom-attributes).
* [Реализовать IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Настраиваемые атрибуты

Для сценариев, где не годятся встроенные атрибуты проверки, можно создать настраиваемые атрибуты. Создайте класс, наследуемый от <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, и переопределите метод <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.

Метод `IsValid` принимает объект с именем *value*, который является входными данными для проверки. Перегрузка также принимает объект `ValidationContext`, который предоставляет дополнительные сведения, такие как экземпляр модели, созданный с помощью привязки модели.

В следующем примере проверяется, что дата выпуска фильмов в *классическом* жанре задана не позднее указанного года. Атрибут `[ClassicMovie]`:

* выполняется только на сервере.
* Для классических фильмов проверяет дату выпуска:

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

Приведенная выше переменная `movie` представляет объект `Movie`, который содержит данные из переданной формы. Если проверка завершается неудачно, возвращается `ValidationResult` с сообщением об ошибке.

## <a name="ivalidatableobject"></a>IValidatableObject

Предыдущий пример работает только с типами `Movie`. Другой вариант для проверки на уровне класса — реализация `IValidatableObject` в классе модели, как показано в следующем примере.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Проверка узлов верхнего уровня

Узлы верхнего уровня содержат:

* параметры действия;
* свойства контроллера;
* параметры обработчика страниц;
* свойства страничной модели.

Проверка привязанных к модели узлов верхнего уровня осуществляется наряду с проверкой свойств модели. В следующем примере, взятом из примера приложения, метод `VerifyPhone` использует класс <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> для проверки параметра действия `phone`.

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Узлы верхнего уровня могут применять класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> с атрибутами проверки. В следующем примере, взятом из примера приложения, метод `CheckAge` указывает, что при отправке формы параметр `age` должен быть привязан из строки запроса.

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

На странице "Check Age" (Проверка возраста) (*CheckAge.cshtml*) находятся две формы. Первая форма отправляет значение `Age`, равное `99`, в виде параметра строки запроса: `https://localhost:5001/Users/CheckAge?Age=99`.

Если из строки запроса отправлен параметр `age` в правильном формате, форма проходит проверку.

Вторая форма на странице "Check Age" (Проверка возраста) отправляет значение `Age` в теле запроса, и проверка завершается сбоем. Ошибка привязки связана с тем, что параметр `age` должен поступать из строки запроса.

## <a name="maximum-errors"></a>Максимальное количество ошибок

При достижении максимального количества ошибок (по умолчанию 200) проверка прекращается. Это число можно изменить с помощью следующего кода в `Startup.ConfigureServices`:

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a>Максимальная рекурсия

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> проходит через граф объектов в проверяемой модели. У глубоких моделей, содержащих бесконечную рекурсию, в ходе проверки может произойти переполнение стека. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) предоставляет способ остановить проверку до превышения настроенной глубины рекурсии обхода. Значение `MvcOptions.MaxValidationDepth` по умолчанию —32.

## <a name="automatic-short-circuit"></a>Автоматическое сокращение

Проверка автоматически сокращается (пропускается), если граф модели не требует проверки. К числу объектов, которые среда выполнения пропускает при проверке, относятся коллекции примитивов (такие как `byte[]`, `string[]`, `Dictionary<string, string>`) и сложные графы объектов, которые не имеют проверяющих элементов управления.

## <a name="disable-validation"></a>Отключение проверки

Чтобы отключить проверку, сделайте следующее.

1. Создайте реализацию интерфейса `IObjectModelValidator`, которая не помечает поля как недопустимые.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. Добавьте следующий код в `Startup.ConfigureServices` для замены реализации `IObjectModelValidator` по умолчанию в контейнере внедрения зависимостей.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

По-прежнему могут отображаться ошибки состояния модели, которые идут из привязки модели.

## <a name="client-side-validation"></a>Проверка на стороне клиента

Проверка на стороне клиента не позволяет отправлять форму, пока ее данные не будут допустимыми. При нажатии кнопки "Отправить" выполняется код JavaScript, который либо отправляет форму, либо выводит сообщения об ошибках.

Проверка на стороне клиента позволяет избежать ненужного кругового захода на сервер при наличии ошибки ввода в форме. Ссылки на следующий скрипт в *_Layout.cshtml* и *_ValidationScriptsPartial.cshtml* поддерживают проверку на стороне клиента.

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

Скрипт [ненавязчивой проверки JQuery](https://github.com/aspnet/jquery-validation-unobtrusive) — это пользовательская библиотека Microsoft, которая основана на популярном подключаемом модуле [проверки JQuery](https://jqueryvalidation.org/) . Без скрипта ненавязчивой проверки jQuery одну и ту же логику проверки приходилось бы реализовывать в двух местах: в атрибутах проверки для свойств модели на стороне сервера, а затем еще раз в скриптах на стороне клиента. Вместо этого [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и [вспомогательные функции HTML](xref:mvc/views/overview) могут использовать атрибуты проверки и метаданные типов из свойств модели для обработки атрибутов `data-` HTML 5 в элементах форм, требующих проверки. ненавязчивая проверка jQuery анализирует `data-` атрибуты и передает логику в проверку jQuery, фактически "копируя" логику проверки на стороне сервера на клиент. Ошибки проверки могут выводиться в клиенте с помощью вспомогательных функций тегов, как показано ниже.

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

Приведенные выше вспомогательные функции тегов отрисовывают следующий код HTML:

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

Обратите внимание на то, что атрибуты `data-` в выходных данных HTML соответствуют атрибутам проверки для свойства `Movie.ReleaseDate`. Атрибут `data-val-required` содержит сообщение об ошибке, которое выводится, если пользователь не заполнил поле даты выхода. ненавязчивая проверка jQuery передает это значение методу, [требуемому](https://jqueryvalidation.org/required-method/) для проверки JQuery (), который затем отображает это сообщение в сопровождающем **\<span>** элементе.

Проверка типа данных основана на типе свойства в .NET, если его не переопределяет атрибут `[DataType]`. Браузеры имеют свои сообщения об по умолчанию, но пакет ненавязчивой проверки jQuery может переопределять эти сообщения. Атрибуты и подклассы `[DataType]`, такие как `[EmailAddress]`, позволяют указать сообщение об ошибке.

## <a name="unobtrusive-validation"></a>Ненавязчивая проверка

См. сведения о ненавязчивой проверке в этой [проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/1111).

### <a name="add-validation-to-dynamic-forms"></a>Добавление проверки к динамическим формам

ненавязчивая проверка jQuery передает логику и параметры проверки в jQuery при первой загрузке страницы. Поэтому динамически создаваемые формы не подвергаются проверке автоматически. Чтобы включить проверку, необходимо указать, что скрипт ненавязчивой проверки jQuery должен анализировать динамическую форму сразу после ее создания. Например, приведенный ниже код показывает, как можно настроить проверку на стороне клиента для формы, добавленной посредством AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

Метод `$.validator.unobtrusive.parse()` принимает селектор jQuery в качестве единственного аргумента. Этот метод предписывает скрипту ненавязчивой проверки jQuery анализировать атрибуты `data-` форм в этом селекторе. Значения этих атрибутов затем передаются в подключаемый модуль проверки jQuery.

### <a name="add-validation-to-dynamic-controls"></a>Добавление проверки к динамическим элементам управления

Метод `$.validator.unobtrusive.parse()` обрабатывает всю форму, а не отдельные динамически создаваемые элементы управления, такие как `<input>` и `<select/>`. Для повторной обработки формы удалите данные проверки, которые были добавлены при анализе формы ранее, как показано в следующем примере:

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Настраиваемая проверка на стороне клиента

Настраиваемая проверка на стороне клиента выполняется путем создания `data-` HTML-атрибутов, которые работают с пользовательским адаптером проверки JQuery. В следующем примере кода адаптера используются атрибуты `[ClassicMovie]` и `[ClassicMovieWithClientValidator]`, которые были введены ранее в этой статье.

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

Сведения о том, как создавать адаптеры, см. в [документации по проверке jQuery](https://jqueryvalidation.org/documentation/).

Использование адаптера для заданного поля инициируется атрибутами `data-`, которые:

* помечают поле как проходящее проверку (`data-val="true"`);
* указывают имя правила проверки и текст сообщения об ошибке (например, `data-val-rulename="Error message."`);
* указывают любые дополнительные параметры, в которых нуждается проверяющий элемент управления (например, `data-val-rulename-param1="value"`).

В следующем примере показаны атрибуты `data-` для нашего атрибута `ClassicMovie` из примера приложения.

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

Как отмечалось ранее, [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и [вспомогательные методы HTML](xref:mvc/views/overview) используют сведения из атрибутов проверки для подготовки к отрисовке атрибутов `data-`. Существует два варианта для написания кода, который приводит к созданию настраиваемых атрибутов HTML `data-`.

* Создайте класс, производный от `AttributeAdapterBase<TAttribute>`, и класс, реализующий `IValidationAttributeAdapterProvider`; зарегистрируйте атрибут и его адаптер в функции внедрения зависимостей. Этот метод следует за [участником с единственной обязанностью](https://wikipedia.org/wiki/Single_responsibility_principle) в том, что код проверки, связанный с сервером и клиентом, выделен в отдельные классы. Адаптер также имеет следующее преимущество: так как он зарегистрирован в функции внедрения зависимостей, для него при необходимости доступны другие службы в ней.
* Реализуйте `IClientModelValidator` в вашем классе `ValidationAttribute`. Этот метод стоит использовать, если атрибут не выполняет проверку на стороне сервера и не нуждается в службах из функции внедрения зависимостей.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter для проверки на стороне клиента

Этот метод отрисовки атрибутов `data-` в формате HTML используется атрибутом `ClassicMovie` в примере приложения. Чтобы добавить клиентскую проверку с помощью этого метода, сделайте следующее.

1. Создайте класс адаптера атрибута для настраиваемого атрибута проверки. Создайте класс, производный от <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>. Создайте метод `AddValidation`, который добавляет атрибуты `data-` для выводимых данных, как показано в следующем примере.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. Создайте класс поставщика адаптера, который реализует <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. В методе `GetAttributeAdapter` передайте настраиваемый атрибут в конструктор адаптера, как показано в следующем примере.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. Зарегистрируйте поставщик адаптера для внедрения зависимостей в `Startup.ConfigureServices`.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator для проверки на стороне клиента

Этот метод отрисовки атрибутов `data-` в формате HTML используется атрибутом `ClassicMovieWithClientValidator` в примере приложения. Чтобы добавить клиентскую проверку с помощью этого метода, сделайте следующее.

* В настраиваемом атрибуте проверки реализуйте интерфейс `IClientModelValidator` и создайте метод `AddValidation`. В метод `AddValidation` добавьте атрибуты `data-` для проверки, как показано в следующем примере.

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a>Отключение проверки на стороне клиента

Следующий код отключает проверку клиента на Razor страницах:

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

Другие параметры отключения проверки на стороне клиента:

* Закомментируйте ссылку на `_ValidationScriptsPartial` во всех файлах с расширением *CSHTML*.
* Удалите содержимое файла *Pages\Shared\_ValidationScriptsPartial.cshtml*.

Предыдущий подход не помешает проверке на стороне клиента ASP.NET Core Identity Razor библиотеки классов. Для получения дополнительной информации см. <xref:security/authentication/scaffold-identity>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Пространство имен System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Привязка модели](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этой статье объясняется, как проверить ввод пользователя в ASP.NET Core приложении MVC или Razor pages.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Состояние модели

Состояние модели представляет ошибки, создаваемые двумя подсистемами: привязкой модели и проверкой модели. Ошибки, поступающие из [привязки модели](model-binding.md), чаще всего представляют собой ошибки преобразования данных (например, x вводится в поле, которое ожидает целое число). Проверка модели проводится после привязки модели и сообщает об ошибках, при которых данные не соответствуют бизнес-правилам (например, 0 вводится в поле, которое ожидает оценку от 1 до 5).

Привязка и проверка модели выполняются до выполнения действия контроллера или Razor метода обработчика страниц. Веб-приложение отвечает за проверку `ModelState.IsValid` и реагирует соответствующим образом. Веб-приложения обычно повторно отображают страницы с сообщением об ошибке.

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

Контроллерам веб-API не нужно проверять `ModelState.IsValid` при наличии атрибута `[ApiController]`. В этом случае автоматически возвращается ответ HTTP 400, содержащий сведения об ошибке, если состояние модели недопустимо. Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Перезапуск проверки

Проверка выполняется автоматически, однако может потребоваться повторить ее вручную. Например, можно вычислить значение свойства и повторно выполнить проверку после установки свойства в вычисляемое значение. Для повторной проверки вызовите метод `TryValidateModel`, как показано ниже.

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Атрибуты проверки

Атрибуты проверки позволяют задать правила проверки для свойств модели. В следующем примере из [примера приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample) показан класс модели, который помечается с помощью атрибутов проверки. `[ClassicMovie]` является настраиваемым атрибутом проверки; остальные являются встроенными. Не показан `[ClassicMovie2]`, который демонстрирует альтернативный способ реализации настраиваемого атрибута.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Встроенные атрибуты

К встроенным атрибутам проверки относятся:

* `[CreditCard]`: Проверяет, что свойство имеет формат кредитной карты.
* `[Compare]`: Проверяет соответствие двух свойств в модели. Например, файл *Register.cshtml.cs* использует `[Compare]` для проверки совпадений двух введенных паролей. [Формирование Identity шаблонов](xref:security/authentication/scaffold-identity) для просмотра кода регистрации.
* `[EmailAddress]`: Проверяет, что свойство имеет формат электронной почты.
* `[Phone]`: Проверяет, что свойство имеет формат номера телефона.
* `[Range]`: Проверяет, попадает значение свойства в указанный диапазон.
* `[RegularExpression]`: Проверяет соответствие значения свойства указанному регулярному выражению.
* `[Required]`: Проверяет, что поле не имеет значение null. Дополнительные сведения о поведении этого атрибута см. в разделе [ `[Required]` Attribute](#required-attribute) .
* `[StringLength]`: Проверяет, что значение свойства строки не превышает заданное ограничение длины.
* `[Url]`: Проверяет, что свойство имеет формат URL-адреса.
* `[Remote]`: Проверяет входные данные на клиенте, вызывая метод действия на сервере. Дополнительные сведения о поведении этого атрибута см. в разделе [ `[Remote]` Attribute](#remote-attribute) .

При использовании атрибута `[RegularExpression]` с проверкой на стороне клиента регулярное выражение выполняется в JavaScript в клиенте. Это означает, что будет использоваться поведение сопоставления [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior). Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/corefx/issues/42487).

Полный перечень атрибутов проверки можно найти в пространстве имен [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).

### <a name="error-messages"></a>Сообщения об ошибках

Атрибуты проверки позволяют указать сообщение об ошибке, которое будет отображаться, если входные данные недопустимы. Пример:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

На внутреннем уровне атрибуты вызывают `String.Format` с заполнителем для имени поля и иногда дополнительным заполнителями. Пример:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

При применении к свойству `Name` сообщение об ошибке, созданное в приведенном выше коде, имело бы вид Name length must be between 6 and 8 (Длина имени должна быть от 6 до 8).

Чтобы узнать, какие параметры передаются в `String.Format` для сообщения об ошибке определенного атрибута, см. раздел [Исходный код DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>Атрибут [Required]

По умолчанию система проверки рассматривает не допускающие значение NULL параметры или свойства так, как если бы они имели атрибут `[Required]`. [Типы значений](/dotnet/csharp/language-reference/keywords/value-types), например `decimal` и `int`, не поддерживают значение NULL.

### <a name="required-validation-on-the-server"></a>Проверка [Required] на сервере

На сервере обязательное значение считается отсутствующим, если свойство имеет значение NULL. Поле, не допускающее значения NULL, всегда является допустимым, и сообщение об ошибке атрибута [Required] никогда не выводится.

Тем не менее привязка модели для ненулевого свойства может завершиться ошибкой, приводящей к сообщению об ошибке, например `The value '' is invalid`. Чтобы задать настраиваемое сообщение об ошибке во время проверки не допускающих значения NULL типов на стороне сервера, у вас есть следующие варианты.

* Сделать поле допускающим значение NULL (например, `decimal?` вместо `decimal`). [Допускает значения NULL \<T> ](/dotnet/csharp/programming-guide/nullable-types/) типы значений обрабатываются как стандартные типы, допускающие значение null.
* Указать сообщение об ошибке по умолчанию для использования в привязке модели, как показано в следующем примере.

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  Дополнительные сведения об ошибках привязки модели, у которых можно задать сообщение по умолчанию, см. в разделе <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.

### <a name="required-validation-on-the-client"></a>Проверка [Required] на клиенте

Типы и строки, не допускающие значение NULL, обрабатываются на клиенте не так, как на сервере. На клиенте:

* Значение считается присутствующим только в том случае, если для него вводятся данные. Таким образом, проверка на стороне клиента обрабатывает типы, не допускающие значение NULL, так же, как обнуляемые типы.
* Пробелы в поле строки считаются допустимыми входными данными при проверке методом jQuery [required](https://jqueryvalidation.org/required-method/). Проверка на стороне сервера считает, что обязательное строковое поле недопустимо, если введены только пробелы.

Как отмечалось ранее, не допускающие значение NULL типы рассматриваются как имеющие атрибут `[Required]`. Это означает, что вы получаете проверку на стороне клиента, даже если не применять атрибут `[Required]`. Но если вы не используете атрибут, вы получаете сообщение об ошибке по умолчанию. Чтобы задать настраиваемое сообщение об ошибке, используйте атрибут.

## <a name="remote-attribute"></a>Атрибут [Remote]

Атрибут `[Remote]` реализует проверку на стороне клиента, в ходе которой требуется вызвать метод на сервере, чтобы определить, является ли допустимым поле ввода. Например, приложению может потребоваться проверить, занято ли имя пользователя.

Для реализации удаленной проверки сделайте следующее.

1. Создайте для вызова из JavaScript метод действия.  Метод проверки jQuery [remote](https://jqueryvalidation.org/remote-method/) ожидает ответ JSON.

   * `"true"` означает, что входные данные допустимы.
   * `"false"`, `undefined` или `null` означают, что входные данные недопустимы.  Вывод стандартного сообщения об ошибке
   * Все прочие значения означают, что входные данные недопустимы. Вывод строки как настраиваемого сообщения об ошибке.

   Ниже приведен пример метода действия, который возвращает настраиваемое сообщение об ошибке.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. В классе модели пометьте свойство атрибутом `[Remote]`, указывающим метод действия проверки, как показано в следующем примере.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   Атрибут `[Remote]` находится в пространстве имен `Microsoft.AspNetCore.Mvc`. Установите пакет NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures), если вы не используете метапакет `Microsoft.AspNetCore.App` или `Microsoft.AspNetCore.All`.
   
### <a name="additional-fields"></a>Дополнительные поля

Свойство `AdditionalFields` атрибута `[Remote]` позволяет проверять сочетания полей с данными на сервере. Например, если бы в модели `User` были свойства `FirstName` и `LastName`, могла бы возникнуть необходимость проверить, нет ли уже пользователя с такой парой имен. В следующем примере показано использование `AdditionalFields`.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields` можно явно присвоить строкам `"FirstName"` и `"LastName"`, но использование оператора [nameof](/dotnet/csharp/language-reference/keywords/nameof) упрощает дальнейший рефакторинг. Методу действия для этой проверки необходимо принимать аргументы для имени и фамилии.

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Когда пользователь вводит имя или фамилию, JavaScript выполняет удаленный вызов, чтобы увидеть, будет ли эта пара принята.

Чтобы проверить несколько дополнительных полей, их следует указывать в виде списка с разделителями-запятыми. Например, чтобы добавить в модель свойство `MiddleName`, задайте атрибут `[Remote]`, как показано в следующем примере.

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, как и все аргументы атрибутов, должен представлять собой константное выражение. Поэтому не следует использовать [интерполированную строку](/dotnet/csharp/language-reference/keywords/interpolated-strings) или вызов <xref:System.String.Join*>для инициализации `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Альтернативы для встроенных атрибутов

Если вам нужна проверка, которую не предоставляют встроенные атрибуты, вы можете следующее.

* [Создать настраиваемые атрибуты](#custom-attributes).
* [Реализовать IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Настраиваемые атрибуты

Для сценариев, где не годятся встроенные атрибуты проверки, можно создать настраиваемые атрибуты. Создайте класс, наследуемый от <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, и переопределите метод <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.

Метод `IsValid` принимает объект с именем *value*, который является входными данными для проверки. Перегрузка также принимает объект `ValidationContext`, который предоставляет дополнительные сведения, такие как экземпляр модели, созданный с помощью привязки модели.

В следующем примере проверяется, что дата выпуска фильмов в *классическом* жанре задана не позднее указанного года. Атрибут `[ClassicMovie2]` сначала проверяет жанр и продолжает проверку, только если он *классический*. У фильмов, попавших в классику, система проверяет даты выпуска, чтобы убедиться в том, что она не является более поздней, чем ограничение, переданное в конструктор атрибута.

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

Приведенная выше переменная `movie` представляет объект `Movie`, который содержит данные из переданной формы. Метод `IsValid` проверяет дату и жанр. После успешной проверки `IsValid` возвращает код `ValidationResult.Success`. Если проверка завершается неудачно, возвращается `ValidationResult` с сообщением об ошибке.

## <a name="ivalidatableobject"></a>IValidatableObject

Предыдущий пример работает только с типами `Movie`. Другой вариант для проверки на уровне класса — реализация `IValidatableObject` в классе модели, как показано в следующем примере.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Проверка узлов верхнего уровня

Узлы верхнего уровня содержат:

* параметры действия;
* свойства контроллера;
* параметры обработчика страниц;
* свойства страничной модели.

Проверка привязанных к модели узлов верхнего уровня осуществляется наряду с проверкой свойств модели. В следующем примере, взятом из примера приложения, метод `VerifyPhone` использует класс <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> для проверки параметра действия `phone`.

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Узлы верхнего уровня могут применять класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> с атрибутами проверки. В следующем примере, взятом из примера приложения, метод `CheckAge` указывает, что при отправке формы параметр `age` должен быть привязан из строки запроса.

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

На странице "Check Age" (Проверка возраста) (*CheckAge.cshtml*) находятся две формы. Первая форма отправляет значение `Age`, равное `99`, в виде строки запроса: `https://localhost:5001/Users/CheckAge?Age=99`.

Если из строки запроса отправлен параметр `age` в правильном формате, форма проходит проверку.

Вторая форма на странице "Check Age" (Проверка возраста) отправляет значение `Age` в теле запроса, и проверка завершается сбоем. Ошибка привязки связана с тем, что параметр `age` должен поступать из строки запроса.

При работе с `CompatibilityVersion.Version_2_1` или более поздней версией проверка узла верхнего уровня по умолчанию включена. В противном случае проверка узла верхнего уровня отключена. Параметр по умолчанию можно переопределить, задав свойство <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> в (`Startup.ConfigureServices`), как показано ниже.

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>Максимальное количество ошибок

При достижении максимального количества ошибок (по умолчанию 200) проверка прекращается. Это число можно изменить с помощью следующего кода в `Startup.ConfigureServices`:

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>Максимальная рекурсия

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> проходит через граф объектов в проверяемой модели. У моделей, которые очень глубоки или содержат бесконечную рекурсию, в ходе проверки может произойти переполнение стека. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) предоставляет способ остановить проверку до превышения настроенной глубины рекурсии обхода. Значение `MvcOptions.MaxValidationDepth` по умолчанию — 32 при работе в `CompatibilityVersion.Version_2_2` или более поздней версии. Для более ранних версий значение равно NULL; это означает отсутствие ограничения глубины.

## <a name="automatic-short-circuit"></a>Автоматическое сокращение

Проверка автоматически сокращается (пропускается), если граф модели не требует проверки. К числу объектов, которые среда выполнения пропускает при проверке, относятся коллекции примитивов (такие как `byte[]`, `string[]`, `Dictionary<string, string>`) и сложные графы объектов, которые не имеют проверяющих элементов управления.

## <a name="disable-validation"></a>Отключение проверки

Чтобы отключить проверку, сделайте следующее.

1. Создайте реализацию интерфейса `IObjectModelValidator`, которая не помечает поля как недопустимые.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. Добавьте следующий код в `Startup.ConfigureServices` для замены реализации `IObjectModelValidator` по умолчанию в контейнере внедрения зависимостей.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

По-прежнему могут отображаться ошибки состояния модели, которые идут из привязки модели.

## <a name="client-side-validation"></a>Проверка на стороне клиента

Проверка на стороне клиента не позволяет отправлять форму, пока ее данные не будут допустимыми. При нажатии кнопки "Отправить" выполняется код JavaScript, который либо отправляет форму, либо выводит сообщения об ошибках.

Проверка на стороне клиента позволяет избежать ненужного кругового захода на сервер при наличии ошибки ввода в форме. Ссылки на следующий скрипт в *_Layout.cshtml* и *_ValidationScriptsPartial.cshtml* поддерживают проверку на стороне клиента.

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

Скрипт [ненавязчивой проверки jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) — это настраиваемая интерфейсная библиотека Майкрософт, которая основана на популярном подключаемом модуле [jQuery Validate](https://jqueryvalidation.org/). Без скрипта ненавязчивой проверки jQuery одну и ту же логику проверки приходилось бы реализовывать в двух местах: в атрибутах проверки для свойств модели на стороне сервера, а затем еще раз в скриптах на стороне клиента. Вместо этого [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и [вспомогательные функции HTML](xref:mvc/views/overview) могут использовать атрибуты проверки и метаданные типов из свойств модели для обработки атрибутов `data-` HTML 5 в элементах форм, требующих проверки. Скрипт ненавязчивой проверки jQuery анализирует эти атрибуты `data-` и передает логику в подключаемый модуль jQuery Validate, по сути копируя логику проверки на стороне сервера в клиент. Ошибки проверки могут выводиться в клиенте с помощью вспомогательных функций тегов, как показано ниже.

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Приведенные выше вспомогательные функции тегов отрисовывают следующий код HTML.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Обратите внимание на то, что атрибуты `data-` в выходных данных HTML соответствуют атрибутам проверки для свойства `ReleaseDate`. Атрибут `data-val-required` содержит сообщение об ошибке, которое выводится, если пользователь не заполнил поле даты выхода. ненавязчивая проверка jQuery передает это значение методу jQuery Validate [Required ()](https://jqueryvalidation.org/required-method/) , который затем отображает это сообщение в сопровождающем **\<span>** элементе.

Проверка типа данных основана на типе свойства в .NET, если его не переопределяет атрибут `[DataType]`. Браузеры имеют свои сообщения об по умолчанию, но пакет ненавязчивой проверки jQuery может переопределять эти сообщения. Атрибуты и подклассы `[DataType]`, такие как `[EmailAddress]`, позволяют указать сообщение об ошибке.

### <a name="add-validation-to-dynamic-forms"></a>Добавление проверки к динамическим формам

Скрипт ненавязчивой проверки jQuery передает логику и параметры проверки в подключаемый модуль jQuery Validate при первой загрузке страницы. Поэтому динамически создаваемые формы не подвергаются проверке автоматически. Чтобы включить проверку, необходимо указать, что скрипт ненавязчивой проверки jQuery должен анализировать динамическую форму сразу после ее создания. Например, приведенный ниже код показывает, как можно настроить проверку на стороне клиента для формы, добавленной посредством AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

Метод `$.validator.unobtrusive.parse()` принимает селектор jQuery в качестве единственного аргумента. Этот метод предписывает скрипту ненавязчивой проверки jQuery анализировать атрибуты `data-` форм в этом селекторе. Значения этих атрибутов затем передаются в подключаемый модуль jQuery Validate.

### <a name="add-validation-to-dynamic-controls"></a>Добавление проверки к динамическим элементам управления

Метод `$.validator.unobtrusive.parse()` обрабатывает всю форму, а не отдельные динамически создаваемые элементы управления, такие как `<input>` и `<select/>`. Для повторной обработки формы удалите данные проверки, которые были добавлены при анализе формы ранее, как показано в следующем примере:

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Настраиваемая проверка на стороне клиента

Настраиваемая проверка на стороне клиента выполняется путем создания атрибутов HTML `data-`, которые работают с настраиваемым адаптером проверки jQuery. В следующем примере кода адаптера используются атрибуты `ClassicMovie` и `ClassicMovie2`, которые были введены ранее в этой статье.

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

Сведения о способах создания адаптеров см. в [документации по проверкам jQuery](https://jqueryvalidation.org/documentation/).

Использование адаптера для заданного поля инициируется атрибутами `data-`, которые:

* помечают поле как проходящее проверку (`data-val="true"`);
* указывают имя правила проверки и текст сообщения об ошибке (например, `data-val-rulename="Error message."`);
* указывают любые дополнительные параметры, в которых нуждается проверяющий элемент управления (например, `data-val-rulename-parm1="value"`).

В следующем примере показаны атрибуты `data-` для нашего атрибута `ClassicMovie` из примера приложения.

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

Как отмечалось ранее, [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и [вспомогательные методы HTML](xref:mvc/views/overview) используют сведения из атрибутов проверки для подготовки к отрисовке атрибутов `data-`. Существует два варианта для написания кода, который приводит к созданию настраиваемых атрибутов HTML `data-`.

* Создайте класс, производный от `AttributeAdapterBase<TAttribute>`, и класс, реализующий `IValidationAttributeAdapterProvider`; зарегистрируйте атрибут и его адаптер в функции внедрения зависимостей. Этот метод следует за [участником с единственной обязанностью](https://wikipedia.org/wiki/Single_responsibility_principle) в том, что код проверки, связанный с сервером и клиентом, выделен в отдельные классы. Адаптер также имеет следующее преимущество: так как он зарегистрирован в функции внедрения зависимостей, для него при необходимости доступны другие службы в ней.
* Реализуйте `IClientModelValidator` в вашем классе `ValidationAttribute`. Этот метод стоит использовать, если атрибут не выполняет проверку на стороне сервера и не нуждается в службах из функции внедрения зависимостей.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter для проверки на стороне клиента

Этот метод отрисовки атрибутов `data-` в формате HTML используется атрибутом `ClassicMovie` в примере приложения. Чтобы добавить клиентскую проверку с помощью этого метода, сделайте следующее.

1. Создайте класс адаптера атрибута для настраиваемого атрибута проверки. Создайте класс, производный от <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>. Создайте метод `AddValidation`, который добавляет атрибуты `data-` для выводимых данных, как показано в следующем примере.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Создайте класс поставщика адаптера, который реализует <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. В методе `GetAttributeAdapter` передайте настраиваемый атрибут в конструктор адаптера, как показано в следующем примере.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Зарегистрируйте поставщик адаптера для внедрения зависимостей в `Startup.ConfigureServices`.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator для проверки на стороне клиента

Этот метод отрисовки атрибутов `data-` в формате HTML используется атрибутом `ClassicMovie2` в примере приложения. Чтобы добавить клиентскую проверку с помощью этого метода, сделайте следующее.

* В настраиваемом атрибуте проверки реализуйте интерфейс `IClientModelValidator` и создайте метод `AddValidation`. В метод `AddValidation` добавьте атрибуты `data-` для проверки, как показано в следующем примере.

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>Отключение проверки на стороне клиента

Следующий код отключает клиентскую проверку в представлениях MVC.

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

И на Razor страницах:

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

Еще один вариант отключения клиентской проверки клиента — закомментировать ссылку на `_ValidationScriptsPartial` в вашем файле *.cshtml*.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Пространство имен System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Привязка модели](model-binding.md)

::: moniker-end
