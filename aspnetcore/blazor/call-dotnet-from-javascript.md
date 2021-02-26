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
ms.openlocfilehash: 45ddcc9e006df2c5e86a7859efc76882b269a496
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280399"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="d5977-103">Вызов методов .NET из функций JavaScript в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d5977-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="d5977-104">Приложение Blazor может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5977-104">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="d5977-105">Такой подход называется *взаимодействием с JavaScript* (*JS*).</span><span class="sxs-lookup"><span data-stu-id="d5977-105">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="d5977-106">В этой статье рассматривается вызов методов .NET из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5977-106">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="d5977-107">Сведения о том, как вызывать функции JavaScript из .NET, см. в разделе <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="d5977-107">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="d5977-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d5977-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="d5977-109">Добавьте JS-файлы (теги `<script>`) перед закрывающим тегом `</body>` в `wwwroot/index.html` файле (Blazor WebAssembly) или файле `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="d5977-109">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="d5977-110">Убедитесь, что JS-файлы с методами взаимодействия JS включены перед JS-файлами платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="d5977-110">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="d5977-111">Вызов статического метода .NET</span><span class="sxs-lookup"><span data-stu-id="d5977-111">Static .NET method call</span></span>

<span data-ttu-id="d5977-112">Чтобы вызвать статический метод .NET из JavaScript, используйте функции `DotNet.invokeMethod` или `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="d5977-112">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="d5977-113">Передайте идентификатор статического метода, который необходимо вызвать, имя сборки, содержащей функцию, и любые аргументы.</span><span class="sxs-lookup"><span data-stu-id="d5977-113">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="d5977-114">Асинхронная версия является предпочтительной для поддержки сценариев Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d5977-114">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="d5977-115">Метод .NET должен быть открытым, статическим и иметь [атрибут `[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="d5977-115">The .NET method must be public, static, and have the [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute).</span></span> <span data-ttu-id="d5977-116">Вызов открытых универсальных методов в настоящее время не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="d5977-116">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="d5977-117">Пример приложения включает метод C#, возвращающий массив `int`.</span><span class="sxs-lookup"><span data-stu-id="d5977-117">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="d5977-118">К методу применяется [атрибут `[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="d5977-118">The [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute) is applied to the method.</span></span>

<span data-ttu-id="d5977-119">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="d5977-119">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="d5977-120">JavaScript, предоставляемый клиенту, вызывает метод C# .NET.</span><span class="sxs-lookup"><span data-stu-id="d5977-120">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="d5977-121">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="d5977-121">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="d5977-122">Если нажата кнопка **`Trigger .NET static method ReturnArrayAsync`** , проверьте выходные данные консоли в средствах веб-разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="d5977-122">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="d5977-123">Выходные данные консоли:</span><span class="sxs-lookup"><span data-stu-id="d5977-123">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="d5977-124">Четвертое значение массива помещается в массив (`data.push(4);`), возвращаемый методом `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="d5977-124">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="d5977-125">По умолчанию идентификатором метода является имя метода, но можно указать другой идентификатор с помощью [атрибута `[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) конструктора:</span><span class="sxs-lookup"><span data-stu-id="d5977-125">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute) constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="d5977-126">В файле JavaScript на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="d5977-126">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="d5977-127">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="d5977-127">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="d5977-128">Вызов метода экземпляра</span><span class="sxs-lookup"><span data-stu-id="d5977-128">Instance method call</span></span>

<span data-ttu-id="d5977-129">Кроме того, из JavaScript можно вызывать методы экземпляра .NET.</span><span class="sxs-lookup"><span data-stu-id="d5977-129">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="d5977-130">Для этого необходимо выполнить следующие действия.</span><span class="sxs-lookup"><span data-stu-id="d5977-130">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="d5977-131">Передайте в JavaScript экземпляр .NET по ссылке:</span><span class="sxs-lookup"><span data-stu-id="d5977-131">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="d5977-132">Выполните статический вызов <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="d5977-132">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="d5977-133">Заключите экземпляр в экземпляр <xref:Microsoft.JSInterop.DotNetObjectReference> и вызовите метод <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> в экземпляре <xref:Microsoft.JSInterop.DotNetObjectReference>.</span><span class="sxs-lookup"><span data-stu-id="d5977-133">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="d5977-134">Удалите объекты <xref:Microsoft.JSInterop.DotNetObjectReference> (пример приведен далее в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="d5977-134">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="d5977-135">Вызовите методы экземпляра .NET в экземпляре с помощью функций `invokeMethod` или `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="d5977-135">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="d5977-136">Экземпляр .NET можно также передать в качестве аргумента при вызове других методов .NET из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5977-136">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="d5977-137">Пример приложения записывает сообщения в консоль на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="d5977-137">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="d5977-138">В следующих примерах, продемонстрированных в учебном приложении, изучите выходные данные консоли браузера в средствах разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="d5977-138">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="d5977-139">Если нажата кнопка **`Trigger .NET instance method HelloHelper.SayHello`** , вызывается `ExampleJsInterop.CallHelloHelperSayHello` и в метод передается имя `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="d5977-139">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="d5977-140">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="d5977-140">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="d5977-141">Метод `CallHelloHelperSayHello` вызывает функцию JavaScript `sayHello` с новым экземпляром `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="d5977-141">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="d5977-142">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="d5977-142">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="d5977-143">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="d5977-143">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="d5977-144">Имя передается в конструктор `HelloHelper`, который задает свойство `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="d5977-144">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="d5977-145">При выполнении функции JavaScript `sayHello` метод `HelloHelper.SayHello` возвращает сообщение `Hello, {Name}!`, которое записывается в консоль функцией JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5977-145">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="d5977-146">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="d5977-146">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="d5977-147">Выходные данные консоли в средствах веб-разработчика браузера:</span><span class="sxs-lookup"><span data-stu-id="d5977-147">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="d5977-148">Чтобы избежать утечки памяти и разрешить сборку мусора для компонента, который создает метод <xref:Microsoft.JSInterop.DotNetObjectReference>, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="d5977-148">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="d5977-149">Удалите объект в классе, который создал экземпляр <xref:Microsoft.JSInterop.DotNetObjectReference>.</span><span class="sxs-lookup"><span data-stu-id="d5977-149">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="d5977-150">Предыдущий шаблон, показанный в классе `ExampleJsInterop`, также можно реализовать в компоненте:</span><span class="sxs-lookup"><span data-stu-id="d5977-150">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
  
  <span data-ttu-id="d5977-151">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="d5977-151">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="d5977-152">Если компонент или класс не удаляет <xref:Microsoft.JSInterop.DotNetObjectReference>, удалите объект в клиенте, вызвав `.dispose()`.</span><span class="sxs-lookup"><span data-stu-id="d5977-152">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="d5977-153">Вызов метода экземпляра компонента</span><span class="sxs-lookup"><span data-stu-id="d5977-153">Component instance method call</span></span>

<span data-ttu-id="d5977-154">Порядок вызова методов .NET компонента</span><span class="sxs-lookup"><span data-stu-id="d5977-154">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="d5977-155">Используйте функцию `invokeMethod` или `invokeMethodAsync`, чтобы вызвать статический метод для компонента.</span><span class="sxs-lookup"><span data-stu-id="d5977-155">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="d5977-156">Статический метод компонента создает программу-оболочку вызова метода экземпляра в виде вызванного <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="d5977-156">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="d5977-157">Для приложений Blazor Server, в которых несколько пользователей могут одновременно использовать один и тот же компонент, используйте вспомогательный класс для вызова методов экземпляра.</span><span class="sxs-lookup"><span data-stu-id="d5977-157">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="d5977-158">Дополнительные сведения см. в разделе [Вспомогательный класс для метода экземпляра компонента](#component-instance-method-helper-class).</span><span class="sxs-lookup"><span data-stu-id="d5977-158">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="d5977-159">В JavaScript на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="d5977-159">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="d5977-160">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="d5977-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="d5977-161">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d5977-161">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="d5977-162">Для передачи аргументов в метод экземпляра выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="d5977-162">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="d5977-163">Добавьте параметры в вызов метода JS.</span><span class="sxs-lookup"><span data-stu-id="d5977-163">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="d5977-164">В приведенном ниже примере в метод передается имя.</span><span class="sxs-lookup"><span data-stu-id="d5977-164">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="d5977-165">При необходимости в список можно добавить дополнительные параметры.</span><span class="sxs-lookup"><span data-stu-id="d5977-165">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="d5977-166">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="d5977-166">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="d5977-167">Укажите правильные типы для параметров <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="d5977-167">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="d5977-168">Укажите список параметров для методов C#.</span><span class="sxs-lookup"><span data-stu-id="d5977-168">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="d5977-169">Вызовите <xref:System.Action> (`UpdateMessage`) с параметрами (`action.Invoke(name)`).</span><span class="sxs-lookup"><span data-stu-id="d5977-169">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="d5977-170">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d5977-170">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="d5977-171">Вывод `message` при нажатии кнопки **Call JS Method**:</span><span class="sxs-lookup"><span data-stu-id="d5977-171">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="d5977-172">Вспомогательный класс для метода экземпляра компонента</span><span class="sxs-lookup"><span data-stu-id="d5977-172">Component instance method helper class</span></span>

<span data-ttu-id="d5977-173">Вспомогательный класс используется для вызова метода экземпляра в качестве <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="d5977-173">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="d5977-174">Вспомогательные классы полезны в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="d5977-174">Helper classes are useful when:</span></span>

* <span data-ttu-id="d5977-175">На одной странице отображается несколько компонентов одного типа.</span><span class="sxs-lookup"><span data-stu-id="d5977-175">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="d5977-176">Используется приложение Blazor Server, в котором несколько пользователей могут одновременно использовать один компонент.</span><span class="sxs-lookup"><span data-stu-id="d5977-176">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="d5977-177">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d5977-177">In the following example:</span></span>

* <span data-ttu-id="d5977-178">Компонент `JSInteropExample` содержит несколько компонентов `ListItem`.</span><span class="sxs-lookup"><span data-stu-id="d5977-178">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="d5977-179">Каждый компонент `ListItem` состоит из сообщения и кнопки.</span><span class="sxs-lookup"><span data-stu-id="d5977-179">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="d5977-180">При выборе кнопки компонента `ListItem` метод `UpdateMessage` `ListItem`изменяет текст элемента списка и скрывает кнопку.</span><span class="sxs-lookup"><span data-stu-id="d5977-180">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="d5977-181">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="d5977-181">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="d5977-182">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="d5977-182">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="d5977-183">В JavaScript на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="d5977-183">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="d5977-184">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="d5977-184">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="d5977-185">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="d5977-185">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="d5977-186">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="d5977-186">`Pages/JSInteropExample.razor`:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="d5977-187">Исключение циклических ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="d5977-187">Avoid circular object references</span></span>

<span data-ttu-id="d5977-188">Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:</span><span class="sxs-lookup"><span data-stu-id="d5977-188">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="d5977-189">вызовов метода .NET.</span><span class="sxs-lookup"><span data-stu-id="d5977-189">.NET method calls.</span></span>
* <span data-ttu-id="d5977-190">Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.</span><span class="sxs-lookup"><span data-stu-id="d5977-190">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="d5977-191">Дополнительные сведения см. на следующих страницах:</span><span class="sxs-lookup"><span data-stu-id="d5977-191">For more information, see the following issues:</span></span>

* [<span data-ttu-id="d5977-192">Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="d5977-192">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="d5977-193">Предложение. Добавить механизм для обработки циклических ссылок при сериализации (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="d5977-193">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="d5977-194">Ограничения размера для вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="d5977-194">Size limits on JS interop calls</span></span>

<span data-ttu-id="d5977-195">При использовании Blazor WebAssembly платформа не накладывает ограничений на размер входных и выходных данных в вызовах взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="d5977-195">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="d5977-196">В Blazor Server размер данных в вызовах взаимодействия с JS ограничен максимальным размером входящего сообщения SignalR, разрешенным для методов концентратора, который задается с помощью <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (по умолчанию: 32 КБ).</span><span class="sxs-lookup"><span data-stu-id="d5977-196">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="d5977-197">Если размер сообщений SignalR JS для .NET превышает <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>, возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="d5977-197">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="d5977-198">Платформа не накладывает ограничение на размер сообщений SignalR от концентратора клиенту.</span><span class="sxs-lookup"><span data-stu-id="d5977-198">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="d5977-199">Если для ведения журнала SignalR не установлен уровень [Отладка](xref:Microsoft.Extensions.Logging.LogLevel) или [Трассировка](xref:Microsoft.Extensions.Logging.LogLevel), ошибка в связи с недопустимым размером сообщения отображается только в консоли средств разработчика браузера:</span><span class="sxs-lookup"><span data-stu-id="d5977-199">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="d5977-200">Ошибка: Подключение разорвано с ошибкой "Ошибка. Сервер вернул ошибку при закрытии: соединение закрыто с ошибкой".</span><span class="sxs-lookup"><span data-stu-id="d5977-200">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="d5977-201">Если для [ведения журнала на стороне сервера SignalR](xref:signalr/diagnostics#server-side-logging) установлен уровень [Отладка](xref:Microsoft.Extensions.Logging.LogLevel) или [Трассировка](xref:Microsoft.Extensions.Logging.LogLevel), функция ведения журнала на стороне сервера предоставляет <xref:System.IO.InvalidDataException> для ошибки в связи с недопустимым размером.</span><span class="sxs-lookup"><span data-stu-id="d5977-201">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="d5977-202">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="d5977-202">`appsettings.Development.json`:</span></span>

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

> <span data-ttu-id="d5977-203">System.IO.InvalidDataException: Превышен максимальный размер сообщения 32768 Б.</span><span class="sxs-lookup"><span data-stu-id="d5977-203">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="d5977-204">Размер сообщения можно настроить в AddHubOptions.</span><span class="sxs-lookup"><span data-stu-id="d5977-204">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="d5977-205">Увеличьте ограничение, настроив <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5977-205">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5977-206">В следующем примере для размера получаемого сообщения устанавливается максимальный размер 64 КБ (64 \* 1024):</span><span class="sxs-lookup"><span data-stu-id="d5977-206">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="d5977-207">Увеличение предельного размера для входящих сообщений SignalR потребует больше серверных ресурсов для обработки, а также повысит уязвимость сервера к злонамеренным действиям пользователей.</span><span class="sxs-lookup"><span data-stu-id="d5977-207">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="d5977-208">Кроме того, считывание большого объема содержимого в память в виде строк или массивов байтов может привести к неудачным выделениям памяти, которые плохо обрабатываются сборщиком мусора, что дополнительно снизит производительность.</span><span class="sxs-lookup"><span data-stu-id="d5977-208">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="d5977-209">Чтобы считывать полезные данные большого размера, можно отправлять такое содержимое меньшими фрагментами и обрабатывать их как <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="d5977-209">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="d5977-210">Это удобно для чтения полезных данных большого объема в формате JSON или необработанных байтов из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5977-210">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="d5977-211">Вы можете изучить отправку больших двоичных данных в Blazor Server с помощью методов, эквивалентных работе компонента `InputFile`, в [примере приложения с отправкой двоичных данных](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="d5977-211">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="d5977-212">При разработке кода, который передает данные большого объема между JavaScript и Blazor, учитывайте следующие рекомендации:</span><span class="sxs-lookup"><span data-stu-id="d5977-212">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="d5977-213">Разделите данные на небольшие части и отправляйте сегменты данных последовательно, пока все данные не будут получены сервером.</span><span class="sxs-lookup"><span data-stu-id="d5977-213">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="d5977-214">Не выделяйте большие объекты в коде C# и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5977-214">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="d5977-215">Не блокируйте основной поток пользовательского интерфейса на длительные периоды при отправке или получении данных.</span><span class="sxs-lookup"><span data-stu-id="d5977-215">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="d5977-216">Освободите занятую память при завершении или отмене процесса.</span><span class="sxs-lookup"><span data-stu-id="d5977-216">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="d5977-217">Применяйте следующие дополнительные требования в целях безопасности:</span><span class="sxs-lookup"><span data-stu-id="d5977-217">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="d5977-218">Объявите максимальный размер файла или данных, который может быть передан.</span><span class="sxs-lookup"><span data-stu-id="d5977-218">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="d5977-219">Объявите минимальную скорость передачи от клиента к серверу.</span><span class="sxs-lookup"><span data-stu-id="d5977-219">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="d5977-220">После получения данных сервером данные могут быть:</span><span class="sxs-lookup"><span data-stu-id="d5977-220">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="d5977-221">Временно сохранены в буфере памяти до тех пор, пока не будут собраны все сегменты.</span><span class="sxs-lookup"><span data-stu-id="d5977-221">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="d5977-222">Использованы немедленно.</span><span class="sxs-lookup"><span data-stu-id="d5977-222">Consumed immediately.</span></span> <span data-ttu-id="d5977-223">Например, данные могут храниться сразу в базе данных или записываться на диск по мере получения каждого сегмента.</span><span class="sxs-lookup"><span data-stu-id="d5977-223">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="d5977-224">Модули JS</span><span class="sxs-lookup"><span data-stu-id="d5977-224">JS modules</span></span>

<span data-ttu-id="d5977-225">Для изоляции JS взаимодействие с JS работает с поддержкой по умолчанию браузера для [модулей EcmaScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([спецификация ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="d5977-225">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5977-226">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d5977-226">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="d5977-227">Пример `InteropComponent.razor` (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)</span><span class="sxs-lookup"><span data-stu-id="d5977-227">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
