---
title: Обработка ошибок в ASP.NET Core
author: rick-anderson
description: Узнайте, как обрабатывать ошибки в приложениях ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
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
uid: fundamentals/error-handling
ms.openlocfilehash: e65983fb1a440057283111ea5a79a79b765607b7
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751683"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="bfcb9-103">Обработка ошибок в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bfcb9-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bfcb9-104">Авторы: [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin), [Том Дикстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="bfcb9-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bfcb9-105">В этой статье рассматриваются основные методы обработки ошибок в веб-приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="bfcb9-106">Для веб-API см. раздел <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="bfcb9-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="bfcb9-108">См. раздел [Загрузка примера](xref:index#how-to-download-a-sample). Вкладка "Сеть" в средствах разработчика F12 в браузере полезна при тестировании примера приложения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="bfcb9-109">Страница со сведениями об исключении для разработчика</span><span class="sxs-lookup"><span data-stu-id="bfcb9-109">Developer Exception Page</span></span>

<span data-ttu-id="bfcb9-110">*Страница исключений для разработчика* содержит подробные сведения об исключениях запросов.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="bfcb9-111">Шаблоны ASP.NET Core создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="bfcb9-112">Выделенный выше код включает страницу исключений для разработчика при выполнении приложения в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="bfcb9-113">Шаблоны помещают метод <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> в начале конвейера ПО промежуточного слоя, чтобы он мог перехватывать исключения, вызываемые в последующем ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="bfcb9-114">Приведенный выше код включает страницу исключений для разработчика \***только** _ при выполнении приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="bfcb9-115">Подробные сведения об исключениях не должны быть общедоступными при выполнении приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="bfcb9-116">Дополнительные сведения о настройке среды см. в статье <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bfcb9-117">Страница исключений для разработчика содержит следующие сведения об исключении и запросе:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="bfcb9-118">_ Трассировка стека</span><span class="sxs-lookup"><span data-stu-id="bfcb9-118">_ Stack trace</span></span>
* <span data-ttu-id="bfcb9-119">параметры строки запроса (при наличии);</span><span class="sxs-lookup"><span data-stu-id="bfcb9-119">Query string parameters if any</span></span>
* <span data-ttu-id="bfcb9-120">Cookie (при наличии);</span><span class="sxs-lookup"><span data-stu-id="bfcb9-120">Cookies if any</span></span>
* <span data-ttu-id="bfcb9-121">Заголовки</span><span class="sxs-lookup"><span data-stu-id="bfcb9-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="bfcb9-122">Страница обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="bfcb9-122">Exception handler page</span></span>

<span data-ttu-id="bfcb9-123">Чтобы настроить пользовательскую страницу обработки ошибок для [рабочей среды](xref:fundamentals/environments), вызовите <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="bfcb9-124">Это ПО промежуточного слоя для обработки исключений выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-124">This exception handling middleware:</span></span>

* <span data-ttu-id="bfcb9-125">Перехватывает и записывает в журнал исключения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="bfcb9-126">повторно выполняет запрос в альтернативном конвейере по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="bfcb9-127">Запрос не выполняется повторно, если запущен отклик.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="bfcb9-128">Созданный шаблоном код повторно выполняет запрос, используя путь `/Error`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="bfcb9-129">В следующем примере с помощью <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> добавляется ПО промежуточного слоя для обработки исключений в средах, не предназначенных для разработки:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="bfcb9-130">Шаблон приложения Razor Pages предоставляет страницу ошибки (*CSHTML*) и класс <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="bfcb9-131">Для приложения MVC шаблон проекта содержит метод действия `Error` и представление ошибок для контроллера Home.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="bfcb9-132">ПО промежуточного слоя обработки исключений повторно выполняет запрос, используя *исходный* метод HTTP.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-132">The exception handling middleware re-executes the request using the *original* HTTP method.</span></span> <span data-ttu-id="bfcb9-133">Если конечная точка обработчика ошибок ограничена определенным набором методов HTTP, она выполняется только для этих методов HTTP.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-133">If an error handler endpoint is restricted to a specific set of HTTP methods, it runs only for those HTTP methods.</span></span> <span data-ttu-id="bfcb9-134">Например, действие контроллера MVC, использующее атрибут `[HttpGet]`, выполняется только для запросов GET.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-134">For example, an MVC controller action that uses the `[HttpGet]` attribute runs only for GET requests.</span></span> <span data-ttu-id="bfcb9-135">Чтобы гарантировать, что страницу пользовательской обработки ошибок будут достигать *все* запросы, не ограничивайте их определенным набором методов HTTP.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-135">To ensure that *all* requests reach the custom error handling page, don't restrict them to a specific set of HTTP methods.</span></span>

<span data-ttu-id="bfcb9-136">Для избирательного управления исключениями в зависимости от исходного метода HTTP:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-136">To handle exceptions differently based on the original HTTP method:</span></span>

* <span data-ttu-id="bfcb9-137">Для Razor Pages создайте несколько методов обработчика.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-137">For Razor Pages, create multiple handler methods.</span></span> <span data-ttu-id="bfcb9-138">Например, используйте `OnGet`, чтобы обрабатывать исключения GET, и `OnPost`, чтобы обрабатывать исключения POST.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-138">For example, use `OnGet` to handle GET exceptions and use `OnPost` to handle POST exceptions.</span></span>
* <span data-ttu-id="bfcb9-139">Для MVC примените атрибуты HTTP-команды к нескольким действиям.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-139">For MVC, apply HTTP verb attributes to multiple actions.</span></span> <span data-ttu-id="bfcb9-140">Например, используйте `[HttpGet]`, чтобы обрабатывать исключения GET, и `[HttpPost]`, чтобы обрабатывать исключения POST.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-140">For example, use `[HttpGet]` to handle GET exceptions and use `[HttpPost]` to handle POST exceptions.</span></span>

<span data-ttu-id="bfcb9-141">Чтобы разрешить пользователям, не прошедшим проверку подлинности, просматривать страницу пользовательской обработки ошибок, убедитесь, что она поддерживает анонимный доступ.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-141">To allow unauthenticated users to view the custom error handling page, ensure that it supports anonymous access.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="bfcb9-142">Доступ к исключению</span><span class="sxs-lookup"><span data-stu-id="bfcb9-142">Access the exception</span></span>

<span data-ttu-id="bfcb9-143">Используйте интерфейс <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, чтобы получить доступ к исключению и к пути исходного запроса в обработчике ошибок.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-143">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="bfcb9-144">Следующий код добавляет `ExceptionMessage` в файл *Pages/Error.cshtml.cs* по умолчанию, создаваемый шаблонами ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-144">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="bfcb9-145">**Не** передавайте клиентам конфиденциальную информацию об ошибках.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-145">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="bfcb9-146">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-146">Serving errors is a security risk.</span></span>

<span data-ttu-id="bfcb9-147">Чтобы протестировать исключение в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-147">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="bfcb9-148">Настройте среду как рабочую.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-148">Set the environment to production.</span></span>
* <span data-ttu-id="bfcb9-149">Удалите комментарии из `webBuilder.UseStartup<Startup>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-149">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="bfcb9-150">На домашней странице выберите **Вызвать исключение**.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-150">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="bfcb9-151">Лямбда-функция для обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="bfcb9-151">Exception handler lambda</span></span>

<span data-ttu-id="bfcb9-152">Альтернативой [пользовательской странице обработчика исключений](#exception-handler-page) является предоставление лямбда-функции для <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-152">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="bfcb9-153">Использование лямбда-функции позволяет получить доступ к ошибке до возврата ответа.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-153">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="bfcb9-154">В следующем коде для обработки исключений используется лямбда-выражение:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-154">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="bfcb9-155">**Не** передавайте клиентам конфиденциальную информацию об ошибках из <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> или <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-155">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="bfcb9-156">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-156">Serving errors is a security risk.</span></span>

<span data-ttu-id="bfcb9-157">Чтобы протестировать лямбда-выражение для обработки исключений в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-157">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="bfcb9-158">Настройте среду как рабочую.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-158">Set the environment to production.</span></span>
* <span data-ttu-id="bfcb9-159">Удалите комментарии из `webBuilder.UseStartup<StartupLambda>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-159">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="bfcb9-160">На домашней странице выберите **Вызвать исключение**.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-160">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="bfcb9-161">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bfcb9-161">UseStatusCodePages</span></span>

<span data-ttu-id="bfcb9-162">По умолчанию приложение ASP.NET Core не предоставляет страницу для кодов состояния ошибок HTTP, таких как код *404 Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-162">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="bfcb9-163">Когда в приложении возникает ошибка с кодом состояния HTTP 400–599 без текста, возвращается код состояния и пустой текст ответа.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-163">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="bfcb9-164">Чтобы указать коды состояния, используйте ПО промежуточного слоя страниц с кодами состояния.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-164">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="bfcb9-165">Чтобы включить обработчики по умолчанию, возвращающие только текст для распространенных кодов состояния ошибки, вызовите <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-165">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="bfcb9-166">Вызовите `UseStatusCodePages` до ПО промежуточного слоя для обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-166">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="bfcb9-167">Например, вызовите `UseStatusCodePages` до ПО промежуточного слоя для статических файлов и конечных точек.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-167">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="bfcb9-168">Если `UseStatusCodePages` не используется, при переходе по URL-адресу без конечной точки возвращается зависящее от браузера сообщение об ошибке, в котором указывается, что конечная точка не найдена.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-168">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="bfcb9-169">Например, можно перейти по следующему адресу: `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-169">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="bfcb9-170">В этом случае при вызове `UseStatusCodePages` браузер вернет следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-170">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="bfcb9-171">`UseStatusCodePages` обычно не применяется в рабочей среде, так как возвращает сообщение, бесполезное для пользователей.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-171">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="bfcb9-172">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-172">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="bfcb9-173">Настройте среду как рабочую.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-173">Set the environment to production.</span></span>
* <span data-ttu-id="bfcb9-174">Удалите комментарии из `webBuilder.UseStartup<StartupUseStatusCodePages>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-174">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="bfcb9-175">Выберите ссылки на домашней странице.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-175">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="bfcb9-176">ПО промежуточного слоя страниц кода состояния **не** перехватывает исключения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-176">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="bfcb9-177">Чтобы предоставить настраиваемую страницу обработки ошибок, используйте[страницу обработчика исключений](#exception-handler-page).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-177">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="bfcb9-178">UseStatusCodePages со строкой формата</span><span class="sxs-lookup"><span data-stu-id="bfcb9-178">UseStatusCodePages with format string</span></span>

<span data-ttu-id="bfcb9-179">Чтобы настроить тип содержимого и указать текст ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает тип содержимого и строку формата.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-179">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="bfcb9-180">В предыдущем коде `{0}` является заполнителем для кода ошибки.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-180">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="bfcb9-181">`UseStatusCodePages` со строкой формата обычно не применяется в рабочей среде, так как возвращает сообщение, бесполезное для пользователей.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-181">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="bfcb9-182">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), удалите комментарии из `webBuilder.UseStartup<StartupFormat>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-182">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="bfcb9-183">UseStatusCodePages с лямбда-выражением</span><span class="sxs-lookup"><span data-stu-id="bfcb9-183">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="bfcb9-184">Чтобы указать пользовательский код для обработки ошибок и отображения ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает лямбда-выражение.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-184">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="bfcb9-185">`UseStatusCodePages` с лямбда-выражением обычно не применяется в рабочей среде, так как возвращает сообщение, бесполезное для пользователей.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-185">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="bfcb9-186">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), удалите комментарии из `webBuilder.UseStartup<StartupStatusLambda>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-186">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="bfcb9-187">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="bfcb9-187">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="bfcb9-188">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-188">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="bfcb9-189">Отправляет клиенту код состояния [302 — Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-189">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="bfcb9-190">Перенаправляет клиент в конечную точку обработки ошибок, указанную в шаблоне URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-190">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="bfcb9-191">Конечная точка обработки ошибок обычно отображает сведения об ошибке и возвращает код HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-191">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="bfcb9-192">Шаблон URL-адреса может содержать заполнитель `{0}` для кода состояния, как показано в предыдущем коде.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-192">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="bfcb9-193">Если шаблон URL-адреса начинается с символа `~` (тильды), `~` заменяется `PathBase` приложения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-193">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="bfcb9-194">При указании конечной точки в приложении создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-194">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bfcb9-195">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) в файле [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-195">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="bfcb9-196">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-196">This method is commonly used when the app:</span></span>

* <span data-ttu-id="bfcb9-197">Должно перенаправлять клиент в другую конечную точку, что обычно бывает в случаях, когда другое приложение обрабатывает ошибку.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-197">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="bfcb9-198">Для веб-приложений в адресной строке браузера клиента отображается конечная точка перенаправления.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-198">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="bfcb9-199">Не должно сохранять и возвращать исходный код состояния с ответом первичного перенаправления.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-199">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="bfcb9-200">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), удалите комментарии из `webBuilder.UseStartup<StartupSCredirect>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-200">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="bfcb9-201">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="bfcb9-201">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="bfcb9-202">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-202">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="bfcb9-203">Возвращает исходный код состояния клиенту.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-203">Returns the original status code to the client.</span></span>
* <span data-ttu-id="bfcb9-204">Позволяет создать текст ответа путем повторного выполнения конвейера запросов с использованием другого пути.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-204">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="bfcb9-205">Если указывается конечная точка в приложении, создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-205">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bfcb9-206">Обязательно поместите `UseStatusCodePagesWithReExecute` перед `UseRouting`, чтобы запрос можно было перенаправить на страницу состояния.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-206">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="bfcb9-207">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) в файле [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-207">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="bfcb9-208">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-208">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="bfcb9-209">Обрабатывает запрос без перенаправления к другой конечной точке.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-209">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="bfcb9-210">Для веб-приложений в адресной строке браузера клиента отображается изначально запрошенная конечная точка.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-210">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="bfcb9-211">Сохраняет и возвращает исходный код состояния с ответом.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-211">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="bfcb9-212">Шаблоны URL-адреса и строки запроса могут содержать заполнитель `{0}` для кода состояния.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-212">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="bfcb9-213">Шаблон URL-адреса должен начинаться с символа `/`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-213">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="bfcb9-214">Конечная точка, которая обрабатывает ошибку, может получать исходный URL-адрес, вызвавший ошибку, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-214">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="bfcb9-215">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) в файле [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-215">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="bfcb9-216">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), удалите комментарии из `webBuilder.UseStartup<StartupSCreX>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-216">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="bfcb9-217">Отключение страниц с кодами состояния</span><span class="sxs-lookup"><span data-stu-id="bfcb9-217">Disable status code pages</span></span>

<span data-ttu-id="bfcb9-218">Чтобы отключить страницы кодов состояния для метода контроллера или действия MVC, используйте атрибут [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-218">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="bfcb9-219">Чтобы отключить страницы кодов состояния для конкретных запросов в методе обработчика Razor Pages или в контроллере MVC, используйте <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-219">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="bfcb9-220">Код обработки исключений</span><span class="sxs-lookup"><span data-stu-id="bfcb9-220">Exception-handling code</span></span>

<span data-ttu-id="bfcb9-221">Код на страницах обработки исключений также может создавать исключения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-221">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="bfcb9-222">Рабочие страницы ошибок необходимо тщательно тестировать, чтобы они не создавали собственных исключений.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-222">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="bfcb9-223">Заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="bfcb9-223">Response headers</span></span>

<span data-ttu-id="bfcb9-224">После отправки заголовков для ответа происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-224">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="bfcb9-225">Приложение не может изменить код состояния ответа.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-225">The app can't change the response's status code.</span></span>
* <span data-ttu-id="bfcb9-226">Нельзя запустить страницу или обработчик исключений.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-226">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="bfcb9-227">Необходимо завершить ответ или прервать подключение.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-227">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="bfcb9-228">Обработка исключений на сервере</span><span class="sxs-lookup"><span data-stu-id="bfcb9-228">Server exception handling</span></span>

<span data-ttu-id="bfcb9-229">Помимо логики обработки исключений в приложении, [реализация HTTP-сервера](xref:fundamentals/servers/index) также может обрабатывать некоторые исключения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-229">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="bfcb9-230">Если сервер перехватывает исключение перед отправкой заголовков ответа, он отсылает ответ `500 - Internal Server Error` (внутренняя ошибка сервера) без текста ответа.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-230">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="bfcb9-231">Если сервер перехватывает исключение после отправки заголовков ответа, он закрывает соединение.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-231">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="bfcb9-232">Запросы, не обработанные приложением, обрабатываются сервером.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-232">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="bfcb9-233">Все исключения, возникшие при обработке запроса серверов, обрабатываются с помощью механизма обработки исключений на сервере.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-233">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="bfcb9-234">Пользовательские страницы ошибок приложений, ПО промежуточного слоя для обработки исключений и фильтры не влияют на этот процесс.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-234">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="bfcb9-235">Обработка исключений при запуске</span><span class="sxs-lookup"><span data-stu-id="bfcb9-235">Startup exception handling</span></span>

<span data-ttu-id="bfcb9-236">Исключения, возникающие во время запуска приложения, могут обрабатываться только на уровне размещения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-236">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="bfcb9-237">Узел можно настроить для [перехвата ошибок при загрузке](xref:fundamentals/host/web-host#capture-startup-errors) и [записи подробных сведений об ошибках](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-237">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="bfcb9-238">На уровне размещения может отображаться страница со сведениями о перехваченной ошибке при загрузке, только если ошибка произошла после привязки адреса и порта узла.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-238">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="bfcb9-239">При сбое привязки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-239">If binding fails:</span></span>

* <span data-ttu-id="bfcb9-240">На уровне размещения в журнале фиксируется критическое исключение.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-240">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="bfcb9-241">Выполняется аварийное завершение процесса dotnet.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-241">The dotnet process crashes.</span></span>
* <span data-ttu-id="bfcb9-242">Если приложение запущено на HTTP-сервере [Kestrel](xref:fundamentals/servers/kestrel), страница со сведениями об ошибке не выводится.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-242">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="bfcb9-243">При работе в службах [IIS](/iis) (или Службе приложений Azure) либо [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)[модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) возвращает ошибку *502.5 Process Failure* (ошибка процесса), если процесс невозможно запустить.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-243">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="bfcb9-244">Для получения дополнительной информации см. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-244">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="bfcb9-245">Страница ошибок базы данных</span><span class="sxs-lookup"><span data-stu-id="bfcb9-245">Database error page</span></span>

<span data-ttu-id="bfcb9-246">Фильтр исключений для страницы разработчика базы данных `AddDatabaseDeveloperPageExceptionFilter` перехватывает исключения, относящиеся к базе данных, которые могут быть устранены с помощью миграций Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-246">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="bfcb9-247">При возникновении этих исключений формируется HTML-ответ с подробными сведениями о возможных действиях для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-247">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="bfcb9-248">Эта страница включается только в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-248">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="bfcb9-249">Следующий код был создан шаблонами страниц Razor в ASP.NET Core при указании отдельных учетных записей пользователей:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-249">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="bfcb9-250">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="bfcb9-250">Exception filters</span></span>

<span data-ttu-id="bfcb9-251">В приложениях MVC фильтры исключений можно настраивать как глобально, так и для отдельных контроллеров или действий.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-251">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="bfcb9-252">В приложениях Razor Pages они могут быть настроены глобально или для модели страницы.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-252">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="bfcb9-253">Эти фильтры обрабатывают все необработанные исключения, которые возникают во время выполнения действия контроллера или другого фильтра.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-253">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="bfcb9-254">Для получения дополнительной информации см. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-254">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="bfcb9-255">Фильтры исключений полезны при перехвате исключений, которые возникают в действиях MVC. Однако эти фильтры не так гибки, как встроенное [ПО промежуточного слоя для обработки исключений](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-255">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="bfcb9-256">Мы рекомендуем использовать `UseExceptionHandler`, если ошибки не нужно обрабатывать по-разному в зависимости от выбранного действия MVC.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-256">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="bfcb9-257">Ошибки состояния модели</span><span class="sxs-lookup"><span data-stu-id="bfcb9-257">Model state errors</span></span>

<span data-ttu-id="bfcb9-258">Сведения о том, как обрабатывать ошибки состояния модели, см. в статьях о [привязке модели](xref:mvc/models/model-binding) и [проверке модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-258">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bfcb9-259">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="bfcb9-259">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="bfcb9-260">Авторы: [Том Дикстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="bfcb9-260">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bfcb9-261">В этой статье рассматриваются основные методы обработки ошибок в веб-приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-261">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="bfcb9-262">Для веб-API см. раздел <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-262">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="bfcb9-263">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="bfcb9-264">См. раздел [Загрузка примера](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-264">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="bfcb9-265">Страница со сведениями об исключении для разработчика</span><span class="sxs-lookup"><span data-stu-id="bfcb9-265">Developer Exception Page</span></span>

<span data-ttu-id="bfcb9-266">*Страница исключений для разработчика* содержит подробные сведения об исключениях запросов.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-266">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="bfcb9-267">Шаблоны ASP.NET Core создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-267">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="bfcb9-268">Приведенный выше код включает страницу исключений для разработчика при выполнении приложения в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-268">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="bfcb9-269">Шаблоны помещают метод <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> перед всем ПО промежуточного слоя, чтобы исключения перехватывались в этом ПО.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-269">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="bfcb9-270">Приведенный выше код включает страницу исключений для разработчика **только при выполнении приложения в среде разработки**.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-270">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="bfcb9-271">Подробные сведения об исключениях не должны быть общедоступными при выполнении приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-271">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="bfcb9-272">Дополнительные сведения о настройке среды см. в статье <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-272">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bfcb9-273">Страница исключений для разработчика содержит следующие сведения об исключении и запросе:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-273">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="bfcb9-274">Трассировка стека</span><span class="sxs-lookup"><span data-stu-id="bfcb9-274">Stack trace</span></span>
* <span data-ttu-id="bfcb9-275">параметры строки запроса (при наличии);</span><span class="sxs-lookup"><span data-stu-id="bfcb9-275">Query string parameters if any</span></span>
* <span data-ttu-id="bfcb9-276">Cookie (при наличии);</span><span class="sxs-lookup"><span data-stu-id="bfcb9-276">Cookies if any</span></span>
* <span data-ttu-id="bfcb9-277">Заголовки</span><span class="sxs-lookup"><span data-stu-id="bfcb9-277">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="bfcb9-278">Страница обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="bfcb9-278">Exception handler page</span></span>

<span data-ttu-id="bfcb9-279">Чтобы настроить пользовательскую страницу обработки ошибок для рабочей среды, используйте ПО промежуточного слоя для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-279">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="bfcb9-280">ПО промежуточного слоя выполняет такие функции:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-280">The middleware:</span></span>

* <span data-ttu-id="bfcb9-281">Перехватывает и записывает в журнал исключения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-281">Catches and logs exceptions.</span></span>
* <span data-ttu-id="bfcb9-282">Повторно выполняет запрос в альтернативном конвейере для указанной страницы или контроллера.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-282">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="bfcb9-283">Запрос не выполняется повторно, если запущен отклик.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-283">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="bfcb9-284">Созданный шаблоном код повторно выполняет запрос к `/Error`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-284">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="bfcb9-285">В следующем примере с помощью <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> добавляется ПО промежуточного слоя для обработки исключений в средах, не предназначенных для разработки.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-285">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="bfcb9-286">Шаблон приложения Razor Pages предоставляет страницу ошибки (*CSHTML*) и класс <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-286">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="bfcb9-287">Для приложения MVC шаблон проекта содержит метод действия при возникновении ошибки и представление ошибок для контроллера Home.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-287">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="bfcb9-288">Не следует помечать метод действия обработки ошибок атрибутами метода HTTP, например `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-288">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="bfcb9-289">Из-за использования явных команд некоторые запросы могут не передаваться в метод.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-289">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="bfcb9-290">Разрешите анонимный доступ к методу, если не прошедшие проверку подлинности пользователи должны видеть представление ошибок.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-290">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="bfcb9-291">Доступ к исключению</span><span class="sxs-lookup"><span data-stu-id="bfcb9-291">Access the exception</span></span>

<span data-ttu-id="bfcb9-292">Используйте интерфейс <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, чтобы получить доступ к исключению и к пути исходного запроса в контроллере или на странице обработчика ошибок:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-292">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="bfcb9-293">**Не** передавайте клиентам конфиденциальную информацию об ошибках.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-293">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="bfcb9-294">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="bfcb9-295">Чтобы активировать предыдущую страницу обработки исключений, задайте рабочую среду и принудительно вызовите исключение.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-295">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="bfcb9-296">Лямбда-функция для обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="bfcb9-296">Exception handler lambda</span></span>

<span data-ttu-id="bfcb9-297">Альтернативой [пользовательской странице обработчика исключений](#exception-handler-page) является предоставление лямбда-функции для <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-297">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="bfcb9-298">Использование лямбда-функции позволяет получить доступ к ошибке до возврата ответа.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-298">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="bfcb9-299">Ниже приведен пример использования лямбда-функции для обработки исключений:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-299">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="bfcb9-300">В приведенном выше коде добавляется `await context.Response.WriteAsync(new string(' ', 512));`, поэтому браузер Internet Explorer отображает сообщение об ошибке, а не сообщение об ошибке IE.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-300">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="bfcb9-301">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-301">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="bfcb9-302">**Не** передавайте клиентам конфиденциальную информацию об ошибках из <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> или <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-302">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="bfcb9-303">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-303">Serving errors is a security risk.</span></span>

<span data-ttu-id="bfcb9-304">Чтобы просмотреть результат обработки ошибок лямбда-функцией в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте директивы препроцессора `ProdEnvironment` и `ErrorHandlerLambda`, а на домашней странице выберите **Trigger an exception** (Вызывать исключение).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-304">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="bfcb9-305">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bfcb9-305">UseStatusCodePages</span></span>

<span data-ttu-id="bfcb9-306">По умолчанию приложение ASP.NET Core не предоставляет страницы для кодов состояния HTTP, таких как код *404 Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-306">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="bfcb9-307">Приложение возвращает код состояния без текста ответа.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-307">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="bfcb9-308">Чтобы указать коды состояния, используйте ПО промежуточного слоя страниц с кодами состояния.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-308">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="bfcb9-309">Это ПО промежуточного слоя доступно в пакете [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-309">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="bfcb9-310">Чтобы включить обработчики по умолчанию, возвращающие только текст для распространенных кодов состояния ошибки, вызовите <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-310">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="bfcb9-311">Вызовите `UseStatusCodePages` до ПО промежуточного слоя для обработки запросов (например ПО промежуточного слоя для статических файлов и ПО промежуточного слоя MVC).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-311">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="bfcb9-312">Если `UseStatusCodePages` не используется, при переходе по URL-адресу без конечной точки возвращается зависящее от браузера сообщение об ошибке, в котором указывается, что конечная точка не найдена.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-312">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="bfcb9-313">Например, можно перейти по следующему адресу: `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-313">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="bfcb9-314">В этом случае при вызове `UseStatusCodePages` браузер вернет следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-314">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="bfcb9-315">UseStatusCodePages со строкой формата</span><span class="sxs-lookup"><span data-stu-id="bfcb9-315">UseStatusCodePages with format string</span></span>

<span data-ttu-id="bfcb9-316">Чтобы настроить тип содержимого и указать текст ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает тип содержимого и строку формата.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-316">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="bfcb9-317">UseStatusCodePages с лямбда-выражением</span><span class="sxs-lookup"><span data-stu-id="bfcb9-317">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="bfcb9-318">Чтобы указать пользовательский код для обработки ошибок и отображения ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает лямбда-выражение.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-318">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="bfcb9-319">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="bfcb9-319">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="bfcb9-320">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-320">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="bfcb9-321">Отправляет клиенту код состояния *302 — Found*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-321">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="bfcb9-322">Перенаправляет клиента к расположению, предоставленному в шаблоне URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-322">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="bfcb9-323">Шаблон URL-адреса может содержать заполнитель `{0}` для кода состояния, как показано в примере.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-323">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="bfcb9-324">Если шаблон URL-адреса начинается с символа `~` (тильды), `~` заменяется `PathBase` приложения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-324">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="bfcb9-325">Если вы указываете на конечную точку в приложении, создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-325">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bfcb9-326">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) в файле *Pages/StatusCode.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-326">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="bfcb9-327">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-327">This method is commonly used when the app:</span></span>

* <span data-ttu-id="bfcb9-328">Должно перенаправлять клиент в другую конечную точку, что обычно бывает в случаях, когда другое приложение обрабатывает ошибку.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-328">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="bfcb9-329">Для веб-приложений в адресной строке браузера клиента отображается конечная точка перенаправления.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-329">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="bfcb9-330">Не должно сохранять и возвращать исходный код состояния с ответом первичного перенаправления.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-330">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="bfcb9-331">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="bfcb9-331">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="bfcb9-332">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-332">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="bfcb9-333">Возвращает исходный код состояния клиенту.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-333">Returns the original status code to the client.</span></span>
* <span data-ttu-id="bfcb9-334">Позволяет создать текст ответа путем повторного выполнения конвейера запросов с использованием другого пути.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-334">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="bfcb9-335">Если вы указываете на конечную точку в приложении, создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-335">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bfcb9-336">Обязательно поместите `UseStatusCodePagesWithReExecute` перед `UseRouting`, чтобы запрос можно было перенаправить на страницу состояния.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-336">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="bfcb9-337">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) в файле *Pages/StatusCode.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-337">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="bfcb9-338">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-338">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="bfcb9-339">Обрабатывает запрос без перенаправления к другой конечной точке.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-339">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="bfcb9-340">Для веб-приложений в адресной строке браузера клиента отображается изначально запрошенная конечная точка.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-340">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="bfcb9-341">Сохраняет и возвращает исходный код состояния с ответом.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-341">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="bfcb9-342">Шаблоны URL-адреса и строки запроса могут содержать заполнитель (`{0}`) для кода состояния.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-342">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="bfcb9-343">Шаблон URL-адреса должен начинаться с косой черты (`/`).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-343">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="bfcb9-344">При использовании заполнителя в пути убедитесь, что конечная точка (страница или контроллер) могут обрабатывать сегмент пути.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-344">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="bfcb9-345">Например, страница Razor для ошибок должна принимать необязательное значение сегмента с директивой `@page`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-345">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="bfcb9-346">Конечная точка, которая обрабатывает ошибку, может получать исходный URL-адрес, вызвавший ошибку, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-346">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="bfcb9-347">Отключение страниц с кодами состояния</span><span class="sxs-lookup"><span data-stu-id="bfcb9-347">Disable status code pages</span></span>

<span data-ttu-id="bfcb9-348">Чтобы отключить страницы кодов состояния для метода контроллера или действия MVC, используйте атрибут [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-348">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="bfcb9-349">Чтобы отключить страницы кодов состояния для конкретных запросов в методе обработчика Razor Pages или в контроллере MVC, используйте <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-349">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="bfcb9-350">Код обработки исключений</span><span class="sxs-lookup"><span data-stu-id="bfcb9-350">Exception-handling code</span></span>

<span data-ttu-id="bfcb9-351">Код на страницах обработки исключений может создавать исключения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-351">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="bfcb9-352">Часто желательно, чтобы страницы ошибок в рабочей среде содержали только статическое содержимое.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-352">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="bfcb9-353">Заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="bfcb9-353">Response headers</span></span>

<span data-ttu-id="bfcb9-354">После отправки заголовков для ответа происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-354">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="bfcb9-355">Приложение не может изменить код состояния ответа.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-355">The app can't change the response's status code.</span></span>
* <span data-ttu-id="bfcb9-356">Нельзя запустить страницу или обработчик исключений.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-356">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="bfcb9-357">Необходимо завершить ответ или прервать подключение.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-357">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="bfcb9-358">Обработка исключений на сервере</span><span class="sxs-lookup"><span data-stu-id="bfcb9-358">Server exception handling</span></span>

<span data-ttu-id="bfcb9-359">Помимо логики обработки исключений в приложении, [реализация HTTP-сервера](xref:fundamentals/servers/index) может выполнять ряд операций по обработке исключений.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-359">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="bfcb9-360">Если сервер перехватывает исключение перед отправкой заголовков ответа, он отсылает ответ *500 Internal Server Error* (внутренняя ошибка сервера) без текста ответа.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-360">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="bfcb9-361">Если сервер перехватывает исключение после отправки заголовков ответа, он закрывает соединение.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-361">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="bfcb9-362">Запросы, не обработанные приложением, обрабатываются сервером.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-362">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="bfcb9-363">Все исключения, возникшие при обработке запроса серверов, обрабатываются с помощью механизма обработки исключений на сервере.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-363">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="bfcb9-364">Пользовательские страницы ошибок приложений, ПО промежуточного слоя для обработки исключений и фильтры не влияют на этот процесс.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-364">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="bfcb9-365">Обработка исключений при запуске</span><span class="sxs-lookup"><span data-stu-id="bfcb9-365">Startup exception handling</span></span>

<span data-ttu-id="bfcb9-366">Исключения, возникающие во время запуска приложения, могут обрабатываться только на уровне размещения.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-366">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="bfcb9-367">Узел можно настроить для [перехвата ошибок при загрузке](xref:fundamentals/host/web-host#capture-startup-errors) и [записи подробных сведений об ошибках](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-367">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="bfcb9-368">На уровне размещения может отображаться страница со сведениями о перехваченной ошибке при загрузке, только если ошибка произошла после привязки адреса и порта узла.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-368">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="bfcb9-369">При сбое привязки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="bfcb9-369">If binding fails:</span></span>

* <span data-ttu-id="bfcb9-370">На уровне размещения в журнале фиксируется критическое исключение.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-370">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="bfcb9-371">Выполняется аварийное завершение процесса dotnet.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-371">The dotnet process crashes.</span></span>
* <span data-ttu-id="bfcb9-372">Если приложение запущено на HTTP-сервере [Kestrel](xref:fundamentals/servers/kestrel), страница со сведениями об ошибке не выводится.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-372">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="bfcb9-373">При работе в службах [IIS](/iis) (или Службе приложений Azure) либо [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)[модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) возвращает ошибку *502.5 Process Failure* (ошибка процесса), если процесс невозможно запустить.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-373">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="bfcb9-374">Для получения дополнительной информации см. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-374">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="bfcb9-375">Страница ошибок базы данных</span><span class="sxs-lookup"><span data-stu-id="bfcb9-375">Database error page</span></span>

<span data-ttu-id="bfcb9-376">ПО промежуточного слоя для отображения страницы ошибок базы данных перехватывает исключения, относящиеся к базе данных, которые могут быть устранены при помощи миграций Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-376">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="bfcb9-377">При возникновении этих исключений формируется HTML-ответ с подробными сведениями о возможных действиях для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-377">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="bfcb9-378">Эту страницу следует включать только в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-378">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="bfcb9-379">Включите страницу, добавив код в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-379">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="bfcb9-380">Для <xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> необходим пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-380"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="bfcb9-381">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="bfcb9-381">Exception filters</span></span>

<span data-ttu-id="bfcb9-382">В приложениях MVC фильтры исключений можно настраивать как глобально, так и для отдельных контроллеров или действий.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-382">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="bfcb9-383">В приложениях Razor Pages они могут быть настроены глобально или для модели страницы.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-383">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="bfcb9-384">Эти фильтры обрабатывают все необработанные исключения, которые возникают во время выполнения действия контроллера или другого фильтра.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-384">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="bfcb9-385">Для получения дополнительной информации см. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-385">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="bfcb9-386">Фильтры исключений полезны при перехвате исключений, которые возникают в действиях MVC. Но эти фильтры не так гибки, как ПО промежуточного слоя для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-386">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="bfcb9-387">Мы рекомендуем использовать ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-387">We recommend using the middleware.</span></span> <span data-ttu-id="bfcb9-388">Используйте фильтры, только если ошибки нужно обрабатывать по-разному в зависимости от выбранного действия MVC.</span><span class="sxs-lookup"><span data-stu-id="bfcb9-388">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="bfcb9-389">Ошибки состояния модели</span><span class="sxs-lookup"><span data-stu-id="bfcb9-389">Model state errors</span></span>

<span data-ttu-id="bfcb9-390">Сведения о том, как обрабатывать ошибки состояния модели, см. в статьях о [привязке модели](xref:mvc/models/model-binding) и [проверке модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="bfcb9-390">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bfcb9-391">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="bfcb9-391">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
