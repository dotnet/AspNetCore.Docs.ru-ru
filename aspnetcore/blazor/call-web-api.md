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
ms.openlocfilehash: 18afa7faa254f89ef664e0188be357a85c790523
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252439"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a><span data-ttu-id="d81b0-103">Вызов веб-API из ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d81b0-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="d81b0-104">Авторы: [Люк Латэм (Luke Latham)](https://github.com/guardrex), [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Хуан Де ла Круз (Juan De la Cruz)](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="d81b0-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

> [!NOTE]
> <span data-ttu-id="d81b0-105">Эта статья относится к Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="d81b0-105">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="d81b0-106">[Приложения Blazor Server](xref:blazor/hosting-models#blazor-server) вызывают веб-интерфейсы API с помощью экземпляров <xref:System.Net.Http.HttpClient>, обычно созданных с помощью <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="d81b0-107">Инструкции, относящиеся к Blazor Server, см. в статье <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-107">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="d81b0-108">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) вызывают веб-интерфейсы API с помощью предварительно настроенной службы <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="d81b0-109">Составляйте запросы, которые могут включать параметры JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API), используя вспомогательные методы JSON Blazor или с помощью <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-109">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="d81b0-110">Служба <xref:System.Net.Http.HttpClient> в приложениях Blazor WebAssembly направляет запросы обратно к серверу-источнику.</span><span class="sxs-lookup"><span data-stu-id="d81b0-110">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="d81b0-111">Рекомендации в этом разделе относятся только к приложениям Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="d81b0-111">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="d81b0-112">[Просмотрите или скачайте пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([описание процедуры скачивания](xref:index#how-to-download-a-sample)). Выберите приложение `BlazorWebAssemblySample`.</span><span class="sxs-lookup"><span data-stu-id="d81b0-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="d81b0-113">См. следующие компоненты в примере приложения `BlazorWebAssemblySample`:</span><span class="sxs-lookup"><span data-stu-id="d81b0-113">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="d81b0-114">Вызов веб-API (`Pages/CallWebAPI.razor`)</span><span class="sxs-lookup"><span data-stu-id="d81b0-114">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="d81b0-115">Тестер HTTP-запросов (`Components/HTTPRequestTester.razor`)</span><span class="sxs-lookup"><span data-stu-id="d81b0-115">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="d81b0-116">Пакеты</span><span class="sxs-lookup"><span data-stu-id="d81b0-116">Packages</span></span>

<span data-ttu-id="d81b0-117">Сошлитесь на пакет NuGet [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="d81b0-117">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="d81b0-118">Добавление службы HttpClient</span><span class="sxs-lookup"><span data-stu-id="d81b0-118">Add the HttpClient service</span></span>

<span data-ttu-id="d81b0-119">В `Program.Main` добавьте службу <xref:System.Net.Http.HttpClient>, если она еще не существует:</span><span class="sxs-lookup"><span data-stu-id="d81b0-119">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="d81b0-120">HttpClient и вспомогательные методы JSON</span><span class="sxs-lookup"><span data-stu-id="d81b0-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="d81b0-121">В приложении Blazor WebAssembly [`HttpClient`](xref:fundamentals/http-requests) доступен в качестве предварительно настроенной службы для отправки запросов обратно к серверу-источнику.</span><span class="sxs-lookup"><span data-stu-id="d81b0-121">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="d81b0-122">Приложение Blazor Server не включает службу <xref:System.Net.Http.HttpClient> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d81b0-122">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="d81b0-123">Предоставьте <xref:System.Net.Http.HttpClient> для приложения с помощью [инфраструктуры фабрики `HttpClient`](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="d81b0-123">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="d81b0-124"><xref:System.Net.Http.HttpClient> и вспомогательные методы JSON также используются для вызова сторонних конечных точек веб-API.</span><span class="sxs-lookup"><span data-stu-id="d81b0-124"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="d81b0-125"><xref:System.Net.Http.HttpClient> реализуется с помощью [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) браузера и зависит от его ограничений, включая принудительное применение той же политики источника.</span><span class="sxs-lookup"><span data-stu-id="d81b0-125"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="d81b0-126">Базовый адрес клиента устанавливается как адрес сервера-источника.</span><span class="sxs-lookup"><span data-stu-id="d81b0-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="d81b0-127">Внедрите экземпляр <xref:System.Net.Http.HttpClient> с помощью директивы [`@inject`](xref:mvc/views/razor#inject):</span><span class="sxs-lookup"><span data-stu-id="d81b0-127">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="d81b0-128">В следующих примерах веб-API Todo обрабатывает операции создания, чтения, обновления и удаления (CRUD).</span><span class="sxs-lookup"><span data-stu-id="d81b0-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="d81b0-129">Примеры основаны на классе `TodoItem`, в котором хранится:</span><span class="sxs-lookup"><span data-stu-id="d81b0-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="d81b0-130">Идентификатор (`Id`, `long`): уникальный идентификатор элемента.</span><span class="sxs-lookup"><span data-stu-id="d81b0-130">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="d81b0-131">Имя (`Name`, `string`). Имя элемента.</span><span class="sxs-lookup"><span data-stu-id="d81b0-131">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="d81b0-132">Состояние (`IsComplete`, `bool`): указание того, завершен ли элемент Todo.</span><span class="sxs-lookup"><span data-stu-id="d81b0-132">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="d81b0-133">Вспомогательные методы JSON отправляют запросы к URI (веб-API в следующих примерах) и обрабатывают ответ:</span><span class="sxs-lookup"><span data-stu-id="d81b0-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="d81b0-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>. Отправляет запрос HTTP GET и анализирует текст ответа JSON для создания объекта.</span><span class="sxs-lookup"><span data-stu-id="d81b0-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="d81b0-135">В следующем коде `todoItems` отображаются компонентом.</span><span class="sxs-lookup"><span data-stu-id="d81b0-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="d81b0-136">Метод `GetTodoItems` срабатывает после завершения подготовки компонента к просмотру ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="d81b0-136">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="d81b0-137">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="d81b0-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="d81b0-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>. Отправляет запрос HTTP POST, включая содержимое в кодировке JSON, и анализирует текст ответа JSON для создания объекта.</span><span class="sxs-lookup"><span data-stu-id="d81b0-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="d81b0-139">В следующем коде `newItemName` предоставляется связанным элементом компонента.</span><span class="sxs-lookup"><span data-stu-id="d81b0-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="d81b0-140">Метод `AddItem` активируется путем выбора элемента `<button>`.</span><span class="sxs-lookup"><span data-stu-id="d81b0-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="d81b0-141">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="d81b0-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="d81b0-142">Вызовы к <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> возвращают <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-142">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="d81b0-143">Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="d81b0-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="d81b0-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>. Отправляет запрос HTTP PUT, включая содержимое в кодировке JSON.</span><span class="sxs-lookup"><span data-stu-id="d81b0-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="d81b0-145">В следующем коде значения `editItem` для `Name` и `IsCompleted` предоставляются связанными элементами компонента.</span><span class="sxs-lookup"><span data-stu-id="d81b0-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="d81b0-146">Элемент `Id` задается, когда элемент выбирается в другой части пользовательского интерфейса и вызывается `EditItem`.</span><span class="sxs-lookup"><span data-stu-id="d81b0-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="d81b0-147">Метод `SaveItem` активируется путем выбора элемента `<button>` "Save".</span><span class="sxs-lookup"><span data-stu-id="d81b0-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="d81b0-148">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="d81b0-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="d81b0-149">Вызовы к <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> возвращают <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-149">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="d81b0-150">Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="d81b0-150">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="d81b0-151"><xref:System.Net.Http> включает дополнительные методы расширения для отправки HTTP-запросов и получения HTTP-ответов.</span><span class="sxs-lookup"><span data-stu-id="d81b0-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="d81b0-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> используется для отправки запроса HTTP DELETE в веб-интерфейс API.</span><span class="sxs-lookup"><span data-stu-id="d81b0-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="d81b0-153">В следующем коде элемент `<button>` "Delete" вызывает метод `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="d81b0-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="d81b0-154">Связанный элемент `<input>` предоставляет `id` удаляемого элемента.</span><span class="sxs-lookup"><span data-stu-id="d81b0-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="d81b0-155">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="d81b0-155">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="d81b0-156">Именованный класс HttpClient с IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="d81b0-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="d81b0-157">Поддерживаются службы <xref:System.Net.Http.IHttpClientFactory> и конфигурация именованного класса <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="d81b0-158">Сошлитесь на пакет NuGet [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="d81b0-158">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package in the project file.</span></span>

<span data-ttu-id="d81b0-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="d81b0-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="d81b0-160">Компонент `FetchData` (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="d81b0-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="d81b0-161">Типизированный класс HttpClient</span><span class="sxs-lookup"><span data-stu-id="d81b0-161">Typed HttpClient</span></span>

<span data-ttu-id="d81b0-162">Для возврата данных из одной или нескольких конечных точек веб-API типизированный класс <xref:System.Net.Http.HttpClient> использует один или несколько экземпляров класса <xref:System.Net.Http.HttpClient> приложения (заданных по умолчанию или именованных).</span><span class="sxs-lookup"><span data-stu-id="d81b0-162">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="d81b0-163">`WeatherForecastClient.cs`.</span><span class="sxs-lookup"><span data-stu-id="d81b0-163">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="d81b0-164">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="d81b0-164">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="d81b0-165">Компоненты внедряют типизированный класс <xref:System.Net.Http.HttpClient> для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="d81b0-165">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="d81b0-166">Компонент `FetchData` (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="d81b0-166">`FetchData` component (`Pages/FetchData.razor`):</span></span>

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

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="d81b0-167">`HttpClient` и `HttpRequestMessage` с параметрами запроса API Fetch</span><span class="sxs-lookup"><span data-stu-id="d81b0-167">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="d81b0-168">При выполнении в WebAssembly в приложении Blazor WebAssembly для настройки запросов можно использовать [`HttpClient`](xref:fundamentals/http-requests) ([документация по API](xref:System.Net.Http.HttpClient)) и <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-168">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="d81b0-169">Например, можно указать метод HTTP и заголовки запроса.</span><span class="sxs-lookup"><span data-stu-id="d81b0-169">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="d81b0-170">Следующий компонент выполняет запрос `POST` к конечной точке API списка дел на сервере и отображает текст ответа:</span><span class="sxs-lookup"><span data-stu-id="d81b0-170">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="d81b0-171">Реализация .NET WebAssembly <xref:System.Net.Http.HttpClient> использует [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="d81b0-171">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="d81b0-172">Извлечение позволяет настроить несколько [параметров, связанных с запросами](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="d81b0-172">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="d81b0-173">Параметры запроса HTTP на извлечение можно настроить с помощью методов расширения <xref:System.Net.Http.HttpRequestMessage>, приведенных в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="d81b0-173">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="d81b0-174">Метод расширения</span><span class="sxs-lookup"><span data-stu-id="d81b0-174">Extension method</span></span> | <span data-ttu-id="d81b0-175">Свойство запроса на извлечение</span><span class="sxs-lookup"><span data-stu-id="d81b0-175">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="d81b0-176">Вы можете задать дополнительные параметры с помощью более универсального метода расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-176">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="d81b0-177">HTTP-ответ обычно помещается в буфер в приложении Blazor WebAssembly для выполнения операций синхронизации содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="d81b0-177">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="d81b0-178">Для выполнения потоковой передачи ответов используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> для запроса.</span><span class="sxs-lookup"><span data-stu-id="d81b0-178">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="d81b0-179">Чтобы включить учетные данные в запрос независимо от источника, используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-179">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="d81b0-180">Дополнительные сведения о возможностях Fetch API см. в разделе [Веб-документы MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="d81b0-180">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="handle-errors"></a><span data-ttu-id="d81b0-181">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="d81b0-181">Handle errors</span></span>

<span data-ttu-id="d81b0-182">Ошибки, возникающие во время взаимодействия с веб-API, могут обрабатываться кодом разработчика.</span><span class="sxs-lookup"><span data-stu-id="d81b0-182">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="d81b0-183">Например, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ожидает ответа JSON от API сервера с заголовком `Content-Type` типа `application/json`.</span><span class="sxs-lookup"><span data-stu-id="d81b0-183">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="d81b0-184">Если формат ответа отличается от JSON, при проверке содержимого возникает исключение <xref:System.NotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-184">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="d81b0-185">В следующем примере показана неправильно написанная конечная точка URI для запроса данных прогноза погоды.</span><span class="sxs-lookup"><span data-stu-id="d81b0-185">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="d81b0-186">URI должен иметь вид `WeatherForecast`, но отображается в вызове как `WeatherForcast` (без "e").</span><span class="sxs-lookup"><span data-stu-id="d81b0-186">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="d81b0-187">Вызов <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ожидает возврата JSON, но сервер возвращает HTML для необработанного исключения на сервере с заголовком `Content-Type` типа `text/html`.</span><span class="sxs-lookup"><span data-stu-id="d81b0-187">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="d81b0-188">Необработанное исключение возникает на сервере, так как путь не найден, а ПО промежуточного слоя не может обслуживать страницу или представление для запроса.</span><span class="sxs-lookup"><span data-stu-id="d81b0-188">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="d81b0-189">Если выясняется, что содержимое ответа не является содержимым формата JSON, в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> на клиенте возникает исключение <xref:System.NotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-189">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="d81b0-190">Исключение перехватывается в блоке `catch`, где пользовательская логика может зарегистрировать ошибку или вывести понятное сообщение об ошибке для пользователя:</span><span class="sxs-lookup"><span data-stu-id="d81b0-190">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="d81b0-191">Предыдущий пример приведен только в качестве демонстрации.</span><span class="sxs-lookup"><span data-stu-id="d81b0-191">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="d81b0-192">Серверное приложение веб-API можно настроить для возврата JSON, даже если не существует конечная точка или на сервере возникает необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="d81b0-192">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="d81b0-193">Для получения дополнительной информации см. <xref:blazor/fundamentals/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-193">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="d81b0-194">Общий доступ к ресурсам независимо от источника (CORS)</span><span class="sxs-lookup"><span data-stu-id="d81b0-194">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="d81b0-195">Безопасность в браузере предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="d81b0-195">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="d81b0-196">Это ограничение называется *политика одного источника*.</span><span class="sxs-lookup"><span data-stu-id="d81b0-196">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="d81b0-197">Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="d81b0-197">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="d81b0-198">Для выполнения запросов из браузера к конечной точке с другим источником *конечная точка* должна включать [общий доступ к ресурсам независимо от источника (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="d81b0-198">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="d81b0-199">В [примере приложения Blazor WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) показано использование CORS в компоненте вызова веб-API (`Pages/CallWebAPI.razor`).</span><span class="sxs-lookup"><span data-stu-id="d81b0-199">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="d81b0-200">Дополнительные сведения о CORS с запросами безопасности в приложениях Blazor см. в разделе <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-200">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="d81b0-201">Общие сведения о CORS с приложениями ASP.NET Core см. в статье <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="d81b0-201">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d81b0-202">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d81b0-202">Additional resources</span></span>

* <span data-ttu-id="d81b0-203"><xref:blazor/security/webassembly/additional-scenarios>. Сведения об использовании класса <xref:System.Net.Http.HttpClient> для отправки безопасных запросов веб-интерфейса API.</span><span class="sxs-lookup"><span data-stu-id="d81b0-203"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
::: moniker range=">= aspnetcore-5.0"
* [<span data-ttu-id="d81b0-204">Конфигурация конечных точек HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="d81b0-204">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel/endpoints)
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* [<span data-ttu-id="d81b0-205">Конфигурация конечных точек HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="d81b0-205">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
::: moniker-end
* [<span data-ttu-id="d81b0-206">Общий доступ к ресурсам независимо от источника (CORS) в W3C</span><span class="sxs-lookup"><span data-stu-id="d81b0-206">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
