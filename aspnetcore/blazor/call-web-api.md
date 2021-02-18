---
title: Вызов веб-API из ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как вызывать веб-API из приложения Blazor WebAssembly с помощью вспомогательных методов JSON, включая создание запросов на общий доступ к ресурсам независимо от источника (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: b3c783623252512621a0cee7a3607c69cb6d09bb
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280283"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Вызов веб-API из ASP.NET Core Blazor

> [!NOTE]
> Эта статья относится к Blazor WebAssembly. [Приложения Blazor Server](xref:blazor/hosting-models#blazor-server) вызывают веб-интерфейсы API с помощью экземпляров <xref:System.Net.Http.HttpClient>, обычно созданных с помощью <xref:System.Net.Http.IHttpClientFactory>. Инструкции, относящиеся к Blazor Server, см. в статье <xref:fundamentals/http-requests>.

[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) вызывают веб-интерфейсы API с помощью предварительно настроенной службы <xref:System.Net.Http.HttpClient>. Составляйте запросы, которые могут включать параметры JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API), используя вспомогательные методы JSON Blazor или с помощью <xref:System.Net.Http.HttpRequestMessage>. Служба <xref:System.Net.Http.HttpClient> в приложениях Blazor WebAssembly направляет запросы обратно к серверу-источнику. Рекомендации в этом разделе относятся только к приложениям Blazor WebAssembly.

[Просмотрите или скачайте пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([описание процедуры скачивания](xref:index#how-to-download-a-sample)). Выберите приложение `BlazorWebAssemblySample`.

См. следующие компоненты в примере приложения `BlazorWebAssemblySample`:

* Вызов веб-API (`Pages/CallWebAPI.razor`)
* Тестер HTTP-запросов (`Components/HTTPRequestTester.razor`)

## <a name="packages"></a>Пакеты

Сошлитесь на пакет NuGet [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) в файле проекта.

## <a name="add-the-httpclient-service"></a>Добавление службы HttpClient

В `Program.Main` добавьте службу <xref:System.Net.Http.HttpClient>, если она еще не существует:

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient и вспомогательные методы JSON

В приложении Blazor WebAssembly [`HttpClient`](xref:fundamentals/http-requests) доступен в качестве предварительно настроенной службы для отправки запросов обратно к серверу-источнику.

Приложение Blazor Server не включает службу <xref:System.Net.Http.HttpClient> по умолчанию. Предоставьте <xref:System.Net.Http.HttpClient> для приложения с помощью [инфраструктуры фабрики `HttpClient`](xref:fundamentals/http-requests).

<xref:System.Net.Http.HttpClient> и вспомогательные методы JSON также используются для вызова сторонних конечных точек веб-API. <xref:System.Net.Http.HttpClient> реализуется с помощью [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) браузера и зависит от его ограничений, включая принудительное применение той же политики источника.

Базовый адрес клиента устанавливается как адрес сервера-источника. Внедрите экземпляр <xref:System.Net.Http.HttpClient> с помощью директивы [`@inject`](xref:mvc/views/razor#inject):

```razor
@using System.Net.Http
@inject HttpClient Http
```

В следующих примерах веб-API Todo обрабатывает операции создания, чтения, обновления и удаления (CRUD). Примеры основаны на классе `TodoItem`, в котором хранится:

* Идентификатор (`Id`, `long`): уникальный идентификатор элемента.
* Имя (`Name`, `string`). Имя элемента.
* Состояние (`IsComplete`, `bool`): указание того, завершен ли элемент Todo.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Вспомогательные методы JSON отправляют запросы к URI (веб-API в следующих примерах) и обрабатывают ответ:

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>. Отправляет запрос HTTP GET и анализирует текст ответа JSON для создания объекта.

  В следующем коде `todoItems` отображаются компонентом. Метод `GetTodoItems` срабатывает после завершения подготовки компонента к просмотру ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)). Полный пример см. в примере приложения.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>. Отправляет запрос HTTP POST, включая содержимое в кодировке JSON, и анализирует текст ответа JSON для создания объекта.

  В следующем коде `newItemName` предоставляется связанным элементом компонента. Метод `AddItem` активируется путем выбора элемента `<button>`. Полный пример см. в примере приложения.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Вызовы к <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> возвращают <xref:System.Net.Http.HttpResponseMessage>. Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения `ReadFromJsonAsync<T>`:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>. Отправляет запрос HTTP PUT, включая содержимое в кодировке JSON.

  В следующем коде значения `editItem` для `Name` и `IsCompleted` предоставляются связанными элементами компонента. Элемент `Id` задается, когда элемент выбирается в другой части пользовательского интерфейса и вызывается `EditItem`. Метод `SaveItem` активируется путем выбора элемента `<button>` "Save". Полный пример см. в примере приложения.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  Вызовы к <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> возвращают <xref:System.Net.Http.HttpResponseMessage>. Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> включает дополнительные методы расширения для отправки HTTP-запросов и получения HTTP-ответов. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> используется для отправки запроса HTTP DELETE в веб-интерфейс API.

В следующем коде элемент `<button>` "Delete" вызывает метод `DeleteItem`. Связанный элемент `<input>` предоставляет `id` удаляемого элемента. Полный пример см. в примере приложения.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a>Именованный класс HttpClient с IHttpClientFactory

Поддерживаются службы <xref:System.Net.Http.IHttpClientFactory> и конфигурация именованного класса <xref:System.Net.Http.HttpClient>.

Сошлитесь на пакет NuGet [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) в файле проекта.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Компонент `FetchData` (`Pages/FetchData.razor`):

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a>Типизированный класс HttpClient

Для возврата данных из одной или нескольких конечных точек веб-API типизированный класс <xref:System.Net.Http.HttpClient> использует один или несколько экземпляров класса <xref:System.Net.Http.HttpClient> приложения (заданных по умолчанию или именованных).

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Компоненты внедряют типизированный класс <xref:System.Net.Http.HttpClient> для вызова веб-API.

Компонент `FetchData` (`Pages/FetchData.razor`):

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>`HttpClient` и `HttpRequestMessage` с параметрами запроса API Fetch

При выполнении в WebAssembly в приложении Blazor WebAssembly для настройки запросов можно использовать [`HttpClient`](xref:fundamentals/http-requests) ([документация по API](xref:System.Net.Http.HttpClient)) и <xref:System.Net.Http.HttpRequestMessage>. Например, можно указать метод HTTP и заголовки запроса. Следующий компонент выполняет запрос `POST` к конечной точке API списка дел на сервере и отображает текст ответа:

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

Реализация .NET WebAssembly <xref:System.Net.Http.HttpClient> использует [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Извлечение позволяет настроить несколько [параметров, связанных с запросами](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Параметры запроса HTTP на извлечение можно настроить с помощью методов расширения <xref:System.Net.Http.HttpRequestMessage>, приведенных в следующей таблице.

| Метод расширения | Свойство запроса на извлечение |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Вы можете задать дополнительные параметры с помощью более универсального метода расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>.
 
HTTP-ответ обычно помещается в буфер в приложении Blazor WebAssembly для выполнения операций синхронизации содержимого ответа. Для выполнения потоковой передачи ответов используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> для запроса.

Чтобы включить учетные данные в запрос независимо от источника, используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>.

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Дополнительные сведения о возможностях Fetch API см. в разделе [Веб-документы MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

## <a name="handle-errors"></a>Обработка ошибок

Ошибки, возникающие во время взаимодействия с веб-API, могут обрабатываться кодом разработчика. Например, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ожидает ответа JSON от API сервера с заголовком `Content-Type` типа `application/json`. Если формат ответа отличается от JSON, при проверке содержимого возникает исключение <xref:System.NotSupportedException>.

В следующем примере показана неправильно написанная конечная точка URI для запроса данных прогноза погоды. URI должен иметь вид `WeatherForecast`, но отображается в вызове как `WeatherForcast` (без "e").

Вызов <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ожидает возврата JSON, но сервер возвращает HTML для необработанного исключения на сервере с заголовком `Content-Type` типа `text/html`. Необработанное исключение возникает на сервере, так как путь не найден, а ПО промежуточного слоя не может обслуживать страницу или представление для запроса.

Если выясняется, что содержимое ответа не является содержимым формата JSON, в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> на клиенте возникает исключение <xref:System.NotSupportedException>. Исключение перехватывается в блоке `catch`, где пользовательская логика может зарегистрировать ошибку или вывести понятное сообщение об ошибке для пользователя:

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> Предыдущий пример приведен только в качестве демонстрации. Серверное приложение веб-API можно настроить для возврата JSON, даже если не существует конечная точка или на сервере возникает необработанное исключение.

Для получения дополнительной информации см. <xref:blazor/fundamentals/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Общий доступ к ресурсам независимо от источника (CORS)

Безопасность в браузере предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу. Это ограничение называется *политика одного источника*. Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта. Для выполнения запросов из браузера к конечной точке с другим источником *конечная точка* должна включать [общий доступ к ресурсам независимо от источника (CORS)](https://www.w3.org/TR/cors/).

В [примере приложения Blazor WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) показано использование CORS в компоненте вызова веб-API (`Pages/CallWebAPI.razor`).

Дополнительные сведения о CORS с запросами безопасности в приложениях Blazor см. в разделе <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.

Общие сведения о CORS с приложениями ASP.NET Core см. в статье <xref:security/cors>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/security/webassembly/additional-scenarios>. Сведения об использовании класса <xref:System.Net.Http.HttpClient> для отправки безопасных запросов веб-интерфейса API.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
::: moniker range=">= aspnetcore-5.0"
* [Конфигурация конечных точек HTTPS Kestrel](xref:fundamentals/servers/kestrel/endpoints)
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* [Конфигурация конечных точек HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
::: moniker-end
* [Общий доступ к ресурсам независимо от источника (CORS) в W3C](https://www.w3.org/TR/cors/)
