---
title: Создание веб-интерфейсов API JSON из gRPC
author: jamesnk
description: Узнайте, как создавать интерфейсы API JSON HTTP для служб gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 78247a9b775ec8c9a3bc4a58209f09e5b714c07d
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217522"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="5520a-103">Создание веб-интерфейсов API JSON из gRPC</span><span class="sxs-lookup"><span data-stu-id="5520a-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="5520a-104">Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5520a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5520a-105">API HTTP gRPC — это экспериментальный проект и пока не является готовым продуктом.</span><span class="sxs-lookup"><span data-stu-id="5520a-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="5520a-106">Наши задачи:</span><span class="sxs-lookup"><span data-stu-id="5520a-106">We want to:</span></span>
>
> * <span data-ttu-id="5520a-107">Проверка, работает ли наш метод создания веб-API JSON для служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="5520a-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="5520a-108">Получение отзывов, полезен ли этот метод для разработчиков .NET.</span><span class="sxs-lookup"><span data-stu-id="5520a-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="5520a-109">Просим вас [оставлять отзывы](https://github.com/grpc/grpc-dotnet/issues/167), чтобы мы могли понять, создаем ли мы удобный и эффективный продукт для разработчиков.</span><span class="sxs-lookup"><span data-stu-id="5520a-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="5520a-110">gRPC — это современный способ обмена данными между приложениями.</span><span class="sxs-lookup"><span data-stu-id="5520a-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="5520a-111">gRPC использует HTTP/2, потоковую передачу, Protobuf и контракты сообщений для создания высокопроизводительных служб в реальном времени.</span><span class="sxs-lookup"><span data-stu-id="5520a-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="5520a-112">gRPC имеет единственное ограничение — его можно использовать не на каждой платформе.</span><span class="sxs-lookup"><span data-stu-id="5520a-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="5520a-113">Браузеры не полностью поддерживают HTTP/2, поэтому REST и JSON являются основными способами передачи данных в браузерные приложения.</span><span class="sxs-lookup"><span data-stu-id="5520a-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="5520a-114">Даже при всех преимуществах gRPC REST и JSON играют важную роль в современных приложениях.</span><span class="sxs-lookup"><span data-stu-id="5520a-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="5520a-115">Создание веб-API gRPC \***и** _ JSON добавляет нежелательные затраты на разработку приложений.</span><span class="sxs-lookup"><span data-stu-id="5520a-115">Building gRPC \***and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="5520a-116">В этом документе рассматривается создание веб-API JSON с помощью служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="5520a-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="5520a-117">API gRPC HTTP</span><span class="sxs-lookup"><span data-stu-id="5520a-117">gRPC HTTP API</span></span>

<span data-ttu-id="5520a-118">API gRPC HTTP — это экспериментальная сборка для ASP.NET Core, которая создает API-интерфейсы RESTful JSON для служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="5520a-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="5520a-119">После настройки API gRPC HTTP позволяет приложениям вызывать службы gRPC, используя следующие привычные понятия HTTP:</span><span class="sxs-lookup"><span data-stu-id="5520a-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="5520a-120">_ HTTP-команды</span><span class="sxs-lookup"><span data-stu-id="5520a-120">_ HTTP verbs</span></span>
* <span data-ttu-id="5520a-121">привязку параметра URL-адреса;</span><span class="sxs-lookup"><span data-stu-id="5520a-121">URL parameter binding</span></span>
* <span data-ttu-id="5520a-122">запросы и ответы JSON.</span><span class="sxs-lookup"><span data-stu-id="5520a-122">JSON requests/responses</span></span>

<span data-ttu-id="5520a-123">gRPC по-прежнему можно использовать для вызова служб.</span><span class="sxs-lookup"><span data-stu-id="5520a-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="5520a-124">Использование</span><span class="sxs-lookup"><span data-stu-id="5520a-124">Usage</span></span>

1. <span data-ttu-id="5520a-125">Добавьте ссылку на пакет в [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="5520a-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="5520a-126">Зарегистрируйте службы в *Startup.cs* с помощью `AddGrpcHttpApi`.</span><span class="sxs-lookup"><span data-stu-id="5520a-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="5520a-127">Добавьте в свой проект файлы [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) и [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto).</span><span class="sxs-lookup"><span data-stu-id="5520a-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="5520a-128">Добавьте заметки в методы gRPC в ваших файлах *PROTO* с маршрутами и привязками HTTP:</span><span class="sxs-lookup"><span data-stu-id="5520a-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

[!code-protobuf[](~/grpc/httpapi/greet.proto?highlight=3,9-11)]

<span data-ttu-id="5520a-129">Теперь метод `SayHello` gRPC можно вызывать как gRPC + Protobuf и как API HTTP:</span><span class="sxs-lookup"><span data-stu-id="5520a-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="5520a-130">Запрос: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="5520a-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="5520a-131">Ответ: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="5520a-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="5520a-132">Серверные журналы показывают, что HTTP-вызов выполняется службой gRPC.</span><span class="sxs-lookup"><span data-stu-id="5520a-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="5520a-133">API gRPC HTTP сопоставляет входящий HTTP-запрос с сообщением gRPC, а затем преобразует ответное сообщение в JSON.</span><span class="sxs-lookup"><span data-stu-id="5520a-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="5520a-134">Ниже приводится простой пример.</span><span class="sxs-lookup"><span data-stu-id="5520a-134">This is a basic example.</span></span> <span data-ttu-id="5520a-135">Дополнительные возможности настройки см. в разделе [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule).</span><span class="sxs-lookup"><span data-stu-id="5520a-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="enable-swaggeropenapi-support"></a><span data-ttu-id="5520a-136">Включение поддержки Swagger (OpenAPI)</span><span class="sxs-lookup"><span data-stu-id="5520a-136">Enable Swagger/OpenAPI support</span></span>

<span data-ttu-id="5520a-137">Swagger (OpenAPI) — это не зависящая от языка спецификация для описания REST API.</span><span class="sxs-lookup"><span data-stu-id="5520a-137">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="5520a-138">API gRPC HTTP можно интегрировать с [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore), чтобы создать конечную точку Swagger для служб gRPC на основе REST.</span><span class="sxs-lookup"><span data-stu-id="5520a-138">gRPC HTTP API can integrate with [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) to generate a Swagger endpoint for RESTful gRPC services.</span></span> <span data-ttu-id="5520a-139">Затем эту конечную точку Swagger можно использовать с [пользовательским интерфейсом Swagger](https://swagger.io/swagger-ui/) и другими инструментами.</span><span class="sxs-lookup"><span data-stu-id="5520a-139">The Swagger endpoint can then be used with [Swagger UI](https://swagger.io/swagger-ui/) and other tooling.</span></span>

<span data-ttu-id="5520a-140">Чтобы включить поддержку Swagger с помощью API gRPC HTTP, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="5520a-140">To enable Swagger with gRPC HTTP API:</span></span>

1. <span data-ttu-id="5520a-141">Добавьте ссылку на пакет в [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).</span><span class="sxs-lookup"><span data-stu-id="5520a-141">Add a package reference to [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).</span></span>
2. <span data-ttu-id="5520a-142">Настройте Swashbuckle в *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5520a-142">Configure Swashbuckle in *Startup.cs*.</span></span> <span data-ttu-id="5520a-143">Метод `AddGrpcSwagger` позволяет настроить Swashbuckle для включения конечных точек API gRPC HTTP.</span><span class="sxs-lookup"><span data-stu-id="5520a-143">The `AddGrpcSwagger` method configures Swashbuckle to include gRPC HTTP API endpoints.</span></span>

[!code-csharp[](~/grpc/httpapi/Startup.cs?name=snippet_1&highlight=6-10,15-19)]

<span data-ttu-id="5520a-144">Чтобы убедиться, что Swashbuckle создает файлы Swagger для служб gRPC на основе REST, запустите приложение и перейдите на страницу пользовательского интерфейса Swagger:</span><span class="sxs-lookup"><span data-stu-id="5520a-144">To confirm that Swashbuckle is generating Swagger for the RESTful gRPC services, start the app and navigate to the Swagger UI page:</span></span>

![Пользовательский интерфейс Swagger](~/grpc/httpapi/static/swaggerui.png)

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="5520a-146">Сравнение API gRPC HTTP и gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="5520a-146">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="5520a-147">И API gRPC HTTP, и gRPC-Web позволяют вызывать службы gRPC из браузера.</span><span class="sxs-lookup"><span data-stu-id="5520a-147">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="5520a-148">Однако способы, которыми они это делают, отличаются.</span><span class="sxs-lookup"><span data-stu-id="5520a-148">However, the way each does this is different:</span></span>

* <span data-ttu-id="5520a-149">gRPC-Web позволяет браузерным приложениям вызывать службы gRPC из браузера с помощью клиента gRPC-Web и Protobuf.</span><span class="sxs-lookup"><span data-stu-id="5520a-149">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="5520a-150">gRPC-Web требует, чтобы браузерное приложение создавало клиент gRPC, а преимущество заключается в том, что отправляются небольшие и быстрые сообщения Protobuf.</span><span class="sxs-lookup"><span data-stu-id="5520a-150">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="5520a-151">API gRPC HTTP позволяет браузерным приложениям вызывать службы gRPC с помощью JSON, как если бы они были API RESTful.</span><span class="sxs-lookup"><span data-stu-id="5520a-151">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="5520a-152">Браузерному приложению не нужно создавать клиент gRPC и не нужно ничего знать о gRPC.</span><span class="sxs-lookup"><span data-stu-id="5520a-152">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="5520a-153">Для API gRPC HTTP не создается сформированный клиент.</span><span class="sxs-lookup"><span data-stu-id="5520a-153">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="5520a-154">Предыдущую службу `Greeter` можно вызывать с помощью API JavaScript браузера:</span><span class="sxs-lookup"><span data-stu-id="5520a-154">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="5520a-155">Экспериментальное состояние</span><span class="sxs-lookup"><span data-stu-id="5520a-155">Experimental status</span></span>

<span data-ttu-id="5520a-156">API gRPC HTTP является экспериментом.</span><span class="sxs-lookup"><span data-stu-id="5520a-156">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="5520a-157">Это решение не завершено и не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="5520a-157">It is not complete and it is not supported.</span></span> <span data-ttu-id="5520a-158">Мы заинтересованы в этой технологии, и она предоставляет разработчикам приложений возможность быстро и одновременно создавать службы gRPC и JSON.</span><span class="sxs-lookup"><span data-stu-id="5520a-158">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="5520a-159">Мы не принимаем на себя обязательства по завершению этого проекта API gRPC HTTP.</span><span class="sxs-lookup"><span data-stu-id="5520a-159">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="5520a-160">Мы хотим оценить заинтересованность разработчиков в API gRPC HTTP.</span><span class="sxs-lookup"><span data-stu-id="5520a-160">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="5520a-161">Если вас заинтересовала технология API gRPC HTTP, просим вас [отправить отзыв](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="5520a-161">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="5520a-162">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="5520a-162">grpc-gateway</span></span>

<span data-ttu-id="5520a-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) — еще одна технология создания API RESTful JSON из служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="5520a-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="5520a-164">Она использует такие же заметки в файлах *PROTO*, чтобы сопоставлять понятия HTTP со службами gRPC.</span><span class="sxs-lookup"><span data-stu-id="5520a-164">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="5520a-165">Главное различие между grpc-gateway и API gRPC HTTP состоит в том, что grpc-gateway использует формирование кода для создания обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="5520a-165">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="5520a-166">Обратный прокси-сервер преобразует вызовы RESTful в gRPC, а затем отправляет их в службу gRPC.</span><span class="sxs-lookup"><span data-stu-id="5520a-166">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="5520a-167">Сведения об установке и использовании grpc-gateway см. в [файле сведений для grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span><span class="sxs-lookup"><span data-stu-id="5520a-167">For installation and usage of grpc-gateway, see the [grpc-gateway README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5520a-168">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5520a-168">Additional resources</span></span>

* [<span data-ttu-id="5520a-169">Документация по google.api.HttpRule</span><span class="sxs-lookup"><span data-stu-id="5520a-169">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
