---
title: Внедрение зависимостей в представления в ASP.NET Core
author: ardalis
description: Узнайте, как ASP.NET Core поддерживает внедрение зависимостей в представления MVC.
ms.author: riande
ms.date: 10/14/2016
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
uid: mvc/views/dependency-injection
ms.openlocfilehash: 9d4920a7b572163b048bfb06029d913cee3f9e5a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587258"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="e1cb2-103">Внедрение зависимостей в представления в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1cb2-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="e1cb2-104">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="e1cb2-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e1cb2-105">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection) в представления.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="e1cb2-106">Это может быть полезно для связанных с представлениями служб (например, локализации) или данных, необходимых только для заполнения элементов представления.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="e1cb2-107">Старайтесь поддерживать [разделение ответственности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) между контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="e1cb2-108">Большая часть данных, отображаемых представлениями, должна передаваться от контроллера.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="e1cb2-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1cb2-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="e1cb2-110">Внедрение конфигурации</span><span class="sxs-lookup"><span data-stu-id="e1cb2-110">Configuration injection</span></span>

<span data-ttu-id="e1cb2-111">*appsettings.json* значения могут быть введены непосредственно в представление.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-111">*appsettings.json* values can be injected directly into a view.</span></span>

<span data-ttu-id="e1cb2-112">Пример *appsettings.json* файла:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-112">Example of an *appsettings.json* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="e1cb2-113">Синтаксис для `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="e1cb2-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="e1cb2-114">Вот пример использования `@inject`:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="e1cb2-115">Внедрение службы</span><span class="sxs-lookup"><span data-stu-id="e1cb2-115">Service injection</span></span>

<span data-ttu-id="e1cb2-116">Службу можно внедрить в представление с помощью директивы `@inject`.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="e1cb2-117">По сути, `@inject` добавляет в представление свойство и заполняет это свойство с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="e1cb2-118">Это представление отображает список экземпляров `ToDoItem`, а также сводку по общей статистике.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="e1cb2-119">Сводка заполняется из внедренного `StatisticsService`.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="e1cb2-120">Эта служба регистрируется для внедрения зависимости в `ConfigureServices` в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="e1cb2-121">Функция `StatisticsService` выполняет ряд вычислений с набором экземпляров `ToDoItem`, к которым она обращается через репозиторий:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="e1cb2-122">Пример репозитория использует коллекцию в памяти.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="e1cb2-123">Представленная выше реализация (работающая со всеми данными в памяти) не рекомендуется для больших наборов данных с удаленным доступом.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="e1cb2-124">Пример показывает данные из модели, привязанной к представлению, и из внедренной в представление службы:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Список дел к выполнению, показывающий общее количество пунктов, число выполненных пунктов, средний показатель приоритета, а также список задач с их уровнями приоритета и логическими значениями, характеризующими выполнение.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="e1cb2-126">Заполнение данных подстановки</span><span class="sxs-lookup"><span data-stu-id="e1cb2-126">Populating Lookup Data</span></span>

<span data-ttu-id="e1cb2-127">Внедрение в представление можно использовать для заполнения параметров в элементах пользовательского интерфейса, например в раскрывающихся списках.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="e1cb2-128">Рассмотрим форму профиля пользователя, содержащую параметры для указания пола, штата и другие свойства.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="e1cb2-129">Для отрисовки такой формы в стандартном подходе MVC контроллеру нужно будет запрашивать каждый из этих наборов параметров в службах доступа к данным, а затем заполнять привязываемыми наборами модель или `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="e1cb2-130">Альтернативный вариант получения параметров заключается во внедрении служб непосредственно в представление.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="e1cb2-131">В этом случае необходимый контроллеру код сводится к минимуму, так как логика создания элемента представления помещается в само представление.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="e1cb2-132">Действие контроллера по отображению формы редактирования профиля должно только передать форму экземпляру профиля:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="e1cb2-133">HTML-форма, используемая для изменения этих свойств, содержит для трех свойств раскрывающиеся списки:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Представление редактирования профиля с формой для ввода имени, пола, штата и любимого цвета.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="e1cb2-135">Эти списки заполняются службой, которая внедрена в представление:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="e1cb2-136">`ProfileOptionsService` — это работающая на уровне пользовательского интерфейса служба, которая предоставляет именно те данные, которые нужны в этой форме:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="e1cb2-137">Не забудьте зарегистрировать типы, которые вы будете запрашивать через внедрение зависимостей в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e1cb2-138">Незарегистрированный тип создает исключение в среде выполнения, так как поставщик услуг получает внутренние запросы через [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="e1cb2-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="e1cb2-139">Переопределение служб</span><span class="sxs-lookup"><span data-stu-id="e1cb2-139">Overriding Services</span></span>

<span data-ttu-id="e1cb2-140">Помимо добавления новых служб, этот метод также позволяет переопределять ранее добавленные службы на странице.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="e1cb2-141">На следующем рисунке показаны все доступные поля на странице, используемой в первом примере:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Контекстное меню IntelliSense к введенному символу @ с полями Html, Component, StatsService и Url](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="e1cb2-143">Как видно, полями по умолчанию являются `Html`, `Component` и `Url` (а также внедренное нами поле `StatsService`).</span><span class="sxs-lookup"><span data-stu-id="e1cb2-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="e1cb2-144">Если, например, нужно заменить вспомогательные методы HTML на собственные, вы можете легко сделать это с помощью `@inject`:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="e1cb2-145">При необходимости расширить существующие службы вы можете просто применять этот метод при наследовании от существующей реализации или использовании ее внутри вашей собственной.</span><span class="sxs-lookup"><span data-stu-id="e1cb2-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="e1cb2-146">См. также:</span><span class="sxs-lookup"><span data-stu-id="e1cb2-146">See Also</span></span>

* <span data-ttu-id="e1cb2-147">Блог Саймона Тиммса (Simon Timms): [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/) (Внедрение данных подстановки в ваше представление)</span><span class="sxs-lookup"><span data-stu-id="e1cb2-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
