---
title: Общие сведения ASP.NET Core MVC
author: ardalis
description: Узнайте, почему ASP.NET MVC является многофункциональной платформой для создания веб-приложений и API-интерфейсов с помощью структуры проектирования Model-View-Controller.
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
uid: mvc/overview
ms.openlocfilehash: 642a7b26a3ba0a79a35437a5c19ee0be63a367af
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057321"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="949ae-103">Общие сведения ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="949ae-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="949ae-104">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="949ae-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="949ae-105">ASP.NET MVC является многофункциональной платформой для создания веб-приложений и API-интерфейсов с помощью структуры проектирования Model-View-Controller.</span><span class="sxs-lookup"><span data-stu-id="949ae-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="949ae-106">Что собой представляет структура MVC?</span><span class="sxs-lookup"><span data-stu-id="949ae-106">What is the MVC pattern?</span></span>

<span data-ttu-id="949ae-107">Структура архитектуры MVC разделяет приложение на три основных группы компонентов: модели, представлении и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="949ae-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="949ae-108">Это позволяет реализовать принципы [разделения задач](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="949ae-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="949ae-109">Согласно этой структуре запросы пользователей направляются в контроллер, который отвечает за работу с моделью для выполнения действий пользователей и (или) получение результатов запросов.</span><span class="sxs-lookup"><span data-stu-id="949ae-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="949ae-110">Контроллер выбирает представление для отображения пользователю со всеми необходимыми данными модели.</span><span class="sxs-lookup"><span data-stu-id="949ae-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="949ae-111">На следующей схеме показаны три основных компонента и существующие между ними связи.</span><span class="sxs-lookup"><span data-stu-id="949ae-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![Структура MVC](overview/_static/mvc.png)

<span data-ttu-id="949ae-113">Такое распределение обязанностей позволяет масштабировать приложение в контексте сложности, так как проще писать код, выполнять отладку и тестирование компонента (модели, представления или контроллера) с одним заданием.</span><span class="sxs-lookup"><span data-stu-id="949ae-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="949ae-114">Гораздо труднее обновлять, тестировать и отлаживать код, зависимости которого находятся в двух или трех этих областях.</span><span class="sxs-lookup"><span data-stu-id="949ae-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="949ae-115">Например, логика пользовательского интерфейса, как правило, подвергается изменениям чаще, чем бизнес-логика.</span><span class="sxs-lookup"><span data-stu-id="949ae-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="949ae-116">Если код представления и бизнес-логика объединены в один объект, содержащий бизнес-логику, объект необходимо изменять при каждом обновлении пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="949ae-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="949ae-117">Это часто приводит к возникновению ошибок и необходимости повторно тестировать бизнес-логику после каждого незначительного изменения пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="949ae-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="949ae-118">Представление и контроллер зависят от модели.</span><span class="sxs-lookup"><span data-stu-id="949ae-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="949ae-119">Однако сама модель не зависит ни от контроллера, ни от представления.</span><span class="sxs-lookup"><span data-stu-id="949ae-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="949ae-120">Это является одним из ключевых преимуществ разделения.</span><span class="sxs-lookup"><span data-stu-id="949ae-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="949ae-121">Такое разделение позволяет создавать и тестировать модели независимо от их визуального представления.</span><span class="sxs-lookup"><span data-stu-id="949ae-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="949ae-122">Функции модели</span><span class="sxs-lookup"><span data-stu-id="949ae-122">Model Responsibilities</span></span>

<span data-ttu-id="949ae-123">Модель в приложении MVC представляет состояние приложения и бизнес-логику или операций, которые должны в нем выполняться.</span><span class="sxs-lookup"><span data-stu-id="949ae-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="949ae-124">Бизнес-логика должна быть включена в состав модели вместе с логикой реализации для сохранения состояния приложения.</span><span class="sxs-lookup"><span data-stu-id="949ae-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="949ae-125">Как правило, строго типизированные представления используют типы ViewModel, предназначенные для хранения данных, отображаемых в этом представлении.</span><span class="sxs-lookup"><span data-stu-id="949ae-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="949ae-126">Контроллер создает и заполняет эти экземпляры ViewModel из модели.</span><span class="sxs-lookup"><span data-stu-id="949ae-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="949ae-127">Функции представления</span><span class="sxs-lookup"><span data-stu-id="949ae-127">View Responsibilities</span></span>

<span data-ttu-id="949ae-128">Представления отвечают за представление содержимого через пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="949ae-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="949ae-129">Они используют [ Razor обработчик представлений](#razor-view-engine) для внедрения кода .NET в разметку HTML.</span><span class="sxs-lookup"><span data-stu-id="949ae-129">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="949ae-130">Представления должны иметь минимальную логику, которая должна быть связана с представлением содержимого.</span><span class="sxs-lookup"><span data-stu-id="949ae-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="949ae-131">Если есть необходимость выполнять большую часть логики в представлении для отображения данных из сложной модели, рекомендуется воспользоваться [компонентом представления](views/view-components.md), ViewModel или шаблоном представления, позволяющими упростить представление.</span><span class="sxs-lookup"><span data-stu-id="949ae-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="949ae-132">Функции контроллера</span><span class="sxs-lookup"><span data-stu-id="949ae-132">Controller Responsibilities</span></span>

<span data-ttu-id="949ae-133">Контроллеры — это компоненты для управления взаимодействием с пользователем, работы с моделью и выбора представления для отображения.</span><span class="sxs-lookup"><span data-stu-id="949ae-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="949ae-134">В приложении MVC представление служит только для отображения информации. Обработку введенных данных, формирование ответа и взаимодействие с пользователем обеспечивает контроллер.</span><span class="sxs-lookup"><span data-stu-id="949ae-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="949ae-135">В структуре MVC контроллер является начальной отправной точкой и отвечает за выбор рабочих типов моделей и отображаемых представлений (именно этим объясняется его название — он контролирует, каким образом приложение отвечает на конкретный запрос).</span><span class="sxs-lookup"><span data-stu-id="949ae-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="949ae-136">Контроллеры не должны быть чересчур сложными из-за слишком большого количества обязанностей.</span><span class="sxs-lookup"><span data-stu-id="949ae-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="949ae-137">Чтобы не перегружать логику контроллера, перенесите бизнес-логику из контроллера в модель предметной области.</span><span class="sxs-lookup"><span data-stu-id="949ae-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="949ae-138">Если ваш контроллер часто выполняет одни и те же виды действий, переместите эти действия в [фильтры](#filters).</span><span class="sxs-lookup"><span data-stu-id="949ae-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="949ae-139">Что такое ASP.NET Core MVC?</span><span class="sxs-lookup"><span data-stu-id="949ae-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="949ae-140">ASP.NET Core MVC представляет собой упрощенную, эффективно тестируемую платформу с открытым исходным кодом, оптимизированную для использования с ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="949ae-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="949ae-141">ASP.NET Core MVC предоставляет основанный на шаблонах способ создания динамических веб-сайтов с четким разделением задач.</span><span class="sxs-lookup"><span data-stu-id="949ae-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="949ae-142">Она обеспечивает полный контроль разметки, поддерживает согласованную с TDD разработку и использует новейшие веб-стандарты.</span><span class="sxs-lookup"><span data-stu-id="949ae-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="949ae-143">Компоненты</span><span class="sxs-lookup"><span data-stu-id="949ae-143">Features</span></span>

<span data-ttu-id="949ae-144">К возможностям ASP.NET MVC относятся:</span><span class="sxs-lookup"><span data-stu-id="949ae-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="949ae-145">Маршрутизация</span><span class="sxs-lookup"><span data-stu-id="949ae-145">Routing</span></span>](#routing)
* [<span data-ttu-id="949ae-146">Привязка модели</span><span class="sxs-lookup"><span data-stu-id="949ae-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="949ae-147">Проверка модели</span><span class="sxs-lookup"><span data-stu-id="949ae-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="949ae-148">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="949ae-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="949ae-149">Фильтры</span><span class="sxs-lookup"><span data-stu-id="949ae-149">Filters</span></span>](#filters)
* [<span data-ttu-id="949ae-150">Области</span><span class="sxs-lookup"><span data-stu-id="949ae-150">Areas</span></span>](#areas)
* [<span data-ttu-id="949ae-151">Интерфейсы веб-API</span><span class="sxs-lookup"><span data-stu-id="949ae-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="949ae-152">Тестирование</span><span class="sxs-lookup"><span data-stu-id="949ae-152">Testability</span></span>](#testability)
* [<span data-ttu-id="949ae-153">Razor Просмотреть подсистему</span><span class="sxs-lookup"><span data-stu-id="949ae-153">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="949ae-154">Строго типизированные представления</span><span class="sxs-lookup"><span data-stu-id="949ae-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="949ae-155">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="949ae-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="949ae-156">Просмотр компонентов</span><span class="sxs-lookup"><span data-stu-id="949ae-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="949ae-157">Маршрутизация</span><span class="sxs-lookup"><span data-stu-id="949ae-157">Routing</span></span>

<span data-ttu-id="949ae-158">Платформа ASP.NET Core MVC создана на основе [маршрутизации ASP.NET Core](../fundamentals/routing.md) — мощного компонента сопоставления URL-адресов, который позволяет создавать приложения с понятными и поддерживающими поиск URL-адресами.</span><span class="sxs-lookup"><span data-stu-id="949ae-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="949ae-159">Вы можете определять шаблоны именования URL-адресов приложения, эффективно работающие для оптимизации для поисковых систем (SEO) и для создания ссылок, независимо от способа организации файлов на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="949ae-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="949ae-160">Вы можете определять маршруты с помощью понятного синтаксиса шаблонов маршрутов, который поддерживает ограничения значений маршрутов, значения по умолчанию и необязательные значения.</span><span class="sxs-lookup"><span data-stu-id="949ae-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="949ae-161">*Маршрутизация на основе соглашений* позволяет глобально определять форматы URL-адресов, которые принимает приложение, и то, как каждый из этих форматов соответствует конкретному методу действия на данном контроллере.</span><span class="sxs-lookup"><span data-stu-id="949ae-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on a given controller.</span></span> <span data-ttu-id="949ae-162">При поступлении входящего запроса модуль маршрутизации выполняет синтаксический анализ URL-адреса и соотносит его с одним из определенных форматов URL-адресов, а затем вызывает метод действия связанного контроллера.</span><span class="sxs-lookup"><span data-stu-id="949ae-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="949ae-163">*Маршрутизация атрибутов* используется для указания сведений о маршрутизации путем добавления атрибутов, определяющих маршруты приложения, к контроллерам и действиям.</span><span class="sxs-lookup"><span data-stu-id="949ae-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="949ae-164">Это означает, что определения маршрутов помещаются рядом с контроллером и действием, с которым они связаны.</span><span class="sxs-lookup"><span data-stu-id="949ae-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a><span data-ttu-id="949ae-165">Привязка модели</span><span class="sxs-lookup"><span data-stu-id="949ae-165">Model binding</span></span>

<span data-ttu-id="949ae-166">[Привязка модели](models/model-binding.md) в ASP.NET Core MVC преобразует данные запроса клиента (значения форм, данные маршрута, параметры строки запроса, заголовки HTTP) в объекты, которые может обрабатывать контроллер.</span><span class="sxs-lookup"><span data-stu-id="949ae-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="949ae-167">В результате логике контроллера не требуется определять данные входящего запроса — данные просто доступны в виде параметров для методов действий.</span><span class="sxs-lookup"><span data-stu-id="949ae-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a><span data-ttu-id="949ae-168">Проверка модели</span><span class="sxs-lookup"><span data-stu-id="949ae-168">Model validation</span></span>

<span data-ttu-id="949ae-169">ASP.NET MVC поддерживает возможность [проверки](models/validation.md), дополняя модель объекта атрибутами проверки заметок к данным.</span><span class="sxs-lookup"><span data-stu-id="949ae-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="949ae-170">Атрибуты проверки проверяются на стороне клиента до размещения значений на сервере, а также на сервере перед выполнением действия контроллера.</span><span class="sxs-lookup"><span data-stu-id="949ae-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="949ae-171">Действие контроллера:</span><span class="sxs-lookup"><span data-stu-id="949ae-171">A controller action:</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="949ae-172">Платформа обрабатывает проверку данных запроса на клиенте и на сервере.</span><span class="sxs-lookup"><span data-stu-id="949ae-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="949ae-173">Логика проверки, указанная в типах модели, добавляется в готовые для просмотра представления в виде ненавязчивых заметок и реализуется в браузере с помощью подключаемого модуля [jQuery Validation](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="949ae-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="949ae-174">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="949ae-174">Dependency injection</span></span>

<span data-ttu-id="949ae-175">ASP.NET Core имеет встроенную поддержку [внедрения зависимостей (DI)](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="949ae-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="949ae-176">В ASP.NET MVC Core [контроллеры](controllers/dependency-injection.md) могут запрашивать необходимые служб через свои конструкторы, предоставляя им возможность следовать [принципу явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="949ae-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="949ae-177">Кроме того, приложение может использовать [внедрение зависимостей в файлы представления](views/dependency-injection.md) с помощью директивы `@inject`:</span><span class="sxs-lookup"><span data-stu-id="949ae-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="949ae-178">Фильтры</span><span class="sxs-lookup"><span data-stu-id="949ae-178">Filters</span></span>

<span data-ttu-id="949ae-179">[Фильтры](controllers/filters.md) помогают разработчикам решать общие задачи, такие как обработка исключений или авторизация.</span><span class="sxs-lookup"><span data-stu-id="949ae-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="949ae-180">Фильтры активируют пользовательскую логику предварительной и завершающей обработки для методов действий и могут быть настроены для запуска в определенные моменты в конвейерном выполнении определенного запроса.</span><span class="sxs-lookup"><span data-stu-id="949ae-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="949ae-181">Фильтры могут применяться к контроллерам или действиям в виде атрибутов (или могут выполняться глобально).</span><span class="sxs-lookup"><span data-stu-id="949ae-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="949ae-182">В состав платформы входит несколько фильтров (например, `Authorize`).</span><span class="sxs-lookup"><span data-stu-id="949ae-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="949ae-183">`[Authorize]` является атрибутом, который используется для создания фильтров авторизации MVC.</span><span class="sxs-lookup"><span data-stu-id="949ae-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a><span data-ttu-id="949ae-184">Области</span><span class="sxs-lookup"><span data-stu-id="949ae-184">Areas</span></span>

<span data-ttu-id="949ae-185">[Области](controllers/areas.md) позволяют секционировать крупные ASP.NET Core веб-приложения MVC в более мелкие функциональные группы.</span><span class="sxs-lookup"><span data-stu-id="949ae-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="949ae-186">Область является структурой MVC внутри приложения.</span><span class="sxs-lookup"><span data-stu-id="949ae-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="949ae-187">В проекте MVC логические компоненты, такие как модель, контроллер и представление, находятся в разных папках, и для создания связи между этими компонентами MVC использует соглашения об именовании.</span><span class="sxs-lookup"><span data-stu-id="949ae-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="949ae-188">Крупное приложение может быть целесообразно разделить на отдельные высокоуровневые области функциональности.</span><span class="sxs-lookup"><span data-stu-id="949ae-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="949ae-189">Например, приложение электронной коммерции с несколькими подразделениями, например извлечение, выставление счетов и поиск и т. д. Каждое из этих устройств имеет собственные представления логических компонентов, контроллеры и модели.</span><span class="sxs-lookup"><span data-stu-id="949ae-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="949ae-190">Веб-API</span><span class="sxs-lookup"><span data-stu-id="949ae-190">Web APIs</span></span>

<span data-ttu-id="949ae-191">Помимо того, что ASP.NET Core MV прекрасно подходит для создания веб-сайтов, эта платформа располагает мощной поддержкой для построения веб-API.</span><span class="sxs-lookup"><span data-stu-id="949ae-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="949ae-192">Создавайте службы, доступные для широкого круга клиентов, включая браузеры и мобильные устройства.</span><span class="sxs-lookup"><span data-stu-id="949ae-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="949ae-193">Платформа поддерживает согласования содержимого HTTP со встроенной поддержкой для [форматирования данных](xref:web-api/advanced/formatting) в виде JSON или XML.</span><span class="sxs-lookup"><span data-stu-id="949ae-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="949ae-194">Пишите [пользовательские модули форматирования](xref:web-api/advanced/custom-formatters) для добавления поддержки собственных форматов.</span><span class="sxs-lookup"><span data-stu-id="949ae-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="949ae-195">Используйте функции создания ссылок для поддержки гипермедиа.</span><span class="sxs-lookup"><span data-stu-id="949ae-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="949ae-196">Легко включайте поддержку [общего доступа к ресурсам независимо от источника (CORS)](https://www.w3.org/TR/cors/) для совместного использования веб-API в нескольких веб-приложениях.</span><span class="sxs-lookup"><span data-stu-id="949ae-196">Easily enable support for [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="949ae-197">Тестирование</span><span class="sxs-lookup"><span data-stu-id="949ae-197">Testability</span></span>

<span data-ttu-id="949ae-198">Благодаря используемым интерфейсам и внедрению зависимостей платформа хорошо подходит для модульного тестирования. Кроме того, с помощью таких компонентов, как TestHost и поставщик InMemory для Entity Framework, можно быстро и просто выполнять [интеграционные тесты](xref:test/integration-tests).</span><span class="sxs-lookup"><span data-stu-id="949ae-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="949ae-199">Узнайте больше о [тестировании логики контроллеров](controllers/testing.md).</span><span class="sxs-lookup"><span data-stu-id="949ae-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="no-locrazor-view-engine"></a><span data-ttu-id="949ae-200">Razor Просмотреть подсистему</span><span class="sxs-lookup"><span data-stu-id="949ae-200">Razor view engine</span></span>

<span data-ttu-id="949ae-201">[ASP.NET Core представления MVC](views/overview.md) используют [ Razor обработчик представлений](views/razor.md) для отображения представлений.</span><span class="sxs-lookup"><span data-stu-id="949ae-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="949ae-202">Razor — Это компактный, выразительный и жидкий язык разметки шаблонов для определения представлений с помощью встраиваемого кода C#.</span><span class="sxs-lookup"><span data-stu-id="949ae-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="949ae-203">Razor используется для динамического создания веб-содержимого на сервере.</span><span class="sxs-lookup"><span data-stu-id="949ae-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="949ae-204">Серверный код можно полностью комбинировать с содержимым и кодом на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="949ae-204">You can cleanly mix server code with client side content and code.</span></span>

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

<span data-ttu-id="949ae-205">С помощью Razor обработчика представлений можно определять [макеты](views/layout.md), [частичные представления](views/partial.md) и заменяемые разделы.</span><span class="sxs-lookup"><span data-stu-id="949ae-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="949ae-206">Строго типизированные представления</span><span class="sxs-lookup"><span data-stu-id="949ae-206">Strongly typed views</span></span>

<span data-ttu-id="949ae-207">Razor представления в MVC могут быть строго типизированы на основе модели.</span><span class="sxs-lookup"><span data-stu-id="949ae-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="949ae-208">Контроллеры передают строго типизированную модель в представления для поддержки в них IntelliSense и проверки типов.</span><span class="sxs-lookup"><span data-stu-id="949ae-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="949ae-209">Например, следующее представление отображает модель типа `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="949ae-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="949ae-210">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="949ae-210">Tag Helpers</span></span>

<span data-ttu-id="949ae-211">[Вспомогательные функции тегов](views/tag-helpers/intro.md) позволяют коду на стороне сервера принимать участие в создании и ОТРИСОВКЕ HTML-элементов в Razor файлах.</span><span class="sxs-lookup"><span data-stu-id="949ae-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="949ae-212">Вспомогательные функции тегов используются для определения настраиваемых тегов (например, `<environment>`) или для изменения поведения существующих тегов (например, `<label>`).</span><span class="sxs-lookup"><span data-stu-id="949ae-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="949ae-213">Вспомогательные функции тегов привязываются к определенным элементам на основе имени элемента и его атрибутов.</span><span class="sxs-lookup"><span data-stu-id="949ae-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="949ae-214">Они предоставляют преимущества отрисовки на стороне сервера, сохраняя при этом возможности редактирования HTML.</span><span class="sxs-lookup"><span data-stu-id="949ae-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="949ae-215">Существует множество встроенных вспомогательных функций тегов для общих задач — например, для создания форм, ссылок, загрузки ресурсов и т. д. Кроме того, огромное количество функций доступно в общедоступных репозиториях GitHub и в качестве пакетов NuGet.</span><span class="sxs-lookup"><span data-stu-id="949ae-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="949ae-216">Вспомогательные функции тегов разрабатываются на C# и предназначены для HTML-элементов на основе имени элемента, имени атрибута или родительского тега.</span><span class="sxs-lookup"><span data-stu-id="949ae-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="949ae-217">Например, встроенную функцию LinkTagHelper можно использовать для создания ссылки на действие `AccountsController` для `Login`:</span><span class="sxs-lookup"><span data-stu-id="949ae-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="949ae-218">С помощью `EnvironmentTagHelper` можно включать в приложения различные сценарии (например, необработанные или минифицированные) для конкретной среды выполнения (разработки, промежуточной или производственной):</span><span class="sxs-lookup"><span data-stu-id="949ae-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="949ae-219">Вспомогательные функции тегов обеспечивают удобный для HTML процесс разработки и расширенную среду IntelliSense для создания HTML и Razor разметки.</span><span class="sxs-lookup"><span data-stu-id="949ae-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="949ae-220">Большинство встроенных вспомогательных функций тегов работают с существующими HTML-элементами и предоставляют для них атрибуты на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="949ae-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="949ae-221">Компоненты представлений</span><span class="sxs-lookup"><span data-stu-id="949ae-221">View Components</span></span>

<span data-ttu-id="949ae-222">[Компоненты представлений](views/view-components.md) позволяют упаковывать логику отрисовки и повторно использовать ее в приложении.</span><span class="sxs-lookup"><span data-stu-id="949ae-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="949ae-223">Они аналогичны [частичным представлениям](views/partial.md), но имеют связанную логику.</span><span class="sxs-lookup"><span data-stu-id="949ae-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="949ae-224">Совместимая версия</span><span class="sxs-lookup"><span data-stu-id="949ae-224">Compatibility version</span></span>

<span data-ttu-id="949ae-225">Метод <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> позволяет приложению принимать или отклонять потенциально критические изменения в поведении, появившиеся в ASP.NET Core MVC 2.1 или более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="949ae-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="949ae-226">Для получения дополнительной информации см. <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="949ae-226">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="949ae-227">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="949ae-227">Additional resources</span></span>

* <span data-ttu-id="949ae-228">[Библиотека тестирования митестед. AspNetCore. MVC-Fluent для ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): строго типизированная Библиотека модульного тестирования, предоставляющая интерфейс Fluent для тестирования приложений MVC и веб-API.</span><span class="sxs-lookup"><span data-stu-id="949ae-228">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="949ae-229">(*Не поддерживается и не обслуживается Майкрософт.* )</span><span class="sxs-lookup"><span data-stu-id="949ae-229">(*Not maintained or supported by Microsoft.*)</span></span>
* <xref:blazor/components/prerendering-and-integration>
