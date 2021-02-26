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
ms.openlocfilehash: e8c5bd00178aefb91ab0e7066c5490ceba315530
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551577"
---
> [!NOTE]
> <span data-ttu-id="5d063-101">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="5d063-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="5d063-102">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="5d063-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="5d063-103">Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="5d063-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="5d063-104">Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="5d063-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="5d063-105">Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="5d063-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="5d063-106">Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite.</span><span class="sxs-lookup"><span data-stu-id="5d063-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="5d063-107">Вместо этого при изменении схемы нужно удалить и снова создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="5d063-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="5d063-108">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="5d063-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="5d063-109">Перестроение таблицы включает в себя:</span><span class="sxs-lookup"><span data-stu-id="5d063-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="5d063-110">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="5d063-110">Creating a new table.</span></span>
>* <span data-ttu-id="5d063-111">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="5d063-111">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="5d063-112">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="5d063-112">Dropping the old table.</span></span>
>* <span data-ttu-id="5d063-113">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="5d063-113">Renaming the new table.</span></span>
>
><span data-ttu-id="5d063-114">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="5d063-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="5d063-115">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="5d063-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="5d063-116">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="5d063-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="5d063-117">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="5d063-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)
  * [<span data-ttu-id="5d063-118">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="5d063-118">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)