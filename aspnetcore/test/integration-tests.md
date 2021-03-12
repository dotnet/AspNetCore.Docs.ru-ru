---
title: Интеграционные тесты на платформе ASP.NET Core
author: rick-anderson
description: Узнайте, как с помощью интеграционных тестов можно проверить работу компонентов приложения на уровне инфраструктуры, включая базу данных, файловую систему и сеть.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: test/integration-tests
ms.openlocfilehash: 47d204a0cde258fda8c9c9b78043046e719d8f18
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588039"
---
# <a name="integration-tests-in-aspnet-core"></a>Интеграционные тесты на платформе ASP.NET Core

Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Стив Смит](https://ardalis.com/) (Steve Smith) и [Йос ван дер Тиль](https://jvandertil.nl) (Jos van der Til)

::: moniker range=">= aspnetcore-3.0"

С помощью интеграционных тестов можно проверить работу компонентов приложения на уровне, который включает инфраструктуру, поддерживающую приложение, такую как база данных, файловая система и сеть. ASP.NET Core поддерживает интеграционные тесты с помощью платформы модульного тестирования с тестовым веб-узлом и сервером тестирования в памяти.

В этом разделе предполагается базовое понимание модульных тестов. Если вы не знакомы с концепциями тестирования, ознакомьтесь с разделом [Модульное тестирование в .NET Core и .NET Standard](/dotnet/core/testing/) и связанным с ним содержимым.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) ([как скачивать](xref:index#how-to-download-a-sample))

В качестве примера используется приложение Razor Pages (предполагается базовое понимание Razor Pages). Если вы не знакомы с Razor Pages, см. следующие разделы:

* [Введение в Razor Pages](xref:razor-pages/index)
* [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Модульные тесты Razor Pages](xref:test/razor-pages-tests)

> [!NOTE]
> Для тестирования одностраничных приложений мы рекомендуем инструмент типа [Selenium](https://www.seleniumhq.org/), с помощью которого можно автоматизировать браузер.

## <a name="introduction-to-integration-tests"></a>Общие сведения об интеграционных тестах

Интеграционные тесты служат для оценки компонентов приложения на более широком уровне, чем [модульные тесты](/dotnet/core/testing/). Модульные тесты используются для тестирования изолированных программных компонентов, таких как отдельные методы класса. Интеграционные тесты позволяют убедиться, что два или несколько компонентов приложения работают совместно для получения ожидаемого результата, включая, возможно, все компоненты, необходимые для полной обработки запроса.

Эти более широкие тесты используются для тестирования инфраструктуры приложения и всей платформы, зачастую включая следующие компоненты:

* База данных
* Файловая система
* Сетевые устройства
* Конвейер "запрос-ответ"

В модульных тестах вместо компонентов инфраструктуры используются структурные компоненты, известные как *имитации* или *макеты объектов*.

В отличие от модульных тестов, интеграционные тесты:

* Используют фактические компоненты, которые приложение использует в рабочей среде.
* Требуют больше кода и обработки данных.
* Выполняются дольше.

Поэтому используйте интеграционные тесты только для наиболее важных сценариев инфраструктуры. Если поведение можно проверить с помощью модульного теста или интеграционного теста, выбирайте модульный тест.

> [!TIP]
> Не создавайте интеграционные тесты для каждого возможного варианта доступа к базам данных и файловым системам. В скольких бы местах приложение ни взаимодействовало с ними, фокусный набор интеграционных тестов на чтение, запись, обновление и удаление обычно способен адекватно протестировать их компоненты. Используйте модульные тесты для стандартных тестов логики методов, взаимодействующих с этими компонентами. В модульных тестах использование инфраструктурных имитаций приводит к более быстрому выполнению теста.

> [!NOTE]
> При обсуждении интеграционных тестов тестируемый проект часто называется *тестируемой системой* или "ТС".
>
> *В этом разделе используется "ТС" для ссылки на проверенное приложение ASP.NET Core.*

## <a name="aspnet-core-integration-tests"></a>Интеграционные тесты ASP.NET Core

Для интеграционных тестов в ASP.NET Core требуется следующее:

* Тестовый проект, который используется для хранения и выполнения тестов. Тестовый проект содержит ссылку на ТС.
* Тестовый проект создает тестовый веб-узел для ТС и использует клиент тестового сервера для обработки запросов и ответов с помощью ТС.
* Средство запуска тестов используется для выполнения тестов и передачи результатов тестов.

Интеграционные тесты придерживаются последовательности событий из обычных шагов теста *Подготовка*, *Выполнение* и *Проверка*.

1. Настраивается веб-узел ТС.
1. Создается клиент тестового сервера для отправки запросов к приложению.
1. Выполняется шаг теста *Подготовка*: тестовое приложение готовит запрос.
1. Выполняется шаг теста *Выполнение*: клиент отправляет запрос и получает ответ.
1. Выполняется шаг теста *Проверка*: *фактический* ответ определяется как *правильный* или *ошибочный* на основе *ожидаемого* ответа.
1. Процесс продолжается до тех пор, пока не будут выполнены все тесты.
1. Выводятся результаты теста.

Как правило, тестовый веб-узел настраивается отлично от обычного веб-узла приложения для тестовых запусков. Например, для тестов может использоваться другая база данных или другие параметры приложения.

Компоненты инфраструктуры, такие как тестовый веб-узел и сервер тестирования в памяти ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), предоставляются или управляются пакетом [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing). Использование этого пакета упрощает создание и выполнение тестов.

Пакет `Microsoft.AspNetCore.Mvc.Testing` выполняет следующие задачи:

* Копирует файл зависимостей (*DEPS*) из ТС в папку *bin* тестового проекта.
* Задает [корневой каталог](xref:fundamentals/index#content-root) содержимого в корне проекта ТС, чтобы при выполнении тестов были найдены статические файлы и страницы или представления.
* Предоставляет класс [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) для упрощения начальной загрузки ТС с `TestServer`.

В документации [модульные тесты](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) описано, как настроить тестовый проект и средство запуска тестов, а также представлены подробные инструкции по выполнению тестов и рекомендаций по именованию тестов и тестовых классов.

> [!NOTE]
> При создании тестового проекта для приложения отделяйте модульные тесты от интеграционных, помещая их в разные проекты. Это гарантирует, что компоненты тестирования инфраструктуры не будут случайно добавлены в модульные тесты. Разделение модульных и интеграционных тестов также позволяет контролировать, какой набор тестов выполняется.

В сущности, между конфигурацией для тестов приложений Razor Pages и приложений MVC нет никаких отличий. Единственное отличие заключается в том, как именуются тесты. В приложении Razor Pages тесты конечных точек страницы обычно именуются по классу модели страницы (например, `IndexPageTests` для тестирования интеграции компонентов на странице индекса). В приложении MVC тесты обычно организованы по классам контроллеров и именуются по контроллеру, который они проверяют (например, `HomeControllerTests` для тестирования интеграции компонентов на контроллере Home).

## <a name="test-app-prerequisites"></a>Проверка необходимых требований к приложению

Тестовый проект должен выполнять следующие требования.

* Ссылаться на пакет [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing).
* Указывать веб-пакет SDK в файле проекта (`<Project Sdk="Microsoft.NET.Sdk.Web">`).

Выполнение необходимых требований можно посмотреть в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples/). Изучите файл *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*. В примере приложения используются платформа тестирования [xUnit](https://xunit.github.io/) и библиотека средства синтаксического анализа [AngleSharp](https://anglesharp.github.io/), поэтому он также ссылается на:

* [xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

В тестах также используется Entity Framework Core. Ссылки на приложение:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Среда ТС

Если [среда](xref:fundamentals/environments) ТС не задана, то по умолчанию среда имеет значение Development.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Базовые тесты со стандартной WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) используется для создания [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) для интеграционных тестов. `TEntryPoint` — это класс точки входа ТС, обычно класс `Startup`.

Тестовые классы реализуют интерфейс *средства тестирования* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)), чтобы указать, что класс содержит тесты, и предоставить экземпляры общего объекта между тестами в классе.

Следующий тестовый класс, `BasicTests`, использует `WebApplicationFactory` для начальной загрузки ТС и предоставляет [HttpClient](/dotnet/api/system.net.http.httpclient) тестовому методу `Get_EndpointsReturnSuccessAndCorrectContentType`. Метод проверяет, что код состояния ответа свидетельствует о выполнении (коды состояния в диапазоне от 200 до 299) и что заголовок `Content-Type` имеет значение `text/html; charset=utf-8` для нескольких страниц приложения.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) создает экземпляр класса `HttpClient`, который автоматически следует за перенаправлениями и обрабатывает файлы cookie.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

По умолчанию некритические файлы cookie не сохраняются в запросах, если включена [политика согласия GDPR](xref:security/gdpr). Чтобы сохранить ненужные файлы cookie, например те, которые используются поставщиком TempData, пометьте их как основные в тестах. Инструкции по маркировке файла cookie в качестве основы см. в разделе [Основные файлы cookie](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Настройка WebApplicationFactory

Конфигурацию веб-узла можно создать независимо от тестовых классов путем наследования от `WebApplicationFactory` для создания одной или нескольких пользовательских фабрик:

1. Выполните наследование от `WebApplicationFactory` и переопределите [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) позволяет настраивать коллекцию служб с помощью [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Заполнение базы данных в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) выполняется методом `InitializeDbForTests`. Этот метод описан в разделе [Пример интеграционных тестов: организация приложения для тестирования](#test-app-organization).

   Контекст базы данных ТС регистрируется в методе `Startup.ConfigureServices`. Обратный вызов `builder.ConfigureServices` тестового приложения выполняется *после* выполнения кода `Startup.ConfigureServices` приложения. Порядок выполнения является критическим изменением для [универсального узла](xref:fundamentals/host/generic-host) с выпуском ASP.NET Core 3.0. Чтобы использовать для тестов базу данных, отличную от базы данных приложения, необходимо заменить контекст базы данных приложения в `builder.ConfigureServices`.

   В тестируемых системах, которые по-прежнему используют [веб-узел](xref:fundamentals/host/web-host), обратный вызов `builder.ConfigureServices` тестового приложения выполняется *до* выполнения кода `Startup.ConfigureServices` тестируемой системы. Обратный вызов `builder.ConfigureTestServices` тестового приложения выполняется *позже*.

   Пример приложения находит дескриптор службы для контекста базы данных и использует дескриптор для удаления регистрации службы. Затем фабрика добавляет новый `ApplicationDbContext`, который использует базу данных в памяти для тестов.

   Чтобы подключиться к базе данных, отличной от базы данных в памяти, измените вызов `UseInMemoryDatabase`, чтобы подключить контекст к другой базе данных. Чтобы использовать тестовую базу данных SQL Server, выполните следующие действия.

   * Сошлитесь на пакет NuGet [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) в файле проекта.
   * Вызовите `UseSqlServer` со строкой подключения к базе данных.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Используйте настраиваемый `CustomWebApplicationFactory` в тестовых классах. В следующем примере используется фабрика в классе `IndexPageTests`:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Клиент примера приложения настроен так, чтобы не допустить выполнение клиентов `HttpClient` следующих перенаправлений. Как описано далее в разделе [Имитация проверки подлинности](#mock-authentication), это позволяет тестам проверять результат первого ответа приложения. Первый ответ — это перенаправление во многих из этих тестов с заголовком `Location`.

3. Обычный тест использует `HttpClient` и вспомогательные методы для обработки запроса и ответа:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Любой запрос POST к ТС должен соответствовать проверке защиты от подделки, которая автоматически вносится в [систему защиты данных от подделки](xref:security/data-protection/introduction) приложения. Чтобы упорядочить запрос POST теста, тестовое приложение должно:

1. Выполнить запрос к странице.
1. Проанализировать файл cookie для защиты от подделки и запросить маркер проверки из ответа.
1. Выполните запрос POST с файлом cookie для защиты от подделки и запросом маркера проверки на месте.

Вспомогательные методы расширения `SendAsync` (*Helpers/HttpClientExtensions.cs*) и вспомогательный метод `GetDocumentAsync` (*Helpers/HtmlHelpers.cs*) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples/) используют средство синтаксического анализа [AngleSharp](https://anglesharp.github.io/) для обработки защиты от подделки с помощью следующих методов:

* `GetDocumentAsync`. Получает [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) и возвращает `IHtmlDocument`. `GetDocumentAsync` использует фабрику, которая подготавливает *виртуальный ответ* на основе исходного `HttpResponseMessage`. Дополнительные сведения см. в [документации по AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* Методы расширения `SendAsync` для `HttpClient` составляют [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) и вызывают [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) для отправки запросов к ТС. Перегрузки для `SendAsync` принимают HTML-форму (`IHtmlFormElement`) и следующие элементы:
  * Кнопка "Отправить" в форме (`IHtmlElement`)
  * Коллекция значений формы (`IEnumerable<KeyValuePair<string, string>>`)
  * Кнопка "Отправить" (`IHtmlElement`) и значения формы (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) — это сторонняя библиотека анализа, используемая для демонстрационных целей в этом разделе и в примере приложения. AngleSharp не поддерживается интеграционным тестированием приложением ASP.NET Core и не требуется для этого. Можно использовать и другие средства синтаксического анализа, такие как [Html Agility Pack (HAP)](https://html-agility-pack.net/). Другой подход заключается в написании кода для непосредственной работы с маркером проверки запроса системы защиты от подделки и файлом cookie защиты от подделки.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Настройка клиента с помощью WithWebHostBuilder

Если в методе теста требуется дополнительная настройка, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) создает новую `WebApplicationFactory` с [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), которая дополнительно настроена в конфигурации.

Метод теста `Post_DeleteMessageHandler_ReturnsRedirectToRoot` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) демонстрирует использование `WithWebHostBuilder`. Этот тест выполняет удаление записи из базы данных, активируя отправку формы в ТС.

Поскольку другой тест в классе `IndexPageTests` выполняет операцию, которая удаляет все записи из базы данных и может выполняться до метода `Post_DeleteMessageHandler_ReturnsRedirectToRoot`, база данных повторно заполняется в этом методе теста, чтобы обеспечить наличие записи для ее удаления ТС. Выбор первой кнопки удаления в форме `messages` в ТС имитируется в запросе к ТС:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Параметры клиента

В следующей таблице показаны [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) по умолчанию, доступные при создании экземпляров `HttpClient`.

| Параметр | Описание | Значение по умолчанию |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Возвращает или задает, должны ли экземпляры `HttpClient` автоматически следовать ответам перенаправления. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Возвращает или задает базовый адрес экземпляров `HttpClient`. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Возвращает или задает, должны ли экземпляры `HttpClient` обрабатывать файлы cookie. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Возвращает или задает максимальное число ответов на перенаправление, которым должны следовать экземпляры `HttpClient`. | 7 |

Создайте класс `WebApplicationFactoryClientOptions` и передайте его в метод [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (значения по умолчанию показаны в примере кода):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Вставка служб имитации

Службы можно переопределить в тесте с помощью вызова [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) в построителе узлов. **Чтобы внедрить службы имитации, в ТС должен иметься класс `Startup` с методом `Startup.ConfigureServices`.**

Пример ТС включает службу с заданной областью, которая возвращает цитату. Цитата внедряется в скрытое поле на странице индекса при запросе страницы индекса.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

При запуске приложения ТС создается следующая разметка:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Чтобы протестировать службу и внедрение цитат в интеграционный тест, служба имитации будет внедрена в ТС тестом. Служба имитации заменяет `QuoteService` приложения службой, предоставляемой тестовым приложением, с именем `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

Вызывается `ConfigureTestServices` и регистрируется служба с заданной областью:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Разметка, созданная во время выполнения теста, отражает текст цитаты, предоставленный `TestQuoteService`, поэтому утверждение передается следующим образом:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Имитация проверки подлинности

Тесты в классе `AuthTests` проверяют, что безопасная конечная точка:

* перенаправляет пользователя, не прошедшего проверку подлинности, на страницу входа;
* возвращает содержимое для пользователя, прошедшего проверку подлинности.

В ТС на странице `/SecurePage` используется соглашение [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) для применения [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) на странице. Дополнительные сведения см. в разделе [Соглашения проверки подлинности RazorPages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

В тесте `Get_SecurePageRedirectsAnUnauthenticatedUser` параметр [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) настроен на запрет перенаправления путем установки для параметра [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) значения `false`:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Если запретить клиенту следовать перенаправлению, можно выполнить следующие проверки:

* Код состояния, возвращаемый ТС, можно проверить по ожидаемому результату [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode), а не по окончательному коду состояния после перенаправления на страницу входа, который будет [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Значение заголовка `Location` в заголовках ответа проверяется, чтобы подтвердить, что он начинается с `http://localhost/Identity/Account/Login`, а не с последнего ответа страницы входа, где отсутствует заголовок `Location`.

Тестовое приложение может имитировать макет <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> в [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) для тестирования аспектов проверки подлинности и авторизации. В минимальном сценарии возвращается [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Для проверки подлинности пользователя вызывается `TestAuthHandler`, если для схемы проверки подлинности задано `Test`, где `AddAuthentication` зарегистрировано для `ConfigureTestServices`. Важно, чтобы схема `Test` соответствовала схеме, которую ожидает приложение. В противном случае проверка подлинности не будет работать.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Дополнительные сведения о `WebApplicationFactoryClientOptions` см. в разделе [Параметры клиента](#client-options).

## <a name="set-the-environment"></a>Указание среды

По умолчанию узел и среда приложений ТС настроены для использования среды Development. Чтобы переопределить среду ТС при использовании `IHostBuilder`, выполните следующие действия:

* Задайте переменную среды `ASPNETCORE_ENVIRONMENT` (например, `Staging`, `Production` или другое настраиваемое значение, например `Testing`).
* Переопределите `CreateHostBuilder` в тестовом приложении, чтобы считать переменные среды с префиксом `ASPNETCORE`.

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

Если ТС использует веб-узел (`IWebHostBuilder`), переопределите `CreateWebHostBuilder`.

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Определение тестовой инфраструктурой пути к корневому каталогу содержимого приложения

Конструктор `WebApplicationFactory` выводит путь к [корневому каталогу содержимого приложения](xref:fundamentals/index#content-root), выполняя поиск [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) в сборке, содержащей интеграционные тесты с ключом, равным сборке `TEntryPoint` `System.Reflection.Assembly.FullName`. Если атрибут с правильным ключом не найден, `WebApplicationFactory` возвращается к поиску файла решения (*SLN*) и добавляет имя сборки `TEntryPoint` в каталог решения. Корневой каталог приложения (корневой путь к содержимому) используется для обнаружения представлений и файлов содержимого.

## <a name="disable-shadow-copying"></a>Отключение теневого копирования

Теневое копирование приводит к тому, что тесты выполняются в каталоге, отличном от выходного каталога. Для правильной работы тестов необходимо отключить теневое копирование. [Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) использует xUnit и отключает теневое копирование для xUnit, включая файл *xunit.runner.json* с правильным параметром конфигурации. Дополнительные сведения см. в разделе [Настройка xUnit с помощью JSON](https://xunit.github.io/docs/configuring-with-json.html).

Добавьте файл *xunit.runner.json* в корень тестового проекта со следующим содержимым:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Удаление объектов

После выполнения тестов реализации `IClassFixture` [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) и [HttpClient](/dotnet/api/system.net.http.httpclient) удаляются, когда xUnit удаляет [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Если объекты, создаваемые разработчиком, требуется удалить, удалите их в реализации `IClassFixture`. Дополнительные сведения см. в разделе [Реализация метода Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Пример интеграционных тестов

[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) состоит из двух приложений:

| Приложение | каталог проекта; | Описание |
| --- | ----------------- | ----------- |
| Приложение для сообщений (ТС) | *src/RazorPagesProject* | Позволяет пользователю добавлять, удалять сообщения (по одному или все) и анализировать их. |
| Тестирование приложения. | *tests/RazorPagesProject.Tests* | Используется для тестирования интеграции ТС. |

Тесты можно выполнять с помощью встроенных функций тестирования интегрированной среды разработки, таких как [Visual Studio](https://visualstudio.microsoft.com). При использовании [Visual Studio Code](https://code.visualstudio.com/) или командной строки выполните следующую команду в командной строке в каталоге *tests/RazorPagesProject.Tests*:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Организация приложения для сообщений (ТС)

Тестируемая система (ТС) — это система сообщений Razor Pages со следующими характеристиками:

* Страница индекса приложения (*Pages/Index.cshtml* и *Pages/Index.cshtml.cs*) предоставляет методы пользовательского интерфейса и модели страницы для управления добавлением, удалением и анализом сообщений (поиск среднего числа слов на сообщение).
* Сообщение описывается классом `Message` (*Data/Message.cs*) с двумя свойствами: `Id` (ключ) и `Text` (сообщение). Свойство `Text` является обязательным и ограничено 200 символами.
* Сообщения хранятся с помощью [базы данных Entity Framework в памяти](/ef/core/providers/in-memory/)&#8224;.
* Приложение содержит слой доступа к данным DAL в своем классе контекста базы данных — `AppDbContext` (*Data/AppDbContext.cs*).
* Если база данных пуста при запуске приложения, то хранилище сообщений инициализируется тремя сообщениями.
* Приложение включает `/SecurePage`, доступ к которому может получить только пользователь, прошедший проверку подлинности.

&#8224;В разделе документации о EF [Тестирование с помощью InMemory](/ef/core/miscellaneous/testing/in-memory) объясняется, как использовать базу данных в памяти для тестов с помощью MSTest. В этом разделе используется платформа тестирования [xUnit](https://xunit.github.io/). Концепции тестирования и реализации тестов в разных платформах тестирования похожи, но не идентичны.

Несмотря на то, что приложение не использует шаблон репозитория и не является эффективным примером [шаблона "единица работы" (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages поддерживает такие шаблоны разработки. Дополнительные сведения см. в разделах [Проектирование уровня сохраняемости инфраструктуры](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) и [Логика контроллера тестирования](../mvc/controllers/testing.md) (пример реализует шаблон репозитория).

### <a name="test-app-organization"></a>Организация приложения для тестирования

Тестовое приложение — это консольное приложение в папке *tests/RazorPagesProject.Tests*.

| Каталог тестового приложения | Описание |
| ------------------ | ----------- |
| *AuthTests* | Содержит методы теста для:<ul><li>доступа к защищенной странице пользователя, не прошедшего проверку подлинности;</li><li>доступа к защищенной странице пользователя, прошедшего проверку подлинности с помощью макета <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>;</li><li>получения профиля пользователя GitHub и проверки имени входа пользователя профиля.</li></ul> |
| *BasicTests* | Содержит метод теста для маршрутизации и типа содержимого. |
| *IntegrationTests* | Содержит интеграционные тесты для страницы индекса с помощью настраиваемого класса `WebApplicationFactory`. |
| *Helpers/Utilities* | <ul><li>*Utilities.cs* содержит метод `InitializeDbForTests`, используемый для заполнения базы данных тестовыми данными.</li><li>*HtmlHelpers.cs* предоставляет метод, возвращающий `IHtmlDocument` AngleSharp для использования методами теста.</li><li>*HttpClientExtensions.cs* предоставляет перегрузки для `SendAsync` для отправки запросов к ТС.</li></ul> |

Используемая платформа тестирования — [xUnit](https://xunit.github.io/). Интеграционные тесты выполняются с помощью [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), который включает [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Поскольку пакет [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) используется для настройки узла тестирования и тестового сервера, для пакетов `TestHost` и `TestServer` не требуются прямые ссылки на пакеты в файле проекта тестового приложения или конфигурации разработчика в тестовом приложении.

**Заполнение базы данных для тестирования**

Перед выполнением интеграционных тестов обычно требуется небольшой набор данных в базе данных. Например, при удалении теста вызывается удаление записей базы данных, поэтому в базе данных должна иметься по крайней мере одна запись, чтобы запрос на удаление был выполнен успешно.

Пример приложения заполняет базу данных тремя сообщениями в *Utilities.cs*, которые могут использоваться тестами при выполнении:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Контекст базы данных ТС регистрируется в методе `Startup.ConfigureServices`. Обратный вызов `builder.ConfigureServices` тестового приложения выполняется *после* выполнения кода `Startup.ConfigureServices` приложения. Чтобы использовать для тестов другую базу данных, необходимо заменить контекст базы данных приложения в `builder.ConfigureServices`. Дополнительные сведения см. в разделе [Настройка WebApplicationFactory](#customize-webapplicationfactory).

В тестируемых системах, которые по-прежнему используют [веб-узел](xref:fundamentals/host/web-host), обратный вызов `builder.ConfigureServices` тестового приложения выполняется *до* выполнения кода `Startup.ConfigureServices` тестируемой системы. Обратный вызов `builder.ConfigureTestServices` тестового приложения выполняется *позже*.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

С помощью интеграционных тестов можно проверить работу компонентов приложения на уровне, который включает инфраструктуру, поддерживающую приложение, такую как база данных, файловая система и сеть. ASP.NET Core поддерживает интеграционные тесты с помощью платформы модульного тестирования с тестовым веб-узлом и сервером тестирования в памяти.

В этом разделе предполагается базовое понимание модульных тестов. Если вы не знакомы с концепциями тестирования, ознакомьтесь с разделом [Модульное тестирование в .NET Core и .NET Standard](/dotnet/core/testing/) и связанным с ним содержимым.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) ([как скачивать](xref:index#how-to-download-a-sample))

В качестве примера используется приложение Razor Pages (предполагается базовое понимание Razor Pages). Если вы не знакомы с Razor Pages, см. следующие разделы:

* [Введение в Razor Pages](xref:razor-pages/index)
* [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Модульные тесты Razor Pages](xref:test/razor-pages-tests)

> [!NOTE]
> Для тестирования одностраничных приложений мы рекомендуем инструмент типа [Selenium](https://www.seleniumhq.org/), с помощью которого можно автоматизировать браузер.

## <a name="introduction-to-integration-tests"></a>Общие сведения об интеграционных тестах

Интеграционные тесты служат для оценки компонентов приложения на более широком уровне, чем [модульные тесты](/dotnet/core/testing/). Модульные тесты используются для тестирования изолированных программных компонентов, таких как отдельные методы класса. Интеграционные тесты позволяют убедиться, что два или несколько компонентов приложения работают совместно для получения ожидаемого результата, включая, возможно, все компоненты, необходимые для полной обработки запроса.

Эти более широкие тесты используются для тестирования инфраструктуры приложения и всей платформы, зачастую включая следующие компоненты:

* База данных
* Файловая система
* Сетевые устройства
* Конвейер "запрос-ответ"

В модульных тестах вместо компонентов инфраструктуры используются структурные компоненты, известные как *имитации* или *макеты объектов*.

В отличие от модульных тестов, интеграционные тесты:

* Используют фактические компоненты, которые приложение использует в рабочей среде.
* Требуют больше кода и обработки данных.
* Выполняются дольше.

Поэтому используйте интеграционные тесты только для наиболее важных сценариев инфраструктуры. Если поведение можно проверить с помощью модульного теста или интеграционного теста, выбирайте модульный тест.

> [!TIP]
> Не создавайте интеграционные тесты для каждого возможного варианта доступа к базам данных и файловым системам. В скольких бы местах приложение ни взаимодействовало с ними, фокусный набор интеграционных тестов на чтение, запись, обновление и удаление обычно способен адекватно протестировать их компоненты. Используйте модульные тесты для стандартных тестов логики методов, взаимодействующих с этими компонентами. В модульных тестах использование инфраструктурных имитаций приводит к более быстрому выполнению теста.

> [!NOTE]
> При обсуждении интеграционных тестов тестируемый проект часто называется *тестируемой системой* или "ТС".
>
> *В этом разделе используется "ТС" для ссылки на проверенное приложение ASP.NET Core.*

## <a name="aspnet-core-integration-tests"></a>Интеграционные тесты ASP.NET Core

Для интеграционных тестов в ASP.NET Core требуется следующее:

* Тестовый проект, который используется для хранения и выполнения тестов. Тестовый проект содержит ссылку на ТС.
* Тестовый проект создает тестовый веб-узел для ТС и использует клиент тестового сервера для обработки запросов и ответов с помощью ТС.
* Средство запуска тестов используется для выполнения тестов и передачи результатов тестов.

Интеграционные тесты придерживаются последовательности событий из обычных шагов теста *Подготовка*, *Выполнение* и *Проверка*.

1. Настраивается веб-узел ТС.
1. Создается клиент тестового сервера для отправки запросов к приложению.
1. Выполняется шаг теста *Подготовка*: тестовое приложение готовит запрос.
1. Выполняется шаг теста *Выполнение*: клиент отправляет запрос и получает ответ.
1. Выполняется шаг теста *Проверка*: *фактический* ответ определяется как *правильный* или *ошибочный* на основе *ожидаемого* ответа.
1. Процесс продолжается до тех пор, пока не будут выполнены все тесты.
1. Выводятся результаты теста.

Как правило, тестовый веб-узел настраивается отлично от обычного веб-узла приложения для тестовых запусков. Например, для тестов может использоваться другая база данных или другие параметры приложения.

Компоненты инфраструктуры, такие как тестовый веб-узел и сервер тестирования в памяти ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), предоставляются или управляются пакетом [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing). Использование этого пакета упрощает создание и выполнение тестов.

Пакет `Microsoft.AspNetCore.Mvc.Testing` выполняет следующие задачи:

* Копирует файл зависимостей (*DEPS*) из ТС в папку *bin* тестового проекта.
* Задает [корневой каталог](xref:fundamentals/index#content-root) содержимого в корне проекта ТС, чтобы при выполнении тестов были найдены статические файлы и страницы или представления.
* Предоставляет класс [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) для упрощения начальной загрузки ТС с `TestServer`.

В документации [модульные тесты](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) описано, как настроить тестовый проект и средство запуска тестов, а также представлены подробные инструкции по выполнению тестов и рекомендаций по именованию тестов и тестовых классов.

> [!NOTE]
> При создании тестового проекта для приложения отделяйте модульные тесты от интеграционных, помещая их в разные проекты. Это гарантирует, что компоненты тестирования инфраструктуры не будут случайно добавлены в модульные тесты. Разделение модульных и интеграционных тестов также позволяет контролировать, какой набор тестов выполняется.

В сущности, между конфигурацией для тестов приложений Razor Pages и приложений MVC нет никаких отличий. Единственное отличие заключается в том, как именуются тесты. В приложении Razor Pages тесты конечных точек страницы обычно именуются по классу модели страницы (например, `IndexPageTests` для тестирования интеграции компонентов на странице индекса). В приложении MVC тесты обычно организованы по классам контроллеров и именуются по контроллеру, который они проверяют (например, `HomeControllerTests` для тестирования интеграции компонентов на контроллере Home).

## <a name="test-app-prerequisites"></a>Проверка необходимых требований к приложению

Тестовый проект должен выполнять следующие требования.

* Ссылаться на следующие пакеты:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Указывать веб-пакет SDK в файле проекта (`<Project Sdk="Microsoft.NET.Sdk.Web">`). Веб-пакет SDK требуется при ссылке на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Выполнение необходимых требований можно посмотреть в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples/). Изучите файл *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*. В примере приложения используются платформа тестирования [xUnit](https://xunit.github.io/) и библиотека средства синтаксического анализа [AngleSharp](https://anglesharp.github.io/), поэтому он также ссылается на:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Среда ТС

Если [среда](xref:fundamentals/environments) ТС не задана, то по умолчанию среда имеет значение Development.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Базовые тесты со стандартной WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) используется для создания [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) для интеграционных тестов. `TEntryPoint` — это класс точки входа ТС, обычно класс `Startup`.

Тестовые классы реализуют интерфейс *средства тестирования* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)), чтобы указать, что класс содержит тесты, и предоставить экземпляры общего объекта между тестами в классе.

Следующий тестовый класс, `BasicTests`, использует `WebApplicationFactory` для начальной загрузки ТС и предоставляет [HttpClient](/dotnet/api/system.net.http.httpclient) тестовому методу `Get_EndpointsReturnSuccessAndCorrectContentType`. Метод проверяет, что код состояния ответа свидетельствует о выполнении (коды состояния в диапазоне от 200 до 299) и что заголовок `Content-Type` имеет значение `text/html; charset=utf-8` для нескольких страниц приложения.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) создает экземпляр класса `HttpClient`, который автоматически следует за перенаправлениями и обрабатывает файлы cookie.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

По умолчанию некритические файлы cookie не сохраняются в запросах, если включена [политика согласия GDPR](xref:security/gdpr). Чтобы сохранить ненужные файлы cookie, например те, которые используются поставщиком TempData, пометьте их как основные в тестах. Инструкции по маркировке файла cookie в качестве основы см. в разделе [Основные файлы cookie](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Настройка WebApplicationFactory

Конфигурацию веб-узла можно создать независимо от тестовых классов путем наследования от `WebApplicationFactory` для создания одной или нескольких пользовательских фабрик:

1. Выполните наследование от `WebApplicationFactory` и переопределите [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) позволяет настраивать коллекцию служб с помощью команды [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), которая выполняется перед командой `Startup.ConfigureServices` приложения. [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) позволяет переопределять и изменять зарегистрированные службы в коллекции служб с помощью команды [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices).

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Заполнение базы данных в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) выполняется методом `InitializeDbForTests`. Этот метод описан в разделе [Пример интеграционных тестов: организация приложения для тестирования](#test-app-organization).

2. Используйте настраиваемый `CustomWebApplicationFactory` в тестовых классах. В следующем примере используется фабрика в классе `IndexPageTests`:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Клиент примера приложения настроен так, чтобы не допустить выполнение клиентов `HttpClient` следующих перенаправлений. Как описано далее в разделе [Имитация проверки подлинности](#mock-authentication), это позволяет тестам проверять результат первого ответа приложения. Первый ответ — это перенаправление во многих из этих тестов с заголовком `Location`.

3. Обычный тест использует `HttpClient` и вспомогательные методы для обработки запроса и ответа:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Любой запрос POST к ТС должен соответствовать проверке защиты от подделки, которая автоматически вносится в [систему защиты данных от подделки](xref:security/data-protection/introduction) приложения. Чтобы упорядочить запрос POST теста, тестовое приложение должно:

1. Выполнить запрос к странице.
1. Проанализировать файл cookie для защиты от подделки и запросить маркер проверки из ответа.
1. Выполните запрос POST с файлом cookie для защиты от подделки и запросом маркера проверки на месте.

Вспомогательные методы расширения `SendAsync` (*Helpers/HttpClientExtensions.cs*) и вспомогательный метод `GetDocumentAsync` (*Helpers/HtmlHelpers.cs*) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples/) используют средство синтаксического анализа [AngleSharp](https://anglesharp.github.io/) для обработки защиты от подделки с помощью следующих методов:

* `GetDocumentAsync`. Получает [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) и возвращает `IHtmlDocument`. `GetDocumentAsync` использует фабрику, которая подготавливает *виртуальный ответ* на основе исходного `HttpResponseMessage`. Дополнительные сведения см. в [документации по AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* Методы расширения `SendAsync` для `HttpClient` составляют [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) и вызывают [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) для отправки запросов к ТС. Перегрузки для `SendAsync` принимают HTML-форму (`IHtmlFormElement`) и следующие элементы:
  * Кнопка "Отправить" в форме (`IHtmlElement`)
  * Коллекция значений формы (`IEnumerable<KeyValuePair<string, string>>`)
  * Кнопка "Отправить" (`IHtmlElement`) и значения формы (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) — это сторонняя библиотека анализа, используемая для демонстрационных целей в этом разделе и в примере приложения. AngleSharp не поддерживается интеграционным тестированием приложением ASP.NET Core и не требуется для этого. Можно использовать и другие средства синтаксического анализа, такие как [Html Agility Pack (HAP)](https://html-agility-pack.net/). Другой подход заключается в написании кода для непосредственной работы с маркером проверки запроса системы защиты от подделки и файлом cookie защиты от подделки.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Настройка клиента с помощью WithWebHostBuilder

Если в методе теста требуется дополнительная настройка, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) создает новую `WebApplicationFactory` с [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), которая дополнительно настроена в конфигурации.

Метод теста `Post_DeleteMessageHandler_ReturnsRedirectToRoot` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) демонстрирует использование `WithWebHostBuilder`. Этот тест выполняет удаление записи из базы данных, активируя отправку формы в ТС.

Поскольку другой тест в классе `IndexPageTests` выполняет операцию, которая удаляет все записи из базы данных и может выполняться до метода `Post_DeleteMessageHandler_ReturnsRedirectToRoot`, база данных повторно заполняется в этом методе теста, чтобы обеспечить наличие записи для ее удаления ТС. Выбор первой кнопки удаления в форме `messages` в ТС имитируется в запросе к ТС:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Параметры клиента

В следующей таблице показаны [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) по умолчанию, доступные при создании экземпляров `HttpClient`.

| Параметр | Описание | Значение по умолчанию |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Возвращает или задает, должны ли экземпляры `HttpClient` автоматически следовать ответам перенаправления. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Возвращает или задает базовый адрес экземпляров `HttpClient`. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Возвращает или задает, должны ли экземпляры `HttpClient` обрабатывать файлы cookie. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Возвращает или задает максимальное число ответов на перенаправление, которым должны следовать экземпляры `HttpClient`. | 7 |

Создайте класс `WebApplicationFactoryClientOptions` и передайте его в метод [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (значения по умолчанию показаны в примере кода):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Вставка служб имитации

Службы можно переопределить в тесте с помощью вызова [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) в построителе узлов. **Чтобы внедрить службы имитации, в ТС должен иметься класс `Startup` с методом `Startup.ConfigureServices`.**

Пример ТС включает службу с заданной областью, которая возвращает цитату. Цитата внедряется в скрытое поле на странице индекса при запросе страницы индекса.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

При запуске приложения ТС создается следующая разметка:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Чтобы протестировать службу и внедрение цитат в интеграционный тест, служба имитации будет внедрена в ТС тестом. Служба имитации заменяет `QuoteService` приложения службой, предоставляемой тестовым приложением, с именем `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

Вызывается `ConfigureTestServices` и регистрируется служба с заданной областью:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Разметка, созданная во время выполнения теста, отражает текст цитаты, предоставленный `TestQuoteService`, поэтому утверждение передается следующим образом:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Имитация проверки подлинности

Тесты в классе `AuthTests` проверяют, что безопасная конечная точка:

* перенаправляет пользователя, не прошедшего проверку подлинности, на страницу входа;
* возвращает содержимое для пользователя, прошедшего проверку подлинности.

В ТС на странице `/SecurePage` используется соглашение [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) для применения [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) на странице. Дополнительные сведения см. в разделе [Соглашения проверки подлинности RazorPages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

В тесте `Get_SecurePageRedirectsAnUnauthenticatedUser` параметр [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) настроен на запрет перенаправления путем установки для параметра [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) значения `false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Если запретить клиенту следовать перенаправлению, можно выполнить следующие проверки:

* Код состояния, возвращаемый ТС, можно проверить по ожидаемому результату [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode), а не по окончательному коду состояния после перенаправления на страницу входа, который будет [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Значение заголовка `Location` в заголовках ответа проверяется, чтобы подтвердить, что он начинается с `http://localhost/Identity/Account/Login`, а не с последнего ответа страницы входа, где отсутствует заголовок `Location`.

Тестовое приложение может имитировать макет <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> в [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) для тестирования аспектов проверки подлинности и авторизации. В минимальном сценарии возвращается [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Для проверки подлинности пользователя вызывается `TestAuthHandler`, если для схемы проверки подлинности задано `Test`, где `AddAuthentication` зарегистрировано для `ConfigureTestServices`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Дополнительные сведения о `WebApplicationFactoryClientOptions` см. в разделе [Параметры клиента](#client-options).

## <a name="set-the-environment"></a>Указание среды

По умолчанию узел и среда приложений ТС настроены для использования среды Development. Чтобы переопределить среду ТС, выполните следующие действия.

* Задайте переменную среды `ASPNETCORE_ENVIRONMENT` (например, `Staging`, `Production` или другое настраиваемое значение, например `Testing`).
* Переопределите `CreateWebHostBuilder` в тестовом приложении, чтобы считать переменные среды `ASPNETCORE_ENVIRONMENT`.

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

Среду также можно задать непосредственно в построителе узлов в пользовательском классе <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>.

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Определение тестовой инфраструктурой пути к корневому каталогу содержимого приложения

Конструктор `WebApplicationFactory` выводит путь к [корневому каталогу содержимого приложения](xref:fundamentals/index#content-root), выполняя поиск [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) в сборке, содержащей интеграционные тесты с ключом, равным сборке `TEntryPoint` `System.Reflection.Assembly.FullName`. Если атрибут с правильным ключом не найден, `WebApplicationFactory` возвращается к поиску файла решения (*SLN*) и добавляет имя сборки `TEntryPoint` в каталог решения. Корневой каталог приложения (корневой путь к содержимому) используется для обнаружения представлений и файлов содержимого.

## <a name="disable-shadow-copying"></a>Отключение теневого копирования

Теневое копирование приводит к тому, что тесты выполняются в каталоге, отличном от выходного каталога. Для правильной работы тестов необходимо отключить теневое копирование. [Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) использует xUnit и отключает теневое копирование для xUnit, включая файл *xunit.runner.json* с правильным параметром конфигурации. Дополнительные сведения см. в разделе [Настройка xUnit с помощью JSON](https://xunit.github.io/docs/configuring-with-json.html).

Добавьте файл *xunit.runner.json* в корень тестового проекта со следующим содержимым:

```json
{
  "shadowCopy": false
}
```

Если используется Microsoft Visual Studio, установите для свойства **Копировать в выходной каталог** значение **Всегда копировать**. Если вы не используете Microsoft Visual Studio, добавьте в файл проекта тестового приложения целевой объект `Content`:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Удаление объектов

После выполнения тестов реализации `IClassFixture` [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) и [HttpClient](/dotnet/api/system.net.http.httpclient) удаляются, когда xUnit удаляет [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Если объекты, создаваемые разработчиком, требуется удалить, удалите их в реализации `IClassFixture`. Дополнительные сведения см. в разделе [Реализация метода Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Пример интеграционных тестов

[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/integration-tests/samples) состоит из двух приложений:

| Приложение | каталог проекта; | Описание |
| --- | ----------------- | ----------- |
| Приложение для сообщений (ТС) | *src/RazorPagesProject* | Позволяет пользователю добавлять, удалять сообщения (по одному или все) и анализировать их. |
| Тестирование приложения. | *tests/RazorPagesProject.Tests* | Используется для тестирования интеграции ТС. |

Тесты можно выполнять с помощью встроенных функций тестирования интегрированной среды разработки, таких как [Visual Studio](https://visualstudio.microsoft.com). При использовании [Visual Studio Code](https://code.visualstudio.com/) или командной строки выполните следующую команду в командной строке в каталоге *tests/RazorPagesProject.Tests*:

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Организация приложения для сообщений (ТС)

Тестируемая система (ТС) — это система сообщений Razor Pages со следующими характеристиками:

* Страница индекса приложения (*Pages/Index.cshtml* и *Pages/Index.cshtml.cs*) предоставляет методы пользовательского интерфейса и модели страницы для управления добавлением, удалением и анализом сообщений (поиск среднего числа слов на сообщение).
* Сообщение описывается классом `Message` (*Data/Message.cs*) с двумя свойствами: `Id` (ключ) и `Text` (сообщение). Свойство `Text` является обязательным и ограничено 200 символами.
* Сообщения хранятся с помощью [базы данных Entity Framework в памяти](/ef/core/providers/in-memory/)&#8224;.
* Приложение содержит слой доступа к данным DAL в своем классе контекста базы данных — `AppDbContext` (*Data/AppDbContext.cs*).
* Если база данных пуста при запуске приложения, то хранилище сообщений инициализируется тремя сообщениями.
* Приложение включает `/SecurePage`, доступ к которому может получить только пользователь, прошедший проверку подлинности.

&#8224;В разделе документации о EF [Тестирование с помощью InMemory](/ef/core/miscellaneous/testing/in-memory) объясняется, как использовать базу данных в памяти для тестов с помощью MSTest. В этом разделе используется платформа тестирования [xUnit](https://xunit.github.io/). Концепции тестирования и реализации тестов в разных платформах тестирования похожи, но не идентичны.

Несмотря на то, что приложение не использует шаблон репозитория и не является эффективным примером [шаблона "единица работы" (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages поддерживает такие шаблоны разработки. Дополнительные сведения см. в разделах [Проектирование уровня сохраняемости инфраструктуры](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) и [Логика контроллера тестирования](../mvc/controllers/testing.md) (пример реализует шаблон репозитория).

### <a name="test-app-organization"></a>Организация приложения для тестирования

Тестовое приложение — это консольное приложение в папке *tests/RazorPagesProject.Tests*.

| Каталог тестового приложения | Описание |
| ------------------ | ----------- |
| *AuthTests* | Содержит методы теста для:<ul><li>доступа к защищенной странице пользователя, не прошедшего проверку подлинности;</li><li>доступа к защищенной странице пользователя, прошедшего проверку подлинности с помощью макета <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>;</li><li>получения профиля пользователя GitHub и проверки имени входа пользователя профиля.</li></ul> |
| *BasicTests* | Содержит метод теста для маршрутизации и типа содержимого. |
| *IntegrationTests* | Содержит интеграционные тесты для страницы индекса с помощью настраиваемого класса `WebApplicationFactory`. |
| *Helpers/Utilities* | <ul><li>*Utilities.cs* содержит метод `InitializeDbForTests`, используемый для заполнения базы данных тестовыми данными.</li><li>*HtmlHelpers.cs* предоставляет метод, возвращающий `IHtmlDocument` AngleSharp для использования методами теста.</li><li>*HttpClientExtensions.cs* предоставляет перегрузки для `SendAsync` для отправки запросов к ТС.</li></ul> |

Используемая платформа тестирования — [xUnit](https://xunit.github.io/). Интеграционные тесты выполняются с помощью [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), который включает [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Поскольку пакет [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) используется для настройки узла тестирования и тестового сервера, для пакетов `TestHost` и `TestServer` не требуются прямые ссылки на пакеты в файле проекта тестового приложения или конфигурации разработчика в тестовом приложении.

**Заполнение базы данных для тестирования**

Перед выполнением интеграционных тестов обычно требуется небольшой набор данных в базе данных. Например, при удалении теста вызывается удаление записей базы данных, поэтому в базе данных должна иметься по крайней мере одна запись, чтобы запрос на удаление был выполнен успешно.

Пример приложения заполняет базу данных тремя сообщениями в *Utilities.cs*, которые могут использоваться тестами при выполнении:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Модульные тесты](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>