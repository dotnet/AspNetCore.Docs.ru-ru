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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551576"
---
### <a name="view-the-identity-database"></a>Просмотр Identity базы данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

* В меню **вид** выберите **Обозреватель объектов SQL Server** (SSOX).
* Перейдите к **(LocalDB) MSSQLLocalDB (SQL Server 13)**. Щелкните dbo правой кнопкой мыши **. AspNetUsers**  >  **представления данных**:

![Контекстное меню в таблице AspNetUsers в обозреватель объектов SQL Server](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Существует множество сторонних средств, которые можно скачать для управления и просмотра базы данных SQLite, например, [браузера базы для SQLite](https://sqlitebrowser.org/).

---