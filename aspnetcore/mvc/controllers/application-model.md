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
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="28ae4-103">Работа с моделью приложения в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28ae4-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="28ae4-104">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="28ae4-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="28ae4-105">В ASP.NET MVC существует *модель приложения*, представляющая компоненты приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="28ae4-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="28ae4-106">Управляя этой моделью, можно изменить поведение элементов MVC.</span><span class="sxs-lookup"><span data-stu-id="28ae4-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="28ae4-107">По умолчанию в MVC используется ряд соглашений для определения того, какие классы считаются контроллерами, какие методы этих классов являются действиями и каким образом работают параметры и маршрутизация.</span><span class="sxs-lookup"><span data-stu-id="28ae4-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="28ae4-108">Чтобы настроить это поведение в соответствии с потребностями приложения, рекомендуется создать собственные соглашения и применить их глобально или в виде атрибутов.</span><span class="sxs-lookup"><span data-stu-id="28ae4-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="28ae4-109">Модели и поставщики</span><span class="sxs-lookup"><span data-stu-id="28ae4-109">Models and Providers</span></span>

<span data-ttu-id="28ae4-110">Модель приложения ASP.NET Core MVC включает как абстрактные интерфейсы, так и конкретные классы реализации, описывающие приложение MVC.</span><span class="sxs-lookup"><span data-stu-id="28ae4-110">The ASP.NET Core MVC application model includes both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="28ae4-111">Эта модель формируется в результате обнаружения MVC контроллеров, действий, параметров действий, маршрутов и фильтров приложения в соответствии с соглашениями по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="28ae4-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="28ae4-112">Работая с моделью приложения, можно изменить приложение так, чтобы для него действовали соглашения, отличные от поведения MVC по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="28ae4-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="28ae4-113">Параметры, имена, маршруты и фильтры используются в качестве данных конфигурации для действий и контроллеров.</span><span class="sxs-lookup"><span data-stu-id="28ae4-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="28ae4-114">Структура модели приложения ASP.NET Core MVC выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="28ae4-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="28ae4-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="28ae4-115">ApplicationModel</span></span>
  * <span data-ttu-id="28ae4-116">Контроллеры (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="28ae4-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="28ae4-117">Действия (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="28ae4-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="28ae4-118">Параметры (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="28ae4-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="28ae4-119">Каждый уровень модели имеет доступ к общей коллекции `Properties`, а более низкие уровни могут обращаться к значениям свойств, заданным на более высоких уровнях иерархии, и перезаписывать их.</span><span class="sxs-lookup"><span data-stu-id="28ae4-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="28ae4-120">Свойства сохраняются в `ActionDescriptor.Properties` при создании действий.</span><span class="sxs-lookup"><span data-stu-id="28ae4-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="28ae4-121">Затем при обработке запроса доступ ко всем свойствам, добавленным или измененным в рамках соглашения, может осуществляться с помощью `ActionContext.ActionDescriptor.Properties`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="28ae4-122">Использование свойств является хорошим способом настроить фильтры, связыватели моделей и т. д. отдельно для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="28ae4-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="28ae4-123">После запуска приложения коллекция `ActionDescriptor.Properties` не становится потокобезопасной (для операций записи).</span><span class="sxs-lookup"><span data-stu-id="28ae4-123">The `ActionDescriptor.Properties` collection isn't thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="28ae4-124">Для безопасного добавления данных в эту коллекцию лучше всего подходят соглашения.</span><span class="sxs-lookup"><span data-stu-id="28ae4-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="28ae4-125">IApplicationModelProvider</span><span class="sxs-lookup"><span data-stu-id="28ae4-125">IApplicationModelProvider</span></span>

<span data-ttu-id="28ae4-126">ASP.NET Core MVC загружает модель приложения с помощью шаблона поставщика, определяемого интерфейсом [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider).</span><span class="sxs-lookup"><span data-stu-id="28ae4-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="28ae4-127">В этом разделе приводятся некоторые сведения о внутренней реализации функциональности этого поставщика.</span><span class="sxs-lookup"><span data-stu-id="28ae4-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="28ae4-128">Это довольно сложная тема — в большинстве приложений, использующих модель приложения, для выполнения этой задачи требуются соглашения.</span><span class="sxs-lookup"><span data-stu-id="28ae4-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="28ae4-129">Реализации интерфейса `IApplicationModelProvider` создают оболочку друг для друга — каждая реализация вызывает `OnProvidersExecuting` по возрастанию на основе его свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="28ae4-130">Затем в обратном порядке вызывается метод `OnProvidersExecuted`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="28ae4-131">Платформа определяет несколько поставщиков:</span><span class="sxs-lookup"><span data-stu-id="28ae4-131">The framework defines several providers:</span></span>

<span data-ttu-id="28ae4-132">Сначала (`Order=-1000`):</span><span class="sxs-lookup"><span data-stu-id="28ae4-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="28ae4-133">Затем (`Order=-990`):</span><span class="sxs-lookup"><span data-stu-id="28ae4-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="28ae4-134">Порядок вызова двух поставщиков с одним и тем же значением для `Order` не определен, поэтому на него не следует полагаться.</span><span class="sxs-lookup"><span data-stu-id="28ae4-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="28ae4-135">`IApplicationModelProvider` является перспективным направлением для разработчиков платформы.</span><span class="sxs-lookup"><span data-stu-id="28ae4-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="28ae4-136">Как правило, в приложениях нужно использовать соглашения, а на платформах — поставщики.</span><span class="sxs-lookup"><span data-stu-id="28ae4-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="28ae4-137">Основное различие заключается в том, что поставщики всегда запускаются перед соглашениями.</span><span class="sxs-lookup"><span data-stu-id="28ae4-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="28ae4-138">Поставщик `DefaultApplicationModelProvider` формирует множество вариантов поведения по умолчанию, используемых в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="28ae4-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="28ae4-139">В его обязанности входит:</span><span class="sxs-lookup"><span data-stu-id="28ae4-139">Its responsibilities include:</span></span>

* <span data-ttu-id="28ae4-140">добавление глобальных фильтров в контекст;</span><span class="sxs-lookup"><span data-stu-id="28ae4-140">Adding global filters to the context</span></span>
* <span data-ttu-id="28ae4-141">добавление контроллеров в контекст;</span><span class="sxs-lookup"><span data-stu-id="28ae4-141">Adding controllers to the context</span></span>
* <span data-ttu-id="28ae4-142">добавление открытых методов контроллера в качестве действий;</span><span class="sxs-lookup"><span data-stu-id="28ae4-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="28ae4-143">добавление параметров методов действий в контекст;</span><span class="sxs-lookup"><span data-stu-id="28ae4-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="28ae4-144">применение маршрута и других атрибутов.</span><span class="sxs-lookup"><span data-stu-id="28ae4-144">Applying route and other attributes</span></span>

<span data-ttu-id="28ae4-145">Поставщик `DefaultApplicationModelProvider` реализует некоторые встроенные поведения.</span><span class="sxs-lookup"><span data-stu-id="28ae4-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="28ae4-146">Этот поставщик отвечает за создание [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) , который, в свою очередь, ссылается [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) на [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) экземпляры, и [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) .</span><span class="sxs-lookup"><span data-stu-id="28ae4-146">This provider is responsible for constructing the [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) instances.</span></span> <span data-ttu-id="28ae4-147">Класс `DefaultApplicationModelProvider` является элементом внутренней реализации структуры, который может быть изменен и изменится в будущем.</span><span class="sxs-lookup"><span data-stu-id="28ae4-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="28ae4-148">Поставщик `AuthorizationApplicationModelProvider` занимается применением поведения, связанным с атрибутами `AuthorizeFilter` и `AllowAnonymousFilter`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="28ae4-149">[Дополнительные сведения об этих атрибутах](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="28ae4-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="28ae4-150">Поставщик `CorsApplicationModelProvider` реализует поведение, связанное с `IEnableCorsAttribute` и `IDisableCorsAttribute`, а также с `DisableCorsAuthorizationFilter`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="28ae4-151">[Дополнительные сведения о CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="28ae4-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="28ae4-152">Соглашения</span><span class="sxs-lookup"><span data-stu-id="28ae4-152">Conventions</span></span>

<span data-ttu-id="28ae4-153">Модель приложения определяет абстракции соглашения, которые предоставляют простой способ настройки поведения моделей вместо переопределения всей модели или поставщика.</span><span class="sxs-lookup"><span data-stu-id="28ae4-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="28ae4-154">Эти абстракции являются рекомендуемыми для изменения режима работы приложения.</span><span class="sxs-lookup"><span data-stu-id="28ae4-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="28ae4-155">На основе соглашений можно писать код, который будет динамически применять настройки.</span><span class="sxs-lookup"><span data-stu-id="28ae4-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="28ae4-156">Несмотря на то, что с помощью [фильтров](xref:mvc/controllers/filters) можно изменить поведение платформы, благодаря настройкам осуществляется контроль работы всего приложения в целом.</span><span class="sxs-lookup"><span data-stu-id="28ae4-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app works together.</span></span>

<span data-ttu-id="28ae4-157">Доступны следующие соглашения:</span><span class="sxs-lookup"><span data-stu-id="28ae4-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="28ae4-158">Соглашения применяются путем их добавления в параметры MVC или реализацией `Attribute` s и их применения к контроллерам, действиям или параметрам действий (аналогично [`Filters`](xref:mvc/controllers/filters) ).</span><span class="sxs-lookup"><span data-stu-id="28ae4-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="28ae4-159">В отличие от фильтров соглашения выполняются только при запуске приложения, а не в составе каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="28ae4-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="28ae4-160">Пример: изменение ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="28ae4-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="28ae4-161">Для добавления свойства в модель приложения используется следующее соглашение.</span><span class="sxs-lookup"><span data-stu-id="28ae4-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="28ae4-162">Соглашения для модели приложения применяются в виде параметров при добавлении MVC в `ConfigureServices` в `Startup`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="28ae4-163">Свойства доступны из коллекции свойств `ActionDescriptor` в действиях контроллера:</span><span class="sxs-lookup"><span data-stu-id="28ae4-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="28ae4-164">Пример: изменение описания ControllerModel</span><span class="sxs-lookup"><span data-stu-id="28ae4-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="28ae4-165">Как и в предыдущем примере, модель контроллера можно изменить для включения в нее настраиваемых свойств.</span><span class="sxs-lookup"><span data-stu-id="28ae4-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="28ae4-166">Они переопределят существующие свойства с тем же именем, указанным в модели приложения.</span><span class="sxs-lookup"><span data-stu-id="28ae4-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="28ae4-167">Следующий атрибут соглашения добавляет описание на уровне контроллера:</span><span class="sxs-lookup"><span data-stu-id="28ae4-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="28ae4-168">Это соглашение будет применяться в качестве атрибута в контроллере.</span><span class="sxs-lookup"><span data-stu-id="28ae4-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="28ae4-169">Доступ к свойству "description" осуществляется так же, как и в предыдущих примерах.</span><span class="sxs-lookup"><span data-stu-id="28ae4-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="28ae4-170">Пример: изменение описания ActionModel</span><span class="sxs-lookup"><span data-stu-id="28ae4-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="28ae4-171">К отдельным действиям может применяться индивидуальное соглашение об атрибутах, переопределяющее поведения, уже примененные на уровне приложения или контроллера.</span><span class="sxs-lookup"><span data-stu-id="28ae4-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="28ae4-172">Его применение к действию в предыдущем примере с контроллером демонстрирует, как оно переопределяет соглашение уровня контроллера:</span><span class="sxs-lookup"><span data-stu-id="28ae4-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="28ae4-173">Пример: изменение ParameterModel</span><span class="sxs-lookup"><span data-stu-id="28ae4-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="28ae4-174">Следующее соглашение может применяться к параметрам действия для изменения их класса `BindingInfo`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="28ae4-175">Для следующего соглашения требуется, чтобы параметр был параметром маршрута. Другие возможные источники привязки (например, значения строки запроса) не учитываются.</span><span class="sxs-lookup"><span data-stu-id="28ae4-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="28ae4-176">Атрибут можно применять к любому параметру действия:</span><span class="sxs-lookup"><span data-stu-id="28ae4-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="28ae4-177">Пример: изменение имени ActionModel</span><span class="sxs-lookup"><span data-stu-id="28ae4-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="28ae4-178">Следующее соглашение изменяет класс `ActionModel` для обновления *имени* действия, к которому оно применяется.</span><span class="sxs-lookup"><span data-stu-id="28ae4-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="28ae4-179">Новое имя указывается в качестве параметра для атрибута.</span><span class="sxs-lookup"><span data-stu-id="28ae4-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="28ae4-180">Имя используется при маршрутизации, поэтому оно повлияет на маршрут для доступа к этому методу действия.</span><span class="sxs-lookup"><span data-stu-id="28ae4-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="28ae4-181">Этот атрибут применяется к методу действия в классе `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="28ae4-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="28ae4-182">Даже несмотря на то, что методу задано имя `SomeName`, атрибут переопределяет соглашение MVC об использовании имени метода и заменяет имя действия на `MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="28ae4-183">Таким образом, для доступа к этому действию используется маршрут `/Home/MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="28ae4-184">Этот пример по существу является аналогом использования встроенного атрибута [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute).</span><span class="sxs-lookup"><span data-stu-id="28ae4-184">This example is essentially the same as using the built-in [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="28ae4-185">Пример: соглашение о настраиваемой маршрутизации</span><span class="sxs-lookup"><span data-stu-id="28ae4-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="28ae4-186">Настроить поведение маршрутизации можно с помощью `IApplicationModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="28ae4-187">Например, следующее соглашение будет включать пространства имен контроллеров в маршруты, заменяя `.` в пространстве имен на `/` в маршруте:</span><span class="sxs-lookup"><span data-stu-id="28ae4-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="28ae4-188">Соглашение добавляется в качестве параметра при запуске.</span><span class="sxs-lookup"><span data-stu-id="28ae4-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="28ae4-189">Соглашения можно добавить в [ПО промежуточного слоя](xref:fundamentals/middleware/index), обратившись к `MvcOptions` с помощью `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-189">You can add conventions to your [middleware](xref:fundamentals/middleware/index) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="28ae4-190">В этом примере это соглашение применяется к маршрутам, не использующим маршрутизацию атрибутов, когда в имени контроллера содержится "Namespace".</span><span class="sxs-lookup"><span data-stu-id="28ae4-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="28ae4-191">Соглашение демонстрируется в следующем контроллере:</span><span class="sxs-lookup"><span data-stu-id="28ae4-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="28ae4-192">Использование модели приложения в WebApiCompatShim</span><span class="sxs-lookup"><span data-stu-id="28ae4-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="28ae4-193">ASP.NET Core MVC использует набор соглашений, отличный от соглашений ASP.NET Web API 2.</span><span class="sxs-lookup"><span data-stu-id="28ae4-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="28ae4-194">С помощью настраиваемых соглашений можно изменить поведение приложения ASP.NET Core MVC так, чтобы оно соответствовало режиму работы приложения веб-API.</span><span class="sxs-lookup"><span data-stu-id="28ae4-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="28ae4-195">Специально для этого корпорация Майкрософт поставляет пакет [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/).</span><span class="sxs-lookup"><span data-stu-id="28ae4-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="28ae4-196">Узнайте больше о [переходе с веб-API ASP.NET](xref:migration/webapi).</span><span class="sxs-lookup"><span data-stu-id="28ae4-196">Learn more about [migration from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="28ae4-197">Чтобы использовать оболочку совместимости Web API Compatibility Shim, необходимо добавить пакет в проект и затем добавить соглашения в MVC, вызвав `AddWebApiConventions` в `Startup`:</span><span class="sxs-lookup"><span data-stu-id="28ae4-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="28ae4-198">Соглашения, предоставляемые оболочкой, применяются только к тем частям приложения, к которым уже применены определенные атрибуты.</span><span class="sxs-lookup"><span data-stu-id="28ae4-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="28ae4-199">Для управления контроллерами, соглашения которых должны быть изменены с помощью соглашений оболочки, используются следующие четыре атрибута:</span><span class="sxs-lookup"><span data-stu-id="28ae4-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="28ae4-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="28ae4-200">UseWebApiActionConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="28ae4-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="28ae4-201">UseWebApiOverloadingAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="28ae4-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="28ae4-202">UseWebApiParameterConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="28ae4-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="28ae4-203">UseWebApiRoutesAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="28ae4-204">Соглашения о действиях</span><span class="sxs-lookup"><span data-stu-id="28ae4-204">Action Conventions</span></span>

<span data-ttu-id="28ae4-205">Атрибут `UseWebApiActionConventionsAttribute` используется для сопоставления метода HTTP с действиями на основе их имен (например, `Get` сопоставляется с `HttpGet`).</span><span class="sxs-lookup"><span data-stu-id="28ae4-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="28ae4-206">Он применяется только к действиям без маршрутизации атрибутов.</span><span class="sxs-lookup"><span data-stu-id="28ae4-206">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="28ae4-207">Перегрузка</span><span class="sxs-lookup"><span data-stu-id="28ae4-207">Overloading</span></span>

<span data-ttu-id="28ae4-208">Атрибут `UseWebApiOverloadingAttribute` используется для применения соглашения `WebApiOverloadingApplicationModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="28ae4-209">Это соглашение добавляет `OverloadActionConstraint` в процесс выбора действия, ограничивая потенциальные действия теми, для которых запрос удовлетворяет всем обязательным параметрам.</span><span class="sxs-lookup"><span data-stu-id="28ae4-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="28ae4-210">Соглашения о параметрах</span><span class="sxs-lookup"><span data-stu-id="28ae4-210">Parameter Conventions</span></span>

<span data-ttu-id="28ae4-211">Атрибут `UseWebApiParameterConventionsAttribute` используется для применения соглашения о действиях `WebApiParameterConventionsApplicationModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="28ae4-212">Это соглашение указывает, что простые типы, используемые в качестве параметров действия, привязываются из URI по умолчанию, тогда как сложные типы привязываются из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="28ae4-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="28ae4-213">Маршруты</span><span class="sxs-lookup"><span data-stu-id="28ae4-213">Routes</span></span>

<span data-ttu-id="28ae4-214">Атрибут `UseWebApiRoutesAttribute` контролирует применение соглашение контроллера `WebApiApplicationModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="28ae4-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="28ae4-215">Активированное соглашение используется для добавления поддержки для [областей](xref:mvc/controllers/areas) в маршрут.</span><span class="sxs-lookup"><span data-stu-id="28ae4-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="28ae4-216">Помимо набора соглашений в пакет совместимости входит базовый класс `System.Web.Http.ApiController`, который заменяет класс, предоставляемый веб-API.</span><span class="sxs-lookup"><span data-stu-id="28ae4-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="28ae4-217">В этом случае контроллеры, написанные для веб-API и наследующие от его `ApiController`, будут функционировать так, как предусмотрено, работая при этом в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="28ae4-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="28ae4-218">Все указанные ранее атрибуты `UseWebApi*` применяются к базовому классу контроллера.</span><span class="sxs-lookup"><span data-stu-id="28ae4-218">All of the `UseWebApi*` attributes listed earlier are applied to the base controller class.</span></span> <span data-ttu-id="28ae4-219">`ApiController` предоставляет свойства, методы и типы результатов, совместимые с их аналогами в веб-API.</span><span class="sxs-lookup"><span data-stu-id="28ae4-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="28ae4-220">Использование ApiExplorer для документирования приложения</span><span class="sxs-lookup"><span data-stu-id="28ae4-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="28ae4-221">Модель приложения предоставляет [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) свойство на каждом уровне, которое можно использовать для прохода по структуре приложения.</span><span class="sxs-lookup"><span data-stu-id="28ae4-221">The application model exposes an [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="28ae4-222">Такая ситуация походит для [создания страниц справки для веб-API с помощью таких средств, как Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="28ae4-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="28ae4-223">Свойство `ApiExplorer` предоставляет свойство `IsVisible`, которое можно задать, чтобы указать, какие части модели приложения следует предоставить.</span><span class="sxs-lookup"><span data-stu-id="28ae4-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="28ae4-224">Этот параметр настраивается с помощью соглашения:</span><span class="sxs-lookup"><span data-stu-id="28ae4-224">You can configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="28ae4-225">Этот подход (и дополнительные соглашения, если необходимо) позволяет включать или отключать видимость API на любом уровне в приложении.</span><span class="sxs-lookup"><span data-stu-id="28ae4-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
