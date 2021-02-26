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
ms.openlocfilehash: 3c21d2a5604c2dfc96624fb5d161537797925fef
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551460"
---
<span data-ttu-id="74c26-101">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="74c26-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="74c26-102">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="74c26-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="74c26-103">Поле `Id`, которое является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="74c26-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="74c26-104">`[DataType(DataType.Date)]`:  Атрибут [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="74c26-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="74c26-105">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="74c26-105">With this attribute:</span></span>

  * <span data-ttu-id="74c26-106">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="74c26-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="74c26-107">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="74c26-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="74c26-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="74c26-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>