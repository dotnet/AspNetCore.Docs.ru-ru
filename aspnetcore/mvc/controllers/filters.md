---
title: Фильтры в ASP.NET Core
author: Rick-Anderson
description: Из этой статьи вы узнаете, как работают фильтры и как их использовать в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: 79457d55e0dcda342bc0017bb386c23525666657
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107198"
---
# <a name="filters-in-aspnet-core"></a>Фильтры в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Авторы: [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin) [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Том Дайкстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)

*Фильтры* в ASP.NET Core позволяют выполнять код до или после определенных этапов в конвейере обработки запросов.

Встроенные фильтры обрабатывают следующие задачи:

* Авторизация (предотвращение несанкционированного доступа к ресурсам).
* Кэширование откликов (замыкание конвейера обработки запросов для возврата кэшированного ответа).

Для обработки сквозной функциональности можно создавать настраиваемые фильтры. Примеры сквозной функциональности включают обработку ошибок, кэширование, конфигурирование, авторизацию и ведение журнала.  Фильтры предотвращают дублирование кода. Например, можно объединить обработку ошибок с помощью фильтра исключений обработки ошибок.

Этот документ применяется к Razor страницам, контроллерам API и контроллерам с представлениями. Фильтры не работают непосредственно с [ Razor компонентами](xref:blazor/components/index). Фильтр может влиять на компонент только косвенно в таких случаях:

* Компонент внедряется в страницу или представление.
* Страница, контроллер или представление использует фильтр.

[Просмотреть или скачать пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([как скачивать](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Как работают фильтры

Фильтры выполняются в *конвейере вызова действий ASP.NET Core*, который иногда называют *конвейером фильтров*. Конвейер фильтров запускается после того, как платформа ASP.NET Core выбирает выполняемое действие.

![Запрос обрабатывается с помощью прочего ПО промежуточного слоя, ПО промежуточного слоя маршрутизации, выбора действия и конвейера вызова действий. Обработка запроса продолжается в обратном порядке посредством выбора действия, ПО промежуточного слоя маршрутизации и прочего ПО промежуточного слоя, пока не будет получен запрос, отправляемый клиенту.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Типы фильтров

Фильтр каждого типа выполняется на определенном этапе конвейера фильтров:

* [Фильтры авторизации](#authorization-filters). Применяются в первую очередь и служат для определения того, авторизован ли пользователь для выполнения запроса. Эти фильтры могут сократить выполнение конвейера, если запрос не авторизован.

* [Фильтры ресурсов](#resource-filters):

  * Выполняются после авторизации.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> выполняет код до остальной части конвейера фильтров. Например, `OnResourceExecuting` выполняет код до привязки модели.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> выполняет код после завершения остальной части конвейера.

* [Фильтры действий](#action-filters):

  * выполняют код непосредственно до и после вызова метода действия.
  * Могут изменять аргументы, передаваемые в действие.
  * Могут изменять результат, возвращенный действием.
  * **Не** поддерживаются на Razor страницах.

* [Фильтры исключений](#exception-filters) применяют глобальные политики к необработанным исключениям, которые происходят до записи данных в тело ответа.

* [Фильтры результатов](#result-filters) выполняют код непосредственно до и после выполнения результатов действия. Они выполняются только в том случае, если метод действия выполнен успешно. Они полезны для логики, которая должна включать исключения для представлений или модуля форматирования.

На приведенной ниже схеме показано, как типы фильтров взаимодействуют друг с другом в конвейере фильтров.

![Запрос обрабатывается посредством фильтров авторизации, фильтров ресурсов, привязки модели, фильтров действий, выполнения действия и преобразования результата действия, фильтров исключений, фильтров результатов и выполнения результатов. На обратном пути запрос обрабатывается только фильтрами результатов и фильтрами ресурсов, прежде чем стать ответом, отправляемым клиенту.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Реализация

Фильтры поддерживают как синхронные, так и асинхронные реализации посредством различных определений интерфейсов.

Синхронные фильтры выполняют код до и после этапа конвейера. Например, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> вызывается перед вызовом метода действия, а <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> — после возврата метода действия.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

В приведенном выше коде [мидебуг](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) — это служебная функция в [примере загрузки](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).

Асинхронные фильтры определяют метод `On-Stage-ExecutionAsync`. Например, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

В приведенном выше коде `SampleAsyncActionFilter` включает <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) для выполнения метода действия.

### <a name="multiple-filter-stages"></a>Несколько этапов фильтра

Реализовать интерфейсы для нескольких этапов фильтра можно в одном классе. Например, класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> реализует следующие интерфейсы:

* синхронные: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>;
* асинхронные: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>.
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

Реализуйте синхронный **или** асинхронный интерфейс фильтра, но **не** оба варианта. Среда выполнения сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается именно он. В противном случае вызываются методы синхронного интерфейса. Если асинхронный и синхронный интерфейсы реализованы в одном классе, вызывается только асинхронный метод. При использовании абстрактных классов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , таких как, переопределяйте только синхронные методы или асинхронные методы для каждого типа фильтра.

### <a name="built-in-filter-attributes"></a>Встроенные атрибуты фильтров

ASP.NET Core включает встроенные фильтры на основе атрибутов, с помощью которых можно создавать подклассы и которые можно настраивать. Например, следующий фильтр результатов добавляет заголовок к ответу:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Атрибуты позволяют фильтрам принимать аргументы, как показано в примере выше. Примените `AddHeaderAttribute` к методу контроллера или действия, а затем укажите имя и значение заголовка HTTP:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Для изучения заголовков используйте средство [разработчика браузера](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) . В **заголовке ответа** отображается `author: Rick Anderson`.

В следующем коде реализуется класс `ActionFilterAttribute`, который выполняет следующие действия:

* Считывает заголовок и имя в системе конфигурации. В отличие от предыдущего примера, для указанного ниже кода не нужно добавлять параметры фильтра.
* Добавляет заголовок и имя к заголовку ответа.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

Параметры конфигурации предоставляются из [системы конфигурации](xref:fundamentals/configuration/index) с помощью [шаблона параметров](xref:fundamentals/configuration/options). Например, из *appsettings.json* файла:

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

В `StartUp.ConfigureServices`:

* Класс `PositionOptions` добавляется в контейнер службы с помощью области конфигурации `"Position"`.
* `MyActionFilterAttribute` добавляется в контейнер службы.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

В следующем коде демонстрируется класс `PositionOptions`:

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

В следующем коде применяется атрибут `MyActionFilterAttribute` к методу `Index2`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

В разделе **заголовки ответов**, `author: Rick Anderson` и `Editor: Joe Smith` отображается при `Sample/Index2` вызове конечной точки.

Следующий код применяет `MyActionFilterAttribute` и `AddHeaderAttribute` к Razor странице:

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

Фильтры не могут применяться к Razor методам обработчика страниц. Их можно применять либо к Razor модели страницы, либо глобально.

Некоторые интерфейсы фильтров имеют соответствующие атрибуты, которые можно использовать как базовые классы для пользовательских реализаций.

Атрибуты фильтров:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Области и порядок выполнения фильтров

Фильтр можно добавить в конвейер в одной из трех *областей*:

* С помощью атрибута в действии контроллера. Атрибуты фильтра не могут применяться к Razor методам обработчика страниц.
* Использование атрибута на контроллере или Razor странице.
* Глобально для всех контроллеров, действий и Razor страниц, как показано в следующем коде:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a>Порядок выполнения по умолчанию

Если для определенного этапа конвейера имеется несколько фильтров, область определяет порядок их выполнения по умолчанию.  Глобальные фильтры заключают в себя фильтры классов, которые, в свою очередь, заключают в себя фильтры методов.

В результате такого вложения *последующий* код фильтров выполняется в порядке, обратном выполнению *предшествующего* кода. Последовательность фильтров:

* *Предшествующий* код глобальных фильтров.
  * Код, *предшествующий* контроллеру и Razor фильтрам страниц.
    * *Предшествующий* код фильтров методов действий.
    * *Последующий* код фильтров методов действий.
  * Код *после* кода фильтров контроллеров и Razor страниц.
* *Последующий* код глобальных фильтров.
  
В следующем примере показан порядок вызова методов фильтров для синхронных фильтров действий.

| Последовательность | Область фильтра | Метод фильтра |
|:--------:|:------------:|:-------------:|
| 1 | Глобальный | `OnActionExecuting` |
| 2 | Контроллер или Razor страница| `OnActionExecuting` |
| 3 | Метод | `OnActionExecuting` |
| 4 | Метод | `OnActionExecuted` |
| 5 | Контроллер или Razor страница | `OnActionExecuted` |
| 6 | Глобальный | `OnActionExecuted` |

### <a name="controller-level-filters"></a>Фильтры на уровне контроллера

Каждый контроллер, наследуемый от базового класса <xref:Microsoft.AspNetCore.Mvc.Controller> включает методы [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) и [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Эти методы:

* Заключают фильтры, которые выполняются для указанного действия.
* `OnActionExecuting` вызывается перед всеми фильтрами действий.
* `OnActionExecuted` вызывается после всех фильтров действий.
* `OnActionExecutionAsync` вызывается перед всеми фильтрами действий. Код в фильтре после `next` выполняется после метода действия.

Например, в скачиваемом примере `MySampleActionFilter` применяется глобально при запуске.

`TestController`:

* Применяет `SampleActionFilterAttribute` (`[SampleActionFilter]`) для действия `FilterTest2`.
* Переопределяет `OnActionExecuting` и `OnActionExecuted`.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

Переходит к `https://localhost:5001/Test/FilterTest2` для выполнения следующего кода:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Фильтры на уровне контроллера задают для свойства [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) значение `int.MinValue`. Их **нельзя** настроить, чтобы они запускались после применения фильтров к методам. Свойство Order рассматривается в следующем разделе.

Для Razor страниц см. раздел [реализация Razor фильтров страниц путем переопределения методов фильтров](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Переопределение порядка по умолчанию

Чтобы переопределить порядок выполнения по умолчанию, можно реализовать <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>. `IOrderedFilter` предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, которое имеет приоритет над областью и определяет порядок выполнения. Фильтр со значением меньше `Order`:

* Выполняется *перед* кодом, выполняемым до фильтра с более высоким значением `Order`.
* Выполняется *после* кода, выполняемого после фильтра с более высоким значением `Order`.

Свойство `Order` задается с помощью параметра конструктора:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

Рассмотрим два фильтра действий в следующем контроллере:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

Глобальный фильтр добавляется в `StartUp.ConfigureServices`:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

3 фильтра выполняются в следующем порядке:

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

Свойство `Order` переопределяет область при определении порядка выполнения фильтров. Фильтры сначала сортируются по порядку, а затем очередность окончательно определяется по области. Все встроенные фильтры реализуют `IOrderedFilter` и задают значение по умолчанию `Order`, равное 0. Как упоминалось ранее, фильтры на уровне контроллера задают для свойства [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) значение `int.MinValue`. Во встроенных фильтрах область определяет порядок, если для `Order` не задано ненулевое значение.

В указанном выше коде `MySampleActionFilter` имеет глобальную область действия и запускается перед `MyAction2FilterAttribute`, у которого область действия контроллера. Чтобы `MyAction2FilterAttribute` запускался первым, задайте для свойства Order значение `int.MinValue`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

Чтобы глобальный фильтр `MySampleActionFilter` запускался первым, задайте для свойства `Order` значение `int.MinValue`:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a>Отмена и сокращенное выполнение

Вы можете настроить сокращенное выполнение конвейера фильтров, задав свойство <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> для параметра <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext>, передаваемого в метод фильтра. Например, приведенный ниже фильтр ресурсов предотвращает выполнение остальной части конвейера:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

В приведенном ниже коде как фильтр `ShortCircuitingResourceFilter`, так и фильтр `AddHeader` нацелены на метод действия `SomeResource`. `ShortCircuitingResourceFilter`:

* Выполняется первым, поскольку это фильтр ресурсов, а `AddHeader` — фильтр действий.
* Замыкает оставшуюся часть конвейера.

Поэтому фильтр `AddHeader` никогда не выполняется для действия `SomeResource`. Поведение будет аналогичным при применении обоих фильтров на уровне метода действия при условии, что фильтр `ShortCircuitingResourceFilter` выполняется первым. Фильтр `ShortCircuitingResourceFilter` выполняется в первую очередь из-за его типа или в связи с явным указанием свойства `Order`.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=1,15)]

## <a name="dependency-injection"></a>Внедрение зависимостей

Фильтры можно добавлять по типу или экземпляру. Добавляемый экземпляр используется для каждого запроса. Если добавляется тип, он является активированным. Активация фильтра означает, что:

* Экземпляр создается для каждого запроса.
* Зависимости конструктора заполняются путем [внедрения зависимостей](xref:fundamentals/dependency-injection).

Фильтры, которые реализуются как атрибуты и добавляются непосредственно в классы контроллеров или методы действий, не могут иметь зависимости конструктора, предоставленные посредством [внедрения зависимостей](xref:fundamentals/dependency-injection). Зависимости конструктора не могут предоставляться путем внедрения зависимостей, так как:

* Параметры конструктора должны предоставляться для атрибутов в месте их применения. 
* Это ограничение, налагаемое на использование атрибутов.

Следующие фильтры поддерживают зависимости конструктора, предоставленные путем внедрения зависимостей:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализуется в атрибуте.

Предыдущие фильтры могут применяться к контроллеру или методу действия.

Средства ведения журнала доступны путем внедрения зависимостей. Но следует избегать создания и использования фильтров исключительно для целей ведения журнала. [Встроенное средство ведения журнала](xref:fundamentals/logging/index) обычно предоставляет все необходимое для ведения журнала. При добавлении ведения журналов в фильтры следует учитывать следующее:

* Следует сосредоточиться на бизнес-среде или поведении в привязке к фильтру.
* **Не** следует регистрировать действия или другие события платформы, так как это делают встроенные фильтры.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Типы реализации фильтра службы регистрируются в `ConfigureServices`. Атрибут <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> извлекает экземпляр фильтра из внедрения зависимостей.

В следующем коде используется `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

В следующем коде в контейнер внедрения зависимостей добавляется `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

В следующем коде атрибут `ServiceFilter` извлекает экземпляр фильтра `AddHeaderResultServiceFilter` из внедрения зависимостей:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

При использовании `ServiceFilterAttribute` задание [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан. Среда выполнения ASP.NET Core не гарантирует:

  * что будет создан хотя бы один экземпляр фильтра;
  * что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.

* Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.

 Объект <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` загружает указанный тип из внедрения зависимостей.

### <a name="typefilterattribute"></a>TypeFilterAttribute

Атрибут <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> похож на <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, но его тип не разрешается напрямую из контейнера внедрения зависимостей. Он создает экземпляр типа с помощью <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.

Так как типы `TypeFilterAttribute` не разрешаются напрямую из контейнера внедрения зависимостей:

* Типы, на которые ссылаются с помощью `TypeFilterAttribute`, не нужно регистрировать в контейнере внедрения зависимостей.  Их зависимости выполняются самим контейнером.
* Атрибут `TypeFilterAttribute` может также принимать аргументы конструктора для типа.

При использовании `TypeFilterAttribute` задание [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан. Среда выполнения ASP.NET Core не предоставляет никаких гарантий, что будет создан хотя бы один экземпляр фильтра.

* Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.

В следующем примере показано, как передавать аргументы в тип с помощью `TypeFilterAttribute`:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Фильтры авторизации

Фильтры авторизации:

* Выполняются в первую очередь в конвейере фильтров.
* Контролируют доступ к методам действий.
* Имеют предшествующий, но не последующий метод.

Для работы пользовательских фильтров авторизации требуется настраиваемая платформа авторизации. Настройка политик авторизации или определение пользовательской политики авторизации предпочтительнее создания пользовательского фильтра. Встроенный фильтр авторизации:

* Вызывает систему авторизации.
* Не выполняет авторизацию запросов.

**Не** вызывайте исключения в фильтрах авторизации:

* Исключение не будет обработано.
* Фильтры исключений не будут обрабатывать исключение.

При возникновении исключения в фильтре авторизации попробуйте создать запрос.

Дополнительные сведения об [авторизации](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Фильтры ресурсов

Фильтры ресурсов:

* Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* Выполнение охватывает большую часть конвейера фильтров.
* До фильтров ресурсов применяются только [фильтры авторизации](#authorization-filters).

Фильтры ресурсов полезны для сокращенного выполнения большей части конвейера. Например, фильтр кэширования предотвращает выполнение остальной части конвейера при попадании в кэше.

Примеры фильтров ресурсов:

* [Сокращенное выполнение фильтра ресурсов](#short-circuiting-resource-filter) показано ранее.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Предотвращает доступ привязки модели к данным формы.
  * Используется для загрузки больших файлов, если необходимо предотвратить считывание данных формы в память.

## <a name="action-filters"></a>Фильтры действий

Фильтры действий **не** применяются к Razor страницам. Razor Страницы поддерживают <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).

Фильтры действий:

* Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* Их выполнение охватывает выполнение методов действия.

В следующем фрагменте кода показан пример фильтра действий:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> предоставляет следующие свойства:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> позволяет считать входные данные в метод действия.
* <xref:Microsoft.AspNetCore.Mvc.Controller> позволяет управлять экземпляром контроллера.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> позволяет при задании свойства `Result` сократить выполнение метода действия и последующих фильтров действий.

Вызов исключения в методе действия:

* Предотвращает выполнение последующих фильтров.
* В отличие от параметра `Result` рассматривается как сбой, а не успешный результат.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> предоставляет `Controller` и `Result`, а также следующие свойства:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> будет иметь ненулевое значение, если предыдущее выполнение фильтра действий вызвало исключение. При присвоении этому свойству ненулевого значения:

  * Будет эффективно обрабатываться исключение.
  * `Result` будет выполняться так, как при возвращении методом действия.

Для `IAsyncActionFilter` вызов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:

* Приводит к выполнению последующих фильтров действий и метода действия.
* Возвращает `ActionExecutedContext`.

Для сокращенного выполнения присвойте <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> экземпляру результата и не вызывайте `next` (`ActionExecutionDelegate`).

Платформа предоставляет абстрактный класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, для которого можно создавать подклассы.

Фильтр действий `OnActionExecuting` можно использовать:

* Для проверки состояния модели.
* Для получения ошибки, если состояние является недопустимым.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> Контроллеры, отмеченные `[ApiController]` атрибутом, автоматически проверяют состояние модели и возвращают ответ 400. Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).

Метод `OnActionExecuted` выполняется после метода действия:

* Он имеет доступ к результатам действия и может управлять ими с помощью свойства <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> будет иметь ненулевое значение, если действие или последующий фильтр действий вызвали исключение. Если установить для `Exception` значение NULL:

  * Будет эффективно обрабатываться исключение.
  * `ActionExecutedContext.Result` будет выполняться так, как если бы метод действия вернул его обычным образом.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Фильтры исключений

Фильтры исключений:

* Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.
* Можно использовать для реализации политик обработки стандартных ошибок.

В следующем примере фильтра исключений используется пользовательское представление ошибок для отображения подробных сведений об исключениях, которые происходят во время разработки приложения:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

В следующем коде проверяется фильтр исключений:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

Фильтры исключений:

* Не имеют предшествующих и последующих событий.
* Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Обрабатывать необработанные исключения, происходящие при Razor создании страницы или контроллера, [Привязка модели](xref:mvc/models/model-binding), фильтры действий или методы действий.
* **Не** перехватывают исключения, которые возникают в фильтрах ресурсов, фильтрах результатов или при выполнении результата MVC.

Для обработки исключения присвойте свойству <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> значение `true` или напишите ответ. Это предотвратит распространение исключения. Фильтр исключений не может преобразовать исключение в успешное выполнение. Это может сделать только фильтр действий.

Фильтры исключений:

* Хорошо подходят для перехвата исключений, возникающих в действиях.
* Не обладает такой гибкостью, как ПО промежуточного слоя обработки ошибок.

ПО промежуточного слоя хорошо подходит для обработки исключений. Используйте фильтры исключений, только если обработка ошибок осуществляется *по-разному* с учетом вызванного метода действия. Например, в приложении могут использоваться методы действий как для конечных точек API, так и для представлений или HTML. Конечные точки API могут возвращать сведения об ошибках в формате JSON, в то время как действия на основе представлений могут возвращать страницу ошибки в формате HTML.

## <a name="result-filters"></a>Фильтры результатов

Фильтры результатов:

* Реализация интерфейса:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> либо <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Их выполнение охватывает выполнение результатов действий.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter и IAsyncResultFilter

В следующем фрагменте кода показан фильтр результатов, который добавляет заголовок HTTP:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Тип выполняемого результата зависит от соответствующего действия. Действие, возвращающее представление, включает всю обработку Razor в рамках выполняемого объекта <xref:Microsoft.AspNetCore.Mvc.ViewResult>. В процессе выполнения результата метод API может производить сериализацию. Дополнительные сведения о [результатах действий](xref:mvc/controllers/actions).

Фильтры результатов выполняются только в том случае, когда действие или фильтры действий предоставляют результат действия. Фильтры результатов не выполняются в следующих случаях:

* Фильтр авторизации или ресурсов выполняет сокращение конвейера.
* Фильтр исключений обрабатывает исключение и выдает результат действия.

Метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> может сокращать выполнение результата действия и последующих фильтров результатов, присваивая свойству <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> значение `true`. При сокращении выполнения выполните запись в объект ответа, чтобы избежать формирования пустого ответа. Создание исключения в `IResultFilter.OnResultExecuting`:

* предотвращает выполнение результата действия и последующих фильтров;
* рассматривается как сбой, а не успешный результат.

Если запускается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, ответ, скорее всего, уже был отправлен клиенту. Если ответ уже был отправлен клиенту, его нельзя изменить.

`ResultExecutedContext.Canceled` будет иметь значение `true`, если выполнение результата действия было сокращено другим фильтром.

`ResultExecutedContext.Exception` будет иметь ненулевое значение, если результат действия или последующий фильтр результатов вызвали исключение. Присвоение `Exception` значения NULL приводит к обработке исключения и предотвращает его последующий вызов на дальнейших этапах конвейера. Надежный способ записи данных в ответ при обработке исключения в фильтре результатов отсутствует. Если результат действия вызывает исключение в процессе выполнения и заголовки уже были переданы в клиент, надежного механизма отправки кода сбоя не существует.

Для <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> вызов к `await next` для <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> приводит к выполнению последующих фильтров результатов и результата действия. Для сокращения выполнения задайте [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) для `true` и не вызывайте `ResultExecutionDelegate`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Платформа предоставляет абстрактный класс `ResultFilterAttribute`, для которого можно создавать подклассы. Представленный ранее класс [AddHeaderAttribute](#add-header-attribute) — это пример атрибута фильтра результатов.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter и IAsyncAlwaysRunResultFilter

Интерфейсы <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> объявляют реализацию <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>, которая выполняется для всех результатов действий. Сюда включены результаты действий, созданные:

* фильтрами авторизации и фильтрами ресурсов, которые сокращают ответ;
* фильтрами исключений.

Например, следующий фильтр всегда выполняется, задавая результат действия (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) с кодом состояния *422 Unprocessable Entity* при сбое согласования содержимого:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

Объект <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Поэтому экземпляр `IFilterFactory` можно использовать в качестве экземпляра `IFilterMetadata` в любом месте конвейера фильтров. Когда среда выполнения готовится вызвать фильтр, она пытается привести его к `IFilterFactory`. Если приведение проходит успешно, вызывается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания вызываемого экземпляра `IFilterMetadata`. Это обеспечивает высокую степень гибкости, так как при запуске приложения нет необходимости в явном и точном определении конвейера фильтров.

`IFilterFactory.IsReusable`:

* Является указанием фабрики, которую экземпляр фильтра, созданный фабрикой, может быть повторно использован вне области запроса, в которой он был создан.
* ***Не*** следует использовать с фильтром, зависящим от служб со временем существования, отличным от Singleton.

Среда выполнения ASP.NET Core не гарантирует:

* что будет создан хотя бы один экземпляр фильтра;
* что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.

> [!WARNING] 
> Настраивается <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.IsReusable?displayProperty=nameWithType> для возврата только в том `true` случае, если источник фильтров является однозначным, фильтры не имеют состояния, а фильтры могут использоваться в нескольких HTTP-запросах. Например, не следует возвращать фильтры из DI, которые зарегистрированы как область или временное значение, если `IFilterFactory.IsReusable` возвращает `true` .

Еще один подход к созданию фильтров заключается в реализации `IFilterFactory` с помощью настраиваемых атрибутов:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

В следующем коде применяется фильтр:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

Проверьте приведенный выше код, выполнив [скачиваемый пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):

* Вызовите средства для разработчика (F12).
* Перейдите на страницу `https://localhost:5001/Sample/HeaderWithFactory`.

В средствах для разработчика (F12) отобразятся следующие заголовки ответа, добавленные примером кода:

* **Автор:**`Rick Anderson`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **внутренний:**`My header`

Приведенный выше код создает заголовок ответа **internal:** `My header`.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>Реализация IFilterFactory в атрибуте

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Фильтры, реализующие `IFilterFactory`, используются для фильтров, которые:

* Не требуют передачи параметров.
* Содержат зависимости конструктора, которые должны заполняться путем внедрения зависимостей.

Объект <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` загружает указанный тип из контейнера служб (внедрение зависимостей).

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

В коде ниже приведено три примера применения `[SampleActionFilter]`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

В приведенном выше коде декорирование метода с использованием `[SampleActionFilter]` является рекомендуемым способом применения `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Использование ПО промежуточного слоя в конвейере фильтров

Фильтры ресурсов по принципу работы похожи на [ПО промежуточного слоя](xref:fundamentals/middleware/index) тем, что они заключают в себя выполнение всех объектов, находящихся далее в конвейере. Однако фильтры отличаются от ПО промежуточного слоя тем, что они являются частью среды выполнения, а значит имеют доступ к контексту и конструкциям.

Чтобы использовать ПО промежуточного слоя в качестве фильтра, создайте тип с методом `Configure`, определяющим ПО промежуточного слоя, которое нужно внедрить в конвейер фильтров. В примере ниже ПО промежуточного слоя локализации применяется для определения текущих языка и региональных параметров для запроса:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Используйте <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> для выполнения ПО промежуточного слоя:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Фильтры ПО промежуточного слоя выполняются на том же этапе конвейера фильтров, что и фильтры ресурсов, перед привязкой модели и после остальной части конвейера.

## <a name="next-actions"></a>Дальнейшие действия

* См. раздел [методы фильтров для Razor страниц](xref:razor-pages/filter).
* Чтобы поэкспериментировать с фильтрами, [скачайте, протестируйте и измените пример с GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Авторы: [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin) [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Том Дайкстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)

*Фильтры* в ASP.NET Core позволяют выполнять код до или после определенных этапов в конвейере обработки запросов.

Встроенные фильтры обрабатывают следующие задачи:

* Авторизация (предотвращение несанкционированного доступа к ресурсам).
* Кэширование откликов (замыкание конвейера обработки запросов для возврата кэшированного ответа).

Для обработки сквозной функциональности можно создавать настраиваемые фильтры. Примеры сквозной функциональности включают обработку ошибок, кэширование, конфигурирование, авторизацию и ведение журнала.  Фильтры предотвращают дублирование кода. Например, можно объединить обработку ошибок с помощью фильтра исключений обработки ошибок.

Этот документ применяется к Razor страницам, контроллерам API и контроллерам с представлениями.

[Просмотреть или скачать пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([как скачивать](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Как работают фильтры

Фильтры выполняются в *конвейере вызова действий ASP.NET Core*, который иногда называют *конвейером фильтров*.  Конвейер фильтров запускается после того, как платформа ASP.NET Core выбирает выполняемое действие.

![Запрос обрабатывается с использованием прочего ПО промежуточного слоя, ПО промежуточного слоя маршрутизации, выбора действия и конвейера вызова действий ASP.NET Core. Обработка запроса продолжается в обратном порядке посредством выбора действия, ПО промежуточного слоя маршрутизации и прочего ПО промежуточного слоя, пока не будет получен запрос, отправляемый клиенту.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Типы фильтров

Фильтр каждого типа выполняется на определенном этапе конвейера фильтров:

* [Фильтры авторизации](#authorization-filters). Применяются в первую очередь и служат для определения того, авторизован ли пользователь для выполнения запроса. Эти фильтры могут сократить выполнение конвейера, если запрос не авторизован.

* [Фильтры ресурсов](#resource-filters):

  * Выполняются после авторизации.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> может выполнять код до остальной части конвейера фильтров. Например, `OnResourceExecuting` может выполнять код до привязки модели.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> может выполнять код после завершения остальной части конвейера.

* [Фильтры действий](#action-filters) могут выполнять код непосредственно до и после вызова отдельного метода действия. С их помощью можно управлять аргументами, передаваемыми в действие, и возвращаемым из него результатом. Фильтры действий **не** поддерживаются на Razor страницах.

* [Фильтры исключений](#exception-filters) служат для применения глобальных политик к необработанным исключениям, которые происходят до записи данных в тело ответа.

* [Фильтры результатов](#result-filters) могут выполнять код непосредственно до и после выполнения результатов отдельного действия. Они выполняются только в том случае, если метод действия выполнен успешно. Они полезны для логики, которая должна включать исключения для представлений или модуля форматирования.

На приведенной ниже схеме показано, как типы фильтров взаимодействуют друг с другом в конвейере фильтров.

![Запрос обрабатывается посредством фильтров авторизации, фильтров ресурсов, привязки модели, фильтров действий, выполнения действия и преобразования результата действия, фильтров исключений, фильтров результатов и выполнения результатов. На обратном пути запрос обрабатывается только фильтрами результатов и фильтрами ресурсов, прежде чем стать ответом, отправляемым клиенту.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Реализация

Фильтры поддерживают как синхронные, так и асинхронные реализации посредством различных определений интерфейсов.

Синхронные фильтры могут выполнять код до (`On-Stage-Executing`) и после (`On-Stage-Executed`) этапа конвейера. Например, `OnActionExecuting` вызывается перед вызовом метода действия, а `OnActionExecuted` — после возврата метода действия.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Асинхронные фильтры определяют метод `On-Stage-ExecutionAsync`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

В приведенном выше коде `SampleAsyncActionFilter` включает <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) для выполнения метода действия.  Каждый из методов `On-Stage-ExecutionAsync` принимает `FilterType-ExecutionDelegate` для выполнения этапа конвейера фильтра.

### <a name="multiple-filter-stages"></a>Несколько этапов фильтра

Реализовать интерфейсы для нескольких этапов фильтра можно в одном классе. Например, класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> реализует интерфейсы `IActionFilter` и `IResultFilter`, а также их асинхронные эквиваленты.

Реализуйте синхронный **или** асинхронный интерфейс фильтра, но **не** оба варианта. Среда выполнения сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается именно он. В противном случае вызываются методы синхронного интерфейса. Если асинхронный и синхронный интерфейсы реализованы в одном классе, вызывается только асинхронный метод. При использовании абстрактных классов, таких как <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, следует переопределять только синхронные методы или асинхронный метод каждого типа фильтра.

### <a name="built-in-filter-attributes"></a>Встроенные атрибуты фильтров

ASP.NET Core включает встроенные фильтры на основе атрибутов, с помощью которых можно создавать подклассы и которые можно настраивать. Например, следующий фильтр результатов добавляет заголовок к ответу:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Атрибуты позволяют фильтрам принимать аргументы, как показано в примере выше. Примените `AddHeaderAttribute` к методу контроллера или действия, а затем укажите имя и значение заголовка HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

Некоторые интерфейсы фильтров имеют соответствующие атрибуты, которые можно использовать как базовые классы для пользовательских реализаций.

Атрибуты фильтров:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Области и порядок выполнения фильтров

Фильтр можно добавить в конвейер в одной из трех *областей*:

* С помощью атрибута в действии.
* С помощью атрибута в контроллере.
* Глобально для всех контроллеров и действий, как показано в следующем коде:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

Предыдущий код глобально добавляет три фильтра с помощью коллекции [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).

### <a name="default-order-of-execution"></a>Порядок выполнения по умолчанию

Если есть несколько *одинаковых* фильтров, область определяет порядок их выполнения по умолчанию.  Глобальные фильтры заключают в себя фильтры классов, которые, в свою очередь, заключают в себя фильтры методов.

В результате такого вложения *последующий* код фильтров выполняется в порядке, обратном выполнению *предшествующего* кода. Последовательность фильтров:

* *Предшествующий* код глобальных фильтров.
  * *Предшествующий* код фильтров контроллера.
    * *Предшествующий* код фильтров методов действий.
    * *Последующий* код фильтров методов действий.
  * *Последующий* код фильтров контроллера.
* *Последующий* код глобальных фильтров.
  
В следующем примере показан порядок вызова методов фильтров для синхронных фильтров действий.

| Последовательность | Область фильтра | Метод фильтра |
|:--------:|:------------:|:-------------:|
| 1 | Глобальный | `OnActionExecuting` |
| 2 | Контроллер | `OnActionExecuting` |
| 3 | Метод | `OnActionExecuting` |
| 4 | Метод | `OnActionExecuted` |
| 5 | Контроллер | `OnActionExecuted` |
| 6 | Глобальный | `OnActionExecuted` |

Эта последовательность показывает:

* Фильтр метода вкладывается в фильтр контроллера.
* Фильтр контроллера вкладывается в глобальный фильтр.

### <a name="controller-and-razor-page-level-filters"></a>RazorФильтры уровня контроллера и страницы

Каждый контроллер, наследуемый от базового класса <xref:Microsoft.AspNetCore.Mvc.Controller> включает методы [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) и [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Эти методы:

* Заключают фильтры, которые выполняются для указанного действия.
* `OnActionExecuting` вызывается перед всеми фильтрами действий.
* `OnActionExecuted` вызывается после всех фильтров действий.
* `OnActionExecutionAsync` вызывается перед всеми фильтрами действий. Код в фильтре после `next` выполняется после метода действия.

Например, в скачиваемом примере `MySampleActionFilter` применяется глобально при запуске.

`TestController`:

* Применяет `SampleActionFilterAttribute` (`[SampleActionFilter]`) для действия `FilterTest2`.
* Переопределяет `OnActionExecuting` и `OnActionExecuted`.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

Переходит к `https://localhost:5001/Test/FilterTest2` для выполнения следующего кода:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Для Razor страниц см. раздел [реализация Razor фильтров страниц путем переопределения методов фильтров](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Переопределение порядка по умолчанию

Чтобы переопределить порядок выполнения по умолчанию, можно реализовать <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>. `IOrderedFilter` предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, которое имеет приоритет над областью и определяет порядок выполнения. Фильтр со значением меньше `Order`:

* Выполняется *перед* кодом, выполняемым до фильтра с более высоким значением `Order`.
* Выполняется *после* кода, выполняемого после фильтра с более высоким значением `Order`.

Свойство `Order` можно задать с помощью параметра конструктора:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Рассмотрим три фильтра действий, показанные в предыдущем примере. Если свойство `Order` контроллера и глобальные фильтры имеют значения 1 и 2 соответственно, порядок выполнения инвертируется.

| Последовательность | Область фильтра | Свойство`Order` | Метод фильтра |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Метод | 0 | `OnActionExecuting` |
| 2 | Контроллер | 1  | `OnActionExecuting` |
| 3 | Глобальный | 2  | `OnActionExecuting` |
| 4 | Глобальный | 2  | `OnActionExecuted` |
| 5 | Контроллер | 1  | `OnActionExecuted` |
| 6 | Метод | 0  | `OnActionExecuted` |

Свойство `Order` переопределяет область при определении порядка выполнения фильтров. Фильтры сначала сортируются по порядку, а затем очередность окончательно определяется по области. Все встроенные фильтры реализуют `IOrderedFilter` и задают значение по умолчанию `Order`, равное 0. Во встроенных фильтрах область определяет порядок, если для `Order` не задано ненулевое значение.

## <a name="cancellation-and-short-circuiting"></a>Отмена и сокращенное выполнение

Вы можете настроить сокращенное выполнение конвейера фильтров, задав свойство <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> для параметра <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext>, передаваемого в метод фильтра. Например, приведенный ниже фильтр ресурсов предотвращает выполнение остальной части конвейера:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

В приведенном ниже коде как фильтр `ShortCircuitingResourceFilter`, так и фильтр `AddHeader` нацелены на метод действия `SomeResource`. `ShortCircuitingResourceFilter`:

* Выполняется первым, поскольку это фильтр ресурсов, а `AddHeader` — фильтр действий.
* Замыкает оставшуюся часть конвейера.

Поэтому фильтр `AddHeader` никогда не выполняется для действия `SomeResource`. Поведение будет аналогичным при применении обоих фильтров на уровне метода действия при условии, что фильтр `ShortCircuitingResourceFilter` выполняется первым. Фильтр `ShortCircuitingResourceFilter` выполняется в первую очередь из-за его типа или в связи с явным указанием свойства `Order`.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Внедрение зависимостей

Фильтры можно добавлять по типу или экземпляру. Добавляемый экземпляр используется для каждого запроса. Если добавляется тип, он является активированным. Активация фильтра означает, что:

* Экземпляр создается для каждого запроса.
* Зависимости конструктора заполняются путем [внедрения зависимостей](xref:fundamentals/dependency-injection).

Фильтры, которые реализуются как атрибуты и добавляются непосредственно в классы контроллеров или методы действий, не могут иметь зависимости конструктора, предоставленные посредством [внедрения зависимостей](xref:fundamentals/dependency-injection). Зависимости конструктора не могут предоставляться путем внедрения зависимостей, так как:

* Параметры конструктора должны предоставляться для атрибутов в месте их применения. 
* Это ограничение, налагаемое на использование атрибутов.

Следующие фильтры поддерживают зависимости конструктора, предоставленные путем внедрения зависимостей:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализуется в атрибуте.

Предыдущие фильтры могут применяться к контроллеру или методу действия.

Средства ведения журнала доступны путем внедрения зависимостей. Но следует избегать создания и использования фильтров исключительно для целей ведения журнала. [Встроенное средство ведения журнала](xref:fundamentals/logging/index) обычно предоставляет все необходимое для ведения журнала. При добавлении ведения журналов в фильтры следует учитывать следующее:

* Следует сосредоточиться на бизнес-среде или поведении в привязке к фильтру.
* **Не** следует регистрировать действия или другие события платформы, так как это делают встроенные фильтры.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Типы реализации фильтра службы регистрируются в `ConfigureServices`. Атрибут <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> извлекает экземпляр фильтра из внедрения зависимостей.

В следующем коде используется `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

В следующем коде в контейнер внедрения зависимостей добавляется `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

В следующем коде атрибут `ServiceFilter` извлекает экземпляр фильтра `AddHeaderResultServiceFilter` из внедрения зависимостей:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

При использовании `ServiceFilterAttribute` задание [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан. Среда выполнения ASP.NET Core не гарантирует:

  * что будет создан хотя бы один экземпляр фильтра;
  * что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.

* Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.

 Объект <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` загружает указанный тип из внедрения зависимостей.

### <a name="typefilterattribute"></a>TypeFilterAttribute

Атрибут <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> похож на <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, но его тип не разрешается напрямую из контейнера внедрения зависимостей. Он создает экземпляр типа с помощью <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.

Так как типы `TypeFilterAttribute` не разрешаются напрямую из контейнера внедрения зависимостей:

* Типы, на которые ссылаются с помощью `TypeFilterAttribute`, не нужно регистрировать в контейнере внедрения зависимостей.  Их зависимости выполняются самим контейнером.
* Атрибут `TypeFilterAttribute` может также принимать аргументы конструктора для типа.

При использовании `TypeFilterAttribute` задание [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан. Среда выполнения ASP.NET Core не предоставляет никаких гарантий, что будет создан хотя бы один экземпляр фильтра.

* Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.

В следующем примере показано, как передавать аргументы в тип с помощью `TypeFilterAttribute`:

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Фильтры авторизации

Фильтры авторизации:

* Выполняются в первую очередь в конвейере фильтров.
* Контролируют доступ к методам действий.
* Имеют предшествующий, но не последующий метод.

Для работы пользовательских фильтров авторизации требуется настраиваемая платформа авторизации. Настройка политик авторизации или определение пользовательской политики авторизации предпочтительнее создания пользовательского фильтра. Встроенный фильтр авторизации:

* Вызывает систему авторизации.
* Не выполняет авторизацию запросов.

**Не** вызывайте исключения в фильтрах авторизации:

* Исключение не будет обработано.
* Фильтры исключений не будут обрабатывать исключение.

При возникновении исключения в фильтре авторизации попробуйте создать запрос.

Дополнительные сведения об [авторизации](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Фильтры ресурсов

Фильтры ресурсов:

* Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* Выполнение охватывает большую часть конвейера фильтров.
* До фильтров ресурсов применяются только [фильтры авторизации](#authorization-filters).

Фильтры ресурсов полезны для сокращенного выполнения большей части конвейера. Например, фильтр кэширования предотвращает выполнение остальной части конвейера при попадании в кэше.

Примеры фильтров ресурсов:

* [Сокращенное выполнение фильтра ресурсов](#short-circuiting-resource-filter) показано ранее.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Предотвращает доступ привязки модели к данным формы.
  * Используется для загрузки больших файлов, если необходимо предотвратить считывание данных формы в память.

## <a name="action-filters"></a>Фильтры действий

> [!IMPORTANT]
> Фильтры действий **не** применяются к Razor страницам. Razor Страницы поддерживают <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).

Фильтры действий:

* Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* Их выполнение охватывает выполнение методов действия.

В следующем фрагменте кода показан пример фильтра действий:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> предоставляет следующие свойства:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> позволяет считать входные данные в метод действия.
* <xref:Microsoft.AspNetCore.Mvc.Controller> позволяет управлять экземпляром контроллера.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> позволяет при задании свойства `Result` сократить выполнение метода действия и последующих фильтров действий.

Вызов исключения в методе действия:

* Предотвращает выполнение последующих фильтров.
* В отличие от параметра `Result` рассматривается как сбой, а не успешный результат.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> предоставляет `Controller` и `Result`, а также следующие свойства:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> будет иметь ненулевое значение, если предыдущее выполнение фильтра действий вызвало исключение. При присвоении этому свойству ненулевого значения:

  * Будет эффективно обрабатываться исключение.
  * `Result` будет выполняться так, как при возвращении методом действия.

Для `IAsyncActionFilter` вызов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:

* Приводит к выполнению последующих фильтров действий и метода действия.
* Возвращает `ActionExecutedContext`.

Для сокращенного выполнения присвойте <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> экземпляру результата и не вызывайте `next` (`ActionExecutionDelegate`).

Платформа предоставляет абстрактный класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, для которого можно создавать подклассы.

Фильтр действий `OnActionExecuting` можно использовать:

* Для проверки состояния модели.
* Для получения ошибки, если состояние является недопустимым.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

Метод `OnActionExecuted` выполняется после метода действия:

* Он имеет доступ к результатам действия и может управлять ими с помощью свойства <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> будет иметь ненулевое значение, если действие или последующий фильтр действий вызвали исключение. Если установить для `Exception` значение NULL:

  * Будет эффективно обрабатываться исключение.
  * `ActionExecutedContext.Result` будет выполняться так, как если бы метод действия вернул его обычным образом.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Фильтры исключений

Фильтры исключений:

* Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>. 
* Можно использовать для реализации политик обработки стандартных ошибок.

В следующем примере фильтра исключений используется пользовательское представление ошибок для отображения подробных сведений об исключениях, которые происходят во время разработки приложения:

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Фильтры исключений:

* Не имеют предшествующих и последующих событий.
* Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Обрабатывать необработанные исключения, происходящие при Razor создании страницы или контроллера, [Привязка модели](xref:mvc/models/model-binding), фильтры действий или методы действий.
* **Не** перехватывают исключения, которые возникают в фильтрах ресурсов, фильтрах результатов или при выполнении результата MVC.

Для обработки исключения присвойте свойству <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> значение `true` или напишите ответ. Это предотвратит распространение исключения. Фильтр исключений не может преобразовать исключение в успешное выполнение. Это может сделать только фильтр действий.

Фильтры исключений:

* Хорошо подходят для перехвата исключений, возникающих в действиях.
* Не обладает такой гибкостью, как ПО промежуточного слоя обработки ошибок.

ПО промежуточного слоя хорошо подходит для обработки исключений. Используйте фильтры исключений, только если обработка ошибок осуществляется *по-разному* с учетом вызванного метода действия. Например, в приложении могут использоваться методы действий как для конечных точек API, так и для представлений или HTML. Конечные точки API могут возвращать сведения об ошибках в формате JSON, в то время как действия на основе представлений могут возвращать страницу ошибки в формате HTML.

## <a name="result-filters"></a>Фильтры результатов

Фильтры результатов:

* Реализация интерфейса:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> либо <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Их выполнение охватывает выполнение результатов действий.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter и IAsyncResultFilter

В следующем фрагменте кода показан фильтр результатов, который добавляет заголовок HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Тип выполняемого результата зависит от соответствующего действия. Действие, возвращающее представление, будет включать всю обработку Razor в рамках выполняемого объекта <xref:Microsoft.AspNetCore.Mvc.ViewResult>. В процессе выполнения результата метод API может производить сериализацию. Дополнительные сведения о [результатах действий](xref:mvc/controllers/actions).

Фильтры результатов выполняются только в том случае, когда действие или фильтры действий предоставляют результат действия. Фильтры результатов не выполняются в следующих случаях:

* Фильтр авторизации или ресурсов выполняет сокращение конвейера.
* Фильтр исключений обрабатывает исключение и выдает результат действия.

Метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> может сокращать выполнение результата действия и последующих фильтров результатов, присваивая свойству <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> значение `true`. При сокращении выполнения выполните запись в объект ответа, чтобы избежать формирования пустого ответа. Вызов исключения в `IResultFilter.OnResultExecuting`:

* Предотвращает выполнение результата действия и последующих фильтров.
* Рассматривается как сбой, а не успешный результат.

Если запускается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, ответ, скорее всего, уже был отправлен клиенту. Если ответ уже был отправлен клиенту его больше нельзя изменить.

`ResultExecutedContext.Canceled` будет иметь значение `true`, если выполнение результата действия было сокращено другим фильтром.

`ResultExecutedContext.Exception` будет иметь ненулевое значение, если результат действия или последующий фильтр результатов вызвали исключение. Присвоение `Exception` значения NULL приводит к обработке исключения и предотвращает его последующий вызов ASP.NET Core на дальнейших этапах конвейера. Надежный способ записи данных в ответ при обработке исключения в фильтре результатов отсутствует. Если результат действия вызывает исключение в процессе выполнения и заголовки уже были переданы в клиент, надежного механизма отправки кода сбоя не существует.

Для <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> вызов к `await next` для <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> приводит к выполнению последующих фильтров результатов и результата действия. Для сокращения выполнения задайте [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) для `true` и не вызывайте `ResultExecutionDelegate`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Платформа предоставляет абстрактный класс `ResultFilterAttribute`, для которого можно создавать подклассы. Представленный ранее класс [AddHeaderAttribute](#add-header-attribute) — это пример атрибута фильтра результатов.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter и IAsyncAlwaysRunResultFilter

Интерфейсы <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> объявляют реализацию <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>, которая выполняется для всех результатов действий. Сюда включены результаты действий, созданные:

* фильтрами авторизации и фильтрами ресурсов, которые сокращают ответ;
* фильтрами исключений.

Например, следующий фильтр всегда выполняется, задавая результат действия (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) с кодом состояния *422 Unprocessable Entity* при сбое согласования содержимого:

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

Объект <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Поэтому экземпляр `IFilterFactory` можно использовать в качестве экземпляра `IFilterMetadata` в любом месте конвейера фильтров. Когда среда выполнения готовится вызвать фильтр, она пытается привести его к `IFilterFactory`. Если приведение проходит успешно, вызывается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания вызываемого экземпляра `IFilterMetadata`. Это обеспечивает высокую степень гибкости, так как при запуске приложения нет необходимости в явном и точном определении конвейера фильтров.

Еще один подход к созданию фильтров заключается в реализации `IFilterFactory` с помощью настраиваемых атрибутов:

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Приведенный выше код можно проверить, выполнив [скачиваемый пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):

* Вызовите средства для разработчика (F12).
* Перейдите на страницу `https://localhost:5001/Sample/HeaderWithFactory`.

В средствах для разработчика (F12) отобразятся следующие заголовки ответа, добавленные примером кода:

* **Автор:**`Joe Smith`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **внутренний:**`My header`

Приведенный выше код создает заголовок ответа **internal:** `My header`.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>Реализация IFilterFactory в атрибуте

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Фильтры, реализующие `IFilterFactory`, используются для фильтров, которые:

* Не требуют передачи параметров.
* Содержат зависимости конструктора, которые должны заполняться путем внедрения зависимостей.

Объект <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` загружает указанный тип из контейнера служб (внедрение зависимостей).

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

В коде ниже приведено три примера применения `[SampleActionFilter]`:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

В приведенном выше коде декорирование метода с использованием `[SampleActionFilter]` является рекомендуемым способом применения `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Использование ПО промежуточного слоя в конвейере фильтров

Фильтры ресурсов по принципу работы похожи на [ПО промежуточного слоя](xref:fundamentals/middleware/index) тем, что они заключают в себя выполнение всех объектов, находящихся далее в конвейере. При этом фильтры отличаются от ПО промежуточного слоя тем, что они являются частью среды выполнения ASP.NET Core, то есть они имеют доступ к контексту и конструкциям ASP.NET Core.

Чтобы использовать ПО промежуточного слоя в качестве фильтра, создайте тип с методом `Configure`, определяющим ПО промежуточного слоя, которое нужно внедрить в конвейер фильтров. В примере ниже ПО промежуточного слоя локализации применяется для определения текущих языка и региональных параметров для запроса:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Используйте <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> для выполнения ПО промежуточного слоя:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Фильтры ПО промежуточного слоя выполняются на том же этапе конвейера фильтров, что и фильтры ресурсов, перед привязкой модели и после остальной части конвейера.

## <a name="next-actions"></a>Дальнейшие действия

* См. раздел [методы фильтров для Razor страниц](xref:razor-pages/filter).
* Чтобы поэкспериментировать с фильтрами, [скачайте, протестируйте и измените пример с GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

::: moniker-end
