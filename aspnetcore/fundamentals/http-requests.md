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
ms.openlocfilehash: 2bc093af63f305dd9808e37011223043646852d5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588052"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="02c23-103">Выполнения HTTP-запросов с помощью IHttpClientFactory в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02c23-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02c23-104">Авторы [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin), [Стив Гордон](https://github.com/stevejgordon) (Steve Gordon), [Гленн Кондрон](https://github.com/glennc) (Glenn Condron) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak).</span><span class="sxs-lookup"><span data-stu-id="02c23-104">By [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc), and [Ryan Nowak](https://github.com/rynowak).</span></span>

<span data-ttu-id="02c23-105"><xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении.</span><span class="sxs-lookup"><span data-stu-id="02c23-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="02c23-106">`IHttpClientFactory` предоставляет следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="02c23-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="02c23-107">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="02c23-108">Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="02c23-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="02c23-109">Можно зарегистрировать клиент по умолчанию для общего доступа.</span><span class="sxs-lookup"><span data-stu-id="02c23-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="02c23-110">Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="02c23-111">Предоставление расширений для ПО промежуточного слоя на основе Polly для делегирования обработчиков в `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="02c23-112">Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="02c23-113">Автоматическое управление позволяет избежать обычных проблем со службой доменных имен (DNS), которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="02c23-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="02c23-114">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="02c23-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="02c23-115">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="02c23-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="02c23-116">Пример кода в этой версии раздела использует <xref:System.Text.Json> для десериализации содержимого JSON, возвращаемого в ответах HTTP.</span><span class="sxs-lookup"><span data-stu-id="02c23-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="02c23-117">Для примеров, использующих `Json.NET` и `ReadAsAsync<T>`, воспользуйтесь средством выбора версии, чтобы выбрать версию 2.x этого раздела.</span><span class="sxs-lookup"><span data-stu-id="02c23-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="02c23-118">Принципы использования</span><span class="sxs-lookup"><span data-stu-id="02c23-118">Consumption patterns</span></span>

<span data-ttu-id="02c23-119">Существует несколько способов использования `IHttpClientFactory` в приложении:</span><span class="sxs-lookup"><span data-stu-id="02c23-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="02c23-120">Основное использование</span><span class="sxs-lookup"><span data-stu-id="02c23-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="02c23-121">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="02c23-122">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="02c23-123">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="02c23-124">Оптимальный подход зависит от требований приложения.</span><span class="sxs-lookup"><span data-stu-id="02c23-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="02c23-125">Основное использование</span><span class="sxs-lookup"><span data-stu-id="02c23-125">Basic usage</span></span>

<span data-ttu-id="02c23-126">`IHttpClientFactory` можно зарегистрировать, вызвав `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="02c23-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="02c23-127">`IHttpClientFactory` можно запросить с помощью [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="02c23-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="02c23-128">Следующий код использует `IHttpClientFactory` для создания экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="02c23-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="02c23-129">Подобное использование `IHttpClientFactory` — это хороший способ рефакторинга имеющегося приложения.</span><span class="sxs-lookup"><span data-stu-id="02c23-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="02c23-130">Он не оказывает влияния на использование `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="02c23-131">Там, где в существующем приложении создаются экземпляры `HttpClient`, используйте вызовы к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="02c23-132">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-132">Named clients</span></span>

<span data-ttu-id="02c23-133">Именованные клиенты являются хорошим выбором в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="02c23-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="02c23-134">Приложение требует много отдельных использований `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="02c23-135">Многие `HttpClient` имеют другую конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="02c23-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="02c23-136">Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="02c23-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="02c23-137">В приведенном выше коде клиент регистрируется с:</span><span class="sxs-lookup"><span data-stu-id="02c23-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="02c23-138">базовым адресом `https://api.github.com/`;</span><span class="sxs-lookup"><span data-stu-id="02c23-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="02c23-139">двумя заголовками, необходимыми для работы с API GitHub.</span><span class="sxs-lookup"><span data-stu-id="02c23-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="02c23-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="02c23-140">CreateClient</span></span>

<span data-ttu-id="02c23-141">При каждом вызове <xref:System.Net.Http.IHttpClientFactory.CreateClient*>:</span><span class="sxs-lookup"><span data-stu-id="02c23-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="02c23-142">создается новый экземпляр `HttpClient`;</span><span class="sxs-lookup"><span data-stu-id="02c23-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="02c23-143">вызывается действие настройки.</span><span class="sxs-lookup"><span data-stu-id="02c23-143">The configuration action is called.</span></span>

<span data-ttu-id="02c23-144">Чтобы создать именованный клиент, передайте его имя в `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="02c23-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="02c23-145">В приведенном выше коде в запросе не требуется указывать имя узла.</span><span class="sxs-lookup"><span data-stu-id="02c23-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="02c23-146">Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="02c23-147">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-147">Typed clients</span></span>

<span data-ttu-id="02c23-148">Типизированные клиенты:</span><span class="sxs-lookup"><span data-stu-id="02c23-148">Typed clients:</span></span>

* <span data-ttu-id="02c23-149">предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.</span><span class="sxs-lookup"><span data-stu-id="02c23-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="02c23-150">Это помогает IntelliSense и компилятору при использовании клиентов.</span><span class="sxs-lookup"><span data-stu-id="02c23-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="02c23-151">Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="02c23-152">Например, один типизированный клиент можно использовать:</span><span class="sxs-lookup"><span data-stu-id="02c23-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="02c23-153">для одной серверной конечной точки;</span><span class="sxs-lookup"><span data-stu-id="02c23-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="02c23-154">для инкапсуляции всей логики, связанной с конечной точкой.</span><span class="sxs-lookup"><span data-stu-id="02c23-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="02c23-155">Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.</span><span class="sxs-lookup"><span data-stu-id="02c23-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="02c23-156">Типизированный клиент принимает параметр `HttpClient` в конструкторе:</span><span class="sxs-lookup"><span data-stu-id="02c23-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="02c23-157">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="02c23-157">In the preceding code:</span></span>

* <span data-ttu-id="02c23-158">Конфигурация перемещается в типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="02c23-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="02c23-159">Объект `HttpClient` предоставляется в виде открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="02c23-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="02c23-160">Можно создать связанные с API методы, которые предоставляют функциональные возможности `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="02c23-161">Например, метод `GetAspNetDocsIssues` инкапсулирует код для получения открытых вопросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="02c23-162">Следующий код вызывает <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices` для регистрации класса типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="02c23-163">Типизированный клиент регистрируется во внедрении зависимостей как временный.</span><span class="sxs-lookup"><span data-stu-id="02c23-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="02c23-164">В приведенном выше коде `AddHttpClient` регистрирует `GitHubService` как временную службу.</span><span class="sxs-lookup"><span data-stu-id="02c23-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="02c23-165">Эта регистрация использует фабричный метод для следующих задач:</span><span class="sxs-lookup"><span data-stu-id="02c23-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="02c23-166">Создание экземпляра `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="02c23-167">Создайте экземпляр `GitHubService`, передав его конструктору экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="02c23-168">Типизированный клиент можно внедрить и использовать напрямую:</span><span class="sxs-lookup"><span data-stu-id="02c23-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="02c23-169">Конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="02c23-170">`HttpClient` может быть инкапсулирован в типизированном клиенте.</span><span class="sxs-lookup"><span data-stu-id="02c23-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="02c23-171">Вместо предоставления его как свойства определите метод для внутреннего вызова экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="02c23-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="02c23-172">В приведенном выше коде `HttpClient` хранится в закрытом поле.</span><span class="sxs-lookup"><span data-stu-id="02c23-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="02c23-173">Доступ к `HttpClient` осуществляется с помощью общедоступного метода `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="02c23-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="02c23-174">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-174">Generated clients</span></span>

<span data-ttu-id="02c23-175">`IHttpClientFactory` можно использовать в сочетании с библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="02c23-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="02c23-176">Refit — это библиотека REST для .NET.</span><span class="sxs-lookup"><span data-stu-id="02c23-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="02c23-177">Она преобразует REST API в динамические интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="02c23-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="02c23-178">Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.</span><span class="sxs-lookup"><span data-stu-id="02c23-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="02c23-179">Для представления внешнего API и его ответа определяются интерфейс и ответ:</span><span class="sxs-lookup"><span data-stu-id="02c23-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="02c23-180">Можно добавить типизированный клиент, используя Refit для создания реализации:</span><span class="sxs-lookup"><span data-stu-id="02c23-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="02c23-181">При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:</span><span class="sxs-lookup"><span data-stu-id="02c23-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="02c23-182">Выполнение запросов POST, PUT и DELETE</span><span class="sxs-lookup"><span data-stu-id="02c23-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="02c23-183">В предыдущих примерах все HTTP-запросы используют HTTP-команду GET.</span><span class="sxs-lookup"><span data-stu-id="02c23-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="02c23-184">`HttpClient` также поддерживает другие HTTP-команды, в том числе:</span><span class="sxs-lookup"><span data-stu-id="02c23-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="02c23-185">ПОМЕСТИТЬ</span><span class="sxs-lookup"><span data-stu-id="02c23-185">POST</span></span>
* <span data-ttu-id="02c23-186">PUT</span><span class="sxs-lookup"><span data-stu-id="02c23-186">PUT</span></span>
* <span data-ttu-id="02c23-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="02c23-187">DELETE</span></span>
* <span data-ttu-id="02c23-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="02c23-188">PATCH</span></span>

<span data-ttu-id="02c23-189">Полный список поддерживаемых HTTP-команд см. в статье <xref:System.Net.Http.HttpMethod>.</span><span class="sxs-lookup"><span data-stu-id="02c23-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="02c23-190">В следующем примере показано, как выполнить HTTP-запрос POST:</span><span class="sxs-lookup"><span data-stu-id="02c23-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="02c23-191">В приведенном выше коде метод `CreateItemAsync` выполняет следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="02c23-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="02c23-192">сериализует параметр `TodoItem` в JSON с помощью `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="02c23-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="02c23-193">Для настройки процесса сериализации используется экземпляр <xref:System.Text.Json.JsonSerializerOptions>.</span><span class="sxs-lookup"><span data-stu-id="02c23-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="02c23-194">создает экземпляр <xref:System.Net.Http.StringContent> для упаковки сериализованного JSON для отправки в тексте HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="02c23-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="02c23-195">вызывает метод <xref:System.Net.Http.HttpClient.PostAsync%2A> для отправки содержимого JSON по указанному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="02c23-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="02c23-196">Это относительный URL-адрес, который добавляется в свойство [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="02c23-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="02c23-197">вызывает метод <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>, чтобы создавать исключение, если код состояния ответа означает неудачное выполнение.</span><span class="sxs-lookup"><span data-stu-id="02c23-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="02c23-198">`HttpClient` также поддерживает другие типы содержимого.</span><span class="sxs-lookup"><span data-stu-id="02c23-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="02c23-199">Например, <xref:System.Net.Http.MultipartContent> и <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="02c23-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="02c23-200">Полный список поддерживаемого содержимого см. в статье <xref:System.Net.Http.HttpContent>.</span><span class="sxs-lookup"><span data-stu-id="02c23-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="02c23-201">Ниже приводится пример HTTP-запроса PUT.</span><span class="sxs-lookup"><span data-stu-id="02c23-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="02c23-202">Приведенный выше код практически аналогичен коду в примере с POST.</span><span class="sxs-lookup"><span data-stu-id="02c23-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="02c23-203">Метод `SaveItemAsync` вызывает <xref:System.Net.Http.HttpClient.PutAsync%2A> вместо `PostAsync`.</span><span class="sxs-lookup"><span data-stu-id="02c23-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="02c23-204">Ниже приводится пример HTTP-запроса DELETE.</span><span class="sxs-lookup"><span data-stu-id="02c23-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="02c23-205">В приведенном выше коде метод `DeleteItemAsync` вызывает <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="02c23-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="02c23-206">Поскольку HTTP-запросы DELETE обычно не содержат текст, метод `DeleteAsync` не предоставляет перегрузку, которая принимает экземпляр `HttpContent`.</span><span class="sxs-lookup"><span data-stu-id="02c23-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="02c23-207">Дополнительные сведения об использовании различных HTTP-команд с `HttpClient` см. в статье <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="02c23-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="02c23-208">ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="02c23-208">Outgoing request middleware</span></span>

<span data-ttu-id="02c23-209">В `HttpClient` существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="02c23-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="02c23-210">`IHttpClientFactory`:</span></span>

  * <span data-ttu-id="02c23-211">Упрощает определение обработчиков для применения к каждому именованному клиенту.</span><span class="sxs-lookup"><span data-stu-id="02c23-211">Simplifies defining the handlers to apply for each named client.</span></span>
  * <span data-ttu-id="02c23-212">Поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-212">Supports registration and chaining of multiple handlers to build an outgoing request   middleware pipeline.</span></span> <span data-ttu-id="02c23-213">Каждый из этих обработчиков может выполнять работу до и после исходящего запроса.</span><span class="sxs-lookup"><span data-stu-id="02c23-213">Each of these handlers is able to perform work before and after the   outgoing request.</span></span> <span data-ttu-id="02c23-214">Этот шаблон:</span><span class="sxs-lookup"><span data-stu-id="02c23-214">This pattern:</span></span>
  
    * <span data-ttu-id="02c23-215">похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="02c23-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
    * <span data-ttu-id="02c23-216">предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая:</span><span class="sxs-lookup"><span data-stu-id="02c23-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>
      * <span data-ttu-id="02c23-217">кэширование</span><span class="sxs-lookup"><span data-stu-id="02c23-217">caching</span></span>
      * <span data-ttu-id="02c23-218">обработку ошибок</span><span class="sxs-lookup"><span data-stu-id="02c23-218">error handling</span></span>
      * <span data-ttu-id="02c23-219">сериализацию</span><span class="sxs-lookup"><span data-stu-id="02c23-219">serialization</span></span>
      * <span data-ttu-id="02c23-220">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="02c23-220">logging</span></span>

<span data-ttu-id="02c23-221">Чтобы создать делегированный обработчик, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="02c23-221">To create a delegating handler:</span></span>

* <span data-ttu-id="02c23-222">Создайте объект, производный от <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="02c23-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="02c23-223">Переопределите метод <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="02c23-224">Выполните код до передачи запроса следующему обработчику в конвейере:</span><span class="sxs-lookup"><span data-stu-id="02c23-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="02c23-225">Предыдущий код проверяет, находится ли заголовок `X-API-KEY` в запросе.</span><span class="sxs-lookup"><span data-stu-id="02c23-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="02c23-226">Если `X-API-KEY` отсутствует, возвращается <xref:System.Net.HttpStatusCode.BadRequest>.</span><span class="sxs-lookup"><span data-stu-id="02c23-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="02c23-227">Можно добавить сразу несколько обработчиков в конфигурацию для `HttpClient` с использованием <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="02c23-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="02c23-228">В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="02c23-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="02c23-229">После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-229">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="02c23-230">Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться.</span><span class="sxs-lookup"><span data-stu-id="02c23-230">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="02c23-231">Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:</span><span class="sxs-lookup"><span data-stu-id="02c23-231">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a><span data-ttu-id="02c23-232">Использование внедрения зависимостей в ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="02c23-232">Use DI in outgoing request middleware</span></span>

<span data-ttu-id="02c23-233">Когда `IHttpClientFactory` создает новый делегированный обработчик, он использует внедрение зависимостей для выполнения параметров конструктора обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-233">When `IHttpClientFactory` creates a new delegating handler, it uses DI to fulfill the handler's constructor parameters.</span></span> <span data-ttu-id="02c23-234">`IHttpClientFactory` создает **отдельную** область внедрения зависимостей для каждого обработчика, что может стать причиной аномального поведения, когда обработчик использует службу с *назначенной областью*.</span><span class="sxs-lookup"><span data-stu-id="02c23-234">`IHttpClientFactory` creates a **separate** DI scope for each handler, which can lead to surprising behavior when a handler consumes a *scoped* service.</span></span>

<span data-ttu-id="02c23-235">Например, рассмотрим следующий интерфейс и его реализацию, которая представляет задачу в виде операции с идентификатором `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="02c23-235">For example, consider the following interface and its implementation, which represents a task as an operation with an identifier, `OperationId`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

<span data-ttu-id="02c23-236">Как можно понять из названия, `IOperationScoped` регистрируется с помощью внедрения зависимостей с использованием времени существования с *назначенной областью*:</span><span class="sxs-lookup"><span data-stu-id="02c23-236">As its name suggests, `IOperationScoped` is registered with DI using a *scoped* lifetime:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

<span data-ttu-id="02c23-237">Следующий делегирующий обработчик принимает и использует `IOperationScoped` для задания заголовка `X-OPERATION-ID` для исходящего запроса:</span><span class="sxs-lookup"><span data-stu-id="02c23-237">The following delegating handler consumes and uses `IOperationScoped` to set the `X-OPERATION-ID` header for the outgoing request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

<span data-ttu-id="02c23-238">В [скачиваемом ресурсе `HttpRequestsSample`](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)] перейдите к `/Operation` и обновите страницу.</span><span class="sxs-lookup"><span data-stu-id="02c23-238">In the [`HttpRequestsSample` download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navigate to `/Operation` and refresh the page.</span></span> <span data-ttu-id="02c23-239">Значение области запроса изменяется с каждым запросом, но значение области обработчика изменяется только каждые 5 секунд.</span><span class="sxs-lookup"><span data-stu-id="02c23-239">The request scope value changes for each request, but the handler scope value only changes every 5 seconds.</span></span>

<span data-ttu-id="02c23-240">Обработчики могут зависеть от служб из любой области.</span><span class="sxs-lookup"><span data-stu-id="02c23-240">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="02c23-241">Службы, которые зависят от обработчиков, удаляются при удалении обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-241">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="02c23-242">Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:</span><span class="sxs-lookup"><span data-stu-id="02c23-242">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="02c23-243">Передайте данные в обработчик с помощью [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="02c23-243">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="02c23-244">Используйте <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> для доступа к текущему запросу.</span><span class="sxs-lookup"><span data-stu-id="02c23-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="02c23-245">Создайте пользовательский объект хранилища <xref:System.Threading.AsyncLocal`1> для передачи данных.</span><span class="sxs-lookup"><span data-stu-id="02c23-245">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="02c23-246">Использование обработчиков на основе Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-246">Use Polly-based handlers</span></span>

<span data-ttu-id="02c23-247">`IHttpClientFactory` интегрируется с библиотекой сторонних разработчиков [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="02c23-247">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="02c23-248">Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET.</span><span class="sxs-lookup"><span data-stu-id="02c23-248">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="02c23-249">Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.</span><span class="sxs-lookup"><span data-stu-id="02c23-249">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="02c23-250">Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения.</span><span class="sxs-lookup"><span data-stu-id="02c23-250">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="02c23-251">Расширения Polly поддерживают добавление обработчиков на основе Polly клиентам.</span><span class="sxs-lookup"><span data-stu-id="02c23-251">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="02c23-252">Polly нужен пакет NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="02c23-252">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="02c23-253">Обработка временных сбоев</span><span class="sxs-lookup"><span data-stu-id="02c23-253">Handle transient faults</span></span>

<span data-ttu-id="02c23-254">Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными.</span><span class="sxs-lookup"><span data-stu-id="02c23-254">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="02c23-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> позволяет определить политику для обработки временных ошибок.</span><span class="sxs-lookup"><span data-stu-id="02c23-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="02c23-256">Политики, настроенные с помощью `AddTransientHttpErrorPolicy`, обрабатывают следующие ответы:</span><span class="sxs-lookup"><span data-stu-id="02c23-256">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="02c23-257">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="02c23-257">HTTP 5xx</span></span>
* <span data-ttu-id="02c23-258">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="02c23-258">HTTP 408</span></span>

<span data-ttu-id="02c23-259">`AddTransientHttpErrorPolicy` предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:</span><span class="sxs-lookup"><span data-stu-id="02c23-259">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="02c23-260">В приведенном выше коде определена политика `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="02c23-260">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="02c23-261">Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.</span><span class="sxs-lookup"><span data-stu-id="02c23-261">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="02c23-262">Динамический выбор политик</span><span class="sxs-lookup"><span data-stu-id="02c23-262">Dynamically select policies</span></span>

<span data-ttu-id="02c23-263">Предоставляются методы расширения для добавления обработчиков на основе Polly, например <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-263">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="02c23-264">Следующая перегрузка `AddPolicyHandler` проверяет запрос для определения применимой политики:</span><span class="sxs-lookup"><span data-stu-id="02c23-264">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="02c23-265">Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="02c23-265">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="02c23-266">Для остальных методов HTTP время ожидания — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="02c23-266">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="02c23-267">Добавление нескольких обработчиков Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-267">Add multiple Polly handlers</span></span>

<span data-ttu-id="02c23-268">Общепринятой практикой является вложение политик Polly:</span><span class="sxs-lookup"><span data-stu-id="02c23-268">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="02c23-269">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="02c23-269">In the preceding example:</span></span>

* <span data-ttu-id="02c23-270">Добавляются два обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-270">Two handlers are added.</span></span>
* <span data-ttu-id="02c23-271">Первый обработчик использует <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>, чтобы добавить политику повтора.</span><span class="sxs-lookup"><span data-stu-id="02c23-271">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="02c23-272">Неудачные запросы выполняются повторно до трех раз.</span><span class="sxs-lookup"><span data-stu-id="02c23-272">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="02c23-273">Второй вызов `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи.</span><span class="sxs-lookup"><span data-stu-id="02c23-273">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="02c23-274">Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд.</span><span class="sxs-lookup"><span data-stu-id="02c23-274">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="02c23-275">Политики размыкателя цепи отслеживают состояние.</span><span class="sxs-lookup"><span data-stu-id="02c23-275">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="02c23-276">Все вызовы через этот клиент имеют одинаковое состояние цепи.</span><span class="sxs-lookup"><span data-stu-id="02c23-276">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="02c23-277">Добавление политик из реестра Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-277">Add policies from the Polly registry</span></span>

<span data-ttu-id="02c23-278">Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="02c23-278">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="02c23-279">В приведенном ниже коде выполняется следующее:</span><span class="sxs-lookup"><span data-stu-id="02c23-279">In the following code:</span></span>

* <span data-ttu-id="02c23-280">Добавляются политики regular и long.</span><span class="sxs-lookup"><span data-stu-id="02c23-280">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="02c23-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> добавляет политики regular и long из реестра.</span><span class="sxs-lookup"><span data-stu-id="02c23-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="02c23-282">Дополнительные сведения о `IHttpClientFactory` и интеграции Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="02c23-282">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="02c23-283">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="02c23-283">HttpClient and lifetime management</span></span>

<span data-ttu-id="02c23-284">При каждом вызове `CreateClient` в `IHttpClientFactory` возвращается новый экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-284">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="02c23-285"><xref:System.Net.Http.HttpMessageHandler> создается для каждого именованного клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-285">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="02c23-286">Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-286">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="02c23-287">`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов.</span><span class="sxs-lookup"><span data-stu-id="02c23-287">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="02c23-288">Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.</span><span class="sxs-lookup"><span data-stu-id="02c23-288">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="02c23-289">Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями.</span><span class="sxs-lookup"><span data-stu-id="02c23-289">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="02c23-290">Создание лишних обработчиков может привести к задержке подключения.</span><span class="sxs-lookup"><span data-stu-id="02c23-290">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="02c23-291">Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения службы доменных имен (DNS).</span><span class="sxs-lookup"><span data-stu-id="02c23-291">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="02c23-292">Время существования обработчика по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="02c23-292">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="02c23-293">Значение по умолчанию можно переопределить для каждого именованного клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-293">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="02c23-294">Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, **не** требующие освобождения.</span><span class="sxs-lookup"><span data-stu-id="02c23-294">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="02c23-295">Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-295">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="02c23-296">`IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-296">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="02c23-297">До появления `IHttpClientFactory` один экземпляр `HttpClient` часто сохраняли в активном состоянии в течение длительного времени.</span><span class="sxs-lookup"><span data-stu-id="02c23-297">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="02c23-298">После перехода на `IHttpClientFactory` это уже не нужно.</span><span class="sxs-lookup"><span data-stu-id="02c23-298">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="02c23-299">Альтернативы интерфейсу IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="02c23-299">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="02c23-300">Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:</span><span class="sxs-lookup"><span data-stu-id="02c23-300">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="02c23-301">предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;</span><span class="sxs-lookup"><span data-stu-id="02c23-301">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="02c23-302">предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-302">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="02c23-303">Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="02c23-303">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="02c23-304">Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.</span><span class="sxs-lookup"><span data-stu-id="02c23-304">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="02c23-305">Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.</span><span class="sxs-lookup"><span data-stu-id="02c23-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="02c23-306">По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="02c23-306">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="02c23-307">Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.</span><span class="sxs-lookup"><span data-stu-id="02c23-307">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="02c23-308">`SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-308">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="02c23-309">Этот позволяет предотвратить нехватку сокетов.</span><span class="sxs-lookup"><span data-stu-id="02c23-309">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="02c23-310">`SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.</span><span class="sxs-lookup"><span data-stu-id="02c23-310">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="02c23-311">Cookies</span><span class="sxs-lookup"><span data-stu-id="02c23-311">Cookies</span></span>

<span data-ttu-id="02c23-312">Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="02c23-312">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="02c23-313">Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде.</span><span class="sxs-lookup"><span data-stu-id="02c23-313">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="02c23-314">Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="02c23-314">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="02c23-315">отключите автоматическую обработку файлов cookie;</span><span class="sxs-lookup"><span data-stu-id="02c23-315">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="02c23-316">не используйте `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="02c23-316">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="02c23-317">Чтобы отключить автоматическую обработку файлов <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>, вызовите cookie:</span><span class="sxs-lookup"><span data-stu-id="02c23-317">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="02c23-318">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="02c23-318">Logging</span></span>

<span data-ttu-id="02c23-319">Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-319">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="02c23-320">Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02c23-320">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="02c23-321">Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.</span><span class="sxs-lookup"><span data-stu-id="02c23-321">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="02c23-322">Категория журнала для каждого клиента включает в себя имя клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-322">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="02c23-323">Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="02c23-323">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="02c23-324">Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-324">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="02c23-325">Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="02c23-325">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="02c23-326">Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="02c23-326">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="02c23-327">Кроме того, журнал ведется в конвейере обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-327">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="02c23-328">В примере *MyNamedClient* эти сообщения регистрируются с категорией журнала "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="02c23-328">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="02c23-329">Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса.</span><span class="sxs-lookup"><span data-stu-id="02c23-329">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="02c23-330">Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.</span><span class="sxs-lookup"><span data-stu-id="02c23-330">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="02c23-331">Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера.</span><span class="sxs-lookup"><span data-stu-id="02c23-331">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="02c23-332">Сюда входят изменения заголовков запросов или кода состояния ответов.</span><span class="sxs-lookup"><span data-stu-id="02c23-332">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="02c23-333">Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам.</span><span class="sxs-lookup"><span data-stu-id="02c23-333">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="02c23-334">Настройка HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="02c23-334">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="02c23-335">Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.</span><span class="sxs-lookup"><span data-stu-id="02c23-335">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="02c23-336">При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="02c23-336">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="02c23-337">Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-337">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="02c23-338">Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:</span><span class="sxs-lookup"><span data-stu-id="02c23-338">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="02c23-339">Использование IHttpClientFactory в консольном приложении</span><span class="sxs-lookup"><span data-stu-id="02c23-339">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="02c23-340">В консольном приложении добавьте в проект следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="02c23-340">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="02c23-341">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="02c23-341">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="02c23-342">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="02c23-342">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="02c23-343">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="02c23-343">In the following example:</span></span>

* <span data-ttu-id="02c23-344"><xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):</span><span class="sxs-lookup"><span data-stu-id="02c23-344"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="02c23-345">`MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-345">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="02c23-346">`HttpClient` используется для получения веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="02c23-346">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="02c23-347">`Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.</span><span class="sxs-lookup"><span data-stu-id="02c23-347">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="02c23-348">ПО промежуточного слоя для распространения заголовков</span><span class="sxs-lookup"><span data-stu-id="02c23-348">Header propagation middleware</span></span>

<span data-ttu-id="02c23-349">Header propagation — это ПО промежуточного слоя ASP.NET Core для распространения HTTP-заголовков из входящего запроса на исходящие запросы HTTP-клиентов.</span><span class="sxs-lookup"><span data-stu-id="02c23-349">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="02c23-350">Чтобы использовать распространение заголовков, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="02c23-350">To use header propagation:</span></span>

* <span data-ttu-id="02c23-351">Укажите ссылку на пакет [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="02c23-351">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="02c23-352">Настройте ПО промежуточного слоя и `HttpClient` в `Startup`:</span><span class="sxs-lookup"><span data-stu-id="02c23-352">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="02c23-353">Клиент включает настроенные заголовки в исходящие запросы:</span><span class="sxs-lookup"><span data-stu-id="02c23-353">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="02c23-354">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="02c23-354">Additional resources</span></span>

* [<span data-ttu-id="02c23-355">Использование HttpClientFactory для реализации устойчивых HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="02c23-355">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="02c23-356">Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-356">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="02c23-357">Реализация шаблона размыкателя цепи</span><span class="sxs-lookup"><span data-stu-id="02c23-357">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="02c23-358">Сериализация и десериализация JSON в .NET</span><span class="sxs-lookup"><span data-stu-id="02c23-358">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="02c23-359">Авторы: [Гленн Кондрон (Glenn Condron)](https://github.com/glennc), [Райан Новак (Ryan Nowak)](https://github.com/rynowak) и [Стив Гордон (Steve Gordon)](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="02c23-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="02c23-360"><xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении.</span><span class="sxs-lookup"><span data-stu-id="02c23-360">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="02c23-361">Так вы получите следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="02c23-361">It offers the following benefits:</span></span>

* <span data-ttu-id="02c23-362">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-362">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="02c23-363">Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="02c23-363">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="02c23-364">Можно зарегистрировать клиент по умолчанию для других целей.</span><span class="sxs-lookup"><span data-stu-id="02c23-364">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="02c23-365">Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient` и предоставление расширений для ПО промежуточного слоя на основе Polly для использования этой возможности.</span><span class="sxs-lookup"><span data-stu-id="02c23-365">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="02c23-366">Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="02c23-366">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="02c23-367">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="02c23-367">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="02c23-368">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02c23-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="02c23-369">Принципы использования</span><span class="sxs-lookup"><span data-stu-id="02c23-369">Consumption patterns</span></span>

<span data-ttu-id="02c23-370">Существует несколько способов использования `IHttpClientFactory` в приложении:</span><span class="sxs-lookup"><span data-stu-id="02c23-370">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="02c23-371">Основное использование</span><span class="sxs-lookup"><span data-stu-id="02c23-371">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="02c23-372">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-372">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="02c23-373">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-373">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="02c23-374">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-374">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="02c23-375">Все способы равноценны.</span><span class="sxs-lookup"><span data-stu-id="02c23-375">None of them are strictly superior to another.</span></span> <span data-ttu-id="02c23-376">Оптимальный подход зависит от ограничений приложения.</span><span class="sxs-lookup"><span data-stu-id="02c23-376">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="02c23-377">Основное использование</span><span class="sxs-lookup"><span data-stu-id="02c23-377">Basic usage</span></span>

<span data-ttu-id="02c23-378">`IHttpClientFactory` можно зарегистрировать путем вызова метода расширения `AddHttpClient` в `IServiceCollection` внутри метода `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02c23-378">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="02c23-379">После регистрации код может принимать `IHttpClientFactory` в любом месте, куда можно внедрить службу с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="02c23-379">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="02c23-380">`IHttpClientFactory` можно использовать для создания экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="02c23-380">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="02c23-381">Подобное использование `IHttpClientFactory` — это отличный способ рефакторинга имеющегося приложения.</span><span class="sxs-lookup"><span data-stu-id="02c23-381">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="02c23-382">Он не оказывает влияния на использование `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-382">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="02c23-383">Там, где в данный момент создаются экземпляры `HttpClient`, используйте вызов к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-383">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="02c23-384">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-384">Named clients</span></span>

<span data-ttu-id="02c23-385">Если для приложения предполагаются разные способы использования `HttpClient`, каждый со своей конфигурацией, можно применять **именованные клиенты**.</span><span class="sxs-lookup"><span data-stu-id="02c23-385">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="02c23-386">Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02c23-386">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="02c23-387">В приведенном выше коде вызывается клиент `AddHttpClient`, предоставляющий имя *github*.</span><span class="sxs-lookup"><span data-stu-id="02c23-387">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="02c23-388">У клиента есть некоторые настройки по умолчанию &mdash; а именно: базовый адрес и два заголовка, необходимые для работы с API GitHub.</span><span class="sxs-lookup"><span data-stu-id="02c23-388">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="02c23-389">При каждом вызове `CreateClient` создается новый экземпляр `HttpClient` и вызывается действие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="02c23-389">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="02c23-390">Для использования именованного клиента можно передать строковый параметр в `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-390">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="02c23-391">Укажите имя создаваемого клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-391">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="02c23-392">В приведенном выше коде в запросе не требуется указывать имя узла.</span><span class="sxs-lookup"><span data-stu-id="02c23-392">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="02c23-393">Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-393">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="02c23-394">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-394">Typed clients</span></span>

<span data-ttu-id="02c23-395">Типизированные клиенты:</span><span class="sxs-lookup"><span data-stu-id="02c23-395">Typed clients:</span></span>

* <span data-ttu-id="02c23-396">предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.</span><span class="sxs-lookup"><span data-stu-id="02c23-396">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="02c23-397">Это помогает IntelliSense и компилятору при использовании клиентов.</span><span class="sxs-lookup"><span data-stu-id="02c23-397">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="02c23-398">Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-398">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="02c23-399">Например, для конечной точки серверной части можно использовать один типизированный клиент, который будет содержать всю логику работы с этой конечной точкой.</span><span class="sxs-lookup"><span data-stu-id="02c23-399">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="02c23-400">Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.</span><span class="sxs-lookup"><span data-stu-id="02c23-400">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="02c23-401">Типизированный клиент принимает параметр `HttpClient` в конструкторе:</span><span class="sxs-lookup"><span data-stu-id="02c23-401">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="02c23-402">В приведенном выше коде конфигурация перемещается в типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="02c23-402">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="02c23-403">Объект `HttpClient` предоставляется в виде открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="02c23-403">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="02c23-404">Можно определить связанные с API методы, которые предоставляют функциональные возможности `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-404">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="02c23-405">Метод `GetAspNetDocsIssues` инкапсулирует код, необходимый для запроса и анализа последнего открытого выпуска из репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="02c23-405">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="02c23-406">Для регистрации типизированного клиента можно использовать универсальный метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices`, указав класс типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-406">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="02c23-407">Типизированный клиент регистрируется во внедрении зависимостей как временный.</span><span class="sxs-lookup"><span data-stu-id="02c23-407">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="02c23-408">Типизированный клиент можно внедрить и использовать напрямую:</span><span class="sxs-lookup"><span data-stu-id="02c23-408">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="02c23-409">При желании конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-409">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="02c23-410">Можно полностью инкапсулировать `HttpClient` внутри типизированного клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-410">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="02c23-411">Вместо предоставления его как свойства можно использовать открытые методы для внутреннего вызова экземпляра `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-411">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="02c23-412">В приведенном выше коде `HttpClient` хранится как закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="02c23-412">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="02c23-413">Любой доступ для совершения внешних вызовов осуществляется через метод `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="02c23-413">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="02c23-414">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-414">Generated clients</span></span>

<span data-ttu-id="02c23-415">`IHttpClientFactory` можно использовать в сочетании с другими библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="02c23-415">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="02c23-416">Refit — это библиотека REST для .NET.</span><span class="sxs-lookup"><span data-stu-id="02c23-416">Refit is a REST library for .NET.</span></span> <span data-ttu-id="02c23-417">Она преобразует REST API в динамические интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="02c23-417">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="02c23-418">Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.</span><span class="sxs-lookup"><span data-stu-id="02c23-418">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="02c23-419">Для представления внешнего API и его ответа определяются интерфейс и ответ:</span><span class="sxs-lookup"><span data-stu-id="02c23-419">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="02c23-420">Можно добавить типизированный клиент, используя Refit для создания реализации:</span><span class="sxs-lookup"><span data-stu-id="02c23-420">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="02c23-421">При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:</span><span class="sxs-lookup"><span data-stu-id="02c23-421">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="02c23-422">ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="02c23-422">Outgoing request middleware</span></span>

<span data-ttu-id="02c23-423">В `HttpClient` уже существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-423">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="02c23-424">Класс `IHttpClientFactory` упрощает определение обработчиков для применения к каждому именованному клиенту.</span><span class="sxs-lookup"><span data-stu-id="02c23-424">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="02c23-425">Он поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-425">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="02c23-426">Каждый из этих обработчиков может выполнять работу до и после исходящего запроса.</span><span class="sxs-lookup"><span data-stu-id="02c23-426">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="02c23-427">Этот шаблон похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02c23-427">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="02c23-428">Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="02c23-428">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="02c23-429">Чтобы создать обработчик, необходимо определить класс, производный от <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="02c23-429">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="02c23-430">Переопределите метод `SendAsync` для выполнения кода до передачи запросов следующему обработчику в конвейере:</span><span class="sxs-lookup"><span data-stu-id="02c23-430">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="02c23-431">В предыдущем коде определяется базовый обработчик.</span><span class="sxs-lookup"><span data-stu-id="02c23-431">The preceding code defines a basic handler.</span></span> <span data-ttu-id="02c23-432">Он проверяет, включен ли в запрос заголовок `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="02c23-432">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="02c23-433">Если заголовок отсутствует, он может избежать вызовов HTTP и вернуть подходящий ответ.</span><span class="sxs-lookup"><span data-stu-id="02c23-433">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="02c23-434">Во время регистрации можно добавить один или несколько обработчиков в конфигурацию для `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-434">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="02c23-435">Эта задача выполняется через методы расширения в <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="02c23-435">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="02c23-436">В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="02c23-436">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="02c23-437">`IHttpClientFactory` создает отдельную область внедрения зависимостей для каждого обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-437">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="02c23-438">Обработчики могут зависеть от служб из любой области.</span><span class="sxs-lookup"><span data-stu-id="02c23-438">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="02c23-439">Службы, которые зависят от обработчиков, удаляются при удалении обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-439">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="02c23-440">После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-440">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="02c23-441">Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться.</span><span class="sxs-lookup"><span data-stu-id="02c23-441">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="02c23-442">Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:</span><span class="sxs-lookup"><span data-stu-id="02c23-442">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="02c23-443">Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:</span><span class="sxs-lookup"><span data-stu-id="02c23-443">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="02c23-444">Передайте данные в обработчик с помощью `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="02c23-444">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="02c23-445">Используйте `IHttpContextAccessor` для доступа к текущему запросу.</span><span class="sxs-lookup"><span data-stu-id="02c23-445">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="02c23-446">Создайте пользовательский объект хранилища `AsyncLocal` для передачи данных.</span><span class="sxs-lookup"><span data-stu-id="02c23-446">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="02c23-447">Использование обработчиков на основе Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-447">Use Polly-based handlers</span></span>

<span data-ttu-id="02c23-448">`IHttpClientFactory` интегрируется с популярной библиотекой сторонних разработчиков под названием [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="02c23-448">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="02c23-449">Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET.</span><span class="sxs-lookup"><span data-stu-id="02c23-449">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="02c23-450">Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.</span><span class="sxs-lookup"><span data-stu-id="02c23-450">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="02c23-451">Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения.</span><span class="sxs-lookup"><span data-stu-id="02c23-451">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="02c23-452">Расширения Polly:</span><span class="sxs-lookup"><span data-stu-id="02c23-452">The Polly extensions:</span></span>

* <span data-ttu-id="02c23-453">Поддерживает добавление обработчиков на основе Polly клиентам.</span><span class="sxs-lookup"><span data-stu-id="02c23-453">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="02c23-454">Можно использовать после установки пакета NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="02c23-454">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="02c23-455">Пакет не включен в общую платформу ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02c23-455">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="02c23-456">Обработка временных сбоев</span><span class="sxs-lookup"><span data-stu-id="02c23-456">Handle transient faults</span></span>

<span data-ttu-id="02c23-457">Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными.</span><span class="sxs-lookup"><span data-stu-id="02c23-457">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="02c23-458">Используется удобный метод расширения `AddTransientHttpErrorPolicy`, который позволяет определить политику для обработки временных ошибок.</span><span class="sxs-lookup"><span data-stu-id="02c23-458">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="02c23-459">Политики, заданные с помощью этого метода расширения, обрабатывают `HttpRequestException`, ответы HTTP 5xx и ответы HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="02c23-459">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="02c23-460">Расширение `AddTransientHttpErrorPolicy` может быть использовано в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02c23-460">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="02c23-461">Данное расширение предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:</span><span class="sxs-lookup"><span data-stu-id="02c23-461">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="02c23-462">В приведенном выше коде определена политика `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="02c23-462">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="02c23-463">Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.</span><span class="sxs-lookup"><span data-stu-id="02c23-463">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="02c23-464">Динамический выбор политик</span><span class="sxs-lookup"><span data-stu-id="02c23-464">Dynamically select policies</span></span>

<span data-ttu-id="02c23-465">Существуют дополнительные методы расширения, которые можно использовать для добавления обработчиков на основе Polly.</span><span class="sxs-lookup"><span data-stu-id="02c23-465">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="02c23-466">Одним из таких расширений является `AddPolicyHandler` с несколькими перегрузками.</span><span class="sxs-lookup"><span data-stu-id="02c23-466">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="02c23-467">Одна перегрузка разрешает проверку запроса для определения необходимой политики:</span><span class="sxs-lookup"><span data-stu-id="02c23-467">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="02c23-468">Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="02c23-468">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="02c23-469">Для остальных методов HTTP время ожидания — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="02c23-469">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="02c23-470">Добавление нескольких обработчиков Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-470">Add multiple Polly handlers</span></span>

<span data-ttu-id="02c23-471">Общепринятой практикой является вложение политик Polly для предоставления расширенной функциональности:</span><span class="sxs-lookup"><span data-stu-id="02c23-471">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="02c23-472">В приведенном выше примере добавляются два обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-472">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="02c23-473">Первый использует расширение `AddTransientHttpErrorPolicy`, чтобы добавить политику повтора.</span><span class="sxs-lookup"><span data-stu-id="02c23-473">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="02c23-474">Неудачные запросы выполняются повторно до трех раз.</span><span class="sxs-lookup"><span data-stu-id="02c23-474">Failed requests are retried up to three times.</span></span> <span data-ttu-id="02c23-475">Второй вызов к `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи.</span><span class="sxs-lookup"><span data-stu-id="02c23-475">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="02c23-476">Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд.</span><span class="sxs-lookup"><span data-stu-id="02c23-476">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="02c23-477">Политики размыкателя цепи отслеживают состояние.</span><span class="sxs-lookup"><span data-stu-id="02c23-477">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="02c23-478">Все вызовы через этот клиент имеют одинаковое состояние цепи.</span><span class="sxs-lookup"><span data-stu-id="02c23-478">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="02c23-479">Добавление политик из реестра Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-479">Add policies from the Polly registry</span></span>

<span data-ttu-id="02c23-480">Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="02c23-480">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="02c23-481">Предоставляется метод расширения, разрешающий добавление обработчика с помощью политики из реестра:</span><span class="sxs-lookup"><span data-stu-id="02c23-481">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="02c23-482">В приведенном выше коде, когда `PolicyRegistry` добавляется в `ServiceCollection`, регистрируются две политики.</span><span class="sxs-lookup"><span data-stu-id="02c23-482">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="02c23-483">Чтобы использовать политику из реестра, применяется метод `AddPolicyHandlerFromRegistry`, который передает имя необходимой политики.</span><span class="sxs-lookup"><span data-stu-id="02c23-483">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="02c23-484">Дополнительные сведения об интеграции `IHttpClientFactory` и Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="02c23-484">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="02c23-485">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="02c23-485">HttpClient and lifetime management</span></span>

<span data-ttu-id="02c23-486">При каждом вызове `CreateClient` в `IHttpClientFactory` возвращается новый экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-486">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="02c23-487">Для каждого названного клиента существует <xref:System.Net.Http.HttpMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="02c23-487">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="02c23-488">Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-488">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="02c23-489">`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов.</span><span class="sxs-lookup"><span data-stu-id="02c23-489">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="02c23-490">Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.</span><span class="sxs-lookup"><span data-stu-id="02c23-490">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="02c23-491">Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями.</span><span class="sxs-lookup"><span data-stu-id="02c23-491">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="02c23-492">Создание лишних обработчиков может привести к задержке подключения.</span><span class="sxs-lookup"><span data-stu-id="02c23-492">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="02c23-493">Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения DNS.</span><span class="sxs-lookup"><span data-stu-id="02c23-493">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="02c23-494">Время существования обработчика по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="02c23-494">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="02c23-495">Значение по умолчанию можно переопределить для каждого именованного клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-495">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="02c23-496">Чтобы переопределить это значение, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> в `IHttpClientBuilder`, который возвращается при создании клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-496">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="02c23-497">Высвобождать клиент не требуется.</span><span class="sxs-lookup"><span data-stu-id="02c23-497">Disposal of the client isn't required.</span></span> <span data-ttu-id="02c23-498">Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-498">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="02c23-499">`IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-499">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="02c23-500">Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, не требующие высвобождения.</span><span class="sxs-lookup"><span data-stu-id="02c23-500">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="02c23-501">До появления `IHttpClientFactory` один экземпляр `HttpClient` часто сохраняли в активном состоянии в течение длительного времени.</span><span class="sxs-lookup"><span data-stu-id="02c23-501">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="02c23-502">После перехода на `IHttpClientFactory` это уже не нужно.</span><span class="sxs-lookup"><span data-stu-id="02c23-502">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="02c23-503">Альтернативы интерфейсу IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="02c23-503">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="02c23-504">Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:</span><span class="sxs-lookup"><span data-stu-id="02c23-504">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="02c23-505">предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;</span><span class="sxs-lookup"><span data-stu-id="02c23-505">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="02c23-506">предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-506">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="02c23-507">Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="02c23-507">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="02c23-508">Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.</span><span class="sxs-lookup"><span data-stu-id="02c23-508">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="02c23-509">Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.</span><span class="sxs-lookup"><span data-stu-id="02c23-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="02c23-510">По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="02c23-510">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="02c23-511">Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.</span><span class="sxs-lookup"><span data-stu-id="02c23-511">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="02c23-512">`SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-512">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="02c23-513">Этот позволяет предотвратить нехватку сокетов.</span><span class="sxs-lookup"><span data-stu-id="02c23-513">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="02c23-514">`SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.</span><span class="sxs-lookup"><span data-stu-id="02c23-514">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="02c23-515">Cookies</span><span class="sxs-lookup"><span data-stu-id="02c23-515">Cookies</span></span>

<span data-ttu-id="02c23-516">Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="02c23-516">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="02c23-517">Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде.</span><span class="sxs-lookup"><span data-stu-id="02c23-517">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="02c23-518">Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="02c23-518">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="02c23-519">отключите автоматическую обработку файлов cookie;</span><span class="sxs-lookup"><span data-stu-id="02c23-519">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="02c23-520">не используйте `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="02c23-520">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="02c23-521">Чтобы отключить автоматическую обработку файлов <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>, вызовите cookie:</span><span class="sxs-lookup"><span data-stu-id="02c23-521">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="02c23-522">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="02c23-522">Logging</span></span>

<span data-ttu-id="02c23-523">Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-523">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="02c23-524">Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02c23-524">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="02c23-525">Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.</span><span class="sxs-lookup"><span data-stu-id="02c23-525">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="02c23-526">Категория журнала для каждого клиента включает в себя имя клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-526">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="02c23-527">Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-527">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="02c23-528">Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-528">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="02c23-529">Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="02c23-529">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="02c23-530">Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="02c23-530">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="02c23-531">Кроме того, журнал ведется в конвейере обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-531">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="02c23-532">В примере *MyNamedClient* эти сообщения вносятся в журнал по категории журнала `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-532">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="02c23-533">Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса по сети.</span><span class="sxs-lookup"><span data-stu-id="02c23-533">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="02c23-534">Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.</span><span class="sxs-lookup"><span data-stu-id="02c23-534">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="02c23-535">Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера.</span><span class="sxs-lookup"><span data-stu-id="02c23-535">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="02c23-536">Сюда входят, например, изменения заголовков запросов или кода состояния ответов.</span><span class="sxs-lookup"><span data-stu-id="02c23-536">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="02c23-537">Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам при необходимости.</span><span class="sxs-lookup"><span data-stu-id="02c23-537">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="02c23-538">Настройка HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="02c23-538">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="02c23-539">Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.</span><span class="sxs-lookup"><span data-stu-id="02c23-539">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="02c23-540">При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="02c23-540">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="02c23-541">Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-541">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="02c23-542">Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:</span><span class="sxs-lookup"><span data-stu-id="02c23-542">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="02c23-543">Использование IHttpClientFactory в консольном приложении</span><span class="sxs-lookup"><span data-stu-id="02c23-543">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="02c23-544">В консольном приложении добавьте в проект следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="02c23-544">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="02c23-545">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="02c23-545">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="02c23-546">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="02c23-546">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="02c23-547">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="02c23-547">In the following example:</span></span>

* <span data-ttu-id="02c23-548"><xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):</span><span class="sxs-lookup"><span data-stu-id="02c23-548"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="02c23-549">`MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-549">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="02c23-550">`HttpClient` используется для получения веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="02c23-550">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="02c23-551">`Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.</span><span class="sxs-lookup"><span data-stu-id="02c23-551">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="02c23-552">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="02c23-552">Additional resources</span></span>

* [<span data-ttu-id="02c23-553">Использование HttpClientFactory для реализации устойчивых HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="02c23-553">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="02c23-554">Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-554">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="02c23-555">Реализация шаблона размыкателя цепи</span><span class="sxs-lookup"><span data-stu-id="02c23-555">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="02c23-556">Авторы: [Гленн Кондрон (Glenn Condron)](https://github.com/glennc), [Райан Новак (Ryan Nowak)](https://github.com/rynowak) и [Стив Гордон (Steve Gordon)](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="02c23-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="02c23-557"><xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении.</span><span class="sxs-lookup"><span data-stu-id="02c23-557">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="02c23-558">Так вы получите следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="02c23-558">It offers the following benefits:</span></span>

* <span data-ttu-id="02c23-559">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-559">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="02c23-560">Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="02c23-560">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="02c23-561">Можно зарегистрировать клиент по умолчанию для других целей.</span><span class="sxs-lookup"><span data-stu-id="02c23-561">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="02c23-562">Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient` и предоставление расширений для ПО промежуточного слоя на основе Polly для использования этой возможности.</span><span class="sxs-lookup"><span data-stu-id="02c23-562">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="02c23-563">Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="02c23-563">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="02c23-564">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="02c23-564">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="02c23-565">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02c23-565">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="02c23-566">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="02c23-566">Prerequisites</span></span>

<span data-ttu-id="02c23-567">Для проектов, предназначенных для .NET Framework, необходимо установить пакет NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="02c23-567">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="02c23-568">Пакет `Microsoft.Extensions.Http` уже включен в проекты, предназначенные для .NET Core и ссылающиеся на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="02c23-568">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="02c23-569">Принципы использования</span><span class="sxs-lookup"><span data-stu-id="02c23-569">Consumption patterns</span></span>

<span data-ttu-id="02c23-570">Существует несколько способов использования `IHttpClientFactory` в приложении:</span><span class="sxs-lookup"><span data-stu-id="02c23-570">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="02c23-571">Основное использование</span><span class="sxs-lookup"><span data-stu-id="02c23-571">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="02c23-572">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-572">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="02c23-573">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-573">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="02c23-574">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-574">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="02c23-575">Все способы равноценны.</span><span class="sxs-lookup"><span data-stu-id="02c23-575">None of them are strictly superior to another.</span></span> <span data-ttu-id="02c23-576">Оптимальный подход зависит от ограничений приложения.</span><span class="sxs-lookup"><span data-stu-id="02c23-576">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="02c23-577">Основное использование</span><span class="sxs-lookup"><span data-stu-id="02c23-577">Basic usage</span></span>

<span data-ttu-id="02c23-578">`IHttpClientFactory` можно зарегистрировать путем вызова метода расширения `AddHttpClient` в `IServiceCollection` внутри метода `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02c23-578">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="02c23-579">После регистрации код может принимать `IHttpClientFactory` в любом месте, куда можно внедрить службу с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="02c23-579">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="02c23-580">`IHttpClientFactory` можно использовать для создания экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="02c23-580">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="02c23-581">Подобное использование `IHttpClientFactory` — это отличный способ рефакторинга имеющегося приложения.</span><span class="sxs-lookup"><span data-stu-id="02c23-581">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="02c23-582">Он не оказывает влияния на использование `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-582">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="02c23-583">Там, где в данный момент создаются экземпляры `HttpClient`, используйте вызов к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-583">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="02c23-584">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-584">Named clients</span></span>

<span data-ttu-id="02c23-585">Если для приложения предполагаются разные способы использования `HttpClient`, каждый со своей конфигурацией, можно применять **именованные клиенты**.</span><span class="sxs-lookup"><span data-stu-id="02c23-585">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="02c23-586">Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02c23-586">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="02c23-587">В приведенном выше коде вызывается клиент `AddHttpClient`, предоставляющий имя *github*.</span><span class="sxs-lookup"><span data-stu-id="02c23-587">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="02c23-588">У клиента есть некоторые настройки по умолчанию &mdash; а именно: базовый адрес и два заголовка, необходимые для работы с API GitHub.</span><span class="sxs-lookup"><span data-stu-id="02c23-588">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="02c23-589">При каждом вызове `CreateClient` создается новый экземпляр `HttpClient` и вызывается действие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="02c23-589">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="02c23-590">Для использования именованного клиента можно передать строковый параметр в `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-590">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="02c23-591">Укажите имя создаваемого клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-591">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="02c23-592">В приведенном выше коде в запросе не требуется указывать имя узла.</span><span class="sxs-lookup"><span data-stu-id="02c23-592">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="02c23-593">Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-593">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="02c23-594">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-594">Typed clients</span></span>

<span data-ttu-id="02c23-595">Типизированные клиенты:</span><span class="sxs-lookup"><span data-stu-id="02c23-595">Typed clients:</span></span>

* <span data-ttu-id="02c23-596">предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.</span><span class="sxs-lookup"><span data-stu-id="02c23-596">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="02c23-597">Это помогает IntelliSense и компилятору при использовании клиентов.</span><span class="sxs-lookup"><span data-stu-id="02c23-597">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="02c23-598">Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-598">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="02c23-599">Например, для конечной точки серверной части можно использовать один типизированный клиент, который будет содержать всю логику работы с этой конечной точкой.</span><span class="sxs-lookup"><span data-stu-id="02c23-599">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="02c23-600">Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.</span><span class="sxs-lookup"><span data-stu-id="02c23-600">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="02c23-601">Типизированный клиент принимает параметр `HttpClient` в конструкторе:</span><span class="sxs-lookup"><span data-stu-id="02c23-601">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="02c23-602">В приведенном выше коде конфигурация перемещается в типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="02c23-602">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="02c23-603">Объект `HttpClient` предоставляется в виде открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="02c23-603">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="02c23-604">Можно определить связанные с API методы, которые предоставляют функциональные возможности `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-604">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="02c23-605">Метод `GetAspNetDocsIssues` инкапсулирует код, необходимый для запроса и анализа последнего открытого выпуска из репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="02c23-605">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="02c23-606">Для регистрации типизированного клиента можно использовать универсальный метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices`, указав класс типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-606">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="02c23-607">Типизированный клиент регистрируется во внедрении зависимостей как временный.</span><span class="sxs-lookup"><span data-stu-id="02c23-607">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="02c23-608">Типизированный клиент можно внедрить и использовать напрямую:</span><span class="sxs-lookup"><span data-stu-id="02c23-608">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="02c23-609">При желании конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-609">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="02c23-610">Можно полностью инкапсулировать `HttpClient` внутри типизированного клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-610">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="02c23-611">Вместо предоставления его как свойства можно использовать открытые методы для внутреннего вызова экземпляра `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-611">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="02c23-612">В приведенном выше коде `HttpClient` хранится как закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="02c23-612">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="02c23-613">Любой доступ для совершения внешних вызовов осуществляется через метод `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="02c23-613">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="02c23-614">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="02c23-614">Generated clients</span></span>

<span data-ttu-id="02c23-615">`IHttpClientFactory` можно использовать в сочетании с другими библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="02c23-615">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="02c23-616">Refit — это библиотека REST для .NET.</span><span class="sxs-lookup"><span data-stu-id="02c23-616">Refit is a REST library for .NET.</span></span> <span data-ttu-id="02c23-617">Она преобразует REST API в динамические интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="02c23-617">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="02c23-618">Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.</span><span class="sxs-lookup"><span data-stu-id="02c23-618">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="02c23-619">Для представления внешнего API и его ответа определяются интерфейс и ответ:</span><span class="sxs-lookup"><span data-stu-id="02c23-619">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="02c23-620">Можно добавить типизированный клиент, используя Refit для создания реализации:</span><span class="sxs-lookup"><span data-stu-id="02c23-620">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="02c23-621">При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:</span><span class="sxs-lookup"><span data-stu-id="02c23-621">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="02c23-622">ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="02c23-622">Outgoing request middleware</span></span>

<span data-ttu-id="02c23-623">В `HttpClient` уже существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-623">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="02c23-624">Класс `IHttpClientFactory` упрощает определение обработчиков для применения к каждому именованному клиенту.</span><span class="sxs-lookup"><span data-stu-id="02c23-624">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="02c23-625">Он поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-625">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="02c23-626">Каждый из этих обработчиков может выполнять работу до и после исходящего запроса.</span><span class="sxs-lookup"><span data-stu-id="02c23-626">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="02c23-627">Этот шаблон похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02c23-627">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="02c23-628">Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="02c23-628">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="02c23-629">Чтобы создать обработчик, необходимо определить класс, производный от <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="02c23-629">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="02c23-630">Переопределите метод `SendAsync` для выполнения кода до передачи запросов следующему обработчику в конвейере:</span><span class="sxs-lookup"><span data-stu-id="02c23-630">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="02c23-631">В предыдущем коде определяется базовый обработчик.</span><span class="sxs-lookup"><span data-stu-id="02c23-631">The preceding code defines a basic handler.</span></span> <span data-ttu-id="02c23-632">Он проверяет, включен ли в запрос заголовок `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="02c23-632">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="02c23-633">Если заголовок отсутствует, он может избежать вызовов HTTP и вернуть подходящий ответ.</span><span class="sxs-lookup"><span data-stu-id="02c23-633">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="02c23-634">Во время регистрации можно добавить один или несколько обработчиков в конфигурацию для `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-634">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="02c23-635">Эта задача выполняется через методы расширения в <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="02c23-635">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="02c23-636">В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="02c23-636">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="02c23-637">Обработчик **должен** регистрироваться во внедрении зависимостей как временная служба, а не ограниченная.</span><span class="sxs-lookup"><span data-stu-id="02c23-637">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="02c23-638">Если обработчик зарегистрирован в качестве службы с областью действия и все службы, от которых зависит этот обработчик, освобождаются:</span><span class="sxs-lookup"><span data-stu-id="02c23-638">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="02c23-639">Службы обработчика могли быть удалены, прежде чем обработчик вышел из области действия.</span><span class="sxs-lookup"><span data-stu-id="02c23-639">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="02c23-640">Освобожденные службы обработчика приводят к его сбою.</span><span class="sxs-lookup"><span data-stu-id="02c23-640">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="02c23-641">После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-641">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="02c23-642">Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться.</span><span class="sxs-lookup"><span data-stu-id="02c23-642">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="02c23-643">Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:</span><span class="sxs-lookup"><span data-stu-id="02c23-643">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="02c23-644">Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:</span><span class="sxs-lookup"><span data-stu-id="02c23-644">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="02c23-645">Передайте данные в обработчик с помощью `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="02c23-645">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="02c23-646">Используйте `IHttpContextAccessor` для доступа к текущему запросу.</span><span class="sxs-lookup"><span data-stu-id="02c23-646">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="02c23-647">Создайте пользовательский объект хранилища `AsyncLocal` для передачи данных.</span><span class="sxs-lookup"><span data-stu-id="02c23-647">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="02c23-648">Использование обработчиков на основе Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-648">Use Polly-based handlers</span></span>

<span data-ttu-id="02c23-649">`IHttpClientFactory` интегрируется с популярной библиотекой сторонних разработчиков под названием [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="02c23-649">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="02c23-650">Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET.</span><span class="sxs-lookup"><span data-stu-id="02c23-650">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="02c23-651">Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.</span><span class="sxs-lookup"><span data-stu-id="02c23-651">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="02c23-652">Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения.</span><span class="sxs-lookup"><span data-stu-id="02c23-652">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="02c23-653">Расширения Polly:</span><span class="sxs-lookup"><span data-stu-id="02c23-653">The Polly extensions:</span></span>

* <span data-ttu-id="02c23-654">Поддерживает добавление обработчиков на основе Polly клиентам.</span><span class="sxs-lookup"><span data-stu-id="02c23-654">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="02c23-655">Можно использовать после установки пакета NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="02c23-655">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="02c23-656">Пакет не включен в общую платформу ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02c23-656">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="02c23-657">Обработка временных сбоев</span><span class="sxs-lookup"><span data-stu-id="02c23-657">Handle transient faults</span></span>

<span data-ttu-id="02c23-658">Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными.</span><span class="sxs-lookup"><span data-stu-id="02c23-658">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="02c23-659">Используется удобный метод расширения `AddTransientHttpErrorPolicy`, который позволяет определить политику для обработки временных ошибок.</span><span class="sxs-lookup"><span data-stu-id="02c23-659">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="02c23-660">Политики, заданные с помощью этого метода расширения, обрабатывают `HttpRequestException`, ответы HTTP 5xx и ответы HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="02c23-660">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="02c23-661">Расширение `AddTransientHttpErrorPolicy` может быть использовано в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02c23-661">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="02c23-662">Данное расширение предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:</span><span class="sxs-lookup"><span data-stu-id="02c23-662">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="02c23-663">В приведенном выше коде определена политика `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="02c23-663">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="02c23-664">Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.</span><span class="sxs-lookup"><span data-stu-id="02c23-664">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="02c23-665">Динамический выбор политик</span><span class="sxs-lookup"><span data-stu-id="02c23-665">Dynamically select policies</span></span>

<span data-ttu-id="02c23-666">Существуют дополнительные методы расширения, которые можно использовать для добавления обработчиков на основе Polly.</span><span class="sxs-lookup"><span data-stu-id="02c23-666">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="02c23-667">Одним из таких расширений является `AddPolicyHandler` с несколькими перегрузками.</span><span class="sxs-lookup"><span data-stu-id="02c23-667">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="02c23-668">Одна перегрузка разрешает проверку запроса для определения необходимой политики:</span><span class="sxs-lookup"><span data-stu-id="02c23-668">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="02c23-669">Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="02c23-669">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="02c23-670">Для остальных методов HTTP время ожидания — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="02c23-670">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="02c23-671">Добавление нескольких обработчиков Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-671">Add multiple Polly handlers</span></span>

<span data-ttu-id="02c23-672">Общепринятой практикой является вложение политик Polly для предоставления расширенной функциональности:</span><span class="sxs-lookup"><span data-stu-id="02c23-672">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="02c23-673">В приведенном выше примере добавляются два обработчика.</span><span class="sxs-lookup"><span data-stu-id="02c23-673">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="02c23-674">Первый использует расширение `AddTransientHttpErrorPolicy`, чтобы добавить политику повтора.</span><span class="sxs-lookup"><span data-stu-id="02c23-674">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="02c23-675">Неудачные запросы выполняются повторно до трех раз.</span><span class="sxs-lookup"><span data-stu-id="02c23-675">Failed requests are retried up to three times.</span></span> <span data-ttu-id="02c23-676">Второй вызов к `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи.</span><span class="sxs-lookup"><span data-stu-id="02c23-676">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="02c23-677">Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд.</span><span class="sxs-lookup"><span data-stu-id="02c23-677">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="02c23-678">Политики размыкателя цепи отслеживают состояние.</span><span class="sxs-lookup"><span data-stu-id="02c23-678">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="02c23-679">Все вызовы через этот клиент имеют одинаковое состояние цепи.</span><span class="sxs-lookup"><span data-stu-id="02c23-679">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="02c23-680">Добавление политик из реестра Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-680">Add policies from the Polly registry</span></span>

<span data-ttu-id="02c23-681">Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="02c23-681">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="02c23-682">Предоставляется метод расширения, разрешающий добавление обработчика с помощью политики из реестра:</span><span class="sxs-lookup"><span data-stu-id="02c23-682">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="02c23-683">В приведенном выше коде, когда `PolicyRegistry` добавляется в `ServiceCollection`, регистрируются две политики.</span><span class="sxs-lookup"><span data-stu-id="02c23-683">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="02c23-684">Чтобы использовать политику из реестра, применяется метод `AddPolicyHandlerFromRegistry`, который передает имя необходимой политики.</span><span class="sxs-lookup"><span data-stu-id="02c23-684">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="02c23-685">Дополнительные сведения об интеграции `IHttpClientFactory` и Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="02c23-685">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="02c23-686">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="02c23-686">HttpClient and lifetime management</span></span>

<span data-ttu-id="02c23-687">При каждом вызове `CreateClient` в `IHttpClientFactory` возвращается новый экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-687">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="02c23-688">Для каждого названного клиента существует <xref:System.Net.Http.HttpMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="02c23-688">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="02c23-689">Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-689">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="02c23-690">`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов.</span><span class="sxs-lookup"><span data-stu-id="02c23-690">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="02c23-691">Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.</span><span class="sxs-lookup"><span data-stu-id="02c23-691">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="02c23-692">Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями.</span><span class="sxs-lookup"><span data-stu-id="02c23-692">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="02c23-693">Создание лишних обработчиков может привести к задержке подключения.</span><span class="sxs-lookup"><span data-stu-id="02c23-693">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="02c23-694">Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения DNS.</span><span class="sxs-lookup"><span data-stu-id="02c23-694">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="02c23-695">Время существования обработчика по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="02c23-695">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="02c23-696">Значение по умолчанию можно переопределить для каждого именованного клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-696">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="02c23-697">Чтобы переопределить это значение, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> в `IHttpClientBuilder`, который возвращается при создании клиента:</span><span class="sxs-lookup"><span data-stu-id="02c23-697">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="02c23-698">Высвобождать клиент не требуется.</span><span class="sxs-lookup"><span data-stu-id="02c23-698">Disposal of the client isn't required.</span></span> <span data-ttu-id="02c23-699">Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-699">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="02c23-700">`IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-700">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="02c23-701">Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, не требующие высвобождения.</span><span class="sxs-lookup"><span data-stu-id="02c23-701">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="02c23-702">До появления `IHttpClientFactory` один экземпляр `HttpClient` часто сохраняли в активном состоянии в течение длительного времени.</span><span class="sxs-lookup"><span data-stu-id="02c23-702">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="02c23-703">После перехода на `IHttpClientFactory` это уже не нужно.</span><span class="sxs-lookup"><span data-stu-id="02c23-703">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="02c23-704">Альтернативы интерфейсу IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="02c23-704">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="02c23-705">Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:</span><span class="sxs-lookup"><span data-stu-id="02c23-705">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="02c23-706">предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;</span><span class="sxs-lookup"><span data-stu-id="02c23-706">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="02c23-707">предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-707">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="02c23-708">Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="02c23-708">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="02c23-709">Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.</span><span class="sxs-lookup"><span data-stu-id="02c23-709">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="02c23-710">Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.</span><span class="sxs-lookup"><span data-stu-id="02c23-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="02c23-711">По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="02c23-711">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="02c23-712">Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.</span><span class="sxs-lookup"><span data-stu-id="02c23-712">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="02c23-713">`SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-713">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="02c23-714">Этот позволяет предотвратить нехватку сокетов.</span><span class="sxs-lookup"><span data-stu-id="02c23-714">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="02c23-715">`SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.</span><span class="sxs-lookup"><span data-stu-id="02c23-715">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="02c23-716">Cookies</span><span class="sxs-lookup"><span data-stu-id="02c23-716">Cookies</span></span>

<span data-ttu-id="02c23-717">Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="02c23-717">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="02c23-718">Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде.</span><span class="sxs-lookup"><span data-stu-id="02c23-718">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="02c23-719">Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="02c23-719">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="02c23-720">отключите автоматическую обработку файлов cookie;</span><span class="sxs-lookup"><span data-stu-id="02c23-720">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="02c23-721">не используйте `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="02c23-721">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="02c23-722">Чтобы отключить автоматическую обработку файлов <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>, вызовите cookie:</span><span class="sxs-lookup"><span data-stu-id="02c23-722">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="02c23-723">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="02c23-723">Logging</span></span>

<span data-ttu-id="02c23-724">Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-724">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="02c23-725">Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02c23-725">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="02c23-726">Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.</span><span class="sxs-lookup"><span data-stu-id="02c23-726">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="02c23-727">Категория журнала для каждого клиента включает в себя имя клиента.</span><span class="sxs-lookup"><span data-stu-id="02c23-727">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="02c23-728">Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-728">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="02c23-729">Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-729">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="02c23-730">Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="02c23-730">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="02c23-731">Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="02c23-731">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="02c23-732">Кроме того, журнал ведется в конвейере обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="02c23-732">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="02c23-733">В примере *MyNamedClient* эти сообщения вносятся в журнал по категории журнала `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="02c23-733">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="02c23-734">Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса по сети.</span><span class="sxs-lookup"><span data-stu-id="02c23-734">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="02c23-735">Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.</span><span class="sxs-lookup"><span data-stu-id="02c23-735">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="02c23-736">Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера.</span><span class="sxs-lookup"><span data-stu-id="02c23-736">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="02c23-737">Сюда входят, например, изменения заголовков запросов или кода состояния ответов.</span><span class="sxs-lookup"><span data-stu-id="02c23-737">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="02c23-738">Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам при необходимости.</span><span class="sxs-lookup"><span data-stu-id="02c23-738">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="02c23-739">Настройка HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="02c23-739">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="02c23-740">Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.</span><span class="sxs-lookup"><span data-stu-id="02c23-740">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="02c23-741">При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="02c23-741">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="02c23-742">Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>.</span><span class="sxs-lookup"><span data-stu-id="02c23-742">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="02c23-743">Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:</span><span class="sxs-lookup"><span data-stu-id="02c23-743">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="02c23-744">Использование IHttpClientFactory в консольном приложении</span><span class="sxs-lookup"><span data-stu-id="02c23-744">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="02c23-745">В консольном приложении добавьте в проект следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="02c23-745">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="02c23-746">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="02c23-746">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="02c23-747">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="02c23-747">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="02c23-748">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="02c23-748">In the following example:</span></span>

* <span data-ttu-id="02c23-749"><xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):</span><span class="sxs-lookup"><span data-stu-id="02c23-749"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="02c23-750">`MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02c23-750">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="02c23-751">`HttpClient` используется для получения веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="02c23-751">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="02c23-752">`Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.</span><span class="sxs-lookup"><span data-stu-id="02c23-752">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="02c23-753">ПО промежуточного слоя для распространения заголовков</span><span class="sxs-lookup"><span data-stu-id="02c23-753">Header propagation middleware</span></span>

<span data-ttu-id="02c23-754">Header propagation — это поддерживаемое сообществом ПО промежуточного слоя для распространения HTTP-заголовков из входящего запроса на исходящие запросы HTTP-клиентов.</span><span class="sxs-lookup"><span data-stu-id="02c23-754">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="02c23-755">Чтобы использовать распространение заголовков, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="02c23-755">To use header propagation:</span></span>

* <span data-ttu-id="02c23-756">Укажите ссылку на поддерживаемый сообществом порт пакета [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="02c23-756">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="02c23-757">ASP.NET Core 3.1 и более поздних версий поддерживает [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="02c23-757">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="02c23-758">Настройте ПО промежуточного слоя и `HttpClient` в `Startup`:</span><span class="sxs-lookup"><span data-stu-id="02c23-758">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="02c23-759">Клиент включает настроенные заголовки в исходящие запросы:</span><span class="sxs-lookup"><span data-stu-id="02c23-759">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="02c23-760">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="02c23-760">Additional resources</span></span>

* [<span data-ttu-id="02c23-761">Использование HttpClientFactory для реализации устойчивых HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="02c23-761">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="02c23-762">Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly</span><span class="sxs-lookup"><span data-stu-id="02c23-762">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="02c23-763">Реализация шаблона размыкателя цепи</span><span class="sxs-lookup"><span data-stu-id="02c23-763">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
