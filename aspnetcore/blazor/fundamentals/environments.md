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
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="3b34d-103">Среды ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="3b34d-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="3b34d-104">Эта статья относится к Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3b34d-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="3b34d-105">Общие рекомендации по конфигурации приложений ASP.NET Core, в которой описываются подходы к использованию приложений Blazor Server, см. в разделе <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3b34d-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3b34d-106">При локальном запуске приложения среда по умолчанию имеет значение Development.</span><span class="sxs-lookup"><span data-stu-id="3b34d-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="3b34d-107">При публикации приложения среда по умолчанию имеет значение Production.</span><span class="sxs-lookup"><span data-stu-id="3b34d-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="3b34d-108">Клиентское приложение Blazor ( *`Client`* ) размещенного решения Blazor WebAssembly выбирает среду в приложении *`Server`* решения через ПО промежуточного слоя, которое передает данные о среде в браузер.</span><span class="sxs-lookup"><span data-stu-id="3b34d-108">The client-side Blazor app (*`Client`*) of a hosted Blazor WebAssembly solution determines the environment from the *`Server`* app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="3b34d-109">Приложение *`Server`* добавляет заголовок с именем `blazor-environment` и средой в качестве значения.</span><span class="sxs-lookup"><span data-stu-id="3b34d-109">The *`Server`* app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="3b34d-110">Приложение *`Client`* считывает заголовок.</span><span class="sxs-lookup"><span data-stu-id="3b34d-110">The *`Client`* app reads the header.</span></span> <span data-ttu-id="3b34d-111">Приложение *`Server`* решения — это приложение ASP.NET Core, поэтому дополнительные сведения о настройке среды см. здесь: <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3b34d-111">The *`Server`* app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3b34d-112">Для автономного приложения Blazor WebAssembly, выполняемого локально, сервер разработки добавляет заголовок `blazor-environment`, чтобы указать среду разработки.</span><span class="sxs-lookup"><span data-stu-id="3b34d-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="3b34d-113">Чтобы указать среду для других сред размещения, добавьте заголовок `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="3b34d-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="3b34d-114">В приведенном ниже примере для служб IIS пользовательский заголовок (`blazor-environment`) добавляется в опубликованный файл `web.config`.</span><span class="sxs-lookup"><span data-stu-id="3b34d-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="3b34d-115">Файл `web.config` находится в папке `bin/Release/{TARGET FRAMEWORK}/publish`, где заполнитель `{TARGET FRAMEWORK}` — это целевая платформа.</span><span class="sxs-lookup"><span data-stu-id="3b34d-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

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
> <span data-ttu-id="3b34d-116">Сведения об использовании пользовательского файла `web.config` для служб IIS, который не перезаписывается при публикации приложения в папку `publish`, см. в разделе <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="3b34d-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="3b34d-117">Чтобы получить среду приложения в компоненте, вставьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> и прочтите свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>.</span><span class="sxs-lookup"><span data-stu-id="3b34d-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="3b34d-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="3b34d-118">`Pages/ReadEnvironment.razor`:</span></span>

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

<span data-ttu-id="3b34d-119">Во время запуска <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> предоставляет <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, которое позволяет реализовать в коде построителя узлов логику для конкретной среды.</span><span class="sxs-lookup"><span data-stu-id="3b34d-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="3b34d-120">В методе `Program.Main` в файле `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="3b34d-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="3b34d-121">Следующие удобные методы расширения, предоставляемые через <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions>, допускают проверку текущей среды на признак среды разработки, тестирования, коммерческой среды или пользовательской среды.</span><span class="sxs-lookup"><span data-stu-id="3b34d-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="3b34d-122">В методе `Program.Main` в файле `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="3b34d-122">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="3b34d-123">Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> можно использовать во время запуска, если служба <xref:Microsoft.AspNetCore.Components.NavigationManager> недоступна.</span><span class="sxs-lookup"><span data-stu-id="3b34d-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b34d-124">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3b34d-124">Additional resources</span></span>

* <xref:fundamentals/environments>
