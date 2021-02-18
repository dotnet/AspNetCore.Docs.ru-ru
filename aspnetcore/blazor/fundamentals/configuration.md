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
# <a name="aspnet-core-blazor-configuration"></a>Конфигурация Blazor ASP.NET Core

> [!NOTE]
> Эта статья относится к Blazor WebAssembly. Общие рекомендации по настройке приложений ASP.NET Core см. в разделе <xref:fundamentals/configuration/index>.

Blazor WebAssembly по умолчанию загружает конфигурацию из следующих файлов параметров приложения:

* `wwwroot/appsettings.json`.
* `wwwroot/appsettings.{ENVIRONMENT}.json`, где заполнитель `{ENVIRONMENT}` — это [среда выполнения](xref:fundamentals/environments) приложения.

Другие поставщики конфигурации, зарегистрированные приложением, также могут предоставлять конфигурацию. Однако не все поставщики или функции поставщиков подходят для приложений Blazor WebAssembly.

* [Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration): поставщик не поддерживается для сценариев управляемого удостоверения и идентификатора приложения (идентификатор клиента) с секретом клиента. Идентификатор приложения с секретом клиента не рекомендуется для приложений ASP.NET Core, особенно приложений Blazor WebAssembly, так как секрет клиента невозможно защитить на стороне клиента для доступа к службе Azure Key Vault.
* [Поставщик конфигурации приложения Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): поставщик не подходит для приложений Blazor WebAssembly, так как приложения Blazor WebAssembly не выполняются на сервере в Azure.

> [!WARNING]
> Конфигурация в приложении Blazor WebAssembly видна пользователям. **Не храните секреты приложения, учетные данные или другие конфиденциальные данные в конфигурации приложения Blazor WebAssembly.**

Дополнительные сведения о поставщиках конфигурации см. в разделе <xref:fundamentals/configuration/index>.

## <a name="app-settings-configuration"></a>Конфигурация параметров приложения

По умолчанию загружается конфигурация из файлов параметров приложения. В приведенном ниже примере значение конфигурации пользовательского интерфейса хранится в файле параметров приложения и автоматически загружается платформой Blazor. Значение считывается компонентом.

`wwwroot/appsettings.json`:

```json
{
  "h1FontSize": "50px"
}
```

Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации.

`Pages/ConfigurationExample.razor`:

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

Для чтения других файлов конфигурации из папки `wwwroot` в конфигурацию используйте <xref:System.Net.Http.HttpClient> для получения содержимого файла. В приведенном ниже примере данные из файла конфигурации (`cars.json`) считываются в конфигурацию приложения.

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

Добавьте пространство имен для <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> в `Program.cs`.

```csharp
using Microsoft.Extensions.Configuration;
```

В методе `Program.Main` в файле `Program.cs` измените существующую регистрацию службы <xref:System.Net.Http.HttpClient>, чтобы использовать клиент для считывания файла.

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

## <a name="memory-configuration-source"></a>Источник конфигурации памяти

В приведенном ниже примере используется <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> в `Program.Main` для предоставления дополнительной конфигурации.

Добавьте пространство имен для <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> в `Program.cs`.

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

В методе `Program.Main` в файле `Program.cs` выполните указанные ниже действия.

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

Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации.

`Pages/MemoryConfig.razor`:

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

Получите раздел конфигурации в коде C# с помощью <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>. В приведенном ниже примере извлекается раздел `wheels` конфигурации из предыдущего примера.

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a>Конфигурация проверки подлинности

Укажите конфигурацию проверки подлинности в файле параметров приложения.

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Загрузите конфигурацию для поставщика Identity с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> в `Program.Main`. В приведенном ниже примере загружается конфигурация для [поставщика OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Конфигурация ведения журнала

Добавьте ссылку на пакет [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) в файл проекта приложения. В файле параметров приложения укажите конфигурацию ведения журнала. Конфигурация ведения журнала загружается в методе `Program.Main`.

`wwwroot/appsettings.json`:

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

Добавьте пространство имен для <xref:Microsoft.Extensions.Logging?displayProperty=fullName> в `Program.cs`.

```csharp
using Microsoft.Extensions.Logging;
```

В методе `Program.Main` в файле `Program.cs` выполните указанные ниже действия.

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Конфигурация построителя узлов

Считайте конфигурацию построителя узлов из <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> в методе `Program.Main`.

В методе `Program.Main` в файле `Program.cs` выполните указанные ниже действия.

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Кэшированная конфигурация

Файлы конфигурации кэшируются для автономного использования. При использовании [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app) файлы конфигурации можно изменять только при создании развертывания. Изменение файлов конфигурации между развертываниями не работает по следующим причинам:

* У пользователей есть кэшированные версии файлов, которые они продолжают использовать.
* Файлы `service-worker.js` и `service-worker-assets.js` PWA должны быть перестроены при компиляции, что при следующем входе пользователя в сеть сообщает приложению о том, что приложение было развернуто повторно.

Дополнительные сведения об обработке фоновых изменений с помощью PWA см. на странице <xref:blazor/progressive-web-app#background-updates>.
