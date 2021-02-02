---
title: Вызов методов .NET из функций JavaScript в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как вызывать методы .NET из функций JavaScript в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e602f29e6932280f4625ade64201ff232e02150d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751632"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="cb360-103">Вызов методов .NET из функций JavaScript в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cb360-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="cb360-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Шашикант Рудравади](http://wisne.co) (Shashikant Rudrawadi) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="cb360-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cb360-105">Приложение Blazor может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb360-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="cb360-106">Такой подход называется *взаимодействием с JavaScript* (*JS*).</span><span class="sxs-lookup"><span data-stu-id="cb360-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="cb360-107">В этой статье рассматривается вызов методов .NET из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb360-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="cb360-108">Сведения о том, как вызывать функции JavaScript из .NET, см. в разделе <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="cb360-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="cb360-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cb360-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="cb360-110">Добавьте JS-файлы (теги `<script>`) перед закрывающим тегом `</body>` в `wwwroot/index.html` файле (Blazor WebAssembly) или файле `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="cb360-110">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="cb360-111">Убедитесь, что JS-файлы с методами взаимодействия JS включены перед JS-файлами платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="cb360-111">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="cb360-112">Вызов статического метода .NET</span><span class="sxs-lookup"><span data-stu-id="cb360-112">Static .NET method call</span></span>

<span data-ttu-id="cb360-113">Чтобы вызвать статический метод .NET из JavaScript, используйте функции `DotNet.invokeMethod` или `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="cb360-113">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="cb360-114">Передайте идентификатор статического метода, который необходимо вызвать, имя сборки, содержащей функцию, и любые аргументы.</span><span class="sxs-lookup"><span data-stu-id="cb360-114">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="cb360-115">Асинхронная версия является предпочтительной для поддержки сценариев Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="cb360-115">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="cb360-116">Метод .NET должен быть открытым, статическим и иметь атрибут [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="cb360-116">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="cb360-117">Вызов открытых универсальных методов в настоящее время не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="cb360-117">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="cb360-118">Пример приложения включает метод C#, возвращающий массив `int`.</span><span class="sxs-lookup"><span data-stu-id="cb360-118">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="cb360-119">К методу применяется атрибут [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="cb360-119">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="cb360-120">`Pages/JsInterop.razor`.</span><span class="sxs-lookup"><span data-stu-id="cb360-120">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="cb360-121">JavaScript, предоставляемый клиенту, вызывает метод C# .NET.</span><span class="sxs-lookup"><span data-stu-id="cb360-121">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="cb360-122">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="cb360-122">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="cb360-123">Если нажата кнопка **`Trigger .NET static method ReturnArrayAsync`** , проверьте выходные данные консоли в средствах веб-разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="cb360-123">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="cb360-124">Выходные данные консоли:</span><span class="sxs-lookup"><span data-stu-id="cb360-124">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="cb360-125">Четвертое значение массива помещается в массив (`data.push(4);`), возвращаемый методом `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="cb360-125">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="cb360-126">По умолчанию идентификатором метода является имя метода, но можно указать другой идентификатор с помощью атрибута конструктора [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute):</span><span class="sxs-lookup"><span data-stu-id="cb360-126">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="cb360-127">В файле JavaScript на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="cb360-127">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="cb360-128">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="cb360-128">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="cb360-129">Вызов метода экземпляра</span><span class="sxs-lookup"><span data-stu-id="cb360-129">Instance method call</span></span>

<span data-ttu-id="cb360-130">Кроме того, из JavaScript можно вызывать методы экземпляра .NET.</span><span class="sxs-lookup"><span data-stu-id="cb360-130">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="cb360-131">Для этого необходимо выполнить следующие действия.</span><span class="sxs-lookup"><span data-stu-id="cb360-131">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="cb360-132">Передайте в JavaScript экземпляр .NET по ссылке:</span><span class="sxs-lookup"><span data-stu-id="cb360-132">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="cb360-133">Выполните статический вызов <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="cb360-133">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="cb360-134">Заключите экземпляр в экземпляр <xref:Microsoft.JSInterop.DotNetObjectReference> и вызовите метод <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> в экземпляре <xref:Microsoft.JSInterop.DotNetObjectReference>.</span><span class="sxs-lookup"><span data-stu-id="cb360-134">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="cb360-135">Удалите объекты <xref:Microsoft.JSInterop.DotNetObjectReference> (пример приведен далее в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="cb360-135">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="cb360-136">Вызовите методы экземпляра .NET в экземпляре с помощью функций `invokeMethod` или `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="cb360-136">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="cb360-137">Экземпляр .NET можно также передать в качестве аргумента при вызове других методов .NET из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb360-137">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="cb360-138">Пример приложения записывает сообщения в консоль на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="cb360-138">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="cb360-139">В следующих примерах, продемонстрированных в учебном приложении, изучите выходные данные консоли браузера в средствах разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="cb360-139">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="cb360-140">Если нажата кнопка **`Trigger .NET instance method HelloHelper.SayHello`** , вызывается `ExampleJsInterop.CallHelloHelperSayHello` и в метод передается имя `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="cb360-140">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="cb360-141">`Pages/JsInterop.razor`.</span><span class="sxs-lookup"><span data-stu-id="cb360-141">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="cb360-142">Метод `CallHelloHelperSayHello` вызывает функцию JavaScript `sayHello` с новым экземпляром `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="cb360-142">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="cb360-143">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="cb360-143">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="cb360-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="cb360-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="cb360-145">Имя передается в конструктор `HelloHelper`, который задает свойство `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="cb360-145">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="cb360-146">При выполнении функции JavaScript `sayHello` метод `HelloHelper.SayHello` возвращает сообщение `Hello, {Name}!`, которое записывается в консоль функцией JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb360-146">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="cb360-147">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="cb360-147">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="cb360-148">Выходные данные консоли в средствах веб-разработчика браузера:</span><span class="sxs-lookup"><span data-stu-id="cb360-148">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="cb360-149">Чтобы избежать утечки памяти и разрешить сборку мусора для компонента, который создает метод <xref:Microsoft.JSInterop.DotNetObjectReference>, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="cb360-149">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="cb360-150">Удалите объект в классе, который создал экземпляр <xref:Microsoft.JSInterop.DotNetObjectReference>.</span><span class="sxs-lookup"><span data-stu-id="cb360-150">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="cb360-151">Предыдущий шаблон, показанный в классе `ExampleJsInterop`, также можно реализовать в компоненте:</span><span class="sxs-lookup"><span data-stu-id="cb360-151">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="cb360-152">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="cb360-152">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="cb360-153">Если компонент или класс не удаляет <xref:Microsoft.JSInterop.DotNetObjectReference>, удалите объект в клиенте, вызвав `.dispose()`.</span><span class="sxs-lookup"><span data-stu-id="cb360-153">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="cb360-154">Вызов метода экземпляра компонента</span><span class="sxs-lookup"><span data-stu-id="cb360-154">Component instance method call</span></span>

<span data-ttu-id="cb360-155">Порядок вызова методов .NET компонента</span><span class="sxs-lookup"><span data-stu-id="cb360-155">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="cb360-156">Используйте функцию `invokeMethod` или `invokeMethodAsync`, чтобы вызвать статический метод для компонента.</span><span class="sxs-lookup"><span data-stu-id="cb360-156">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="cb360-157">Статический метод компонента создает программу-оболочку вызова метода экземпляра в виде вызванного <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="cb360-157">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="cb360-158">Для приложений Blazor Server, в которых несколько пользователей могут одновременно использовать один и тот же компонент, используйте вспомогательный класс для вызова методов экземпляра.</span><span class="sxs-lookup"><span data-stu-id="cb360-158">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="cb360-159">Дополнительные сведения см. в разделе [Вспомогательный класс для метода экземпляра компонента](#component-instance-method-helper-class).</span><span class="sxs-lookup"><span data-stu-id="cb360-159">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="cb360-160">В JavaScript на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="cb360-160">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="cb360-161">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="cb360-161">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="cb360-162">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="cb360-162">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="cb360-163">Для передачи аргументов в метод экземпляра выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="cb360-163">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="cb360-164">Добавьте параметры в вызов метода JS.</span><span class="sxs-lookup"><span data-stu-id="cb360-164">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="cb360-165">В приведенном ниже примере в метод передается имя.</span><span class="sxs-lookup"><span data-stu-id="cb360-165">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="cb360-166">При необходимости в список можно добавить дополнительные параметры.</span><span class="sxs-lookup"><span data-stu-id="cb360-166">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="cb360-167">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="cb360-167">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="cb360-168">Укажите правильные типы для параметров <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="cb360-168">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="cb360-169">Укажите список параметров для методов C#.</span><span class="sxs-lookup"><span data-stu-id="cb360-169">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="cb360-170">Вызовите <xref:System.Action> (`UpdateMessage`) с параметрами (`action.Invoke(name)`).</span><span class="sxs-lookup"><span data-stu-id="cb360-170">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="cb360-171">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="cb360-171">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="cb360-172">Вывод `message` при нажатии кнопки **Call JS Method**:</span><span class="sxs-lookup"><span data-stu-id="cb360-172">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="cb360-173">Вспомогательный класс для метода экземпляра компонента</span><span class="sxs-lookup"><span data-stu-id="cb360-173">Component instance method helper class</span></span>

<span data-ttu-id="cb360-174">Вспомогательный класс используется для вызова метода экземпляра в качестве <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="cb360-174">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="cb360-175">Вспомогательные классы полезны в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="cb360-175">Helper classes are useful when:</span></span>

* <span data-ttu-id="cb360-176">На одной странице отображается несколько компонентов одного типа.</span><span class="sxs-lookup"><span data-stu-id="cb360-176">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="cb360-177">Используется приложение Blazor Server, в котором несколько пользователей могут одновременно использовать один компонент.</span><span class="sxs-lookup"><span data-stu-id="cb360-177">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="cb360-178">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="cb360-178">In the following example:</span></span>

* <span data-ttu-id="cb360-179">Компонент `JSInteropExample` содержит несколько компонентов `ListItem`.</span><span class="sxs-lookup"><span data-stu-id="cb360-179">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="cb360-180">Каждый компонент `ListItem` состоит из сообщения и кнопки.</span><span class="sxs-lookup"><span data-stu-id="cb360-180">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="cb360-181">При выборе кнопки компонента `ListItem` метод `UpdateMessage` `ListItem`изменяет текст элемента списка и скрывает кнопку.</span><span class="sxs-lookup"><span data-stu-id="cb360-181">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="cb360-182">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="cb360-182">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="cb360-183">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="cb360-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="cb360-184">В JavaScript на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="cb360-184">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="cb360-185">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="cb360-185">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="cb360-186">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="cb360-186">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="cb360-187">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="cb360-187">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="cb360-188">Исключение циклических ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="cb360-188">Avoid circular object references</span></span>

<span data-ttu-id="cb360-189">Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:</span><span class="sxs-lookup"><span data-stu-id="cb360-189">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="cb360-190">вызовов метода .NET.</span><span class="sxs-lookup"><span data-stu-id="cb360-190">.NET method calls.</span></span>
* <span data-ttu-id="cb360-191">Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.</span><span class="sxs-lookup"><span data-stu-id="cb360-191">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="cb360-192">Дополнительные сведения см. на следующих страницах:</span><span class="sxs-lookup"><span data-stu-id="cb360-192">For more information, see the following issues:</span></span>

* [<span data-ttu-id="cb360-193">Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="cb360-193">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="cb360-194">Предложение. Добавить механизм для обработки циклических ссылок при сериализации (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="cb360-194">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="cb360-195">Ограничения размера для вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="cb360-195">Size limits on JS interop calls</span></span>

<span data-ttu-id="cb360-196">При использовании Blazor WebAssembly платформа не накладывает ограничений на размер входных и выходных данных в вызовах взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="cb360-196">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="cb360-197">В Blazor Server размер данных в вызовах взаимодействия с JS ограничен максимальным размером входящего сообщения SignalR, разрешенным для методов концентратора, который задается с помощью <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (по умолчанию: 32 КБ).</span><span class="sxs-lookup"><span data-stu-id="cb360-197">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="cb360-198">Если размер сообщений SignalR JS для .NET превышает <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>, возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="cb360-198">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="cb360-199">Платформа не накладывает ограничение на размер сообщений SignalR от концентратора клиенту.</span><span class="sxs-lookup"><span data-stu-id="cb360-199">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="cb360-200">Если для ведения журнала SignalR не установлен уровень [Отладка](xref:Microsoft.Extensions.Logging.LogLevel) или [Трассировка](xref:Microsoft.Extensions.Logging.LogLevel), ошибка в связи с недопустимым размером сообщения отображается только в консоли средств разработчика браузера:</span><span class="sxs-lookup"><span data-stu-id="cb360-200">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="cb360-201">Ошибка: Подключение разорвано с ошибкой "Ошибка. Сервер вернул ошибку при закрытии: соединение закрыто с ошибкой".</span><span class="sxs-lookup"><span data-stu-id="cb360-201">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="cb360-202">Если для [ведения журнала на стороне сервера SignalR](xref:signalr/diagnostics#server-side-logging) установлен уровень [Отладка](xref:Microsoft.Extensions.Logging.LogLevel) или [Трассировка](xref:Microsoft.Extensions.Logging.LogLevel), функция ведения журнала на стороне сервера предоставляет <xref:System.IO.InvalidDataException> для ошибки в связи с недопустимым размером.</span><span class="sxs-lookup"><span data-stu-id="cb360-202">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="cb360-203">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="cb360-203">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> <span data-ttu-id="cb360-204">System.IO.InvalidDataException: Превышен максимальный размер сообщения 32768 Б.</span><span class="sxs-lookup"><span data-stu-id="cb360-204">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="cb360-205">Размер сообщения можно настроить в AddHubOptions.</span><span class="sxs-lookup"><span data-stu-id="cb360-205">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="cb360-206">Увеличьте ограничение, настроив <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cb360-206">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cb360-207">В следующем примере для размера получаемого сообщения устанавливается максимальный размер 64 КБ (64 \* 1024):</span><span class="sxs-lookup"><span data-stu-id="cb360-207">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="cb360-208">Увеличение предельного размера для входящих сообщений SignalR потребует больше серверных ресурсов для обработки, а также повысит уязвимость сервера к злонамеренным действиям пользователей.</span><span class="sxs-lookup"><span data-stu-id="cb360-208">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="cb360-209">Кроме того, считывание большого объема содержимого в память в виде строк или массивов байтов может привести к неудачным выделениям памяти, которые плохо обрабатываются сборщиком мусора, что дополнительно снизит производительность.</span><span class="sxs-lookup"><span data-stu-id="cb360-209">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="cb360-210">Чтобы считывать полезные данные большого размера, можно отправлять такое содержимое меньшими фрагментами и обрабатывать их как <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="cb360-210">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="cb360-211">Это удобно для чтения полезных данных большого объема в формате JSON или необработанных байтов из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb360-211">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="cb360-212">Вы можете изучить отправку больших двоичных данных в Blazor Server с помощью методов, эквивалентных работе компонента `InputFile`, в [примере приложения с отправкой двоичных данных](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="cb360-212">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="cb360-213">При разработке кода, который передает данные большого объема между JavaScript и Blazor, учитывайте следующие рекомендации:</span><span class="sxs-lookup"><span data-stu-id="cb360-213">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="cb360-214">Разделите данные на небольшие части и отправляйте сегменты данных последовательно, пока все данные не будут получены сервером.</span><span class="sxs-lookup"><span data-stu-id="cb360-214">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="cb360-215">Не выделяйте большие объекты в коде C# и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb360-215">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="cb360-216">Не блокируйте основной поток пользовательского интерфейса на длительные периоды при отправке или получении данных.</span><span class="sxs-lookup"><span data-stu-id="cb360-216">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="cb360-217">Освободите занятую память при завершении или отмене процесса.</span><span class="sxs-lookup"><span data-stu-id="cb360-217">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="cb360-218">Применяйте следующие дополнительные требования в целях безопасности:</span><span class="sxs-lookup"><span data-stu-id="cb360-218">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="cb360-219">Объявите максимальный размер файла или данных, который может быть передан.</span><span class="sxs-lookup"><span data-stu-id="cb360-219">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="cb360-220">Объявите минимальную скорость передачи от клиента к серверу.</span><span class="sxs-lookup"><span data-stu-id="cb360-220">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="cb360-221">После получения данных сервером данные могут быть:</span><span class="sxs-lookup"><span data-stu-id="cb360-221">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="cb360-222">Временно сохранены в буфере памяти до тех пор, пока не будут собраны все сегменты.</span><span class="sxs-lookup"><span data-stu-id="cb360-222">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="cb360-223">Использованы немедленно.</span><span class="sxs-lookup"><span data-stu-id="cb360-223">Consumed immediately.</span></span> <span data-ttu-id="cb360-224">Например, данные могут храниться сразу в базе данных или записываться на диск по мере получения каждого сегмента.</span><span class="sxs-lookup"><span data-stu-id="cb360-224">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="cb360-225">Модули JS</span><span class="sxs-lookup"><span data-stu-id="cb360-225">JS modules</span></span>

<span data-ttu-id="cb360-226">Для изоляции JS взаимодействие с JS работает с поддержкой по умолчанию браузера для [модулей EcmaScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([спецификация ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="cb360-226">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb360-227">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="cb360-227">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="cb360-228">Пример `InteropComponent.razor` (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)</span><span class="sxs-lookup"><span data-stu-id="cb360-228">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
