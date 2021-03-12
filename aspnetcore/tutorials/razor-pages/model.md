---
title: Часть 2. Добавление модели
author: rick-anderson
description: Часть 2 серии руководств по Razor Pages. В этом разделе содержатся сведения о добавлении классов моделей.
ms.author: riande
ms.date: 11/11/2020
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 3677cd6fe5c2ff901a17c9dccdc749d8eb2709f2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588670"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Часть 2. Добавление модели в приложение Razor Pages в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

В этом разделе добавляются классы для управления фильмами в базе данных. Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных. EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным. Вы напишете классы моделей, а затем EF Core создаст базу данных.

Эти классы моделей называются классами POCO (от "**P** lain-**O** ld **C** LR **O** bjects" — "старые добрые объекты CLR"), так как они не зависят от EF Core. Эти классы определяют свойства данных, которые хранятся в базе данных.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Добавление модели данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. В **Обозревателе решений** щелкните правой кнопкой мыши проект *RazorPagesMovie* и выберите пункт **Add**(Добавить) > **New Folder**(Новая папка). Присвойте папке имя *Models*.
1. Щелкните правой кнопкой мыши папку *Models*. Выберите **Добавить** > **Класс**. Присвойте классу имя *Movie*.
1. Добавьте в класс `Movie` следующие свойства:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`). С этим атрибутом:

  * Пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Добавьте папку с именем *Models*.
1. Добавьте класс в папку *Models* с именем *Movie.cs*.

Добавьте в класс `Movie` следующие свойства:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Добавление пакетов NuGet и средств EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a>Добавление класса контекста для базы данных

1. В проекте *RazorPagesMovie* создайте папку с именем *Data*.
1. В папке *Data* добавьте файл с именем *RazorPagesMovieContext.cs* с помощью следующего кода:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   Представленный выше код создает свойство `DbSet` для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице. Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Добавление строки подключения базы данных

Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Регистрация контекста базы данных

1. Добавьте следующие инструкции `using` в начало файла *Startup.cs*.

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. В **окне инструментов решения** щелкните проект *RazorPagesMovie*, а затем выберите **Add**(Добавить)> **Создать папку...** . Присвойте папке имя *Models*.
1. Щелкните папку *Models* и выберите пункт **Add** (Добавить)>**Создать файл...** .
1. В диалоговом окне **Новый файл** выполните следующие действия.
   1. Выберите на левой панели пункт **Общие**.
   1. В центральной области выберите **Пустой класс**.
   1. Назовите класс **Movie** и выберите **Создать**.

1. Добавьте в класс `Movie` следующие свойства:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

---

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.

Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.

## <a name="scaffold-the-movie-model"></a>Создание модели фильма

В этом разделе создается модель фильма. То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Создайте папку *Pages/Movies*:
   1. Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).
   1. Назовите папку *Movies*.

1. Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).

   ![Изображение из предыдущих инструкций.](model/_static/5/sca.png)

1. В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.

   ![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

1. Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :
   1. В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .
   1. В строке **Класс контекста данных** щелкните знак плюса ( **+** ).
      1. В диалоговом окне **Добавление контекста данных** будет сгенерировано имя класса `RazorPagesMovie.Data.RazorPagesMovieContext`.
   1. Нажмите **Добавить**.

   ![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Откройте в командной оболочке папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.

* **Для Windows**: Выполните следующую команду:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Для macOS и Linux**: Выполните следующую команду:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> В следующей таблице представлены параметры генератора кода ASP.NET Core:

| Параметр               | Описание:|
| ----------------- | ------------ |
| `-m`  | Имя модели |
| `-dc`  | Класс `DbContext` для использования. |
| `-udl` | Использование макета по умолчанию |
| `-outDir` | Относительный путь к папке выходных данных для создания представлений |
| `--referenceScriptLibraries` | Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания |

Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Использование SQLite для среды разработки и SQL Server для рабочей среды

Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке. В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup`. `IWebHostEnvironment` внедрен, поэтому приложение может использовать SQLite в среде разработки и SQL Server в рабочей среде.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. Создайте папку *Pages/Movies*:
   1. Щелкните папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).
   1. Назовите папку *Movies*.

1. Щелкните папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolding...** (Создать шаблон...).

   ![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

1. В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.

   ![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

1. Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :
   1. В строке **Класс DbContext для использования** присвойте классу имя `RazorPagesMovie.Data.RazorPagesMovieContext`.
   1. Нажмите кнопку **Готово**.

   ![Изображение из предыдущих инструкций.](model/_static/5/arpMac.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Использование SQLite для среды разработки и SQL Server для рабочей среды

Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке. В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup`. `IWebHostEnvironment` внедрен, поэтому приложение может использовать SQLite в среде разработки и SQL Server в рабочей среде.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Создаваемые файлы

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.

* *Pages/Movies*: Create, Delete, Details, Edit и Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Обновленные возможности

* *Startup.cs*

В следующем разделе приводится описание созданных и обновленных файлов.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

В процессе формирования шаблонов создаются указанные ниже файлы.

* *Pages/Movies*: Create, Delete, Details, Edit и Index.

В следующем разделе приводится описание созданных файлов.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.

* *Pages/Movies*: Create, Delete, Details, Edit и Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Обновленные возможности

* *Startup.cs*

В следующем разделе приводится описание созданных и обновленных файлов.

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a>Создание начальной схемы базы данных с помощью функции миграции EF

Функция миграции в Entity Framework Core предоставляет следующие возможности:

* Создание начальной схемы базы данных.
* Поэтапное обновление схемы базы данных, чтобы она соответствовала модели данных приложения.  Существующие данные в базе данных сохраняются.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В этом разделе окно **Консоль диспетчера пакетов** (PMC) используется для выполнения следующих действий:

* Добавления первоначальной миграции.
* Обновления базы данных с помощью первоначальной миграции.

1. В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

   ![Меню PMC](model/_static/5/pmc.png)

1. В PMC введите следующие команды:

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* Выполните следующие команды интерфейса командной строки .NET:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип. Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию. С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".

Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.

Команда `migrations` формирует код для создания схемы исходной базы данных. Схема создается на основе модели, указанной в `DbContext`. Аргумент `InitialCreate` используется для присвоения имен миграциям. Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.

Команда `update` выполняет метод `Up` в миграциях, которые не были применены. В этом случае `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*, который создает базу данных.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Проверка контекста, зарегистрированного с помощью внедрения зависимостей

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.

Проверьте метод `Startup.ConfigureServices`. Средством формирования шаблонов была добавлена выделенная строка:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` координирует функции EF Core, такие как Create (создание), Read (чтение), Update (обновление) и Delete (удаление) для модели `Movie`. Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). Контекст данных указывает сущности, которые включаются в модель данных.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

Представленный выше код создает свойство [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных. Сущность соответствует строке в таблице.

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Проверьте метод `Up`.

---

<a name="test"></a>

## <a name="test-the-app"></a>Тестирование приложения

1. Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).

   Если вы получаете следующую ошибку:

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   Вы пропустили [шаг миграции](#pmc).

1. Протестируйте ссылку **Создать**.

   ![Страница "Создать"](model/_static/conan5.png)

   > [!NOTE]
   > В поле `Price` нельзя вводить десятичные запятые. Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения. Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

1. Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.

В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

В этом разделе добавляются классы для управления фильмами. Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных. EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным.

Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core. Эти классы определяют свойства данных, которые хранятся в базе данных.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Добавление модели данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**. Присвойте папке имя *Models*.

Щелкните правой кнопкой мыши папку *Models*. Выберите **Добавить** > **Класс**. Присвойте классу имя **Movie**.

Добавьте в класс `Movie` следующие свойства:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Добавьте папку с именем *Models*.
* Добавьте класс в папку *Models* с именем *Movie.cs*.

Добавьте в класс `Movie` следующие свойства:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Добавление пакетов NuGet и средств EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Добавление класса контекста для базы данных

* В проекте *RazorPagesMovie* создайте папку с именем *Data*.
* Добавьте следующий класс `RazorPagesMovieContext` в папку *Data*:

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Представленный выше код создает свойство `DbSet` для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице. Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Добавление строки подключения базы данных

Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Регистрация контекста базы данных

Добавьте следующие инструкции `using` в начало файла *Startup.cs*.

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* В **окне инструментов решения** щелкните проект **RazorPagesMovie**, а затем выберите **Add**(Добавить)> **Создать папку...** . Присвойте папке имя *Models*.
* Щелкните папку *Models* правой кнопкой мыши и выберите пункт **Добавить**>**Новый файл...**
* В диалоговом окне **Новый файл** выполните следующие действия.

  * Выберите на левой панели пункт **Общие**.
  * В центральной области выберите **Пустой класс**.
  * Назовите класс **Movie** и выберите **Создать**.

Добавьте в класс `Movie` следующие свойства:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

---

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.

Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.

## <a name="scaffold-the-movie-model"></a>Создание модели фильма

В этом разделе создается модель фильма. То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Создайте папку *Pages/Movies*:

* Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).
* Назовите папку *Movies*.

Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).

![Изображение из предыдущих инструкций.](model/_static/sca.png)

В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :

* В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .
* В строке **Класс контекста данных** щелкните значок плюса **+** и измените сгенерированное имя RazorPagesMovie.**Models**.RazorPagesMovieContext на RazorPagesMovie.**Data**.RazorPagesMovieContext. [Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется. Оно создает класс контекста базы данных с правильным пространством имен.
* Нажмите **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Откройте в командном окне папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.

* **Для Windows**: Выполните следующую команду:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Для macOS и Linux**: Выполните следующую команду:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> В следующей таблице представлены параметры генератора кода ASP.NET Core:

| Параметр               | Описание:|
| ----------------- | ------------ |
| `-m`  | Имя модели |
| `-dc`  | Класс `DbContext` для использования. |
| `-udl` | Использование макета по умолчанию |
| `-outDir` | Относительный путь к папке выходных данных для создания представлений |
| `--referenceScriptLibraries` | Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания |

Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Использование SQLite для среды разработки и SQL Server для рабочей среды

Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке. В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки. Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Создайте папку *Pages/Movies*:

* Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).
* Назовите папку *Movies*.

Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolding...** (Создать шаблон...).

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :

* В раскрывающемся списке **Класс модели** выберите или введите **Фильм (RazorPagesMovie.Models)** .
* В строке **Класс контекста данных** введите имя нового класса RazorPagesMovie.**Data**.RazorPagesMovieContext. [Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется. Оно создает класс контекста базы данных с правильным пространством имен.
* Нажмите **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

### <a name="add-ef-tools"></a>Добавление средств EF

Выполните следующую команду .NET Core CLI:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Приведенная выше команда добавляет средства Entity Framework Core для .NET Core CLI. Дополнительные сведения см. в статье [Справочник по средствам Entity Framework Core в .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Использование SQLite для среды разработки и SQL Server для рабочей среды

Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке. В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки. Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Создаваемые файлы

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.

* *Pages/Movies*: Create, Delete, Details, Edit и Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Обновленные возможности

* *Startup.cs*

В следующем разделе приводится описание созданных и обновленных файлов.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.

* *Pages/Movies*: Create, Delete, Details, Edit и Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Обновленные возможности

* *Startup.cs*

В следующем разделе приводится описание созданных и обновленных файлов.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

В процессе формирования шаблонов создаются указанные ниже файлы.

* *Pages/Movies*: Create, Delete, Details, Edit и Index.

В следующем разделе приводится описание созданных файлов.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Первоначальная миграция

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:

* Добавления первоначальной миграции.
* Обновления базы данных с помощью первоначальной миграции.

В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

В PMC введите следующие команды:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Выполните следующие команды интерфейса командной строки .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип. Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию. С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".

Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.

Команда миграции формирует код для создания схемы исходной базы данных. Схема создается на основе модели, указанной в `DbContext`. Аргумент `InitialCreate` используется для присвоения имен миграциям. Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.

Команда `update` выполняет метод `Up` в миграциях, которые не были применены. В этом случае `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*, который создает базу данных.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Проверка контекста, зарегистрированного с помощью внедрения зависимостей

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы, например контекст базы данных EF Core, регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы, например Razor Pages, обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.

Проверьте метод `Startup.ConfigureServices`. Средством формирования шаблонов была добавлена выделенная строка:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` координирует функции EF Core, такие как Create (создание), Read (чтение), Update (обновление) и Delete (удаление) для модели `Movie`. Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). Контекст данных указывает сущности, которые включаются в модель данных.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных. Сущность соответствует строке в таблице.

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Проверьте метод `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Тестирование приложения

* Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).

Если возникает ошибка.

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Вы пропустили [шаг миграции](#pmc).

* Протестируйте ссылку **Создать**.

  ![Страница "Создать"](model/_static/conan5.png)

  > [!NOTE]
  > В поле `Price` нельзя вводить десятичные запятые. Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения. Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.

В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

В этом разделе добавляются классы для управления фильмами в кроссплатформенной [базе данных SQLite](https://www.sqlite.org/index.html). Приложения, созданные на основе шаблона ASP.NET Core, используют базу данных SQLite. Эти классы этой модели приложений используются в [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) для работы с базой данных. EF Core —это платформа объектно-реляционного сопоставления (ORM), которая упрощает получение доступа к данным.

Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core. Эти классы определяют свойства данных, которые хранятся в базе данных.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Добавление модели данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**. Присвойте папке имя *Models*.

Щелкните правой кнопкой мыши папку *Models*. Выберите **Добавить** > **Класс**. Присвойте классу имя **Movie**.

Добавьте в класс `Movie` следующие свойства:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Добавьте папку с именем *Models*.
* Добавьте класс в папку *Models* с именем *Movie.cs*.

Добавьте в класс `Movie` следующие свойства:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Добавление пакетов NuGet и средств EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Добавление класса контекста для базы данных

В проекте RazorPagesMovie создайте папку с именем *Data*. 
Добавьте следующий класс `RazorPagesMovieContext` в папку *Data*:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Представленный выше код создает свойство `DbSet` для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице. Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Добавление строки подключения базы данных

Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Добавьте необходимые пакеты NuGet

Выполните следующую команду .NET Core CLI, чтобы добавить в проект SQLite и CodeGeneration.Design:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

Пакет `Microsoft.VisualStudio.Web.CodeGeneration.Design` необходим для формирования шаблонов.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Регистрация контекста базы данных

Добавьте следующие инструкции `using` в начало файла *Startup.cs*.

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Соберите проект как проверку на ошибки.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* В **окне инструментов решения** щелкните проект *RazorPagesMovie*, а затем выберите **Add**(Добавить) > **New Folder**(Новая папка). Присвойте папке имя *Models*.
* Щелкните папку *Models* и выберите пункт **Add** (Добавить) > **Новый файл**.
* В диалоговом окне **Новый файл** выполните следующие действия.

  * Выберите на левой панели пункт **Общие**.
  * В центральной области выберите **Пустой класс**.
  * Назовите класс **Movie** и выберите **Создать**.

Добавьте в класс `Movie` следующие свойства:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.

---

Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.

## <a name="scaffold-the-movie-model"></a>Создание модели фильма

В этом разделе создается модель фильма. То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Создайте папку *Pages/Movies*:

* Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).
* Назовите папку *Movies*.

Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).

![Изображение из предыдущих инструкций.](model/_static/sca.png)

В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .
* В строке **Класс контекста данных** нажмите на значок плюса **+** и примите сгенерированное имя **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Нажмите **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/arp.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Откройте в командном окне папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.

* **Для Windows**: Выполните следующую команду:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Для macOS и Linux**: Выполните следующую команду:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> В следующей таблице представлены параметры генератора кода ASP.NET Core:

| Параметр               | Описание:|
| ----------------- | ------------ |
| `-m`  | Имя модели |
| `-dc`  | Класс `DbContext` для использования. |
| `-udl` | Использование макета по умолчанию |
| `-outDir` | Относительный путь к папке выходных данных для создания представлений |
| `--referenceScriptLibraries` | Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания |

Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Создайте папку *Pages/Movies*:

* Щелкните папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).
* Назовите папку *Movies*.

Щелкните папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

В диалоговом окне **Add New Scaffolding** (Добавление нового шаблона) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :

* В раскрывающемся списке **Класс модели** выберите или введите **Фильм**.
* В строке **Data context class** (Класс контекста данных) введите или выберите **RazorPagesMovieContext**. Это приведет к созданию класса контекста базы данных с правильным пространством имен. В этом случае он будет **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Нажмите **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

---

В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.

### <a name="files-created"></a>Создаваемые файлы

* *Pages/Movies*: Create, Delete, Details, Edit и Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Обновляемые файлы

* *Startup.cs*

В следующем разделе приводится описание созданных и обновленных файлов.

<a name="pmc"></a>

## <a name="initial-migration"></a>Первоначальная миграция

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:

* Добавления первоначальной миграции.
* Обновления базы данных с помощью первоначальной миграции.

В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

В PMC введите следующие команды:

```powershell
Add-Migration Initial
Update-Database
```

Команда `Add-Migration` формирует код для создания схемы исходной базы данных. Схема создается на основе модели, указанной в `DbContext`в файле *RazorPagesMovieContext.cs*. Аргумент `InitialCreate` используется для присвоения имен миграции. Можно использовать любое имя, однако по соглашению используется имя, которое описывает миграцию. Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.

Команда `Update-Database` выполняет метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*. Метод `Up` создает базу данных.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Выполните следующие команды интерфейса командной строки .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип. Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию. С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".

Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Проверка контекста, зарегистрированного с помощью внедрения зависимостей

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.

Проверьте метод `Startup.ConfigureServices`. Средством формирования шаблонов была добавлена выделенная строка:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` координирует функции EF Core, такие как Create (создание), Read (чтение), Update (обновление) и Delete (удаление) для модели `Movie`. Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). Контекст данных указывает сущности, которые включаются в модель данных.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных. Сущность соответствует строке в таблице.

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Проверьте метод `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Тестирование приложения

* Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).

Если возникает ошибка.

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Вы пропустили [шаг миграции](#pmc).

* Протестируйте ссылку **Создать**.

  ![Страница "Создать"](model/_static/conan.png)

  > [!NOTE]
  > В поле `Price` нельзя вводить десятичные запятые. Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения. Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.

В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end
