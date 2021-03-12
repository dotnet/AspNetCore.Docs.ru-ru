---
title: Активация ПО промежуточного слоя с помощью контейнера сторонних разработчиков в ASP.NET Core
author: rick-anderson
description: В этой статье приводятся сведения о том, как использовать строго типизированное ПО промежуточного слоя с активацией на основе фабрики и контейнером сторонних разработчиков в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: bbbfe0e9fba33eba9d69101e5615e931ea917bc1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587376"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="0006f-103">Активация ПО промежуточного слоя с помощью контейнера сторонних разработчиков в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0006f-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0006f-104">В этой статье демонстрируется использование <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> и <xref:Microsoft.AspNetCore.Http.IMiddleware> в качестве точки расширяемости для активации [ПО промежуточного слоя](xref:fundamentals/middleware/index) с помощью контейнера сторонних разработчиков.</span><span class="sxs-lookup"><span data-stu-id="0006f-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="0006f-105">Вводные сведения о `IMiddlewareFactory` и `IMiddleware` см. в статье <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="0006f-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="0006f-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0006f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0006f-107">В примере приложения показана активация ПО промежуточного слоя путем реализации `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="0006f-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="0006f-108">В этом образце используется контейнер внедрения зависимостей [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="0006f-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="0006f-109">Реализация ПО промежуточного слоя в примере записывает значение, предоставленное в параметре строки запроса (`key`).</span><span class="sxs-lookup"><span data-stu-id="0006f-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="0006f-110">ПО промежуточного слоя использует внедренный контекст базы данных (служба с заданной областью) для записи значения строки запроса в базу данных, выполняющуюся в памяти.</span><span class="sxs-lookup"><span data-stu-id="0006f-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="0006f-111">В примере приложения [Simple Injector](https://github.com/simpleinjector/SimpleInjector) используется исключительно для демонстрации.</span><span class="sxs-lookup"><span data-stu-id="0006f-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="0006f-112">Использование Simple Injector не означает поддержку данного инструмента.</span><span class="sxs-lookup"><span data-stu-id="0006f-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="0006f-113">Подходы к активации ПО промежуточного слоя, описанные в документации по Simple Injector и статьях на GitHub, рекомендованы командой Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="0006f-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="0006f-114">Дополнительные сведения см. в [документации по Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) и [в репозитории Simple Injector в GitHub](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="0006f-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="0006f-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="0006f-115">IMiddlewareFactory</span></span>

<span data-ttu-id="0006f-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> предоставляет методы для создания ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="0006f-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="0006f-117">В примере приложения реализуется фабрика ПО промежуточного слоя для создания экземпляра `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="0006f-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="0006f-118">Фабрика ПО промежуточного слоя использует контейнер Simple Injector для разрешения по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="0006f-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="0006f-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="0006f-119">IMiddleware</span></span>

<span data-ttu-id="0006f-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> определяет ПО промежуточного слоя для конвейера запросов приложения.</span><span class="sxs-lookup"><span data-stu-id="0006f-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="0006f-121">ПО промежуточного слоя, активированное реализацией `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="0006f-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="0006f-122">Для ПО промежуточного слоя создается расширение (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="0006f-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="0006f-123">`Startup.ConfigureServices` должен выполнять несколько задач:</span><span class="sxs-lookup"><span data-stu-id="0006f-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="0006f-124">Настройка контейнера Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="0006f-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="0006f-125">Регистрация фабрики и ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="0006f-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="0006f-126">Предоставление контекста базы данных приложения из контейнера Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="0006f-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="0006f-127">ПО промежуточного слоя регистрируется в конвейере обработки запросов в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0006f-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0006f-128">В этой статье демонстрируется использование <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> и <xref:Microsoft.AspNetCore.Http.IMiddleware> в качестве точки расширяемости для активации [ПО промежуточного слоя](xref:fundamentals/middleware/index) с помощью контейнера сторонних разработчиков.</span><span class="sxs-lookup"><span data-stu-id="0006f-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="0006f-129">Вводные сведения о `IMiddlewareFactory` и `IMiddleware` см. в статье <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="0006f-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="0006f-130">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0006f-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0006f-131">В примере приложения показана активация ПО промежуточного слоя путем реализации `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="0006f-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="0006f-132">В этом образце используется контейнер внедрения зависимостей [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="0006f-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="0006f-133">Реализация ПО промежуточного слоя в примере записывает значение, предоставленное в параметре строки запроса (`key`).</span><span class="sxs-lookup"><span data-stu-id="0006f-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="0006f-134">ПО промежуточного слоя использует внедренный контекст базы данных (служба с заданной областью) для записи значения строки запроса в базу данных, выполняющуюся в памяти.</span><span class="sxs-lookup"><span data-stu-id="0006f-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="0006f-135">В примере приложения [Simple Injector](https://github.com/simpleinjector/SimpleInjector) используется исключительно для демонстрации.</span><span class="sxs-lookup"><span data-stu-id="0006f-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="0006f-136">Использование Simple Injector не означает поддержку данного инструмента.</span><span class="sxs-lookup"><span data-stu-id="0006f-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="0006f-137">Подходы к активации ПО промежуточного слоя, описанные в документации по Simple Injector и статьях на GitHub, рекомендованы командой Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="0006f-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="0006f-138">Дополнительные сведения см. в [документации по Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) и [в репозитории Simple Injector в GitHub](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="0006f-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="0006f-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="0006f-139">IMiddlewareFactory</span></span>

<span data-ttu-id="0006f-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> предоставляет методы для создания ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="0006f-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="0006f-141">В примере приложения реализуется фабрика ПО промежуточного слоя для создания экземпляра `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="0006f-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="0006f-142">Фабрика ПО промежуточного слоя использует контейнер Simple Injector для разрешения по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="0006f-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="0006f-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="0006f-143">IMiddleware</span></span>

<span data-ttu-id="0006f-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> определяет ПО промежуточного слоя для конвейера запросов приложения.</span><span class="sxs-lookup"><span data-stu-id="0006f-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="0006f-145">ПО промежуточного слоя, активированное реализацией `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="0006f-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="0006f-146">Для ПО промежуточного слоя создается расширение (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="0006f-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="0006f-147">`Startup.ConfigureServices` должен выполнять несколько задач:</span><span class="sxs-lookup"><span data-stu-id="0006f-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="0006f-148">Настройка контейнера Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="0006f-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="0006f-149">Регистрация фабрики и ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="0006f-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="0006f-150">Предоставление контекста базы данных приложения из контейнера Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="0006f-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="0006f-151">ПО промежуточного слоя регистрируется в конвейере обработки запросов в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0006f-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0006f-152">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="0006f-152">Additional resources</span></span>

* [<span data-ttu-id="0006f-153">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="0006f-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="0006f-154">Активация фабричного ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="0006f-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="0006f-155">Репозиторий Simple Injector в GitHub</span><span class="sxs-lookup"><span data-stu-id="0006f-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="0006f-156">Документация по Simple Injector</span><span class="sxs-lookup"><span data-stu-id="0006f-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
