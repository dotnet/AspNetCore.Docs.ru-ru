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
ms.openlocfilehash: 44cef0c1491cc609dd9b821910014ec88ecaad81
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552491"
---
::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="34406-101">Создайте папку *Data*.</span><span class="sxs-lookup"><span data-stu-id="34406-101">Create a *Data* folder.</span></span>

<span data-ttu-id="34406-102">Добавьте следующий класс `MvcMovieContext` в папку *Data*:</span><span class="sxs-lookup"><span data-stu-id="34406-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="34406-103">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="34406-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="34406-104">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="34406-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="34406-105">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="34406-105">Add a database connection string</span></span>

<span data-ttu-id="34406-106">Добавьте строку подключения в файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="34406-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="34406-107">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="34406-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="34406-108">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="34406-108">Register the database context</span></span>

<span data-ttu-id="34406-109">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="34406-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="34406-110">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="34406-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="34406-111">Выполните сборку проекта, чтобы проверить его на ошибки компиляции.</span><span class="sxs-lookup"><span data-stu-id="34406-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34406-112">Добавьте следующий класс `MvcMovieContext` в папку *Models*:</span><span class="sxs-lookup"><span data-stu-id="34406-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="34406-113">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="34406-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="34406-114">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="34406-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="34406-115">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="34406-115">Add a database connection string</span></span>

<span data-ttu-id="34406-116">Добавьте строку подключения в файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="34406-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="34406-117">Добавьте необходимые пакеты NuGet</span><span class="sxs-lookup"><span data-stu-id="34406-117">Add required NuGet packages</span></span>

<span data-ttu-id="34406-118">Выполните следующую команду .NET Core CLI, чтобы добавить в проект SQLite и CodeGeneration.Design:</span><span class="sxs-lookup"><span data-stu-id="34406-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="34406-119">Пакет `Microsoft.VisualStudio.Web.CodeGeneration.Design` необходим для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="34406-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="34406-120">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="34406-120">Register the database context</span></span>

<span data-ttu-id="34406-121">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="34406-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="34406-122">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="34406-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="34406-123">Соберите проект как проверку на ошибки.</span><span class="sxs-lookup"><span data-stu-id="34406-123">Build the project as a check for errors.</span></span>
::: moniker-end