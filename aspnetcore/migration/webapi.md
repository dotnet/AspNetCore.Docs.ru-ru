---
title: Переход с веб-API ASP.NET на ASP.NET Core
author: ardalis
description: Узнайте, как перенести реализацию веб-API с веб-API ASP.NET 4. x на ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 3bd34f677271ddfc00e6e65ddf3a5e3c10eec863
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588787"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="75aa5-103">Переход с веб-API ASP.NET на ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75aa5-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="75aa5-104">[Скотта Эдди (](https://twitter.com/scott_addie) и [Виктор Смит](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="75aa5-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="75aa5-105">Веб-API ASP.NET 4. x — это служба HTTP, которая достигает широкого спектра клиентов, включая браузеры и мобильные устройства.</span><span class="sxs-lookup"><span data-stu-id="75aa5-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="75aa5-106">ASP.NET Core объединяет модели приложений MVC и веб-API ASP.NET 4. x в единую модель программирования, известную как ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="75aa5-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="75aa5-107">В этой статье описываются шаги, необходимые для перехода с веб-API ASP.NET 4. x на ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="75aa5-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="75aa5-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/webapi/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="75aa5-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="75aa5-109">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="75aa5-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="75aa5-110">Обзор проекта веб-API ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="75aa5-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="75aa5-111">В этой статье используется проект *продуктсапп* , созданный в [Начало работы с веб-API ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="75aa5-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="75aa5-112">В этом проекте базовый проект веб-API ASP.NET 4. x настраивается следующим образом.</span><span class="sxs-lookup"><span data-stu-id="75aa5-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="75aa5-113">В *Global.asax.CS* выполняется вызов `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="75aa5-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="75aa5-114">`WebApiConfig`Класс находится в папке *App_Start* и имеет статический `Register` метод:</span><span class="sxs-lookup"><span data-stu-id="75aa5-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="75aa5-115">Предыдущий класс:</span><span class="sxs-lookup"><span data-stu-id="75aa5-115">The preceding class:</span></span>

* <span data-ttu-id="75aa5-116">Настраивает [маршрутизацию атрибутов](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), хотя в действительности она не используется.</span><span class="sxs-lookup"><span data-stu-id="75aa5-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="75aa5-117">Настраивает таблицу маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="75aa5-117">Configures the routing table.</span></span>
<span data-ttu-id="75aa5-118">В примере кода требуется, чтобы URL-адреса совпадали с форматом `/api/{controller}/{id}` , а — `{id}` необязательным.</span><span class="sxs-lookup"><span data-stu-id="75aa5-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="75aa5-119">В следующих разделах демонстрируется перенос проекта веб-API в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="75aa5-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="75aa5-120">Создание целевого проекта</span><span class="sxs-lookup"><span data-stu-id="75aa5-120">Create the destination project</span></span>

<span data-ttu-id="75aa5-121">Создайте новое пустое решение в Visual Studio и добавьте проект веб-API ASP.NET 4. x для миграции:</span><span class="sxs-lookup"><span data-stu-id="75aa5-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="75aa5-122">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="75aa5-122">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="75aa5-123">Выберите шаблон **пустое решение** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="75aa5-123">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="75aa5-124">Назовите решение *вебапимигратион*.</span><span class="sxs-lookup"><span data-stu-id="75aa5-124">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="75aa5-125">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="75aa5-125">Select **Create**.</span></span>
1. <span data-ttu-id="75aa5-126">Добавьте существующий проект *продуктсапп* в решение.</span><span class="sxs-lookup"><span data-stu-id="75aa5-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="75aa5-127">Добавьте новый проект API для переноса в:</span><span class="sxs-lookup"><span data-stu-id="75aa5-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="75aa5-128">Добавьте в решение новый проект **веб-приложения ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="75aa5-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="75aa5-129">В диалоговом окне **Настройка нового проекта** назовите проект *Продуктскоре* и выберите **создать**.</span><span class="sxs-lookup"><span data-stu-id="75aa5-129">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="75aa5-130">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="75aa5-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="75aa5-131">Выберите шаблон проекта **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="75aa5-131">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="75aa5-132">Удалите файлы примеров *WeatherForecast.CS* и *Controllers/веасерфорекастконтроллер. CS* из нового проекта *продуктскоре* .</span><span class="sxs-lookup"><span data-stu-id="75aa5-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="75aa5-133">Решение теперь содержит два проекта.</span><span class="sxs-lookup"><span data-stu-id="75aa5-133">The solution now contains two projects.</span></span> <span data-ttu-id="75aa5-134">В следующих разделах описывается перенос содержимого проекта *продуктсапп* в проект *продуктскоре* .</span><span class="sxs-lookup"><span data-stu-id="75aa5-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="75aa5-135">Миграция конфигурации</span><span class="sxs-lookup"><span data-stu-id="75aa5-135">Migrate configuration</span></span>

<span data-ttu-id="75aa5-136">ASP.NET Core не использует папку *App_Start* или файл *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="75aa5-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="75aa5-137">Кроме того, файл *web.config* добавляется во время публикации.</span><span class="sxs-lookup"><span data-stu-id="75aa5-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="75aa5-138">Класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="75aa5-138">The `Startup` class:</span></span>

* <span data-ttu-id="75aa5-139">Заменяет *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="75aa5-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="75aa5-140">Обрабатывает все задачи запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="75aa5-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="75aa5-141">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="75aa5-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="75aa5-142">Перенос моделей и контроллеров</span><span class="sxs-lookup"><span data-stu-id="75aa5-142">Migrate models and controllers</span></span>

<span data-ttu-id="75aa5-143">В следующем коде показано, что `ProductsController` нужно обновить для ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="75aa5-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="75aa5-144">Обновите `ProductsController` для ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="75aa5-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="75aa5-145">Скопируйте *Controllers/продуктсконтроллер. CS* и папку *Models* из исходного проекта в новый.</span><span class="sxs-lookup"><span data-stu-id="75aa5-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="75aa5-146">Измените корневое пространство имен скопированных файлов на `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="75aa5-147">Обновите `using ProductsApp.Models;` инструкцию до `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="75aa5-148">Следующие компоненты не существуют в ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="75aa5-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="75aa5-149">Класс `ApiController`</span><span class="sxs-lookup"><span data-stu-id="75aa5-149">`ApiController` class</span></span>
* <span data-ttu-id="75aa5-150">Пространство имен `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="75aa5-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="75aa5-151">Интерфейс `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="75aa5-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="75aa5-152">Внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="75aa5-152">Make the following changes:</span></span>

1. <span data-ttu-id="75aa5-153">Измените `ApiController` на <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="75aa5-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="75aa5-154">Добавьте, `using Microsoft.AspNetCore.Mvc;` чтобы разрешить `ControllerBase` ссылку.</span><span class="sxs-lookup"><span data-stu-id="75aa5-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="75aa5-155">Удалите `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="75aa5-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="75aa5-156">Измените `GetProduct` тип возвращаемого значения действия с `IHttpActionResult` на `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="75aa5-157">Упростите `GetProduct` `return` инструкцию действия следующим образом:</span><span class="sxs-lookup"><span data-stu-id="75aa5-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="75aa5-158">Настройка маршрутизации</span><span class="sxs-lookup"><span data-stu-id="75aa5-158">Configure routing</span></span>

<span data-ttu-id="75aa5-159">Шаблон проекта ASP.NET Core *API* содержит конфигурацию маршрутизации конечных точек в созданном коде.</span><span class="sxs-lookup"><span data-stu-id="75aa5-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="75aa5-160">Следующие <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> вызовы и <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> вызывают:</span><span class="sxs-lookup"><span data-stu-id="75aa5-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="75aa5-161">Регистрация сопоставления маршрута и выполнения конечной точки в конвейере по [промежуточного слоя](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="75aa5-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="75aa5-162">Замените файл *App_Start/вебапиконфиг.КС* проекта *продуктсапп* .</span><span class="sxs-lookup"><span data-stu-id="75aa5-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="75aa5-163">Настройте маршрутизацию следующим образом.</span><span class="sxs-lookup"><span data-stu-id="75aa5-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="75aa5-164">Пометьте `ProductsController` класс следующими атрибутами:</span><span class="sxs-lookup"><span data-stu-id="75aa5-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="75aa5-165">Предыдущий [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) атрибут настраивает шаблон маршрутизации атрибутов контроллера.</span><span class="sxs-lookup"><span data-stu-id="75aa5-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="75aa5-166">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Атрибут делает маршрутизацию атрибутов требованием для всех действий в этом контроллере.</span><span class="sxs-lookup"><span data-stu-id="75aa5-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="75aa5-167">Маршрутизация атрибутов поддерживает маркеры, такие как `[controller]` и `[action]` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="75aa5-168">Во время выполнения каждый токен заменяется именем контроллера или действия соответственно, к которому был применен атрибут.</span><span class="sxs-lookup"><span data-stu-id="75aa5-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="75aa5-169">Токены:</span><span class="sxs-lookup"><span data-stu-id="75aa5-169">The tokens:</span></span>
    * <span data-ttu-id="75aa5-170">Сократите число волшебных строк в проекте.</span><span class="sxs-lookup"><span data-stu-id="75aa5-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="75aa5-171">Убедитесь, что маршруты синхронизированы с соответствующими контроллерами и действиями при применении рефакторинга автоматического переименования.</span><span class="sxs-lookup"><span data-stu-id="75aa5-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="75aa5-172">Включить HTTP-запросы GET к `ProductController` действиям:</span><span class="sxs-lookup"><span data-stu-id="75aa5-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="75aa5-173">Примените [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) атрибут к `GetAllProducts` действию.</span><span class="sxs-lookup"><span data-stu-id="75aa5-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="75aa5-174">Примените `[HttpGet("{id}")]` атрибут к `GetProduct` действию.</span><span class="sxs-lookup"><span data-stu-id="75aa5-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="75aa5-175">Запустите перенесенный проект и перейдите к `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="75aa5-176">Появится полный список из трех продуктов.</span><span class="sxs-lookup"><span data-stu-id="75aa5-176">A full list of three products appears.</span></span> <span data-ttu-id="75aa5-177">Перейдите по адресу `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="75aa5-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="75aa5-178">Появится первый продукт.</span><span class="sxs-lookup"><span data-stu-id="75aa5-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75aa5-179">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="75aa5-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="75aa5-180">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="75aa5-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="75aa5-181">Обзор проекта веб-API ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="75aa5-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="75aa5-182">В этой статье используется проект *продуктсапп* , созданный в [Начало работы с веб-API ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="75aa5-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="75aa5-183">В этом проекте базовый проект веб-API ASP.NET 4. x настраивается следующим образом.</span><span class="sxs-lookup"><span data-stu-id="75aa5-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="75aa5-184">В *Global.asax.CS* выполняется вызов `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="75aa5-184">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="75aa5-185">`WebApiConfig`Класс находится в папке *App_Start* и имеет статический `Register` метод:</span><span class="sxs-lookup"><span data-stu-id="75aa5-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="75aa5-186">Этот класс настраивает [маршрутизацию атрибутов](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), хотя на самом деле она не используется в проекте.</span><span class="sxs-lookup"><span data-stu-id="75aa5-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="75aa5-187">Он также настраивает таблицу маршрутизации, используемую веб-API ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="75aa5-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="75aa5-188">В этом случае ASP.NET 4. x Web API ждет, что URL-адреса будут соответствовать формату `/api/{controller}/{id}` , с `{id}` необязательным.</span><span class="sxs-lookup"><span data-stu-id="75aa5-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="75aa5-189">В следующих разделах демонстрируется перенос проекта веб-API в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="75aa5-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="75aa5-190">Создание целевого проекта</span><span class="sxs-lookup"><span data-stu-id="75aa5-190">Create the destination project</span></span>

<span data-ttu-id="75aa5-191">В Visual Studio выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="75aa5-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="75aa5-192">Последовательно выберите **файл**  >  **создать**  >  **проект**  >  **другие типы проектов**  >  **решения Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="75aa5-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="75aa5-193">Выберите **пустое решение** и назовите решение *вебапимигратион*.</span><span class="sxs-lookup"><span data-stu-id="75aa5-193">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="75aa5-194">Нажмите кнопку **ОК** .</span><span class="sxs-lookup"><span data-stu-id="75aa5-194">Click the **OK** button.</span></span>
* <span data-ttu-id="75aa5-195">Добавьте существующий проект *продуктсапп* в решение.</span><span class="sxs-lookup"><span data-stu-id="75aa5-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="75aa5-196">Добавьте в решение новый проект **веб-приложения ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="75aa5-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="75aa5-197">Выберите целевую платформу **.NET Core** в раскрывающемся списке и выберите шаблон проекта **API** .</span><span class="sxs-lookup"><span data-stu-id="75aa5-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="75aa5-198">Назовите проект *продуктскоре* и нажмите кнопку **ОК** .</span><span class="sxs-lookup"><span data-stu-id="75aa5-198">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="75aa5-199">Решение теперь содержит два проекта.</span><span class="sxs-lookup"><span data-stu-id="75aa5-199">The solution now contains two projects.</span></span> <span data-ttu-id="75aa5-200">В следующих разделах описывается перенос содержимого проекта *продуктсапп* в проект *продуктскоре* .</span><span class="sxs-lookup"><span data-stu-id="75aa5-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="75aa5-201">Миграция конфигурации</span><span class="sxs-lookup"><span data-stu-id="75aa5-201">Migrate configuration</span></span>

<span data-ttu-id="75aa5-202">ASP.NET Core не использует:</span><span class="sxs-lookup"><span data-stu-id="75aa5-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="75aa5-203">*App_Startная* папка или файл *Global. asax*</span><span class="sxs-lookup"><span data-stu-id="75aa5-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="75aa5-204">*web.config* файл добавляется во время публикации.</span><span class="sxs-lookup"><span data-stu-id="75aa5-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="75aa5-205">Класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="75aa5-205">The `Startup` class:</span></span>

* <span data-ttu-id="75aa5-206">Заменяет *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="75aa5-206">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="75aa5-207">Обрабатывает все задачи запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="75aa5-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="75aa5-208">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="75aa5-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="75aa5-209">В ASP.NET Core MVC маршрутизация атрибутов включается по умолчанию при <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> вызове в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="75aa5-210">Следующий `UseMvc` вызов заменяет файл *App_Start/Вебапиконфиг.КС* проекта *продуктсапп* :</span><span class="sxs-lookup"><span data-stu-id="75aa5-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="75aa5-211">Перенос моделей и контроллеров</span><span class="sxs-lookup"><span data-stu-id="75aa5-211">Migrate models and controllers</span></span>

<span data-ttu-id="75aa5-212">В следующем коде показано `ProductsController` Обновление для ASP.NET Core. [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="75aa5-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="75aa5-213">Обновите `ProductsController` для ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="75aa5-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="75aa5-214">Скопируйте *Controllers/продуктсконтроллер. CS* из исходного проекта в новый.</span><span class="sxs-lookup"><span data-stu-id="75aa5-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="75aa5-215">Скопируйте папку *Models* из исходного проекта в новую.</span><span class="sxs-lookup"><span data-stu-id="75aa5-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="75aa5-216">Измените корневое пространство имен скопированных файлов на `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="75aa5-217">Обновите `using ProductsApp.Models;` инструкцию до `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="75aa5-218">Следующие компоненты не существуют в ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="75aa5-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="75aa5-219">Класс `ApiController`</span><span class="sxs-lookup"><span data-stu-id="75aa5-219">`ApiController` class</span></span>
* <span data-ttu-id="75aa5-220">Пространство имен `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="75aa5-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="75aa5-221">Интерфейс `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="75aa5-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="75aa5-222">Внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="75aa5-222">Make the following changes:</span></span>

1. <span data-ttu-id="75aa5-223">Измените `ApiController` на <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="75aa5-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="75aa5-224">Добавьте, `using Microsoft.AspNetCore.Mvc;` чтобы разрешить `ControllerBase` ссылку.</span><span class="sxs-lookup"><span data-stu-id="75aa5-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="75aa5-225">Удалите `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="75aa5-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="75aa5-226">Измените `GetProduct` тип возвращаемого значения действия с `IHttpActionResult` на `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="75aa5-227">Упростите `GetProduct` `return` инструкцию действия следующим образом:</span><span class="sxs-lookup"><span data-stu-id="75aa5-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="75aa5-228">Настройка маршрутизации</span><span class="sxs-lookup"><span data-stu-id="75aa5-228">Configure routing</span></span>

<span data-ttu-id="75aa5-229">Настройте маршрутизацию следующим образом.</span><span class="sxs-lookup"><span data-stu-id="75aa5-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="75aa5-230">Пометьте `ProductsController` класс следующими атрибутами:</span><span class="sxs-lookup"><span data-stu-id="75aa5-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="75aa5-231">Предыдущий [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) атрибут настраивает шаблон маршрутизации атрибутов контроллера.</span><span class="sxs-lookup"><span data-stu-id="75aa5-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="75aa5-232">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Атрибут делает маршрутизацию атрибутов требованием для всех действий в этом контроллере.</span><span class="sxs-lookup"><span data-stu-id="75aa5-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="75aa5-233">Маршрутизация атрибутов поддерживает маркеры, такие как `[controller]` и `[action]` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="75aa5-234">Во время выполнения каждый токен заменяется именем контроллера или действия соответственно, к которому был применен атрибут.</span><span class="sxs-lookup"><span data-stu-id="75aa5-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="75aa5-235">Токены уменьшают количество волшебных строк в проекте.</span><span class="sxs-lookup"><span data-stu-id="75aa5-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="75aa5-236">Кроме того, токены обеспечивают синхронизацию маршрутов с соответствующими контроллерами и действиями при применении рефакторинга автоматического переименования.</span><span class="sxs-lookup"><span data-stu-id="75aa5-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="75aa5-237">Задайте для режима совместимости проекта значение ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="75aa5-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="75aa5-238">Предыдущее изменение:</span><span class="sxs-lookup"><span data-stu-id="75aa5-238">The preceding change:</span></span>

    * <span data-ttu-id="75aa5-239">Требуется для использования `[ApiController]` атрибута на уровне контроллера.</span><span class="sxs-lookup"><span data-stu-id="75aa5-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="75aa5-240">Может привести к возможным нарушениям поведений, появившимся в ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="75aa5-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="75aa5-241">Включить HTTP-запросы GET к `ProductController` действиям:</span><span class="sxs-lookup"><span data-stu-id="75aa5-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="75aa5-242">Примените [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) атрибут к `GetAllProducts` действию.</span><span class="sxs-lookup"><span data-stu-id="75aa5-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="75aa5-243">Примените `[HttpGet("{id}")]` атрибут к `GetProduct` действию.</span><span class="sxs-lookup"><span data-stu-id="75aa5-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="75aa5-244">Запустите перенесенный проект и перейдите к `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="75aa5-245">Появится полный список из трех продуктов.</span><span class="sxs-lookup"><span data-stu-id="75aa5-245">A full list of three products appears.</span></span> <span data-ttu-id="75aa5-246">Перейдите по адресу `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="75aa5-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="75aa5-247">Появится первый продукт.</span><span class="sxs-lookup"><span data-stu-id="75aa5-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="75aa5-248">Оболочка совместимости</span><span class="sxs-lookup"><span data-stu-id="75aa5-248">Compatibility shim</span></span>

<span data-ttu-id="75aa5-249">Библиотека [Microsoft. AspNetCore. MVC. вебапикомпатшим](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) предоставляет оболочку совместимости для перемещения проектов веб-API ASP.NET 4. x в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75aa5-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="75aa5-250">Оболочка совместимости расширяет ASP.NET Core для поддержки ряда соглашений от ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="75aa5-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="75aa5-251">Пример, переданный ранее в этом документе, достаточно прост в том, что оболочка совместимости не была нужна.</span><span class="sxs-lookup"><span data-stu-id="75aa5-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="75aa5-252">Для более крупных проектов использование оболочки совместимости может оказаться полезным для временного разделения зазора API между ASP.NET Core и ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="75aa5-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="75aa5-253">Оболочка совместимости веб-API предназначена для использования в качестве временной меры для поддержки переноса больших проектов веб-API ASP.NET 4. x в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75aa5-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="75aa5-254">Со временем проекты должны быть обновлены для использования шаблонов ASP.NET Core, а не полагаться на оболочку совместимости.</span><span class="sxs-lookup"><span data-stu-id="75aa5-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="75aa5-255">Функции обеспечения совместимости, включенные в `Microsoft.AspNetCore.Mvc.WebApiCompatShim` :</span><span class="sxs-lookup"><span data-stu-id="75aa5-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="75aa5-256">Добавляет `ApiController` тип, поэтому не требуется обновлять базовые типы контроллеров.</span><span class="sxs-lookup"><span data-stu-id="75aa5-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="75aa5-257">Включает привязку модели в стиле веб-API.</span><span class="sxs-lookup"><span data-stu-id="75aa5-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="75aa5-258">ASP.NET Core функция привязки модели MVC аналогична ASP.NET 4. x MVC 5 по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="75aa5-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="75aa5-259">Оболочка совместимости изменяет привязку модели так, чтобы она была похожа на соглашения о привязке модели Web API 2 ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="75aa5-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="75aa5-260">Например, сложные типы автоматически привязываются из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="75aa5-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="75aa5-261">Расширяет привязку модели, чтобы действия контроллера могли принимать параметры типа `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="75aa5-262">Добавляет модули форматирования сообщений, позволяя действиям возвращать результаты типа `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="75aa5-263">Добавляет дополнительные методы ответа, которые могут использоваться действиями веб-API 2 для обслуживания ответов:</span><span class="sxs-lookup"><span data-stu-id="75aa5-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="75aa5-264">`HttpResponseMessage` Каждый</span><span class="sxs-lookup"><span data-stu-id="75aa5-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="75aa5-265">Методы результата действия:</span><span class="sxs-lookup"><span data-stu-id="75aa5-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="75aa5-266">Добавляет экземпляр `IContentNegotiator` в контейнер внедрения зависимостей (DI) приложения и делает доступными типы, связанные с согласованием содержимого, из [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="75aa5-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="75aa5-267">Примерами таких типов являются `DefaultContentNegotiator` и `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="75aa5-268">Чтобы использовать оболочку совместимости, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="75aa5-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="75aa5-269">Установите пакет NuGet [Microsoft. AspNetCore. MVC. вебапикомпатшим](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="75aa5-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="75aa5-270">Зарегистрируйте службы оболочки совместимости с контейнером внедрения приложения, вызвав `services.AddMvc().AddWebApiConventions()` в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="75aa5-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="75aa5-271">Определите зависящие от веб-API маршруты с помощью в в `MapWebApiRoute` `IRouteBuilder` `IApplicationBuilder.UseMvc` вызове приложения.</span><span class="sxs-lookup"><span data-stu-id="75aa5-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75aa5-272">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="75aa5-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
