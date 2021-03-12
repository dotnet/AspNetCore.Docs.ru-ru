---
title: ASP.NET Core Blazor Server и Entity Framework Core (EF Core)
author: JeremyLikness
description: Руководство по использованию EF Core в приложениях Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 53d276db996304852d69566584e43d47aa73f921
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586557"
---
# <a name="aspnet-core-blazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="d35e5-103">ASP.NET Core Blazor Server и Entity Framework Core (EF Core)</span><span class="sxs-lookup"><span data-stu-id="d35e5-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d35e5-104">Blazor Server — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="d35e5-104">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="d35e5-105">Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="d35e5-105">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="d35e5-106">Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="d35e5-106">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="d35e5-107">Для уникальной модели приложения, которую предоставляет Blazor Server, требуется специальный подход к использованию Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d35e5-107">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d35e5-108">В этой статье рассматривается EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d35e5-108">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="d35e5-109">Приложения Blazor WebAssembly выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных.</span><span class="sxs-lookup"><span data-stu-id="d35e5-109">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="d35e5-110">Запуск EF Core в Blazor WebAssembly выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="d35e5-110">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="d35e5-111">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="d35e5-111">Sample app</span></span></h2>

<span data-ttu-id="d35e5-112">Пример приложения был создан в виде справочника для приложений Blazor Server, использующих EF Core.</span><span class="sxs-lookup"><span data-stu-id="d35e5-112">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="d35e5-113">Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления.</span><span class="sxs-lookup"><span data-stu-id="d35e5-113">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="d35e5-114">В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.</span><span class="sxs-lookup"><span data-stu-id="d35e5-114">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="d35e5-115">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d35e5-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d35e5-116">В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе.</span><span class="sxs-lookup"><span data-stu-id="d35e5-116">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="d35e5-117">В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL.</span><span class="sxs-lookup"><span data-stu-id="d35e5-117">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="d35e5-118">Это настраивается в `appsettings.Development.json`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-118">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="d35e5-119">Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="d35e5-119">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="d35e5-120">Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="d35e5-120">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="d35e5-121">Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны.</span><span class="sxs-lookup"><span data-stu-id="d35e5-121">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="d35e5-122">Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров Razor, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="d35e5-122">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="d35e5-123">Доступ к базе данных</span><span class="sxs-lookup"><span data-stu-id="d35e5-123">Database access</span></span></h2>

<span data-ttu-id="d35e5-124">EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="d35e5-124">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="d35e5-125">EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*.</span><span class="sxs-lookup"><span data-stu-id="d35e5-125">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="d35e5-126">В приложениях Blazor Server регистрация службы с заданной областью может быть проблематичной, так как экземпляр является общим для компонентов в канале пользователя.</span><span class="sxs-lookup"><span data-stu-id="d35e5-126">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="d35e5-127"><xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования.</span><span class="sxs-lookup"><span data-stu-id="d35e5-127"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="d35e5-128">Существующие времена существования не подходят по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="d35e5-128">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="d35e5-129">**Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.</span><span class="sxs-lookup"><span data-stu-id="d35e5-129">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="d35e5-130">**С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.</span><span class="sxs-lookup"><span data-stu-id="d35e5-130">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="d35e5-131">**Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.</span><span class="sxs-lookup"><span data-stu-id="d35e5-131">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="d35e5-132">Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d35e5-132">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="d35e5-133">По умолчанию рассмотрите возможность использования одного контекста для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="d35e5-133">By default, consider using one context per operation.</span></span> <span data-ttu-id="d35e5-134">Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.</span><span class="sxs-lookup"><span data-stu-id="d35e5-134">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="d35e5-135">Используйте флаг для предотвращения нескольких одновременных операций.</span><span class="sxs-lookup"><span data-stu-id="d35e5-135">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="d35e5-136">Размещайте операции после строки `Loading = true;` в блоке `try`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-136">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="d35e5-137">Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="d35e5-137">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="d35e5-138">Новые экземпляры DbContext</span><span class="sxs-lookup"><span data-stu-id="d35e5-138">New DbContext instances</span></span></h3>

<span data-ttu-id="d35e5-139">Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-139">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="d35e5-140">Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d35e5-140">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="d35e5-141">Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="d35e5-141">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="d35e5-142">Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики.</span><span class="sxs-lookup"><span data-stu-id="d35e5-142">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="d35e5-143">EF Core 5.0 и более поздние версии предоставляют встроенную фабрику для создания новых контекстов.</span><span class="sxs-lookup"><span data-stu-id="d35e5-143">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="d35e5-144">В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных.</span><span class="sxs-lookup"><span data-stu-id="d35e5-144">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="d35e5-145">В этом коде используется [метод расширения (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d35e5-145">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="d35e5-146">Фабрика внедряется в компоненты и используется для создания новых экземпляров.</span><span class="sxs-lookup"><span data-stu-id="d35e5-146">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="d35e5-147">Например, для `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="d35e5-147">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="d35e5-148">`Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-148">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="d35e5-149">См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="d35e5-149">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="d35e5-150">Новые экземпляры <xref:Microsoft.EntityFrameworkCore.DbContext> можно создать с помощью фабрики, которая позволяет настроить строку подключения для каждого экземпляра `DbContext`, например при использовании модели [ Identity ASP.NET Core](xref:security/authentication/customize_identity_model).</span><span class="sxs-lookup"><span data-stu-id="d35e5-150">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="d35e5-151">Область действия на время существования компонента</span><span class="sxs-lookup"><span data-stu-id="d35e5-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="d35e5-152">Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="d35e5-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="d35e5-153">Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="d35e5-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="d35e5-154">Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="d35e5-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="d35e5-155">Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="d35e5-156">Пример приложения обеспечивает удаление контекста при удалении компонента.</span><span class="sxs-lookup"><span data-stu-id="d35e5-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="d35e5-157">Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста.</span><span class="sxs-lookup"><span data-stu-id="d35e5-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="d35e5-158">В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.</span><span class="sxs-lookup"><span data-stu-id="d35e5-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="d35e5-159">Включение ведения журнала для конфиденциальных данных</span><span class="sxs-lookup"><span data-stu-id="d35e5-159">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="d35e5-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> позволяет включить данные приложения в сообщения об исключениях и журналы платформы.</span><span class="sxs-lookup"><span data-stu-id="d35e5-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="d35e5-161">Записанные в журнал данные могут содержать значения, присвоенные свойствам экземпляров сущностей, и значения параметров для команд, отправляемых в базу данных.</span><span class="sxs-lookup"><span data-stu-id="d35e5-161">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="d35e5-162">Использовать <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> для записи данных в журнал **небезопасно**, так как этот метод может раскрывать пароли и другие личные сведения (PII) при записи инструкций SQL, выполняемых в базе данных.</span><span class="sxs-lookup"><span data-stu-id="d35e5-162">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="d35e5-163">Мы рекомендуем включать метод <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> только на этапах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="d35e5-163">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="d35e5-164">Blazor Server — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="d35e5-164">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="d35e5-165">Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="d35e5-165">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="d35e5-166">Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="d35e5-166">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="d35e5-167">Для уникальной модели приложения, которую предоставляет Blazor Server, требуется специальный подход к использованию Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d35e5-167">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d35e5-168">В этой статье рассматривается EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d35e5-168">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="d35e5-169">Приложения Blazor WebAssembly выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных.</span><span class="sxs-lookup"><span data-stu-id="d35e5-169">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="d35e5-170">Запуск EF Core в Blazor WebAssembly выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="d35e5-170">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="d35e5-171">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="d35e5-171">Sample app</span></span></h2>

<span data-ttu-id="d35e5-172">Пример приложения был создан в виде справочника для приложений Blazor Server, использующих EF Core.</span><span class="sxs-lookup"><span data-stu-id="d35e5-172">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="d35e5-173">Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления.</span><span class="sxs-lookup"><span data-stu-id="d35e5-173">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="d35e5-174">В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.</span><span class="sxs-lookup"><span data-stu-id="d35e5-174">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="d35e5-175">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d35e5-175">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d35e5-176">В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе.</span><span class="sxs-lookup"><span data-stu-id="d35e5-176">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="d35e5-177">В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL.</span><span class="sxs-lookup"><span data-stu-id="d35e5-177">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="d35e5-178">Это настраивается в `appsettings.Development.json`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-178">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="d35e5-179">Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="d35e5-179">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="d35e5-180">Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="d35e5-180">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="d35e5-181">Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны.</span><span class="sxs-lookup"><span data-stu-id="d35e5-181">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="d35e5-182">Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров Razor, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="d35e5-182">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="d35e5-183">Доступ к базе данных</span><span class="sxs-lookup"><span data-stu-id="d35e5-183">Database access</span></span></h2>

<span data-ttu-id="d35e5-184">EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="d35e5-184">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="d35e5-185">EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*.</span><span class="sxs-lookup"><span data-stu-id="d35e5-185">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="d35e5-186">В приложениях Blazor Server это может быть проблематично, так как экземпляр является общим для компонентов в канале пользователя.</span><span class="sxs-lookup"><span data-stu-id="d35e5-186">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="d35e5-187"><xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования.</span><span class="sxs-lookup"><span data-stu-id="d35e5-187"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="d35e5-188">Существующие времена существования не подходят по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="d35e5-188">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="d35e5-189">**Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.</span><span class="sxs-lookup"><span data-stu-id="d35e5-189">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="d35e5-190">**С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.</span><span class="sxs-lookup"><span data-stu-id="d35e5-190">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="d35e5-191">**Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.</span><span class="sxs-lookup"><span data-stu-id="d35e5-191">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="d35e5-192">Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d35e5-192">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="d35e5-193">По умолчанию рассмотрите возможность использования одного контекста для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="d35e5-193">By default, consider using one context per operation.</span></span> <span data-ttu-id="d35e5-194">Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.</span><span class="sxs-lookup"><span data-stu-id="d35e5-194">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="d35e5-195">Используйте флаг для предотвращения нескольких одновременных операций.</span><span class="sxs-lookup"><span data-stu-id="d35e5-195">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="d35e5-196">Размещайте операции после строки `Loading = true;` в блоке `try`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-196">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="d35e5-197">Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="d35e5-197">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="d35e5-198">Новые экземпляры DbContext</span><span class="sxs-lookup"><span data-stu-id="d35e5-198">New DbContext instances</span></span></h3>

<span data-ttu-id="d35e5-199">Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-199">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="d35e5-200">Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d35e5-200">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="d35e5-201">Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="d35e5-201">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="d35e5-202">Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики.</span><span class="sxs-lookup"><span data-stu-id="d35e5-202">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="d35e5-203">Пример приложения реализует собственную фабрику в `Data/DbContextFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-203">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="d35e5-204">В предыдущей фабрике:</span><span class="sxs-lookup"><span data-stu-id="d35e5-204">In the preceding factory:</span></span>

* <span data-ttu-id="d35e5-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> удовлетворяет все зависимости через поставщика услуг;</span><span class="sxs-lookup"><span data-stu-id="d35e5-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="d35e5-206">`IDbContextFactory` доступен в EF Core ASP.NET Core 5.0 или более поздней версии, поэтому интерфейс [реализован в примере приложения для ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="d35e5-206">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="d35e5-207">В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных.</span><span class="sxs-lookup"><span data-stu-id="d35e5-207">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="d35e5-208">Этот код использует метод расширения для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d35e5-208">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="d35e5-209">Фабрика внедряется в компоненты и используется для создания новых экземпляров.</span><span class="sxs-lookup"><span data-stu-id="d35e5-209">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="d35e5-210">Например, для `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="d35e5-210">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="d35e5-211">`Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-211">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="d35e5-212">См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="d35e5-212">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="d35e5-213">Новые экземпляры <xref:Microsoft.EntityFrameworkCore.DbContext> можно создать с помощью фабрики, которая позволяет настроить строку подключения для каждого экземпляра `DbContext`, например при использовании [модели Identity ASP.NET Core](xref:security/authentication/customize_identity_model):</span><span class="sxs-lookup"><span data-stu-id="d35e5-213">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="d35e5-214">Область действия на время существования компонента</span><span class="sxs-lookup"><span data-stu-id="d35e5-214">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="d35e5-215">Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="d35e5-215">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="d35e5-216">Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="d35e5-216">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="d35e5-217">Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="d35e5-217">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="d35e5-218">Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-218">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="d35e5-219">Пример приложения обеспечивает удаление контекста при удалении компонента.</span><span class="sxs-lookup"><span data-stu-id="d35e5-219">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="d35e5-220">Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста.</span><span class="sxs-lookup"><span data-stu-id="d35e5-220">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="d35e5-221">В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.</span><span class="sxs-lookup"><span data-stu-id="d35e5-221">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="d35e5-222">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="d35e5-222">In the preceding example:</span></span>

* <span data-ttu-id="d35e5-223">Если `Busy` имеет значение `true`, могут начинаться асинхронные операции.</span><span class="sxs-lookup"><span data-stu-id="d35e5-223">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="d35e5-224">Если `Busy` возвращается в значение `false`, асинхронные операции должны быть завершены.</span><span class="sxs-lookup"><span data-stu-id="d35e5-224">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="d35e5-225">Разместите дополнительную логику обработки ошибок в блоке `catch`.</span><span class="sxs-lookup"><span data-stu-id="d35e5-225">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="d35e5-226">Включение ведения журнала для конфиденциальных данных</span><span class="sxs-lookup"><span data-stu-id="d35e5-226">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="d35e5-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> позволяет включить данные приложения в сообщения об исключениях и журналы платформы.</span><span class="sxs-lookup"><span data-stu-id="d35e5-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="d35e5-228">Записанные в журнал данные могут содержать значения, присвоенные свойствам экземпляров сущностей, и значения параметров для команд, отправляемых в базу данных.</span><span class="sxs-lookup"><span data-stu-id="d35e5-228">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="d35e5-229">Использовать <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> для записи данных в журнал **небезопасно**, так как этот метод может раскрывать пароли и другие личные сведения (PII) при записи инструкций SQL, выполняемых в базе данных.</span><span class="sxs-lookup"><span data-stu-id="d35e5-229">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="d35e5-230">Мы рекомендуем включать метод <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> только на этапах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="d35e5-230">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d35e5-231">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d35e5-231">Additional resources</span></span>

* [<span data-ttu-id="d35e5-232">Документация по EF Core</span><span class="sxs-lookup"><span data-stu-id="d35e5-232">EF Core documentation</span></span>](/ef/)
