---
title: Учебник. Часть 5. Применение миграций к примеру приложения университета Contoso
description: Часть 5 серии руководств по университету Contoso. Используйте функцию миграций EF Core для управления изменениями модели данных в приложении ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: contperf-fy21q2
ms.date: 11/13/2020
ms.topic: tutorial
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: aebbc3f29b0356c7993abd83869ab21d3613bf61
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589352"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a>Учебник. Часть 5. Применение миграций к примеру приложения университета Contoso

В этом руководстве вы начинаете использовать функцию миграций EF Core для управления изменениями модели данных. В последующих руководствах вы добавите дополнительные миграции по мере изменения модели данных.

Изучив это руководство, вы:

> [!div class="checklist"]
> * Дополнительные сведения о миграциях
> * Создание первоначальной миграции
> * Обзор методов Up и Down
> * Дополнительные сведения о моментальном снимке модели данных
> * Применение миграции

## <a name="prerequisites"></a>Предварительные требования

* [Сортировка, фильтрация и разбиение на страницы](sort-filter-page.md)

## <a name="about-migrations"></a>Сведения о миграциях

При разработке нового приложения ваша модель данных часто меняется, и при каждом таком изменении она нарушает синхронизацию с базой данных. Вы начали работу с этими руководствами, настроив Entity Framework для создания базы данных, если она еще не существует. Затем при каждом изменении модели данных — добавлении, удалении или изменении классов сущностей или изменении класса DbContext — вы можете удалить базу данных, после чего EF создает новую, которая соответствует данной модели, и заполняет ее тестовыми данными.

Этот способ для обеспечения синхронизации базы данных с моделью данных хорошо работает до развертывания приложения в рабочей среде. Когда приложение выполняется в рабочей среде, оно обычно хранит данные, которые вы хотите сохранить, и вам нежелательно терять все при каждом изменении, например при добавлении нового столбца. Функция миграций EF Core решает эту проблему, позволяя EF обновить схему базы данных вместо создания базы данных.

Для миграции можно использовать **консоль диспетчера пакетов** (PMC) или CLI.  Эти руководства демонстрируют использование команд интерфейса командной строки. Дополнительные сведения о PMC [приведены в конце этого руководства](#pmc).

## <a name="drop-the-database"></a>Удалите базу данных:

Установите средства EF Core в качестве [глобальных средств](/ef/core/miscellaneous/cli/dotnet) и удалите базу данных:

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

Следующий раздел описывает выполнение команд интерфейса командной строки.

## <a name="create-an-initial-migration"></a>Создание первоначальной миграции

Сохраните изменения и выполните сборку проекта. После этого откройте командное окно и в папку проекта. Вот как это можно сделать быстро:

* Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите в контекстном меню пункт **Открыть папку в проводнике**.

  ![Элемент меню "Открыть в проводнике"](migrations/_static/open-in-file-explorer.png)

* Введите "cmd" в адресной строке и нажмите клавишу ВВОД.

  ![Открытие командного окна](migrations/_static/open-command-window.png)

Введите в командном окне следующую команду:

```dotnetcli
dotnet ef migrations add InitialCreate
```

Приведенные выше команды выводят результат наподобие следующего:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

Если отображается сообщение об ошибке "*Доступ к файлу... ContosoUniversity.dll невозможен, так как файл используется другим процессом*", найдите значок IIS Express в области уведомлений Windows, щелкните его правой кнопкой мыши, а затем выберите **ContosoUniversity > Остановить сайт**.

## <a name="examine-up-and-down-methods"></a>Обзор методов Up и Down

При выполнении команды `migrations add` система EF сформировала код, который создаст базу данных с нуля. Этот код находится в файле *\<timestamp>_InitialCreate.cs* внутри папки *Migrations*. Метод `Up` класса `InitialCreate` создает таблицы базы данных, которые соответствуют наборам сущностей модели данных, а метод `Down` удаляет их, как показано в следующем примере.

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Функция миграций вызывает метод `Up`, чтобы реализовать изменения модели данных для миграции. При вводе команды для отката обновления функция миграций вызывает метод `Down`.

Этот пример кода предназначен для первоначальной миграции, созданной при вводе команды `migrations add InitialCreate`. Параметр имени миграции (в примере это "InitialCreate") используется в качестве имени файла и может быть любым. Рекомендуется выбрать слово или фразу, которые кратко описывают назначение миграции. Например, последнюю миграцию можно назвать "AddDepartmentTable".

Если вы создали первоначальную миграцию, когда база данных уже существовала, код для создания базы данных формируется, но выполнять его не требуется, так как база данных уже соответствует модели данных. Однако при развертывании приложения в другой среде, где база данных еще не существует, этот код будет выполняться для создания базы данных, поэтому рекомендуется сначала его протестировать. Вот почему ранее вы изменили имя базы данных в строке подключения — это позволяет функции миграций создать базу данных с нуля.

## <a name="the-data-model-snapshot"></a>Моментальный снимок модели данных

Функция миграций создает *моментальный снимок* текущей схемы базы данных в *Migrations/SchoolContextModelSnapshot.cs*. При добавлении миграции EF определяет, что именно изменилось, сравнивая модель данных с файлом моментального снимка.

Для удаления миграции используйте команду [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove). `dotnet ef migrations remove` удаляет миграцию и гарантирует корректный сброс моментального снимка. В случае сбоя `dotnet ef migrations remove` используйте `dotnet ef migrations remove -v`, чтобы получить сведения об ошибке.

Дополнительные сведения об использовании файла моментального снимка см. в разделе [Миграции Core EF в средах групп](/ef/core/managing-schemas/migrations/teams).

## <a name="apply-the-migration"></a>Применение миграции

Введите следующую команду в командном окне, чтобы создать базу данных и таблицы в ней.

```dotnetcli
dotnet ef database update
```

Выходные данные команды аналогичны команде `migrations add`, за исключением того, что вы видите журналы для команд SQL, настраивающих базу данных. В приведенном ниже примере выходных данных большинство журналов опущено. Если вам не нужен такой уровень детализации сообщений журнала, можно изменить уровень ведения журнала в файле *appsettings.Development.json*. Дополнительные сведения см. в разделе <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

Используйте **обозреватель объектов SQL Server** для проверки базы данных, как описано в первом руководстве.  Вы заметите добавление таблицы \_\_EFMigrationsHistory, отслеживающей миграции, которые были применены к базе данных. Просмотрев данные в этой таблице, вы увидите одну строку для первой миграции. (Последний журнал в предыдущем примере выходных данных интерфейса командной строки показывает оператор INSERT, создающий эту строку.)

Запустите приложение, чтобы убедиться, что все работает, как и раньше.

![Страница указателя учащихся](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Сравнение CLI и PMC

Средства EF для управления миграциями доступны в виде команд интерфейса командной строки .NET Core или командлетов PowerShell в окне **консоли диспетчера пакетов** Visual Studio. Это руководство описывает использование интерфейса командной строки, но вы можете использовать и консоль диспетчера пакетов.

Команды EF для команд консоли диспетчера пакетов находятся в пакете [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools). Этот пакет входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), поэтому если приложение уже содержит ссылку на пакет `Microsoft.AspNetCore.App`, добавлять ссылку на пакет не нужно.

**Внимание!** Это не тот же пакет, который вы устанавливаете для CLI, изменив файл *.csproj*. Его имя заканчивается на `Tools`, а имя пакета интерфейса командной строки — на `Tools.DotNet`.

Дополнительные сведения о командах интерфейса командной строки см. в разделе [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).

Дополнительные сведения о командах консоли диспетчера пакетов см. в разделе [Консоль диспетчера пакетов (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Получение кода

[Скачайте или ознакомьтесь с готовым приложением.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a>Следующий шаг

В следующем учебнике описаны более сложные вопросы, связанные с развертыванием модели данных. Попутно вы создадите и примените дополнительные миграции.

> [!div class="nextstepaction"]
> [Создание и применение дополнительных миграций](complex-data-model.md)
