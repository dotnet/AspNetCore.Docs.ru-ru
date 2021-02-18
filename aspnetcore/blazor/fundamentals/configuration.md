---
title: Конфигурация Blazor ASP.NET Core
author: guardrex
description: Сведения о настройке приложений Blazor, включая параметры приложений, проверку подлинности и конфигурацию ведения журнала.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 48d78f40e9254bac182ffbc534550157664bcc5b
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106938"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="c73bc-103">Конфигурация Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c73bc-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="c73bc-104">Эта статья относится к Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="c73bc-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="c73bc-105">Общие рекомендации по настройке приложений ASP.NET Core см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c73bc-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="c73bc-106">Blazor WebAssembly по умолчанию загружает конфигурацию из следующих файлов параметров приложения:</span><span class="sxs-lookup"><span data-stu-id="c73bc-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="c73bc-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="c73bc-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="c73bc-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, где заполнитель `{ENVIRONMENT}` — это [среда выполнения](xref:fundamentals/environments) приложения.</span><span class="sxs-lookup"><span data-stu-id="c73bc-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="c73bc-109">Другие поставщики конфигурации, зарегистрированные приложением, также могут предоставлять конфигурацию. Однако не все поставщики или функции поставщиков подходят для приложений Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="c73bc-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="c73bc-110">[Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration): поставщик не поддерживается для сценариев управляемого удостоверения и идентификатора приложения (идентификатор клиента) с секретом клиента.</span><span class="sxs-lookup"><span data-stu-id="c73bc-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="c73bc-111">Идентификатор приложения с секретом клиента не рекомендуется для приложений ASP.NET Core, особенно приложений Blazor WebAssembly, так как секрет клиента невозможно защитить на стороне клиента для доступа к службе Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="c73bc-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="c73bc-112">[Поставщик конфигурации приложения Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): поставщик не подходит для приложений Blazor WebAssembly, так как приложения Blazor WebAssembly не выполняются на сервере в Azure.</span><span class="sxs-lookup"><span data-stu-id="c73bc-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="c73bc-113">Конфигурация в приложении Blazor WebAssembly видна пользователям.</span><span class="sxs-lookup"><span data-stu-id="c73bc-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="c73bc-114">**Не храните секреты приложения, учетные данные или другие конфиденциальные данные в конфигурации приложения Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="c73bc-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="c73bc-115">Дополнительные сведения о поставщиках конфигурации см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c73bc-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="c73bc-116">Конфигурация параметров приложения</span><span class="sxs-lookup"><span data-stu-id="c73bc-116">App settings configuration</span></span>

<span data-ttu-id="c73bc-117">По умолчанию загружается конфигурация из файлов параметров приложения.</span><span class="sxs-lookup"><span data-stu-id="c73bc-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="c73bc-118">В приведенном ниже примере значение конфигурации пользовательского интерфейса хранится в файле параметров приложения и автоматически загружается платформой Blazor.</span><span class="sxs-lookup"><span data-stu-id="c73bc-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="c73bc-119">Значение считывается компонентом.</span><span class="sxs-lookup"><span data-stu-id="c73bc-119">The value is read by a component.</span></span>

<span data-ttu-id="c73bc-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="c73bc-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="c73bc-121">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c73bc-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="c73bc-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="c73bc-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="c73bc-123">Для чтения других файлов конфигурации из папки `wwwroot` в конфигурацию используйте <xref:System.Net.Http.HttpClient> для получения содержимого файла.</span><span class="sxs-lookup"><span data-stu-id="c73bc-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="c73bc-124">В приведенном ниже примере данные из файла конфигурации (`cars.json`) считываются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="c73bc-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="c73bc-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="c73bc-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="c73bc-126">Добавьте пространство имен для <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> в `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="c73bc-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="c73bc-127">В методе `Program.Main` в файле `Program.cs` измените существующую регистрацию службы <xref:System.Net.Http.HttpClient>, чтобы использовать клиент для считывания файла.</span><span class="sxs-lookup"><span data-stu-id="c73bc-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="memory-configuration-source"></a><span data-ttu-id="c73bc-128">Источник конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="c73bc-128">Memory Configuration Source</span></span>

<span data-ttu-id="c73bc-129">В приведенном ниже примере используется <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> в `Program.Main` для предоставления дополнительной конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c73bc-129">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="c73bc-130">Добавьте пространство имен для <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> в `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="c73bc-130">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="c73bc-131">В методе `Program.Main` в файле `Program.cs` выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="c73bc-131">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="c73bc-132">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c73bc-132">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="c73bc-133">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="c73bc-133">`Pages/MemoryConfig.razor`:</span></span>

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

<span data-ttu-id="c73bc-134">Получите раздел конфигурации в коде C# с помощью <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c73bc-134">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c73bc-135">В приведенном ниже примере извлекается раздел `wheels` конфигурации из предыдущего примера.</span><span class="sxs-lookup"><span data-stu-id="c73bc-135">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="c73bc-136">Конфигурация проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="c73bc-136">Authentication configuration</span></span>

<span data-ttu-id="c73bc-137">Укажите конфигурацию проверки подлинности в файле параметров приложения.</span><span class="sxs-lookup"><span data-stu-id="c73bc-137">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="c73bc-138">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="c73bc-138">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="c73bc-139">Загрузите конфигурацию для поставщика Identity с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="c73bc-139">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="c73bc-140">В приведенном ниже примере загружается конфигурация для [поставщика OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).</span><span class="sxs-lookup"><span data-stu-id="c73bc-140">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="c73bc-141">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="c73bc-141">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="c73bc-142">Конфигурация ведения журнала</span><span class="sxs-lookup"><span data-stu-id="c73bc-142">Logging configuration</span></span>

<span data-ttu-id="c73bc-143">Добавьте ссылку на пакет [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) в файл проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="c73bc-143">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="c73bc-144">В файле параметров приложения укажите конфигурацию ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c73bc-144">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="c73bc-145">Конфигурация ведения журнала загружается в методе `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="c73bc-145">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="c73bc-146">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="c73bc-146">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="c73bc-147">Добавьте пространство имен для <xref:Microsoft.Extensions.Logging?displayProperty=fullName> в `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="c73bc-147">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="c73bc-148">В методе `Program.Main` в файле `Program.cs` выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="c73bc-148">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="c73bc-149">Конфигурация построителя узлов</span><span class="sxs-lookup"><span data-stu-id="c73bc-149">Host builder configuration</span></span>

<span data-ttu-id="c73bc-150">Считайте конфигурацию построителя узлов из <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> в методе `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="c73bc-150">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="c73bc-151">В методе `Program.Main` в файле `Program.cs` выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="c73bc-151">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="c73bc-152">Кэшированная конфигурация</span><span class="sxs-lookup"><span data-stu-id="c73bc-152">Cached configuration</span></span>

<span data-ttu-id="c73bc-153">Файлы конфигурации кэшируются для автономного использования.</span><span class="sxs-lookup"><span data-stu-id="c73bc-153">Configuration files are cached for offline use.</span></span> <span data-ttu-id="c73bc-154">При использовании [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app) файлы конфигурации можно изменять только при создании развертывания.</span><span class="sxs-lookup"><span data-stu-id="c73bc-154">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="c73bc-155">Изменение файлов конфигурации между развертываниями не работает по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="c73bc-155">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="c73bc-156">У пользователей есть кэшированные версии файлов, которые они продолжают использовать.</span><span class="sxs-lookup"><span data-stu-id="c73bc-156">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="c73bc-157">Файлы `service-worker.js` и `service-worker-assets.js` PWA должны быть перестроены при компиляции, что при следующем входе пользователя в сеть сообщает приложению о том, что приложение было развернуто повторно.</span><span class="sxs-lookup"><span data-stu-id="c73bc-157">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="c73bc-158">Дополнительные сведения об обработке фоновых изменений с помощью PWA см. на странице <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="c73bc-158">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
