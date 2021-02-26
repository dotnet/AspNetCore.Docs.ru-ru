---
title: Статические файлы Blazor в ASP.NET Core
author: guardrex
description: Сведения о настройке статических файлов для приложений и управлении ими для приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/static-files
ms.openlocfilehash: 709250251113014027fe86c6a373e58a9c2a5009
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100109961"
---
# <a name="aspnet-core-blazor-static-files"></a><span data-ttu-id="aa25c-103">Статические файлы Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa25c-103">ASP.NET Core Blazor static files</span></span>

<span data-ttu-id="aa25c-104">*Эта статься относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="aa25c-104">*This article applies to Blazor Server.*</span></span>

<span data-ttu-id="aa25c-105">Чтобы создать дополнительные сопоставления файлов с помощью <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> или настроить другие <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, используйте **один** из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="aa25c-105">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="aa25c-106">В следующих примерах заполнитель `{EXTENSION}` является расширением файла, а заполнитель `{CONTENT TYPE}` — типом содержимого.</span><span class="sxs-lookup"><span data-stu-id="aa25c-106">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="aa25c-107">Настройте параметры с помощью [внедрения зависимостей (DI)](xref:blazor/fundamentals/dependency-injection) в `Startup.ConfigureServices` (`Startup.cs`) с помощью <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="aa25c-107">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="aa25c-108">Так как в этом способе настраивается тот же поставщик файлов, который используется для обслуживания `blazor.server.js`, убедитесь, что настраиваемая конфигурация не мешает обслуживать `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="aa25c-108">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="aa25c-109">Например, не удаляйте сопоставление для файлов JavaScript, настраивая поставщик с помощью `provider.Mappings.Remove(".js")`.</span><span class="sxs-lookup"><span data-stu-id="aa25c-109">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="aa25c-110">Используйте два вызова <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure` (`Startup.cs`).</span><span class="sxs-lookup"><span data-stu-id="aa25c-110">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="aa25c-111">Настройте пользовательский поставщик файлов при первом вызове с помощью <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="aa25c-111">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="aa25c-112">Второе ПО промежуточного слоя обслуживает `blazor.server.js`, в котором используется конфигурация статических файлов по умолчанию, предоставляемая платформой Blazor.</span><span class="sxs-lookup"><span data-stu-id="aa25c-112">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="aa25c-113">Чтобы избежать конфликтов при обслуживании `_framework/blazor.server.js`, можете использовать <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> для выполнения пользовательского ПО промежуточного слоя для статических файлов.</span><span class="sxs-lookup"><span data-stu-id="aa25c-113">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```
