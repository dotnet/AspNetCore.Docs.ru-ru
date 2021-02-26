---
title: Часть 7. Добавление нового поля
author: rick-anderson
description: Часть 7 серии руководств по Razor Pages.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101150"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a>Часть 7. Добавление нового поля на страницу Razor в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-5.0"

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).

В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:

* Добавление нового поля в модель.
* Перенос изменений в схеме нового поля в базу данных.

Если вы используете EF Code First для автоматического создания базы данных, Code First:

* Добавляет в базу данных таблицу [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table), которая позволяет отслеживать синхронизацию схемы базы данных с классами модели, на основе которой она была создана.
* Если классы модели не синхронизированы с базой данных, EF выдает исключение.

Автоматическая проверка синхронизации схемы и модели упрощает поиск несогласованных проблем в коде базы данных.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Добавление свойства Rating в модель Movie

1. Откройте файл *Models/Movie.cs* и добавьте свойство `Rating`:

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. Построение приложения.

1. Измените файл *Pages/Movies/Index.cshtml* и добавьте поле `Rating`:

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. Обновите следующие страницы:
   1. Добавьте поле `Rating` на страницы "Delete" (Удаление) и "Details" (Сведения).
   1. Обновите файл [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml), добавив в него поле `Rating`.
   1. Добавьте поле `Rating` на страницу "Edit" (Редактирование).

Для работы приложения необходимо обновить базу данных, включив в нее новое поле. При запуске приложения без обновления базы данных возникает `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Исключение `SqlException` связано с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в базе данных. В таблице базы данных нет столбца `Rating`.

Устранить эту ошибку можно несколькими способами:

1. Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели. Этот подход удобен на ранних стадиях цикла разработки. В этом случае развитие модели и схемы базы данных осуществляется одновременно. Недостатком такого подхода является потеря существующих данных в базе. В рабочей базе данных применять этот подход не следует! При разработке приложения часто выполняется удаление базы данных при изменении схемы, для чего используется инициализатор для автоматического заполнения базы тестовыми данными.

2. Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели. Преимущество подхода в том, что он сохраняет данные. Внесите это изменение вручную либо путем создания скрипта изменения для базы данных.

3. Можно обновить схему базы данных с помощью Code First Migrations.

В этом руководстве используется Code First Migrations.

Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца. Ниже приведен пример изменения, которое необходимо реализовать для каждого блока `new Movie`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

См. [готовый файл SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).

Постройте решение.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Добавление миграции для поля Rating

1. В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.
2. В PMC введите следующие команды:

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

Команда `Add-Migration` задает следующие инструкции для платформы:

* Сравните модель `Movie` со схемой базы данных `Movie`.
* Создайте код для переноса схемы базы данных в новую модель.

В качестве имени файла переноса используется произвольное имя "Rating". Рекомендуется присваивать этому файлу понятное имя.

Команда `Update-Database` указывает платформе, что к базе данных нужно применить изменения схемы, а также сохранить существующие данные.

<a name="ssox"></a>

Если удалить все записи из базы данных, при инициализации она будет заполнена начальными значениями и в нее будет включено поле `Rating`. Это можно сделать с помощью ссылок удаления в браузере или из [обозревателя объектов SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Другой вариант — удалить базу данных и использовать миграции для повторного создания базы данных. Удаление базы данных в SSOX:

1. Выберите базу данных в SSOX.
1. Щелкните базу данных правой кнопкой мыши и выберите **Удалить**.
1. Выберите **Закрыть существующие соединения**.
1. Нажмите кнопку **ОК**.
1. Обновите базу данных в [PMC](xref:tutorials/razor-pages/new-field#pmc).

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Удаление и повторное создание базы данных

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
> * [Инструкция по ALTER TABLE SQLite](https://sqlite.org/lang_altertable.html)

1. Удалите папку миграции.  

1. Используйте следующие команды, чтобы заново создать базу данных.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`. Если база данных не заполнена начальными значениями, задайте точку останова в методе `SeedData.Initialize`.

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Добавление поиска](xref:tutorials/razor-pages/search)
> [Следующая статья. Добавление проверки](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).

В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:

* Добавление нового поля в модель.
* Перенос изменений в схеме нового поля в базу данных.

Если вы используете EF Code First для автоматического создания базы данных, Code First:

* Добавляет в базу данных таблицу [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table), которая позволяет отслеживать синхронизацию схемы базы данных с классами модели, на основе которой она была создана.
* Если классы модели не синхронизированы с базой данных, EF выдает исключение.

Автоматическая проверка синхронизации схемы и модели упрощает поиск несогласованных проблем в коде базы данных.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Добавление свойства Rating в модель Movie

1. Откройте файл *Models/Movie.cs* и добавьте свойство `Rating`:

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. Построение приложения.

1. Измените файл *Pages/Movies/Index.cshtml* и добавьте поле `Rating`:

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. Обновите следующие страницы:
   1. Добавьте поле `Rating` на страницы "Delete" (Удаление) и "Details" (Сведения).
   1. Обновите файл [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml), добавив в него поле `Rating`.
   1. Добавьте поле `Rating` на страницу "Edit" (Редактирование).

Для работы приложения необходимо обновить базу данных, включив в нее новое поле. При запуске приложения без обновления базы данных возникает `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Исключение `SqlException` связано с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в базе данных. В таблице базы данных нет столбца `Rating`.

Устранить эту ошибку можно несколькими способами:

1. Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели. Этот подход удобен на ранних стадиях цикла разработки. В этом случае развитие модели и схемы базы данных осуществляется одновременно. Недостатком такого подхода является потеря существующих данных в базе. В рабочей базе данных применять этот подход не следует! При разработке приложения часто выполняется удаление базы данных при изменении схемы, для чего используется инициализатор для автоматического заполнения базы тестовыми данными.

2. Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели. Преимущество подхода в том, что он сохраняет данные. Внесите это изменение вручную либо путем создания скрипта изменения для базы данных.

3. Можно обновить схему базы данных с помощью Code First Migrations.

В этом руководстве используется Code First Migrations.

Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца. Ниже приведен пример изменения, которое необходимо реализовать для каждого блока `new Movie`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

См. [готовый файл SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).

Постройте решение.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Добавление миграции для поля Rating

1. В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.
2. В PMC введите следующие команды:

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

Команда `Add-Migration` задает следующие инструкции для платформы:

* Сравните модель `Movie` со схемой базы данных `Movie`.
* Создайте код для переноса схемы базы данных в новую модель.

В качестве имени файла переноса используется произвольное имя "Rating". Рекомендуется присваивать этому файлу понятное имя.

Команда `Update-Database` указывает платформе, что к базе данных нужно применить изменения схемы, а также сохранить существующие данные.

<a name="ssox"></a>

Если удалить все записи из базы данных, при инициализации она будет заполнена начальными значениями и в нее будет включено поле `Rating`. Это можно сделать с помощью ссылок удаления в браузере или из [обозревателя объектов SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Другой вариант — удалить базу данных и использовать миграции для повторного создания базы данных. Удаление базы данных в SSOX:

* Выберите базу данных в SSOX.
* Щелкните базу данных правой кнопкой мыши и выберите **Удалить**.
* Выберите **Закрыть существующие соединения**.
* Нажмите кнопку **ОК**.
* Обновите базу данных в [PMC](xref:tutorials/razor-pages/new-field#pmc).

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Удаление и повторное создание базы данных

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
> * [Инструкция по ALTER TABLE SQLite](https://sqlite.org/lang_altertable.html)

1. Удалите папку миграции.  

1. Используйте следующие команды, чтобы заново создать базу данных.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`. Если база данных не заполнена начальными значениями, задайте точку останова в методе `SeedData.Initialize`.

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Добавление поиска](xref:tutorials/razor-pages/search)
> [Следующая статья. Добавление проверки](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).

В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:

* Добавление нового поля в модель.
* Перенос изменений в схеме нового поля в базу данных.

Если вы используете EF Code First для автоматического создания базы данных, Code First:

* Добавляет в базу данных таблицу [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table), которая позволяет отслеживать синхронизацию схемы базы данных с классами модели, на основе которой она была создана.
* Если классы модели не синхронизированы с базой данных, EF выдает исключение.

Автоматическая проверка синхронизации схемы и модели упрощает поиск несогласованных проблем в коде базы данных.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Добавление свойства Rating в модель Movie

Откройте файл *Models/Movie.cs* и добавьте свойство `Rating`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Построение приложения.

Измените файл *Pages/Movies/Index.cshtml* и добавьте поле `Rating`:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Обновите следующие страницы:

* Добавьте поле `Rating` на страницы "Delete" (Удаление) и "Details" (Сведения).
* Обновите файл [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml), добавив в него поле `Rating`.
* Добавьте поле `Rating` на страницу "Edit" (Редактирование).

Для работы приложения необходимо обновить базу данных, включив в нее новое поле. Если приложение выполняется, возникнет исключение `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Эта ошибка связана с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в базе данных. В таблице базы данных нет столбца `Rating`.

Устранить эту ошибку можно несколькими способами:

1. Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели. Этот подход удобен на ранних стадиях цикла разработки. В этом случае развитие модели и схемы базы данных осуществляется одновременно. Недостатком такого подхода является потеря существующих данных в базе. В рабочей базе данных применять этот подход не следует! При разработке приложения часто выполняется удаление базы данных при изменении схемы, для чего используется инициализатор для автоматического заполнения базы тестовыми данными.

2. Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели. Преимущество подхода в том, что он сохраняет данные. Внесите это изменение вручную либо путем создания скрипта изменения для базы данных.

3. Можно обновить схему базы данных с помощью Code First Migrations.

В этом руководстве используется Code First Migrations.

Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца. Ниже приведен пример изменения, которое необходимо реализовать для каждого блока `new Movie`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

См. [готовый файл SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Постройте решение.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Добавление миграции для поля Rating

В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.
В PMC введите следующие команды:

```powershell
Add-Migration Rating
Update-Database
```

Команда `Add-Migration` задает следующие инструкции для платформы:

* Сравните модель `Movie` со схемой базы данных `Movie`.
* Создайте код для переноса схемы базы данных в новую модель.

В качестве имени файла переноса используется произвольное имя "Rating". Рекомендуется присваивать этому файлу понятное имя.

Команда `Update-Database` указывает платформе, что нужно применить изменения схемы к базе данных.

<a name="ssox"></a>

Если удалить все записи из базы данных, при инициализации она будет заполнена начальными значениями и в нее будет включено поле `Rating`. Это можно сделать с помощью ссылок удаления в браузере или из [обозревателя объектов SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Другой вариант — удалить базу данных и использовать миграции для повторного создания базы данных. Удаление базы данных в SSOX:

* Выберите базу данных в SSOX.
* Щелкните базу данных правой кнопкой мыши и выберите **Удалить**.
* Выберите **Закрыть существующие соединения**.
* Нажмите кнопку **ОК**.
* Обновите базу данных в [PMC](xref:tutorials/razor-pages/new-field#pmc).

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Удаление и повторное создание базы данных

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
> * [Инструкция по ALTER TABLE SQLite](https://sqlite.org/lang_altertable.html)

Удалите базу данных и используйте миграции для повторного создания базы данных. Чтобы удалить базу данных, удалите файл базы данных (*MvcMovie.db*). Затем выполните команду `ef database update`:

```dotnetcli
dotnet ef database update
```

---

Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`. Если база данных не заполнена начальными значениями, задайте точку останова в методе `SeedData.Initialize`.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Предыдущая статья. Добавление поиска](xref:tutorials/razor-pages/search)
> [Следующая статья. Добавление проверки](xref:tutorials/razor-pages/validation)

::: moniker-end