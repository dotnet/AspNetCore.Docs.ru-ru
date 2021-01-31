---
title: Работа с моделью приложения в ASP.NET Core
author: ardalis
description: Узнайте, как читать и обрабатывать модель приложения, чтобы изменить поведение элементов MVC в ASP.NET Core.
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/application-model
ms.openlocfilehash: cf16536284ee9c88913257607df837ad6e50ea2c
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217379"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>Работа с моделью приложения в ASP.NET Core

Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)

В ASP.NET MVC существует *модель приложения*, представляющая компоненты приложения MVC. Управляя этой моделью, можно изменить поведение элементов MVC. По умолчанию в MVC используется ряд соглашений для определения того, какие классы считаются контроллерами, какие методы этих классов являются действиями и каким образом работают параметры и маршрутизация. Чтобы настроить это поведение в соответствии с потребностями приложения, рекомендуется создать собственные соглашения и применить их глобально или в виде атрибутов.

## <a name="models-and-providers"></a>Модели и поставщики

Модель приложения ASP.NET Core MVC включает как абстрактные интерфейсы, так и конкретные классы реализации, описывающие приложение MVC. Эта модель формируется в результате обнаружения MVC контроллеров, действий, параметров действий, маршрутов и фильтров приложения в соответствии с соглашениями по умолчанию. Работая с моделью приложения, можно изменить приложение так, чтобы для него действовали соглашения, отличные от поведения MVC по умолчанию. Параметры, имена, маршруты и фильтры используются в качестве данных конфигурации для действий и контроллеров.

Структура модели приложения ASP.NET Core MVC выглядит следующим образом:

* ApplicationModel
  * Контроллеры (ControllerModel)
    * Действия (ActionModel)
      * Параметры (ParameterModel)

Каждый уровень модели имеет доступ к общей коллекции `Properties`, а более низкие уровни могут обращаться к значениям свойств, заданным на более высоких уровнях иерархии, и перезаписывать их. Свойства сохраняются в `ActionDescriptor.Properties` при создании действий. Затем при обработке запроса доступ ко всем свойствам, добавленным или измененным в рамках соглашения, может осуществляться с помощью `ActionContext.ActionDescriptor.Properties`. Использование свойств является хорошим способом настроить фильтры, связыватели моделей и т. д. отдельно для каждого действия.

> [!NOTE]
> После запуска приложения коллекция `ActionDescriptor.Properties` не становится потокобезопасной (для операций записи). Для безопасного добавления данных в эту коллекцию лучше всего подходят соглашения.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

ASP.NET Core MVC загружает модель приложения с помощью шаблона поставщика, определяемого интерфейсом [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider). В этом разделе приводятся некоторые сведения о внутренней реализации функциональности этого поставщика. Это довольно сложная тема — в большинстве приложений, использующих модель приложения, для выполнения этой задачи требуются соглашения.

Реализации интерфейса `IApplicationModelProvider` создают оболочку друг для друга — каждая реализация вызывает `OnProvidersExecuting` по возрастанию на основе его свойства `Order`. Затем в обратном порядке вызывается метод `OnProvidersExecuted`. Платформа определяет несколько поставщиков:

Сначала (`Order=-1000`):

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Затем (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> Порядок вызова двух поставщиков с одним и тем же значением для `Order` не определен, поэтому на него не следует полагаться.

> [!NOTE]
> `IApplicationModelProvider` является перспективным направлением для разработчиков платформы. Как правило, в приложениях нужно использовать соглашения, а на платформах — поставщики. Основное различие заключается в том, что поставщики всегда запускаются перед соглашениями.

Поставщик `DefaultApplicationModelProvider` формирует множество вариантов поведения по умолчанию, используемых в ASP.NET Core MVC. В его обязанности входит:

* добавление глобальных фильтров в контекст;
* добавление контроллеров в контекст;
* добавление открытых методов контроллера в качестве действий;
* добавление параметров методов действий в контекст;
* применение маршрута и других атрибутов.

Поставщик `DefaultApplicationModelProvider` реализует некоторые встроенные поведения. Этот поставщик отвечает за создание [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) , который, в свою очередь, ссылается [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) на [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) экземпляры, и [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) . Класс `DefaultApplicationModelProvider` является элементом внутренней реализации структуры, который может быть изменен и изменится в будущем. 

Поставщик `AuthorizationApplicationModelProvider` занимается применением поведения, связанным с атрибутами `AuthorizeFilter` и `AllowAnonymousFilter`. [Дополнительные сведения об этих атрибутах](xref:security/authorization/simple).

Поставщик `CorsApplicationModelProvider` реализует поведение, связанное с `IEnableCorsAttribute` и `IDisableCorsAttribute`, а также с `DisableCorsAuthorizationFilter`. [Дополнительные сведения о CORS](xref:security/cors).

## <a name="conventions"></a>Соглашения

Модель приложения определяет абстракции соглашения, которые предоставляют простой способ настройки поведения моделей вместо переопределения всей модели или поставщика. Эти абстракции являются рекомендуемыми для изменения режима работы приложения. На основе соглашений можно писать код, который будет динамически применять настройки. Несмотря на то, что с помощью [фильтров](xref:mvc/controllers/filters) можно изменить поведение платформы, благодаря настройкам осуществляется контроль работы всего приложения в целом.

Доступны следующие соглашения:

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

Соглашения применяются путем их добавления в параметры MVC или реализацией `Attribute` s и их применения к контроллерам, действиям или параметрам действий (аналогично [`Filters`](xref:mvc/controllers/filters) ). В отличие от фильтров соглашения выполняются только при запуске приложения, а не в составе каждого запроса.

### <a name="sample-modifying-the-applicationmodel"></a>Пример: изменение ApplicationModel

Для добавления свойства в модель приложения используется следующее соглашение. 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

Соглашения для модели приложения применяются в виде параметров при добавлении MVC в `ConfigureServices` в `Startup`.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

Свойства доступны из коллекции свойств `ActionDescriptor` в действиях контроллера:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>Пример: изменение описания ControllerModel

Как и в предыдущем примере, модель контроллера можно изменить для включения в нее настраиваемых свойств. Они переопределят существующие свойства с тем же именем, указанным в модели приложения. Следующий атрибут соглашения добавляет описание на уровне контроллера:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

Это соглашение будет применяться в качестве атрибута в контроллере.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

Доступ к свойству "description" осуществляется так же, как и в предыдущих примерах.

### <a name="sample-modifying-the-actionmodel-description"></a>Пример: изменение описания ActionModel

К отдельным действиям может применяться индивидуальное соглашение об атрибутах, переопределяющее поведения, уже примененные на уровне приложения или контроллера.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

Его применение к действию в предыдущем примере с контроллером демонстрирует, как оно переопределяет соглашение уровня контроллера:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>Пример: изменение ParameterModel

Следующее соглашение может применяться к параметрам действия для изменения их класса `BindingInfo`. Для следующего соглашения требуется, чтобы параметр был параметром маршрута. Другие возможные источники привязки (например, значения строки запроса) не учитываются.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

Атрибут можно применять к любому параметру действия:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>Пример: изменение имени ActionModel

Следующее соглашение изменяет класс `ActionModel` для обновления *имени* действия, к которому оно применяется. Новое имя указывается в качестве параметра для атрибута. Имя используется при маршрутизации, поэтому оно повлияет на маршрут для доступа к этому методу действия.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

Этот атрибут применяется к методу действия в классе `HomeController`:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

Даже несмотря на то, что методу задано имя `SomeName`, атрибут переопределяет соглашение MVC об использовании имени метода и заменяет имя действия на `MyCoolAction`. Таким образом, для доступа к этому действию используется маршрут `/Home/MyCoolAction`.

> [!NOTE]
> Этот пример по существу является аналогом использования встроенного атрибута [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute).

### <a name="sample-custom-routing-convention"></a>Пример: соглашение о настраиваемой маршрутизации

Настроить поведение маршрутизации можно с помощью `IApplicationModelConvention`. Например, следующее соглашение будет включать пространства имен контроллеров в маршруты, заменяя `.` в пространстве имен на `/` в маршруте:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

Соглашение добавляется в качестве параметра при запуске.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> Соглашения можно добавить в [ПО промежуточного слоя](xref:fundamentals/middleware/index), обратившись к `MvcOptions` с помощью `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`.

В этом примере это соглашение применяется к маршрутам, не использующим маршрутизацию атрибутов, когда в имени контроллера содержится "Namespace". Соглашение демонстрируется в следующем контроллере:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>Использование модели приложения в WebApiCompatShim

ASP.NET Core MVC использует набор соглашений, отличный от соглашений ASP.NET Web API 2. С помощью настраиваемых соглашений можно изменить поведение приложения ASP.NET Core MVC так, чтобы оно соответствовало режиму работы приложения веб-API. Специально для этого корпорация Майкрософт поставляет пакет [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/).

> [!NOTE]
> Узнайте больше о [переходе с веб-API ASP.NET](xref:migration/webapi).

Чтобы использовать оболочку совместимости Web API Compatibility Shim, необходимо добавить пакет в проект и затем добавить соглашения в MVC, вызвав `AddWebApiConventions` в `Startup`:

```csharp
services.AddMvc().AddWebApiConventions();
```

Соглашения, предоставляемые оболочкой, применяются только к тем частям приложения, к которым уже применены определенные атрибуты. Для управления контроллерами, соглашения которых должны быть изменены с помощью соглашений оболочки, используются следующие четыре атрибута:

* [UseWebApiActionConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>Соглашения о действиях

Атрибут `UseWebApiActionConventionsAttribute` используется для сопоставления метода HTTP с действиями на основе их имен (например, `Get` сопоставляется с `HttpGet`). Он применяется только к действиям без маршрутизации атрибутов.

### <a name="overloading"></a>Перегрузка

Атрибут `UseWebApiOverloadingAttribute` используется для применения соглашения `WebApiOverloadingApplicationModelConvention`. Это соглашение добавляет `OverloadActionConstraint` в процесс выбора действия, ограничивая потенциальные действия теми, для которых запрос удовлетворяет всем обязательным параметрам.

### <a name="parameter-conventions"></a>Соглашения о параметрах

Атрибут `UseWebApiParameterConventionsAttribute` используется для применения соглашения о действиях `WebApiParameterConventionsApplicationModelConvention`. Это соглашение указывает, что простые типы, используемые в качестве параметров действия, привязываются из URI по умолчанию, тогда как сложные типы привязываются из текста запроса.

### <a name="routes"></a>Маршруты

Атрибут `UseWebApiRoutesAttribute` контролирует применение соглашение контроллера `WebApiApplicationModelConvention`. Активированное соглашение используется для добавления поддержки для [областей](xref:mvc/controllers/areas) в маршрут.

Помимо набора соглашений в пакет совместимости входит базовый класс `System.Web.Http.ApiController`, который заменяет класс, предоставляемый веб-API. В этом случае контроллеры, написанные для веб-API и наследующие от его `ApiController`, будут функционировать так, как предусмотрено, работая при этом в ASP.NET Core MVC. Все указанные ранее атрибуты `UseWebApi*` применяются к базовому классу контроллера. `ApiController` предоставляет свойства, методы и типы результатов, совместимые с их аналогами в веб-API.

## <a name="using-apiexplorer-to-document-your-app"></a>Использование ApiExplorer для документирования приложения

Модель приложения предоставляет [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) свойство на каждом уровне, которое можно использовать для прохода по структуре приложения. Такая ситуация походит для [создания страниц справки для веб-API с помощью таких средств, как Swagger](xref:tutorials/web-api-help-pages-using-swagger). Свойство `ApiExplorer` предоставляет свойство `IsVisible`, которое можно задать, чтобы указать, какие части модели приложения следует предоставить. Этот параметр настраивается с помощью соглашения:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

Этот подход (и дополнительные соглашения, если необходимо) позволяет включать или отключать видимость API на любом уровне в приложении. 
