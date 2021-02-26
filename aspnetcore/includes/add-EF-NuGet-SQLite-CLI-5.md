---
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
ms.openlocfilehash: 27cb64dc8734fcb6d165795515096c9d9dd2a9cc
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551452"
---
<span data-ttu-id="cfabd-101">Выполните следующие команды интерфейса командной строки .NET:</span><span class="sxs-lookup"><span data-stu-id="cfabd-101">Run the following .NET CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="cfabd-102">Приведенные выше команды добавляют следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="cfabd-102">The preceding commands add:</span></span>

* <span data-ttu-id="cfabd-103">[средство формирования шаблонов](xref:fundamentals/tools/dotnet-aspnet-codegenerator);</span><span class="sxs-lookup"><span data-stu-id="cfabd-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="cfabd-104">средства EF Core для интерфейса командной строки .NET;</span><span class="sxs-lookup"><span data-stu-id="cfabd-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="cfabd-105">поставщик EF Core для SQLite, который устанавливает пакет EF Core в качестве зависимости;</span><span class="sxs-lookup"><span data-stu-id="cfabd-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="cfabd-106">Пакеты, необходимые для формирования шаблонов: `Microsoft.VisualStudio.Web.CodeGeneration.Design` и `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="cfabd-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="cfabd-107">Рекомендации по настройке нескольких сред, позволяющей приложению настраивать свои контексты баз данных в среде, см. в разделе <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span><span class="sxs-lookup"><span data-stu-id="cfabd-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
