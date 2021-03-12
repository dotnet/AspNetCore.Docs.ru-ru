---
title: Внедрение зависимостей в ASP.NET Core
author: rick-anderson
description: Сведения о том, как ASP.NET Core реализует внедрение зависимостей и как его использовать.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ce6804a58c5b17b57732713acc3aeca15042da0a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589716"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="fa7dd-103">Внедрение зависимостей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa7dd-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fa7dd-104">Авторы: [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin), [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="fa7dd-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="fa7dd-105">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="fa7dd-106">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="fa7dd-107">Дополнительные сведения об использовании внедрения зависимостей в приложениях (кроме веб-приложений) см. в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="fa7dd-108">Дополнительные сведения о внедрении параметров зависимостей см. в разделе <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="fa7dd-109">В этой статье приводятся сведения о внедрении зависимостей в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="fa7dd-110">Основная документация по использованию внедрения зависимостей указана в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="fa7dd-111">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fa7dd-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="fa7dd-112">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa7dd-112">Overview of dependency injection</span></span>

<span data-ttu-id="fa7dd-113">*Зависимость* — это любой объект, от которого зависит другой объект.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="fa7dd-114">Рассмотрим следующий класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="fa7dd-115">Класс может создать экземпляр класса `MyDependency`, чтобы использовать его метод `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="fa7dd-116">В следующем примере класс `MyDependency` выступает зависимостью класса `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="fa7dd-117">Этот класс создает `MyDependency` и напрямую зависит от этого класса.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="fa7dd-118">Зависимости в коде, как в предыдущем примере, представляют собой определенную проблему. Их следует избегать по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="fa7dd-119">Чтобы заменить `MyDependency` другой реализацией, класс `IndexModel` необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="fa7dd-120">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="fa7dd-121">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="fa7dd-122">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="fa7dd-123">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="fa7dd-124">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="fa7dd-125">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="fa7dd-126">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="fa7dd-127">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="fa7dd-128">Как правило, службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="fa7dd-129">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="fa7dd-130">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="fa7dd-131">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="fa7dd-132">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="fa7dd-133">Пример приложения регистрирует службу `IMyDependency` с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="fa7dd-134">Метод <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> регистрирует службу с заданной областью времени существования, временем существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="fa7dd-135">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="fa7dd-136">В примере приложения запрашивается служба `IMyDependency`, которая затем используется для вызова метода `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="fa7dd-137">Используя шаблон внедрения зависимостей, контроллер:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="fa7dd-138">не использует конкретный тип `MyDependency`, только интерфейс `IMyDependency`, который он реализует.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="fa7dd-139">Это упрощает изменение реализации, используемой контроллером, без изменения контроллера.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="fa7dd-140">не создает экземпляр `MyDependency`, он создается контейнером внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="fa7dd-141">Реализацию интерфейса `IMyDependency` можно улучшить с помощью встроенного API ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="fa7dd-142">Обновленный метод `ConfigureServices` регистрирует новую реализацию `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="fa7dd-143">`MyDependency2` зависит от <xref:Microsoft.Extensions.Logging.ILogger%601>, который запрашивается в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="fa7dd-144">`ILogger<TCategoryName>` — это [предоставленная платформой служба](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="fa7dd-145">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="fa7dd-146">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="fa7dd-147">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="fa7dd-148">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="fa7dd-149">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="fa7dd-150">В терминологии внедрения зависимостей — служба:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="fa7dd-151">Обычно является объектом, предоставляющим службу для других объектов, например службу `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="fa7dd-152">Не относится к веб-службе, хотя служба может использовать веб-службу.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="fa7dd-153">Платформа предоставляет эффективную систему [ведения журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="fa7dd-154">Реализации `IMyDependency`, приведенные в предыдущем примере были написаны для демонстрации базового внедрения зависимостей, а не для реализации ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="fa7dd-155">Большинству приложений не нужно писать средства ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="fa7dd-156">В следующем коде показано использование журнала по умолчанию, для которого не требуется регистрация служб в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="fa7dd-157">Используя приведенный выше код, не нужно обновлять `ConfigureServices`, поскольку платформа предоставляет возможность [ведения журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="fa7dd-158">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="fa7dd-158">Services injected into Startup</span></span>

<span data-ttu-id="fa7dd-159">Службы можно внедрить в конструктор `Startup` и метод `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="fa7dd-160">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие службы:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="fa7dd-161">Любая служба, зарегистрированная в контейнере внедрения зависимостей, может быть внедрена в метод `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="fa7dd-162">Дополнительные сведения см. в разделах <xref:fundamentals/startup> и [Доступ к конфигурации во время запуска](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="fa7dd-163">Регистрация групп служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="fa7dd-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="fa7dd-164">Для регистрации группы связанных служб на платформе ASP.NET Core используется соглашение.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="fa7dd-165">Соглашение заключается в использовании одного метода расширения `Add{GROUP_NAME}` для регистрации всех служб, необходимых компоненту платформы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="fa7dd-166">Например, метод расширения <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> регистрирует службы, необходимые контроллерам MVC.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-166">For example, the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="fa7dd-167">Следующий код создается шаблоном Razor Pages с использованием отдельных учетных записей пользователей. Он демонстрирует, как добавить дополнительные службы в контейнер с помощью методов расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-167">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="fa7dd-168">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="fa7dd-168">Service lifetimes</span></span>

<span data-ttu-id="fa7dd-169">См. раздел [Время существования службы](/dotnet/core/extensions/dependency-injection#service-lifetimes) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="fa7dd-170">Используйте службы с заданной областью в ПО промежуточного слоя, применяя один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="fa7dd-171">Внедрите службу в метод `Invoke` или `InvokeAsync` ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="fa7dd-172">С помощью [внедрите конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) создается исключение времени выполнения, поскольку оно заставляет службу с заданной областью вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="fa7dd-173">В примере в разделе [Параметры времени существования и регистрации](#lifetime-and-registration-options) демонстрируется подход `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="fa7dd-174">Используйте [фабричное ПО промежуточного слоя](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="fa7dd-175">ПО промежуточного слоя, зарегистрированное с использованием этого подхода, активируется при каждом клиентском запросе (подключении), что позволяет внедрять службы с заданной областью в метод `InvokeAsync` ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="fa7dd-176">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="fa7dd-177">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="fa7dd-177">Service registration methods</span></span>

<span data-ttu-id="fa7dd-178">См. раздел [Методы регистрации службы](/dotnet/core/extensions/dependency-injection#service-registration-methods) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="fa7dd-179">Распространенный сценарий для использования нескольких реализаций — [макетирование типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="fa7dd-180">Регистрация службы только с типом реализации эквивалентна регистрации этой службы с той же реализацией и типом службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="fa7dd-181">Именно поэтому несколько реализаций службы не могут быть зарегистрированы с помощью методов, которые не принимают явный тип службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="fa7dd-182">Эти методы могут регистрировать несколько *экземпляров* службы, но все они будут иметь одинаковую *реализацию* типа.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="fa7dd-183">Любой из указанных выше методов регистрации службы можно использовать для регистрации нескольких экземпляров службы одного типа службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="fa7dd-184">В следующем примере метод `AddSingleton` вызывается дважды с типом службы `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="fa7dd-185">Второй вызов `AddSingleton` переопределяет предыдущий, если он разрешается как `IMyDependency`, и добавляет к предыдущему, если несколько служб разрешаются через `IEnumerable<IMyDependency>`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="fa7dd-186">Службы отображаются в том порядке, в котором они были зарегистрированы при разрешении через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a><span data-ttu-id="fa7dd-187">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="fa7dd-187">Constructor injection behavior</span></span>

<span data-ttu-id="fa7dd-188">См. раздел [Поведение при внедрении конструктора](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="fa7dd-189">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="fa7dd-189">Entity Framework contexts</span></span>

<span data-ttu-id="fa7dd-190">По умолчанию контексты Entity Framework добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="fa7dd-191">Чтобы использовать другое время существования, укажите его с помощью перегрузки <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="fa7dd-192">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="fa7dd-193">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="fa7dd-193">Lifetime and registration options</span></span>

<span data-ttu-id="fa7dd-194">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации службы, рассмотрим интерфейсы, представляющие задачу в виде операции с идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="fa7dd-195">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="fa7dd-196">Следующий класс `Operation` реализует все предыдущие интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="fa7dd-197">Конструктор `Operation` создает идентификатор GUID и сохраняет последние 4 символа в свойстве `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="fa7dd-198">Метод `Startup.ConfigureServices` создает несколько регистраций класса `Operation` в соответствии с именованным временем существования:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="fa7dd-199">В примере приложения показано время существования объектов в пределах запросов и между запросами.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="fa7dd-200">`IndexModel` и ПО промежуточного слоя запрашивают каждый тип `IOperation` и регистрируют `OperationId` для каждого из них:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="fa7dd-201">Аналогично `IndexModel`, ПО промежуточного слоя и разрешает те же службы:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="fa7dd-202">Службы с заданной областью должны быть разрешены в методе `InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="fa7dd-203">Выходные данные средства ведения журнала содержат:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-203">The logger output shows:</span></span>

* <span data-ttu-id="fa7dd-204">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-204">*Transient* objects are always different.</span></span> <span data-ttu-id="fa7dd-205">Значение временного `OperationId` отличается в `IndexModel` и ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="fa7dd-206">Объекты с *заданной областью* остаются неизменными в пределах одного запроса, но в разных запросах используются разные объекты.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="fa7dd-207">*Одноэлементные* объекты одинаковы для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="fa7dd-208">Чтобы уменьшить объем выводимых данных журнала, задайте в файле *appsettings.Development.json* параметр "Logging:LogLevel:Microsoft:Error".</span><span class="sxs-lookup"><span data-stu-id="fa7dd-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="fa7dd-209">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="fa7dd-209">Call services from main</span></span>

<span data-ttu-id="fa7dd-210">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="fa7dd-211">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="fa7dd-212">В следующем примере показано, как получить доступ к службе `IMyDependency` с заданной областью и вызвать ее метод `WriteMessage` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="fa7dd-213">Проверка области</span><span class="sxs-lookup"><span data-stu-id="fa7dd-213">Scope validation</span></span>

<span data-ttu-id="fa7dd-214">См. раздел [Поведение при внедрении конструктора](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="fa7dd-215">Дополнительные сведения см. в разделе [Проверка области](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="fa7dd-216">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="fa7dd-216">Request Services</span></span>

<span data-ttu-id="fa7dd-217">Службы, доступные в пределах запроса ASP.NET Core, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="fa7dd-218">При запросе в рамках запроса службы и их зависимости разрешаются из коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="fa7dd-219">Платформа создает область для каждого запроса, а `RequestServices` предоставляет поставщик услуг с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="fa7dd-220">Все службы с заданной областью действительны до тех пор, пока запрос активен.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="fa7dd-221">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не разрешать службы из коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="fa7dd-222">В результате создаются классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="fa7dd-223">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa7dd-223">Design services for dependency injection</span></span>

<span data-ttu-id="fa7dd-224">При разработке служб для внедрения зависимостей придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="fa7dd-225">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="fa7dd-226">Избегайте создания глобального состояния. Для этого проектируйте приложения для использования отдельных служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="fa7dd-227">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="fa7dd-228">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="fa7dd-229">Сделайте службы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="fa7dd-230">Если класс имеет слишком много внедренных зависимостей, это может указывать на то, что у класса слишком много задач и он нарушает [принцип единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="fa7dd-231">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новые классы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="fa7dd-232">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-232">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="fa7dd-233">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="fa7dd-233">Disposal of services</span></span>

<span data-ttu-id="fa7dd-234">Контейнер вызывает <xref:System.IDisposable.Dispose%2A> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="fa7dd-235">Службы, разрешенные из контейнера, никогда не должны удаляться разработчиком.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="fa7dd-236">Если тип или фабрика зарегистрированы как одноэлементный объект, контейнер автоматически удалит одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="fa7dd-237">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="fa7dd-238">После каждого обновления страницы индекса в консоли отладки отображаются следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="fa7dd-239">Службы, не созданные контейнером службы</span><span class="sxs-lookup"><span data-stu-id="fa7dd-239">Services not created by the service container</span></span>

<span data-ttu-id="fa7dd-240">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="fa7dd-241">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-241">In the preceding code:</span></span>

* <span data-ttu-id="fa7dd-242">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="fa7dd-243">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="fa7dd-244">За удаление служб отвечает разработчик.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="fa7dd-245">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="fa7dd-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="fa7dd-246">См. раздел [Рекомендации по IDisposable при использовании промежуточного и общего экземпляра](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="fa7dd-247">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="fa7dd-247">Default service container replacement</span></span>

<span data-ttu-id="fa7dd-248">См. раздел [Замена контейнера службы по умолчанию](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="fa7dd-249">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="fa7dd-249">Recommendations</span></span>

<span data-ttu-id="fa7dd-250">См. раздел [Рекомендации](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="fa7dd-251">Старайтесь не использовать *схему указателя служб*.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-251">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="fa7dd-252">Например, не вызывайте <xref:System.IServiceProvider.GetService%2A> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="fa7dd-253">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-253">**Incorrect:**</span></span>

    ![Неверный код](dependency-injection/_static/bad.png)

  <span data-ttu-id="fa7dd-255">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-255">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="fa7dd-256">Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="fa7dd-257">Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="fa7dd-258">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="fa7dd-259">Избегайте вызовов <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="fa7dd-260">Вызов `BuildServiceProvider` обычно происходит, когда разработчику необходимо разрешить службу в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="fa7dd-261">Например, рассмотрим случай, когда `LoginPath` загружается из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="fa7dd-262">Добавьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-262">Avoid the following approach:</span></span>

  ![Неверный код при вызове BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="fa7dd-264">На предыдущем рисунке при выборе строки, отмеченной зеленой волнистой линией в разделе `services.BuildServiceProvider`, отображается следующее предупреждение ASP0000:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="fa7dd-265">ASP0000. Вызов BuildServiceProvider из кода приложения приводит к созданию дополнительной копии создаваемых одноэлементных служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="fa7dd-266">В качестве параметров для Configure можно использовать альтернативные варианты, такие как службы внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="fa7dd-267">При вызове `BuildServiceProvider` создается второй контейнер, который может создавать разорванные одноэлементные экземпляры и ссылаться на графы объектов в нескольких контейнерах.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="fa7dd-268">Правильный способ получения `LoginPath` — использование встроенной поддержки шаблона параметров для внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="fa7dd-269">Неудаляемые временные службы фиксируются контейнером для их удаления.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="fa7dd-270">Это может привести к утечке памяти, если она разрешена из контейнера верхнего уровня.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="fa7dd-271">Включите проверку области, чтобы убедиться, что в приложении нет отдельных объектов, записывающих службы с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="fa7dd-272">Дополнительные сведения см. в разделе [Проверка области](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="fa7dd-273">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="fa7dd-274">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="fa7dd-275">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="fa7dd-276">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="fa7dd-277">Рекомендуемые подходы к мультитенантности при внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa7dd-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="fa7dd-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) — это платформа приложений для создания модульных мультитенантных приложений в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="fa7dd-279">Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="fa7dd-280">Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. [здесь](https://github.com/OrchardCMS/OrchardCore.Samples).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fa7dd-281">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="fa7dd-281">Framework-provided services</span></span>

<span data-ttu-id="fa7dd-282">Метод `Startup.ConfigureServices` регистрирует службы, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="fa7dd-283">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="fa7dd-284">Для приложений, основанных на шаблонах ASP.NET Core, платформа регистрирует более 250 служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="fa7dd-285">В следующей таблице перечислены некоторые примеры этих зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="fa7dd-286">Тип службы</span><span class="sxs-lookup"><span data-stu-id="fa7dd-286">Service Type</span></span>                                                                                    | <span data-ttu-id="fa7dd-287">Время существования</span><span class="sxs-lookup"><span data-stu-id="fa7dd-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="fa7dd-288">Временный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="fa7dd-289">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="fa7dd-290">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="fa7dd-291">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="fa7dd-292">Временный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="fa7dd-293">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="fa7dd-294">Временный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="fa7dd-295">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="fa7dd-296">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="fa7dd-297">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="fa7dd-298">Временный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="fa7dd-299">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="fa7dd-300">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="fa7dd-301">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="fa7dd-302">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fa7dd-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="fa7dd-303">Шаблоны конференций NDC для разработки приложений с внедрением зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa7dd-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="fa7dd-304">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa7dd-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="fa7dd-305">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="fa7dd-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="fa7dd-306">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa7dd-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="fa7dd-307">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="fa7dd-307">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="fa7dd-308">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="fa7dd-308">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fa7dd-309">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="fa7dd-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="fa7dd-310">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="fa7dd-311">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="fa7dd-312">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fa7dd-312">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="fa7dd-313">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa7dd-313">Overview of dependency injection</span></span>

<span data-ttu-id="fa7dd-314">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="fa7dd-315">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="fa7dd-316">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="fa7dd-317">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="fa7dd-318">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="fa7dd-319">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="fa7dd-320">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="fa7dd-321">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="fa7dd-322">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="fa7dd-323">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="fa7dd-324">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="fa7dd-325">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="fa7dd-326">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="fa7dd-327">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="fa7dd-328">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="fa7dd-329">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="fa7dd-330">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="fa7dd-331">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="fa7dd-332">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-332">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="fa7dd-333">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="fa7dd-334">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="fa7dd-335">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="fa7dd-336">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="fa7dd-337">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="fa7dd-338">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="fa7dd-339">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="fa7dd-340">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fa7dd-341">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="fa7dd-342">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="fa7dd-343">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="fa7dd-344">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="fa7dd-345">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="fa7dd-346">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет и, возможно, настраивает службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-346">Each `services.Add{SERVICE_NAME}` extension method adds, and potentially configures, services.</span></span> <span data-ttu-id="fa7dd-347">Например, `services.AddControllersWithViews`, `services.AddRazorPages` и `services.AddControllers` добавляют службы, необходимые приложениям ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-347">For example, `services.AddControllersWithViews`, `services.AddRazorPages`, and `services.AddControllers` adds the services ASP.NET Core apps require.</span></span> <span data-ttu-id="fa7dd-348">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="fa7dd-349">Поместите методы расширения в пространство имен <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>, чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-349">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span> <span data-ttu-id="fa7dd-350">Включение части `Microsoft.Extensions.DependencyInjection` пространства имен для методов расширения внедрения зависимостей также:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-350">Including the namespace portion `Microsoft.Extensions.DependencyInjection` for DI extension methods also:</span></span>
>
> * <span data-ttu-id="fa7dd-351">позволяет отображать их в [IntelliSense](/visualstudio/ide/using-intellisense) без добавления дополнительных блоков `using`;</span><span class="sxs-lookup"><span data-stu-id="fa7dd-351">Allows them to be displayed in [IntelliSense](/visualstudio/ide/using-intellisense) without adding additional `using` blocks.</span></span>
> * <span data-ttu-id="fa7dd-352">позволяет избежать чрезмерного количества инструкций `using` в классе `Startup`, из которого обычно вызываются эти методы расширения.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-352">Prevents excessive `using` statements in the `Startup` class where these extension methods are typically called from.</span></span>

<span data-ttu-id="fa7dd-353">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-353">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="fa7dd-354">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-354">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="fa7dd-355">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-355">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="fa7dd-356">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-356">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="fa7dd-357">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="fa7dd-357">Services injected into Startup</span></span>

<span data-ttu-id="fa7dd-358">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-358">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="fa7dd-359">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-359">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="fa7dd-360">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-360">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fa7dd-361">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="fa7dd-361">Framework-provided services</span></span>

<span data-ttu-id="fa7dd-362">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-362">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="fa7dd-363">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-363">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="fa7dd-364">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-364">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="fa7dd-365">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-365">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="fa7dd-366">Тип службы</span><span class="sxs-lookup"><span data-stu-id="fa7dd-366">Service Type</span></span> | <span data-ttu-id="fa7dd-367">Время существования</span><span class="sxs-lookup"><span data-stu-id="fa7dd-367">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="fa7dd-368">Временный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-368">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="fa7dd-369">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-369">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="fa7dd-370">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="fa7dd-371">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-371">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="fa7dd-372">Временный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-372">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="fa7dd-373">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-373">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="fa7dd-374">Временный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-374">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="fa7dd-375">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-375">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="fa7dd-376">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-376">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="fa7dd-377">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-377">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="fa7dd-378">Временный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-378">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="fa7dd-379">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-379">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="fa7dd-380">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-380">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="fa7dd-381">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-381">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="fa7dd-382">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="fa7dd-382">Register additional services with extension methods</span></span>

<span data-ttu-id="fa7dd-383">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-383">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="fa7dd-384">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-384">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="fa7dd-385">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-385">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="fa7dd-386">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="fa7dd-386">Service lifetimes</span></span>

<span data-ttu-id="fa7dd-387">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-387">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="fa7dd-388">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-388">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="fa7dd-389">Временный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-389">Transient</span></span>

<span data-ttu-id="fa7dd-390">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-390">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="fa7dd-391">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-391">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="fa7dd-392">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-392">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="fa7dd-393">Область действия</span><span class="sxs-lookup"><span data-stu-id="fa7dd-393">Scoped</span></span>

<span data-ttu-id="fa7dd-394">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-394">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="fa7dd-395">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-395">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="fa7dd-396">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-396">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="fa7dd-397">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-397">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="fa7dd-398">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-398">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="fa7dd-399">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="fa7dd-399">Singleton</span></span>

<span data-ttu-id="fa7dd-400">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-400">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="fa7dd-401">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-401">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="fa7dd-402">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-402">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="fa7dd-403">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-403">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="fa7dd-404">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-404">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="fa7dd-405">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-405">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="fa7dd-406">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-406">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="fa7dd-407">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="fa7dd-407">Service registration methods</span></span>

<span data-ttu-id="fa7dd-408">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-408">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="fa7dd-409">Метод</span><span class="sxs-lookup"><span data-stu-id="fa7dd-409">Method</span></span> | <span data-ttu-id="fa7dd-410">Автоматически</span><span class="sxs-lookup"><span data-stu-id="fa7dd-410">Automatic</span></span><br><span data-ttu-id="fa7dd-411">object</span><span class="sxs-lookup"><span data-stu-id="fa7dd-411">object</span></span><br><span data-ttu-id="fa7dd-412">удаление</span><span class="sxs-lookup"><span data-stu-id="fa7dd-412">disposal</span></span> | <span data-ttu-id="fa7dd-413">Несколько</span><span class="sxs-lookup"><span data-stu-id="fa7dd-413">Multiple</span></span><br><span data-ttu-id="fa7dd-414">реализации</span><span class="sxs-lookup"><span data-stu-id="fa7dd-414">implementations</span></span> | <span data-ttu-id="fa7dd-415">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="fa7dd-415">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="fa7dd-416">Пример</span><span class="sxs-lookup"><span data-stu-id="fa7dd-416">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="fa7dd-417">Да</span><span class="sxs-lookup"><span data-stu-id="fa7dd-417">Yes</span></span> | <span data-ttu-id="fa7dd-418">Да</span><span class="sxs-lookup"><span data-stu-id="fa7dd-418">Yes</span></span> | <span data-ttu-id="fa7dd-419">Нет</span><span class="sxs-lookup"><span data-stu-id="fa7dd-419">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="fa7dd-420">Примеры:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-420">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="fa7dd-421">Да</span><span class="sxs-lookup"><span data-stu-id="fa7dd-421">Yes</span></span> | <span data-ttu-id="fa7dd-422">Да</span><span class="sxs-lookup"><span data-stu-id="fa7dd-422">Yes</span></span> | <span data-ttu-id="fa7dd-423">Да</span><span class="sxs-lookup"><span data-stu-id="fa7dd-423">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="fa7dd-424">Пример</span><span class="sxs-lookup"><span data-stu-id="fa7dd-424">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="fa7dd-425">Да</span><span class="sxs-lookup"><span data-stu-id="fa7dd-425">Yes</span></span> | <span data-ttu-id="fa7dd-426">Нет</span><span class="sxs-lookup"><span data-stu-id="fa7dd-426">No</span></span> | <span data-ttu-id="fa7dd-427">Нет</span><span class="sxs-lookup"><span data-stu-id="fa7dd-427">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="fa7dd-428">Примеры:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-428">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="fa7dd-429">Нет</span><span class="sxs-lookup"><span data-stu-id="fa7dd-429">No</span></span> | <span data-ttu-id="fa7dd-430">Да</span><span class="sxs-lookup"><span data-stu-id="fa7dd-430">Yes</span></span> | <span data-ttu-id="fa7dd-431">Да</span><span class="sxs-lookup"><span data-stu-id="fa7dd-431">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="fa7dd-432">Примеры:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-432">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="fa7dd-433">Нет</span><span class="sxs-lookup"><span data-stu-id="fa7dd-433">No</span></span> | <span data-ttu-id="fa7dd-434">Нет</span><span class="sxs-lookup"><span data-stu-id="fa7dd-434">No</span></span> | <span data-ttu-id="fa7dd-435">Да</span><span class="sxs-lookup"><span data-stu-id="fa7dd-435">Yes</span></span> |

<span data-ttu-id="fa7dd-436">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-436">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="fa7dd-437">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-437">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="fa7dd-438">Регистрация службы только с типом реализации эквивалентна регистрации этой службы с той же реализацией и типом службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-438">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="fa7dd-439">Именно поэтому несколько реализаций службы не могут быть зарегистрированы с помощью методов, которые не принимают явный тип службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-439">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="fa7dd-440">Эти методы могут регистрировать несколько *экземпляров* службы, но все они будут иметь одинаковую *реализацию* типа.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-440">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="fa7dd-441">Любой из указанных выше методов регистрации службы можно использовать для регистрации нескольких экземпляров службы одного типа службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-441">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="fa7dd-442">В следующем примере метод `AddSingleton` вызывается дважды с типом службы `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-442">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="fa7dd-443">Второй вызов `AddSingleton` переопределяет предыдущий, если он разрешается как `IMyDependency`, и добавляет к предыдущему, если несколько служб разрешаются через `IEnumerable<IMyDependency>`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-443">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="fa7dd-444">Службы отображаются в том порядке, в котором они были зарегистрированы при разрешении через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-444">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="fa7dd-445">Платформа также предоставляет методы расширения `TryAdd{LIFETIME}`, которые регистрируют службу только в том случае, если реализация еще не зарегистрирована.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-445">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="fa7dd-446">В следующем примере вызов `AddSingleton` регистрирует `MyDependency` как реализацию для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-446">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="fa7dd-447">Вызов `TryAddSingleton` ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-447">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="fa7dd-448">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-448">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="fa7dd-449">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-449">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="fa7dd-450">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-450">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="fa7dd-451">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-451">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="fa7dd-452">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-452">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="fa7dd-453">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-453">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="fa7dd-454">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-454">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="fa7dd-455">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-455">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="fa7dd-456">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="fa7dd-456">Constructor injection behavior</span></span>

<span data-ttu-id="fa7dd-457">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-457">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="fa7dd-458"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-458"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="fa7dd-459">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-459">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="fa7dd-460">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-460">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="fa7dd-461">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-461">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="fa7dd-462">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-462">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="fa7dd-463">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-463">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="fa7dd-464">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="fa7dd-464">Entity Framework contexts</span></span>

<span data-ttu-id="fa7dd-465">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-465">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="fa7dd-466">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-466">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="fa7dd-467">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-467">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="fa7dd-468">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="fa7dd-468">Lifetime and registration options</span></span>

<span data-ttu-id="fa7dd-469">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-469">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="fa7dd-470">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-470">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="fa7dd-471">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-471">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="fa7dd-472">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-472">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="fa7dd-473">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-473">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="fa7dd-474">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-474">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="fa7dd-475">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-475">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="fa7dd-476">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-476">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="fa7dd-477">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-477">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="fa7dd-478">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-478">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="fa7dd-479">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-479">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="fa7dd-480">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-480">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="fa7dd-481">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-481">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="fa7dd-482">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-482">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="fa7dd-483">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-483">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="fa7dd-484">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-484">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="fa7dd-485">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-485">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="fa7dd-486">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-486">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="fa7dd-487">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-487">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="fa7dd-488">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="fa7dd-488">**First request:**</span></span>

<span data-ttu-id="fa7dd-489">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-489">Controller operations:</span></span>

<span data-ttu-id="fa7dd-490">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="fa7dd-490">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="fa7dd-491">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fa7dd-491">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fa7dd-492">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fa7dd-492">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fa7dd-493">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fa7dd-493">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fa7dd-494">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-494">`OperationService` operations:</span></span>

<span data-ttu-id="fa7dd-495">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="fa7dd-495">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="fa7dd-496">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fa7dd-496">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fa7dd-497">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fa7dd-497">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fa7dd-498">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fa7dd-498">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fa7dd-499">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="fa7dd-499">**Second request:**</span></span>

<span data-ttu-id="fa7dd-500">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-500">Controller operations:</span></span>

<span data-ttu-id="fa7dd-501">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="fa7dd-501">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="fa7dd-502">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fa7dd-502">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fa7dd-503">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fa7dd-503">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fa7dd-504">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fa7dd-504">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fa7dd-505">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-505">`OperationService` operations:</span></span>

<span data-ttu-id="fa7dd-506">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="fa7dd-506">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="fa7dd-507">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fa7dd-507">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fa7dd-508">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fa7dd-508">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fa7dd-509">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fa7dd-509">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fa7dd-510">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-510">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="fa7dd-511">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-511">*Transient* objects are always different.</span></span> <span data-ttu-id="fa7dd-512">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-512">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="fa7dd-513">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-513">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="fa7dd-514">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-514">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="fa7dd-515">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-515">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="fa7dd-516">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="fa7dd-516">Call services from main</span></span>

<span data-ttu-id="fa7dd-517">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-517">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="fa7dd-518">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-518">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="fa7dd-519">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-519">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="fa7dd-520">Проверка области</span><span class="sxs-lookup"><span data-stu-id="fa7dd-520">Scope validation</span></span>

<span data-ttu-id="fa7dd-521">Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-521">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="fa7dd-522">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-522">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="fa7dd-523">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-523">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="fa7dd-524">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-524">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="fa7dd-525">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-525">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="fa7dd-526">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-526">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="fa7dd-527">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-527">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="fa7dd-528">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-528">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="fa7dd-529">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-529">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="fa7dd-530">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="fa7dd-530">Request Services</span></span>

<span data-ttu-id="fa7dd-531">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-531">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="fa7dd-532">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-532">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="fa7dd-533">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-533">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="fa7dd-534">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-534">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="fa7dd-535">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-535">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="fa7dd-536">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-536">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="fa7dd-537">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-537">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="fa7dd-538">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa7dd-538">Design services for dependency injection</span></span>

<span data-ttu-id="fa7dd-539">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-539">Best practices are to:</span></span>

* <span data-ttu-id="fa7dd-540">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-540">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="fa7dd-541">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-541">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="fa7dd-542">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-542">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="fa7dd-543">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-543">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="fa7dd-544">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-544">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="fa7dd-545">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-545">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="fa7dd-546">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-546">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="fa7dd-547">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-547">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="fa7dd-548">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-548">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="fa7dd-549">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-549">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="fa7dd-550">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="fa7dd-550">Disposal of services</span></span>

<span data-ttu-id="fa7dd-551">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-551">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="fa7dd-552">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-552">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="fa7dd-553">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-553">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="fa7dd-554">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-554">In the following example:</span></span>

* <span data-ttu-id="fa7dd-555">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-555">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="fa7dd-556">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-556">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="fa7dd-557">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-557">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="fa7dd-558">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-558">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="fa7dd-559">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="fa7dd-559">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="fa7dd-560">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="fa7dd-560">Transient, limited lifetime</span></span>

<span data-ttu-id="fa7dd-561">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="fa7dd-561">**Scenario**</span></span>

<span data-ttu-id="fa7dd-562">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-562">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="fa7dd-563">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-563">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="fa7dd-564">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-564">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="fa7dd-565">**Решение**</span><span class="sxs-lookup"><span data-stu-id="fa7dd-565">**Solution**</span></span>

<span data-ttu-id="fa7dd-566">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-566">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="fa7dd-567">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-567">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="fa7dd-568">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-568">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="fa7dd-569">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-569">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="fa7dd-570">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-570">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="fa7dd-571">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="fa7dd-571">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="fa7dd-572">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="fa7dd-572">**Scenario**</span></span>

<span data-ttu-id="fa7dd-573">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-573">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="fa7dd-574">**Решение**</span><span class="sxs-lookup"><span data-stu-id="fa7dd-574">**Solution**</span></span>

<span data-ttu-id="fa7dd-575">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-575">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="fa7dd-576">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-576">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="fa7dd-577">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-577">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="fa7dd-578">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-578">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="fa7dd-579">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="fa7dd-579">General Guidelines</span></span>

* <span data-ttu-id="fa7dd-580">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-580">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="fa7dd-581">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-581">Use the factory pattern instead.</span></span>
* <span data-ttu-id="fa7dd-582">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-582">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="fa7dd-583">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-583">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="fa7dd-584">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-584">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="fa7dd-585">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-585">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="fa7dd-586">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-586">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="fa7dd-587">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-587">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="fa7dd-588">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="fa7dd-588">Default service container replacement</span></span>

<span data-ttu-id="fa7dd-589">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-589">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="fa7dd-590">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-590">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="fa7dd-591">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="fa7dd-591">Property injection</span></span>
* <span data-ttu-id="fa7dd-592">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="fa7dd-592">Injection based on name</span></span>
* <span data-ttu-id="fa7dd-593">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="fa7dd-593">Child containers</span></span>
* <span data-ttu-id="fa7dd-594">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="fa7dd-594">Custom lifetime management</span></span>
* <span data-ttu-id="fa7dd-595">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="fa7dd-595">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="fa7dd-596">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="fa7dd-596">Convention-based registration</span></span>

<span data-ttu-id="fa7dd-597">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-597">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="fa7dd-598">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="fa7dd-598">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="fa7dd-599">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="fa7dd-599">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="fa7dd-600">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="fa7dd-600">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="fa7dd-601">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="fa7dd-601">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="fa7dd-602">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="fa7dd-602">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="fa7dd-603">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="fa7dd-603">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="fa7dd-604">Unity</span><span class="sxs-lookup"><span data-stu-id="fa7dd-604">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="fa7dd-605">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="fa7dd-605">Thread safety</span></span>

<span data-ttu-id="fa7dd-606">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-606">Create thread-safe singleton services.</span></span> <span data-ttu-id="fa7dd-607">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-607">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="fa7dd-608">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-608">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="fa7dd-609">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-609">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="fa7dd-610">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="fa7dd-610">Recommendations</span></span>

* <span data-ttu-id="fa7dd-611">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-611">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="fa7dd-612">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-612">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="fa7dd-613">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-613">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="fa7dd-614">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-614">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="fa7dd-615">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-615">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="fa7dd-616">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-616">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="fa7dd-617">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-617">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="fa7dd-618">Избегайте статического доступа к службам.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-618">Avoid static access to services.</span></span> <span data-ttu-id="fa7dd-619">Например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-619">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="fa7dd-620">Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-620">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="fa7dd-621">Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-621">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="fa7dd-622">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-622">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="fa7dd-623">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="fa7dd-623">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="fa7dd-624">Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-624">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="fa7dd-625">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="fa7dd-625">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="fa7dd-626">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-626">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="fa7dd-627">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-627">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="fa7dd-628">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-628">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="fa7dd-629">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="fa7dd-629">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fa7dd-630">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fa7dd-630">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="fa7dd-631">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa7dd-631">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="fa7dd-632">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="fa7dd-632">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="fa7dd-633">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa7dd-633">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="fa7dd-634">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="fa7dd-634">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="fa7dd-635">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="fa7dd-635">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
