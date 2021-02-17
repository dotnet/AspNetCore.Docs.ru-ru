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
### <a name="view-the-identity-database"></a><span data-ttu-id="b90f2-101">Просмотр Identity базы данных</span><span class="sxs-lookup"><span data-stu-id="b90f2-101">View the Identity database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b90f2-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b90f2-102">Visual Studio</span></span>](#tab/visual-studio) 

* <span data-ttu-id="b90f2-103">В меню **вид** выберите **Обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b90f2-103">From the **View** menu, select **SQL Server Object Explorer** (SSOX).</span></span>
* <span data-ttu-id="b90f2-104">Перейдите к **(LocalDB) MSSQLLocalDB (SQL Server 13)**.</span><span class="sxs-lookup"><span data-stu-id="b90f2-104">Navigate to **(localdb)MSSQLLocalDB(SQL Server 13)**.</span></span> <span data-ttu-id="b90f2-105">Щелкните dbo правой кнопкой мыши **. AspNetUsers**  >  **представления данных**:</span><span class="sxs-lookup"><span data-stu-id="b90f2-105">Right-click on **dbo.AspNetUsers** > **View Data**:</span></span>

![Контекстное меню в таблице AspNetUsers в обозреватель объектов SQL Server](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[<span data-ttu-id="b90f2-107">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="b90f2-107">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b90f2-108">Существует множество сторонних средств, которые можно скачать для управления и просмотра базы данных SQLite, например, [браузера базы для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="b90f2-108">There are many third party tools you can download to manage and view a SQLite database, for example [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

---