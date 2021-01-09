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
# <a name="handle-errors-in-aspnet-core-web-apis"></a>Обработка ошибок в веб-API ASP.NET Core

В этой статье описывается обработка и настройка обработки ошибок с помощью веб-API ASP.NET Core.

[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Загрузка](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Страница со сведениями об исключении для разработчика

Страница [со сведениями об исключении для разработчика](xref:fundamentals/error-handling) — это полезное средство, с помощью которого можно получить подробные трассировки стека для ошибок сервера. Он использует <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> для записи синхронных и асинхронных исключений из конвейера HTTP и для создания ответов об ошибках. Для иллюстрации рассмотрим следующее действие контроллера:

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

Выполните следующую команду `curl`, чтобы проверить предыдущее действие:

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

В ASP.NET Core 3.0 и более поздних версиях на странице со сведениями об исключении для разработчика ответ отображается в виде обычного текста, если клиент не запрашивает выходные данные в формате HTML. Отображаются следующие результаты:

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

Чтобы вместо этого отображался отформатированный HTML-запрос, задайте для заголовка HTTP-запроса `Accept` тип носителя `text/html`. Например:

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

Рассмотрим следующий фрагмент HTTP-ответа:

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

В ASP.NET Core 2.2 и более ранних версиях на странице исключения для разработчика отображается ответ в формате HTML. Например, рассмотрим следующий фрагмент HTTP-ответа:

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

Отформатированный HTML-ответ полезен при тестировании с помощью таких инструментов, как Postman. На следующем снимке экрана показаны ответы в виде обычного текста и в формате HTML в Postman:

![Тестирование страницы исключения для разработчика в Postman](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> Включать страницу исключений для разработчика следует **только тогда, когда приложение выполняется в среде разработки**. Не предоставляйте общий доступ к подробным сведениям об исключении при запуске приложения в рабочей среде. Дополнительные сведения о настройке среды см. в статье <xref:fundamentals/environments>.
>
> Не следует помечать метод действия обработки ошибок атрибутами метода HTTP, например `HttpGet`. Из-за использования явных команд некоторые запросы могут не передаваться в метод действия. Разрешить анонимный доступ к методу, если пользователи, не прошедшие проверку подлинности, должны увидеть ошибку.

## <a name="exception-handler"></a>Обработчик исключений

В средах, не относящихся к разработке, для получения полезных данных об ошибках можно использовать [ПО промежуточного слоя для обработки исключений](xref:fundamentals/error-handling).

1. В `Startup.Configure` вызовите <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>, чтобы использовать ПО промежуточного слоя:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. Настройте действие контроллера для ответа на маршрут `/error`:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

Предыдущее действие `Error` отправляет клиенту полезные данные, соответствующие [RFC 7807](https://tools.ietf.org/html/rfc7807).

ПО промежуточного слоя для обработки исключений также может предоставлять более подробные данные, согласованные с содержимым, в локальной среде разработки. Чтобы создать согласованный формат полезных данных в среде разработки и рабочей среде, сделайте следующее:

1. В `Startup.Configure` зарегистрируйте экземпляры ПО промежуточного слоя обработки исключений для конкретной среды:

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

    В приведенном выше коде ПО промежуточного слоя регистрируется с помощью внедрения зависимостей.

    * Маршрут `/error-local-development` в среде разработки.
    * Маршрут `/error` в средах, не имеющих отношения к разработке.
    
1. Примените маршрутизацию атрибутов к действиям контроллера:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    Приведенный выше код вызывает [ControllerBase. проблема](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) создания <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> ответа.

## <a name="use-exceptions-to-modify-the-response"></a>Изменение ответа с помощью исключений

Содержимое ответа можно изменить за пределами контроллера. В веб-API ASP.NET 4.x один из способов это сделать — использовать тип <xref:System.Web.Http.HttpResponseException>. ASP.NET Core не содержит эквивалентный тип. Поддержку `HttpResponseException` можно добавить, сделав следующее:

1. Создайте известный тип исключения с именем `HttpResponseException`:

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Создайте фильтр действий с именем `HttpResponseExceptionFilter`:

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    Предыдущий фильтр указывает `Order` максимальное целое значение минус 10. Это позволяет другим фильтрам выполняться в конце конвейера.

1. В `Startup.ConfigureServices` добавьте фильтр действий в коллекцию фильтров:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>Ответ в случае ошибки при сбое проверки

В контроллерах веб-API MVC отвечает с помощью типа ответа <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> при сбое проверки модели. MVC использует результаты <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> для создания ответа в случае ошибки при сбое проверки. В следующем примере в `Startup.ConfigureServices` для изменения типа ответа по умолчанию на <xref:Microsoft.AspNetCore.Mvc.SerializableError> используется фабрика:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>Ответ при ошибке клиента

*Результат ошибки* определяется как результат с кодом состояния HTTP 400 или выше. В контроллерах веб-API платформа MVC преобразует результат ошибки в результат с <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> ASP.NET Core 2.1 выдает ответ со сведениями о проблеме, почти соответствующий RFC 7807. Если важно обеспечить полное соответствие требованиям, обновите проект до ASP.NET Core 2.2 или более поздней версии.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Ответ при ошибке можно настроить одним из следующих способов:

1. [Реализация ProblemDetailsFactory](#implement-problemdetailsfactory).
1. [Использование ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping).

### <a name="implement-problemdetailsfactory"></a>Реализуйте `ProblemDetailsFactory`

MVC создает все экземпляры <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> и <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> с помощью <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName>. Сюда относятся ответы при ошибках клиента, ответы в случае ошибок при сбое проверки, а также вспомогательные методы <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> и <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType>.

Чтобы настроить ответ с подробными сведениями о проблемах, зарегистрируйте пользовательскую реализацию <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> в `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Ответ при ошибке можно настроить, как описано в разделе [Использование ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping).

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>Использование ApiBehaviorOptions.ClientErrorMapping

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A>, чтобы настроить содержимое ответа `ProblemDetails`. Например, следующий код в `type` обновляет свойство `Startup.ConfigureServices` для ответов 404:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a>Настраиваемое по промежуточного слоя для управления исключениями

Значения по умолчанию в по промежуточного слоя обработки исключений прекрасно подходят для большинства приложений. Для приложений, требующих специализированной обработки исключений, рассмотрите возможность [настройки по промежуточного слоя обработки исключений](xref:fundamentals/error-handling#exception-handler-lambda).
