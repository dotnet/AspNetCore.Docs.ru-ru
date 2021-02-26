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
ms.openlocfilehash: 1a9e98a84093f89f3859454f482dfe59c8504d9b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551454"
---
<span data-ttu-id="57cea-101">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="57cea-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

<span data-ttu-id="57cea-102">Приведенные выше команды добавляют следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="57cea-102">The preceding commands add:</span></span>

* <span data-ttu-id="57cea-103">[средство формирования шаблонов](xref:fundamentals/tools/dotnet-aspnet-codegenerator);</span><span class="sxs-lookup"><span data-stu-id="57cea-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="57cea-104">средства Entity Framework Core для .NET Core CLI;</span><span class="sxs-lookup"><span data-stu-id="57cea-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="57cea-105">поставщик EF Core для SQLite, который устанавливает пакет EF Core в качестве зависимости;</span><span class="sxs-lookup"><span data-stu-id="57cea-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="57cea-106">Пакеты, необходимые для формирования шаблонов: `Microsoft.VisualStudio.Web.CodeGeneration.Design` и `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="57cea-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="57cea-107">Рекомендации по настройке нескольких сред, позволяющей приложению настраивать свои контексты баз данных в среде, см. в разделе <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span><span class="sxs-lookup"><span data-stu-id="57cea-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]