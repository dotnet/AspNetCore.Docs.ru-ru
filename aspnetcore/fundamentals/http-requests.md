---
title: Выполнения HTTP-запросов с помощью IHttpClientFactory в ASP.NET Core
author: stevejgordon
description: Сведения об использовании интерфейса IHttpClientFactory для управления логическими экземплярами HttpClient в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 1/21/2021
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
uid: fundamentals/http-requests
ms.openlocfilehash: 1cf3029452f87a396847f969f0f3136a75874752
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057334"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="ad943-103">Выполнения HTTP-запросов с помощью IHttpClientFactory в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad943-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ad943-104">Авторы [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin), [Стив Гордон](https://github.com/stevejgordon) (Steve Gordon), [Гленн Кондрон](https://github.com/glennc) (Glenn Condron) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak).</span><span class="sxs-lookup"><span data-stu-id="ad943-104">By [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc), and [Ryan Nowak](https://github.com/rynowak).</span></span>

<span data-ttu-id="ad943-105"><xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad943-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="ad943-106">`IHttpClientFactory` предоставляет следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="ad943-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="ad943-107">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ad943-108">Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="ad943-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="ad943-109">Можно зарегистрировать клиент по умолчанию для общего доступа.</span><span class="sxs-lookup"><span data-stu-id="ad943-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="ad943-110">Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="ad943-111">Предоставление расширений для ПО промежуточного слоя на основе Polly для делегирования обработчиков в `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="ad943-112">Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="ad943-113">Автоматическое управление позволяет избежать обычных проблем со службой доменных имен (DNS), которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="ad943-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ad943-114">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="ad943-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ad943-115">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ad943-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ad943-116">Пример кода в этой версии раздела использует <xref:System.Text.Json> для десериализации содержимого JSON, возвращаемого в ответах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad943-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="ad943-117">Для примеров, использующих `Json.NET` и `ReadAsAsync<T>`, воспользуйтесь средством выбора версии, чтобы выбрать версию 2.x этого раздела.</span><span class="sxs-lookup"><span data-stu-id="ad943-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="ad943-118">Принципы использования</span><span class="sxs-lookup"><span data-stu-id="ad943-118">Consumption patterns</span></span>

<span data-ttu-id="ad943-119">Существует несколько способов использования `IHttpClientFactory` в приложении:</span><span class="sxs-lookup"><span data-stu-id="ad943-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="ad943-120">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad943-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="ad943-121">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="ad943-122">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="ad943-123">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="ad943-124">Оптимальный подход зависит от требований приложения.</span><span class="sxs-lookup"><span data-stu-id="ad943-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="ad943-125">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad943-125">Basic usage</span></span>

<span data-ttu-id="ad943-126">`IHttpClientFactory` можно зарегистрировать, вызвав `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad943-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="ad943-127">`IHttpClientFactory` можно запросить с помощью [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ad943-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ad943-128">Следующий код использует `IHttpClientFactory` для создания экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad943-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="ad943-129">Подобное использование `IHttpClientFactory` — это хороший способ рефакторинга имеющегося приложения.</span><span class="sxs-lookup"><span data-stu-id="ad943-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="ad943-130">Он не оказывает влияния на использование `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="ad943-131">Там, где в существующем приложении создаются экземпляры `HttpClient`, используйте вызовы к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="ad943-132">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-132">Named clients</span></span>

<span data-ttu-id="ad943-133">Именованные клиенты являются хорошим выбором в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="ad943-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="ad943-134">Приложение требует много отдельных использований `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="ad943-135">Многие `HttpClient` имеют другую конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="ad943-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="ad943-136">Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ad943-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="ad943-137">В приведенном выше коде клиент регистрируется с:</span><span class="sxs-lookup"><span data-stu-id="ad943-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="ad943-138">базовым адресом `https://api.github.com/`;</span><span class="sxs-lookup"><span data-stu-id="ad943-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="ad943-139">двумя заголовками, необходимыми для работы с API GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad943-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="ad943-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="ad943-140">CreateClient</span></span>

<span data-ttu-id="ad943-141">При каждом вызове <xref:System.Net.Http.IHttpClientFactory.CreateClient*>:</span><span class="sxs-lookup"><span data-stu-id="ad943-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="ad943-142">создается новый экземпляр `HttpClient`;</span><span class="sxs-lookup"><span data-stu-id="ad943-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="ad943-143">вызывается действие настройки.</span><span class="sxs-lookup"><span data-stu-id="ad943-143">The configuration action is called.</span></span>

<span data-ttu-id="ad943-144">Чтобы создать именованный клиент, передайте его имя в `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="ad943-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ad943-145">В приведенном выше коде в запросе не требуется указывать имя узла.</span><span class="sxs-lookup"><span data-stu-id="ad943-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="ad943-146">Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="ad943-147">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-147">Typed clients</span></span>

<span data-ttu-id="ad943-148">Типизированные клиенты:</span><span class="sxs-lookup"><span data-stu-id="ad943-148">Typed clients:</span></span>

* <span data-ttu-id="ad943-149">предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.</span><span class="sxs-lookup"><span data-stu-id="ad943-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="ad943-150">Это помогает IntelliSense и компилятору при использовании клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad943-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="ad943-151">Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="ad943-152">Например, один типизированный клиент можно использовать:</span><span class="sxs-lookup"><span data-stu-id="ad943-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="ad943-153">для одной серверной конечной точки;</span><span class="sxs-lookup"><span data-stu-id="ad943-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="ad943-154">для инкапсуляции всей логики, связанной с конечной точкой.</span><span class="sxs-lookup"><span data-stu-id="ad943-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="ad943-155">Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad943-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="ad943-156">Типизированный клиент принимает параметр `HttpClient` в конструкторе:</span><span class="sxs-lookup"><span data-stu-id="ad943-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ad943-157">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="ad943-157">In the preceding code:</span></span>

* <span data-ttu-id="ad943-158">Конфигурация перемещается в типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="ad943-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="ad943-159">Объект `HttpClient` предоставляется в виде открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="ad943-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="ad943-160">Можно создать связанные с API методы, которые предоставляют функциональные возможности `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="ad943-161">Например, метод `GetAspNetDocsIssues` инкапсулирует код для получения открытых вопросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="ad943-162">Следующий код вызывает <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices` для регистрации класса типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="ad943-163">Типизированный клиент регистрируется во внедрении зависимостей как временный.</span><span class="sxs-lookup"><span data-stu-id="ad943-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="ad943-164">В приведенном выше коде `AddHttpClient` регистрирует `GitHubService` как временную службу.</span><span class="sxs-lookup"><span data-stu-id="ad943-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="ad943-165">Эта регистрация использует фабричный метод для следующих задач:</span><span class="sxs-lookup"><span data-stu-id="ad943-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="ad943-166">Создание экземпляра `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="ad943-167">Создайте экземпляр `GitHubService`, передав его конструктору экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="ad943-168">Типизированный клиент можно внедрить и использовать напрямую:</span><span class="sxs-lookup"><span data-stu-id="ad943-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="ad943-169">Конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="ad943-170">`HttpClient` может быть инкапсулирован в типизированном клиенте.</span><span class="sxs-lookup"><span data-stu-id="ad943-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="ad943-171">Вместо предоставления его как свойства определите метод для внутреннего вызова экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad943-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="ad943-172">В приведенном выше коде `HttpClient` хранится в закрытом поле.</span><span class="sxs-lookup"><span data-stu-id="ad943-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="ad943-173">Доступ к `HttpClient` осуществляется с помощью общедоступного метода `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="ad943-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="ad943-174">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-174">Generated clients</span></span>

<span data-ttu-id="ad943-175">`IHttpClientFactory` можно использовать в сочетании с библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="ad943-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="ad943-176">Refit — это библиотека REST для .NET.</span><span class="sxs-lookup"><span data-stu-id="ad943-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="ad943-177">Она преобразует REST API в динамические интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="ad943-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="ad943-178">Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad943-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="ad943-179">Для представления внешнего API и его ответа определяются интерфейс и ответ:</span><span class="sxs-lookup"><span data-stu-id="ad943-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="ad943-180">Можно добавить типизированный клиент, используя Refit для создания реализации:</span><span class="sxs-lookup"><span data-stu-id="ad943-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="ad943-181">При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:</span><span class="sxs-lookup"><span data-stu-id="ad943-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="ad943-182">Выполнение запросов POST, PUT и DELETE</span><span class="sxs-lookup"><span data-stu-id="ad943-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="ad943-183">В предыдущих примерах все HTTP-запросы используют HTTP-команду GET.</span><span class="sxs-lookup"><span data-stu-id="ad943-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="ad943-184">`HttpClient` также поддерживает другие HTTP-команды, в том числе:</span><span class="sxs-lookup"><span data-stu-id="ad943-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="ad943-185">ПОМЕСТИТЬ</span><span class="sxs-lookup"><span data-stu-id="ad943-185">POST</span></span>
* <span data-ttu-id="ad943-186">ОТПРАВКА</span><span class="sxs-lookup"><span data-stu-id="ad943-186">PUT</span></span>
* <span data-ttu-id="ad943-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="ad943-187">DELETE</span></span>
* <span data-ttu-id="ad943-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="ad943-188">PATCH</span></span>

<span data-ttu-id="ad943-189">Полный список поддерживаемых HTTP-команд см. в статье <xref:System.Net.Http.HttpMethod>.</span><span class="sxs-lookup"><span data-stu-id="ad943-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="ad943-190">В следующем примере показано, как выполнить HTTP-запрос POST:</span><span class="sxs-lookup"><span data-stu-id="ad943-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="ad943-191">В приведенном выше коде метод `CreateItemAsync` выполняет следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="ad943-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="ad943-192">сериализует параметр `TodoItem` в JSON с помощью `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="ad943-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="ad943-193">Для настройки процесса сериализации используется экземпляр <xref:System.Text.Json.JsonSerializerOptions>.</span><span class="sxs-lookup"><span data-stu-id="ad943-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="ad943-194">создает экземпляр <xref:System.Net.Http.StringContent> для упаковки сериализованного JSON для отправки в тексте HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="ad943-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="ad943-195">вызывает метод <xref:System.Net.Http.HttpClient.PostAsync%2A> для отправки содержимого JSON по указанному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="ad943-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="ad943-196">Это относительный URL-адрес, который добавляется в свойство [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="ad943-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="ad943-197">вызывает метод <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>, чтобы создавать исключение, если код состояния ответа означает неудачное выполнение.</span><span class="sxs-lookup"><span data-stu-id="ad943-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="ad943-198">`HttpClient` также поддерживает другие типы содержимого.</span><span class="sxs-lookup"><span data-stu-id="ad943-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="ad943-199">Например, <xref:System.Net.Http.MultipartContent> и <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="ad943-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="ad943-200">Полный список поддерживаемого содержимого см. в статье <xref:System.Net.Http.HttpContent>.</span><span class="sxs-lookup"><span data-stu-id="ad943-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="ad943-201">Ниже приводится пример HTTP-запроса PUT.</span><span class="sxs-lookup"><span data-stu-id="ad943-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="ad943-202">Приведенный выше код практически аналогичен коду в примере с POST.</span><span class="sxs-lookup"><span data-stu-id="ad943-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="ad943-203">Метод `SaveItemAsync` вызывает <xref:System.Net.Http.HttpClient.PutAsync%2A> вместо `PostAsync`.</span><span class="sxs-lookup"><span data-stu-id="ad943-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="ad943-204">Ниже приводится пример HTTP-запроса DELETE.</span><span class="sxs-lookup"><span data-stu-id="ad943-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="ad943-205">В приведенном выше коде метод `DeleteItemAsync` вызывает <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="ad943-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="ad943-206">Поскольку HTTP-запросы DELETE обычно не содержат текст, метод `DeleteAsync` не предоставляет перегрузку, которая принимает экземпляр `HttpContent`.</span><span class="sxs-lookup"><span data-stu-id="ad943-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="ad943-207">Дополнительные сведения об использовании различных HTTP-команд с `HttpClient` см. в статье <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="ad943-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="ad943-208">ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="ad943-208">Outgoing request middleware</span></span>

<span data-ttu-id="ad943-209">В `HttpClient` существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="ad943-210">`IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad943-210">`IHttpClientFactory`:</span></span>

  * <span data-ttu-id="ad943-211">Упрощает определение обработчиков для применения к каждому именованному клиенту.</span><span class="sxs-lookup"><span data-stu-id="ad943-211">Simplifies defining the handlers to apply for each named client.</span></span>
  * <span data-ttu-id="ad943-212">Поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-212">Supports registration and chaining of multiple handlers to build an outgoing request   middleware pipeline.</span></span> <span data-ttu-id="ad943-213">Каждый из этих обработчиков может выполнять работу до и после исходящего запроса.</span><span class="sxs-lookup"><span data-stu-id="ad943-213">Each of these handlers is able to perform work before and after the   outgoing request.</span></span> <span data-ttu-id="ad943-214">Этот шаблон:</span><span class="sxs-lookup"><span data-stu-id="ad943-214">This pattern:</span></span>
  
    * <span data-ttu-id="ad943-215">похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="ad943-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
    * <span data-ttu-id="ad943-216">предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая:</span><span class="sxs-lookup"><span data-stu-id="ad943-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>
      * <span data-ttu-id="ad943-217">кэширование</span><span class="sxs-lookup"><span data-stu-id="ad943-217">caching</span></span>
      * <span data-ttu-id="ad943-218">обработку ошибок</span><span class="sxs-lookup"><span data-stu-id="ad943-218">error handling</span></span>
      * <span data-ttu-id="ad943-219">сериализацию</span><span class="sxs-lookup"><span data-stu-id="ad943-219">serialization</span></span>
      * <span data-ttu-id="ad943-220">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="ad943-220">logging</span></span>

<span data-ttu-id="ad943-221">Чтобы создать делегированный обработчик, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ad943-221">To create a delegating handler:</span></span>

* <span data-ttu-id="ad943-222">Создайте объект, производный от <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad943-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="ad943-223">Переопределите метод <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="ad943-224">Выполните код до передачи запроса следующему обработчику в конвейере:</span><span class="sxs-lookup"><span data-stu-id="ad943-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="ad943-225">Предыдущий код проверяет, находится ли заголовок `X-API-KEY` в запросе.</span><span class="sxs-lookup"><span data-stu-id="ad943-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="ad943-226">Если `X-API-KEY` отсутствует, возвращается <xref:System.Net.HttpStatusCode.BadRequest>.</span><span class="sxs-lookup"><span data-stu-id="ad943-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="ad943-227">Можно добавить сразу несколько обработчиков в конфигурацию для `HttpClient` с использованием <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="ad943-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="ad943-228">В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ad943-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="ad943-229">После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-229">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="ad943-230">Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться.</span><span class="sxs-lookup"><span data-stu-id="ad943-230">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="ad943-231">Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:</span><span class="sxs-lookup"><span data-stu-id="ad943-231">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a><span data-ttu-id="ad943-232">Использование внедрения зависимостей в ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="ad943-232">Use DI in outgoing request middleware</span></span>

<span data-ttu-id="ad943-233">Когда `IHttpClientFactory` создает новый делегированный обработчик, он использует внедрение зависимостей для выполнения параметров конструктора обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-233">When `IHttpClientFactory` creates a new delegating handler, it uses DI to fulfill the handler's constructor parameters.</span></span> <span data-ttu-id="ad943-234">`IHttpClientFactory` создает **отдельную** область внедрения зависимостей для каждого обработчика, что может стать причиной аномального поведения, когда обработчик использует службу с *назначенной областью*.</span><span class="sxs-lookup"><span data-stu-id="ad943-234">`IHttpClientFactory` creates a **separate** DI scope for each handler, which can lead to surprising behavior when a handler consumes a *scoped* service.</span></span>

<span data-ttu-id="ad943-235">Например, рассмотрим следующий интерфейс и его реализацию, которая представляет задачу в виде операции с идентификатором `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="ad943-235">For example, consider the following interface and its implementation, which represents a task as an operation with an identifier, `OperationId`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

<span data-ttu-id="ad943-236">Как можно понять из названия, `IOperationScoped` регистрируется с помощью внедрения зависимостей с использованием времени существования с *назначенной областью*:</span><span class="sxs-lookup"><span data-stu-id="ad943-236">As its name suggests, `IOperationScoped` is registered with DI using a *scoped* lifetime:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

<span data-ttu-id="ad943-237">Следующий делегирующий обработчик принимает и использует `IOperationScoped` для задания заголовка `X-OPERATION-ID` для исходящего запроса:</span><span class="sxs-lookup"><span data-stu-id="ad943-237">The following delegating handler consumes and uses `IOperationScoped` to set the `X-OPERATION-ID` header for the outgoing request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

<span data-ttu-id="ad943-238">В [скачиваемом ресурсе `HttpRequestsSample`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)] перейдите к `/Operation` и обновите страницу.</span><span class="sxs-lookup"><span data-stu-id="ad943-238">In the [`HttpRequestsSample` download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navigate to `/Operation` and refresh the page.</span></span> <span data-ttu-id="ad943-239">Значение области запроса изменяется с каждым запросом, но значение области обработчика изменяется только каждые 5 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad943-239">The request scope value changes for each request, but the handler scope value only changes every 5 seconds.</span></span>

<span data-ttu-id="ad943-240">Обработчики могут зависеть от служб из любой области.</span><span class="sxs-lookup"><span data-stu-id="ad943-240">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="ad943-241">Службы, которые зависят от обработчиков, удаляются при удалении обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-241">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="ad943-242">Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:</span><span class="sxs-lookup"><span data-stu-id="ad943-242">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="ad943-243">Передайте данные в обработчик с помощью [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="ad943-243">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="ad943-244">Используйте <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> для доступа к текущему запросу.</span><span class="sxs-lookup"><span data-stu-id="ad943-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="ad943-245">Создайте пользовательский объект хранилища <xref:System.Threading.AsyncLocal`1> для передачи данных.</span><span class="sxs-lookup"><span data-stu-id="ad943-245">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="ad943-246">Использование обработчиков на основе Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-246">Use Polly-based handlers</span></span>

<span data-ttu-id="ad943-247">`IHttpClientFactory` интегрируется с библиотекой сторонних разработчиков [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="ad943-247">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="ad943-248">Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET.</span><span class="sxs-lookup"><span data-stu-id="ad943-248">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="ad943-249">Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.</span><span class="sxs-lookup"><span data-stu-id="ad943-249">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="ad943-250">Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения.</span><span class="sxs-lookup"><span data-stu-id="ad943-250">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="ad943-251">Расширения Polly поддерживают добавление обработчиков на основе Polly клиентам.</span><span class="sxs-lookup"><span data-stu-id="ad943-251">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="ad943-252">Polly нужен пакет NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="ad943-252">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="ad943-253">Обработка временных сбоев</span><span class="sxs-lookup"><span data-stu-id="ad943-253">Handle transient faults</span></span>

<span data-ttu-id="ad943-254">Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными.</span><span class="sxs-lookup"><span data-stu-id="ad943-254">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="ad943-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> позволяет определить политику для обработки временных ошибок.</span><span class="sxs-lookup"><span data-stu-id="ad943-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="ad943-256">Политики, настроенные с помощью `AddTransientHttpErrorPolicy`, обрабатывают следующие ответы:</span><span class="sxs-lookup"><span data-stu-id="ad943-256">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="ad943-257">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="ad943-257">HTTP 5xx</span></span>
* <span data-ttu-id="ad943-258">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="ad943-258">HTTP 408</span></span>

<span data-ttu-id="ad943-259">`AddTransientHttpErrorPolicy` предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:</span><span class="sxs-lookup"><span data-stu-id="ad943-259">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="ad943-260">В приведенном выше коде определена политика `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="ad943-260">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="ad943-261">Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.</span><span class="sxs-lookup"><span data-stu-id="ad943-261">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="ad943-262">Динамический выбор политик</span><span class="sxs-lookup"><span data-stu-id="ad943-262">Dynamically select policies</span></span>

<span data-ttu-id="ad943-263">Предоставляются методы расширения для добавления обработчиков на основе Polly, например <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-263">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="ad943-264">Следующая перегрузка `AddPolicyHandler` проверяет запрос для определения применимой политики:</span><span class="sxs-lookup"><span data-stu-id="ad943-264">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="ad943-265">Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad943-265">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="ad943-266">Для остальных методов HTTP время ожидания — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad943-266">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="ad943-267">Добавление нескольких обработчиков Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-267">Add multiple Polly handlers</span></span>

<span data-ttu-id="ad943-268">Общепринятой практикой является вложение политик Polly:</span><span class="sxs-lookup"><span data-stu-id="ad943-268">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="ad943-269">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="ad943-269">In the preceding example:</span></span>

* <span data-ttu-id="ad943-270">Добавляются два обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-270">Two handlers are added.</span></span>
* <span data-ttu-id="ad943-271">Первый обработчик использует <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>, чтобы добавить политику повтора.</span><span class="sxs-lookup"><span data-stu-id="ad943-271">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="ad943-272">Неудачные запросы выполняются повторно до трех раз.</span><span class="sxs-lookup"><span data-stu-id="ad943-272">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="ad943-273">Второй вызов `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи.</span><span class="sxs-lookup"><span data-stu-id="ad943-273">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="ad943-274">Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд.</span><span class="sxs-lookup"><span data-stu-id="ad943-274">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="ad943-275">Политики размыкателя цепи отслеживают состояние.</span><span class="sxs-lookup"><span data-stu-id="ad943-275">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="ad943-276">Все вызовы через этот клиент имеют одинаковое состояние цепи.</span><span class="sxs-lookup"><span data-stu-id="ad943-276">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="ad943-277">Добавление политик из реестра Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-277">Add policies from the Polly registry</span></span>

<span data-ttu-id="ad943-278">Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="ad943-278">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="ad943-279">В приведенном ниже коде выполняется следующее:</span><span class="sxs-lookup"><span data-stu-id="ad943-279">In the following code:</span></span>

* <span data-ttu-id="ad943-280">Добавляются политики regular и long.</span><span class="sxs-lookup"><span data-stu-id="ad943-280">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="ad943-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> добавляет политики regular и long из реестра.</span><span class="sxs-lookup"><span data-stu-id="ad943-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="ad943-282">Дополнительные сведения о `IHttpClientFactory` и интеграции Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="ad943-282">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="ad943-283">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="ad943-283">HttpClient and lifetime management</span></span>

<span data-ttu-id="ad943-284">При каждом вызове `CreateClient` в `IHttpClientFactory` возвращается новый экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-284">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="ad943-285"><xref:System.Net.Http.HttpMessageHandler> создается для каждого именованного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-285">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="ad943-286">Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-286">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="ad943-287">`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов.</span><span class="sxs-lookup"><span data-stu-id="ad943-287">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="ad943-288">Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.</span><span class="sxs-lookup"><span data-stu-id="ad943-288">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="ad943-289">Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями.</span><span class="sxs-lookup"><span data-stu-id="ad943-289">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="ad943-290">Создание лишних обработчиков может привести к задержке подключения.</span><span class="sxs-lookup"><span data-stu-id="ad943-290">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="ad943-291">Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения службы доменных имен (DNS).</span><span class="sxs-lookup"><span data-stu-id="ad943-291">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="ad943-292">Время существования обработчика по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="ad943-292">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="ad943-293">Значение по умолчанию можно переопределить для каждого именованного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-293">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="ad943-294">Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, **не** требующие освобождения.</span><span class="sxs-lookup"><span data-stu-id="ad943-294">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="ad943-295">Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-295">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="ad943-296">`IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-296">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="ad943-297">До появления `IHttpClientFactory` один экземпляр `HttpClient` часто сохраняли в активном состоянии в течение длительного времени.</span><span class="sxs-lookup"><span data-stu-id="ad943-297">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="ad943-298">После перехода на `IHttpClientFactory` это уже не нужно.</span><span class="sxs-lookup"><span data-stu-id="ad943-298">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="ad943-299">Альтернативы интерфейсу IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="ad943-299">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="ad943-300">Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:</span><span class="sxs-lookup"><span data-stu-id="ad943-300">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="ad943-301">предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;</span><span class="sxs-lookup"><span data-stu-id="ad943-301">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="ad943-302">предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-302">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="ad943-303">Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad943-303">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="ad943-304">Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.</span><span class="sxs-lookup"><span data-stu-id="ad943-304">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="ad943-305">Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad943-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="ad943-306">По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="ad943-306">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="ad943-307">Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.</span><span class="sxs-lookup"><span data-stu-id="ad943-307">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="ad943-308">`SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-308">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="ad943-309">Этот позволяет предотвратить нехватку сокетов.</span><span class="sxs-lookup"><span data-stu-id="ad943-309">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="ad943-310">`SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad943-310">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="ad943-311">Cookies</span><span class="sxs-lookup"><span data-stu-id="ad943-311">Cookies</span></span>

<span data-ttu-id="ad943-312">Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="ad943-312">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="ad943-313">Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде.</span><span class="sxs-lookup"><span data-stu-id="ad943-313">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="ad943-314">Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ad943-314">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="ad943-315">отключите автоматическую обработку файлов cookie;</span><span class="sxs-lookup"><span data-stu-id="ad943-315">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="ad943-316">не используйте `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad943-316">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="ad943-317">Чтобы отключить автоматическую обработку файлов <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>, вызовите cookie:</span><span class="sxs-lookup"><span data-stu-id="ad943-317">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="ad943-318">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="ad943-318">Logging</span></span>

<span data-ttu-id="ad943-319">Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-319">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="ad943-320">Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ad943-320">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="ad943-321">Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.</span><span class="sxs-lookup"><span data-stu-id="ad943-321">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="ad943-322">Категория журнала для каждого клиента включает в себя имя клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-322">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="ad943-323">Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="ad943-323">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="ad943-324">Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-324">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="ad943-325">Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad943-325">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="ad943-326">Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad943-326">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="ad943-327">Кроме того, журнал ведется в конвейере обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-327">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="ad943-328">В примере *MyNamedClient* эти сообщения регистрируются с категорией журнала "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="ad943-328">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="ad943-329">Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса.</span><span class="sxs-lookup"><span data-stu-id="ad943-329">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="ad943-330">Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.</span><span class="sxs-lookup"><span data-stu-id="ad943-330">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="ad943-331">Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера.</span><span class="sxs-lookup"><span data-stu-id="ad943-331">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="ad943-332">Сюда входят изменения заголовков запросов или кода состояния ответов.</span><span class="sxs-lookup"><span data-stu-id="ad943-332">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="ad943-333">Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам.</span><span class="sxs-lookup"><span data-stu-id="ad943-333">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="ad943-334">Настройка HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ad943-334">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="ad943-335">Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.</span><span class="sxs-lookup"><span data-stu-id="ad943-335">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="ad943-336">При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ad943-336">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="ad943-337">Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-337">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="ad943-338">Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:</span><span class="sxs-lookup"><span data-stu-id="ad943-338">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="ad943-339">Использование IHttpClientFactory в консольном приложении</span><span class="sxs-lookup"><span data-stu-id="ad943-339">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="ad943-340">В консольном приложении добавьте в проект следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="ad943-340">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="ad943-341">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="ad943-341">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="ad943-342">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="ad943-342">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="ad943-343">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ad943-343">In the following example:</span></span>

* <span data-ttu-id="ad943-344"><xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):</span><span class="sxs-lookup"><span data-stu-id="ad943-344"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="ad943-345">`MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-345">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="ad943-346">`HttpClient` используется для получения веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="ad943-346">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="ad943-347">`Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.</span><span class="sxs-lookup"><span data-stu-id="ad943-347">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="ad943-348">ПО промежуточного слоя для распространения заголовков</span><span class="sxs-lookup"><span data-stu-id="ad943-348">Header propagation middleware</span></span>

<span data-ttu-id="ad943-349">Header propagation — это ПО промежуточного слоя ASP.NET Core для распространения HTTP-заголовков из входящего запроса на исходящие запросы HTTP-клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad943-349">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="ad943-350">Чтобы использовать распространение заголовков, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ad943-350">To use header propagation:</span></span>

* <span data-ttu-id="ad943-351">Укажите ссылку на пакет [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="ad943-351">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="ad943-352">Настройте ПО промежуточного слоя и `HttpClient` в `Startup`:</span><span class="sxs-lookup"><span data-stu-id="ad943-352">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="ad943-353">Клиент включает настроенные заголовки в исходящие запросы:</span><span class="sxs-lookup"><span data-stu-id="ad943-353">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="ad943-354">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ad943-354">Additional resources</span></span>

* [<span data-ttu-id="ad943-355">Использование HttpClientFactory для реализации устойчивых HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="ad943-355">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="ad943-356">Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-356">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="ad943-357">Реализация шаблона размыкателя цепи</span><span class="sxs-lookup"><span data-stu-id="ad943-357">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="ad943-358">Сериализация и десериализация JSON в .NET</span><span class="sxs-lookup"><span data-stu-id="ad943-358">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ad943-359">Авторы: [Гленн Кондрон (Glenn Condron)](https://github.com/glennc), [Райан Новак (Ryan Nowak)](https://github.com/rynowak) и [Стив Гордон (Steve Gordon)](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="ad943-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="ad943-360"><xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad943-360">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="ad943-361">Так вы получите следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="ad943-361">It offers the following benefits:</span></span>

* <span data-ttu-id="ad943-362">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-362">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ad943-363">Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="ad943-363">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="ad943-364">Можно зарегистрировать клиент по умолчанию для других целей.</span><span class="sxs-lookup"><span data-stu-id="ad943-364">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="ad943-365">Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient` и предоставление расширений для ПО промежуточного слоя на основе Polly для использования этой возможности.</span><span class="sxs-lookup"><span data-stu-id="ad943-365">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="ad943-366">Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="ad943-366">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ad943-367">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="ad943-367">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ad943-368">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ad943-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="ad943-369">Принципы использования</span><span class="sxs-lookup"><span data-stu-id="ad943-369">Consumption patterns</span></span>

<span data-ttu-id="ad943-370">Существует несколько способов использования `IHttpClientFactory` в приложении:</span><span class="sxs-lookup"><span data-stu-id="ad943-370">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="ad943-371">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad943-371">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="ad943-372">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-372">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="ad943-373">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-373">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="ad943-374">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-374">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="ad943-375">Все способы равноценны.</span><span class="sxs-lookup"><span data-stu-id="ad943-375">None of them are strictly superior to another.</span></span> <span data-ttu-id="ad943-376">Оптимальный подход зависит от ограничений приложения.</span><span class="sxs-lookup"><span data-stu-id="ad943-376">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="ad943-377">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad943-377">Basic usage</span></span>

<span data-ttu-id="ad943-378">`IHttpClientFactory` можно зарегистрировать путем вызова метода расширения `AddHttpClient` в `IServiceCollection` внутри метода `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad943-378">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="ad943-379">После регистрации код может принимать `IHttpClientFactory` в любом месте, куда можно внедрить службу с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="ad943-379">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ad943-380">`IHttpClientFactory` можно использовать для создания экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad943-380">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="ad943-381">Подобное использование `IHttpClientFactory` — это отличный способ рефакторинга имеющегося приложения.</span><span class="sxs-lookup"><span data-stu-id="ad943-381">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="ad943-382">Он не оказывает влияния на использование `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-382">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="ad943-383">Там, где в данный момент создаются экземпляры `HttpClient`, используйте вызов к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-383">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="ad943-384">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-384">Named clients</span></span>

<span data-ttu-id="ad943-385">Если для приложения предполагаются разные способы использования `HttpClient`, каждый со своей конфигурацией, можно применять **именованные клиенты**.</span><span class="sxs-lookup"><span data-stu-id="ad943-385">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="ad943-386">Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad943-386">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="ad943-387">В приведенном выше коде вызывается клиент `AddHttpClient`, предоставляющий имя *github*.</span><span class="sxs-lookup"><span data-stu-id="ad943-387">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="ad943-388">У клиента есть некоторые настройки по умолчанию &mdash; а именно: базовый адрес и два заголовка, необходимые для работы с API GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad943-388">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="ad943-389">При каждом вызове `CreateClient` создается новый экземпляр `HttpClient` и вызывается действие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ad943-389">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="ad943-390">Для использования именованного клиента можно передать строковый параметр в `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-390">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="ad943-391">Укажите имя создаваемого клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-391">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ad943-392">В приведенном выше коде в запросе не требуется указывать имя узла.</span><span class="sxs-lookup"><span data-stu-id="ad943-392">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="ad943-393">Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-393">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="ad943-394">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-394">Typed clients</span></span>

<span data-ttu-id="ad943-395">Типизированные клиенты:</span><span class="sxs-lookup"><span data-stu-id="ad943-395">Typed clients:</span></span>

* <span data-ttu-id="ad943-396">предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.</span><span class="sxs-lookup"><span data-stu-id="ad943-396">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="ad943-397">Это помогает IntelliSense и компилятору при использовании клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad943-397">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="ad943-398">Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-398">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="ad943-399">Например, для конечной точки серверной части можно использовать один типизированный клиент, который будет содержать всю логику работы с этой конечной точкой.</span><span class="sxs-lookup"><span data-stu-id="ad943-399">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="ad943-400">Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad943-400">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="ad943-401">Типизированный клиент принимает параметр `HttpClient` в конструкторе:</span><span class="sxs-lookup"><span data-stu-id="ad943-401">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ad943-402">В приведенном выше коде конфигурация перемещается в типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="ad943-402">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="ad943-403">Объект `HttpClient` предоставляется в виде открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="ad943-403">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="ad943-404">Можно определить связанные с API методы, которые предоставляют функциональные возможности `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-404">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="ad943-405">Метод `GetAspNetDocsIssues` инкапсулирует код, необходимый для запроса и анализа последнего открытого выпуска из репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad943-405">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="ad943-406">Для регистрации типизированного клиента можно использовать универсальный метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices`, указав класс типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-406">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="ad943-407">Типизированный клиент регистрируется во внедрении зависимостей как временный.</span><span class="sxs-lookup"><span data-stu-id="ad943-407">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="ad943-408">Типизированный клиент можно внедрить и использовать напрямую:</span><span class="sxs-lookup"><span data-stu-id="ad943-408">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="ad943-409">При желании конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-409">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="ad943-410">Можно полностью инкапсулировать `HttpClient` внутри типизированного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-410">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="ad943-411">Вместо предоставления его как свойства можно использовать открытые методы для внутреннего вызова экземпляра `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-411">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="ad943-412">В приведенном выше коде `HttpClient` хранится как закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="ad943-412">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="ad943-413">Любой доступ для совершения внешних вызовов осуществляется через метод `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="ad943-413">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="ad943-414">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-414">Generated clients</span></span>

<span data-ttu-id="ad943-415">`IHttpClientFactory` можно использовать в сочетании с другими библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="ad943-415">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="ad943-416">Refit — это библиотека REST для .NET.</span><span class="sxs-lookup"><span data-stu-id="ad943-416">Refit is a REST library for .NET.</span></span> <span data-ttu-id="ad943-417">Она преобразует REST API в динамические интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="ad943-417">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="ad943-418">Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad943-418">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="ad943-419">Для представления внешнего API и его ответа определяются интерфейс и ответ:</span><span class="sxs-lookup"><span data-stu-id="ad943-419">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="ad943-420">Можно добавить типизированный клиент, используя Refit для создания реализации:</span><span class="sxs-lookup"><span data-stu-id="ad943-420">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="ad943-421">При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:</span><span class="sxs-lookup"><span data-stu-id="ad943-421">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="ad943-422">ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="ad943-422">Outgoing request middleware</span></span>

<span data-ttu-id="ad943-423">В `HttpClient` уже существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-423">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="ad943-424">Класс `IHttpClientFactory` упрощает определение обработчиков для применения к каждому именованному клиенту.</span><span class="sxs-lookup"><span data-stu-id="ad943-424">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="ad943-425">Он поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-425">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ad943-426">Каждый из этих обработчиков может выполнять работу до и после исходящего запроса.</span><span class="sxs-lookup"><span data-stu-id="ad943-426">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="ad943-427">Этот шаблон похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad943-427">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="ad943-428">Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="ad943-428">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="ad943-429">Чтобы создать обработчик, необходимо определить класс, производный от <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad943-429">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="ad943-430">Переопределите метод `SendAsync` для выполнения кода до передачи запросов следующему обработчику в конвейере:</span><span class="sxs-lookup"><span data-stu-id="ad943-430">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="ad943-431">В предыдущем коде определяется базовый обработчик.</span><span class="sxs-lookup"><span data-stu-id="ad943-431">The preceding code defines a basic handler.</span></span> <span data-ttu-id="ad943-432">Он проверяет, включен ли в запрос заголовок `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="ad943-432">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="ad943-433">Если заголовок отсутствует, он может избежать вызовов HTTP и вернуть подходящий ответ.</span><span class="sxs-lookup"><span data-stu-id="ad943-433">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="ad943-434">Во время регистрации можно добавить один или несколько обработчиков в конфигурацию для `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-434">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="ad943-435">Эта задача выполняется через методы расширения в <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ad943-435">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="ad943-436">В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ad943-436">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="ad943-437">`IHttpClientFactory` создает отдельную область внедрения зависимостей для каждого обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-437">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="ad943-438">Обработчики могут зависеть от служб из любой области.</span><span class="sxs-lookup"><span data-stu-id="ad943-438">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="ad943-439">Службы, которые зависят от обработчиков, удаляются при удалении обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-439">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="ad943-440">После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-440">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="ad943-441">Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться.</span><span class="sxs-lookup"><span data-stu-id="ad943-441">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="ad943-442">Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:</span><span class="sxs-lookup"><span data-stu-id="ad943-442">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="ad943-443">Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:</span><span class="sxs-lookup"><span data-stu-id="ad943-443">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="ad943-444">Передайте данные в обработчик с помощью `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="ad943-444">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="ad943-445">Используйте `IHttpContextAccessor` для доступа к текущему запросу.</span><span class="sxs-lookup"><span data-stu-id="ad943-445">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="ad943-446">Создайте пользовательский объект хранилища `AsyncLocal` для передачи данных.</span><span class="sxs-lookup"><span data-stu-id="ad943-446">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="ad943-447">Использование обработчиков на основе Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-447">Use Polly-based handlers</span></span>

<span data-ttu-id="ad943-448">`IHttpClientFactory` интегрируется с популярной библиотекой сторонних разработчиков под названием [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="ad943-448">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="ad943-449">Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET.</span><span class="sxs-lookup"><span data-stu-id="ad943-449">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="ad943-450">Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.</span><span class="sxs-lookup"><span data-stu-id="ad943-450">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="ad943-451">Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения.</span><span class="sxs-lookup"><span data-stu-id="ad943-451">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="ad943-452">Расширения Polly:</span><span class="sxs-lookup"><span data-stu-id="ad943-452">The Polly extensions:</span></span>

* <span data-ttu-id="ad943-453">Поддерживает добавление обработчиков на основе Polly клиентам.</span><span class="sxs-lookup"><span data-stu-id="ad943-453">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="ad943-454">Можно использовать после установки пакета NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="ad943-454">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="ad943-455">Пакет не включен в общую платформу ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad943-455">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="ad943-456">Обработка временных сбоев</span><span class="sxs-lookup"><span data-stu-id="ad943-456">Handle transient faults</span></span>

<span data-ttu-id="ad943-457">Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными.</span><span class="sxs-lookup"><span data-stu-id="ad943-457">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="ad943-458">Используется удобный метод расширения `AddTransientHttpErrorPolicy`, который позволяет определить политику для обработки временных ошибок.</span><span class="sxs-lookup"><span data-stu-id="ad943-458">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="ad943-459">Политики, заданные с помощью этого метода расширения, обрабатывают `HttpRequestException`, ответы HTTP 5xx и ответы HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="ad943-459">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="ad943-460">Расширение `AddTransientHttpErrorPolicy` может быть использовано в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad943-460">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ad943-461">Данное расширение предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:</span><span class="sxs-lookup"><span data-stu-id="ad943-461">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="ad943-462">В приведенном выше коде определена политика `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="ad943-462">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="ad943-463">Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.</span><span class="sxs-lookup"><span data-stu-id="ad943-463">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="ad943-464">Динамический выбор политик</span><span class="sxs-lookup"><span data-stu-id="ad943-464">Dynamically select policies</span></span>

<span data-ttu-id="ad943-465">Существуют дополнительные методы расширения, которые можно использовать для добавления обработчиков на основе Polly.</span><span class="sxs-lookup"><span data-stu-id="ad943-465">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="ad943-466">Одним из таких расширений является `AddPolicyHandler` с несколькими перегрузками.</span><span class="sxs-lookup"><span data-stu-id="ad943-466">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="ad943-467">Одна перегрузка разрешает проверку запроса для определения необходимой политики:</span><span class="sxs-lookup"><span data-stu-id="ad943-467">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="ad943-468">Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad943-468">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="ad943-469">Для остальных методов HTTP время ожидания — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad943-469">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="ad943-470">Добавление нескольких обработчиков Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-470">Add multiple Polly handlers</span></span>

<span data-ttu-id="ad943-471">Общепринятой практикой является вложение политик Polly для предоставления расширенной функциональности:</span><span class="sxs-lookup"><span data-stu-id="ad943-471">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="ad943-472">В приведенном выше примере добавляются два обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-472">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="ad943-473">Первый использует расширение `AddTransientHttpErrorPolicy`, чтобы добавить политику повтора.</span><span class="sxs-lookup"><span data-stu-id="ad943-473">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="ad943-474">Неудачные запросы выполняются повторно до трех раз.</span><span class="sxs-lookup"><span data-stu-id="ad943-474">Failed requests are retried up to three times.</span></span> <span data-ttu-id="ad943-475">Второй вызов к `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи.</span><span class="sxs-lookup"><span data-stu-id="ad943-475">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="ad943-476">Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд.</span><span class="sxs-lookup"><span data-stu-id="ad943-476">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="ad943-477">Политики размыкателя цепи отслеживают состояние.</span><span class="sxs-lookup"><span data-stu-id="ad943-477">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="ad943-478">Все вызовы через этот клиент имеют одинаковое состояние цепи.</span><span class="sxs-lookup"><span data-stu-id="ad943-478">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="ad943-479">Добавление политик из реестра Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-479">Add policies from the Polly registry</span></span>

<span data-ttu-id="ad943-480">Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="ad943-480">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="ad943-481">Предоставляется метод расширения, разрешающий добавление обработчика с помощью политики из реестра:</span><span class="sxs-lookup"><span data-stu-id="ad943-481">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="ad943-482">В приведенном выше коде, когда `PolicyRegistry` добавляется в `ServiceCollection`, регистрируются две политики.</span><span class="sxs-lookup"><span data-stu-id="ad943-482">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="ad943-483">Чтобы использовать политику из реестра, применяется метод `AddPolicyHandlerFromRegistry`, который передает имя необходимой политики.</span><span class="sxs-lookup"><span data-stu-id="ad943-483">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="ad943-484">Дополнительные сведения об интеграции `IHttpClientFactory` и Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="ad943-484">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="ad943-485">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="ad943-485">HttpClient and lifetime management</span></span>

<span data-ttu-id="ad943-486">При каждом вызове `CreateClient` в `IHttpClientFactory` возвращается новый экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-486">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="ad943-487">Для каждого названного клиента существует <xref:System.Net.Http.HttpMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad943-487">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="ad943-488">Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-488">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="ad943-489">`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов.</span><span class="sxs-lookup"><span data-stu-id="ad943-489">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="ad943-490">Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.</span><span class="sxs-lookup"><span data-stu-id="ad943-490">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="ad943-491">Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями.</span><span class="sxs-lookup"><span data-stu-id="ad943-491">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="ad943-492">Создание лишних обработчиков может привести к задержке подключения.</span><span class="sxs-lookup"><span data-stu-id="ad943-492">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="ad943-493">Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения DNS.</span><span class="sxs-lookup"><span data-stu-id="ad943-493">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="ad943-494">Время существования обработчика по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="ad943-494">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="ad943-495">Значение по умолчанию можно переопределить для каждого именованного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-495">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="ad943-496">Чтобы переопределить это значение, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> в `IHttpClientBuilder`, который возвращается при создании клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-496">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="ad943-497">Высвобождать клиент не требуется.</span><span class="sxs-lookup"><span data-stu-id="ad943-497">Disposal of the client isn't required.</span></span> <span data-ttu-id="ad943-498">Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-498">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="ad943-499">`IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-499">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="ad943-500">Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, не требующие высвобождения.</span><span class="sxs-lookup"><span data-stu-id="ad943-500">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="ad943-501">До появления `IHttpClientFactory` один экземпляр `HttpClient` часто сохраняли в активном состоянии в течение длительного времени.</span><span class="sxs-lookup"><span data-stu-id="ad943-501">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="ad943-502">После перехода на `IHttpClientFactory` это уже не нужно.</span><span class="sxs-lookup"><span data-stu-id="ad943-502">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="ad943-503">Альтернативы интерфейсу IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="ad943-503">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="ad943-504">Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:</span><span class="sxs-lookup"><span data-stu-id="ad943-504">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="ad943-505">предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;</span><span class="sxs-lookup"><span data-stu-id="ad943-505">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="ad943-506">предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-506">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="ad943-507">Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad943-507">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="ad943-508">Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.</span><span class="sxs-lookup"><span data-stu-id="ad943-508">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="ad943-509">Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad943-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="ad943-510">По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="ad943-510">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="ad943-511">Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.</span><span class="sxs-lookup"><span data-stu-id="ad943-511">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="ad943-512">`SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-512">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="ad943-513">Этот позволяет предотвратить нехватку сокетов.</span><span class="sxs-lookup"><span data-stu-id="ad943-513">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="ad943-514">`SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad943-514">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="ad943-515">Cookies</span><span class="sxs-lookup"><span data-stu-id="ad943-515">Cookies</span></span>

<span data-ttu-id="ad943-516">Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="ad943-516">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="ad943-517">Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде.</span><span class="sxs-lookup"><span data-stu-id="ad943-517">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="ad943-518">Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ad943-518">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="ad943-519">отключите автоматическую обработку файлов cookie;</span><span class="sxs-lookup"><span data-stu-id="ad943-519">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="ad943-520">не используйте `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad943-520">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="ad943-521">Чтобы отключить автоматическую обработку файлов <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>, вызовите cookie:</span><span class="sxs-lookup"><span data-stu-id="ad943-521">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="ad943-522">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="ad943-522">Logging</span></span>

<span data-ttu-id="ad943-523">Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-523">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="ad943-524">Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ad943-524">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="ad943-525">Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.</span><span class="sxs-lookup"><span data-stu-id="ad943-525">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="ad943-526">Категория журнала для каждого клиента включает в себя имя клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-526">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="ad943-527">Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-527">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="ad943-528">Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-528">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="ad943-529">Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad943-529">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="ad943-530">Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad943-530">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="ad943-531">Кроме того, журнал ведется в конвейере обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-531">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="ad943-532">В примере *MyNamedClient* эти сообщения вносятся в журнал по категории журнала `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-532">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="ad943-533">Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса по сети.</span><span class="sxs-lookup"><span data-stu-id="ad943-533">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="ad943-534">Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.</span><span class="sxs-lookup"><span data-stu-id="ad943-534">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="ad943-535">Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера.</span><span class="sxs-lookup"><span data-stu-id="ad943-535">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="ad943-536">Сюда входят, например, изменения заголовков запросов или кода состояния ответов.</span><span class="sxs-lookup"><span data-stu-id="ad943-536">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="ad943-537">Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам при необходимости.</span><span class="sxs-lookup"><span data-stu-id="ad943-537">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="ad943-538">Настройка HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ad943-538">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="ad943-539">Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.</span><span class="sxs-lookup"><span data-stu-id="ad943-539">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="ad943-540">При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ad943-540">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="ad943-541">Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-541">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="ad943-542">Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:</span><span class="sxs-lookup"><span data-stu-id="ad943-542">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="ad943-543">Использование IHttpClientFactory в консольном приложении</span><span class="sxs-lookup"><span data-stu-id="ad943-543">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="ad943-544">В консольном приложении добавьте в проект следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="ad943-544">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="ad943-545">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="ad943-545">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="ad943-546">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="ad943-546">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="ad943-547">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ad943-547">In the following example:</span></span>

* <span data-ttu-id="ad943-548"><xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):</span><span class="sxs-lookup"><span data-stu-id="ad943-548"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="ad943-549">`MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-549">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="ad943-550">`HttpClient` используется для получения веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="ad943-550">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="ad943-551">`Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.</span><span class="sxs-lookup"><span data-stu-id="ad943-551">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="ad943-552">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ad943-552">Additional resources</span></span>

* [<span data-ttu-id="ad943-553">Использование HttpClientFactory для реализации устойчивых HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="ad943-553">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="ad943-554">Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-554">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="ad943-555">Реализация шаблона размыкателя цепи</span><span class="sxs-lookup"><span data-stu-id="ad943-555">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="ad943-556">Авторы: [Гленн Кондрон (Glenn Condron)](https://github.com/glennc), [Райан Новак (Ryan Nowak)](https://github.com/rynowak) и [Стив Гордон (Steve Gordon)](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="ad943-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="ad943-557"><xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad943-557">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="ad943-558">Так вы получите следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="ad943-558">It offers the following benefits:</span></span>

* <span data-ttu-id="ad943-559">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-559">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ad943-560">Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="ad943-560">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="ad943-561">Можно зарегистрировать клиент по умолчанию для других целей.</span><span class="sxs-lookup"><span data-stu-id="ad943-561">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="ad943-562">Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient` и предоставление расширений для ПО промежуточного слоя на основе Polly для использования этой возможности.</span><span class="sxs-lookup"><span data-stu-id="ad943-562">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="ad943-563">Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="ad943-563">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ad943-564">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="ad943-564">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ad943-565">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ad943-565">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ad943-566">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ad943-566">Prerequisites</span></span>

<span data-ttu-id="ad943-567">Для проектов, предназначенных для .NET Framework, необходимо установить пакет NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="ad943-567">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="ad943-568">Пакет `Microsoft.Extensions.Http` уже включен в проекты, предназначенные для .NET Core и ссылающиеся на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ad943-568">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="ad943-569">Принципы использования</span><span class="sxs-lookup"><span data-stu-id="ad943-569">Consumption patterns</span></span>

<span data-ttu-id="ad943-570">Существует несколько способов использования `IHttpClientFactory` в приложении:</span><span class="sxs-lookup"><span data-stu-id="ad943-570">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="ad943-571">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad943-571">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="ad943-572">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-572">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="ad943-573">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-573">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="ad943-574">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-574">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="ad943-575">Все способы равноценны.</span><span class="sxs-lookup"><span data-stu-id="ad943-575">None of them are strictly superior to another.</span></span> <span data-ttu-id="ad943-576">Оптимальный подход зависит от ограничений приложения.</span><span class="sxs-lookup"><span data-stu-id="ad943-576">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="ad943-577">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad943-577">Basic usage</span></span>

<span data-ttu-id="ad943-578">`IHttpClientFactory` можно зарегистрировать путем вызова метода расширения `AddHttpClient` в `IServiceCollection` внутри метода `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad943-578">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="ad943-579">После регистрации код может принимать `IHttpClientFactory` в любом месте, куда можно внедрить службу с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="ad943-579">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ad943-580">`IHttpClientFactory` можно использовать для создания экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad943-580">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="ad943-581">Подобное использование `IHttpClientFactory` — это отличный способ рефакторинга имеющегося приложения.</span><span class="sxs-lookup"><span data-stu-id="ad943-581">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="ad943-582">Он не оказывает влияния на использование `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-582">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="ad943-583">Там, где в данный момент создаются экземпляры `HttpClient`, используйте вызов к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-583">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="ad943-584">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-584">Named clients</span></span>

<span data-ttu-id="ad943-585">Если для приложения предполагаются разные способы использования `HttpClient`, каждый со своей конфигурацией, можно применять **именованные клиенты**.</span><span class="sxs-lookup"><span data-stu-id="ad943-585">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="ad943-586">Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad943-586">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="ad943-587">В приведенном выше коде вызывается клиент `AddHttpClient`, предоставляющий имя *github*.</span><span class="sxs-lookup"><span data-stu-id="ad943-587">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="ad943-588">У клиента есть некоторые настройки по умолчанию &mdash; а именно: базовый адрес и два заголовка, необходимые для работы с API GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad943-588">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="ad943-589">При каждом вызове `CreateClient` создается новый экземпляр `HttpClient` и вызывается действие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ad943-589">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="ad943-590">Для использования именованного клиента можно передать строковый параметр в `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-590">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="ad943-591">Укажите имя создаваемого клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-591">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ad943-592">В приведенном выше коде в запросе не требуется указывать имя узла.</span><span class="sxs-lookup"><span data-stu-id="ad943-592">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="ad943-593">Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-593">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="ad943-594">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-594">Typed clients</span></span>

<span data-ttu-id="ad943-595">Типизированные клиенты:</span><span class="sxs-lookup"><span data-stu-id="ad943-595">Typed clients:</span></span>

* <span data-ttu-id="ad943-596">предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.</span><span class="sxs-lookup"><span data-stu-id="ad943-596">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="ad943-597">Это помогает IntelliSense и компилятору при использовании клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad943-597">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="ad943-598">Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-598">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="ad943-599">Например, для конечной точки серверной части можно использовать один типизированный клиент, который будет содержать всю логику работы с этой конечной точкой.</span><span class="sxs-lookup"><span data-stu-id="ad943-599">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="ad943-600">Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad943-600">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="ad943-601">Типизированный клиент принимает параметр `HttpClient` в конструкторе:</span><span class="sxs-lookup"><span data-stu-id="ad943-601">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ad943-602">В приведенном выше коде конфигурация перемещается в типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="ad943-602">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="ad943-603">Объект `HttpClient` предоставляется в виде открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="ad943-603">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="ad943-604">Можно определить связанные с API методы, которые предоставляют функциональные возможности `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-604">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="ad943-605">Метод `GetAspNetDocsIssues` инкапсулирует код, необходимый для запроса и анализа последнего открытого выпуска из репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad943-605">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="ad943-606">Для регистрации типизированного клиента можно использовать универсальный метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices`, указав класс типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-606">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="ad943-607">Типизированный клиент регистрируется во внедрении зависимостей как временный.</span><span class="sxs-lookup"><span data-stu-id="ad943-607">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="ad943-608">Типизированный клиент можно внедрить и использовать напрямую:</span><span class="sxs-lookup"><span data-stu-id="ad943-608">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="ad943-609">При желании конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-609">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="ad943-610">Можно полностью инкапсулировать `HttpClient` внутри типизированного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-610">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="ad943-611">Вместо предоставления его как свойства можно использовать открытые методы для внутреннего вызова экземпляра `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-611">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="ad943-612">В приведенном выше коде `HttpClient` хранится как закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="ad943-612">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="ad943-613">Любой доступ для совершения внешних вызовов осуществляется через метод `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="ad943-613">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="ad943-614">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad943-614">Generated clients</span></span>

<span data-ttu-id="ad943-615">`IHttpClientFactory` можно использовать в сочетании с другими библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="ad943-615">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="ad943-616">Refit — это библиотека REST для .NET.</span><span class="sxs-lookup"><span data-stu-id="ad943-616">Refit is a REST library for .NET.</span></span> <span data-ttu-id="ad943-617">Она преобразует REST API в динамические интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="ad943-617">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="ad943-618">Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad943-618">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="ad943-619">Для представления внешнего API и его ответа определяются интерфейс и ответ:</span><span class="sxs-lookup"><span data-stu-id="ad943-619">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="ad943-620">Можно добавить типизированный клиент, используя Refit для создания реализации:</span><span class="sxs-lookup"><span data-stu-id="ad943-620">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="ad943-621">При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:</span><span class="sxs-lookup"><span data-stu-id="ad943-621">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="ad943-622">ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="ad943-622">Outgoing request middleware</span></span>

<span data-ttu-id="ad943-623">В `HttpClient` уже существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-623">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="ad943-624">Класс `IHttpClientFactory` упрощает определение обработчиков для применения к каждому именованному клиенту.</span><span class="sxs-lookup"><span data-stu-id="ad943-624">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="ad943-625">Он поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-625">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ad943-626">Каждый из этих обработчиков может выполнять работу до и после исходящего запроса.</span><span class="sxs-lookup"><span data-stu-id="ad943-626">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="ad943-627">Этот шаблон похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad943-627">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="ad943-628">Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="ad943-628">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="ad943-629">Чтобы создать обработчик, необходимо определить класс, производный от <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad943-629">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="ad943-630">Переопределите метод `SendAsync` для выполнения кода до передачи запросов следующему обработчику в конвейере:</span><span class="sxs-lookup"><span data-stu-id="ad943-630">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="ad943-631">В предыдущем коде определяется базовый обработчик.</span><span class="sxs-lookup"><span data-stu-id="ad943-631">The preceding code defines a basic handler.</span></span> <span data-ttu-id="ad943-632">Он проверяет, включен ли в запрос заголовок `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="ad943-632">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="ad943-633">Если заголовок отсутствует, он может избежать вызовов HTTP и вернуть подходящий ответ.</span><span class="sxs-lookup"><span data-stu-id="ad943-633">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="ad943-634">Во время регистрации можно добавить один или несколько обработчиков в конфигурацию для `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-634">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="ad943-635">Эта задача выполняется через методы расширения в <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ad943-635">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="ad943-636">В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ad943-636">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="ad943-637">Обработчик **должен** регистрироваться во внедрении зависимостей как временная служба, а не ограниченная.</span><span class="sxs-lookup"><span data-stu-id="ad943-637">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="ad943-638">Если обработчик зарегистрирован в качестве службы с областью действия и все службы, от которых зависит этот обработчик, освобождаются:</span><span class="sxs-lookup"><span data-stu-id="ad943-638">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="ad943-639">Службы обработчика могли быть удалены, прежде чем обработчик вышел из области действия.</span><span class="sxs-lookup"><span data-stu-id="ad943-639">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="ad943-640">Освобожденные службы обработчика приводят к его сбою.</span><span class="sxs-lookup"><span data-stu-id="ad943-640">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="ad943-641">После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-641">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="ad943-642">Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться.</span><span class="sxs-lookup"><span data-stu-id="ad943-642">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="ad943-643">Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:</span><span class="sxs-lookup"><span data-stu-id="ad943-643">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="ad943-644">Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:</span><span class="sxs-lookup"><span data-stu-id="ad943-644">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="ad943-645">Передайте данные в обработчик с помощью `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="ad943-645">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="ad943-646">Используйте `IHttpContextAccessor` для доступа к текущему запросу.</span><span class="sxs-lookup"><span data-stu-id="ad943-646">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="ad943-647">Создайте пользовательский объект хранилища `AsyncLocal` для передачи данных.</span><span class="sxs-lookup"><span data-stu-id="ad943-647">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="ad943-648">Использование обработчиков на основе Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-648">Use Polly-based handlers</span></span>

<span data-ttu-id="ad943-649">`IHttpClientFactory` интегрируется с популярной библиотекой сторонних разработчиков под названием [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="ad943-649">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="ad943-650">Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET.</span><span class="sxs-lookup"><span data-stu-id="ad943-650">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="ad943-651">Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.</span><span class="sxs-lookup"><span data-stu-id="ad943-651">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="ad943-652">Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения.</span><span class="sxs-lookup"><span data-stu-id="ad943-652">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="ad943-653">Расширения Polly:</span><span class="sxs-lookup"><span data-stu-id="ad943-653">The Polly extensions:</span></span>

* <span data-ttu-id="ad943-654">Поддерживает добавление обработчиков на основе Polly клиентам.</span><span class="sxs-lookup"><span data-stu-id="ad943-654">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="ad943-655">Можно использовать после установки пакета NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="ad943-655">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="ad943-656">Пакет не включен в общую платформу ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad943-656">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="ad943-657">Обработка временных сбоев</span><span class="sxs-lookup"><span data-stu-id="ad943-657">Handle transient faults</span></span>

<span data-ttu-id="ad943-658">Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными.</span><span class="sxs-lookup"><span data-stu-id="ad943-658">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="ad943-659">Используется удобный метод расширения `AddTransientHttpErrorPolicy`, который позволяет определить политику для обработки временных ошибок.</span><span class="sxs-lookup"><span data-stu-id="ad943-659">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="ad943-660">Политики, заданные с помощью этого метода расширения, обрабатывают `HttpRequestException`, ответы HTTP 5xx и ответы HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="ad943-660">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="ad943-661">Расширение `AddTransientHttpErrorPolicy` может быть использовано в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad943-661">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ad943-662">Данное расширение предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:</span><span class="sxs-lookup"><span data-stu-id="ad943-662">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="ad943-663">В приведенном выше коде определена политика `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="ad943-663">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="ad943-664">Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.</span><span class="sxs-lookup"><span data-stu-id="ad943-664">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="ad943-665">Динамический выбор политик</span><span class="sxs-lookup"><span data-stu-id="ad943-665">Dynamically select policies</span></span>

<span data-ttu-id="ad943-666">Существуют дополнительные методы расширения, которые можно использовать для добавления обработчиков на основе Polly.</span><span class="sxs-lookup"><span data-stu-id="ad943-666">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="ad943-667">Одним из таких расширений является `AddPolicyHandler` с несколькими перегрузками.</span><span class="sxs-lookup"><span data-stu-id="ad943-667">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="ad943-668">Одна перегрузка разрешает проверку запроса для определения необходимой политики:</span><span class="sxs-lookup"><span data-stu-id="ad943-668">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="ad943-669">Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad943-669">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="ad943-670">Для остальных методов HTTP время ожидания — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad943-670">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="ad943-671">Добавление нескольких обработчиков Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-671">Add multiple Polly handlers</span></span>

<span data-ttu-id="ad943-672">Общепринятой практикой является вложение политик Polly для предоставления расширенной функциональности:</span><span class="sxs-lookup"><span data-stu-id="ad943-672">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="ad943-673">В приведенном выше примере добавляются два обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad943-673">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="ad943-674">Первый использует расширение `AddTransientHttpErrorPolicy`, чтобы добавить политику повтора.</span><span class="sxs-lookup"><span data-stu-id="ad943-674">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="ad943-675">Неудачные запросы выполняются повторно до трех раз.</span><span class="sxs-lookup"><span data-stu-id="ad943-675">Failed requests are retried up to three times.</span></span> <span data-ttu-id="ad943-676">Второй вызов к `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи.</span><span class="sxs-lookup"><span data-stu-id="ad943-676">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="ad943-677">Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд.</span><span class="sxs-lookup"><span data-stu-id="ad943-677">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="ad943-678">Политики размыкателя цепи отслеживают состояние.</span><span class="sxs-lookup"><span data-stu-id="ad943-678">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="ad943-679">Все вызовы через этот клиент имеют одинаковое состояние цепи.</span><span class="sxs-lookup"><span data-stu-id="ad943-679">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="ad943-680">Добавление политик из реестра Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-680">Add policies from the Polly registry</span></span>

<span data-ttu-id="ad943-681">Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="ad943-681">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="ad943-682">Предоставляется метод расширения, разрешающий добавление обработчика с помощью политики из реестра:</span><span class="sxs-lookup"><span data-stu-id="ad943-682">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="ad943-683">В приведенном выше коде, когда `PolicyRegistry` добавляется в `ServiceCollection`, регистрируются две политики.</span><span class="sxs-lookup"><span data-stu-id="ad943-683">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="ad943-684">Чтобы использовать политику из реестра, применяется метод `AddPolicyHandlerFromRegistry`, который передает имя необходимой политики.</span><span class="sxs-lookup"><span data-stu-id="ad943-684">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="ad943-685">Дополнительные сведения об интеграции `IHttpClientFactory` и Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="ad943-685">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="ad943-686">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="ad943-686">HttpClient and lifetime management</span></span>

<span data-ttu-id="ad943-687">При каждом вызове `CreateClient` в `IHttpClientFactory` возвращается новый экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-687">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="ad943-688">Для каждого названного клиента существует <xref:System.Net.Http.HttpMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad943-688">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="ad943-689">Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-689">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="ad943-690">`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов.</span><span class="sxs-lookup"><span data-stu-id="ad943-690">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="ad943-691">Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.</span><span class="sxs-lookup"><span data-stu-id="ad943-691">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="ad943-692">Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями.</span><span class="sxs-lookup"><span data-stu-id="ad943-692">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="ad943-693">Создание лишних обработчиков может привести к задержке подключения.</span><span class="sxs-lookup"><span data-stu-id="ad943-693">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="ad943-694">Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения DNS.</span><span class="sxs-lookup"><span data-stu-id="ad943-694">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="ad943-695">Время существования обработчика по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="ad943-695">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="ad943-696">Значение по умолчанию можно переопределить для каждого именованного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-696">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="ad943-697">Чтобы переопределить это значение, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> в `IHttpClientBuilder`, который возвращается при создании клиента:</span><span class="sxs-lookup"><span data-stu-id="ad943-697">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="ad943-698">Высвобождать клиент не требуется.</span><span class="sxs-lookup"><span data-stu-id="ad943-698">Disposal of the client isn't required.</span></span> <span data-ttu-id="ad943-699">Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-699">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="ad943-700">`IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-700">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="ad943-701">Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, не требующие высвобождения.</span><span class="sxs-lookup"><span data-stu-id="ad943-701">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="ad943-702">До появления `IHttpClientFactory` один экземпляр `HttpClient` часто сохраняли в активном состоянии в течение длительного времени.</span><span class="sxs-lookup"><span data-stu-id="ad943-702">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="ad943-703">После перехода на `IHttpClientFactory` это уже не нужно.</span><span class="sxs-lookup"><span data-stu-id="ad943-703">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="ad943-704">Альтернативы интерфейсу IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="ad943-704">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="ad943-705">Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:</span><span class="sxs-lookup"><span data-stu-id="ad943-705">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="ad943-706">предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;</span><span class="sxs-lookup"><span data-stu-id="ad943-706">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="ad943-707">предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-707">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="ad943-708">Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad943-708">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="ad943-709">Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.</span><span class="sxs-lookup"><span data-stu-id="ad943-709">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="ad943-710">Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad943-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="ad943-711">По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="ad943-711">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="ad943-712">Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.</span><span class="sxs-lookup"><span data-stu-id="ad943-712">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="ad943-713">`SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-713">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="ad943-714">Этот позволяет предотвратить нехватку сокетов.</span><span class="sxs-lookup"><span data-stu-id="ad943-714">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="ad943-715">`SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad943-715">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="ad943-716">Cookies</span><span class="sxs-lookup"><span data-stu-id="ad943-716">Cookies</span></span>

<span data-ttu-id="ad943-717">Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="ad943-717">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="ad943-718">Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде.</span><span class="sxs-lookup"><span data-stu-id="ad943-718">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="ad943-719">Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ad943-719">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="ad943-720">отключите автоматическую обработку файлов cookie;</span><span class="sxs-lookup"><span data-stu-id="ad943-720">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="ad943-721">не используйте `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad943-721">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="ad943-722">Чтобы отключить автоматическую обработку файлов <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>, вызовите cookie:</span><span class="sxs-lookup"><span data-stu-id="ad943-722">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="ad943-723">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="ad943-723">Logging</span></span>

<span data-ttu-id="ad943-724">Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-724">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="ad943-725">Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ad943-725">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="ad943-726">Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.</span><span class="sxs-lookup"><span data-stu-id="ad943-726">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="ad943-727">Категория журнала для каждого клиента включает в себя имя клиента.</span><span class="sxs-lookup"><span data-stu-id="ad943-727">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="ad943-728">Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-728">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="ad943-729">Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-729">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="ad943-730">Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad943-730">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="ad943-731">Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad943-731">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="ad943-732">Кроме того, журнал ведется в конвейере обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad943-732">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="ad943-733">В примере *MyNamedClient* эти сообщения вносятся в журнал по категории журнала `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad943-733">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="ad943-734">Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса по сети.</span><span class="sxs-lookup"><span data-stu-id="ad943-734">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="ad943-735">Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.</span><span class="sxs-lookup"><span data-stu-id="ad943-735">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="ad943-736">Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера.</span><span class="sxs-lookup"><span data-stu-id="ad943-736">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="ad943-737">Сюда входят, например, изменения заголовков запросов или кода состояния ответов.</span><span class="sxs-lookup"><span data-stu-id="ad943-737">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="ad943-738">Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам при необходимости.</span><span class="sxs-lookup"><span data-stu-id="ad943-738">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="ad943-739">Настройка HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ad943-739">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="ad943-740">Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.</span><span class="sxs-lookup"><span data-stu-id="ad943-740">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="ad943-741">При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ad943-741">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="ad943-742">Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>.</span><span class="sxs-lookup"><span data-stu-id="ad943-742">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="ad943-743">Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:</span><span class="sxs-lookup"><span data-stu-id="ad943-743">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="ad943-744">Использование IHttpClientFactory в консольном приложении</span><span class="sxs-lookup"><span data-stu-id="ad943-744">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="ad943-745">В консольном приложении добавьте в проект следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="ad943-745">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="ad943-746">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="ad943-746">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="ad943-747">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="ad943-747">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="ad943-748">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ad943-748">In the following example:</span></span>

* <span data-ttu-id="ad943-749"><xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):</span><span class="sxs-lookup"><span data-stu-id="ad943-749"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="ad943-750">`MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad943-750">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="ad943-751">`HttpClient` используется для получения веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="ad943-751">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="ad943-752">`Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.</span><span class="sxs-lookup"><span data-stu-id="ad943-752">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="ad943-753">ПО промежуточного слоя для распространения заголовков</span><span class="sxs-lookup"><span data-stu-id="ad943-753">Header propagation middleware</span></span>

<span data-ttu-id="ad943-754">Header propagation — это поддерживаемое сообществом ПО промежуточного слоя для распространения HTTP-заголовков из входящего запроса на исходящие запросы HTTP-клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad943-754">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="ad943-755">Чтобы использовать распространение заголовков, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ad943-755">To use header propagation:</span></span>

* <span data-ttu-id="ad943-756">Укажите ссылку на поддерживаемый сообществом порт пакета [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="ad943-756">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="ad943-757">ASP.NET Core 3.1 и более поздних версий поддерживает [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="ad943-757">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="ad943-758">Настройте ПО промежуточного слоя и `HttpClient` в `Startup`:</span><span class="sxs-lookup"><span data-stu-id="ad943-758">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="ad943-759">Клиент включает настроенные заголовки в исходящие запросы:</span><span class="sxs-lookup"><span data-stu-id="ad943-759">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="ad943-760">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ad943-760">Additional resources</span></span>

* [<span data-ttu-id="ad943-761">Использование HttpClientFactory для реализации устойчивых HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="ad943-761">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="ad943-762">Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly</span><span class="sxs-lookup"><span data-stu-id="ad943-762">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="ad943-763">Реализация шаблона размыкателя цепи</span><span class="sxs-lookup"><span data-stu-id="ad943-763">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
