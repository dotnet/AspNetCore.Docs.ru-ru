---
title: ASP.NET Core Blazor Server и Entity Framework Core (EF Core)
author: JeremyLikness
description: Руководство по использованию EF Core в приложениях Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 6fc8913640a0a8d506e2c00002912897edbfd826
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280479"
---
# <a name="aspnet-core-blazor-server-with-entity-framework-core-efcore"></a>ASP.NET Core Blazor Server и Entity Framework Core (EF Core)

:::moniker range=">= aspnetcore-5.0"

Blazor Server — это платформа приложений с отслеживанием состояния. Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*. Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал. Для уникальной модели приложения, которую предоставляет Blazor Server, требуется специальный подход к использованию Entity Framework Core.

> [!NOTE]
> В этой статье рассматривается EF Core в приложениях Blazor Server. Приложения Blazor WebAssembly выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных. Запуск EF Core в Blazor WebAssembly выходит за рамки этой статьи.

<h2 id="sample-app-5x">Пример приложения</h2>

Пример приложения был создан в виде справочника для приложений Blazor Server, использующих EF Core. Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления. В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))

В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе. В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL. Это настраивается в `appsettings.Development.json`.

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции. Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.

> [!NOTE]
> Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны. Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров Razor, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).

<h2 id="database-access-5x">Доступ к базе данных</h2>

EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*. В приложениях Blazor Server регистрация службы с заданной областью может быть проблематичной, так как экземпляр является общим для компонентов в канале пользователя. <xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования. Существующие времена существования не подходят по следующим причинам.

* **Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.
* **С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.
* **Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.

Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях Blazor Server.

* По умолчанию рассмотрите возможность использования одного контекста для каждой операции. Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Используйте флаг для предотвращения нескольких одновременных операций.

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  Размещайте операции после строки `Loading = true;` в блоке `try`.

* Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime-5x).

<h3 id="new-dbcontext-instances-5x">Новые экземпляры DbContext</h3>

Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`. Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей. Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).

Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики. EF Core 5.0 и более поздние версии предоставляют встроенную фабрику для создания новых контекстов.

В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных. В этом коде используется [метод расширения (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Фабрика внедряется в компоненты и используется для создания новых экземпляров. Например, для `Pages/Index.razor`:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`. См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

Новые экземпляры <xref:Microsoft.EntityFrameworkCore.DbContext> можно создать с помощью фабрики, которая позволяет настроить строку подключения для каждого экземпляра `DbContext`, например при использовании модели [ Identity ASP.NET Core](xref:security/authentication/customize_identity_model).

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x">Область действия на время существования компонента</h3>

Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента. Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.
Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента. Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Пример приложения обеспечивает удаление контекста при удалении компонента.

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста. В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging">Включение ведения журнала для конфиденциальных данных</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> позволяет включить данные приложения в сообщения об исключениях и журналы платформы. Записанные в журнал данные могут содержать значения, присвоенные свойствам экземпляров сущностей, и значения параметров для команд, отправляемых в базу данных. Использовать <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> для записи данных в журнал **небезопасно**, так как этот метод может раскрывать пароли и другие личные сведения (PII) при записи инструкций SQL, выполняемых в базе данных.

Мы рекомендуем включать метод <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> только на этапах разработки и тестирования.

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server — это платформа приложений с отслеживанием состояния. Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*. Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал. Для уникальной модели приложения, которую предоставляет Blazor Server, требуется специальный подход к использованию Entity Framework Core.

> [!NOTE]
> В этой статье рассматривается EF Core в приложениях Blazor Server. Приложения Blazor WebAssembly выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных. Запуск EF Core в Blazor WebAssembly выходит за рамки этой статьи.

<h2 id="sample-app-3x">Пример приложения</h2>

Пример приложения был создан в виде справочника для приложений Blazor Server, использующих EF Core. Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления. В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))

В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе. В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL. Это настраивается в `appsettings.Development.json`.

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции. Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.

> [!NOTE]
> Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны. Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров Razor, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).

<h2 id="database-access-3x">Доступ к базе данных</h2>

EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*. В приложениях Blazor Server это может быть проблематично, так как экземпляр является общим для компонентов в канале пользователя. <xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования. Существующие времена существования не подходят по следующим причинам.

* **Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.
* **С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.
* **Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.

Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях Blazor Server.

* По умолчанию рассмотрите возможность использования одного контекста для каждой операции. Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Используйте флаг для предотвращения нескольких одновременных операций.

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  Размещайте операции после строки `Loading = true;` в блоке `try`.

* Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime-3x).

<h3 id="new-dbcontext-instances-3x">Новые экземпляры DbContext</h3>

Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`. Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей. Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).

Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики. Пример приложения реализует собственную фабрику в `Data/DbContextFactory.cs`.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

В предыдущей фабрике:

* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> удовлетворяет все зависимости через поставщика услуг;
* `IDbContextFactory` доступен в EF Core ASP.NET Core 5.0 или более поздней версии, поэтому интерфейс [реализован в примере приложения для ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).

В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных. Этот код использует метод расширения для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Фабрика внедряется в компоненты и используется для создания новых экземпляров. Например, для `Pages/Index.razor`:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`. См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

Новые экземпляры <xref:Microsoft.EntityFrameworkCore.DbContext> можно создать с помощью фабрики, которая позволяет настроить строку подключения для каждого экземпляра `DbContext`, например при использовании [модели Identity ASP.NET Core](xref:security/authentication/customize_identity_model):

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x">Область действия на время существования компонента</h3>

Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента. Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.
Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента. Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Пример приложения обеспечивает удаление контекста при удалении компонента.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста. В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

В предшествующем примере:

* Если `Busy` имеет значение `true`, могут начинаться асинхронные операции. Если `Busy` возвращается в значение `false`, асинхронные операции должны быть завершены.
* Разместите дополнительную логику обработки ошибок в блоке `catch`.

<h3 id="enable-sensitive-data-logging">Включение ведения журнала для конфиденциальных данных</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> позволяет включить данные приложения в сообщения об исключениях и журналы платформы. Записанные в журнал данные могут содержать значения, присвоенные свойствам экземпляров сущностей, и значения параметров для команд, отправляемых в базу данных. Использовать <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> для записи данных в журнал **небезопасно**, так как этот метод может раскрывать пароли и другие личные сведения (PII) при записи инструкций SQL, выполняемых в базе данных.

Мы рекомендуем включать метод <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> только на этапах разработки и тестирования.

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Документация по EF Core](/ef/)
