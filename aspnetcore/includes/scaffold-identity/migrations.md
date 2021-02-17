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
Для созданного Identity кода базы данных требуется [Entity Framework Core миграций](/ef/core/managing-schemas/migrations/). Создайте миграцию и обновите базу данных. Например, выполните следующие команды:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В **консоли диспетчера пакетов** Visual Studio:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

IdentityПараметр имени "CREATE SCHEMA" для `Add-Migration` команды является произвольным. `"CreateIdentitySchema"` Описывает миграцию.
