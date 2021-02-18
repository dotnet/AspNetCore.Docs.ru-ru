---
title: Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как вызывать функции JavaScript из методов .NET в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 5b28ad594567e7bfc87e15eed419bea520125654
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280307"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="1513d-103">Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="1513d-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="1513d-104">Приложение Blazor может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1513d-104">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="1513d-105">Такой подход называется *взаимодействием с JavaScript* (*JS*).</span><span class="sxs-lookup"><span data-stu-id="1513d-105">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="1513d-106">В этой статье рассматривается вызов функций JavaScript из .NET.</span><span class="sxs-lookup"><span data-stu-id="1513d-106">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="1513d-107">Сведения о том, как вызывать методы .NET из JavaScript, см. в статье <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="1513d-107">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="1513d-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1513d-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="1513d-109">Добавьте JS-файлы (теги `<script>`) перед закрывающим тегом `</body>` в `wwwroot/index.html` файле (Blazor WebAssembly) или файле `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="1513d-109">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="1513d-110">Убедитесь, что JS-файлы с методами взаимодействия JS включены перед JS-файлами платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="1513d-110">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

<span data-ttu-id="1513d-111">Для вызова JavaScript из .NET используйте абстракцию <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="1513d-111">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="1513d-112">Чтобы выполнять вызовы взаимодействия с JS, внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент.</span><span class="sxs-lookup"><span data-stu-id="1513d-112">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="1513d-113"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> принимает идентификатор функции JavaScript, которую нужно вызвать, вместе с любым числом аргументов, сериализуемых в JSON.</span><span class="sxs-lookup"><span data-stu-id="1513d-113"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="1513d-114">Идентификатор функции задается относительно глобальной области (`window`).</span><span class="sxs-lookup"><span data-stu-id="1513d-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="1513d-115">Если нужно вызвать функцию `window.someScope.someFunction`, идентификатором будет `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="1513d-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="1513d-116">Регистрировать функцию перед ее вызовом не требуется.</span><span class="sxs-lookup"><span data-stu-id="1513d-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="1513d-117">Тип возвращаемого значения `T` также должен сериализоваться в JSON.</span><span class="sxs-lookup"><span data-stu-id="1513d-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="1513d-118">Тип `T` должен соответствовать типу .NET, который лучше всего соответствует возвращаемому типу JSON.</span><span class="sxs-lookup"><span data-stu-id="1513d-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="1513d-119">Функции JavaScript, возвращающие [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), вызываются с помощью <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="1513d-119">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="1513d-120">`InvokeAsync` распаковывает обещание и возвращает значение, ожидаемое обещанием.</span><span class="sxs-lookup"><span data-stu-id="1513d-120">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="1513d-121">Для приложений Blazor Server с включенной предварительной обработкой вызвать JavaScript нельзя во время первоначальной предварительной обработки.</span><span class="sxs-lookup"><span data-stu-id="1513d-121">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="1513d-122">Вызовы взаимодействия с JavaScript должны быть отложены до тех пор, пока не будет установлено соединение с браузером.</span><span class="sxs-lookup"><span data-stu-id="1513d-122">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="1513d-123">См. раздел [Обнаружение предварительной обработки в приложении Blazor Server](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="1513d-123">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="1513d-124">Приведенный ниже пример основан на [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), декодере на базе JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1513d-124">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="1513d-125">В примере показано, как вызвать функцию JavaScript из метода C#, которая переносит требование из кода разработчика в существующий API JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1513d-125">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="1513d-126">Функция JavaScript принимает массив байтов из метода C#, декодирует его и возвращает компоненту текст для отображения.</span><span class="sxs-lookup"><span data-stu-id="1513d-126">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="1513d-127">Внутри элемента `<head>` файла `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) предоставьте функцию JavaScript, которая использует `TextDecoder` для декодирования переданного массива и возвращения декодированного значения:</span><span class="sxs-lookup"><span data-stu-id="1513d-127">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="1513d-128">Код JavaScript, например из предыдущего примера, можно также загрузить из файла JavaScript (`.js`) со ссылкой на файл скрипта:</span><span class="sxs-lookup"><span data-stu-id="1513d-128">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="1513d-129">Приведенный ниже компонент выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="1513d-129">The following component:</span></span>

* <span data-ttu-id="1513d-130">вызывает функцию JavaScript `convertArray` с помощью `JS` при нажатии кнопки компонента ( **`Convert Array`** );</span><span class="sxs-lookup"><span data-stu-id="1513d-130">Invokes the `convertArray` JavaScript function using `JS` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="1513d-131">после вызова функции JavaScript переданный массив преобразуется в строку.</span><span class="sxs-lookup"><span data-stu-id="1513d-131">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="1513d-132">Строка возвращается компоненту для отображения.</span><span class="sxs-lookup"><span data-stu-id="1513d-132">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="1513d-133">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="1513d-133">IJSRuntime</span></span>

<span data-ttu-id="1513d-134">Чтобы использовать абстракцию <xref:Microsoft.JSInterop.IJSRuntime>, можно выбрать один из описанных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="1513d-134">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="1513d-135">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент Razor (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="1513d-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="1513d-136">В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="1513d-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="1513d-137">Функция вызывается с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> и не возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="1513d-137">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="1513d-138">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в класс (`.cs`):</span><span class="sxs-lookup"><span data-stu-id="1513d-138">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="1513d-139">В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="1513d-139">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="1513d-140">Функция вызывается с помощью метода `JS.InvokeAsync` и возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="1513d-140">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="1513d-141">Для динамического создания содержимого с помощью [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) используйте атрибут `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="1513d-141">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="1513d-142">В примере клиентского приложения, используемом в этой статье, приложению доступны две функции JavaScript, которые взаимодействуют с моделью DOM для получения вводимых пользователем данных и вывода приветственного сообщения:</span><span class="sxs-lookup"><span data-stu-id="1513d-142">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="1513d-143">`showPrompt`. Создает запрос на ввод пользователем данных (имени пользователя) и возвращает имя вызвавшему объекту.</span><span class="sxs-lookup"><span data-stu-id="1513d-143">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="1513d-144">`displayWelcome`. Назначает приветственное сообщение от вызывающего объекта объекту модели DOM со значением `id`, равным `welcome`.</span><span class="sxs-lookup"><span data-stu-id="1513d-144">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="1513d-145">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="1513d-145">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="1513d-146">Поместите тег `<script>` со ссылкой на файл JavaScript в файл `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="1513d-146">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="1513d-147">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="1513d-147">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=23)]

<span data-ttu-id="1513d-148">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="1513d-148">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="1513d-149">Не помещайте тег `<script>` в файл компонента, так как тег `<script>` не может изменяться динамически.</span><span class="sxs-lookup"><span data-stu-id="1513d-149">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="1513d-150">Методы .NET взаимодействуют с функциями JavaScript в файле `exampleJsInterop.js` путем вызова <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="1513d-150">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="1513d-151">Абстракция <xref:Microsoft.JSInterop.IJSRuntime> является асинхронной для поддержки сценариев Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="1513d-151">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="1513d-152">Если вы используете приложение Blazor WebAssembly и вам нужно вызывать функцию JavaScript синхронно, выполните нисходящее приведение к <xref:Microsoft.JSInterop.IJSInProcessRuntime> и вызовите <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> вместо этого.</span><span class="sxs-lookup"><span data-stu-id="1513d-152">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="1513d-153">В большинстве библиотек взаимодействия с JS рекомендуется использовать асинхронные интерфейсы API, чтобы обеспечить доступность библиотек в любых сценариях.</span><span class="sxs-lookup"><span data-stu-id="1513d-153">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1513d-154">Сведения о включении изоляции JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) см. в разделе об [изоляции JavaScript Blazor и ссылках на объекты](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="1513d-154">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="1513d-155">Пример приложения включает в себя компонент для демонстрации взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="1513d-155">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="1513d-156">Он выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="1513d-156">The component:</span></span>

* <span data-ttu-id="1513d-157">принимает вводимые пользователем данные посредством запроса JavaScript;</span><span class="sxs-lookup"><span data-stu-id="1513d-157">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="1513d-158">возвращает текст компоненту для обработки;</span><span class="sxs-lookup"><span data-stu-id="1513d-158">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="1513d-159">вызывает еще одну функцию JavaScript, которая взаимодействует с моделью DOM для вывода приветственного сообщения.</span><span class="sxs-lookup"><span data-stu-id="1513d-159">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="1513d-160">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="1513d-160">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="1513d-161">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="1513d-161">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="1513d-162">Если функция `TriggerJsPrompt` выполняется при нажатии кнопки **`Trigger JavaScript Prompt`** , то вызывается функция JavaScript `showPrompt`, предоставленная в файле `wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="1513d-162">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="1513d-163">Функция `showPrompt` принимает введенные пользователем данные (имя пользователя), которые кодируются в HTML и возвращаются компоненту.</span><span class="sxs-lookup"><span data-stu-id="1513d-163">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="1513d-164">Компонент сохраняет имя пользователя в локальной переменной `name`.</span><span class="sxs-lookup"><span data-stu-id="1513d-164">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="1513d-165">Строка, хранящаяся в `name`, включается в приветственное сообщение, которое передается в функцию JavaScript `displayWelcome`, визуализирующую приветственное сообщение в теге заголовка.</span><span class="sxs-lookup"><span data-stu-id="1513d-165">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="1513d-166">Вызов функции void JavaScript</span><span class="sxs-lookup"><span data-stu-id="1513d-166">Call a void JavaScript function</span></span>

<span data-ttu-id="1513d-167">Используйте <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="1513d-167">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="1513d-168">для функций JavaScript, возвращающих значение [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) или [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined);</span><span class="sxs-lookup"><span data-stu-id="1513d-168">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="1513d-169">если .NET не нужно считывать результат вызова JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1513d-169">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="1513d-170">Обнаружение предварительной отрисовки в приложении Blazor Server</span><span class="sxs-lookup"><span data-stu-id="1513d-170">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="1513d-171">Получение ссылок на элементы</span><span class="sxs-lookup"><span data-stu-id="1513d-171">Capture references to elements</span></span>

<span data-ttu-id="1513d-172">В некоторых сценариях взаимодействия с JS требуются ссылки на элементы HTML.</span><span class="sxs-lookup"><span data-stu-id="1513d-172">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="1513d-173">Например, ссылка на элемент может требоваться библиотеке пользовательского интерфейса для инициализации либо необходимо вызывать командные интерфейсы API для элемента, такого как `focus` или `play`.</span><span class="sxs-lookup"><span data-stu-id="1513d-173">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="1513d-174">Для получения ссылок на элементы HTML в компоненте используйте описанный ниже подход.</span><span class="sxs-lookup"><span data-stu-id="1513d-174">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="1513d-175">Добавьте атрибут `@ref` к элементу HTML.</span><span class="sxs-lookup"><span data-stu-id="1513d-175">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="1513d-176">Определите поле типа <xref:Microsoft.AspNetCore.Components.ElementReference>, имя которого совпадает со значением атрибута `@ref`.</span><span class="sxs-lookup"><span data-stu-id="1513d-176">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="1513d-177">В следующем примере показано получение ссылки на элемент `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="1513d-177">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="1513d-178">Ссылку на элемент следует использовать только для изменения содержимого пустого элемента, который не взаимодействует с Blazor.</span><span class="sxs-lookup"><span data-stu-id="1513d-178">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="1513d-179">Этот сценарий полезен, если сторонний интерфейс API предоставляет содержимое элементу.</span><span class="sxs-lookup"><span data-stu-id="1513d-179">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="1513d-180">Так как Blazor не взаимодействует с элементом, риск конфликта между представлением Blazor элемента и моделью DOM отсутствует.</span><span class="sxs-lookup"><span data-stu-id="1513d-180">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="1513d-181">В следующем примере изменять содержимое неупорядоченного списка (`ul`) *опасно*, так как Blazor взаимодействует с моделью DOM для заполнения элементов этого списка (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="1513d-181">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="1513d-182">Если при взаимодействии с JS содержимое элемента `MyList` изменяется и Blazor пытается применить изменения к элементу, эти изменения не будут соответствовать модели DOM.</span><span class="sxs-lookup"><span data-stu-id="1513d-182">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="1513d-183"><xref:Microsoft.AspNetCore.Components.ElementReference> передается в код JavaScript через JS-взаимодействие.</span><span class="sxs-lookup"><span data-stu-id="1513d-183">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="1513d-184">Код JavaScript получает экземпляр `HTMLElement`, который может использоваться с обычными интерфейсами API DOM.</span><span class="sxs-lookup"><span data-stu-id="1513d-184">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="1513d-185">Например, в приведенном ниже коде определяется метод расширения .NET, который позволяет отправить щелчок мыши в элемент.</span><span class="sxs-lookup"><span data-stu-id="1513d-185">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="1513d-186">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="1513d-186">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1513d-187">Чтобы установить фокус на элементе в коде C#, используйте [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element), который встроен в платформу Blazor и работает со ссылками на элементы.</span><span class="sxs-lookup"><span data-stu-id="1513d-187">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="1513d-188">Для вызова функции JavaScript, которая не возвращает значение, используйте метод <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="1513d-188">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1513d-189">Следующий код активирует событие `Click` на стороне клиента, вызывая предыдущую функцию JavaScript с захваченным <xref:Microsoft.AspNetCore.Components.ElementReference>:</span><span class="sxs-lookup"><span data-stu-id="1513d-189">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="1513d-190">Чтобы использовать метод расширения, создайте статический метод расширения, который принимает экземпляр <xref:Microsoft.JSInterop.IJSRuntime>:</span><span class="sxs-lookup"><span data-stu-id="1513d-190">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="1513d-191">Метод `clickElement` вызывается для объекта напрямую.</span><span class="sxs-lookup"><span data-stu-id="1513d-191">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="1513d-192">В следующем примере предполагается, что метод `TriggerClickEvent` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="1513d-192">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="1513d-193">Переменная `exampleButton` заполняется только после отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="1513d-193">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="1513d-194">Если в код JavaScript передается пустая ссылка <xref:Microsoft.AspNetCore.Components.ElementReference>, он получает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="1513d-194">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="1513d-195">Для управления ссылками на элементы после завершения отрисовки компонента используйте [методы жизненного цикла компонента `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="1513d-195">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="1513d-196">При работе с универсальными типами и возврате значения используйте <xref:System.Threading.Tasks.ValueTask%601>:</span><span class="sxs-lookup"><span data-stu-id="1513d-196">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="1513d-197">Метод `GenericMethod` вызывается для объекта с типом напрямую.</span><span class="sxs-lookup"><span data-stu-id="1513d-197">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="1513d-198">В следующем примере предполагается, что метод `GenericMethod` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="1513d-198">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="1513d-199">Ссылки на элементы между компонентами</span><span class="sxs-lookup"><span data-stu-id="1513d-199">Reference elements across components</span></span>

<span data-ttu-id="1513d-200"><xref:Microsoft.AspNetCore.Components.ElementReference> нельзя передать между компонентами, так как:</span><span class="sxs-lookup"><span data-stu-id="1513d-200">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="1513d-201">Экземпляр гарантированно существует только после визуализации компонента, то есть во время или после выполнения метода компонента <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="1513d-201">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="1513d-202"><xref:Microsoft.AspNetCore.Components.ElementReference> имеет тип [`struct`](/csharp/language-reference/builtin-types/struct), который нельзя передать в качестве [параметра компонента](xref:blazor/components/index#component-parameters).</span><span class="sxs-lookup"><span data-stu-id="1513d-202">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="1513d-203">Чтобы сделать ссылку на элемент доступной для других компонентов, родительский компонент может:</span><span class="sxs-lookup"><span data-stu-id="1513d-203">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="1513d-204">разрешить дочерним компонентам регистрировать обратные вызовы;</span><span class="sxs-lookup"><span data-stu-id="1513d-204">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="1513d-205">вызывать зарегистрированные обратные вызовы во время события <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> с помощью переданной ссылки на элемент.</span><span class="sxs-lookup"><span data-stu-id="1513d-205">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="1513d-206">Такой подход позволяет дочерним компонентам взаимодействовать со ссылкой на элемент родительского компонента косвенным образом.</span><span class="sxs-lookup"><span data-stu-id="1513d-206">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="1513d-207">Этот подход показан в следующем примере Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="1513d-207">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="1513d-208">В `<head>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="1513d-208">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="1513d-209">В `<body>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="1513d-209">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="1513d-210">`Pages/Index.razor` (родительский компонент):</span><span class="sxs-lookup"><span data-stu-id="1513d-210">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="1513d-211">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="1513d-211">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="1513d-212">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="1513d-212">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="1513d-213">`Shared/SurveyPrompt.razor` (дочерний компонент):</span><span class="sxs-lookup"><span data-stu-id="1513d-213">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="1513d-214">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="1513d-214">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="1513d-215">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="1513d-215">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="1513d-216">Повышение надежности вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="1513d-216">Harden JS interop calls</span></span>

<span data-ttu-id="1513d-217">Взаимодействие с JS может завершаться сбоем из-за ошибок сети и в этом случае должно считаться ненадежным.</span><span class="sxs-lookup"><span data-stu-id="1513d-217">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="1513d-218">По умолчанию в приложениях Blazor Server время ожидания вызовов взаимодействия с JS на сервере равно одной минуте.</span><span class="sxs-lookup"><span data-stu-id="1513d-218">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="1513d-219">Если для приложения допустимо более короткое время ожидания, установите его одним из указанных ниже способов.</span><span class="sxs-lookup"><span data-stu-id="1513d-219">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="1513d-220">Глобально в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1513d-220">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="1513d-221">Время ожидания можно указать для отдельного вызова в коде компонента:</span><span class="sxs-lookup"><span data-stu-id="1513d-221">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="1513d-222">Дополнительные сведения о нехватке ресурсов см. в статье <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="1513d-222">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="1513d-223">Исключение циклических ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="1513d-223">Avoid circular object references</span></span>

<span data-ttu-id="1513d-224">Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:</span><span class="sxs-lookup"><span data-stu-id="1513d-224">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="1513d-225">вызовов метода .NET.</span><span class="sxs-lookup"><span data-stu-id="1513d-225">.NET method calls.</span></span>
* <span data-ttu-id="1513d-226">Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.</span><span class="sxs-lookup"><span data-stu-id="1513d-226">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="1513d-227">Дополнительные сведения см. в описании проблемы [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525) (Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore №20525)).</span><span class="sxs-lookup"><span data-stu-id="1513d-227">For more information, see [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="blazor-javascript-isolation-and-object-references"></a><span data-ttu-id="1513d-228">Изоляция Blazor JavaScript и ссылки на объекты</span><span class="sxs-lookup"><span data-stu-id="1513d-228">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="1513d-229">Blazor реализует изоляцию JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="1513d-229">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="1513d-230">Изоляция JavaScript обеспечивает следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="1513d-230">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="1513d-231">Импортированный JavaScript больше не засоряет глобальное пространство имен.</span><span class="sxs-lookup"><span data-stu-id="1513d-231">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="1513d-232">Пользователям библиотеки и компонентов не требуется импортировать связанный код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1513d-232">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="1513d-233">Например, следующий модуль JavaScript экспортирует функцию JavaScript для отображения запроса браузера:</span><span class="sxs-lookup"><span data-stu-id="1513d-233">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="1513d-234">Добавьте предыдущий модуль JavaScript в библиотеку .NET в виде статического веб-ресурса (`wwwroot/exampleJsInterop.js`), а затем импортируйте модуль в код .NET, вызвав <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> для службы <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="1513d-234">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> on the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="1513d-235">Служба внедряется как `js` (не показано) в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="1513d-235">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="1513d-236">Идентификатором `import` в предыдущем примере является специальный идентификатор, используемый специально для импорта модуля JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1513d-236">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="1513d-237">Укажите модуль, используя путь к статическому стабильному веб-ресурсу: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span><span class="sxs-lookup"><span data-stu-id="1513d-237">Specify the module using its stable static web asset path: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="1513d-238">Сегмент пути для текущего каталога (`./`) необходим для создания корректного пути к статическому ресурсу в файле JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1513d-238">The path segment for the current directory (`./`) is required in order to create the correct static asset path to the JavaScript file.</span></span> <span data-ttu-id="1513d-239">Для динамического импорта модуля требуется сетевой запрос, поэтому его можно выполнить только асинхронно, вызвав <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="1513d-239">Dynamically importing a module requires a network request, so it can only be achieved asynchronously by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="1513d-240">Заполнитель `{LIBRARY NAME}` — это имя библиотеки.</span><span class="sxs-lookup"><span data-stu-id="1513d-240">The `{LIBRARY NAME}` placeholder is the library name.</span></span> <span data-ttu-id="1513d-241">Заполнитель `{PATH UNDER WWWROOT}` — это путь к скрипту в папке `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="1513d-241">The `{PATH UNDER WWWROOT}` placeholder is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="1513d-242"><xref:Microsoft.JSInterop.IJSRuntime> импортирует модуль как `IJSObjectReference`, который представляет ссылку на объект JavaScript из кода .NET.</span><span class="sxs-lookup"><span data-stu-id="1513d-242"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="1513d-243">Используйте `IJSObjectReference` для вызова экспортированных функций JavaScript из модуля:</span><span class="sxs-lookup"><span data-stu-id="1513d-243">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="1513d-244">`IJSInProcessObjectReference` представляет ссылку на объект JavaScript, функции которого могут вызываться синхронно.</span><span class="sxs-lookup"><span data-stu-id="1513d-244">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="1513d-245">Использование библиотек JavaScript, отображающих пользовательский интерфейс (элементы DOM)</span><span class="sxs-lookup"><span data-stu-id="1513d-245">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="1513d-246">Иногда может потребоваться использовать библиотеки JavaScript, которые создают видимые элементы пользовательского интерфейса в модели DOM браузера.</span><span class="sxs-lookup"><span data-stu-id="1513d-246">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="1513d-247">На первый взгляд, это может показаться затруднительным, так как система сравнения Blazor предполагает наличие контроля над деревом элементов DOM и в ней возникают ошибки, если какой-либо внешний код изменяет дерево DOM и механизм применения различий становится недействительным.</span><span class="sxs-lookup"><span data-stu-id="1513d-247">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="1513d-248">Это ограничение не относится лишь к Blazor.</span><span class="sxs-lookup"><span data-stu-id="1513d-248">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="1513d-249">Такая же проблема возникает при использовании любой платформы пользовательского интерфейса на основе сравнения.</span><span class="sxs-lookup"><span data-stu-id="1513d-249">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="1513d-250">К счастью, в пользовательский интерфейс компонента Blazor можно легко внедрить внешний пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="1513d-250">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="1513d-251">Рекомендуемым методом является создание пустого элемента кодом компонента (в файле `.razor`).</span><span class="sxs-lookup"><span data-stu-id="1513d-251">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="1513d-252">С точки зрения системы сравнения Blazor элемент всегда пуст, поэтому отрисовщик не выполняет рекурсию в него и не обрабатывает его содержимое.</span><span class="sxs-lookup"><span data-stu-id="1513d-252">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="1513d-253">Это позволяет спокойно заполнить элемент произвольным содержимым, управляемым извне.</span><span class="sxs-lookup"><span data-stu-id="1513d-253">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="1513d-254">Данный принцип демонстрируется в приведенном ниже примере.</span><span class="sxs-lookup"><span data-stu-id="1513d-254">The following example demonstrates the concept.</span></span> <span data-ttu-id="1513d-255">В операторе `if`, когда `firstRender` имеет значение `true`, сделайте что-нибудь с `myElement`.</span><span class="sxs-lookup"><span data-stu-id="1513d-255">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="1513d-256">Например, вызовите внешнюю библиотеку JavaScript для заполнения элемента.</span><span class="sxs-lookup"><span data-stu-id="1513d-256">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="1513d-257">Blazor оставляет содержимое элемента без изменений, пока сам компонент не будет удален.</span><span class="sxs-lookup"><span data-stu-id="1513d-257">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="1513d-258">При удалении компонента удаляется и все его поддерево DOM.</span><span class="sxs-lookup"><span data-stu-id="1513d-258">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="1513d-259">В качестве более подробного примера рассмотрим следующий компонент, который отрисовывает интерактивную карту с помощью [интерфейсов API Mapbox с открытым кодом](https://www.mapbox.com/).</span><span class="sxs-lookup"><span data-stu-id="1513d-259">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="1513d-260">Соответствующий модуль JavaScript, который следует поместить в `wwwroot/mapComponent.js`, выглядит следующим образом.</span><span class="sxs-lookup"><span data-stu-id="1513d-260">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="1513d-261">В предыдущем примере замените строку `{ACCESS TOKEN}` допустимым маркером доступа, который можно получить из https://account.mapbox.com.</span><span class="sxs-lookup"><span data-stu-id="1513d-261">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="1513d-262">Чтобы обеспечить правильный стиль, добавьте следующий тег таблицы стилей на страницу HTML узла (`index.html` или `_Host.cshtml`).</span><span class="sxs-lookup"><span data-stu-id="1513d-262">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="1513d-263">В предыдущем примере создается интерактивный пользовательский интерфейс карты, в котором пользователь может выполнять следующие действия:</span><span class="sxs-lookup"><span data-stu-id="1513d-263">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="1513d-264">прокручивать карту или изменять масштаб перетаскиванием;</span><span class="sxs-lookup"><span data-stu-id="1513d-264">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="1513d-265">переходить к заданным расположениям нажатием кнопок.</span><span class="sxs-lookup"><span data-stu-id="1513d-265">Click buttons to jump to predefined locations.</span></span>

![Карта улиц Mapbox для Токио (Япония) с кнопками для выбора Бристоля (Великобритания) и Токио (Япония)](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="1513d-267">Важном понимать следующие основные моменты.</span><span class="sxs-lookup"><span data-stu-id="1513d-267">The key points to understand are:</span></span>

 * <span data-ttu-id="1513d-268">В случае с Blazor `<div>` с `@ref="mapElement"` остается пустым.</span><span class="sxs-lookup"><span data-stu-id="1513d-268">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="1513d-269">Поэтому `mapbox-gl.js` может спокойно заполнять его и затем изменять его содержимое.</span><span class="sxs-lookup"><span data-stu-id="1513d-269">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="1513d-270">Этот метод можно использовать с любой библиотекой JavaScript, которая отрисовывает пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="1513d-270">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="1513d-271">В компоненты Blazor можно даже внедрять компоненты из сторонней платформы одностраничных приложений JavaScript, если они не пытаются изменить другие части страницы.</span><span class="sxs-lookup"><span data-stu-id="1513d-271">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="1513d-272">Ситуация, когда внешний код JavaScript изменяет элементы, которые Blazor не считает пустыми, *небезопасна*.</span><span class="sxs-lookup"><span data-stu-id="1513d-272">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="1513d-273">При использовании этого подхода необходимо учитывать то, как Blazor удерживает или уничтожает элементы DOM.</span><span class="sxs-lookup"><span data-stu-id="1513d-273">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="1513d-274">В предыдущем примере компонент безопасно обрабатывает события нажатия кнопок и обновляет существующий экземпляр карты, так как по умолчанию элементы DOM по возможности сохраняются без изменений.</span><span class="sxs-lookup"><span data-stu-id="1513d-274">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="1513d-275">При отрисовке списка элементов карты из цикла `@foreach` необходимо использовать `@key`, чтобы гарантировать сохранность экземпляров компонента.</span><span class="sxs-lookup"><span data-stu-id="1513d-275">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="1513d-276">В противном случае изменения в данных списка могут приводить к нежелательному сохранению состояния предыдущих экземпляров компонента.</span><span class="sxs-lookup"><span data-stu-id="1513d-276">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="1513d-277">Дополнительные сведения см. в разделе об [использовании @key для сохранения элементов и компонентов](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="1513d-277">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="1513d-278">Кроме того, в предыдущем примере показано, как можно инкапсулировать логику и зависимости JavaScript в модуле ES6 и динамически загружать его с помощью идентификатора `import`.</span><span class="sxs-lookup"><span data-stu-id="1513d-278">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="1513d-279">Дополнительные сведения см.в статье [Изоляция JavaScript и ссылки на объекты](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="1513d-279">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="1513d-280">Ограничения размера для вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="1513d-280">Size limits on JS interop calls</span></span>

<span data-ttu-id="1513d-281">При использовании Blazor WebAssembly платформа не накладывает ограничений на размер входных и выходных данных в вызовах взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="1513d-281">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="1513d-282">В Blazor Server размер данных в вызовах взаимодействия с JS ограничен максимальным размером входящего сообщения SignalR, разрешенным для методов концентратора, который задается с помощью <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (по умолчанию: 32 КБ).</span><span class="sxs-lookup"><span data-stu-id="1513d-282">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="1513d-283">Если размер сообщений SignalR JS для .NET превышает <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>, возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="1513d-283">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="1513d-284">Платформа не накладывает ограничение на размер сообщений SignalR от концентратора клиенту.</span><span class="sxs-lookup"><span data-stu-id="1513d-284">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span> <span data-ttu-id="1513d-285">Для получения дополнительной информации см. <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span><span class="sxs-lookup"><span data-stu-id="1513d-285">For more information, see <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span></span>
  
## <a name="js-modules"></a><span data-ttu-id="1513d-286">Модули JS</span><span class="sxs-lookup"><span data-stu-id="1513d-286">JS modules</span></span>

<span data-ttu-id="1513d-287">Для изоляции JS взаимодействие с JS работает с поддержкой по умолчанию браузера для [модулей EcmaScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([спецификация ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="1513d-287">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="unmarshalled-js-interop"></a><span data-ttu-id="1513d-288">Демаршалированные вызовы взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="1513d-288">Unmarshalled JS interop</span></span>

<span data-ttu-id="1513d-289">Производительность компонентов Blazor WebAssembly может снизиться при сериализации объектов .NET для взаимодействия с JS и наличии одного из следующих условий:</span><span class="sxs-lookup"><span data-stu-id="1513d-289">Blazor WebAssembly components may experience poor performance when .NET objects are serialized for JS interop and either of the following are true:</span></span>

* <span data-ttu-id="1513d-290">Быстро сериализуется большой объем объектов .NET.</span><span class="sxs-lookup"><span data-stu-id="1513d-290">A high volume of .NET objects are rapidly serialized.</span></span> <span data-ttu-id="1513d-291">Пример. Вызовы взаимодействия с JS выполняются на основе перемещения устройства ввода, например прокрутки колесика мыши.</span><span class="sxs-lookup"><span data-stu-id="1513d-291">Example: JS interop calls are made on the basis of moving an input device, such as spinning a mouse wheel.</span></span>
* <span data-ttu-id="1513d-292">Для взаимодействия с JS нужно сериализовать большие объекты .NET или много объектов .NET.</span><span class="sxs-lookup"><span data-stu-id="1513d-292">Large .NET objects or many .NET objects must be serialized for JS interop.</span></span> <span data-ttu-id="1513d-293">Пример. Для вызовов взаимодействия с JS требуется сериализовать десятки файлов.</span><span class="sxs-lookup"><span data-stu-id="1513d-293">Example: JS interop calls require serializing dozens of files.</span></span>

<span data-ttu-id="1513d-294"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> представляет ссылку на объект JavaScript, функции которого могут вызываться без дополнительных затрат, связанных с сериализацией данных .NET.</span><span class="sxs-lookup"><span data-stu-id="1513d-294"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span>

<span data-ttu-id="1513d-295">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="1513d-295">In the following example:</span></span>

* <span data-ttu-id="1513d-296">[Структура](/dotnet/csharp/language-reference/builtin-types/struct), содержащая строку и целое число, передается в JavaScript без сериализации.</span><span class="sxs-lookup"><span data-stu-id="1513d-296">A [struct](/dotnet/csharp/language-reference/builtin-types/struct) containing a string and an integer is passed unserialized to JavaScript.</span></span>
* <span data-ttu-id="1513d-297">Функции JavaScript обрабатывают данные и возвращают вызывающему объекту логическое значение или строку.</span><span class="sxs-lookup"><span data-stu-id="1513d-297">JavaScript functions process the data and return either a boolean or string to the caller.</span></span>
* <span data-ttu-id="1513d-298">Строку JavaScript нельзя напрямую преобразовать в объект `string` .NET.</span><span class="sxs-lookup"><span data-stu-id="1513d-298">A JavaScript string isn't directly convertible into a .NET `string` object.</span></span> <span data-ttu-id="1513d-299">Функция `unmarshalledFunctionReturnString` вызывает `BINDING.js_string_to_mono_string` для управления преобразованием строки JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1513d-299">The `unmarshalledFunctionReturnString` function calls `BINDING.js_string_to_mono_string` to manage the conversion of a Javascript string.</span></span>

> [!NOTE]
> <span data-ttu-id="1513d-300">Следующие примеры не являются типичными вариантами использования для этого сценария, так как [структура](/dotnet/csharp/language-reference/builtin-types/struct), передаваемая в JavaScript, не приводит к ухудшению производительности компонента.</span><span class="sxs-lookup"><span data-stu-id="1513d-300">The following examples aren't typical use cases for this scenario because the [struct](/dotnet/csharp/language-reference/builtin-types/struct) passed to JavaScript doesn't result in poor component performance.</span></span> <span data-ttu-id="1513d-301">В примере мы используем небольшой объект, только чтобы продемонстрировать концепцию передачи несериализованных данных .NET.</span><span class="sxs-lookup"><span data-stu-id="1513d-301">The example uses a small object merely to demonstrate the concepts for passing unserialized .NET data.</span></span>

<span data-ttu-id="1513d-302">Содержимое блока `<script>` в `wwwroot/index.html` или во внешнем файле JavaScript, на который указывает ссылка `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="1513d-302">Content of a `<script>` block in `wwwroot/index.html` or an external Javascript file referenced by `wwwroot/index.html`:</span></span>

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> <span data-ttu-id="1513d-303">Возможно, в одном из будущих выпусков .NET. имя и поведение функции `js_string_to_mono_string` изменится либо она будет удалена.</span><span class="sxs-lookup"><span data-stu-id="1513d-303">The `js_string_to_mono_string` function name, behavior, and existence is subject to change in a future release of .NET.</span></span> <span data-ttu-id="1513d-304">Пример:</span><span class="sxs-lookup"><span data-stu-id="1513d-304">For example:</span></span>
>
> * <span data-ttu-id="1513d-305">Скорее всего, функция будет переименована.</span><span class="sxs-lookup"><span data-stu-id="1513d-305">The function is likely to be renamed.</span></span>
> * <span data-ttu-id="1513d-306">Возможно, сама функция будет удалена, а вместо нее будет реализовано автоматическое преобразование строк платформой.</span><span class="sxs-lookup"><span data-stu-id="1513d-306">The function itself might be removed in favor of automatic conversion of strings by the framework.</span></span>

<span data-ttu-id="1513d-307">`Pages/UnmarshalledJSInterop.razor` (URL-адрес: `/unmarshalled-js-interop`).</span><span class="sxs-lookup"><span data-stu-id="1513d-307">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span></span>

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

<span data-ttu-id="1513d-308">Если экземпляр `IJSUnmarshalledObjectReference` не удален в коде C#, он может быть удален в JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1513d-308">If an `IJSUnmarshalledObjectReference` instance isn't disposed in C# code, it can be disposed in JavaScript.</span></span> <span data-ttu-id="1513d-309">Следующая функция `dispose` удаляет ссылку на объект при вызове из JavaScript:</span><span class="sxs-lookup"><span data-stu-id="1513d-309">The following `dispose` function disposes the object reference when called from JavaScript:</span></span>

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

<span data-ttu-id="1513d-310">Типы массивов можно преобразовать из объектов JavaScript в объекты .NET с помощью `js_typed_array_to_array`, но при этом массив JavaScript должен быть типизированным.</span><span class="sxs-lookup"><span data-stu-id="1513d-310">Array types can be converted from JavaScript objects into .NET objects using `js_typed_array_to_array`, but the JavaScript array must be a typed array.</span></span> <span data-ttu-id="1513d-311">Массивы из JavaScript могут считываться в коде C# как массив объектов .NET (`object[]`).</span><span class="sxs-lookup"><span data-stu-id="1513d-311">Arrays from JavaScript can be read in C# code as a .NET object array (`object[]`).</span></span>

<span data-ttu-id="1513d-312">Можно преобразовать и другие типы данных, например массивы строк, но при этом нужно создать новый объект массива Mono (`mono_obj_array_new`) и задать его значение (`mono_obj_array_set`).</span><span class="sxs-lookup"><span data-stu-id="1513d-312">Other data types, such as string arrays, can be converted but require creating a new Mono array object (`mono_obj_array_new`) and setting its value (`mono_obj_array_set`).</span></span>

> [!WARNING]
> <span data-ttu-id="1513d-313">Возможно, в будущих выпусках .NET. функции JavaScript (такие как `js_typed_array_to_array`, `mono_obj_array_new` и `mono_obj_array_set`), предоставляемые платформой Blazor, будут удалены либо изменятся их имена или поведение.</span><span class="sxs-lookup"><span data-stu-id="1513d-313">JavaScript functions provided by the Blazor framework, such as `js_typed_array_to_array`, `mono_obj_array_new`, and `mono_obj_array_set`, are subject to name changes, behavioral changes, or removal in future releases of .NET.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1513d-314">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1513d-314">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="1513d-315">Пример InteropComponent.razor (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)</span><span class="sxs-lookup"><span data-stu-id="1513d-315">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
