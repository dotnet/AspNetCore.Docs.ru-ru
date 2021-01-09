---
title: Обработка ошибок в веб-API ASP.NET Core
author: pranavkm
description: Сведения об обработке ошибок с помощью веб-API ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 1/11/2021
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
uid: web-api/handle-errors
ms.openlocfilehash: 92e9350a7892f8f38f64d4ebd68d54a97ec7e994
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058380"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="d3587-103">Обработка ошибок в веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3587-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="d3587-104">В этой статье описывается обработка и настройка обработки ошибок с помощью веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3587-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="d3587-105">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Загрузка](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d3587-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="d3587-106">Страница со сведениями об исключении для разработчика</span><span class="sxs-lookup"><span data-stu-id="d3587-106">Developer Exception Page</span></span>

<span data-ttu-id="d3587-107">Страница [со сведениями об исключении для разработчика](xref:fundamentals/error-handling) — это полезное средство, с помощью которого можно получить подробные трассировки стека для ошибок сервера.</span><span class="sxs-lookup"><span data-stu-id="d3587-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="d3587-108">Он использует <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> для записи синхронных и асинхронных исключений из конвейера HTTP и для создания ответов об ошибках.</span><span class="sxs-lookup"><span data-stu-id="d3587-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="d3587-109">Для иллюстрации рассмотрим следующее действие контроллера:</span><span class="sxs-lookup"><span data-stu-id="d3587-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="d3587-110">Выполните следующую команду `curl`, чтобы проверить предыдущее действие:</span><span class="sxs-lookup"><span data-stu-id="d3587-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3587-111">В ASP.NET Core 3.0 и более поздних версиях на странице со сведениями об исключении для разработчика ответ отображается в виде обычного текста, если клиент не запрашивает выходные данные в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="d3587-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="d3587-112">Отображаются следующие результаты:</span><span class="sxs-lookup"><span data-stu-id="d3587-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="d3587-113">Чтобы вместо этого отображался отформатированный HTML-запрос, задайте для заголовка HTTP-запроса `Accept` тип носителя `text/html`.</span><span class="sxs-lookup"><span data-stu-id="d3587-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="d3587-114">Например:</span><span class="sxs-lookup"><span data-stu-id="d3587-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="d3587-115">Рассмотрим следующий фрагмент HTTP-ответа:</span><span class="sxs-lookup"><span data-stu-id="d3587-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="d3587-116">В ASP.NET Core 2.2 и более ранних версиях на странице исключения для разработчика отображается ответ в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="d3587-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="d3587-117">Например, рассмотрим следующий фрагмент HTTP-ответа:</span><span class="sxs-lookup"><span data-stu-id="d3587-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3587-118">Отформатированный HTML-ответ полезен при тестировании с помощью таких инструментов, как Postman.</span><span class="sxs-lookup"><span data-stu-id="d3587-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="d3587-119">На следующем снимке экрана показаны ответы в виде обычного текста и в формате HTML в Postman:</span><span class="sxs-lookup"><span data-stu-id="d3587-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Тестирование страницы исключения для разработчика в Postman](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="d3587-121">Включать страницу исключений для разработчика следует **только тогда, когда приложение выполняется в среде разработки**.</span><span class="sxs-lookup"><span data-stu-id="d3587-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="d3587-122">Не предоставляйте общий доступ к подробным сведениям об исключении при запуске приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="d3587-122">Don't share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="d3587-123">Дополнительные сведения о настройке среды см. в статье <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d3587-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>
>
> <span data-ttu-id="d3587-124">Не следует помечать метод действия обработки ошибок атрибутами метода HTTP, например `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="d3587-124">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="d3587-125">Из-за использования явных команд некоторые запросы могут не передаваться в метод действия.</span><span class="sxs-lookup"><span data-stu-id="d3587-125">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="d3587-126">Разрешить анонимный доступ к методу, если пользователи, не прошедшие проверку подлинности, должны увидеть ошибку.</span><span class="sxs-lookup"><span data-stu-id="d3587-126">Allow anonymous access to the method if unauthenticated users should see the error.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="d3587-127">Обработчик исключений</span><span class="sxs-lookup"><span data-stu-id="d3587-127">Exception handler</span></span>

<span data-ttu-id="d3587-128">В средах, не относящихся к разработке, для получения полезных данных об ошибках можно использовать [ПО промежуточного слоя для обработки исключений](xref:fundamentals/error-handling).</span><span class="sxs-lookup"><span data-stu-id="d3587-128">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="d3587-129">В `Startup.Configure` вызовите <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>, чтобы использовать ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="d3587-129">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="d3587-130">Настройте действие контроллера для ответа на маршрут `/error`:</span><span class="sxs-lookup"><span data-stu-id="d3587-130">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="d3587-131">Предыдущее действие `Error` отправляет клиенту полезные данные, соответствующие [RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="d3587-131">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="d3587-132">ПО промежуточного слоя для обработки исключений также может предоставлять более подробные данные, согласованные с содержимым, в локальной среде разработки.</span><span class="sxs-lookup"><span data-stu-id="d3587-132">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="d3587-133">Чтобы создать согласованный формат полезных данных в среде разработки и рабочей среде, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="d3587-133">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="d3587-134">В `Startup.Configure` зарегистрируйте экземпляры ПО промежуточного слоя обработки исключений для конкретной среды:</span><span class="sxs-lookup"><span data-stu-id="d3587-134">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="d3587-135">В приведенном выше коде ПО промежуточного слоя регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d3587-135">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="d3587-136">Маршрут `/error-local-development` в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="d3587-136">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="d3587-137">Маршрут `/error` в средах, не имеющих отношения к разработке.</span><span class="sxs-lookup"><span data-stu-id="d3587-137">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="d3587-138">Примените маршрутизацию атрибутов к действиям контроллера:</span><span class="sxs-lookup"><span data-stu-id="d3587-138">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    <span data-ttu-id="d3587-139">Приведенный выше код вызывает [ControllerBase. проблема](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) создания <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> ответа.</span><span class="sxs-lookup"><span data-stu-id="d3587-139">The preceding code calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response.</span></span>

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="d3587-140">Изменение ответа с помощью исключений</span><span class="sxs-lookup"><span data-stu-id="d3587-140">Use exceptions to modify the response</span></span>

<span data-ttu-id="d3587-141">Содержимое ответа можно изменить за пределами контроллера.</span><span class="sxs-lookup"><span data-stu-id="d3587-141">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="d3587-142">В веб-API ASP.NET 4.x один из способов это сделать — использовать тип <xref:System.Web.Http.HttpResponseException>.</span><span class="sxs-lookup"><span data-stu-id="d3587-142">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="d3587-143">ASP.NET Core не содержит эквивалентный тип.</span><span class="sxs-lookup"><span data-stu-id="d3587-143">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="d3587-144">Поддержку `HttpResponseException` можно добавить, сделав следующее:</span><span class="sxs-lookup"><span data-stu-id="d3587-144">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="d3587-145">Создайте известный тип исключения с именем `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="d3587-145">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="d3587-146">Создайте фильтр действий с именем `HttpResponseExceptionFilter`:</span><span class="sxs-lookup"><span data-stu-id="d3587-146">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    <span data-ttu-id="d3587-147">Предыдущий фильтр указывает `Order` максимальное целое значение минус 10.</span><span class="sxs-lookup"><span data-stu-id="d3587-147">The preceding filter specifies an `Order` of the maximum integer value minus 10.</span></span> <span data-ttu-id="d3587-148">Это позволяет другим фильтрам выполняться в конце конвейера.</span><span class="sxs-lookup"><span data-stu-id="d3587-148">This allows other filters to run at the end of the pipeline.</span></span>

1. <span data-ttu-id="d3587-149">В `Startup.ConfigureServices` добавьте фильтр действий в коллекцию фильтров:</span><span class="sxs-lookup"><span data-stu-id="d3587-149">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="d3587-150">Ответ в случае ошибки при сбое проверки</span><span class="sxs-lookup"><span data-stu-id="d3587-150">Validation failure error response</span></span>

<span data-ttu-id="d3587-151">В контроллерах веб-API MVC отвечает с помощью типа ответа <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> при сбое проверки модели.</span><span class="sxs-lookup"><span data-stu-id="d3587-151">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="d3587-152">MVC использует результаты <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> для создания ответа в случае ошибки при сбое проверки.</span><span class="sxs-lookup"><span data-stu-id="d3587-152">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="d3587-153">В следующем примере в `Startup.ConfigureServices` для изменения типа ответа по умолчанию на <xref:Microsoft.AspNetCore.Mvc.SerializableError> используется фабрика:</span><span class="sxs-lookup"><span data-stu-id="d3587-153">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="d3587-154">Ответ при ошибке клиента</span><span class="sxs-lookup"><span data-stu-id="d3587-154">Client error response</span></span>

<span data-ttu-id="d3587-155">*Результат ошибки* определяется как результат с кодом состояния HTTP 400 или выше.</span><span class="sxs-lookup"><span data-stu-id="d3587-155">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="d3587-156">В контроллерах веб-API платформа MVC преобразует результат ошибки в результат с <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="d3587-156">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="d3587-157">ASP.NET Core 2.1 выдает ответ со сведениями о проблеме, почти соответствующий RFC 7807.</span><span class="sxs-lookup"><span data-stu-id="d3587-157">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="d3587-158">Если важно обеспечить полное соответствие требованиям, обновите проект до ASP.NET Core 2.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="d3587-158">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3587-159">Ответ при ошибке можно настроить одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="d3587-159">The error response can be configured in one of the following ways:</span></span>

1. <span data-ttu-id="d3587-160">[Реализация ProblemDetailsFactory](#implement-problemdetailsfactory).</span><span class="sxs-lookup"><span data-stu-id="d3587-160">[Implement ProblemDetailsFactory](#implement-problemdetailsfactory)</span></span>
1. <span data-ttu-id="d3587-161">[Использование ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping).</span><span class="sxs-lookup"><span data-stu-id="d3587-161">[Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping)</span></span>

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="d3587-162">Реализуйте `ProblemDetailsFactory`</span><span class="sxs-lookup"><span data-stu-id="d3587-162">Implement `ProblemDetailsFactory`</span></span>

<span data-ttu-id="d3587-163">MVC создает все экземпляры <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> и <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> с помощью <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d3587-163">MVC uses <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="d3587-164">Сюда относятся ответы при ошибках клиента, ответы в случае ошибок при сбое проверки, а также вспомогательные методы <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> и <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="d3587-164">This includes client error responses, validation failure error responses, and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper methods.</span></span>

<span data-ttu-id="d3587-165">Чтобы настроить ответ с подробными сведениями о проблемах, зарегистрируйте пользовательскую реализацию <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d3587-165">To customize the problem details response, register a custom implementation of <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d3587-166">Ответ при ошибке можно настроить, как описано в разделе [Использование ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping).</span><span class="sxs-lookup"><span data-stu-id="d3587-166">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="d3587-167">Использование ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="d3587-167">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="d3587-168">Используйте свойство <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A>, чтобы настроить содержимое ответа `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="d3587-168">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="d3587-169">Например, следующий код в `type` обновляет свойство `Startup.ConfigureServices` для ответов 404:</span><span class="sxs-lookup"><span data-stu-id="d3587-169">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a><span data-ttu-id="d3587-170">Настраиваемое по промежуточного слоя для управления исключениями</span><span class="sxs-lookup"><span data-stu-id="d3587-170">Custom Middleware to handle exceptions</span></span>

<span data-ttu-id="d3587-171">Значения по умолчанию в по промежуточного слоя обработки исключений прекрасно подходят для большинства приложений.</span><span class="sxs-lookup"><span data-stu-id="d3587-171">The defaults in the exception handling middleware works well for most apps.</span></span> <span data-ttu-id="d3587-172">Для приложений, требующих специализированной обработки исключений, рассмотрите возможность [настройки по промежуточного слоя обработки исключений](xref:fundamentals/error-handling#exception-handler-lambda).</span><span class="sxs-lookup"><span data-stu-id="d3587-172">For apps that require specialized exception handling, consider [customizing the exception handling middleware](xref:fundamentals/error-handling#exception-handler-lambda).</span></span>
