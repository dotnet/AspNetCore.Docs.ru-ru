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
ms.openlocfilehash: 6808c71b1ca43755eea4958ff9409f40e8685694
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552529"
---
<span data-ttu-id="59564-101">Это руководство описывает MVC-модель ASP.NET Core и Entity Framework Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="59564-101">This tutorial teaches ASP.NET Core MVC and Entity Framework Core with controllers and views.</span></span> <span data-ttu-id="59564-102">[Razor Pages](xref:razor-pages/index) — это альтернативная модель программирования.</span><span class="sxs-lookup"><span data-stu-id="59564-102">[Razor Pages](xref:razor-pages/index) is an alternative programming model.</span></span> <span data-ttu-id="59564-103">Для новой разработки мы рекомендуем использовать Razor Pages, а не MVC с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="59564-103">For new development, we recommend Razor Pages over MVC with controllers and views.</span></span> <span data-ttu-id="59564-104">См. версию этого руководства для [Razor Pages](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="59564-104">See the [Razor Pages](xref:data/ef-rp/intro) version of this tutorial.</span></span> <span data-ttu-id="59564-105">В каждом руководстве содержатся уникальные материалы:</span><span class="sxs-lookup"><span data-stu-id="59564-105">Each tutorial covers some material the other doesn't:</span></span>

<span data-ttu-id="59564-106">В руководстве по MVC содержатся материалы, которых нет в руководстве по Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="59564-106">Some things this MVC tutorial has that the Razor Pages tutorial doesn't:</span></span>

* <span data-ttu-id="59564-107">Реализация наследования в модели данных</span><span class="sxs-lookup"><span data-stu-id="59564-107">Implement inheritance in the data model</span></span>
* <span data-ttu-id="59564-108">Выполнение прямых SQL-запросов</span><span class="sxs-lookup"><span data-stu-id="59564-108">Perform raw SQL queries</span></span>
* <span data-ttu-id="59564-109">Использование динамических запросов LINQ для упрощения кода</span><span class="sxs-lookup"><span data-stu-id="59564-109">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="59564-110">В руководстве по Razor Pages содержатся материалы, которых нет в этом руководстве:</span><span class="sxs-lookup"><span data-stu-id="59564-110">Some things the Razor Pages tutorial has that this one doesn't:</span></span>

* <span data-ttu-id="59564-111">Использование метода Select для загрузки связанных данных</span><span class="sxs-lookup"><span data-stu-id="59564-111">Use Select method to load related data</span></span>
* <span data-ttu-id="59564-112">Рекомендации по использованию EF.</span><span class="sxs-lookup"><span data-stu-id="59564-112">Best practices for EF.</span></span>