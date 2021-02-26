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
> В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно. Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных. Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены. Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается. Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет. Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite. Вместо этого при изменении схемы нужно удалить и снова создать базу данных.
>
>Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений. Перестроение таблицы включает в себя:
>
>* Создание новой таблицы.
>* Копирование данных из старой таблицы в новую.
>* Удаление старой таблицы.
>* Переименование новой таблицы.
>
>Дополнительные сведения см. в следующих ресурсах:
>
> * [Ограничения поставщика базы данных SQLite EF Core](/ef/core/providers/sqlite/limitations)
> * [Настройка кода миграции](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Присвоение начальных значений данных](/ef/core/modeling/data-seeding)
  * [Инструкция по ALTER TABLE SQLite](https://sqlite.org/lang_altertable.html)