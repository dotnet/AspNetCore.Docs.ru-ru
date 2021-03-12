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
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Выполнения HTTP-запросов с помощью IHttpClientFactory в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Авторы [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin), [Стив Гордон](https://github.com/stevejgordon) (Steve Gordon), [Гленн Кондрон](https://github.com/glennc) (Glenn Condron) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak).

<xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении. `IHttpClientFactory` предоставляет следующие преимущества:

* Центральное расположение для именования и настройки логических экземпляров `HttpClient`. Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/). Можно зарегистрировать клиент по умолчанию для общего доступа.
* Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient`. Предоставление расширений для ПО промежуточного слоя на основе Polly для делегирования обработчиков в `HttpClient`.
* Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler`. Автоматическое управление позволяет избежать обычных проблем со службой доменных имен (DNS), которые возникают при управлении временем существования `HttpClient` вручную.
* Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).

Пример кода в этой версии раздела использует <xref:System.Text.Json> для десериализации содержимого JSON, возвращаемого в ответах HTTP. Для примеров, использующих `Json.NET` и `ReadAsAsync<T>`, воспользуйтесь средством выбора версии, чтобы выбрать версию 2.x этого раздела.

## <a name="consumption-patterns"></a>Принципы использования

Существует несколько способов использования `IHttpClientFactory` в приложении:

* [Основное использование](#basic-usage)
* [Именованные клиенты](#named-clients)
* [Типизированные клиенты](#typed-clients)
* [Созданные клиенты](#generated-clients)

Оптимальный подход зависит от требований приложения.

### <a name="basic-usage"></a>Основное использование

`IHttpClientFactory` можно зарегистрировать, вызвав `AddHttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

`IHttpClientFactory` можно запросить с помощью [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection). Следующий код использует `IHttpClientFactory` для создания экземпляра `HttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Подобное использование `IHttpClientFactory` — это хороший способ рефакторинга имеющегося приложения. Он не оказывает влияния на использование `HttpClient`. Там, где в существующем приложении создаются экземпляры `HttpClient`, используйте вызовы к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Именованные клиенты

Именованные клиенты являются хорошим выбором в следующих случаях:

* Приложение требует много отдельных использований `HttpClient`.
* Многие `HttpClient` имеют другую конфигурацию.

Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

В приведенном выше коде клиент регистрируется с:

* базовым адресом `https://api.github.com/`;
* двумя заголовками, необходимыми для работы с API GitHub.

#### <a name="createclient"></a>CreateClient

При каждом вызове <xref:System.Net.Http.IHttpClientFactory.CreateClient*>:

* создается новый экземпляр `HttpClient`;
* вызывается действие настройки.

Чтобы создать именованный клиент, передайте его имя в `CreateClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

В приведенном выше коде в запросе не требуется указывать имя узла. Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.

### <a name="typed-clients"></a>Типизированные клиенты

Типизированные клиенты:

* предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.
* Это помогает IntelliSense и компилятору при использовании клиентов.
* Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`. Например, один типизированный клиент можно использовать:
  * для одной серверной конечной точки;
  * для инкапсуляции всей логики, связанной с конечной точкой.
* Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.

Типизированный клиент принимает параметр `HttpClient` в конструкторе:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

В приведенном выше коде:

* Конфигурация перемещается в типизированный клиент.
* Объект `HttpClient` предоставляется в виде открытого свойства.

Можно создать связанные с API методы, которые предоставляют функциональные возможности `HttpClient`. Например, метод `GetAspNetDocsIssues` инкапсулирует код для получения открытых вопросов.

Следующий код вызывает <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices` для регистрации класса типизированного клиента:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Типизированный клиент регистрируется во внедрении зависимостей как временный. В приведенном выше коде `AddHttpClient` регистрирует `GitHubService` как временную службу. Эта регистрация использует фабричный метод для следующих задач:

1. Создание экземпляра `HttpClient`.
1. Создайте экземпляр `GitHubService`, передав его конструктору экземпляр `HttpClient`.

Типизированный клиент можно внедрить и использовать напрямую:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

`HttpClient` может быть инкапсулирован в типизированном клиенте. Вместо предоставления его как свойства определите метод для внутреннего вызова экземпляра `HttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

В приведенном выше коде `HttpClient` хранится в закрытом поле. Доступ к `HttpClient` осуществляется с помощью общедоступного метода `GetRepos`.

### <a name="generated-clients"></a>Созданные клиенты

`IHttpClientFactory` можно использовать в сочетании с библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit). Refit — это библиотека REST для .NET. Она преобразует REST API в динамические интерфейсы. Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.

Для представления внешнего API и его ответа определяются интерфейс и ответ:

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

Можно добавить типизированный клиент, используя Refit для создания реализации:

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

При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:

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

## <a name="make-post-put-and-delete-requests"></a>Выполнение запросов POST, PUT и DELETE

В предыдущих примерах все HTTP-запросы используют HTTP-команду GET. `HttpClient` также поддерживает другие HTTP-команды, в том числе:

* ПОМЕСТИТЬ
* PUT
* DELETE
* PATCH

Полный список поддерживаемых HTTP-команд см. в статье <xref:System.Net.Http.HttpMethod>.

В следующем примере показано, как выполнить HTTP-запрос POST:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

В приведенном выше коде метод `CreateItemAsync` выполняет следующие задачи:

* сериализует параметр `TodoItem` в JSON с помощью `System.Text.Json`. Для настройки процесса сериализации используется экземпляр <xref:System.Text.Json.JsonSerializerOptions>.
* создает экземпляр <xref:System.Net.Http.StringContent> для упаковки сериализованного JSON для отправки в тексте HTTP-запроса.
* вызывает метод <xref:System.Net.Http.HttpClient.PostAsync%2A> для отправки содержимого JSON по указанному URL-адресу. Это относительный URL-адрес, который добавляется в свойство [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).
* вызывает метод <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>, чтобы создавать исключение, если код состояния ответа означает неудачное выполнение.

`HttpClient` также поддерживает другие типы содержимого. Например, <xref:System.Net.Http.MultipartContent> и <xref:System.Net.Http.StreamContent>. Полный список поддерживаемого содержимого см. в статье <xref:System.Net.Http.HttpContent>.

Ниже приводится пример HTTP-запроса PUT.

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

Приведенный выше код практически аналогичен коду в примере с POST. Метод `SaveItemAsync` вызывает <xref:System.Net.Http.HttpClient.PutAsync%2A> вместо `PostAsync`.

Ниже приводится пример HTTP-запроса DELETE.

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

В приведенном выше коде метод `DeleteItemAsync` вызывает <xref:System.Net.Http.HttpClient.DeleteAsync%2A>. Поскольку HTTP-запросы DELETE обычно не содержат текст, метод `DeleteAsync` не предоставляет перегрузку, которая принимает экземпляр `HttpContent`.

Дополнительные сведения об использовании различных HTTP-команд с `HttpClient` см. в статье <xref:System.Net.Http.HttpClient>.

## <a name="outgoing-request-middleware"></a>ПО промежуточного слоя для исходящих запросов

В `HttpClient` существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов. `IHttpClientFactory`:

  * Упрощает определение обработчиков для применения к каждому именованному клиенту.
  * Поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов. Каждый из этих обработчиков может выполнять работу до и после исходящего запроса. Этот шаблон:
  
    * похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core;
    * предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая:
      * кэширование
      * обработку ошибок
      * сериализацию
      * Ведение журнала

Чтобы создать делегированный обработчик, сделайте следующее:

* Создайте объект, производный от <xref:System.Net.Http.DelegatingHandler>.
* Переопределите метод <xref:System.Net.Http.DelegatingHandler.SendAsync*>. Выполните код до передачи запроса следующему обработчику в конвейере:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Предыдущий код проверяет, находится ли заголовок `X-API-KEY` в запросе. Если `X-API-KEY` отсутствует, возвращается <xref:System.Net.HttpStatusCode.BadRequest>.

Можно добавить сразу несколько обработчиков в конфигурацию для `HttpClient` с использованием <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей. После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.

Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться. Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a>Использование внедрения зависимостей в ПО промежуточного слоя для исходящих запросов

Когда `IHttpClientFactory` создает новый делегированный обработчик, он использует внедрение зависимостей для выполнения параметров конструктора обработчика. `IHttpClientFactory` создает **отдельную** область внедрения зависимостей для каждого обработчика, что может стать причиной аномального поведения, когда обработчик использует службу с *назначенной областью*.

Например, рассмотрим следующий интерфейс и его реализацию, которая представляет задачу в виде операции с идентификатором `OperationId`:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

Как можно понять из названия, `IOperationScoped` регистрируется с помощью внедрения зависимостей с использованием времени существования с *назначенной областью*:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

Следующий делегирующий обработчик принимает и использует `IOperationScoped` для задания заголовка `X-OPERATION-ID` для исходящего запроса:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

В [скачиваемом ресурсе `HttpRequestsSample`](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)] перейдите к `/Operation` и обновите страницу. Значение области запроса изменяется с каждым запросом, но значение области обработчика изменяется только каждые 5 секунд.

Обработчики могут зависеть от служб из любой области. Службы, которые зависят от обработчиков, удаляются при удалении обработчика.

Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:

* Передайте данные в обработчик с помощью [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* Используйте <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> для доступа к текущему запросу.
* Создайте пользовательский объект хранилища <xref:System.Threading.AsyncLocal`1> для передачи данных.

## <a name="use-polly-based-handlers"></a>Использование обработчиков на основе Polly

`IHttpClientFactory` интегрируется с библиотекой сторонних разработчиков [Polly](https://github.com/App-vNext/Polly). Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET. Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.

Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения. Расширения Polly поддерживают добавление обработчиков на основе Polly клиентам. Polly нужен пакет NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).

### <a name="handle-transient-faults"></a>Обработка временных сбоев

Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> позволяет определить политику для обработки временных ошибок. Политики, настроенные с помощью `AddTransientHttpErrorPolicy`, обрабатывают следующие ответы:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy` предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

В приведенном выше коде определена политика `WaitAndRetryAsync`. Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.

### <a name="dynamically-select-policies"></a>Динамический выбор политик

Предоставляются методы расширения для добавления обработчиков на основе Polly, например <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>. Следующая перегрузка `AddPolicyHandler` проверяет запрос для определения применимой политики:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд. Для остальных методов HTTP время ожидания — 30 секунд.

### <a name="add-multiple-polly-handlers"></a>Добавление нескольких обработчиков Polly

Общепринятой практикой является вложение политик Polly:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

В предшествующем примере:

* Добавляются два обработчика.
* Первый обработчик использует <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>, чтобы добавить политику повтора. Неудачные запросы выполняются повторно до трех раз.
* Второй вызов `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи. Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд. Политики размыкателя цепи отслеживают состояние. Все вызовы через этот клиент имеют одинаковое состояние цепи.

### <a name="add-policies-from-the-polly-registry"></a>Добавление политик из реестра Polly

Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.

В приведенном ниже коде выполняется следующее:

* Добавляются политики regular и long.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> добавляет политики regular и long из реестра.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Дополнительные сведения о `IHttpClientFactory` и интеграции Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Управление HttpClient и временем существования

При каждом вызове `CreateClient` в `IHttpClientFactory` возвращается новый экземпляр `HttpClient`. <xref:System.Net.Http.HttpMessageHandler> создается для каждого именованного клиента. Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.

`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов. Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.

Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями. Создание лишних обработчиков может привести к задержке подключения. Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения службы доменных имен (DNS).

Время существования обработчика по умолчанию — две минуты. Значение по умолчанию можно переопределить для каждого именованного клиента:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, **не** требующие освобождения. Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.

До появления `IHttpClientFactory` один экземпляр `HttpClient` часто сохраняли в активном состоянии в течение длительного времени. После перехода на `IHttpClientFactory` это уже не нужно.

### <a name="alternatives-to-ihttpclientfactory"></a>Альтернативы интерфейсу IHttpClientFactory

Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:

* предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;
* предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.

Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.

- Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.
- Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.
- По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.

Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.

- `SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`. Этот позволяет предотвратить нехватку сокетов.
- `SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.

### <a name="cookies"></a>Cookies

Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`. Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде. Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:

 - отключите автоматическую обработку файлов cookie;
 - не используйте `IHttpClientFactory`.

Чтобы отключить автоматическую обработку файлов <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>, вызовите cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Ведение журнала

Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов. Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию. Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.

Категория журнала для каждого клиента включает в себя имя клиента. Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler". Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов. Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере. Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.

Кроме того, журнал ведется в конвейере обработчиков запросов. В примере *MyNamedClient* эти сообщения регистрируются с категорией журнала "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler". Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса. Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.

Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера. Сюда входят изменения заголовков запросов или кода состояния ответов.

Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам.

## <a name="configure-the-httpmessagehandler"></a>Настройка HttpMessageHandler

Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.

При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`. Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>. Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Использование IHttpClientFactory в консольном приложении

В консольном приложении добавьте в проект следующие ссылки на пакеты:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

В следующем примере:

* <xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):
* `MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`. `HttpClient` используется для получения веб-страницы.
* `Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>ПО промежуточного слоя для распространения заголовков

Header propagation — это ПО промежуточного слоя ASP.NET Core для распространения HTTP-заголовков из входящего запроса на исходящие запросы HTTP-клиентов. Чтобы использовать распространение заголовков, сделайте следующее:

* Укажите ссылку на пакет [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).
* Настройте ПО промежуточного слоя и `HttpClient` в `Startup`:

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* Клиент включает настроенные заголовки в исходящие запросы:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Использование HttpClientFactory для реализации устойчивых HTTP-запросов](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Реализация шаблона размыкателя цепи](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [Сериализация и десериализация JSON в .NET](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Авторы: [Гленн Кондрон (Glenn Condron)](https://github.com/glennc), [Райан Новак (Ryan Nowak)](https://github.com/rynowak) и [Стив Гордон (Steve Gordon)](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении. Так вы получите следующие преимущества:

* Центральное расположение для именования и настройки логических экземпляров `HttpClient`. Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/). Можно зарегистрировать клиент по умолчанию для других целей.
* Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient` и предоставление расширений для ПО промежуточного слоя на основе Polly для использования этой возможности.
* Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.
* Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Принципы использования

Существует несколько способов использования `IHttpClientFactory` в приложении:

* [Основное использование](#basic-usage)
* [Именованные клиенты](#named-clients)
* [Типизированные клиенты](#typed-clients)
* [Созданные клиенты](#generated-clients)

Все способы равноценны. Оптимальный подход зависит от ограничений приложения.

### <a name="basic-usage"></a>Основное использование

`IHttpClientFactory` можно зарегистрировать путем вызова метода расширения `AddHttpClient` в `IServiceCollection` внутри метода `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

После регистрации код может принимать `IHttpClientFactory` в любом месте, куда можно внедрить службу с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI). `IHttpClientFactory` можно использовать для создания экземпляра `HttpClient`:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Подобное использование `IHttpClientFactory` — это отличный способ рефакторинга имеющегося приложения. Он не оказывает влияния на использование `HttpClient`. Там, где в данный момент создаются экземпляры `HttpClient`, используйте вызов к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Именованные клиенты

Если для приложения предполагаются разные способы использования `HttpClient`, каждый со своей конфигурацией, можно применять **именованные клиенты**. Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

В приведенном выше коде вызывается клиент `AddHttpClient`, предоставляющий имя *github*. У клиента есть некоторые настройки по умолчанию &mdash; а именно: базовый адрес и два заголовка, необходимые для работы с API GitHub.

При каждом вызове `CreateClient` создается новый экземпляр `HttpClient` и вызывается действие конфигурации.

Для использования именованного клиента можно передать строковый параметр в `CreateClient`. Укажите имя создаваемого клиента:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

В приведенном выше коде в запросе не требуется указывать имя узла. Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.

### <a name="typed-clients"></a>Типизированные клиенты

Типизированные клиенты:

* предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.
* Это помогает IntelliSense и компилятору при использовании клиентов.
* Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`. Например, для конечной точки серверной части можно использовать один типизированный клиент, который будет содержать всю логику работы с этой конечной точкой.
* Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.

Типизированный клиент принимает параметр `HttpClient` в конструкторе:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

В приведенном выше коде конфигурация перемещается в типизированный клиент. Объект `HttpClient` предоставляется в виде открытого свойства. Можно определить связанные с API методы, которые предоставляют функциональные возможности `HttpClient`. Метод `GetAspNetDocsIssues` инкапсулирует код, необходимый для запроса и анализа последнего открытого выпуска из репозитория GitHub.

Для регистрации типизированного клиента можно использовать универсальный метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices`, указав класс типизированного клиента:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Типизированный клиент регистрируется во внедрении зависимостей как временный. Типизированный клиент можно внедрить и использовать напрямую:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

При желании конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Можно полностью инкапсулировать `HttpClient` внутри типизированного клиента. Вместо предоставления его как свойства можно использовать открытые методы для внутреннего вызова экземпляра `HttpClient`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

В приведенном выше коде `HttpClient` хранится как закрытое поле. Любой доступ для совершения внешних вызовов осуществляется через метод `GetRepos`.

### <a name="generated-clients"></a>Созданные клиенты

`IHttpClientFactory` можно использовать в сочетании с другими библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit). Refit — это библиотека REST для .NET. Она преобразует REST API в динамические интерфейсы. Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.

Для представления внешнего API и его ответа определяются интерфейс и ответ:

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

Можно добавить типизированный клиент, используя Refit для создания реализации:

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

При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:

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

## <a name="outgoing-request-middleware"></a>ПО промежуточного слоя для исходящих запросов

В `HttpClient` уже существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов. Класс `IHttpClientFactory` упрощает определение обработчиков для применения к каждому именованному клиенту. Он поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов. Каждый из этих обработчиков может выполнять работу до и после исходящего запроса. Этот шаблон похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core. Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.

Чтобы создать обработчик, необходимо определить класс, производный от <xref:System.Net.Http.DelegatingHandler>. Переопределите метод `SendAsync` для выполнения кода до передачи запросов следующему обработчику в конвейере:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

В предыдущем коде определяется базовый обработчик. Он проверяет, включен ли в запрос заголовок `X-API-KEY`. Если заголовок отсутствует, он может избежать вызовов HTTP и вернуть подходящий ответ.

Во время регистрации можно добавить один или несколько обработчиков в конфигурацию для `HttpClient`. Эта задача выполняется через методы расширения в <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей. `IHttpClientFactory` создает отдельную область внедрения зависимостей для каждого обработчика. Обработчики могут зависеть от служб из любой области. Службы, которые зависят от обработчиков, удаляются при удалении обработчика.

После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.

Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться. Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:

* Передайте данные в обработчик с помощью `HttpRequestMessage.Properties`.
* Используйте `IHttpContextAccessor` для доступа к текущему запросу.
* Создайте пользовательский объект хранилища `AsyncLocal` для передачи данных.

## <a name="use-polly-based-handlers"></a>Использование обработчиков на основе Polly

`IHttpClientFactory` интегрируется с популярной библиотекой сторонних разработчиков под названием [Polly](https://github.com/App-vNext/Polly). Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET. Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.

Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения. Расширения Polly:

* Поддерживает добавление обработчиков на основе Polly клиентам.
* Можно использовать после установки пакета NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/). Пакет не включен в общую платформу ASP.NET Core.

### <a name="handle-transient-faults"></a>Обработка временных сбоев

Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными. Используется удобный метод расширения `AddTransientHttpErrorPolicy`, который позволяет определить политику для обработки временных ошибок. Политики, заданные с помощью этого метода расширения, обрабатывают `HttpRequestException`, ответы HTTP 5xx и ответы HTTP 408.

Расширение `AddTransientHttpErrorPolicy` может быть использовано в `Startup.ConfigureServices`. Данное расширение предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

В приведенном выше коде определена политика `WaitAndRetryAsync`. Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.

### <a name="dynamically-select-policies"></a>Динамический выбор политик

Существуют дополнительные методы расширения, которые можно использовать для добавления обработчиков на основе Polly. Одним из таких расширений является `AddPolicyHandler` с несколькими перегрузками. Одна перегрузка разрешает проверку запроса для определения необходимой политики:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд. Для остальных методов HTTP время ожидания — 30 секунд.

### <a name="add-multiple-polly-handlers"></a>Добавление нескольких обработчиков Polly

Общепринятой практикой является вложение политик Polly для предоставления расширенной функциональности:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

В приведенном выше примере добавляются два обработчика. Первый использует расширение `AddTransientHttpErrorPolicy`, чтобы добавить политику повтора. Неудачные запросы выполняются повторно до трех раз. Второй вызов к `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи. Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд. Политики размыкателя цепи отслеживают состояние. Все вызовы через этот клиент имеют одинаковое состояние цепи.

### <a name="add-policies-from-the-polly-registry"></a>Добавление политик из реестра Polly

Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`. Предоставляется метод расширения, разрешающий добавление обработчика с помощью политики из реестра:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

В приведенном выше коде, когда `PolicyRegistry` добавляется в `ServiceCollection`, регистрируются две политики. Чтобы использовать политику из реестра, применяется метод `AddPolicyHandlerFromRegistry`, который передает имя необходимой политики.

Дополнительные сведения об интеграции `IHttpClientFactory` и Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Управление HttpClient и временем существования

При каждом вызове `CreateClient` в `IHttpClientFactory` возвращается новый экземпляр `HttpClient`. Для каждого названного клиента существует <xref:System.Net.Http.HttpMessageHandler>. Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.

`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов. Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.

Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями. Создание лишних обработчиков может привести к задержке подключения. Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения DNS.

Время существования обработчика по умолчанию — две минуты. Значение по умолчанию можно переопределить для каждого именованного клиента. Чтобы переопределить это значение, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> в `IHttpClientBuilder`, который возвращается при создании клиента:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Высвобождать клиент не требуется. Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`. Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, не требующие высвобождения.

До появления `IHttpClientFactory` один экземпляр `HttpClient` часто сохраняли в активном состоянии в течение длительного времени. После перехода на `IHttpClientFactory` это уже не нужно.

### <a name="alternatives-to-ihttpclientfactory"></a>Альтернативы интерфейсу IHttpClientFactory

Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:

* предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;
* предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.

Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.

- Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.
- Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.
- По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.

Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.

- `SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`. Этот позволяет предотвратить нехватку сокетов.
- `SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.

### <a name="cookies"></a>Cookies

Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`. Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде. Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:

 - отключите автоматическую обработку файлов cookie;
 - не используйте `IHttpClientFactory`.

Чтобы отключить автоматическую обработку файлов <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>, вызовите cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Ведение журнала

Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов. Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию. Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.

Категория журнала для каждого клиента включает в себя имя клиента. Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов. Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере. Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.

Кроме того, журнал ведется в конвейере обработчиков запросов. В примере *MyNamedClient* эти сообщения вносятся в журнал по категории журнала `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса по сети. Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.

Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера. Сюда входят, например, изменения заголовков запросов или кода состояния ответов.

Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам при необходимости.

## <a name="configure-the-httpmessagehandler"></a>Настройка HttpMessageHandler

Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.

При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`. Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>. Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Использование IHttpClientFactory в консольном приложении

В консольном приложении добавьте в проект следующие ссылки на пакеты:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

В следующем примере:

* <xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):
* `MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`. `HttpClient` используется для получения веб-страницы.
* `Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Использование HttpClientFactory для реализации устойчивых HTTP-запросов](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Реализация шаблона размыкателя цепи](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Авторы: [Гленн Кондрон (Glenn Condron)](https://github.com/glennc), [Райан Новак (Ryan Nowak)](https://github.com/rynowak) и [Стив Гордон (Steve Gordon)](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении. Так вы получите следующие преимущества:

* Центральное расположение для именования и настройки логических экземпляров `HttpClient`. Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/). Можно зарегистрировать клиент по умолчанию для других целей.
* Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient` и предоставление расширений для ПО промежуточного слоя на основе Polly для использования этой возможности.
* Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.
* Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

Для проектов, предназначенных для .NET Framework, необходимо установить пакет NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/). Пакет `Microsoft.Extensions.Http` уже включен в проекты, предназначенные для .NET Core и ссылающиеся на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

## <a name="consumption-patterns"></a>Принципы использования

Существует несколько способов использования `IHttpClientFactory` в приложении:

* [Основное использование](#basic-usage)
* [Именованные клиенты](#named-clients)
* [Типизированные клиенты](#typed-clients)
* [Созданные клиенты](#generated-clients)

Все способы равноценны. Оптимальный подход зависит от ограничений приложения.

### <a name="basic-usage"></a>Основное использование

`IHttpClientFactory` можно зарегистрировать путем вызова метода расширения `AddHttpClient` в `IServiceCollection` внутри метода `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

После регистрации код может принимать `IHttpClientFactory` в любом месте, куда можно внедрить службу с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI). `IHttpClientFactory` можно использовать для создания экземпляра `HttpClient`:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Подобное использование `IHttpClientFactory` — это отличный способ рефакторинга имеющегося приложения. Он не оказывает влияния на использование `HttpClient`. Там, где в данный момент создаются экземпляры `HttpClient`, используйте вызов к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Именованные клиенты

Если для приложения предполагаются разные способы использования `HttpClient`, каждый со своей конфигурацией, можно применять **именованные клиенты**. Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

В приведенном выше коде вызывается клиент `AddHttpClient`, предоставляющий имя *github*. У клиента есть некоторые настройки по умолчанию &mdash; а именно: базовый адрес и два заголовка, необходимые для работы с API GitHub.

При каждом вызове `CreateClient` создается новый экземпляр `HttpClient` и вызывается действие конфигурации.

Для использования именованного клиента можно передать строковый параметр в `CreateClient`. Укажите имя создаваемого клиента:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

В приведенном выше коде в запросе не требуется указывать имя узла. Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.

### <a name="typed-clients"></a>Типизированные клиенты

Типизированные клиенты:

* предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.
* Это помогает IntelliSense и компилятору при использовании клиентов.
* Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`. Например, для конечной точки серверной части можно использовать один типизированный клиент, который будет содержать всю логику работы с этой конечной точкой.
* Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.

Типизированный клиент принимает параметр `HttpClient` в конструкторе:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

В приведенном выше коде конфигурация перемещается в типизированный клиент. Объект `HttpClient` предоставляется в виде открытого свойства. Можно определить связанные с API методы, которые предоставляют функциональные возможности `HttpClient`. Метод `GetAspNetDocsIssues` инкапсулирует код, необходимый для запроса и анализа последнего открытого выпуска из репозитория GitHub.

Для регистрации типизированного клиента можно использовать универсальный метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices`, указав класс типизированного клиента:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Типизированный клиент регистрируется во внедрении зависимостей как временный. Типизированный клиент можно внедрить и использовать напрямую:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

При желании конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Можно полностью инкапсулировать `HttpClient` внутри типизированного клиента. Вместо предоставления его как свойства можно использовать открытые методы для внутреннего вызова экземпляра `HttpClient`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

В приведенном выше коде `HttpClient` хранится как закрытое поле. Любой доступ для совершения внешних вызовов осуществляется через метод `GetRepos`.

### <a name="generated-clients"></a>Созданные клиенты

`IHttpClientFactory` можно использовать в сочетании с другими библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit). Refit — это библиотека REST для .NET. Она преобразует REST API в динамические интерфейсы. Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.

Для представления внешнего API и его ответа определяются интерфейс и ответ:

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

Можно добавить типизированный клиент, используя Refit для создания реализации:

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

При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:

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

## <a name="outgoing-request-middleware"></a>ПО промежуточного слоя для исходящих запросов

В `HttpClient` уже существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов. Класс `IHttpClientFactory` упрощает определение обработчиков для применения к каждому именованному клиенту. Он поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов. Каждый из этих обработчиков может выполнять работу до и после исходящего запроса. Этот шаблон похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core. Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.

Чтобы создать обработчик, необходимо определить класс, производный от <xref:System.Net.Http.DelegatingHandler>. Переопределите метод `SendAsync` для выполнения кода до передачи запросов следующему обработчику в конвейере:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

В предыдущем коде определяется базовый обработчик. Он проверяет, включен ли в запрос заголовок `X-API-KEY`. Если заголовок отсутствует, он может избежать вызовов HTTP и вернуть подходящий ответ.

Во время регистрации можно добавить один или несколько обработчиков в конфигурацию для `HttpClient`. Эта задача выполняется через методы расширения в <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей. Обработчик **должен** регистрироваться во внедрении зависимостей как временная служба, а не ограниченная. Если обработчик зарегистрирован в качестве службы с областью действия и все службы, от которых зависит этот обработчик, освобождаются:

* Службы обработчика могли быть удалены, прежде чем обработчик вышел из области действия.
* Освобожденные службы обработчика приводят к его сбою.

После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.

Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться. Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:

* Передайте данные в обработчик с помощью `HttpRequestMessage.Properties`.
* Используйте `IHttpContextAccessor` для доступа к текущему запросу.
* Создайте пользовательский объект хранилища `AsyncLocal` для передачи данных.

## <a name="use-polly-based-handlers"></a>Использование обработчиков на основе Polly

`IHttpClientFactory` интегрируется с популярной библиотекой сторонних разработчиков под названием [Polly](https://github.com/App-vNext/Polly). Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET. Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.

Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения. Расширения Polly:

* Поддерживает добавление обработчиков на основе Polly клиентам.
* Можно использовать после установки пакета NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/). Пакет не включен в общую платформу ASP.NET Core.

### <a name="handle-transient-faults"></a>Обработка временных сбоев

Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными. Используется удобный метод расширения `AddTransientHttpErrorPolicy`, который позволяет определить политику для обработки временных ошибок. Политики, заданные с помощью этого метода расширения, обрабатывают `HttpRequestException`, ответы HTTP 5xx и ответы HTTP 408.

Расширение `AddTransientHttpErrorPolicy` может быть использовано в `Startup.ConfigureServices`. Данное расширение предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

В приведенном выше коде определена политика `WaitAndRetryAsync`. Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.

### <a name="dynamically-select-policies"></a>Динамический выбор политик

Существуют дополнительные методы расширения, которые можно использовать для добавления обработчиков на основе Polly. Одним из таких расширений является `AddPolicyHandler` с несколькими перегрузками. Одна перегрузка разрешает проверку запроса для определения необходимой политики:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд. Для остальных методов HTTP время ожидания — 30 секунд.

### <a name="add-multiple-polly-handlers"></a>Добавление нескольких обработчиков Polly

Общепринятой практикой является вложение политик Polly для предоставления расширенной функциональности:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

В приведенном выше примере добавляются два обработчика. Первый использует расширение `AddTransientHttpErrorPolicy`, чтобы добавить политику повтора. Неудачные запросы выполняются повторно до трех раз. Второй вызов к `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи. Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд. Политики размыкателя цепи отслеживают состояние. Все вызовы через этот клиент имеют одинаковое состояние цепи.

### <a name="add-policies-from-the-polly-registry"></a>Добавление политик из реестра Polly

Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`. Предоставляется метод расширения, разрешающий добавление обработчика с помощью политики из реестра:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

В приведенном выше коде, когда `PolicyRegistry` добавляется в `ServiceCollection`, регистрируются две политики. Чтобы использовать политику из реестра, применяется метод `AddPolicyHandlerFromRegistry`, который передает имя необходимой политики.

Дополнительные сведения об интеграции `IHttpClientFactory` и Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Управление HttpClient и временем существования

При каждом вызове `CreateClient` в `IHttpClientFactory` возвращается новый экземпляр `HttpClient`. Для каждого названного клиента существует <xref:System.Net.Http.HttpMessageHandler>. Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.

`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов. Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.

Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями. Создание лишних обработчиков может привести к задержке подключения. Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения DNS.

Время существования обработчика по умолчанию — две минуты. Значение по умолчанию можно переопределить для каждого именованного клиента. Чтобы переопределить это значение, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> в `IHttpClientBuilder`, который возвращается при создании клиента:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Высвобождать клиент не требуется. Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`. Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, не требующие высвобождения.

До появления `IHttpClientFactory` один экземпляр `HttpClient` часто сохраняли в активном состоянии в течение длительного времени. После перехода на `IHttpClientFactory` это уже не нужно.

### <a name="alternatives-to-ihttpclientfactory"></a>Альтернативы интерфейсу IHttpClientFactory

Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:

* предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;
* предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.

Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.

- Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.
- Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.
- По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.

Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.

- `SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`. Этот позволяет предотвратить нехватку сокетов.
- `SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.

### <a name="cookies"></a>Cookies

Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`. Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде. Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:

 - отключите автоматическую обработку файлов cookie;
 - не используйте `IHttpClientFactory`.

Чтобы отключить автоматическую обработку файлов <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>, вызовите cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Ведение журнала

Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов. Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию. Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.

Категория журнала для каждого клиента включает в себя имя клиента. Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов. Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере. Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.

Кроме того, журнал ведется в конвейере обработчиков запросов. В примере *MyNamedClient* эти сообщения вносятся в журнал по категории журнала `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса по сети. Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.

Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера. Сюда входят, например, изменения заголовков запросов или кода состояния ответов.

Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам при необходимости.

## <a name="configure-the-httpmessagehandler"></a>Настройка HttpMessageHandler

Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.

При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`. Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>. Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Использование IHttpClientFactory в консольном приложении

В консольном приложении добавьте в проект следующие ссылки на пакеты:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

В следующем примере:

* <xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):
* `MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`. `HttpClient` используется для получения веб-страницы.
* `Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>ПО промежуточного слоя для распространения заголовков

Header propagation — это поддерживаемое сообществом ПО промежуточного слоя для распространения HTTP-заголовков из входящего запроса на исходящие запросы HTTP-клиентов. Чтобы использовать распространение заголовков, сделайте следующее:

* Укажите ссылку на поддерживаемый сообществом порт пакета [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation). ASP.NET Core 3.1 и более поздних версий поддерживает [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).

* Настройте ПО промежуточного слоя и `HttpClient` в `Startup`:

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* Клиент включает настроенные заголовки в исходящие запросы:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Использование HttpClientFactory для реализации устойчивых HTTP-запросов](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Реализация шаблона размыкателя цепи](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
