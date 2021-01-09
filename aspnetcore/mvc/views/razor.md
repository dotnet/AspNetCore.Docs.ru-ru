---
title: Справочник по синтаксису Razor для ASP.NET Core
author: rick-anderson
description: Сведения о Razor синтаксисе разметки для внедрения серверного кода на веб-страницы.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: cb9ffab19062bf726dd519c782d502f76e372073
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058289"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a>Razor Справочник по синтаксису для ASP.NET Core

По [Рик Андерсон (](https://twitter.com/RickAndMSFT), [Кузнецова Маллен](https://twitter.com/ntaylormullen)и " [викарел](https://github.com/Rabadash8820) "

Razor — это синтаксис разметки для внедрения серверного кода на веб-страницы. RazorСинтаксис состоит из Razor разметки, C# и HTML. Файлы, содержащие Razor обычно, имеют расширение *. cshtml* . Razorтакже находится в файлах [ Razor Components](xref:blazor/components/index) (*. Razor*).

## <a name="rendering-html"></a>Отрисовка HTML

Языком по умолчанию Razor является HTML. Отрисовка HTML из Razor разметки не отличается от отрисовки HTML из HTML-файла. Разметка HTML в *CSHTML* - Razor файлах отображается на сервере без изменений.

## <a name="no-locrazor-syntax"></a>Синтаксис Razor

Razor поддерживает C# и использует `@` символ для перехода от HTML к C#. Razor Вычисляет выражения C# и отображает их в выходных данных HTML.

Если `@` за символом следует [ Razor зарезервированное ключевое слово](#razor-reserved-keywords), он перемещается в Razor разметку, относящуюся к конкретному. В противном случае он переходит на обычный C#.

Чтобы экранировать `@` символ в Razor разметке, используйте второй `@` символ:

```cshtml
<p>@@Username</p>
```

Код будет отображен в HTML с одним символом `@`:

```html
<p>@Username</p>
```

HTML-атрибуты и содержимое, включающие адреса электронной почты, не расценивают символ `@` как символ перехода. Адреса электронной почты в следующем примере не затрагиваются путем Razor синтаксического анализа:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a>Неявные Razor выражения

Неявные Razor выражения начинаются с `@` следующего кода C#:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Неявные выражения не должны содержать пробелов. Исключением является ключевое слово C# `await`. Если оператор C# имеет четкое окончание, пробелы вставлять можно:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Неявные выражения **не могут** содержать универсальные шаблоны C#, так как символы в угловых скобках (`<>`) интерпретируются как тег HTML. Следующий код является **недопустимым**:

```cshtml
<p>@GenericMethod<int>()</p>
```

Приведенный выше код вызывает ошибку компилятора примерно следующего вида:

* Элемент "int" не был закрыт. Все элементы должны быть самозакрывающимися или иметь соответствующий закрывающий тег.
* Не удается преобразовать группу методов "GenericMethod" в не являющийся делегатом тип "object". Предполагалось вызывать этот метод?

Вызовы универсальных методов должны быть заключены в [явное Razor выражение](#explicit-razor-expressions) или [ Razor блок кода](#razor-code-blocks).

## <a name="explicit-no-locrazor-expressions"></a>Явные Razor выражения

Явные Razor выражения состоят из `@` символа с сбалансированной круглой скобкой. Для визуализации времени на прошлой неделе Razor используется следующая разметка:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Любое содержимое в скобках `@()` вычисляется и отображается в выходных данных.

Неявные выражения, описанные в предыдущем разделе, обычно не содержат пробелов. В следующем коде из значения текущего времени неделя не вычитается:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Код отображает следующий HTML:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Явные выражения позволяют объединять результат своего выполнения с дополнительным текстом:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Без явного выражения `<p>Age@joe.Age</p>` обрабатывается как адрес электронной почты, и на выходе отображается `<p>Age@joe.Age</p>`. Если же текст написан как явное выражение, то вы получите `<p>Age33</p>`.

Вы можете использовать явные выражения для отображения выходных данных из универсальных методов в файлах *CSHTML*. В следующем примере показано, как исправить ошибку, показанную ранее и вызванную скобками в универсальном шаблоне C#. Код записывается как явное выражение:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Кодирование выражений

Выражения C#, которые имеют строковое выходное значение, кодируются в формате HTML. Выражения C#, которые имеют значение `IHtmlContent`, обрабатываются непосредственно с помощью `IHtmlContent.WriteTo`. Выражения C#, которые не имеют выходное значение `IHtmlContent`, преобразуются в строку с помощью `ToString` и кодируются перед обработкой.

```cshtml
@("<span>Hello World</span>")
```

Приведенный выше код отображает следующий HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML-код отображается в браузере как обычный текст:

&lt;охват &gt; Hello World &lt; /span&gt;

Выходные данные `HtmlHelper.Raw` не кодируются, но отображаются в виде разметки HTML.

> [!WARNING]
> Использование `HtmlHelper.Raw` с непроверенными входными данными пользователя представляет угрозу безопасности. Эти входные данные могут содержать вредоносный код JavaScript или другие эксплойты. Очистка вводимых пользователем данных является сложной задачей. Старайтесь не использовать `HtmlHelper.Raw` с такими данными.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Код отображает следующий HTML:

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a>Razor блоки кода

Razor блоки кода начинаются с `@` и заключаются в `{}` . В отличие от выражений код C# внутри блоков кода не обрабатывается. Блоки кода и выражения в представлении используют общую область и определяются в следующем порядке:

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

Код отображает следующий HTML:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

В блоках кода объявите [локальные функции](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) с помощью разметки для использования в качестве методов создания шаблонов:

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

Код отображает следующий HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Неявные переходы

Языком по умолчанию в блоке кода является C#, но Razor страница может перейти обратно в HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Явный переход с разделителями

Чтобы определить подраздел блока кода, который должен визуализировать HTML, заключите символы для отрисовки с помощью Razor `<text>` тега:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Используйте этот способ для отрисовки HTML, не заключенного в HTML-теги. Без HTML или Razor тега Razor возникает ошибка времени выполнения.

Тег `<text>` хорошо подходит для контроля пробелов при отрисовке содержимого:

* Отрисовывается только содержимое между тегами `<text>`.
* В выходных данных HTML пробелы до или после тега `<text>` не отображаются.

### <a name="explicit-line-transition"></a>Явный перенос строки

Для отрисовки оставшейся части строки в виде HTML внутри блока кода используйте синтаксис `@:`:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Без `@:` кода в коде Razor создается ошибка времени выполнения.

Дополнительные `@` символы в Razor файле могут вызвать ошибки компилятора в инструкциях далее в блоке. Эти ошибки компилятора может быть трудно проанализировать, так как ошибка фактически возникает раньше, чем указано. Чаще всего эта ошибка появляется после объединения множества неявных или явных выражений в один блок кода.

## <a name="control-structures"></a>Управляющие структуры

Управляющие структуры являются расширением блоков кода. Все аспекты блоков кода (переход на разметку, встроенный код C#) также относятся к следующим структурам.

### <a name="conditionals-if-else-if-else-and-switch"></a>Условные выражения `@if, else if, else, and @switch`

`@if` контролирует, когда нужно запускать код:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

Для `else` и `else if` символ `@` не требуется:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

В следующей разметке показано использование оператора switch:

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Циклов `@for, @foreach, @while, and @do while`

Операторы выполнения цикла позволяют выполнять отрисовку шаблонного HTML. Отрисовка списка людей:

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

Поддерживаются следующие операторы выполнения цикла:

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>Составной оператор `@using`

В C# оператор `using` позволяет обеспечить использование какого-то объекта. В Razor для создания вспомогательных функций HTML, содержащих дополнительное содержимое, используется тот же механизм. В следующем коде вспомогательные функции HTML используют оператор `@using` для создания тега `<form>`:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

Обработка исключений выполняется так же, как в C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor имеет возможность защищать критические разделы с помощью инструкций Lock:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Комментарии

Razor поддерживает комментарии C# и HTML:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Код отображает следующий HTML:

```html
<!-- HTML comment -->
```

Razor перед отрисовкой веб-страницы комментарии удаляются с сервера. Razor использует `@*  *@` для разделения комментариев. Следующий код закомментирован, поэтому сервер не отрисовывает разметку:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Директивы

Razor директивы представлены неявными выражениями с зарезервированными ключевыми словами после `@` символа. Как правило, директива изменяет способ анализа представления или открывает доступ к дополнительным функциям.

Понимание того Razor , как формирует код для представления, упрощает понимание принципов работы директив.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Код создает класс, аналогичный следующему:

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

Далее в этой статье описывается, как просмотреть созданный класс в разделе [изучение Razor класса C#, созданного для представления](#inspect-the-razor-c-class-generated-for-a-view) .

### `@attribute`

Директива `@attribute` добавляет данный атрибут к классу созданной страницы или представления. В следующем примере добавляется атрибут `[Authorize]`:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

*Этот сценарий применим только к Razor компонентам (. Razor).*

`@code`Блок позволяет [ Razor компоненту](xref:blazor/components/index) добавлять в компонент элементы C# (поля, свойства и методы):

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Для Razor компонентов `@code` является псевдонимом [`@functions`](#functions) и рекомендуется для `@functions` . Допускается более одного блока `@code`.

::: moniker-end

### `@functions`

Директива `@functions` позволяет добавлять элементы C# (поля, свойства и методы) в создаваемый класс:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

В [ Razor компонентах](xref:blazor/components/index)используйте `@code` `@functions` для добавления членов C#.

::: moniker-end

Например:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Код создает следующую разметку HTML:

```html
<div>From method: Hello</div>
```

Следующий код является созданным Razor классом C#:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

Методы `@functions` служат в качестве методов создания шаблонов при наличии разметки:

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

Код отображает следующий HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

Директива `@implements` реализует интерфейс для созданного класса.

В следующем примере реализуется <xref:System.IDisposable?displayProperty=fullName>, чтобы можно было вызывать метод <xref:System.IDisposable.Dispose*>:

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### `@inherits`

Директива `@inherits` позволяет полностью управлять классом, которому наследует представление:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Следующий код является пользовательским Razor типом страницы:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

В представлении отображается `CustomText`:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Код отображает следующий HTML:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` и `@inherits` могут использоваться в одном представлении. `@inherits` может находиться в файле *_ViewImports.cshtml*, который импортируется представлением:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Следующий код показывает пример строго типизированного представления:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Если передать в модель "rick@contoso.com", представление создает следующую разметку HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

`@inject`Директива позволяет Razor странице внедрить службу из [контейнера службы](xref:fundamentals/dependency-injection) в представление. Дополнительные сведения: [Внедрение зависимостей в представления](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### `@layout`

*Этот сценарий применим только к Razor компонентам (. Razor).*

`@layout`Директива задает макет для маршрутизируемых Razor компонентов, имеющих [`@page`](#page) директиву. Компоненты макета используются, чтобы избежать дублирования и несогласованности кода. Для получения дополнительной информации см. <xref:blazor/layouts>.

::: moniker-end

### `@model`

*Этот сценарий применим только к представлениям и Razor страницам MVC (. cshtml).*

Директива `@model` определяет тип модели, передаваемой в представление или страницу:

```cshtml
@model TypeNameOfModel
```

В ASP.NET Core приложении MVC или Razor pages, созданном с учетными записями отдельных пользователей, *представления/учетная запись/имя входа. cshtml* содержит следующее объявление модели:

```cshtml
@model LoginViewModel
```

Созданный класс наследует от `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor предоставляет `Model` свойство для доступа к модели, передаваемой в представление:

```cshtml
<div>The Login Email: @Model.Email</div>
```

Директива `@model` задает тип свойства `Model`. Директива указывает `T` в `RazorPage<T>` — созданном классе, на основе которого создается производное представление. Если директива `@model` не указана, свойство `Model` имеет тип `dynamic`. Дополнительные сведения см. [в разделе модели со строгой типизацией и @model ключевое слово](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### `@namespace`

Директива `@namespace`:

* Задает пространство имен класса созданной Razor страницы, представления MVC или Razor компонента.
* Устанавливает корневые производные пространства имен страниц, представлений или классов компонентов из ближайшего файла импорта в дереве каталогов, *_ViewImports. cshtml* (представления или страницы) или *_Imports. Razor* ( Razor компоненты).

```cshtml
@namespace Your.Namespace.Here
```

Для Razor примера страниц, показанного в следующей таблице:

* Каждая страница импортирует *Pages/_ViewImports.cshtml*.
* Файл *Pages/_ViewImports.cshtml* содержит `@namespace Hello.World`.
* Каждая страница имеет `Hello.World` в качестве корня пространства имен.

| Страница                                        | Пространство имен                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/index. cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Предыдущие связи применяются к файлам импорта, которые используются в представлениях и Razor компонентах MVC.

Если у нескольких файлов импорта есть директива `@namespace`, для задания корневого пространства имен используется файл, ближайший к странице, компоненту или представлению в дереве каталогов.

Если папка *EvenMorePages* в предыдущем примере содержит файл импорта с `@namespace Another.Planet` (или же файл *Pages/MorePages/EvenMorePages/Page.cshtml* содержит `@namespace Another.Planet`), результат отображается в следующей таблице.

| Страница                                        | Пространство имен               |
| ------------------------------------------- | ----------------------- |
| *Pages/index. cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

Директива `@page` имеет различные эффекты в зависимости от типа файла, в котором она используется. Директива:

* В *CSHTML* -файле указывает, что файл является Razor страницей. Дополнительные сведения см. в разделе [Пользовательские маршруты](xref:razor-pages/index#custom-routes) и <xref:razor-pages/index>.
* Указывает, что Razor компонент должен выполнять запросы напрямую. Для получения дополнительной информации см. <xref:blazor/fundamentals/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

`@page`Директива в первой строке файла *CSHTML* указывает на то, что файл является Razor страницей. Для получения дополнительной информации см. <xref:razor-pages/index>.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

*Этот сценарий применяется только к Razor компонентам ( `.razor` ).*

Если задано значение `false` (по умолчанию), пробелы в отображаемой разметке Razor компонентов ( `.razor` ) удаляются, если:

* Они находятся в начале или конце элемента.
* Они находятся в начале или конце параметра `RenderFragment`. Например, когда дочернее содержимое передается другому компоненту.
* Они находятся в начале или конце блока кода C#, например `@if` и `@foreach`.

::: moniker-end

### `@section`

*Этот сценарий применим только к представлениям и Razor страницам MVC (. cshtml).*

`@section`Директива используется совместно с [ Razor разметкой MVC и Pages](xref:mvc/views/layout) , позволяя представлениям или страницам отображать содержимое в различных частях HTML-страницы. Для получения дополнительной информации см. <xref:mvc/views/layout>.

### `@using`

Директива `@using` добавляет директиву C# `using` в созданное представление:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

В [ Razor компонентах](xref:blazor/components/index) `@using` также управляет тем, какие компоненты находятся в области.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Атрибуты директивы

Razor атрибуты директивы представлены неявными выражениями с зарезервированными ключевыми словами после `@` символа. Атрибут директивы обычно изменяет способ синтаксического анализа элемента или предоставляет различные функциональные возможности.

### `@attributes`

*Этот сценарий применим только к Razor компонентам (. Razor).*

`@attributes` позволяет компоненту обрабатывать необъявленные атрибуты. Для получения дополнительной информации см. <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

### `@bind`

*Этот сценарий применим только к Razor компонентам (. Razor).*

Привязка данных в компонентах выполняется с помощью атрибута `@bind`. Для получения дополнительной информации см. <xref:blazor/components/data-binding>.

### `@on{EVENT}`

*Этот сценарий применим только к Razor компонентам (. Razor).*

Razor предоставляет функции обработки событий для компонентов. Для получения дополнительной информации см. <xref:blazor/components/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

*Этот сценарий применим только к Razor компонентам (. Razor).*

Запрещает выполнение действия по умолчанию для события.

### `@on{EVENT}:stopPropagation`

*Этот сценарий применим только к Razor компонентам (. Razor).*

Останавливает распространение события.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

*Этот сценарий применим только к Razor компонентам (. Razor).*

Атрибут директивы `@key` заставляет алгоритм сравнения компонентов гарантировать сохранение элементов или компонентов на основе значения ключа. Для получения дополнительной информации см. <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.

### `@ref`

*Этот сценарий применим только к Razor компонентам (. Razor).*

Ссылки на компоненты (`@ref`) предоставляют способ ссылаться на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра. Для получения дополнительной информации см. <xref:blazor/components/index#capture-references-to-components>.

### `@typeparam`

*Этот сценарий применим только к Razor компонентам (. Razor).*

Директива `@typeparam` объявляет параметр универсального типа для созданного класса компонента. Для получения дополнительной информации см. <xref:blazor/components/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a>Шаблонные Razor делегаты

Razor шаблоны позволяют определить фрагмент пользовательского интерфейса в следующем формате:

```cshtml
@<tag>...</tag>
```

В следующем примере показано, как указать шаблонный Razor делегат в качестве <xref:System.Func%602> . [Динамический тип](/dotnet/csharp/programming-guide/types/using-type-dynamic) указывается для параметра метода, инкапсулируемого делегатом. [Тип объекта](/dotnet/csharp/language-reference/keywords/object) указывается в качестве возвращаемого значения делегата. Этот шаблон используется с <xref:System.Collections.Generic.List%601>объекта `Pet`, имеющим свойство `Name`.

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

Шаблон отрисовывается с использованием `pets`, предоставляемого оператором `foreach`:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Отображенные выходные данные:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Встроенный шаблон также можно указать Razor в качестве аргумента для метода. В следующем примере `Repeat` метод получает Razor шаблон. Метод использует этот шаблон для создания HTML-содержимого с повторениями элементов из списка:

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

С использованием списка домашних животных из предыдущего примера метод `Repeat` вызывается следующим образом:

* <xref:System.Collections.Generic.List%601> объекта `Pet`.
* Количество повторений для каждого домашнего животного.
* Встроенный шаблон, используемый для перечисления элементов неупорядоченного списка.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Отображенные выходные данные:

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a>Вспомогательные функции тегов

*Этот сценарий применим только к представлениям и Razor страницам MVC (. cshtml).*

Существует три директивы, которые относятся к [вспомогательным функциям тегов](xref:mvc/views/tag-helpers/intro).

| Директива | Функция |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Делает вспомогательные функции тегов доступными в представлении. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Удаляет из представления вспомогательные функции тегов, добавленные ранее. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Задает префикс тега, который активирует поддержку вспомогательной функции тега и ее использования в явном виде. |

## <a name="no-locrazor-reserved-keywords"></a>Razor зарезервированные ключевые слова

### <a name="no-locrazor-keywords"></a>Razor словами

* `page` (Требуется ASP.NET Core 2,1 или более поздней версии)
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* `helper` (В настоящее время не поддерживается ASP.NET Core)

Razor Ключевые слова экранированы с помощью `@(Razor Keyword)` (например, `@(functions)` ).

### <a name="c-no-locrazor-keywords"></a>RazorКлючевые слова C#

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

RazorКлючевые слова C# должны быть двойным образом экранированы с помощью `@(@C# Razor Keyword)` (например, `@(@case)` ). Первый заключается в `@` Escape- Razor анализе. а второй `@` — для обхода C#.

### <a name="reserved-keywords-not-used-by-no-locrazor"></a>Зарезервированные ключевые слова не используются Razor

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a>Проверка Razor класса C#, созданного для представления

::: moniker range=">= aspnetcore-2.1"

В пакет SDK для .NET Core 2,1 или более поздней версии [ Razor пакет SDK](xref:razor-pages/sdk) обрабатывает компиляцию Razor файлов. При сборке проекта Razor пакет SDK создает *obj/<build_configuration>/<target_framework_moniker>/ Razor* каталог в корневом каталоге проекта. Структура каталогов в *Razor* каталоге отражает структуру каталогов проекта.

Рассмотрим следующую структуру каталогов в проекте ASP.NET Core 2,1 Razor страниц, предназначенном для .NET Core 2,1:

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

При сборке проекта в конфигурации *Отладка* создается следующий каталог *obj*:

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

Чтобы просмотреть созданный класс для *pages/index. cshtml*, откройте объект *obj/Debug/netcoreapp 2.1/ Razor /Пажес/индекс.г.кштмл.КС*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Добавьте в MVC-проект ASP.NET следующий класс:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

В `Startup.ConfigureServices` переопределите класс `RazorTemplateEngine`, добавленный MVC, классом `CustomTemplateEngine`:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Установите точку останова в операторе `return csharpDocument;` класса `CustomTemplateEngine`. Когда выполнение программы остановится в этой точке, просмотрите значение `generatedCode`.

![Представление generatedCode в визуализаторе текста](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Поиск данных в представлениях и учет регистра

RazorОбработчик представлений выполняет поиск представлений с учетом регистра. Однако фактический поиск зависит от используемой файловой системы.

* Источники в виде файлов:
  * В операционных системах, файловые системы которых не учитывают регистр (например, Windows), поиск поставщика физических файлов не зависит от регистра. Например, поиск по `return View("Test")` выводит совпадения */Views/Home/Test.cshtml*, */Views/home/test.cshtml* и другие варианты с различными сочетаниями регистра.
  * В файловых системах, учитывающих регистр (например, в Linux, OSX и где используется `EmbeddedFileProvider`), поиск выполняется с учетом регистра. Например, поиск по `return View("Test")` дает точное совпадение */Views/Home/Test.cshtml*.
* Предварительно скомпилированные представления: в ASP.NET Core 2.0 и более поздних версиях поиск в предварительно скомпилированных представлениях выполняется без учета регистра во всех операционных системах. Это поведение аналогично поведению поставщика физических файлов в Windows. Если два предварительно скомпилированных представления отличаются только регистром, результат поиска является недетерминированным.

Разработчикам рекомендуется использовать для файлов и каталогов тот же регистр, что и для:

* имен областей, контроллеров и действий;
* Razor См.

Совпадающий регистр гарантирует, что развертываемые службы смогут находить свои представления вне зависимости от используемой файловой системы.

## <a name="additional-resources"></a>Дополнительные ресурсы

[Введение в веб-программирование ASP.NET с помощью Razor Синтаксис](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) предоставляет множество примеров программирования с помощью Razor синтаксиса.
