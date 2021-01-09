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
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="c9b08-103">Razor Справочник по синтаксису для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9b08-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="c9b08-104">По [Рик Андерсон (](https://twitter.com/RickAndMSFT), [Кузнецова Маллен](https://twitter.com/ntaylormullen)и " [викарел](https://github.com/Rabadash8820) "</span><span class="sxs-lookup"><span data-stu-id="c9b08-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="c9b08-105">Razor — это синтаксис разметки для внедрения серверного кода на веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="c9b08-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="c9b08-106">RazorСинтаксис состоит из Razor разметки, C# и HTML.</span><span class="sxs-lookup"><span data-stu-id="c9b08-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="c9b08-107">Файлы, содержащие Razor обычно, имеют расширение *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c9b08-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="c9b08-108">Razorтакже находится в файлах [ Razor Components](xref:blazor/components/index) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="c9b08-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="c9b08-109">Отрисовка HTML</span><span class="sxs-lookup"><span data-stu-id="c9b08-109">Rendering HTML</span></span>

<span data-ttu-id="c9b08-110">Языком по умолчанию Razor является HTML.</span><span class="sxs-lookup"><span data-stu-id="c9b08-110">The default Razor language is HTML.</span></span> <span data-ttu-id="c9b08-111">Отрисовка HTML из Razor разметки не отличается от отрисовки HTML из HTML-файла.</span><span class="sxs-lookup"><span data-stu-id="c9b08-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="c9b08-112">Разметка HTML в *CSHTML* - Razor файлах отображается на сервере без изменений.</span><span class="sxs-lookup"><span data-stu-id="c9b08-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="c9b08-113">Синтаксис Razor</span><span class="sxs-lookup"><span data-stu-id="c9b08-113">Razor syntax</span></span>

<span data-ttu-id="c9b08-114">Razor поддерживает C# и использует `@` символ для перехода от HTML к C#.</span><span class="sxs-lookup"><span data-stu-id="c9b08-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="c9b08-115">Razor Вычисляет выражения C# и отображает их в выходных данных HTML.</span><span class="sxs-lookup"><span data-stu-id="c9b08-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="c9b08-116">Если `@` за символом следует [ Razor зарезервированное ключевое слово](#razor-reserved-keywords), он перемещается в Razor разметку, относящуюся к конкретному.</span><span class="sxs-lookup"><span data-stu-id="c9b08-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="c9b08-117">В противном случае он переходит на обычный C#.</span><span class="sxs-lookup"><span data-stu-id="c9b08-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="c9b08-118">Чтобы экранировать `@` символ в Razor разметке, используйте второй `@` символ:</span><span class="sxs-lookup"><span data-stu-id="c9b08-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="c9b08-119">Код будет отображен в HTML с одним символом `@`:</span><span class="sxs-lookup"><span data-stu-id="c9b08-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="c9b08-120">HTML-атрибуты и содержимое, включающие адреса электронной почты, не расценивают символ `@` как символ перехода.</span><span class="sxs-lookup"><span data-stu-id="c9b08-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="c9b08-121">Адреса электронной почты в следующем примере не затрагиваются путем Razor синтаксического анализа:</span><span class="sxs-lookup"><span data-stu-id="c9b08-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="c9b08-122">Неявные Razor выражения</span><span class="sxs-lookup"><span data-stu-id="c9b08-122">Implicit Razor expressions</span></span>

<span data-ttu-id="c9b08-123">Неявные Razor выражения начинаются с `@` следующего кода C#:</span><span class="sxs-lookup"><span data-stu-id="c9b08-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="c9b08-124">Неявные выражения не должны содержать пробелов. Исключением является ключевое слово C# `await`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="c9b08-125">Если оператор C# имеет четкое окончание, пробелы вставлять можно:</span><span class="sxs-lookup"><span data-stu-id="c9b08-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="c9b08-126">Неявные выражения **не могут** содержать универсальные шаблоны C#, так как символы в угловых скобках (`<>`) интерпретируются как тег HTML.</span><span class="sxs-lookup"><span data-stu-id="c9b08-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="c9b08-127">Следующий код является **недопустимым**:</span><span class="sxs-lookup"><span data-stu-id="c9b08-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="c9b08-128">Приведенный выше код вызывает ошибку компилятора примерно следующего вида:</span><span class="sxs-lookup"><span data-stu-id="c9b08-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="c9b08-129">Элемент "int" не был закрыт.</span><span class="sxs-lookup"><span data-stu-id="c9b08-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="c9b08-130">Все элементы должны быть самозакрывающимися или иметь соответствующий закрывающий тег.</span><span class="sxs-lookup"><span data-stu-id="c9b08-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="c9b08-131">Не удается преобразовать группу методов "GenericMethod" в не являющийся делегатом тип "object".</span><span class="sxs-lookup"><span data-stu-id="c9b08-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="c9b08-132">Предполагалось вызывать этот метод?</span><span class="sxs-lookup"><span data-stu-id="c9b08-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="c9b08-133">Вызовы универсальных методов должны быть заключены в [явное Razor выражение](#explicit-razor-expressions) или [ Razor блок кода](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="c9b08-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="c9b08-134">Явные Razor выражения</span><span class="sxs-lookup"><span data-stu-id="c9b08-134">Explicit Razor expressions</span></span>

<span data-ttu-id="c9b08-135">Явные Razor выражения состоят из `@` символа с сбалансированной круглой скобкой.</span><span class="sxs-lookup"><span data-stu-id="c9b08-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="c9b08-136">Для визуализации времени на прошлой неделе Razor используется следующая разметка:</span><span class="sxs-lookup"><span data-stu-id="c9b08-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="c9b08-137">Любое содержимое в скобках `@()` вычисляется и отображается в выходных данных.</span><span class="sxs-lookup"><span data-stu-id="c9b08-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="c9b08-138">Неявные выражения, описанные в предыдущем разделе, обычно не содержат пробелов.</span><span class="sxs-lookup"><span data-stu-id="c9b08-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="c9b08-139">В следующем коде из значения текущего времени неделя не вычитается:</span><span class="sxs-lookup"><span data-stu-id="c9b08-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="c9b08-140">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="c9b08-141">Явные выражения позволяют объединять результат своего выполнения с дополнительным текстом:</span><span class="sxs-lookup"><span data-stu-id="c9b08-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="c9b08-142">Без явного выражения `<p>Age@joe.Age</p>` обрабатывается как адрес электронной почты, и на выходе отображается `<p>Age@joe.Age</p>`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="c9b08-143">Если же текст написан как явное выражение, то вы получите `<p>Age33</p>`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="c9b08-144">Вы можете использовать явные выражения для отображения выходных данных из универсальных методов в файлах *CSHTML*.</span><span class="sxs-lookup"><span data-stu-id="c9b08-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="c9b08-145">В следующем примере показано, как исправить ошибку, показанную ранее и вызванную скобками в универсальном шаблоне C#.</span><span class="sxs-lookup"><span data-stu-id="c9b08-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="c9b08-146">Код записывается как явное выражение:</span><span class="sxs-lookup"><span data-stu-id="c9b08-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="c9b08-147">Кодирование выражений</span><span class="sxs-lookup"><span data-stu-id="c9b08-147">Expression encoding</span></span>

<span data-ttu-id="c9b08-148">Выражения C#, которые имеют строковое выходное значение, кодируются в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="c9b08-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="c9b08-149">Выражения C#, которые имеют значение `IHtmlContent`, обрабатываются непосредственно с помощью `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="c9b08-150">Выражения C#, которые не имеют выходное значение `IHtmlContent`, преобразуются в строку с помощью `ToString` и кодируются перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="c9b08-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="c9b08-151">Приведенный выше код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="c9b08-152">HTML-код отображается в браузере как обычный текст:</span><span class="sxs-lookup"><span data-stu-id="c9b08-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="c9b08-153">&lt;охват &gt; Hello World &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="c9b08-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="c9b08-154">Выходные данные `HtmlHelper.Raw` не кодируются, но отображаются в виде разметки HTML.</span><span class="sxs-lookup"><span data-stu-id="c9b08-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="c9b08-155">Использование `HtmlHelper.Raw` с непроверенными входными данными пользователя представляет угрозу безопасности.</span><span class="sxs-lookup"><span data-stu-id="c9b08-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="c9b08-156">Эти входные данные могут содержать вредоносный код JavaScript или другие эксплойты.</span><span class="sxs-lookup"><span data-stu-id="c9b08-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="c9b08-157">Очистка вводимых пользователем данных является сложной задачей.</span><span class="sxs-lookup"><span data-stu-id="c9b08-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="c9b08-158">Старайтесь не использовать `HtmlHelper.Raw` с такими данными.</span><span class="sxs-lookup"><span data-stu-id="c9b08-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="c9b08-159">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="c9b08-160">Razor блоки кода</span><span class="sxs-lookup"><span data-stu-id="c9b08-160">Razor code blocks</span></span>

<span data-ttu-id="c9b08-161">Razor блоки кода начинаются с `@` и заключаются в `{}` .</span><span class="sxs-lookup"><span data-stu-id="c9b08-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="c9b08-162">В отличие от выражений код C# внутри блоков кода не обрабатывается.</span><span class="sxs-lookup"><span data-stu-id="c9b08-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="c9b08-163">Блоки кода и выражения в представлении используют общую область и определяются в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="c9b08-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="c9b08-164">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9b08-165">В блоках кода объявите [локальные функции](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) с помощью разметки для использования в качестве методов создания шаблонов:</span><span class="sxs-lookup"><span data-stu-id="c9b08-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="c9b08-166">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="c9b08-167">Неявные переходы</span><span class="sxs-lookup"><span data-stu-id="c9b08-167">Implicit transitions</span></span>

<span data-ttu-id="c9b08-168">Языком по умолчанию в блоке кода является C#, но Razor страница может перейти обратно в HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="c9b08-169">Явный переход с разделителями</span><span class="sxs-lookup"><span data-stu-id="c9b08-169">Explicit delimited transition</span></span>

<span data-ttu-id="c9b08-170">Чтобы определить подраздел блока кода, который должен визуализировать HTML, заключите символы для отрисовки с помощью Razor `<text>` тега:</span><span class="sxs-lookup"><span data-stu-id="c9b08-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="c9b08-171">Используйте этот способ для отрисовки HTML, не заключенного в HTML-теги.</span><span class="sxs-lookup"><span data-stu-id="c9b08-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="c9b08-172">Без HTML или Razor тега Razor возникает ошибка времени выполнения.</span><span class="sxs-lookup"><span data-stu-id="c9b08-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="c9b08-173">Тег `<text>` хорошо подходит для контроля пробелов при отрисовке содержимого:</span><span class="sxs-lookup"><span data-stu-id="c9b08-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="c9b08-174">Отрисовывается только содержимое между тегами `<text>`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="c9b08-175">В выходных данных HTML пробелы до или после тега `<text>` не отображаются.</span><span class="sxs-lookup"><span data-stu-id="c9b08-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="c9b08-176">Явный перенос строки</span><span class="sxs-lookup"><span data-stu-id="c9b08-176">Explicit line transition</span></span>

<span data-ttu-id="c9b08-177">Для отрисовки оставшейся части строки в виде HTML внутри блока кода используйте синтаксис `@:`:</span><span class="sxs-lookup"><span data-stu-id="c9b08-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="c9b08-178">Без `@:` кода в коде Razor создается ошибка времени выполнения.</span><span class="sxs-lookup"><span data-stu-id="c9b08-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="c9b08-179">Дополнительные `@` символы в Razor файле могут вызвать ошибки компилятора в инструкциях далее в блоке.</span><span class="sxs-lookup"><span data-stu-id="c9b08-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="c9b08-180">Эти ошибки компилятора может быть трудно проанализировать, так как ошибка фактически возникает раньше, чем указано.</span><span class="sxs-lookup"><span data-stu-id="c9b08-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="c9b08-181">Чаще всего эта ошибка появляется после объединения множества неявных или явных выражений в один блок кода.</span><span class="sxs-lookup"><span data-stu-id="c9b08-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="c9b08-182">Управляющие структуры</span><span class="sxs-lookup"><span data-stu-id="c9b08-182">Control structures</span></span>

<span data-ttu-id="c9b08-183">Управляющие структуры являются расширением блоков кода.</span><span class="sxs-lookup"><span data-stu-id="c9b08-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="c9b08-184">Все аспекты блоков кода (переход на разметку, встроенный код C#) также относятся к следующим структурам.</span><span class="sxs-lookup"><span data-stu-id="c9b08-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="c9b08-185">Условные выражения `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="c9b08-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="c9b08-186">`@if` контролирует, когда нужно запускать код:</span><span class="sxs-lookup"><span data-stu-id="c9b08-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="c9b08-187">Для `else` и `else if` символ `@` не требуется:</span><span class="sxs-lookup"><span data-stu-id="c9b08-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="c9b08-188">В следующей разметке показано использование оператора switch:</span><span class="sxs-lookup"><span data-stu-id="c9b08-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="c9b08-189">Циклов `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="c9b08-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="c9b08-190">Операторы выполнения цикла позволяют выполнять отрисовку шаблонного HTML.</span><span class="sxs-lookup"><span data-stu-id="c9b08-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="c9b08-191">Отрисовка списка людей:</span><span class="sxs-lookup"><span data-stu-id="c9b08-191">To render a list of people:</span></span>

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

<span data-ttu-id="c9b08-192">Поддерживаются следующие операторы выполнения цикла:</span><span class="sxs-lookup"><span data-stu-id="c9b08-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="c9b08-193">Составной оператор `@using`</span><span class="sxs-lookup"><span data-stu-id="c9b08-193">Compound `@using`</span></span>

<span data-ttu-id="c9b08-194">В C# оператор `using` позволяет обеспечить использование какого-то объекта.</span><span class="sxs-lookup"><span data-stu-id="c9b08-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="c9b08-195">В Razor для создания вспомогательных функций HTML, содержащих дополнительное содержимое, используется тот же механизм.</span><span class="sxs-lookup"><span data-stu-id="c9b08-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="c9b08-196">В следующем коде вспомогательные функции HTML используют оператор `@using` для создания тега `<form>`:</span><span class="sxs-lookup"><span data-stu-id="c9b08-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="c9b08-197">Обработка исключений выполняется так же, как в C#:</span><span class="sxs-lookup"><span data-stu-id="c9b08-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="c9b08-198">Razor имеет возможность защищать критические разделы с помощью инструкций Lock:</span><span class="sxs-lookup"><span data-stu-id="c9b08-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="c9b08-199">Комментарии</span><span class="sxs-lookup"><span data-stu-id="c9b08-199">Comments</span></span>

<span data-ttu-id="c9b08-200">Razor поддерживает комментарии C# и HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="c9b08-201">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="c9b08-202">Razor перед отрисовкой веб-страницы комментарии удаляются с сервера.</span><span class="sxs-lookup"><span data-stu-id="c9b08-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="c9b08-203">Razor использует `@*  *@` для разделения комментариев.</span><span class="sxs-lookup"><span data-stu-id="c9b08-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="c9b08-204">Следующий код закомментирован, поэтому сервер не отрисовывает разметку:</span><span class="sxs-lookup"><span data-stu-id="c9b08-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="c9b08-205">Директивы</span><span class="sxs-lookup"><span data-stu-id="c9b08-205">Directives</span></span>

<span data-ttu-id="c9b08-206">Razor директивы представлены неявными выражениями с зарезервированными ключевыми словами после `@` символа.</span><span class="sxs-lookup"><span data-stu-id="c9b08-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="c9b08-207">Как правило, директива изменяет способ анализа представления или открывает доступ к дополнительным функциям.</span><span class="sxs-lookup"><span data-stu-id="c9b08-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="c9b08-208">Понимание того Razor , как формирует код для представления, упрощает понимание принципов работы директив.</span><span class="sxs-lookup"><span data-stu-id="c9b08-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="c9b08-209">Код создает класс, аналогичный следующему:</span><span class="sxs-lookup"><span data-stu-id="c9b08-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="c9b08-210">Далее в этой статье описывается, как просмотреть созданный класс в разделе [изучение Razor класса C#, созданного для представления](#inspect-the-razor-c-class-generated-for-a-view) .</span><span class="sxs-lookup"><span data-stu-id="c9b08-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="c9b08-211">Директива `@attribute` добавляет данный атрибут к классу созданной страницы или представления.</span><span class="sxs-lookup"><span data-stu-id="c9b08-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="c9b08-212">В следующем примере добавляется атрибут `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="c9b08-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="c9b08-213">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-214">`@code`Блок позволяет [ Razor компоненту](xref:blazor/components/index) добавлять в компонент элементы C# (поля, свойства и методы):</span><span class="sxs-lookup"><span data-stu-id="c9b08-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="c9b08-215">Для Razor компонентов `@code` является псевдонимом [`@functions`](#functions) и рекомендуется для `@functions` .</span><span class="sxs-lookup"><span data-stu-id="c9b08-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="c9b08-216">Допускается более одного блока `@code`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="c9b08-217">Директива `@functions` позволяет добавлять элементы C# (поля, свойства и методы) в создаваемый класс:</span><span class="sxs-lookup"><span data-stu-id="c9b08-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9b08-218">В [ Razor компонентах](xref:blazor/components/index)используйте `@code` `@functions` для добавления членов C#.</span><span class="sxs-lookup"><span data-stu-id="c9b08-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="c9b08-219">Например:</span><span class="sxs-lookup"><span data-stu-id="c9b08-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="c9b08-220">Код создает следующую разметку HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="c9b08-221">Следующий код является созданным Razor классом C#:</span><span class="sxs-lookup"><span data-stu-id="c9b08-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9b08-222">Методы `@functions` служат в качестве методов создания шаблонов при наличии разметки:</span><span class="sxs-lookup"><span data-stu-id="c9b08-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="c9b08-223">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="c9b08-224">Директива `@implements` реализует интерфейс для созданного класса.</span><span class="sxs-lookup"><span data-stu-id="c9b08-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="c9b08-225">В следующем примере реализуется <xref:System.IDisposable?displayProperty=fullName>, чтобы можно было вызывать метод <xref:System.IDisposable.Dispose*>:</span><span class="sxs-lookup"><span data-stu-id="c9b08-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="c9b08-226">Директива `@inherits` позволяет полностью управлять классом, которому наследует представление:</span><span class="sxs-lookup"><span data-stu-id="c9b08-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="c9b08-227">Следующий код является пользовательским Razor типом страницы:</span><span class="sxs-lookup"><span data-stu-id="c9b08-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="c9b08-228">В представлении отображается `CustomText`:</span><span class="sxs-lookup"><span data-stu-id="c9b08-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="c9b08-229">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="c9b08-230">`@model` и `@inherits` могут использоваться в одном представлении.</span><span class="sxs-lookup"><span data-stu-id="c9b08-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="c9b08-231">`@inherits` может находиться в файле *_ViewImports.cshtml*, который импортируется представлением:</span><span class="sxs-lookup"><span data-stu-id="c9b08-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="c9b08-232">Следующий код показывает пример строго типизированного представления:</span><span class="sxs-lookup"><span data-stu-id="c9b08-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="c9b08-233">Если передать в модель "rick@contoso.com", представление создает следующую разметку HTML:</span><span class="sxs-lookup"><span data-stu-id="c9b08-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="c9b08-234">`@inject`Директива позволяет Razor странице внедрить службу из [контейнера службы](xref:fundamentals/dependency-injection) в представление.</span><span class="sxs-lookup"><span data-stu-id="c9b08-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="c9b08-235">Дополнительные сведения: [Внедрение зависимостей в представления](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c9b08-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="c9b08-236">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-237">`@layout`Директива задает макет для маршрутизируемых Razor компонентов, имеющих [`@page`](#page) директиву.</span><span class="sxs-lookup"><span data-stu-id="c9b08-237">The `@layout` directive specifies a layout for routable Razor components that have an [`@page`](#page) directive.</span></span> <span data-ttu-id="c9b08-238">Компоненты макета используются, чтобы избежать дублирования и несогласованности кода.</span><span class="sxs-lookup"><span data-stu-id="c9b08-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="c9b08-239">Для получения дополнительной информации см. <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="c9b08-240">*Этот сценарий применим только к представлениям и Razor страницам MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="c9b08-241">Директива `@model` определяет тип модели, передаваемой в представление или страницу:</span><span class="sxs-lookup"><span data-stu-id="c9b08-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="c9b08-242">В ASP.NET Core приложении MVC или Razor pages, созданном с учетными записями отдельных пользователей, *представления/учетная запись/имя входа. cshtml* содержит следующее объявление модели:</span><span class="sxs-lookup"><span data-stu-id="c9b08-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="c9b08-243">Созданный класс наследует от `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="c9b08-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="c9b08-244">Razor предоставляет `Model` свойство для доступа к модели, передаваемой в представление:</span><span class="sxs-lookup"><span data-stu-id="c9b08-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="c9b08-245">Директива `@model` задает тип свойства `Model`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="c9b08-246">Директива указывает `T` в `RazorPage<T>` — созданном классе, на основе которого создается производное представление.</span><span class="sxs-lookup"><span data-stu-id="c9b08-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="c9b08-247">Если директива `@model` не указана, свойство `Model` имеет тип `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="c9b08-248">Дополнительные сведения см. [в разделе модели со строгой типизацией и @model ключевое слово](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="c9b08-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="c9b08-249">Директива `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="c9b08-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="c9b08-250">Задает пространство имен класса созданной Razor страницы, представления MVC или Razor компонента.</span><span class="sxs-lookup"><span data-stu-id="c9b08-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="c9b08-251">Устанавливает корневые производные пространства имен страниц, представлений или классов компонентов из ближайшего файла импорта в дереве каталогов, *_ViewImports. cshtml* (представления или страницы) или *_Imports. Razor* ( Razor компоненты).</span><span class="sxs-lookup"><span data-stu-id="c9b08-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="c9b08-252">Для Razor примера страниц, показанного в следующей таблице:</span><span class="sxs-lookup"><span data-stu-id="c9b08-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="c9b08-253">Каждая страница импортирует *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c9b08-253">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="c9b08-254">Файл *Pages/_ViewImports.cshtml* содержит `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="c9b08-255">Каждая страница имеет `Hello.World` в качестве корня пространства имен.</span><span class="sxs-lookup"><span data-stu-id="c9b08-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="c9b08-256">Страница</span><span class="sxs-lookup"><span data-stu-id="c9b08-256">Page</span></span>                                        | <span data-ttu-id="c9b08-257">Пространство имен</span><span class="sxs-lookup"><span data-stu-id="c9b08-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="c9b08-258">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c9b08-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="c9b08-259">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c9b08-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="c9b08-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c9b08-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="c9b08-261">Предыдущие связи применяются к файлам импорта, которые используются в представлениях и Razor компонентах MVC.</span><span class="sxs-lookup"><span data-stu-id="c9b08-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="c9b08-262">Если у нескольких файлов импорта есть директива `@namespace`, для задания корневого пространства имен используется файл, ближайший к странице, компоненту или представлению в дереве каталогов.</span><span class="sxs-lookup"><span data-stu-id="c9b08-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="c9b08-263">Если папка *EvenMorePages* в предыдущем примере содержит файл импорта с `@namespace Another.Planet` (или же файл *Pages/MorePages/EvenMorePages/Page.cshtml* содержит `@namespace Another.Planet`), результат отображается в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="c9b08-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="c9b08-264">Страница</span><span class="sxs-lookup"><span data-stu-id="c9b08-264">Page</span></span>                                        | <span data-ttu-id="c9b08-265">Пространство имен</span><span class="sxs-lookup"><span data-stu-id="c9b08-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="c9b08-266">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c9b08-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="c9b08-267">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c9b08-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="c9b08-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c9b08-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9b08-269">Директива `@page` имеет различные эффекты в зависимости от типа файла, в котором она используется.</span><span class="sxs-lookup"><span data-stu-id="c9b08-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="c9b08-270">Директива:</span><span class="sxs-lookup"><span data-stu-id="c9b08-270">The directive:</span></span>

* <span data-ttu-id="c9b08-271">В *CSHTML* -файле указывает, что файл является Razor страницей.</span><span class="sxs-lookup"><span data-stu-id="c9b08-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="c9b08-272">Дополнительные сведения см. в разделе [Пользовательские маршруты](xref:razor-pages/index#custom-routes) и <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="c9b08-273">Указывает, что Razor компонент должен выполнять запросы напрямую.</span><span class="sxs-lookup"><span data-stu-id="c9b08-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="c9b08-274">Для получения дополнительной информации см. <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c9b08-275">`@page`Директива в первой строке файла *CSHTML* указывает на то, что файл является Razor страницей.</span><span class="sxs-lookup"><span data-stu-id="c9b08-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="c9b08-276">Для получения дополнительной информации см. <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

<span data-ttu-id="c9b08-277">*Этот сценарий применяется только к Razor компонентам ( `.razor` ).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-277">*This scenario only applies to Razor components (`.razor`).*</span></span>

<span data-ttu-id="c9b08-278">Если задано значение `false` (по умолчанию), пробелы в отображаемой разметке Razor компонентов ( `.razor` ) удаляются, если:</span><span class="sxs-lookup"><span data-stu-id="c9b08-278">When set to `false` (default), whitespace in the rendered markup from Razor components (`.razor`) is removed if:</span></span>

* <span data-ttu-id="c9b08-279">Они находятся в начале или конце элемента.</span><span class="sxs-lookup"><span data-stu-id="c9b08-279">Leading or trailing within an element.</span></span>
* <span data-ttu-id="c9b08-280">Они находятся в начале или конце параметра `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-280">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="c9b08-281">Например, когда дочернее содержимое передается другому компоненту.</span><span class="sxs-lookup"><span data-stu-id="c9b08-281">For example, child content passed to another component.</span></span>
* <span data-ttu-id="c9b08-282">Они находятся в начале или конце блока кода C#, например `@if` и `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-282">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="c9b08-283">*Этот сценарий применим только к представлениям и Razor страницам MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-283">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="c9b08-284">`@section`Директива используется совместно с [ Razor разметкой MVC и Pages](xref:mvc/views/layout) , позволяя представлениям или страницам отображать содержимое в различных частях HTML-страницы.</span><span class="sxs-lookup"><span data-stu-id="c9b08-284">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="c9b08-285">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-285">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="c9b08-286">Директива `@using` добавляет директиву C# `using` в созданное представление:</span><span class="sxs-lookup"><span data-stu-id="c9b08-286">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9b08-287">В [ Razor компонентах](xref:blazor/components/index) `@using` также управляет тем, какие компоненты находятся в области.</span><span class="sxs-lookup"><span data-stu-id="c9b08-287">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="c9b08-288">Атрибуты директивы</span><span class="sxs-lookup"><span data-stu-id="c9b08-288">Directive attributes</span></span>

<span data-ttu-id="c9b08-289">Razor атрибуты директивы представлены неявными выражениями с зарезервированными ключевыми словами после `@` символа.</span><span class="sxs-lookup"><span data-stu-id="c9b08-289">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="c9b08-290">Атрибут директивы обычно изменяет способ синтаксического анализа элемента или предоставляет различные функциональные возможности.</span><span class="sxs-lookup"><span data-stu-id="c9b08-290">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="c9b08-291">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-292">`@attributes` позволяет компоненту обрабатывать необъявленные атрибуты.</span><span class="sxs-lookup"><span data-stu-id="c9b08-292">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="c9b08-293">Для получения дополнительной информации см. <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-293">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="c9b08-294">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-295">Привязка данных в компонентах выполняется с помощью атрибута `@bind`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-295">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="c9b08-296">Для получения дополнительной информации см. <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-296">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="c9b08-297">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-298">Razor предоставляет функции обработки событий для компонентов.</span><span class="sxs-lookup"><span data-stu-id="c9b08-298">Razor provides event handling features for components.</span></span> <span data-ttu-id="c9b08-299">Для получения дополнительной информации см. <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-299">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="c9b08-300">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-301">Запрещает выполнение действия по умолчанию для события.</span><span class="sxs-lookup"><span data-stu-id="c9b08-301">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="c9b08-302">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-302">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-303">Останавливает распространение события.</span><span class="sxs-lookup"><span data-stu-id="c9b08-303">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="c9b08-304">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-305">Атрибут директивы `@key` заставляет алгоритм сравнения компонентов гарантировать сохранение элементов или компонентов на основе значения ключа.</span><span class="sxs-lookup"><span data-stu-id="c9b08-305">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="c9b08-306">Для получения дополнительной информации см. <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-306">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="c9b08-307">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-308">Ссылки на компоненты (`@ref`) предоставляют способ ссылаться на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра.</span><span class="sxs-lookup"><span data-stu-id="c9b08-308">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="c9b08-309">Для получения дополнительной информации см. <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-309">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="c9b08-310">*Этот сценарий применим только к Razor компонентам (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-310">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c9b08-311">Директива `@typeparam` объявляет параметр универсального типа для созданного класса компонента.</span><span class="sxs-lookup"><span data-stu-id="c9b08-311">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="c9b08-312">Для получения дополнительной информации см. <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="c9b08-312">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="c9b08-313">Шаблонные Razor делегаты</span><span class="sxs-lookup"><span data-stu-id="c9b08-313">Templated Razor delegates</span></span>

<span data-ttu-id="c9b08-314">Razor шаблоны позволяют определить фрагмент пользовательского интерфейса в следующем формате:</span><span class="sxs-lookup"><span data-stu-id="c9b08-314">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="c9b08-315">В следующем примере показано, как указать шаблонный Razor делегат в качестве <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="c9b08-315">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="c9b08-316">[Динамический тип](/dotnet/csharp/programming-guide/types/using-type-dynamic) указывается для параметра метода, инкапсулируемого делегатом.</span><span class="sxs-lookup"><span data-stu-id="c9b08-316">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="c9b08-317">[Тип объекта](/dotnet/csharp/language-reference/keywords/object) указывается в качестве возвращаемого значения делегата.</span><span class="sxs-lookup"><span data-stu-id="c9b08-317">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="c9b08-318">Этот шаблон используется с <xref:System.Collections.Generic.List%601>объекта `Pet`, имеющим свойство `Name`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-318">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="c9b08-319">Шаблон отрисовывается с использованием `pets`, предоставляемого оператором `foreach`:</span><span class="sxs-lookup"><span data-stu-id="c9b08-319">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="c9b08-320">Отображенные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="c9b08-320">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="c9b08-321">Встроенный шаблон также можно указать Razor в качестве аргумента для метода.</span><span class="sxs-lookup"><span data-stu-id="c9b08-321">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="c9b08-322">В следующем примере `Repeat` метод получает Razor шаблон.</span><span class="sxs-lookup"><span data-stu-id="c9b08-322">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="c9b08-323">Метод использует этот шаблон для создания HTML-содержимого с повторениями элементов из списка:</span><span class="sxs-lookup"><span data-stu-id="c9b08-323">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="c9b08-324">С использованием списка домашних животных из предыдущего примера метод `Repeat` вызывается следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c9b08-324">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="c9b08-325"><xref:System.Collections.Generic.List%601> объекта `Pet`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-325"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="c9b08-326">Количество повторений для каждого домашнего животного.</span><span class="sxs-lookup"><span data-stu-id="c9b08-326">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="c9b08-327">Встроенный шаблон, используемый для перечисления элементов неупорядоченного списка.</span><span class="sxs-lookup"><span data-stu-id="c9b08-327">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="c9b08-328">Отображенные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="c9b08-328">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="c9b08-329">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="c9b08-329">Tag Helpers</span></span>

<span data-ttu-id="c9b08-330">*Этот сценарий применим только к представлениям и Razor страницам MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="c9b08-330">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="c9b08-331">Существует три директивы, которые относятся к [вспомогательным функциям тегов](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="c9b08-331">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="c9b08-332">Директива</span><span class="sxs-lookup"><span data-stu-id="c9b08-332">Directive</span></span> | <span data-ttu-id="c9b08-333">Функция</span><span class="sxs-lookup"><span data-stu-id="c9b08-333">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="c9b08-334">Делает вспомогательные функции тегов доступными в представлении.</span><span class="sxs-lookup"><span data-stu-id="c9b08-334">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="c9b08-335">Удаляет из представления вспомогательные функции тегов, добавленные ранее.</span><span class="sxs-lookup"><span data-stu-id="c9b08-335">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="c9b08-336">Задает префикс тега, который активирует поддержку вспомогательной функции тега и ее использования в явном виде.</span><span class="sxs-lookup"><span data-stu-id="c9b08-336">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="c9b08-337">Razor зарезервированные ключевые слова</span><span class="sxs-lookup"><span data-stu-id="c9b08-337">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="c9b08-338">Razor словами</span><span class="sxs-lookup"><span data-stu-id="c9b08-338">Razor keywords</span></span>

* <span data-ttu-id="c9b08-339">`page` (Требуется ASP.NET Core 2,1 или более поздней версии)</span><span class="sxs-lookup"><span data-stu-id="c9b08-339">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="c9b08-340">`helper` (В настоящее время не поддерживается ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="c9b08-340">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="c9b08-341">Razor Ключевые слова экранированы с помощью `@(Razor Keyword)` (например, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="c9b08-341">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="c9b08-342">RazorКлючевые слова C#</span><span class="sxs-lookup"><span data-stu-id="c9b08-342">C# Razor keywords</span></span>

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

<span data-ttu-id="c9b08-343">RazorКлючевые слова C# должны быть двойным образом экранированы с помощью `@(@C# Razor Keyword)` (например, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="c9b08-343">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="c9b08-344">Первый заключается в `@` Escape- Razor анализе.</span><span class="sxs-lookup"><span data-stu-id="c9b08-344">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="c9b08-345">а второй `@` — для обхода C#.</span><span class="sxs-lookup"><span data-stu-id="c9b08-345">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="c9b08-346">Зарезервированные ключевые слова не используются Razor</span><span class="sxs-lookup"><span data-stu-id="c9b08-346">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="c9b08-347">Проверка Razor класса C#, созданного для представления</span><span class="sxs-lookup"><span data-stu-id="c9b08-347">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c9b08-348">В пакет SDK для .NET Core 2,1 или более поздней версии [ Razor пакет SDK](xref:razor-pages/sdk) обрабатывает компиляцию Razor файлов.</span><span class="sxs-lookup"><span data-stu-id="c9b08-348">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="c9b08-349">При сборке проекта Razor пакет SDK создает *obj/<build_configuration>/<target_framework_moniker>/ Razor* каталог в корневом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="c9b08-349">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="c9b08-350">Структура каталогов в *Razor* каталоге отражает структуру каталогов проекта.</span><span class="sxs-lookup"><span data-stu-id="c9b08-350">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="c9b08-351">Рассмотрим следующую структуру каталогов в проекте ASP.NET Core 2,1 Razor страниц, предназначенном для .NET Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="c9b08-351">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="c9b08-352">При сборке проекта в конфигурации *Отладка* создается следующий каталог *obj*:</span><span class="sxs-lookup"><span data-stu-id="c9b08-352">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

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

<span data-ttu-id="c9b08-353">Чтобы просмотреть созданный класс для *pages/index. cshtml*, откройте объект *obj/Debug/netcoreapp 2.1/ Razor /Пажес/индекс.г.кштмл.КС*.</span><span class="sxs-lookup"><span data-stu-id="c9b08-353">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="c9b08-354">Добавьте в MVC-проект ASP.NET следующий класс:</span><span class="sxs-lookup"><span data-stu-id="c9b08-354">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="c9b08-355">В `Startup.ConfigureServices` переопределите класс `RazorTemplateEngine`, добавленный MVC, классом `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="c9b08-355">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="c9b08-356">Установите точку останова в операторе `return csharpDocument;` класса `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-356">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="c9b08-357">Когда выполнение программы остановится в этой точке, просмотрите значение `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="c9b08-357">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Представление generatedCode в визуализаторе текста](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="c9b08-359">Поиск данных в представлениях и учет регистра</span><span class="sxs-lookup"><span data-stu-id="c9b08-359">View lookups and case sensitivity</span></span>

<span data-ttu-id="c9b08-360">RazorОбработчик представлений выполняет поиск представлений с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="c9b08-360">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="c9b08-361">Однако фактический поиск зависит от используемой файловой системы.</span><span class="sxs-lookup"><span data-stu-id="c9b08-361">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="c9b08-362">Источники в виде файлов:</span><span class="sxs-lookup"><span data-stu-id="c9b08-362">File based source:</span></span>
  * <span data-ttu-id="c9b08-363">В операционных системах, файловые системы которых не учитывают регистр (например, Windows), поиск поставщика физических файлов не зависит от регистра.</span><span class="sxs-lookup"><span data-stu-id="c9b08-363">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="c9b08-364">Например, поиск по `return View("Test")` выводит совпадения */Views/Home/Test.cshtml*, */Views/home/test.cshtml* и другие варианты с различными сочетаниями регистра.</span><span class="sxs-lookup"><span data-stu-id="c9b08-364">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="c9b08-365">В файловых системах, учитывающих регистр (например, в Linux, OSX и где используется `EmbeddedFileProvider`), поиск выполняется с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="c9b08-365">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="c9b08-366">Например, поиск по `return View("Test")` дает точное совпадение */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c9b08-366">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="c9b08-367">Предварительно скомпилированные представления: в ASP.NET Core 2.0 и более поздних версиях поиск в предварительно скомпилированных представлениях выполняется без учета регистра во всех операционных системах.</span><span class="sxs-lookup"><span data-stu-id="c9b08-367">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="c9b08-368">Это поведение аналогично поведению поставщика физических файлов в Windows.</span><span class="sxs-lookup"><span data-stu-id="c9b08-368">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="c9b08-369">Если два предварительно скомпилированных представления отличаются только регистром, результат поиска является недетерминированным.</span><span class="sxs-lookup"><span data-stu-id="c9b08-369">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="c9b08-370">Разработчикам рекомендуется использовать для файлов и каталогов тот же регистр, что и для:</span><span class="sxs-lookup"><span data-stu-id="c9b08-370">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="c9b08-371">имен областей, контроллеров и действий;</span><span class="sxs-lookup"><span data-stu-id="c9b08-371">Area, controller, and action names.</span></span>
* <span data-ttu-id="c9b08-372">Razor См.</span><span class="sxs-lookup"><span data-stu-id="c9b08-372">Razor Pages.</span></span>

<span data-ttu-id="c9b08-373">Совпадающий регистр гарантирует, что развертываемые службы смогут находить свои представления вне зависимости от используемой файловой системы.</span><span class="sxs-lookup"><span data-stu-id="c9b08-373">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9b08-374">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c9b08-374">Additional resources</span></span>

<span data-ttu-id="c9b08-375">[Введение в веб-программирование ASP.NET с помощью Razor Синтаксис](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) предоставляет множество примеров программирования с помощью Razor синтаксиса.</span><span class="sxs-lookup"><span data-stu-id="c9b08-375">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
