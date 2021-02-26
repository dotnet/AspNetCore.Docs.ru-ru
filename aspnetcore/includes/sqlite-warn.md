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
ms.openlocfilehash: cf20722e8c8669fb17af8db032d4064ca2be2f4c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551999"
---
> [!NOTE]
> 
> <span data-ttu-id="145e6-101">**Ограничения SQLite**</span><span class="sxs-lookup"><span data-stu-id="145e6-101">**SQLite limitations**</span></span>
>
> <span data-ttu-id="145e6-102">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="145e6-102">This tutorial uses the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="145e6-103">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="145e6-103">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="145e6-104">Однако миграции могут вносить только такие изменения, которые поддерживает ядро СУБД, а возможности изменения схемы SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="145e6-104">However, migrations only does the kinds of changes that the database engine supports, and SQLite's schema change capabilities are limited.</span></span> <span data-ttu-id="145e6-105">Например, добавление столбца поддерживается, но удаление столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="145e6-105">For example, adding a column is supported, but removing a column is not supported.</span></span> <span data-ttu-id="145e6-106">Если миграция создается для удаления столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="145e6-106">If a migration is created to remove a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> 
>
> <span data-ttu-id="145e6-107">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="145e6-107">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="145e6-108">Код для миграции будет находиться в методах `Up` и `Down` и выполнять следующие действия:</span><span class="sxs-lookup"><span data-stu-id="145e6-108">The code would go in the `Up` and `Down` methods for a migration and would involve:</span></span>
>
> * <span data-ttu-id="145e6-109">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="145e6-109">Creating a new table.</span></span>
> * <span data-ttu-id="145e6-110">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="145e6-110">Copying data from the old table to the new table.</span></span>
> * <span data-ttu-id="145e6-111">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="145e6-111">Dropping the old table.</span></span>
> * <span data-ttu-id="145e6-112">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="145e6-112">Renaming the new table.</span></span>
>
> <span data-ttu-id="145e6-113">Написание кода такого рода для конкретной базы данных выходит за рамки данного учебника.</span><span class="sxs-lookup"><span data-stu-id="145e6-113">Writing database-specific code of this type is outside the scope of this tutorial.</span></span> <span data-ttu-id="145e6-114">Вместо этого в данном учебнике база данных удаляется и создается повторно, когда попытка применить миграцию завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="145e6-114">Instead, this tutorial drops and re-creates the database whenever an attempt to apply a migration would fail.</span></span> <span data-ttu-id="145e6-115">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="145e6-115">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="145e6-116">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="145e6-116">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="145e6-117">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="145e6-117">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="145e6-118">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="145e6-118">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="145e6-119">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="145e6-119">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)