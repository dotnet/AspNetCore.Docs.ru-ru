---
title: 'Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8'
author: rick-anderson
description: Сведения о том, как создать приложение Razor Pages с помощью Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: e797228d498ec9b269e718c099a0532bbb4504d3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530246"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8

Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-5.0"

Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index). В учебниках создается веб-сайт для вымышленного университета Contoso. На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей. В этом руководстве используется подход Code First. См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Скачайте или ознакомьтесь с готовым приложением.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Указания по скачиванию](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Предварительные требования

* Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a>Ядра СУБД

В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.

В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.

Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Устранение неполадок

Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Пример приложения

Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета. Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях. Здесь приведено несколько экранов, создаваемых в руководстве.

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов. Это руководство посвящено использованию EF Core с ASP.NET Core, а не настройке пользовательского интерфейса.

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a>Создание проекта веб-приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Запустите Visual Studio и щелкните **Создать проект**
1. В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** > **Далее**.
1. В диалоговом окне **Настроить новый проект** введите `ContosoUniversity` в поле **Имя проекта**. Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен (`namespace`) совпадали при копировании кода.
1. Щелкните **Создать**.
1. В диалоговом окне **Создайте веб-приложение ASP.NET Core** сделайте следующее:
    1. В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.
    1. **Веб-приложение ASP.NET Core**.
    1. Нажмите кнопку **Создать**.
      ![Диалоговое окно создания проекта ASP.NET Core](~/data/ef-rp/intro/_static/new-aspnet5.png)
    
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* В терминале перейдите в папку, в которой следует создать папку проекта.
* Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a>Настройка стиля сайта

Скопируйте следующий код и вставьте его в файл *Pages/Shared/_Layout.cshtml*: 

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Файл макета задает заголовок, нижний колонтитул и меню для сайта. Приведенный выше код вносит следующие изменения:

* Заменяет все вхождения "ContosoUniversity" на "Contoso University". Таких элементов будет три.
* Удаляются пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность).
* Добавляются пункты меню **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).

Замените содержимое файла *Pages/Index.cshtml* следующим кодом:

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

Приведенный выше код заменяет текст об ASP.NET Core текстом об этом приложении.

Запустите приложение, чтобы проверить правильность отображения домашней страницы.

## <a name="the-data-model"></a>Модель данных

В следующих разделах создается модель данных.

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.

## <a name="the-student-entity"></a>Сущность Student

![Схема сущности Student](intro/_static/student-entity.png)

* Создайте папку *Models* (Модели) в папке проекта. 

* Создайте файл *Models/Student.cs* со следующим кодом:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу. По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`. Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`. Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)

Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships). Свойства навигации содержат другие сущности, связанные с этой сущностью. В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся. Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment. 

В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося. Например, предположим, что строка Student содержит идентификатор 1. Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1. StudentID — это *внешний ключ* в таблице Enrollment. 

Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment. Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`. Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.

## <a name="the-enrollment-entity"></a>Сущность Enrollment

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

Создайте файл *Models/Enrollment.cs* со следующим кодом:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`. Для рабочей модели данных выберите один шаблон и используйте только его. В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу. Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.

Свойство `Grade` имеет тип `enum`. Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/). Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.

Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`. Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.

Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`. Сущность `Enrollment` связана с одной сущностью `Course`.

EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`. Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`. Свойства внешнего ключа также могут называться `<primary key property name>`. Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.

## <a name="the-course-entity"></a>Сущность Course

![Схема сущности Course](intro/_static/course-entity.png)

Создайте файл *Models/Course.cs* со следующим кодом:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

Свойство `Enrollments` является свойством навигации. Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.

Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.

Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.

## <a name="scaffold-student-pages"></a>Формирование шаблона для страниц Student

В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.

* Класс `DbContext` EF Core. Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных. Он является производным от класса <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.
* Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Создайте папку *Pages/Students*.
* В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.
* В диалоговом окне **Добавление нового элемента шаблона** выполните указанные ниже действия.
  * На левой вкладке выберите **Установленные > Общие > Страницы Razor** .
  * Выберите **Razor Pages на основе Entity Framework (CRUD)**  > **Добавить**.
* В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:
  * В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .
  * В строке **Класс контекста данных** щелкните знак плюса ( **+** ).
    * Измените имя контекста данных так, чтобы оно заканчивалось на `SchoolContext`, а не на `ContosoUniversityContext`. Новое имя контекста: `ContosoUniversity.Data.SchoolContext`.
    * Выберите **Добавить**, чтобы завершить добавление класса контекста данных.
   * Выберите **Добавить**, чтобы закрыть диалоговое окно **Добавление Razor Pages**.

Следующие пакеты устанавливаются автоматически:

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов. Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.

* Создайте папку *Pages/Students*.

* Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.

  **В Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  **В macOS или Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

Если предыдущий шаг завершился сбоем, выполните сборку проекта и повторите шаг формирования шаблона.

В процессе формирования шаблона выполняются следующие действия:

* создаются страницы Razor в папке *Pages/Students*:
  * *Create.cshtml* и *Create.cshtml.cs*;
  * *Delete.cshtml* и *Delete.cshtml.cs*;
  * *Details.cshtml* и *Details.cshtml.cs*;
  * *Edit.cshtml* и *Edit.cshtml.cs*;
  * *Index.cshtml* и *Index.cshtml.cs*;
* создается файл *Data/SchoolContext.cs*;
* добавляется контекст для внедрения зависимостей в файле *Startup.cs*;
* добавляет строку подключения к базе данных в файл *appsettings.json* .

## <a name="database-connection-string"></a>Строка подключения к базе данных

Средство формирования шаблонов создает строку подключения в файле *appsettings.json* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде. По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Сократите строку подключения SQLite до *CU.db*:

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Обновление класса контекста базы данных

Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных. Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Контекст указывает сущности, которые включаются в модель данных. В этом проекте соответствующий класс называется `SchoolContext`.

Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Приведенный выше код изменяет форму слова `DbSet<Student> Student` на множественную: `DbSet<Student> Students`. Чтобы код Razor Pages соответствовал новому имени `DBSet`, произведите глобальное изменение `_context.Student.`
на `_context.Students.`.

Всего это имя встречается 8 раз.

Так как набор сущностей содержит несколько сущностей, многие разработчики предпочитают имена свойств `DBSet` во множественном числе.

Выделенный код:

* создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей. В терминологии EF Core:
  * Набор сущностей обычно соответствует таблице базы данных.
  * Сущность соответствует строке в таблице.
* Вызывает <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>. `OnModelCreating`:
  * вызывается, когда контекст `SchoolContext` был инициализирован, но прежде чем модель была заблокирована и использована для инициализации контекста;
  * является обязательным, так как далее в руководстве сущность `Student` будет содержать ссылки на другие сущности.
  <!-- Review, OnModelCreating needs review -->

Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы, например `SchoolContext`, регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы, например Razor Pages, обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Следующие выделенные строки были добавлены средством формирования шаблонов:

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Убедитесь в том, что код, добавленный средством формирования шаблонов, вызывает `UseSqlite`.

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

Сведения об использовании рабочей базы данных см. в статье [Использование SQLite для среды разработки и SQL Server для рабочей среды](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).

---

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .

### <a name="add-the-database-exception-filter"></a>Добавление фильтра исключений базы данных

Добавьте <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> в `ConfigureServices`, как показано в следующем коде:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

Добавьте пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore).

Чтобы добавить пакет NuGet, в PMC введите следующую команду:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

Пакет NuGet `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` предоставляет ПО промежуточного слоя ASP.NET Core для страниц ошибок Entity Framework Core. Это ПО промежуточного слоя помогает обнаруживать и диагностировать ошибки с помощью миграций Entity Framework Core.

`AddDatabaseDeveloperPageExceptionFilter` предоставляет полезные сведения об ошибках в [среде разработки](xref:fundamentals/environments).

## <a name="create-the-database"></a>Создание базы данных

Обновите файл *Program.cs*, чтобы создать базу данных, если она не существует.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует. Если база данных не существует, она создается вместе со схемой. `EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.

* База данных удаляется. Все существующие данные теряются.
* Модель данных изменяется. Например, добавляется поле `EmailAddress`.
* Запустите приложение.
* Метод `EnsureCreated` создает базу данных с новой схемой.

Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется. Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной. В таком случае используйте перенос.

Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос. Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.

### <a name="test-the-app"></a>Тестирование приложения

* Запустите приложение.
* Щелкните ссылку **Students** и выберите **Создать**.
* Протестируйте ссылки Edit, Details и Delete.

## <a name="seed-the-database"></a>Заполнение базы данных

Метод `EnsureCreated` создает пустую базу данных. В этом разделе добавляется код, который заполняет базу данных тестовыми данными.

Создайте файл *Data/DbInitializer.cs* со следующим кодом:
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Этот код проверяет наличие учащихся в базе данных. Если учащихся нет, в базу данных добавляются тестовые данные. Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.

В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):

```powershell
Drop-Database -Confirm
```

Выберите `Y`, чтобы удалить базу данных.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Остановите приложение, если оно выполняется, и удалите файл *CU.db*.

---

* Перезапустите приложение.
* Выберите страницу учащихся, чтобы увидеть заполненные данные.

## <a name="view-the-database"></a>Просмотр базы данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.
* В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** . Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.
* Разверните узел **Таблицы**.
* Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.
* Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код**, чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Используйте средство SQLite для просмотра схемы базы данных и заполненных данных. Файл базы данных называется *CU.db* и находится в папке проекта.

---

## <a name="asynchronous-code"></a>Асинхронный код

Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.

Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки. В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки. В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода. В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов. Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.

Во время выполнения асинхронный код использует немного больше служебных ресурсов. Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.

В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task`, ключевое слово `await` и метод `ToListAsync`.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Ключевое слово `async` указывает компилятору:
  * создавать обратные вызовы для частей тела метода;
  * создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).
* Тип возвращаемого значения `Task` представляет текущую операцию.
* Ключевое слово `await` предписывает компилятору разделить метод на две части. Первая часть завершается операцией, которая запускается в асинхронном режиме. Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.
* `ToListAsync` является асинхронной версией метода расширения `ToList`.

При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:

* Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды. К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`. В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.
* Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.

Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a>Вопросы производительности

Как правило, веб-страница не должна загружать произвольное число строк. Запрос должен использовать разбиение на страницы или применять ограничение. Например, предыдущий запрос может использовать `Take` для ограничения количества возвращаемых строк:

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

Если в процессе перечисления большой таблицы в представлении возникло исключение базы данных, может быть возвращен ответ HTTP 200 с сообщением о частично сформированных данных.

Значение <xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> по умолчанию равно 1024. Следующий код задает `MaxModelBindingCollectionSize`:

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

Разбиение на страницы рассматривается далее в этом руководстве.

## <a name="next-steps"></a>Следующие шаги

> [!div class="step-by-step"]
> [Следующий учебник](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index). В учебниках создается веб-сайт для вымышленного университета Contoso. На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей. В этом руководстве используется подход Code First. См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Скачайте или ознакомьтесь с готовым приложением.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Указания по скачиванию](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Предварительные требования

* Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Ядра СУБД

В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.

В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.

Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Устранение неполадок

Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Пример приложения

Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета. Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях. Здесь приведено несколько экранов, создаваемых в руководстве.

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов. Это руководство посвящено использованию EF Core, а не настройке пользовательского интерфейса.

Чтобы получить исходный код готового проекта, перейдите по ссылке в верхней части страницы. В папке *cu30* содержится код для версии учебника по ASP.NET Core 3.0. Файлы, отражающие состояние кода для учебников 1–7, находятся в папке *cu30snapshots*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.

* Выполните построение проекта.
* В консоли диспетчера пакетов (PMC) выполните следующую команду:

  ```powershell
  Update-Database
  ```

* Запустите проект, чтобы заполнить базу данных.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.

* Удалите файл *ContosoUniversity.csproj*, а файл *ContosoUniversitySQLite.csproj* переименуйте в *ContosoUniversity.csproj*.
* В файле *Program.cs* закомментируйте `#define Startup`, чтобы использовать `StartupSQLite`.
* Удалите файл *appSettings.json*, а файл *appSettingsSQLite.json* переименуйте в *appSettings.json*.
* Удалите папку *Migrations*, а папку *MigrationsSQL* переименуйте в *Migrations*.
* Выполните глобальный поиск `#if SQLiteVersion` и удалите `#if SQLiteVersion` и связанную инструкцию `#endif`.
* Выполните построение проекта.
* В командной строке в папке проекта выполните следующие команды:

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* В средстве SQLite выполните следующую инструкцию SQL:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* Запустите проект, чтобы заполнить базу данных.

---

## <a name="create-the-web-app-project"></a>Создание проекта веб-приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.
* Выберите **Новое веб-приложение ASP.NET Core**.
* Назовите проект *ContosoUniversity*. Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен совпадали при копировании и вставке кода.
* Выберите в раскрывающихся списках пункты **.NET Core** и **ASP.NET Core 3.0**, а затем выберите **Веб-приложение**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* В терминале перейдите в папку, в которой следует создать папку проекта.

* Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Настройка стиля сайта

Настройте заголовок сайта, нижний колонтитул и меню, внеся изменения в файл *Pages/Shared/_Layout.cshtml*.

* Замените все вхождения "ContosoUniversity" на "Contoso University". Таких элементов будет три.

* Удалите пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность). Добавьте пункты **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).

Изменения выделены.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст об ASP.NET Core описанием этого приложения:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Запустите приложение, чтобы проверить правильность отображения домашней страницы.

## <a name="the-data-model"></a>Модель данных

В следующих разделах создается модель данных.

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.

## <a name="the-student-entity"></a>Сущность Student

![Схема сущности Student](intro/_static/student-entity.png)

* Создайте папку *Models* (Модели) в папке проекта.
* Создайте файл *Models/Student.cs* со следующим кодом:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу. По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`. Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`. Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)

Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships). Свойства навигации содержат другие сущности, связанные с этой сущностью. В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся. Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment. 

В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося. Например, предположим, что строка Student содержит идентификатор 1. Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1. StudentID — это *внешний ключ* в таблице Enrollment. 

Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment. Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`. Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.

## <a name="the-enrollment-entity"></a>Сущность Enrollment

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

Создайте файл *Models/Enrollment.cs* со следующим кодом:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`. Для рабочей модели данных выберите один шаблон и используйте только его. В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу. Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.

Свойство `Grade` имеет тип `enum`. Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/). Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.

Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`. Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.

Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`. Сущность `Enrollment` связана с одной сущностью `Course`.

EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`. Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`. Свойства внешнего ключа также могут называться `<primary key property name>`. Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.

## <a name="the-course-entity"></a>Сущность Course

![Схема сущности Course](intro/_static/course-entity.png)

Создайте файл *Models/Course.cs* со следующим кодом:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

Свойство `Enrollments` является свойством навигации. Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.

Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.

Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.

## <a name="scaffold-student-pages"></a>Формирование шаблона для страниц Student

В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.

* Класс *контекста* EF Core. Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных. Он является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.
* Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В папке *Pages* создайте папку *Students*.
* В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.
* В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.
* В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:
  * В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .
  * В строке **Класс контекста данных** щелкните знак плюса ( **+** ).
  * Измените имя контекста данных с *ContosoUniversity.Models.ContosoUniversityContext* на *ContosoUniversity.Data.SchoolContext*.
  * Нажмите **Добавить**.

Следующие пакеты устанавливаются автоматически:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов. Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.

* Создайте папку *Pages/Students*.

* Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.

  **В Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **В macOS или Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Если в предыдущем шаге возникает проблема, выполните сборку проекта и повторите шаг формирования шаблона.

В процессе формирования шаблона выполняются следующие действия:

* создаются страницы Razor в папке *Pages/Students*:
  * *Create.cshtml* и *Create.cshtml.cs*;
  * *Delete.cshtml* и *Delete.cshtml.cs*;
  * *Details.cshtml* и *Details.cshtml.cs*;
  * *Edit.cshtml* и *Edit.cshtml.cs*;
  * *Index.cshtml* и *Index.cshtml.cs*;
* создается файл *Data/SchoolContext.cs*;
* добавляется контекст для внедрения зависимостей в файле *Startup.cs*;
* добавляет строку подключения к базе данных в файл *appsettings.json* .

## <a name="database-connection-string"></a>Строка подключения к базе данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В файле *appsettings.json* указывается строка подключения для [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде. По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Измените строку подключения так, чтобы она указывала на файл базы данных SQLite с именем *CU.db*.

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Обновление класса контекста базы данных

Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных. Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Контекст указывает сущности, которые включаются в модель данных. В этом проекте соответствующий класс называется `SchoolContext`.

Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей. В терминологии EF Core:

* Набор сущностей обычно соответствует таблице базы данных.
* Сущность соответствует строке в таблице.

Так как набор сущностей содержит несколько сущностей, свойства DBSet должны иметь имена во множественном числе. Так как средство формирования шаблонов создало DBSet `Student`, в этом шаге его имя меняется на имя во множественном числе: `Students`. 

Чтобы код Razor Pages соответствовал новому имени DBSet, измените `_context.Student` на `_context.Students` во всем проекте.  Всего это имя встречается 8 раз.

Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Выделенные строки в `ConfigureServices` были добавлены средством формирования шаблонов:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Убедитесь в том, что код, добавленный средством формирования шаблонов в `ConfigureServices`, вызывает `UseSqlite`.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .

## <a name="create-the-database"></a>Создание базы данных

Обновите файл *Program.cs*, чтобы создать базу данных, если она не существует.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует. Если база данных не существует, она создается вместе со схемой. `EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.

* База данных удаляется. Все существующие данные теряются.
* Модель данных изменяется. Например, добавляется поле `EmailAddress`.
* Запустите приложение.
* Метод `EnsureCreated` создает базу данных с новой схемой.

Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется. Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной. В таком случае используйте перенос.

Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос. Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.

### <a name="test-the-app"></a>Тестирование приложения

* Запустите приложение.
* Щелкните ссылку **Students** и выберите **Создать**.
* Протестируйте ссылки Edit, Details и Delete.

## <a name="seed-the-database"></a>Заполнение базы данных

Метод `EnsureCreated` создает пустую базу данных. В этом разделе добавляется код, который заполняет базу данных тестовыми данными.

Создайте файл *Data/DbInitializer.cs* со следующим кодом:
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Этот код проверяет наличие учащихся в базе данных. Если учащихся нет, в базу данных добавляются тестовые данные. Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.

* В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Остановите приложение, если оно выполняется, и удалите файл *CU.db*.

---

* Перезапустите приложение.

* Выберите страницу учащихся, чтобы увидеть заполненные данные.

## <a name="view-the-database"></a>Просмотр базы данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.
* В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** . Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.
* Разверните узел **Таблицы**.
* Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.
* Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код**, чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Используйте средство SQLite для просмотра схемы базы данных и заполненных данных. Файл базы данных называется *CU.db* и находится в папке проекта.

---

## <a name="asynchronous-code"></a>Асинхронный код

Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.

Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки. В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки. В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода. В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов. Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.

Во время выполнения асинхронный код использует немного больше служебных ресурсов. Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.

В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Ключевое слово `async` указывает компилятору:
  * создавать обратные вызовы для частей тела метода;
  * создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).
* Тип возвращаемого значения `Task<T>` представляет текущую операцию.
* Ключевое слово `await` предписывает компилятору разделить метод на две части. Первая часть завершается операцией, которая запускается в асинхронном режиме. Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.
* `ToListAsync` является асинхронной версией метода расширения `ToList`.

При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:

* Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды. К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`. В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.
* Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.

Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Следующие шаги

> [!div class="step-by-step"]
> [Следующий учебник](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

На примере веб-приложения Contoso University показано, как создать веб-приложение Razor Pages ASP.NET Core с помощью Entity Framework (EF) Core.

В этом примере приложения реализуется веб-сайт вымышленного университета Contoso. На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей. Это первое руководство из серии, в котором описывается создание примера приложения для университета Contoso.

[Скачайте или ознакомьтесь с готовым приложением.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Указания по скачиванию](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Опыт работы с [Razor Pages](xref:razor-pages/index). Начинающие программисты должны изучить статью [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к этой серии.

## <a name="troubleshooting"></a>Устранение неполадок

Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) в разделе, посвященном [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>Веб-приложение университета Contoso

Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.

Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях. Здесь приведено несколько экранов, создаваемых в руководстве.

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

Стиль пользовательского интерфейса для этого сайта близок к обеспечиваемому встроенными шаблонами. Это руководство посвящено использованию EF Core с Razor Pages, а не работе с пользовательским интерфейсом.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Создание веб-приложения Razor Pages ContosoUniversity

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.
* Создайте новое веб-приложение ASP.NET Core. Назовите проект **ContosoUniversity**. Очень важно, чтобы проект имел имя *ContosoUniversity*, чтобы совпадали пространства имен при копировании и вставке кода.
* Выберите в раскрывающемся списке **ASP.NET Core 2.1**, а затем **Веб-приложение**.

Изображения для приведенных выше шагов см. в разделе [Создание веб-приложения Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Запустите приложение.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Настройка стиля сайта

Выполните небольшую настройку меню, макета и домашней страницы сайта. Внесите следующие изменения в файл *Pages/Shared/_Layout.cshtml*:

* Замените все вхождения "ContosoUniversity" на "Contoso University". Таких элементов будет три.

* Добавьте пункты меню **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Contact** (Контакты).

Изменения выделены. (Вся разметка *не* отображается.)

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст о ASP.NET и MVC описанием этого приложения:

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Создание модели данных

Создайте классы сущностей для приложения университета Contoso. Начните со следующих трех сущностей:

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

Между сущностями `Student` и `Enrollment` действует связь один ко многим. Между сущностями `Course` и `Enrollment` действует связь один ко многим. Студент может записаться на любое число курсов. На курс может быть зачислено любое количество учащихся.

В следующих разделах создается класс для каждой из этих сущностей.

### <a name="the-student-entity"></a>Сущность Student

![Схема сущности Student](intro/_static/student-entity.png)

Создайте папку *Models*. В папке *Models* создайте файл класса с именем *Student.cs*, содержащий следующий код:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу. По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`. В `classnameID``classname` — это имя класса. Альтернативным автоматически распознаваемым первичным ключом является `StudentID` в предыдущем примере.

Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships). Свойства навигации ссылаются на другие сущности, связанные с этой сущностью. В этом случае свойство `Enrollments` сущности `Student entity` содержит все сущности `Enrollment`, которые связаны с этой сущностью `Student`. Например, если строка "Student" (Учащийся) в базе данных имеет две связанные строки "Enrollment" (зачисление), свойство навигации `Enrollments` содержит две этих сущности `Enrollment`. Связанная строка `Enrollment` содержит значение первичного ключа для этого учащегося в столбце `StudentID`. Предположим, например, что учащийся с идентификатором 1 имеет две строки в таблице `Enrollment`. Таблица `Enrollment` содержит две строки с `StudentID` = 1. `StudentID` является внешним ключом в таблице `Enrollment`, указывающим учащегося в таблице `Student`.

Если свойство навигации может содержать несколько сущностей, оно должно иметь тип списка, такой как `ICollection<T>`. Можно указать `ICollection<T>` либо тип, такой как `List<T>` или `HashSet<T>`. Если используется `ICollection<T>`, платформа EF Core по умолчанию создает коллекцию `HashSet<T>`. Свойства навигации, содержащие несколько сущностей, поступают по связям многие ко многим и один ко многим.

### <a name="the-enrollment-entity"></a>Сущность Enrollment

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

В папке *Models* создайте файл *Enrollment.cs*, содержащий следующий код:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

Свойство `EnrollmentID` является первичным ключом. Эта сущность использует шаблон `classnameID` вместо `ID`, как сущность `Student`. Обычно разработчики выбирают один шаблон и используют его в рамках всей модели данных. В одном из следующих руководств показано, за счет чего использование идентификатора без имени класса позволяет упростить реализацию наследования в модели данных.

Свойство `Grade` имеет тип `enum`. Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade` допускает значение null. Оценка со значением null отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.

Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`. Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`. Сущность `Student` отличается от свойства навигации `Student.Enrollments`, которое содержит несколько сущностей `Enrollment`.

Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`. Сущность `Enrollment` связана с одной сущностью `Course`.

EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`. Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`. Свойства внешнего ключа также могут называться `<primary key property name>`. Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.

### <a name="the-course-entity"></a>Сущность Course

![Схема сущности Course](intro/_static/course-entity.png)

В папке *Models* создайте файл *Course.cs*, содержащий следующий код:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

Свойство `Enrollments` является свойством навигации. Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.

Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.

## <a name="scaffold-the-student-model"></a>Формирование шаблона для модели Student

В этом разделе формируется шаблон для модели Student. То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели Student.

* Выполните построение проекта.
* Создайте папку *Pages/Students*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.
* В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.

Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :

* В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .
* В строке **Класс контекста данных** щелкните значок плюса ( **+** ) и измените автоматически присвоенное имя на **ContosoUniversity.Models.SchoolContext**.
* В раскрывающемся списке **Класс контекста данных** выберите **ContosoUniversity.Models.SchoolContext**
* Нажмите **Добавить**.

![Диалоговое окно CRUD](intro/_static/s1.png)

Если на предыдущем шаге у вас возникли проблемы, см. раздел [Создание модели фильма](xref:tutorials/razor-pages/model#scaffold-the-movie-model).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Выполните следующую команду, чтобы сформировать шаблон для модели Student.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

В процессе формирования шаблонов были созданы и изменены следующие файлы:

### <a name="files-created"></a>Создаваемые файлы

* *Pages/Students* Create, Delete, Details, Edit, Index.
* *Data/SchoolContext.cs*.

### <a name="file-updates"></a>Обновления файла

* *Startup.cs*: изменения в этом файле подробно описываются в следующем разделе.
* *appsettings.json* : добавляется строка подключения, используемая для подключения к локальной базе данных.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Проверка контекста, зарегистрированного с помощью внедрения зависимостей

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.

Проверьте метод `ConfigureServices` в файле *Startup.cs*. Средством формирования шаблонов была добавлена выделенная строка:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .

## <a name="update-main"></a>Обновление метода Main

В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее:

* Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.
* Вызовите [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Высвободите контекст после завершения работы метода `EnsureCreated`.

В следующем примере кода показан обновленный файл *Program.cs*.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` позволяет проверить существование базы данных для контекста. Если контекст существует, никаких действий не предпринимается. Если контекст не существует, создаются база данных и вся ее схема. `EnsureCreated` не использует миграции для создания базы данных. Созданную с помощью `EnsureCreated` базу данных впоследствии нельзя обновить, используя миграции.

`EnsureCreated` вызывается при запуске приложения, что обеспечивает следующий рабочий процесс:

* Удалите базу данных.
* Измените схему базы данных (например, добавьте поле `EmailAddress`).
* Запустите приложение.
* `EnsureCreated` создает базу данных со столбцом `EmailAddress`.

`EnsureCreated` удобно использовать на ранних стадиях разработки, когда схема часто меняется. Далее в этом учебнике база данных удаляется и используются миграции.

### <a name="test-the-app"></a>Тестирование приложения

Запустите приложение и примите политику использования файлов cookie. Это приложение не хранит персональные данные. Сведения о политике использования файлов cookie можно прочитать в разделе [Поддержка общего регламента по защите данных ЕС (GDPR)](xref:security/gdpr).

* Щелкните ссылку **Students** и выберите **Создать**.
* Протестируйте ссылки Edit, Details и Delete.

## <a name="examine-the-schoolcontext-db-context"></a>Проверка контекста базы данных SchoolContext

Контекст базы данных — это класс main, который координирует функциональные возможности EF Core для заданной модели данных. Контекст данных получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Контекст данных указывает сущности, которые включаются в модель данных. В этом проекте соответствующий класс называется `SchoolContext`.

Обновите файл *SchoolContext.cs*, добавив в него следующий код:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей. В терминологии EF Core:

* Набор сущностей обычно соответствует таблице базы данных.
* Сущность соответствует строке в таблице.

`DbSet<Enrollment>` и `DbSet<Course>` можно опустить. Платформа EF Core включает эти операторы неявно, так как сущность `Student` ссылается на сущность `Enrollment`, а `Enrollment` ссылается на сущность `Course`. В рамках данного руководства оставьте `DbSet<Enrollment>` и `DbSet<Course>` в `SchoolContext`.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде. LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны. По умолчанию LocalDB создает файлы базы данных *MDF* в каталоге `C:/Users/<user>`.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Добавление кода для инициализации базы данных с использованием тестовых данных

EF Core создает пустую базу данных. В этом разделе создается метод `Initialize`, предназначенный для заполнения тестовыми данными.

В папке *Data* создайте файл класса с именем *DbInitializer.cs* и добавьте следующий код:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Примечание. Предыдущий код использует `Models` для пространства имен (`namespace ContosoUniversity.Models`) вместо `Data`. `Models` согласуется с кодом, созданным средством формирования шаблонов. См. дополнительные сведения о [проблеме с формированием шаблонов на GitHub ](https://github.com/aspnet/Scaffolding/issues/822).

Код проверяет наличие учащихся в базе данных. Если учащихся в базе данных нет, она заполняется тестовыми данными. Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.

Метод `EnsureCreated` автоматически создает базу данных для контекста базы данных. Если такая база данных существует, `EnsureCreated` возвращает управление без изменения базы данных.

В файле *Program.cs* измените метод `Main`, чтобы реализовать вызов `Initialize`:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Остановите приложение, если оно выполняется, и удалите файл *CU.db*.

---

## <a name="view-the-db"></a>Просмотр базы данных

Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID. Таким образом, имя базы данных будет SchoolContext-{GUID}. GUID будет отличаться для каждого пользователя.
Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.
В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .

Разверните узел **Таблицы**.

Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.

## <a name="asynchronous-code"></a>Асинхронный код

Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.

Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки. В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки. В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода. В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов. Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.

Во время выполнения асинхронный код использует немного больше служебных ресурсов. Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.

В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Ключевое слово `async` указывает компилятору:
  * создавать обратные вызовы для частей тела метода;
  * автоматически создавать возвращаемый объект [Task](/dotnet/api/system.threading.tasks.task). Дополнительные сведения см. в разделе [Тип возвращаемых значений задач](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Неявный тип возвращаемого значения `Task` представляет текущую операцию.
* Ключевое слово `await` предписывает компилятору разделить метод на две части. Первая часть завершается операцией, которая запускается в асинхронном режиме. Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.
* `ToListAsync` является асинхронной версией метода расширения `ToList`.

При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:

* Асинхронно выполняются только те операторы, в результате которых в базу данных отправляются запросы или команды. К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`. В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.
* Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.

Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).

Следующее руководство посвящено базовым операциям CRUD (создание, чтение, обновление, удаление).



## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Вперед](xref:data/ef-rp/crud)

::: moniker-end
