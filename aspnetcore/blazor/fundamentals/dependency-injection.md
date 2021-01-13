---
title: Внедрение зависимостей Blazor в ASP.NET Core
author: guardrex
description: Подробные сведения о том, как приложения Blazor могут внедрять службы в компоненты.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3f2b4eff5422acbec80b2fd9b801101271cc3f75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97808729"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>Внедрение зависимостей Blazor в ASP.NET Core

Авторы: [Райнер Стропек](https://www.timecockpit.com) (Rainer Stropek) и [Майк Роусос](https://github.com/mjrousos) (Mike Rousos)

[Внедрение зависимостей](xref:fundamentals/dependency-injection) — это методика доступа к службам, настроенным в центральном расположении.

* Зарегистрированные на платформе службы можно внедрять непосредственно в компоненты приложений Blazor.
* Приложения Blazor определяют и регистрируют пользовательские службы и предоставляют к ним доступ в рамках всего приложения с помощью внедрения зависимостей.

## <a name="default-services"></a>Службы по умолчанию

В таблице ниже представлены службы, часто используемые в приложениях Blazor.

| Служба | Время существования | Описание |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Область действия | <p>Предоставляет методы для отправки HTTP-запросов и получения HTTP-ответов от ресурса с заданным URI.</p><p>Экземпляр <xref:System.Net.Http.HttpClient> в приложении Blazor WebAssembly использует браузер для обработки HTTP-трафика в фоновом режиме.</p><p>Приложения Blazor Server не включают клиент <xref:System.Net.Http.HttpClient>, настроенный в качестве службы по умолчанию. Предоставьте <xref:System.Net.Http.HttpClient> приложению Blazor Server.</p><p>Для получения дополнительной информации см. <xref:blazor/call-web-api>.</p><p><xref:System.Net.Http.HttpClient> регистрируется как служба с заданной областью, а не как singleton. Дополнительные сведения см. в разделе [Время существования службы](#service-lifetime).</p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p>**Blazor WebAssembly** . Одноэлементный</p><p>**Blazor Server** : Область действия</p> | Представляет экземпляр среды выполнения JavaScript, в которую отправляются вызовы JavaScript. Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p>**Blazor WebAssembly** . Одноэлементный</p><p>**Blazor Server** : Область действия</p> | Содержит вспомогательные методы для работы с URI и состоянием навигации. Дополнительные сведения см. в разделе [URI и вспомогательные инструменты состояния навигации](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Пользовательский поставщик услуг не предоставляет перечисленные в таблице службы по умолчанию автоматически. Если вы используете пользовательский поставщик услуг и нуждаетесь в какой-либо из служб, указанных в таблице, добавьте необходимые службы в новый поставщик услуг.

## <a name="add-services-to-an-app"></a>Добавление служб в приложение

::: zone pivot="webassembly"

Настройте службы для коллекции служб приложения в методе `Program.Main` файла `Program.cs`. В следующем примере реализация `MyDependency` зарегистрирована для `IMyDependency`:

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

После сборки узла доступ к службам можно получить из корневой области внедрения зависимостей до отрисовки каких-либо компонентов. Это может быть удобно для запуска логики инициализации перед отрисовкой содержимого:

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

Узел предоставляет центральный экземпляр конфигурации для приложения. Основываясь на предыдущем примере, URL-адрес службы погоды передается из источника конфигурации по умолчанию (например, `appsettings.json`) в `InitializeWeatherAsync`:

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

После создания приложения изучите метод `Startup.ConfigureServices` в `Startup.cs`.

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

В метод <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> передается <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, представляющий собой список объектов [дескриптора службы](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor). Службы добавляются в метод `ConfigureServices` путем предоставления дескрипторов служб в коллекцию служб. В следующем примере показана концепция с интерфейсом `IDataAccess` и его конкретной реализацией `DataAccess`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a>Время существования службы

Для служб можно настроить параметры времени существования, указанные в следующей таблице.

| Время существования | Описание |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p>Сейчас в приложениях Blazor WebAssembly концепция областей внедрения зависимостей отсутствует. Службы, зарегистрированные как `Scoped`, работают аналогично службам `Singleton`.</p><p>Модель размещения Blazor Server поддерживает время существования `Scoped` в HTTP-запросах, но не в сообщениях о соединении и создании канала SignalR между компонентами, загруженными на клиенте. Razor Pages или MVC-часть приложения взаимодействует со службами с заданной областью в обычном режиме и воссоздает службы для *каждого HTTP-запроса* при переходе между страницами или представлениями либо со страницы или из представления в компонент. Службы с заданной областью не воссоздаются при переходе между компонентами на клиенте, где обмен данными с сервером происходит через SignalR-подключение по каналу пользователя, а не через HTTP-запросы. В следующих сценариях использования компонентов на клиенте службы с заданной областью воссоздаются, так как для пользователя создается новый канал:</p><ul><li>Пользователь закрывает окно браузера. Пользователь открывает новое окно и снова переходит к приложению.</li><li>Пользователь закрывает последнюю вкладку приложения в окне браузера. Пользователь открывает новую вкладку и снова переходит к приложению.</li><li>Пользователь нажимает кнопку перезагрузки или обновления в браузере.</li></ul><p>Дополнительные сведения о сохранении состояния пользователей в службах с областью действия в Blazor Server приложениях см. в разделе <xref:blazor/hosting-models?pivots=server>.</p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Система внедрения зависимостей создает *один экземпляр* службы. Все компоненты, для которых необходима служба `Singleton`, получают экземпляр той же службы. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Каждый раз, когда компонент получает экземпляр службы `Transient` из контейнера службы, он получает *новый экземпляр* этой службы. |

Система внедрения зависимостей основана на системе внедрения зависимостей в ASP.NET Core. Для получения дополнительной информации см. <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Запрос службы в компоненте

После добавления служб в коллекцию служб внедрите службы в компоненты с помощью директивы [`@inject`](xref:mvc/views/razor#inject) Razor, которая имеет два параметра:

* Тип: тип внедряемой службы.
* Свойство: имя свойства, получающего внедренную службу приложений. Свойство не требуется создавать вручную. Его создает компилятор.

Для получения дополнительной информации см. <xref:mvc/views/dependency-injection>.

Используйте несколько инструкций [`@inject`](xref:mvc/views/razor#inject) для внедрения различных служб.

В следующем примере показано, как использовать [`@inject`](xref:mvc/views/razor#inject). Служба, реализующая `Services.IDataAccess`, внедряется в свойство `DataRepository` компонента. Обратите внимание, что код использует только абстракцию `IDataAccess`:

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

На внутреннем уровне создаваемое свойство (`DataRepository`) использует атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute). Как правило, этот атрибут не используется напрямую. Если базовый класс необходим для компонентов, а для базового класса также требуются обязательные свойства, добавьте атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) вручную:

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

В компонентах, производных от базового класса, директива [`@inject`](xref:mvc/views/razor#inject) не требуется. <xref:Microsoft.AspNetCore.Components.InjectAttribute> базового класса достаточно:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Использование внедрения зависимостей в службах

Для сложных служб могут потребоваться дополнительные службы. В приведенном ниже примере для `DataAccess` требуется служба <xref:System.Net.Http.HttpClient> по умолчанию. [`@inject`](xref:mvc/views/razor#inject) (или атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) невозможно использовать в службах. Вместо этого следует использовать *внедрения конструктора*. Необходимые службы добавляются путем добавления параметров в конструктор службы. Когда система внедрения зависимостей создает службу, она распознает необходимые службы в конструкторе и предоставляет их соответствующим образом. В следующем примере конструктор получает <xref:System.Net.Http.HttpClient> через внедрение зависимостей. <xref:System.Net.Http.HttpClient> — это служба по умолчанию.

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

Необходимые условия для внедрения конструктора:

* Должен существовать один конструктор, аргументы которого могут использоваться системой внедрения зависимостей. Дополнительные параметры, не охваченные системой внедрения зависимостей, разрешены, если они указывают значения по умолчанию.
* Применимый конструктор должен быть `public`.
* Должен существовать один подходящий конструктор. В случае неоднозначности система внедрения зависимостей выдает исключение.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей

В приложениях ASP.NET Core службы с заданной областью обычно ограничены текущим запросом. По завершении запроса все временные службы или службы с заданной областью удаляются системой внедрения зависимостей. В приложениях Blazor Server область запроса существует на протяжении всего клиентского подключения. Это может привести к тому, что временные службы или службы с заданной областью будут работать намного дольше, чем ожидалось. В приложениях Blazor WebAssembly службы, зарегистрированные с ограниченным временем существования, рассматриваются как singleton, поэтому они существуют дольше, чем службы с заданной областью в типичных приложениях ASP.NET Core.

> [!NOTE]
> См. раздел [Обнаружение высвобождаемых временных служб](#detect-transient-disposables).

Подход, ограничивающий время существования службы в приложениях Blazor, заключается в использовании типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>. <xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактным типом, производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>, который создает область внедрения зависимостей, соответствующую времени существования компонента. Используя эту область, можно использовать службы внедрения зависимостей с ограниченным временем существования, заставляя их существовать так же долго, как и компонент. При удалении компонента также удаляются и службы из поставщика служб с заданной областью действия этого компонента. Это может быть полезно для служб, которые:

* требуется повторно использовать в компоненте, так как временное существование не подходит;
* не должны совместно использоваться компонентами, так как одноэлементное время существования не подходит.

Доступны две версии типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактной высвобождаемой дочерней версией типа <xref:Microsoft.AspNetCore.Components.ComponentBase> с защищенным свойством <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> типа <xref:System.IServiceProvider>. Этот поставщик можно использовать для разрешения служб, ограниченных временем существования компонента.

  Службы внедрения зависимостей, внедренные в компонент с помощью атрибута [`@inject`](xref:mvc/views/razor#inject) или [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute), не создаются в области компонента. Чтобы использовать область компонента, необходимо разрешить службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> или <xref:System.IServiceProvider.GetService%2A>. Все службы, разрешенные с помощью поставщика <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>, имеют свои зависимости из этой же области.

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> является производным от <xref:Microsoft.AspNetCore.Components.OwningComponentBase> и добавляет свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A>, которое возвращает экземпляр `T` из поставщика внедрения зависимостей с заданной областью. Этот тип удобен для доступа к службам с заданной областью без использования экземпляра <xref:System.IServiceProvider> при наличии одной основной службы, которую приложение запрашивает из контейнера внедрения зависимостей с использованием области компонента. Свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> доступно, поэтому при необходимости приложение может получить службы других типов.

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>Использование DbContext Entity Framework Core (EF Core) из внедрения зависимостей

Дополнительные сведения см. в разделе <xref:blazor/blazor-server-ef-core>.

## <a name="detect-transient-disposables"></a>Обнаружение высвобождаемых временных служб

В следующих примерах показано, как обнаружить высвобождаемые временные службы в приложении, которые должны использовать <xref:Microsoft.AspNetCore.Components.OwningComponentBase>. См. раздел [Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей](#utility-base-component-classes-to-manage-a-di-scope).

::: zone pivot="webassembly"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

В следующем примере обнаруживается `TransientDisposable` (`Program.cs`):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end 

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: zone-end

::: zone pivot="server"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

Добавьте пространство имен для <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> в `Program.cs`.

```csharp
using Microsoft.Extensions.DependencyInjection;
```

В методе `Program.CreateHostBuilder` в файле `Program.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

В следующем примере обнаруживается `TransientDependency` (`Startup.cs`):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

Приложение может регистрировать временные высвобождаемые службы, не вызывая исключение. При этом попытка разрешить временную высвобождаемую службу приведет к <xref:System.InvalidOperationException>, как показано в следующем примере.

`Pages/TransientDisposable.razor`:

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

Перейдите к компоненту `TransientDisposable` в `/transient-disposable`. Когда платформа попытается создать экземпляр `TransientDisposable`, возникнет исключение <xref:System.InvalidOperationException>:

> "System.InvalidOperationException: Trying to resolve transient disposable service TransientDisposable in the wrong scope. Use an 'OwningComponentBase\<T>' component base class for the service 'T' you are trying to resolve.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/dependency-injection>
* [Руководство по применению временных и общих экземпляров `IDisposable`](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
