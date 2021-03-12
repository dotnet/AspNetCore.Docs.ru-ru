---
title: Внедрение зависимостей в ASP.NET Core
author: rick-anderson
description: Сведения о том, как ASP.NET Core реализует внедрение зависимостей и как его использовать.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ce6804a58c5b17b57732713acc3aeca15042da0a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589716"
---
# <a name="dependency-injection-in-aspnet-core"></a>Внедрение зависимостей в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Авторы: [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin), [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)

ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.

Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.

Дополнительные сведения об использовании внедрения зависимостей в приложениях (кроме веб-приложений) см. в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).

Дополнительные сведения о внедрении параметров зависимостей см. в разделе <xref:fundamentals/configuration/options>.

В этой статье приводятся сведения о внедрении зависимостей в ASP.NET Core. Основная документация по использованию внедрения зависимостей указана в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Общие сведения о внедрении зависимостей

*Зависимость* — это любой объект, от которого зависит другой объект. Рассмотрим следующий класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы:

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

Класс может создать экземпляр класса `MyDependency`, чтобы использовать его метод `WriteMessage`. В следующем примере класс `MyDependency` выступает зависимостью класса `IndexModel`:

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

Этот класс создает `MyDependency` и напрямую зависит от этого класса. Зависимости в коде, как в предыдущем примере, представляют собой определенную проблему. Их следует избегать по следующим причинам.

* Чтобы заменить `MyDependency` другой реализацией, класс `IndexModel` необходимо изменить.
* Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс `IndexModel`. В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.
* Такая реализация плохо подходит для модульных тестов. В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.

Внедрение зависимостей устраняет эти проблемы следующим образом:

* Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.
* Зависимость регистрируется в контейнере служб. ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>. Как правило, службы регистрируются в приложении в методе `Startup.ConfigureServices`.
* Служба *внедряется* в конструктор класса там, где он используется. Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.

В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод `WriteMessage`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Этот интерфейс реализуется конкретным типом, `MyDependency`.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

Пример приложения регистрирует службу `IMyDependency` с конкретным типом `MyDependency`. Метод <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> регистрирует службу с заданной областью времени существования, временем существования одного запроса. Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

В примере приложения запрашивается служба `IMyDependency`, которая затем используется для вызова метода `WriteMessage`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

Используя шаблон внедрения зависимостей, контроллер:

* не использует конкретный тип `MyDependency`, только интерфейс `IMyDependency`, который он реализует. Это упрощает изменение реализации, используемой контроллером, без изменения контроллера.
* не создает экземпляр `MyDependency`, он создается контейнером внедрения зависимостей.

Реализацию интерфейса `IMyDependency` можно улучшить с помощью встроенного API ведения журнала:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

Обновленный метод `ConfigureServices` регистрирует новую реализацию `IMyDependency`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

`MyDependency2` зависит от <xref:Microsoft.Extensions.Logging.ILogger%601>, который запрашивается в конструкторе. `ILogger<TCategoryName>` — это [предоставленная платформой служба](#framework-provided-services).

Использование цепочки внедрений зависимостей не является чем-то необычным. Каждая запрашиваемая зависимость запрашивает собственные зависимости. Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу. Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.

Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).

В терминологии внедрения зависимостей — служба:

* Обычно является объектом, предоставляющим службу для других объектов, например службу `IMyDependency`.
* Не относится к веб-службе, хотя служба может использовать веб-службу.

Платформа предоставляет эффективную систему [ведения журнала](xref:fundamentals/logging/index). Реализации `IMyDependency`, приведенные в предыдущем примере были написаны для демонстрации базового внедрения зависимостей, а не для реализации ведения журнала. Большинству приложений не нужно писать средства ведения журнала. В следующем коде показано использование журнала по умолчанию, для которого не требуется регистрация служб в `ConfigureServices`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

Используя приведенный выше код, не нужно обновлять `ConfigureServices`, поскольку платформа предоставляет возможность [ведения журнала](xref:fundamentals/logging/index).

## <a name="services-injected-into-startup"></a>Службы, внедренные в конструктор Startup

Службы можно внедрить в конструктор `Startup` и метод `Startup.Configure`.

При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие службы:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Любая служба, зарегистрированная в контейнере внедрения зависимостей, может быть внедрена в метод `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

Дополнительные сведения см. в разделах <xref:fundamentals/startup> и [Доступ к конфигурации во время запуска](xref:fundamentals/configuration/index#access-configuration-in-startup).

## <a name="register-groups-of-services-with-extension-methods"></a>Регистрация групп служб с помощью методов расширения

Для регистрации группы связанных служб на платформе ASP.NET Core используется соглашение. Соглашение заключается в использовании одного метода расширения `Add{GROUP_NAME}` для регистрации всех служб, необходимых компоненту платформы. Например, метод расширения <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> регистрирует службы, необходимые контроллерам MVC.

Следующий код создается шаблоном Razor Pages с использованием отдельных учетных записей пользователей. Он демонстрирует, как добавить дополнительные службы в контейнер с помощью методов расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a>Время существования служб

См. раздел [Время существования службы](/dotnet/core/extensions/dependency-injection#service-lifetimes) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).

Используйте службы с заданной областью в ПО промежуточного слоя, применяя один из следующих подходов:

* Внедрите службу в метод `Invoke` или `InvokeAsync` ПО промежуточного слоя. С помощью [внедрите конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) создается исключение времени выполнения, поскольку оно заставляет службу с заданной областью вести себя как одноэлементный объект. В примере в разделе [Параметры времени существования и регистрации](#lifetime-and-registration-options) демонстрируется подход `InvokeAsync`.
* Используйте [фабричное ПО промежуточного слоя](xref:fundamentals/middleware/extensibility). ПО промежуточного слоя, зарегистрированное с использованием этого подхода, активируется при каждом клиентском запросе (подключении), что позволяет внедрять службы с заданной областью в метод `InvokeAsync` ПО промежуточного слоя.

Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

## <a name="service-registration-methods"></a>Методы регистрации службы

См. раздел [Методы регистрации службы](/dotnet/core/extensions/dependency-injection#service-registration-methods) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).

 Распространенный сценарий для использования нескольких реализаций — [макетирование типов для тестирования](xref:test/integration-tests#inject-mock-services).

Регистрация службы только с типом реализации эквивалентна регистрации этой службы с той же реализацией и типом службы. Именно поэтому несколько реализаций службы не могут быть зарегистрированы с помощью методов, которые не принимают явный тип службы. Эти методы могут регистрировать несколько *экземпляров* службы, но все они будут иметь одинаковую *реализацию* типа.

Любой из указанных выше методов регистрации службы можно использовать для регистрации нескольких экземпляров службы одного типа службы. В следующем примере метод `AddSingleton` вызывается дважды с типом службы `IMyDependency`. Второй вызов `AddSingleton` переопределяет предыдущий, если он разрешается как `IMyDependency`, и добавляет к предыдущему, если несколько служб разрешаются через `IEnumerable<IMyDependency>`. Службы отображаются в том порядке, в котором они были зарегистрированы при разрешении через `IEnumerable<{SERVICE}>`.

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a>Поведение внедрения через конструктор

См. раздел [Поведение при внедрении конструктора](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).

## <a name="entity-framework-contexts"></a>Контексты Entity Framework

По умолчанию контексты Entity Framework добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса. Чтобы использовать другое время существования, укажите его с помощью перегрузки <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>. Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.

## <a name="lifetime-and-registration-options"></a>Параметры времени существования и регистрации

Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации службы, рассмотрим интерфейсы, представляющие задачу в виде операции с идентификатором `OperationId`. В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Следующий класс `Operation` реализует все предыдущие интерфейсы. Конструктор `Operation` создает идентификатор GUID и сохраняет последние 4 символа в свойстве `OperationId`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Метод `Startup.ConfigureServices` создает несколько регистраций класса `Operation` в соответствии с именованным временем существования:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

В примере приложения показано время существования объектов в пределах запросов и между запросами. `IndexModel` и ПО промежуточного слоя запрашивают каждый тип `IOperation` и регистрируют `OperationId` для каждого из них:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

Аналогично `IndexModel`, ПО промежуточного слоя и разрешает те же службы:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

Службы с заданной областью должны быть разрешены в методе `InvokeAsync`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

Выходные данные средства ведения журнала содержат:

* *Временные* объекты всегда разные. Значение временного `OperationId` отличается в `IndexModel` и ПО промежуточного слоя.
* Объекты с *заданной областью* остаются неизменными в пределах одного запроса, но в разных запросах используются разные объекты.
* *Одноэлементные* объекты одинаковы для каждого запроса.

Чтобы уменьшить объем выводимых данных журнала, задайте в файле *appsettings.Development.json* параметр "Logging:LogLevel:Microsoft:Error".

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a>Вызов служб из функции main

Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) для разрешения службы с заданной областью в области приложения. Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.

В следующем примере показано, как получить доступ к службе `IMyDependency` с заданной областью и вызвать ее метод `WriteMessage` в `Program.Main`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a>Проверка области

См. раздел [Поведение при внедрении конструктора](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).

Дополнительные сведения см. в разделе [Проверка области](xref:fundamentals/host/web-host#scope-validation).

## <a name="request-services"></a>Службы запросов

Службы, доступные в пределах запроса ASP.NET Core, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices). При запросе в рамках запроса службы и их зависимости разрешаются из коллекции `RequestServices`.

Платформа создает область для каждого запроса, а `RequestServices` предоставляет поставщик услуг с заданной областью. Все службы с заданной областью действительны до тех пор, пока запрос активен.

> [!NOTE]
> Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не разрешать службы из коллекции `RequestServices`. В результате создаются классы, которые легче тестировать.

## <a name="design-services-for-dependency-injection"></a>Проектирование служб для внедрения зависимостей

При разработке служб для внедрения зависимостей придерживайтесь следующих рекомендаций:

* Избегайте статических классов и членов с отслеживанием состояния. Избегайте создания глобального состояния. Для этого проектируйте приложения для использования отдельных служб.
* Избегайте прямого создания экземпляров зависимых классов внутри служб. Прямое создание экземпляров обязывает использовать в коде определенную реализацию.
* Сделайте службы приложения небольшими, хорошо организованными и удобными в тестировании.

Если класс имеет слишком много внедренных зависимостей, это может указывать на то, что у класса слишком много задач и он нарушает [принцип единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новые классы. Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.

### <a name="disposal-of-services"></a>Удаление служб

Контейнер вызывает <xref:System.IDisposable.Dispose%2A> для создаваемых им типов <xref:System.IDisposable>. Службы, разрешенные из контейнера, никогда не должны удаляться разработчиком. Если тип или фабрика зарегистрированы как одноэлементный объект, контейнер автоматически удалит одноэлементные объекты.

В следующем примере службы создаются контейнером службы и автоматически удаляются:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

После каждого обновления страницы индекса в консоли отладки отображаются следующие выходные данные:

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a>Службы, не созданные контейнером службы

Рассмотрим следующий код.

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

В приведенном выше коде:

* Экземпляры службы не создаются контейнером службы.
* Платформа не удаляет службы автоматически.
* За удаление служб отвечает разработчик.

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Руководство по применению временных и общих экземпляров IDisposable

См. раздел [Рекомендации по IDisposable при использовании промежуточного и общего экземпляра](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).

## <a name="default-service-container-replacement"></a>Замена стандартного контейнера служб

См. раздел [Замена контейнера службы по умолчанию](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).

## <a name="recommendations"></a>Рекомендации

См. раздел [Рекомендации](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) в статье [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).

* Старайтесь не использовать *схему указателя служб*. Например, не вызывайте <xref:System.IServiceProvider.GetService%2A> для получения экземпляра службы, когда можно использовать внедрение зависимостей:

  **Неправильно**:

    ![Неверный код](dependency-injection/_static/bad.png)

  **Правильно**:

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения. Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).
* Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

<a name="ASP0000"></a>
* Избегайте вызовов <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> в `ConfigureServices`. Вызов `BuildServiceProvider` обычно происходит, когда разработчику необходимо разрешить службу в `ConfigureServices`. Например, рассмотрим случай, когда `LoginPath` загружается из конфигурации. Добавьте следующий код:

  ![Неверный код при вызове BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  На предыдущем рисунке при выборе строки, отмеченной зеленой волнистой линией в разделе `services.BuildServiceProvider`, отображается следующее предупреждение ASP0000:

  > ASP0000. Вызов BuildServiceProvider из кода приложения приводит к созданию дополнительной копии создаваемых одноэлементных служб. В качестве параметров для Configure можно использовать альтернативные варианты, такие как службы внедрения зависимостей.

  При вызове `BuildServiceProvider` создается второй контейнер, который может создавать разорванные одноэлементные экземпляры и ссылаться на графы объектов в нескольких контейнерах.

  Правильный способ получения `LoginPath` — использование встроенной поддержки шаблона параметров для внедрения зависимостей:

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* Неудаляемые временные службы фиксируются контейнером для их удаления. Это может привести к утечке памяти, если она разрешена из контейнера верхнего уровня.
* Включите проверку области, чтобы убедиться, что в приложении нет отдельных объектов, записывающих службы с заданной областью. Дополнительные сведения см. в разделе [Проверка области](#scope-validation).

Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил. Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.

Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам. Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>Рекомендуемые подходы к мультитенантности при внедрении зависимостей

[Orchard Core](https://github.com/OrchardCMS/OrchardCore) — это платформа приложений для создания модульных мультитенантных приложений в ASP.NET Core. Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).

Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. [здесь](https://github.com/OrchardCMS/OrchardCore.Samples).

## <a name="framework-provided-services"></a>Платформенные службы

Метод `Startup.ConfigureServices` регистрирует службы, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC. Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)). Для приложений, основанных на шаблонах ASP.NET Core, платформа регистрирует более 250 служб. 

В следующей таблице перечислены некоторые примеры этих зарегистрированных платформой служб.

| Тип службы                                                                                    | Время существования  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Временный |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | Одноэлементный |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | Одноэлементный |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | Одноэлементный |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | Временный |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | Одноэлементный |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | Временный |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | Одноэлементный |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | Одноэлементный |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | Одноэлементный |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | Временный |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | Одноэлементный |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | Одноэлементный |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | Одноэлементный |

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [Шаблоны конференций NDC для разработки приложений с внедрением зависимостей](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Четыре способа удаления интерфейсов IDisposable в ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)](https://www.martinfowler.com/articles/injection.html)
* [How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)

ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.

Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Общие сведения о внедрении зависимостей

*Зависимость* — это любой объект, который требуется другому объекту. Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`. Класс `MyDependency` выступает зависимостью класса `IndexModel`.

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

Этот класс создает экземпляр `MyDependency` и напрямую зависит от него. Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:

* Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.
* Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс. В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.
* Такая реализация плохо подходит для модульных тестов. В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.

Внедрение зависимостей устраняет эти проблемы следующим образом:

* Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.
* Зависимость регистрируется в контейнере служб. ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>. Службы регистрируются в приложении в методе `Startup.ConfigureServices`.
* Служба *внедряется* в конструктор класса там, где он используется. Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.

В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Этот интерфейс реализуется конкретным типом, `MyDependency`.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе. Использование цепочки внедрений зависимостей не является чем-то необычным. Каждая запрашиваемая зависимость запрашивает собственные зависимости. Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу. Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.

`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб. `IMyDependency` регистрируется в `Startup.ConfigureServices`. Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.

Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`. Регистрация регулирует время существования службы согласно времени существования одного запроса. Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет и, возможно, настраивает службы. Например, `services.AddControllersWithViews`, `services.AddRazorPages` и `services.AddControllers` добавляют службы, необходимые приложениям ASP.NET Core. Рекомендуем придерживаться такого подхода в приложениях. Поместите методы расширения в пространство имен <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>, чтобы инкапсулировать группы зарегистрированных служб. Включение части `Microsoft.Extensions.DependencyInjection` пространства имен для методов расширения внедрения зависимостей также:
>
> * позволяет отображать их в [IntelliSense](/visualstudio/ide/using-intellisense) без добавления дополнительных блоков `using`;
> * позволяет избежать чрезмерного количества инструкций `using` в классе `Startup`, из которого обычно вызываются эти методы расширения.

Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю. Поле используется во всем классе для доступа к службе (по мере необходимости).

В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Службы, внедренные в конструктор Startup

При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Службы можно внедрить в `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Для получения дополнительной информации см. <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Платформенные службы

Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC. Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)). Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой. В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.

| Тип службы | Время существования |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Временный |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Временный |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Временный |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Временный |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Одноэлементный |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Одноэлементный |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Одноэлементный |

## <a name="register-additional-services-with-extension-methods"></a>Регистрация дополнительных служб с помощью методов расширения

Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`. Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.

## <a name="service-lifetimes"></a>Время существования служб

Для каждой зарегистрированной службы выбирайте подходящее время существования. Для служб ASP.NET можно настроить следующие параметры времени существования:

### <a name="transient"></a>Временный

Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб. Это время существования лучше всего подходит для простых служб без отслеживания состояния.

В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.

### <a name="scoped"></a>Область действия

Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).

В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.

> [!WARNING]
> При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`. Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект. Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Одноэлементный

Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы). Созданный экземпляр используют все последующие запросы. Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования. Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.

В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.

> [!WARNING]
> Разрешать регулируемую службу из одиночной нельзя. При обработке последующих запросов это может вызвать неправильное состояние службы.

## <a name="service-registration-methods"></a>Методы регистрации службы

Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.

| Метод | Автоматически<br>object<br>удаление | Несколько<br>реализации | Передача аргументов |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Пример<br>`services.AddSingleton<IMyDep, MyDep>();` | Да | Да | Нет |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Примеры:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Да | Да | Да |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Пример<br>`services.AddSingleton<MyDep>();` | Да | Нет | Нет |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Примеры:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Нет | Да | Да |
| `AddSingleton(new {IMPLEMENTATION})`<br>Примеры:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Нет | Нет | Да |

Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services). Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).

Регистрация службы только с типом реализации эквивалентна регистрации этой службы с той же реализацией и типом службы. Именно поэтому несколько реализаций службы не могут быть зарегистрированы с помощью методов, которые не принимают явный тип службы. Эти методы могут регистрировать несколько *экземпляров* службы, но все они будут иметь одинаковую *реализацию* типа.

Любой из указанных выше методов регистрации службы можно использовать для регистрации нескольких экземпляров службы одного типа службы. В следующем примере метод `AddSingleton` вызывается дважды с типом службы `IMyDependency`. Второй вызов `AddSingleton` переопределяет предыдущий, если он разрешается как `IMyDependency`, и добавляет к предыдущему, если несколько служб разрешаются через `IEnumerable<IMyDependency>`. Службы отображаются в том порядке, в котором они были зарегистрированы при разрешении через `IEnumerable<{SERVICE}>`.

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

Платформа также предоставляет методы расширения `TryAdd{LIFETIME}`, которые регистрируют службу только в том случае, если реализация еще не зарегистрирована.

В следующем примере вызов `AddSingleton` регистрирует `MyDependency` как реализацию для `IMyDependency`. Вызов `TryAddSingleton` ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация.

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

Дополнительные сведения см. в разделе:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*. Несколько служб разрешается через `IEnumerable<{SERVICE}>`. При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен. Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.

В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`. Вторая строка регистрирует `MyDep` для `IMyDep2`. Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Поведение внедрения через конструктор

Службы можно разрешать двумя методами:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей. `ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.

Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.

Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.

Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора. Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.

## <a name="entity-framework-contexts"></a>Контексты Entity Framework

Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса. Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных. Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.

## <a name="lifetime-and-registration-options"></a>Параметры времени существования и регистрации

Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`. В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Интерфейсы реализованы в классе `Operation`. Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`. Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.

* Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`. `OperationService` получает новый экземпляр класса `IOperationTransient`. Новый экземпляр возвращает другой идентификатор `OperationId`.
* Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе. В разных клиентских запросах обе службы используют разные значения `OperationId`.
* Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`. Понятно, когда этот тип используется (его GUID — все нули).

В примере приложения показано время существования объектов в пределах одного и нескольких запросов. В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`. После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Результаты двух запросов будут выглядеть так:

**Первый запрос**

Операции контроллера:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Операции `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Второй запрос**

Операции контроллера:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Операции `OperationService`:

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.

* *Временные* объекты всегда разные. Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`. Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.
* *Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.
* *Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.

## <a name="call-services-from-main"></a>Вызов служб из функции main

Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения. Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации. В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a>Проверка области

Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:

* Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.
* Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.

Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>. Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.

Службы с заданной областью удаляются создавшим их контейнером. Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера. Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.

Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.   

## <a name="request-services"></a>Службы запросов

Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Службы запросов — это все настроенные и запрашиваемые в приложении службы. Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.

Как правило, использовать эти свойства в приложении напрямую не следует. Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости. Этот процесс создает классы, которые легче тестировать.

> [!NOTE]
> Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Проектирование служб для внедрения зависимостей

Придерживайтесь следующих рекомендаций:

* Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.
* Избегайте статических классов и членов с отслеживанием состояния. Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.
* Избегайте прямого создания экземпляров зависимых классов внутри служб. Прямое создание экземпляров обязывает использовать в коде определенную реализацию.
* Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.

Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс. Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом. Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Удаление служб

Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>. Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.

В следующем примере службы создаются контейнером службы и автоматически удаляются:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

В следующем примере:

* Экземпляры службы не создаются контейнером службы.
* Платформе неизвестно предполагаемое время жизни службы.
* Платформа не удаляет службы автоматически.
* Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Руководство по применению временных и общих экземпляров IDisposable

#### <a name="transient-limited-lifetime"></a>Временный экземпляр, ограниченное время существования

**Сценарий**

Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:

* Экземпляр разрешается в корневой области.
* Экземпляр должен быть удален до завершения области.

**Решение**

Используйте шаблон фабрики для создания экземпляра за пределами родительской области. В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа. Если окончательный тип имеет другие зависимости, фабрика позволяет:

* Получить <xref:System.IServiceProvider> в своем конструкторе.
* Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.

#### <a name="shared-instance-limited-lifetime"></a>Общий экземпляр, ограниченное время существования

**Сценарий**

Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.

**Решение**

Зарегистрируйте экземпляр с временем существования с заданной областью. Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>. Используйте <xref:System.IServiceProvider> области для получения необходимых служб. Удалить область по истечении времени существования.

#### <a name="general-guidelines"></a>Общие рекомендации

* Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью. Вместо этого используйте шаблон фабрики.
* Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область. Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.
* Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>. Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.
* Области должны использоваться для управления жизненным циклом служб. Области не являются иерархическими, и между ними нет специальной связи.

## <a name="default-service-container-replacement"></a>Замена стандартного контейнера служб

Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений. Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:

* Внедрение свойств
* Внедрение по имени
* Дочерние контейнеры
* Настраиваемое управление временем существования
* `Func<T>` поддерживает отложенную инициализацию
* Регистрация на основе соглашения

С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);
* [Lamar](https://jasperfx.github.io/lamar/);
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Потокобезопасность

Создавайте потокобезопасные одноэлементные службы. Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.

Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным. Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.

## <a name="recommendations"></a>Рекомендации

* Разрешение служб на основе `async/await` и `Task` не поддерживается. C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.

* Не храните данные и конфигурацию непосредственно в контейнере служб. Например, обычно не следует добавлять корзину пользователя в контейнер служб. Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options). Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту. Лучше запросить фактический элемент через внедрение зависимостей.
* Избегайте статического доступа к службам. Например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices).

* Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).
  * Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:

    **Неправильно**:

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    **Правильно**:

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.
* Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил. Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.

Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам. Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Четыре способа удаления интерфейсов IDisposable в ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)](https://www.martinfowler.com/articles/injection.html)
* [How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)

::: moniker-end
