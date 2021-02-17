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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551283"
---
<span data-ttu-id="ac651-101">Для созданного Identity кода базы данных требуется [Entity Framework Core миграций](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="ac651-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="ac651-102">Создайте миграцию и обновите базу данных.</span><span class="sxs-lookup"><span data-stu-id="ac651-102">Create a migration and update the database.</span></span> <span data-ttu-id="ac651-103">Например, выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="ac651-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac651-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac651-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac651-105">В **консоли диспетчера пакетов** Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="ac651-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="ac651-106">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="ac651-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="ac651-107">IdentityПараметр имени "CREATE SCHEMA" для `Add-Migration` команды является произвольным.</span><span class="sxs-lookup"><span data-stu-id="ac651-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="ac651-108">`"CreateIdentitySchema"` Описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="ac651-108">`"CreateIdentitySchema"` describes the migration.</span></span>
