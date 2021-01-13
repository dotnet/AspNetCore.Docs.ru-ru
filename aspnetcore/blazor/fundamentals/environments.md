---
title: Среды ASP.NET Core Blazor
author: guardrex
description: Сведения о средах в Blazor, в том числе о настройке среды для приложения Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 3d9b0cab42a826c7a5868324d891e597cd9ed986
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97678295"
---
# <a name="aspnet-core-no-locblazor-environments"></a>Среды ASP.NET Core Blazor

> [!NOTE]
> Эта статья относится к Blazor WebAssembly. Общие рекомендации по конфигурации приложений ASP.NET Core, в которой описываются подходы к использованию приложений Blazor Server, см. в разделе <xref:fundamentals/environments>.

При локальном запуске приложения среда по умолчанию имеет значение Development. При публикации приложения среда по умолчанию имеет значение Production.

Клиентское приложение Blazor ( *`Client`* ) размещенного решения Blazor WebAssembly выбирает среду в приложении *`Server`* решения через ПО промежуточного слоя, которое передает данные о среде в браузер. Приложение *`Server`* добавляет заголовок с именем `blazor-environment` и средой в качестве значения. Приложение *`Client`* считывает заголовок. Приложение *`Server`* решения — это приложение ASP.NET Core, поэтому дополнительные сведения о настройке среды см. здесь: <xref:fundamentals/environments>.

Для автономного приложения Blazor WebAssembly, выполняемого локально, сервер разработки добавляет заголовок `blazor-environment`, чтобы указать среду разработки. Чтобы указать среду для других сред размещения, добавьте заголовок `blazor-environment`.

В приведенном ниже примере для служб IIS пользовательский заголовок (`blazor-environment`) добавляется в опубликованный файл `web.config`. Файл `web.config` находится в папке `bin/Release/{TARGET FRAMEWORK}/publish`, где заполнитель `{TARGET FRAMEWORK}` — это целевая платформа.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Сведения об использовании пользовательского файла `web.config` для служб IIS, который не перезаписывается при публикации приложения в папку `publish`, см. в разделе <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.

Чтобы получить среду приложения в компоненте, вставьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> и прочтите свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>.

`Pages/ReadEnvironment.razor`:

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

Во время запуска <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> предоставляет <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, которое позволяет реализовать в коде построителя узлов логику для конкретной среды.

В методе `Program.Main` в файле `Program.cs`:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Следующие удобные методы расширения, предоставляемые через <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions>, допускают проверку текущей среды на признак среды разработки, тестирования, коммерческой среды или пользовательской среды.

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

В методе `Program.Main` в файле `Program.cs`:

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> можно использовать во время запуска, если служба <xref:Microsoft.AspNetCore.Components.NavigationManager> недоступна.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/environments>
