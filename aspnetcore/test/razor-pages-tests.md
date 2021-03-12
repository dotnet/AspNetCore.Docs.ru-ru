---
title: Модульные тесты Razor Pages в ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать модульные тесты для приложений Razor Pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: test/razor-pages-tests
ms.openlocfilehash: a67f51561d0a165db04418f2040ffe68e3ebe8ce
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588494"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Модульные тесты Razor Pages в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core поддерживает модульные тесты приложений Razor Pages. Тесты уровня доступа к данным (DAL) и модели страниц помогают обеспечить следующее:

* во время создания приложения Razor Pages его части работают независимо друг от друга и вместе как единое целое;
* классы и методы имеют ограниченные области ответственности;
* существует дополнительная документация о том, как приложение должно себя вести;
* регрессии, которые являются ошибками, вызванными обновлениями кода, обнаруживаются во время автоматической сборки и развертывания.

В этом разделе предполагается, что у вас есть базовое представление о приложениях Razor Pages и модульных тестах. Если вы не знакомы с приложениями Razor Pages или основами тестирования, см. следующие статьи:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Модульное тестирование C# в .NET Core с использованием dotnet test и xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/razor-pages-tests/samples) ([как скачивать](xref:index#how-to-download-a-sample))

Пример проекта состоит из двух приложений:

| Приложение         | Папка проекта                     | Описание |
| ----------- | ---------------------------------- | ----------- |
| Приложение для сообщений | *src/RazorPagesTestSample*         | Позволяет пользователю добавлять сообщение, удалять одно сообщение, удалять все сообщения и анализировать сообщения (найти среднее количество слов на сообщение). |
| Тестирование приложения.    | *tests/RazorPagesTestSample.Tests* | Используется для модульного тестирования модели DAL и индексной страницы приложения для сообщений. |

Тесты можно выполнять с помощью встроенных функций тестирования интегрированной среды разработки, таких как [Visual Studio](/visualstudio/test/unit-test-your-code) или [Visual Studio для Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). При использовании [Visual Studio Code](https://code.visualstudio.com/) или командной строки выполните следующую команду в командной строке в папке *tests/RazorPagesTestSample.Tests*:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Организация приложения для сообщений

Приложение для сообщений — это система сообщений Razor Pages со следующими характеристиками:

* Индексная страница приложения (*Pages/Index.cshtml* и *Pages/Index.cshtml.cs*) предоставляет методы пользовательского интерфейса и модели страницы для управления добавлением, удалением и анализом сообщений (поиск среднего числа слов на сообщение).
* Сообщение описывается классом `Message` (*Data/Message.cs*) с двумя свойствами: `Id` (ключ) и `Text` (сообщение). Свойство `Text` является обязательным и ограничено 200 символами.
* Сообщения хранятся с помощью [базы данных Entity Framework в памяти](/ef/core/providers/in-memory/)&#8224;.
* Приложение содержит слой DAL в своем классе контекста базы данных — `AppDbContext` (*Data/AppDbContext.cs*). Методы DAL помечаются как `virtual`, что позволяет макетирование методов для использования в тестах.
* Если база данных пуста при запуске приложения, то хранилище сообщений инициализируется тремя сообщениями. Эти *начальные сообщения* также используются в тестах.

&#8224;В разделе документации о EF [Тестирование с помощью InMemory](/ef/core/miscellaneous/testing/in-memory) объясняется, как использовать базу данных в памяти для тестов с помощью MSTest. В этом разделе используется платформа тестирования [xUnit](https://xunit.github.io/). Концепции тестирования и реализации тестов в разных платформах тестирования похожи, но не идентичны.

Хотя пример приложения не использует шаблон репозитория и не является эффективным примером [шаблона "единица работы" (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages поддерживает такие шаблоны разработки. Дополнительные сведения см. в разделах [Проектирование уровня сохраняемости инфраструктуры](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) и <xref:mvc/controllers/testing> (пример реализует шаблон репозитория).

## <a name="test-app-organization"></a>Организация приложения для тестирования

Приложение для тестирования — это консольное приложение в папке *tests/RazorPagesTestSample.Tests*.

| Папка приложения для тестирования | Описание |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>Файл *DataAccessLayerTest.cs* содержит модульные тесты для DAL.</li><li>Файл *IndexPageTests.cs* содержит модульные тесты для модели индексной страницы.</li></ul> |
| *Utilities*     | Содержит метод `TestDbContextOptions`, используемый для создания новых параметров контекста базы данных для каждого модульного теста DAL, чтобы база данных сбрасывалась в базовое состояние для каждого теста. |

Используемая платформа тестирования — [xUnit](https://xunit.github.io/). Используемая платформа макетирования объектов — [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Модульные тесты уровня доступа к данным (DAL)

Приложение для сообщений имеет DAL с четырьмя методами, содержащимися в классе `AppDbContext` (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Каждый метод содержит один или два модульных теста в приложении для тестирования.

| Метод DAL               | Функция                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Получает `List<Message>` из базы данных, отсортированной по свойству `Text`. |
| `AddMessageAsync`        | Добавляет `Message` в базу данных.                                          |
| `DeleteAllMessagesAsync` | Удаляет все записи `Message` из базы данных.                           |
| `DeleteMessageAsync`     | Удаляет одну запись `Message` из базы данных по полю `Id`.                      |

Модульные тесты DAL требует <xref:Microsoft.EntityFrameworkCore.DbContextOptions> при создании нового экземпляра `AppDbContext` для каждого теста. Один из подходов к созданию `DbContextOptions` для каждого теста заключается в использовании <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Проблема этого подхода заключается в том, что каждый тест получает базу данных в том состоянии, в котором ее оставил предыдущий тест. Это может быть проблемой при попытке написания атомарных модульных тестов, которые не должны мешать друг другу. Чтобы заставить `AppDbContext` использовать новый контекст базы данных для каждого теста, укажите экземпляр `DbContextOptions`, основанный на новом поставщике служб. В приложении для тестирования показано, как это сделать с помощью метода `TestDbContextOptions` класса `Utilities` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*).

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Использование `DbContextOptions` в модульных тестах DAL позволяет выполнять каждый тест атомарно с использованием нового экземпляра базы данных.

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Каждый метод тестирования в классе `DataAccessLayerTest` (*UnitTests/DataAccessLayerTest.cs*) соответствует аналогичному шаблону "размещение-действие-утверждение".

1. Размещение: база данных настраивается для тестирования и (или) определяется ожидаемый результат.
1. Действие: выполняется тестирование.
1. Утверждение: утверждения выполняются, чтобы определить, является ли результат теста успешным.

Например, метод `DeleteMessageAsync` отвечает за удаление одного сообщения, идентифицируемого по `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*).

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Для этого метода существует два теста. Один тест проверяет, что метод удаляет сообщение, когда оно присутствует в базе данных. Другой метод проверяет, что база данных не изменяется, если не существует `Id` сообщения для удаления. Ниже представлен код метода `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`.

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Сначала метод выполняет этап размещения, на котором выполняется подготовка для этапа действия. Начальные сообщения получаются и сохраняются в `seedMessages`. Начальные сообщения сохраняются в базу данных. Сообщение с `Id`, равным `1`, устанавливается для удаления. После выполнения метода `DeleteMessageAsync` ожидаемый набор сообщений должен содержать все сообщения, кроме одного с `Id`, равным `1`. Переменная `expectedMessages` содержит ожидаемый результат.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Действие метода: выполняется метод `DeleteMessageAsync` с параметром `recId`, равным `1`.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Наконец, метод получает `Messages` из контекста и сравнивает его с `expectedMessages`, проверяя, что эти два значения равны.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Чтобы сравнить, что два `List<Message>` одинаковы.

* Сообщения упорядочиваются по `Id`.
* Пары сообщений сравниваются по свойству `Text`.

Аналогичный метод теста `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` проверяет результат попытки удаления несуществующего сообщения. В этом случае после выполнения метода `DeleteMessageAsync` ожидаемый набор сообщений в базе данных должен быть равным фактическим сообщениям. Содержимое базы данных не должно измениться:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Модульные тесты методов модели страницы

Другой набор модульных тестов отвечает за тестирование методов модели страницы. В приложении для сообщений модели индексных страниц находятся в классе `IndexModel` в файле *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Метод модели страницы | Функция |
| ----------------- | -------- |
| `OnGetAsync` | Получает сообщения из DAL для пользовательского интерфейса с помощью метода `GetMessagesAsync`. |
| `OnPostAddMessageAsync` | Если [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) является допустимым, вызывает метод `AddMessageAsync`, чтобы добавить сообщение в базу данных. |
| `OnPostDeleteAllMessagesAsync` | Вызывает метод `DeleteAllMessagesAsync` для удаления всех сообщений в базе данных. |
| `OnPostDeleteMessageAsync` | Выполняет метод `DeleteMessageAsync`, чтобы удалить сообщение с указанным `Id`. |
| `OnPostAnalyzeMessagesAsync` | Если в базе данных содержится одно или несколько сообщений, вычисляет среднее количество слов на сообщение. |

Методы модели страницы проверяются с помощью семи тестов в классе `IndexPageTests` (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). В тестах используется привычный шаблон "размещение-утверждение-действие". Эти тесты выполняют следующие задачи:

* определение правильности поведения методов, если [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) является недопустимым;
* подтверждение того, что методы выдают правильный <xref:Microsoft.AspNetCore.Mvc.IActionResult>;
* проверка правильности назначения значений свойств.

Эта группа тестов часто макетирует методы DAL для получения ожидаемых данных для этапа действия, в котором выполняется метод модели страницы. Например, метод `GetMessagesAsync` класса `AppDbContext` макетируется для получения выходных данных. Когда метод модели страницы выполняет этот метод, макет возвращает результат. Данные не поступают из базы данных. Это создает предсказуемые и надежные тестовые условия для использования DAL в тестовой модели страницы.

Тест `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` показывает, как макетируется метод `GetMessagesAsync` для модели страницы.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Когда метод `OnGetAsync` выполняется на шаге действия, он вызывает метод `GetMessagesAsync` модели страницы.

Шаг действия модульного теста (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

Метод `OnGetAsync` модели страницы `IndexPage` (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Метод `GetMessagesAsync` в DAL не возвращает результат для этого вызова метода. Макетная версия метода возвращает результат.

На шаге `Assert` (Утверждение) фактические сообщения (`actualMessages`) назначаются из свойства `Messages` модели страницы. Проверка типа также выполняется при назначении сообщений. Ожидаемые и фактические сообщения сравниваются по свойствам `Text`. Тест утверждает, что два экземпляра `List<Message>` содержат одни и те же сообщения.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Другие тесты в этой группе создают объекты модели страницы, включающие <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, <xref:Microsoft.AspNetCore.Mvc.ActionContext> для установки `PageContext`, `ViewDataDictionary` и `PageContext`. Они полезны при проведении тестов. Например, приложение для сообщений создает ошибку `ModelState` с <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, чтобы убедиться, что при выполнении `OnPostAddMessageAsync` возвращается допустимый <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Модульное тестирование C# в .NET Core с использованием dotnet test и xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Модульное тестирование кода](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Создание полноценного решения .NET Core на базе macOS с помощью Visual Studio для Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Getting started with xUnit.net: Using .NET Core with the .NET SDK command line](https://xunit.github.io/docs/getting-started-dotnet-core) (Начало работы с xUnit.net. Использование .NET Core с командной строкой пакета SDK для .NET)
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart) (Краткое руководство по Moq)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core поддерживает модульные тесты приложений Razor Pages. Тесты уровня доступа к данным (DAL) и модели страниц помогают обеспечить следующее:

* во время создания приложения Razor Pages его части работают независимо друг от друга и вместе как единое целое;
* классы и методы имеют ограниченные области ответственности;
* существует дополнительная документация о том, как приложение должно себя вести;
* регрессии, которые являются ошибками, вызванными обновлениями кода, обнаруживаются во время автоматической сборки и развертывания.

В этом разделе предполагается, что у вас есть базовое представление о приложениях Razor Pages и модульных тестах. Если вы не знакомы с приложениями Razor Pages или основами тестирования, см. следующие статьи:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Модульное тестирование C# в .NET Core с использованием dotnet test и xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/razor-pages-tests/samples) ([как скачивать](xref:index#how-to-download-a-sample))

Пример проекта состоит из двух приложений:

| Приложение         | Папка проекта                     | Описание |
| ----------- | ---------------------------------- | ----------- |
| Приложение для сообщений | *src/RazorPagesTestSample*         | Позволяет пользователю добавлять сообщение, удалять одно сообщение, удалять все сообщения и анализировать сообщения (найти среднее количество слов на сообщение). |
| Тестирование приложения.    | *tests/RazorPagesTestSample.Tests* | Используется для модульного тестирования модели DAL и индексной страницы приложения для сообщений. |

Тесты можно выполнять с помощью встроенных функций тестирования интегрированной среды разработки, таких как [Visual Studio](/visualstudio/test/unit-test-your-code) или [Visual Studio для Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). При использовании [Visual Studio Code](https://code.visualstudio.com/) или командной строки выполните следующую команду в командной строке в папке *tests/RazorPagesTestSample.Tests*:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Организация приложения для сообщений

Приложение для сообщений — это система сообщений Razor Pages со следующими характеристиками:

* Индексная страница приложения (*Pages/Index.cshtml* и *Pages/Index.cshtml.cs*) предоставляет методы пользовательского интерфейса и модели страницы для управления добавлением, удалением и анализом сообщений (поиск среднего числа слов на сообщение).
* Сообщение описывается классом `Message` (*Data/Message.cs*) с двумя свойствами: `Id` (ключ) и `Text` (сообщение). Свойство `Text` является обязательным и ограничено 200 символами.
* Сообщения хранятся с помощью [базы данных Entity Framework в памяти](/ef/core/providers/in-memory/)&#8224;.
* Приложение содержит слой DAL в своем классе контекста базы данных — `AppDbContext` (*Data/AppDbContext.cs*). Методы DAL помечаются как `virtual`, что позволяет макетирование методов для использования в тестах.
* Если база данных пуста при запуске приложения, то хранилище сообщений инициализируется тремя сообщениями. Эти *начальные сообщения* также используются в тестах.

&#8224;В разделе документации о EF [Тестирование с помощью InMemory](/ef/core/miscellaneous/testing/in-memory) объясняется, как использовать базу данных в памяти для тестов с помощью MSTest. В этом разделе используется платформа тестирования [xUnit](https://xunit.github.io/). Концепции тестирования и реализации тестов в разных платформах тестирования похожи, но не идентичны.

Хотя пример приложения не использует шаблон репозитория и не является эффективным примером [шаблона "единица работы" (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages поддерживает такие шаблоны разработки. Дополнительные сведения см. в разделах [Проектирование уровня сохраняемости инфраструктуры](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) и <xref:mvc/controllers/testing> (пример реализует шаблон репозитория).

## <a name="test-app-organization"></a>Организация приложения для тестирования

Приложение для тестирования — это консольное приложение в папке *tests/RazorPagesTestSample.Tests*.

| Папка приложения для тестирования | Описание |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>Файл *DataAccessLayerTest.cs* содержит модульные тесты для DAL.</li><li>Файл *IndexPageTests.cs* содержит модульные тесты для модели индексной страницы.</li></ul> |
| *Utilities*     | Содержит метод `TestDbContextOptions`, используемый для создания новых параметров контекста базы данных для каждого модульного теста DAL, чтобы база данных сбрасывалась в базовое состояние для каждого теста. |

Используемая платформа тестирования — [xUnit](https://xunit.github.io/). Используемая платформа макетирования объектов — [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Модульные тесты уровня доступа к данным (DAL)

Приложение для сообщений имеет DAL с четырьмя методами, содержащимися в классе `AppDbContext` (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Каждый метод содержит один или два модульных теста в приложении для тестирования.

| Метод DAL               | Функция                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Получает `List<Message>` из базы данных, отсортированной по свойству `Text`. |
| `AddMessageAsync`        | Добавляет `Message` в базу данных.                                          |
| `DeleteAllMessagesAsync` | Удаляет все записи `Message` из базы данных.                           |
| `DeleteMessageAsync`     | Удаляет одну запись `Message` из базы данных по полю `Id`.                      |

Модульные тесты DAL требует <xref:Microsoft.EntityFrameworkCore.DbContextOptions> при создании нового экземпляра `AppDbContext` для каждого теста. Один из подходов к созданию `DbContextOptions` для каждого теста заключается в использовании <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Проблема этого подхода заключается в том, что каждый тест получает базу данных в том состоянии, в котором ее оставил предыдущий тест. Это может быть проблемой при попытке написания атомарных модульных тестов, которые не должны мешать друг другу. Чтобы заставить `AppDbContext` использовать новый контекст базы данных для каждого теста, укажите экземпляр `DbContextOptions`, основанный на новом поставщике служб. В приложении для тестирования показано, как это сделать с помощью метода `TestDbContextOptions` класса `Utilities` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*).

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Использование `DbContextOptions` в модульных тестах DAL позволяет выполнять каждый тест атомарно с использованием нового экземпляра базы данных.

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Каждый метод тестирования в классе `DataAccessLayerTest` (*UnitTests/DataAccessLayerTest.cs*) соответствует аналогичному шаблону "размещение-действие-утверждение".

1. Размещение: база данных настраивается для тестирования и (или) определяется ожидаемый результат.
1. Действие: выполняется тестирование.
1. Утверждение: утверждения выполняются, чтобы определить, является ли результат теста успешным.

Например, метод `DeleteMessageAsync` отвечает за удаление одного сообщения, идентифицируемого по `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*).

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Для этого метода существует два теста. Один тест проверяет, что метод удаляет сообщение, когда оно присутствует в базе данных. Другой метод проверяет, что база данных не изменяется, если не существует `Id` сообщения для удаления. Ниже представлен код метода `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`.

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Сначала метод выполняет этап размещения, на котором выполняется подготовка для этапа действия. Начальные сообщения получаются и сохраняются в `seedMessages`. Начальные сообщения сохраняются в базу данных. Сообщение с `Id`, равным `1`, устанавливается для удаления. После выполнения метода `DeleteMessageAsync` ожидаемый набор сообщений должен содержать все сообщения, кроме одного с `Id`, равным `1`. Переменная `expectedMessages` содержит ожидаемый результат.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Действие метода: выполняется метод `DeleteMessageAsync` с параметром `recId`, равным `1`.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Наконец, метод получает `Messages` из контекста и сравнивает его с `expectedMessages`, проверяя, что эти два значения равны.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Чтобы сравнить, что два `List<Message>` одинаковы.

* Сообщения упорядочиваются по `Id`.
* Пары сообщений сравниваются по свойству `Text`.

Аналогичный метод теста `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` проверяет результат попытки удаления несуществующего сообщения. В этом случае после выполнения метода `DeleteMessageAsync` ожидаемый набор сообщений в базе данных должен быть равным фактическим сообщениям. Содержимое базы данных не должно измениться:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Модульные тесты методов модели страницы

Другой набор модульных тестов отвечает за тестирование методов модели страницы. В приложении для сообщений модели индексных страниц находятся в классе `IndexModel` в файле *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Метод модели страницы | Функция |
| ----------------- | -------- |
| `OnGetAsync` | Получает сообщения из DAL для пользовательского интерфейса с помощью метода `GetMessagesAsync`. |
| `OnPostAddMessageAsync` | Если [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) является допустимым, вызывает метод `AddMessageAsync`, чтобы добавить сообщение в базу данных. |
| `OnPostDeleteAllMessagesAsync` | Вызывает метод `DeleteAllMessagesAsync` для удаления всех сообщений в базе данных. |
| `OnPostDeleteMessageAsync` | Выполняет метод `DeleteMessageAsync`, чтобы удалить сообщение с указанным `Id`. |
| `OnPostAnalyzeMessagesAsync` | Если в базе данных содержится одно или несколько сообщений, вычисляет среднее количество слов на сообщение. |

Методы модели страницы проверяются с помощью семи тестов в классе `IndexPageTests` (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). В тестах используется привычный шаблон "размещение-утверждение-действие". Эти тесты выполняют следующие задачи:

* определение правильности поведения методов, если [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) является недопустимым;
* подтверждение того, что методы выдают правильный <xref:Microsoft.AspNetCore.Mvc.IActionResult>;
* проверка правильности назначения значений свойств.

Эта группа тестов часто макетирует методы DAL для получения ожидаемых данных для этапа действия, в котором выполняется метод модели страницы. Например, метод `GetMessagesAsync` класса `AppDbContext` макетируется для получения выходных данных. Когда метод модели страницы выполняет этот метод, макет возвращает результат. Данные не поступают из базы данных. Это создает предсказуемые и надежные тестовые условия для использования DAL в тестовой модели страницы.

Тест `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` показывает, как макетируется метод `GetMessagesAsync` для модели страницы.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Когда метод `OnGetAsync` выполняется на шаге действия, он вызывает метод `GetMessagesAsync` модели страницы.

Шаг действия модульного теста (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

Метод `OnGetAsync` модели страницы `IndexPage` (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Метод `GetMessagesAsync` в DAL не возвращает результат для этого вызова метода. Макетная версия метода возвращает результат.

На шаге `Assert` (Утверждение) фактические сообщения (`actualMessages`) назначаются из свойства `Messages` модели страницы. Проверка типа также выполняется при назначении сообщений. Ожидаемые и фактические сообщения сравниваются по свойствам `Text`. Тест утверждает, что два экземпляра `List<Message>` содержат одни и те же сообщения.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Другие тесты в этой группе создают объекты модели страницы, включающие <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, <xref:Microsoft.AspNetCore.Mvc.ActionContext> для установки `PageContext`, `ViewDataDictionary` и `PageContext`. Они полезны при проведении тестов. Например, приложение для сообщений создает ошибку `ModelState` с <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, чтобы убедиться, что при выполнении `OnPostAddMessageAsync` возвращается допустимый <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Модульное тестирование C# в .NET Core с использованием dotnet test и xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Модульное тестирование кода](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Создание полноценного решения .NET Core на базе macOS с помощью Visual Studio для Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Getting started with xUnit.net: Using .NET Core with the .NET SDK command line](https://xunit.github.io/docs/getting-started-dotnet-core) (Начало работы с xUnit.net. Использование .NET Core с командной строкой пакета SDK для .NET)
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart) (Краткое руководство по Moq)
* [JustMockLite](https://github.com/telerik/JustMockLite): платформа макетирования для разработчиков на .NET. (*Не поддерживается и не обслуживается Майкрософт.* )

::: moniker-end
