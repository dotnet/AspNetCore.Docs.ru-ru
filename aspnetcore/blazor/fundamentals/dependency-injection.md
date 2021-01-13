---
title: Внедрение зависимостей Blazor в ASP.NET Core
author: guardrex
description: Подробные сведения о том, как приложения Blazor могут внедрять службы в компоненты.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3f2b4eff5422acbec80b2fd9b801101271cc3f75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97808729"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="e012e-103">Внедрение зависимостей Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e012e-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="e012e-104">Авторы: [Райнер Стропек](https://www.timecockpit.com) (Rainer Stropek) и [Майк Роусос](https://github.com/mjrousos) (Mike Rousos)</span><span class="sxs-lookup"><span data-stu-id="e012e-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="e012e-105">[Внедрение зависимостей](xref:fundamentals/dependency-injection) — это методика доступа к службам, настроенным в центральном расположении.</span><span class="sxs-lookup"><span data-stu-id="e012e-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="e012e-106">Зарегистрированные на платформе службы можно внедрять непосредственно в компоненты приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="e012e-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="e012e-107">Приложения Blazor определяют и регистрируют пользовательские службы и предоставляют к ним доступ в рамках всего приложения с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e012e-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="e012e-108">Службы по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e012e-108">Default services</span></span>

<span data-ttu-id="e012e-109">В таблице ниже представлены службы, часто используемые в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="e012e-109">The services shown the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="e012e-110">Служба</span><span class="sxs-lookup"><span data-stu-id="e012e-110">Service</span></span> | <span data-ttu-id="e012e-111">Время существования</span><span class="sxs-lookup"><span data-stu-id="e012e-111">Lifetime</span></span> | <span data-ttu-id="e012e-112">Описание</span><span class="sxs-lookup"><span data-stu-id="e012e-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="e012e-113">Область действия</span><span class="sxs-lookup"><span data-stu-id="e012e-113">Scoped</span></span> | <p><span data-ttu-id="e012e-114">Предоставляет методы для отправки HTTP-запросов и получения HTTP-ответов от ресурса с заданным URI.</span><span class="sxs-lookup"><span data-stu-id="e012e-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="e012e-115">Экземпляр <xref:System.Net.Http.HttpClient> в приложении Blazor WebAssembly использует браузер для обработки HTTP-трафика в фоновом режиме.</span><span class="sxs-lookup"><span data-stu-id="e012e-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="e012e-116">Приложения Blazor Server не включают клиент <xref:System.Net.Http.HttpClient>, настроенный в качестве службы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e012e-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="e012e-117">Предоставьте <xref:System.Net.Http.HttpClient> приложению Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="e012e-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="e012e-118">Для получения дополнительной информации см. <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="e012e-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="e012e-119"><xref:System.Net.Http.HttpClient> регистрируется как служба с заданной областью, а не как singleton.</span><span class="sxs-lookup"><span data-stu-id="e012e-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="e012e-120">Дополнительные сведения см. в разделе [Время существования службы](#service-lifetime).</span><span class="sxs-lookup"><span data-stu-id="e012e-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="e012e-121">**Blazor WebAssembly** . Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="e012e-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="e012e-122">**Blazor Server** : Область действия</span><span class="sxs-lookup"><span data-stu-id="e012e-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="e012e-123">Представляет экземпляр среды выполнения JavaScript, в которую отправляются вызовы JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e012e-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="e012e-124">Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="e012e-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="e012e-125">**Blazor WebAssembly** . Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="e012e-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="e012e-126">**Blazor Server** : Область действия</span><span class="sxs-lookup"><span data-stu-id="e012e-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="e012e-127">Содержит вспомогательные методы для работы с URI и состоянием навигации.</span><span class="sxs-lookup"><span data-stu-id="e012e-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="e012e-128">Дополнительные сведения см. в разделе [URI и вспомогательные инструменты состояния навигации](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="e012e-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="e012e-129">Пользовательский поставщик услуг не предоставляет перечисленные в таблице службы по умолчанию автоматически.</span><span class="sxs-lookup"><span data-stu-id="e012e-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="e012e-130">Если вы используете пользовательский поставщик услуг и нуждаетесь в какой-либо из служб, указанных в таблице, добавьте необходимые службы в новый поставщик услуг.</span><span class="sxs-lookup"><span data-stu-id="e012e-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="e012e-131">Добавление служб в приложение</span><span class="sxs-lookup"><span data-stu-id="e012e-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="e012e-132">Настройте службы для коллекции служб приложения в методе `Program.Main` файла `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="e012e-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="e012e-133">В следующем примере реализация `MyDependency` зарегистрирована для `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="e012e-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

<span data-ttu-id="e012e-134">После сборки узла доступ к службам можно получить из корневой области внедрения зависимостей до отрисовки каких-либо компонентов.</span><span class="sxs-lookup"><span data-stu-id="e012e-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="e012e-135">Это может быть удобно для запуска логики инициализации перед отрисовкой содержимого:</span><span class="sxs-lookup"><span data-stu-id="e012e-135">This can be useful for running initialization logic before rendering content:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

<span data-ttu-id="e012e-136">Узел предоставляет центральный экземпляр конфигурации для приложения.</span><span class="sxs-lookup"><span data-stu-id="e012e-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="e012e-137">Основываясь на предыдущем примере, URL-адрес службы погоды передается из источника конфигурации по умолчанию (например, `appsettings.json`) в `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="e012e-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

<span data-ttu-id="e012e-138">После создания приложения изучите метод `Startup.ConfigureServices` в `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="e012e-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="e012e-139">В метод <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> передается <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, представляющий собой список объектов [дескриптора службы](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor).</span><span class="sxs-lookup"><span data-stu-id="e012e-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="e012e-140">Службы добавляются в метод `ConfigureServices` путем предоставления дескрипторов служб в коллекцию служб.</span><span class="sxs-lookup"><span data-stu-id="e012e-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="e012e-141">В следующем примере показана концепция с интерфейсом `IDataAccess` и его конкретной реализацией `DataAccess`.</span><span class="sxs-lookup"><span data-stu-id="e012e-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="e012e-142">Время существования службы</span><span class="sxs-lookup"><span data-stu-id="e012e-142">Service lifetime</span></span>

<span data-ttu-id="e012e-143">Для служб можно настроить параметры времени существования, указанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="e012e-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="e012e-144">Время существования</span><span class="sxs-lookup"><span data-stu-id="e012e-144">Lifetime</span></span> | <span data-ttu-id="e012e-145">Описание</span><span class="sxs-lookup"><span data-stu-id="e012e-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="e012e-146">Сейчас в приложениях Blazor WebAssembly концепция областей внедрения зависимостей отсутствует.</span><span class="sxs-lookup"><span data-stu-id="e012e-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="e012e-147">Службы, зарегистрированные как `Scoped`, работают аналогично службам `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="e012e-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="e012e-148">Модель размещения Blazor Server поддерживает время существования `Scoped` в HTTP-запросах, но не в сообщениях о соединении и создании канала SignalR между компонентами, загруженными на клиенте.</span><span class="sxs-lookup"><span data-stu-id="e012e-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SignalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="e012e-149">Razor Pages или MVC-часть приложения взаимодействует со службами с заданной областью в обычном режиме и воссоздает службы для *каждого HTTP-запроса* при переходе между страницами или представлениями либо со страницы или из представления в компонент.</span><span class="sxs-lookup"><span data-stu-id="e012e-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="e012e-150">Службы с заданной областью не воссоздаются при переходе между компонентами на клиенте, где обмен данными с сервером происходит через SignalR-подключение по каналу пользователя, а не через HTTP-запросы.</span><span class="sxs-lookup"><span data-stu-id="e012e-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="e012e-151">В следующих сценариях использования компонентов на клиенте службы с заданной областью воссоздаются, так как для пользователя создается новый канал:</span><span class="sxs-lookup"><span data-stu-id="e012e-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="e012e-152">Пользователь закрывает окно браузера.</span><span class="sxs-lookup"><span data-stu-id="e012e-152">The user closes the browser's window.</span></span> <span data-ttu-id="e012e-153">Пользователь открывает новое окно и снова переходит к приложению.</span><span class="sxs-lookup"><span data-stu-id="e012e-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="e012e-154">Пользователь закрывает последнюю вкладку приложения в окне браузера.</span><span class="sxs-lookup"><span data-stu-id="e012e-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="e012e-155">Пользователь открывает новую вкладку и снова переходит к приложению.</span><span class="sxs-lookup"><span data-stu-id="e012e-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="e012e-156">Пользователь нажимает кнопку перезагрузки или обновления в браузере.</span><span class="sxs-lookup"><span data-stu-id="e012e-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="e012e-157">Дополнительные сведения о сохранении состояния пользователей в службах с областью действия в Blazor Server приложениях см. в разделе <xref:blazor/hosting-models?pivots=server>.</span><span class="sxs-lookup"><span data-stu-id="e012e-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="e012e-158">Система внедрения зависимостей создает *один экземпляр* службы.</span><span class="sxs-lookup"><span data-stu-id="e012e-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="e012e-159">Все компоненты, для которых необходима служба `Singleton`, получают экземпляр той же службы.</span><span class="sxs-lookup"><span data-stu-id="e012e-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="e012e-160">Каждый раз, когда компонент получает экземпляр службы `Transient` из контейнера службы, он получает *новый экземпляр* этой службы.</span><span class="sxs-lookup"><span data-stu-id="e012e-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="e012e-161">Система внедрения зависимостей основана на системе внедрения зависимостей в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e012e-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="e012e-162">Для получения дополнительной информации см. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="e012e-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="e012e-163">Запрос службы в компоненте</span><span class="sxs-lookup"><span data-stu-id="e012e-163">Request a service in a component</span></span>

<span data-ttu-id="e012e-164">После добавления служб в коллекцию служб внедрите службы в компоненты с помощью директивы [`@inject`](xref:mvc/views/razor#inject) Razor, которая имеет два параметра:</span><span class="sxs-lookup"><span data-stu-id="e012e-164">After services are added to the service collection, inject the services into the components using the [`@inject`](xref:mvc/views/razor#inject) Razor directive, which has two parameters:</span></span>

* <span data-ttu-id="e012e-165">Тип: тип внедряемой службы.</span><span class="sxs-lookup"><span data-stu-id="e012e-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="e012e-166">Свойство: имя свойства, получающего внедренную службу приложений.</span><span class="sxs-lookup"><span data-stu-id="e012e-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="e012e-167">Свойство не требуется создавать вручную.</span><span class="sxs-lookup"><span data-stu-id="e012e-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="e012e-168">Его создает компилятор.</span><span class="sxs-lookup"><span data-stu-id="e012e-168">The compiler creates the property.</span></span>

<span data-ttu-id="e012e-169">Для получения дополнительной информации см. <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="e012e-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="e012e-170">Используйте несколько инструкций [`@inject`](xref:mvc/views/razor#inject) для внедрения различных служб.</span><span class="sxs-lookup"><span data-stu-id="e012e-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="e012e-171">В следующем примере показано, как использовать [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="e012e-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="e012e-172">Служба, реализующая `Services.IDataAccess`, внедряется в свойство `DataRepository` компонента.</span><span class="sxs-lookup"><span data-stu-id="e012e-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="e012e-173">Обратите внимание, что код использует только абстракцию `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="e012e-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="e012e-174">На внутреннем уровне создаваемое свойство (`DataRepository`) использует атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="e012e-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="e012e-175">Как правило, этот атрибут не используется напрямую.</span><span class="sxs-lookup"><span data-stu-id="e012e-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="e012e-176">Если базовый класс необходим для компонентов, а для базового класса также требуются обязательные свойства, добавьте атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) вручную:</span><span class="sxs-lookup"><span data-stu-id="e012e-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="e012e-177">В компонентах, производных от базового класса, директива [`@inject`](xref:mvc/views/razor#inject) не требуется.</span><span class="sxs-lookup"><span data-stu-id="e012e-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="e012e-178"><xref:Microsoft.AspNetCore.Components.InjectAttribute> базового класса достаточно:</span><span class="sxs-lookup"><span data-stu-id="e012e-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="e012e-179">Использование внедрения зависимостей в службах</span><span class="sxs-lookup"><span data-stu-id="e012e-179">Use DI in services</span></span>

<span data-ttu-id="e012e-180">Для сложных служб могут потребоваться дополнительные службы.</span><span class="sxs-lookup"><span data-stu-id="e012e-180">Complex services might require additional services.</span></span> <span data-ttu-id="e012e-181">В приведенном ниже примере для `DataAccess` требуется служба <xref:System.Net.Http.HttpClient> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e012e-181">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="e012e-182">[`@inject`](xref:mvc/views/razor#inject) (или атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) невозможно использовать в службах.</span><span class="sxs-lookup"><span data-stu-id="e012e-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="e012e-183">Вместо этого следует использовать *внедрения конструктора*.</span><span class="sxs-lookup"><span data-stu-id="e012e-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="e012e-184">Необходимые службы добавляются путем добавления параметров в конструктор службы.</span><span class="sxs-lookup"><span data-stu-id="e012e-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="e012e-185">Когда система внедрения зависимостей создает службу, она распознает необходимые службы в конструкторе и предоставляет их соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="e012e-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="e012e-186">В следующем примере конструктор получает <xref:System.Net.Http.HttpClient> через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e012e-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="e012e-187"><xref:System.Net.Http.HttpClient> — это служба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e012e-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="e012e-188">Необходимые условия для внедрения конструктора:</span><span class="sxs-lookup"><span data-stu-id="e012e-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="e012e-189">Должен существовать один конструктор, аргументы которого могут использоваться системой внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e012e-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="e012e-190">Дополнительные параметры, не охваченные системой внедрения зависимостей, разрешены, если они указывают значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e012e-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="e012e-191">Применимый конструктор должен быть `public`.</span><span class="sxs-lookup"><span data-stu-id="e012e-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="e012e-192">Должен существовать один подходящий конструктор.</span><span class="sxs-lookup"><span data-stu-id="e012e-192">One applicable constructor must exist.</span></span> <span data-ttu-id="e012e-193">В случае неоднозначности система внедрения зависимостей выдает исключение.</span><span class="sxs-lookup"><span data-stu-id="e012e-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="e012e-194">Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="e012e-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="e012e-195">В приложениях ASP.NET Core службы с заданной областью обычно ограничены текущим запросом.</span><span class="sxs-lookup"><span data-stu-id="e012e-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="e012e-196">По завершении запроса все временные службы или службы с заданной областью удаляются системой внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e012e-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="e012e-197">В приложениях Blazor Server область запроса существует на протяжении всего клиентского подключения. Это может привести к тому, что временные службы или службы с заданной областью будут работать намного дольше, чем ожидалось.</span><span class="sxs-lookup"><span data-stu-id="e012e-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="e012e-198">В приложениях Blazor WebAssembly службы, зарегистрированные с ограниченным временем существования, рассматриваются как singleton, поэтому они существуют дольше, чем службы с заданной областью в типичных приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e012e-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="e012e-199">См. раздел [Обнаружение высвобождаемых временных служб](#detect-transient-disposables).</span><span class="sxs-lookup"><span data-stu-id="e012e-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="e012e-200">Подход, ограничивающий время существования службы в приложениях Blazor, заключается в использовании типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="e012e-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="e012e-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактным типом, производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>, который создает область внедрения зависимостей, соответствующую времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="e012e-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="e012e-202">Используя эту область, можно использовать службы внедрения зависимостей с ограниченным временем существования, заставляя их существовать так же долго, как и компонент.</span><span class="sxs-lookup"><span data-stu-id="e012e-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="e012e-203">При удалении компонента также удаляются и службы из поставщика служб с заданной областью действия этого компонента.</span><span class="sxs-lookup"><span data-stu-id="e012e-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="e012e-204">Это может быть полезно для служб, которые:</span><span class="sxs-lookup"><span data-stu-id="e012e-204">This can be useful for services that:</span></span>

* <span data-ttu-id="e012e-205">требуется повторно использовать в компоненте, так как временное существование не подходит;</span><span class="sxs-lookup"><span data-stu-id="e012e-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="e012e-206">не должны совместно использоваться компонентами, так как одноэлементное время существования не подходит.</span><span class="sxs-lookup"><span data-stu-id="e012e-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="e012e-207">Доступны две версии типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>:</span><span class="sxs-lookup"><span data-stu-id="e012e-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="e012e-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактной высвобождаемой дочерней версией типа <xref:Microsoft.AspNetCore.Components.ComponentBase> с защищенным свойством <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> типа <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="e012e-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="e012e-209">Этот поставщик можно использовать для разрешения служб, ограниченных временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="e012e-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="e012e-210">Службы внедрения зависимостей, внедренные в компонент с помощью атрибута [`@inject`](xref:mvc/views/razor#inject) или [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute), не создаются в области компонента.</span><span class="sxs-lookup"><span data-stu-id="e012e-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="e012e-211">Чтобы использовать область компонента, необходимо разрешить службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> или <xref:System.IServiceProvider.GetService%2A>.</span><span class="sxs-lookup"><span data-stu-id="e012e-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="e012e-212">Все службы, разрешенные с помощью поставщика <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>, имеют свои зависимости из этой же области.</span><span class="sxs-lookup"><span data-stu-id="e012e-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <span data-ttu-id="e012e-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> является производным от <xref:Microsoft.AspNetCore.Components.OwningComponentBase> и добавляет свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A>, которое возвращает экземпляр `T` из поставщика внедрения зависимостей с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="e012e-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="e012e-214">Этот тип удобен для доступа к службам с заданной областью без использования экземпляра <xref:System.IServiceProvider> при наличии одной основной службы, которую приложение запрашивает из контейнера внедрения зависимостей с использованием области компонента.</span><span class="sxs-lookup"><span data-stu-id="e012e-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="e012e-215">Свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> доступно, поэтому при необходимости приложение может получить службы других типов.</span><span class="sxs-lookup"><span data-stu-id="e012e-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="e012e-216">Использование DbContext Entity Framework Core (EF Core) из внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="e012e-216">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="e012e-217">Дополнительные сведения см. в разделе <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="e012e-217">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="e012e-218">Обнаружение высвобождаемых временных служб</span><span class="sxs-lookup"><span data-stu-id="e012e-218">Detect transient disposables</span></span>

<span data-ttu-id="e012e-219">В следующих примерах показано, как обнаружить высвобождаемые временные службы в приложении, которые должны использовать <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="e012e-219">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="e012e-220">См. раздел [Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей](#utility-base-component-classes-to-manage-a-di-scope).</span><span class="sxs-lookup"><span data-stu-id="e012e-220">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="e012e-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="e012e-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="e012e-222">В следующем примере обнаруживается `TransientDisposable` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="e012e-222">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end 

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="e012e-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="e012e-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="e012e-224">Добавьте пространство имен для <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> в `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="e012e-224">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="e012e-225">В методе `Program.CreateHostBuilder` в файле `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="e012e-225">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

<span data-ttu-id="e012e-226">В следующем примере обнаруживается `TransientDependency` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="e012e-226">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

<span data-ttu-id="e012e-227">Приложение может регистрировать временные высвобождаемые службы, не вызывая исключение.</span><span class="sxs-lookup"><span data-stu-id="e012e-227">The app can register transient disposables without throwing an exception.</span></span> <span data-ttu-id="e012e-228">При этом попытка разрешить временную высвобождаемую службу приведет к <xref:System.InvalidOperationException>, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="e012e-228">However, attempting to resolve a transient disposable results in an <xref:System.InvalidOperationException>, as the following example shows.</span></span>

<span data-ttu-id="e012e-229">`Pages/TransientDisposable.razor`:</span><span class="sxs-lookup"><span data-stu-id="e012e-229">`Pages/TransientDisposable.razor`:</span></span>

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

<span data-ttu-id="e012e-230">Перейдите к компоненту `TransientDisposable` в `/transient-disposable`. Когда платформа попытается создать экземпляр `TransientDisposable`, возникнет исключение <xref:System.InvalidOperationException>:</span><span class="sxs-lookup"><span data-stu-id="e012e-230">Navigate to the `TransientDisposable` component at `/transient-disposable` and an <xref:System.InvalidOperationException> is thrown when the framework attempts to construct an instance of `TransientDisposable`:</span></span>

> <span data-ttu-id="e012e-231">"System.InvalidOperationException: Trying to resolve transient disposable service TransientDisposable in the wrong scope.</span><span class="sxs-lookup"><span data-stu-id="e012e-231">System.InvalidOperationException: Trying to resolve transient disposable service TransientDisposable in the wrong scope.</span></span> <span data-ttu-id="e012e-232">Use an 'OwningComponentBase\<T>' component base class for the service 'T' you are trying to resolve.</span><span class="sxs-lookup"><span data-stu-id="e012e-232">Use an 'OwningComponentBase\<T>' component base class for the service 'T' you are trying to resolve.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e012e-233">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e012e-233">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="e012e-234">Руководство по применению временных и общих экземпляров `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="e012e-234">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
