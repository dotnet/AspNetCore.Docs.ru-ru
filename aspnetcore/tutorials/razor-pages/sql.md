---
title: Часть 4. Работа с базой данных
author: rick-anderson
description: Часть 4 серии руководств по Razor Pages.
ms.author: riande
ms.date: 01/05/2021
ms.custom: contperf-fy21q2
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3133ff7a192f5e3bdec4f6f1e6e6d55f966d7e64
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589768"
---
# <a name="part-4-of-tutorial-series-on-razor-pages"></a>Часть 4 серии руководств по Razor Pages

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Джо Одетт](https://twitter.com/joeaudette) (Joe Audette)

::: moniker range=">= aspnetcore-5.0"

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).

Объект `RazorPagesMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных. Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает ключ `ConnectionString`. Для разработки на локальном уровне конфигурация получает строку подключения из файла *appsettings.json* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Созданная строка подключения выглядит аналогично следующему коду JSON.

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

Если приложение развертывается на тестовом или рабочем сервере, можно задать строку подключения к тестовому или рабочему серверу базы данных с помощью переменной среды. Дополнительные сведения см. в разделе [Конфигурация](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ. LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны. По умолчанию база данных LocalDB создает файлы `*.mdf` в каталоге `C:\Users\<user>\`.

<a name="ssox"></a>
1. В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).

   ![Меню "Вид"](sql/_static/5/ssox.png)

1. Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**:

   ![Контекстные меню, открытые на таблице Movie](sql/_static/5/design.png)

   ![Таблицы Movie, открытые в конструкторе](sql/_static/dv.png)

   Обратите внимание на значок с изображением ключа рядом с `ID`. По умолчанию EF создает свойство с именем `ID` для первичного ключа.

1. Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотреть данные**:

   ![Открытая таблица Movie с отображением данных](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

На веб-сайте [SQLite](https://www.sqlite.org/) указывается следующее:

> SQLite — это автономная внедряемая полнофункциональная общедоступная система управления базами данных SQL с высокой степенью надежности. На данный момент SQLite является самой популярной СУБД в мире.

Для просмотра базы данных SQLite, а также управления ей можно использовать множество самых разных сторонних инструментов. На следующем рисунке показан [DB Browser для SQLite](https://sqlitebrowser.org/). Если вы предпочитаете использовать другое средство для работы с SQLite, напишите нам в комментариях, чем именно оно нравится вам.

![DB Browser для SQLite с базой данных Movie](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно. Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных. Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены. Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается. Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет. Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite. Вместо этого при изменении схемы база данных удаляется и создается повторно.
>
>Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений. Перестроение таблицы включает в себя:
>
>* Создание новой таблицы.
>* Копирование данных из старой таблицы в новую.
>* Удаление старой таблицы.
>* Переименование новой таблицы.
>
>Дополнительные сведения см. в следующих ресурсах:
> * [Ограничения поставщика базы данных SQLite EF Core](/ef/core/providers/sqlite/limitations)
> * [Настройка кода миграции](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Присвоение начальных значений данных](/ef/core/modeling/data-seeding)
> * [Инструкция по ALTER TABLE SQLite](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Заполнение базы данных

Создайте класс `SeedData` в папке *Models* со следующим кодом:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Добавление инициализатора заполнения

Замените содержимое файла *Program.cs* следующим кодом:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

В приведенном выше коде метод `Main` изменен, чтобы сделать следующее:

* Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.
* Вызовите метод `seedData.Initialize`, передав ему экземпляр контекста базы данных.
* Высвобождение контекста после завершения работы метода заполнения. [Оператор using](/dotnet/csharp/language-reference/keywords/using-statement) гарантирует удаление контекста.

Если `Update-Database` не выполнялось, возникает следующее исключение:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Тестирование приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Удалите все записи в базе данных. Используйте ссылки удаления в браузере или в [SSOX](xref:tutorials/razor-pages/new-field#ssox).

1. Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения. Для этого следует остановить и перезапустить IIS Express. Закройте и перезапустите службы IIS с помощью любого из перечисленных ниже подходов.

   1. Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выйти** или **Остановить сайт**.

      ![Значок IIS Express в области уведомлений](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Контекстное меню](sql/_static/stopIIS.png)

   1. Если приложение работает в режиме без отладки, нажмите клавишу <kbd>F5</kbd> для запуска в режиме отладки.
   1. Если приложение находится в режиме отладки, остановите отладчик и нажмите клавишу <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Удалите все записи в базе данных для запуска метода Seed. Остановите и запустите приложение, чтобы начать заполнение базы данных.

---

В приложении отображаются заполненные данные.

![Приложение Movie с данными по фильмам, открытое в браузере](sql/_static/5/m55.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)
> [Далее: Обновление страниц](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).

Объект `RazorPagesMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных. Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает ключ `ConnectionString`. Для разработки на локальном уровне конфигурация получает строку подключения из файла *appsettings.json* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Созданная строка подключения будет выглядеть следующим образом:

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

Если приложение развертывается на тестовом или рабочем сервере, можно задать строку подключения к тестовому или рабочему серверу базы данных с помощью переменной среды. Дополнительные сведения см. в статье [Конфигурация](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ. LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны. По умолчанию база данных LocalDB создает файлы `*.mdf` в каталоге `C:\Users\<user>\`.

<a name="ssox"></a>
* В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).

  ![Меню "Вид"](sql/_static/ssox.png)

* Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**:

  ![Контекстные меню, открытые на таблице Movie](sql/_static/design.png)

  ![Таблицы Movie, открытые в конструкторе](sql/_static/dv.png)

Обратите внимание на значок с изображением ключа рядом с `ID`. По умолчанию EF создает свойство с именем `ID` для первичного ключа.

* Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотреть данные**:

  ![Открытая таблица Movie с отображением данных](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

На веб-сайте [SQLite](https://www.sqlite.org/) указывается следующее:

> SQLite — это автономная внедряемая полнофункциональная общедоступная система управления базами данных SQL с высокой степенью надежности. На данный момент SQLite является самой популярной СУБД в мире.

Для просмотра базы данных SQLite, а также управления ей можно использовать множество самых разных сторонних инструментов. На следующем рисунке показан [DB Browser для SQLite](https://sqlitebrowser.org/). Если вы предпочитаете использовать другое средство для работы с SQLite, напишите нам в комментариях, чем именно оно нравится вам.

![DB Browser для SQLite с базой данных Movie](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно. Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных. Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены. Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается. Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет. Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite. Вместо этого при изменении схемы база данных удаляется и создается повторно.
>
>Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений. Перестроение таблицы включает в себя:
>
>* Создание новой таблицы.
>* Копирование данных из старой таблицы в новую.
>* Удаление старой таблицы.
>* Переименование новой таблицы.
>
>Дополнительные сведения см. в следующих ресурсах:
> * [Ограничения поставщика базы данных SQLite EF Core](/ef/core/providers/sqlite/limitations)
> * [Настройка кода миграции](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Присвоение начальных значений данных](/ef/core/modeling/data-seeding)
> * [Инструкция по ALTER TABLE SQLite](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Заполнение базы данных

Создайте класс `SeedData` в папке *Models* со следующим кодом:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Добавление инициализатора заполнения

Замените содержимое файла *Program.cs* следующим кодом:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

В приведенном выше коде метод `Main` изменен, чтобы сделать следующее:

* Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.
* Вызовите метод `seedData.Initialize`, передав ему экземпляр контекста базы данных.
* Высвобождение контекста после завершения работы метода заполнения. [Оператор using](/dotnet/csharp/language-reference/keywords/using-statement) гарантирует удаление контекста.

Если `Update-Database` не выполнялось, возникает следующее исключение:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Тестирование приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Удалите все записи в базе данных. Используйте ссылки удаления в браузере или в [SSOX](xref:tutorials/razor-pages/new-field#ssox).
* Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения. Для этого следует остановить и перезапустить IIS Express. Закройте и перезапустите службы IIS с помощью любого из перечисленных ниже подходов.

  * Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выйти** или **Остановить сайт**.

    ![Значок IIS Express в области уведомлений](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Контекстное меню](sql/_static/stopIIS.png)

    * Если приложение работает в режиме без отладки, нажмите клавишу <kbd>F5</kbd> для запуска в режиме отладки.
    * Если приложение находится в режиме отладки, остановите отладчик и нажмите клавишу <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Удалите все записи в базе данных для запуска метода Seed. Остановите и запустите приложение, чтобы начать заполнение базы данных.

---

В приложении отображаются заполненные данные.

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55https.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)
> [Далее: Обновление страниц](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).

Объект `RazorPagesMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных. Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Дополнительные сведения о методах, которые используются в `ConfigureServices`, см.:

* [Общий регламент по защите данных (GDPR), принятый в ЕС, в ASP.NET Core](xref:security/gdpr) для `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает ключ `ConnectionString`. Для разработки на локальном уровне конфигурация получает строку подключения из файла *appsettings.json* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Созданная строка подключения будет выглядеть следующим образом:

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

Если приложение развертывается на тестовом или рабочем сервере, можно задать строку подключения к тестовому или рабочему серверу базы данных с помощью переменной среды. Дополнительные сведения см. в статье [Конфигурация](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ. LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны. По умолчанию база данных LocalDB создает файлы `*.mdf` в каталоге `C:/Users/<user/>`.

<a name="ssox"></a>
* В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).

  ![Меню "Вид"](sql/_static/ssox.png)

* Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**:

  ![Контекстное меню, открытое на таблице Movie](sql/_static/design.png)

  ![Таблица Movie, открытая в конструкторе](sql/_static/dv.png)

Обратите внимание на значок с изображением ключа рядом с `ID`. По умолчанию EF создает свойство с именем `ID` для первичного ключа.

* Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотреть данные**:

  ![Открытая таблица Movie с отображением данных](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Заполнение базы данных

Создайте класс `SeedData` в папке *Models* со следующим кодом:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Добавление инициализатора заполнения

Замените содержимое файла *Program.cs* следующим кодом:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

В приведенном выше коде метод `Main` изменен, чтобы сделать следующее:

* Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.
* Вызовите метод `seedData.Initialize`, передав ему экземпляр контекста базы данных.
* Высвобождение контекста после завершения работы метода заполнения. [Оператор using](/dotnet/csharp/language-reference/keywords/using-statement) гарантирует удаление контекста.

Рабочее приложение не вызывает `Database.Migrate`. Он добавляется в предыдущем коде, чтобы предотвратить следующее исключение, если `Update-Database` не был запущен.

SqlException: Не удается открыть базу данных "RazorPagesMovieContext-21", запрашиваемую именем входа. Сбой при входе.
Сбой при входе в систему пользователя user name.

### <a name="test-the-app"></a>Тестирование приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Удалите все записи в базе данных. Это можно сделать с помощью ссылок удаления в браузере или из [SSOX](xref:tutorials/razor-pages/new-field#ssox).
* Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения. Для этого следует остановить и перезапустить IIS Express. Воспользуйтесь одним из перечисленных ниже подходов.

  * Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выйти** или **Остановить сайт**.

    ![Значок IIS Express в области уведомлений](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Контекстное меню](sql/_static/stopIIS.png)

    * Если приложение работает в режиме без отладки, нажмите клавишу <kbd>F5</kbd> для запуска в режиме отладки.
    * Если приложение находится в режиме отладки, остановите отладчик и нажмите клавишу <kbd>F5</kbd>.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Удалите все записи в базе данных для запуска метода Seed. Остановите и запустите приложение, чтобы начать заполнение базы данных.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Удалите все записи в базе данных для запуска метода Seed. Остановите и запустите приложение, чтобы начать заполнение базы данных.

---

В приложении отображаются заполненные данные.

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55https.png)

В следующем учебнике будет улучшено представление данных.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Предыдущая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)
> [Далее: Обновление страниц](xref:tutorials/razor-pages/da1)

::: moniker-end