---
title: Новые возможности в ASP.NET Core 5.0
author: rick-anderson
description: Сведения о новых возможностях в ASP.NET Core 5.0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: 5caa412773bf9c8e3bed5ebc529d48b886de6956
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253076"
---
# <a name="whats-new-in-aspnet-core-50"></a><span data-ttu-id="d5db5-103">Новые возможности в ASP.NET Core 5.0</span><span class="sxs-lookup"><span data-stu-id="d5db5-103">What's new in ASP.NET Core 5.0</span></span>

<span data-ttu-id="d5db5-104">В этой статье описываются наиболее важные изменения в ASP.NET Core 5.0 со ссылками на соответствующую документацию.</span><span class="sxs-lookup"><span data-stu-id="d5db5-104">This article highlights the most significant changes in ASP.NET Core 5.0 with links to relevant documentation.</span></span>

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a><span data-ttu-id="d5db5-105">Улучшения ASP.NET Core MVC и Razor</span><span class="sxs-lookup"><span data-stu-id="d5db5-105">ASP.NET Core MVC and Razor improvements</span></span>

### <a name="model-binding-datetime-as-utc"></a><span data-ttu-id="d5db5-106">Привязка модели DateTime в формате UTC</span><span class="sxs-lookup"><span data-stu-id="d5db5-106">Model binding DateTime as UTC</span></span>

<span data-ttu-id="d5db5-107">Привязка модели теперь поддерживает привязку строк времени в формате UTC к `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-107">Model binding now supports binding UTC time strings to `DateTime`.</span></span> <span data-ttu-id="d5db5-108">Если запрос содержит строку времени в формате UTC, привязка модели привязывает ее к `DateTime` в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="d5db5-108">If the request contains a UTC time string, model binding binds it to a UTC `DateTime`.</span></span> <span data-ttu-id="d5db5-109">Например, следующая строка времени привязывается к `DateTime` в формате UTC: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span><span class="sxs-lookup"><span data-stu-id="d5db5-109">For example, the following time string is bound the UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span></span>

### <a name="model-binding-and-validation-with-c-9-record-types"></a><span data-ttu-id="d5db5-110">Привязка модели и проверка с помощью типов записей C# 9</span><span class="sxs-lookup"><span data-stu-id="d5db5-110">Model binding and validation with C# 9 record types</span></span>

<span data-ttu-id="d5db5-111">[Типы записей C# 9](/dotnet/csharp/whats-new/csharp-9#record-types) можно использовать с привязкой модели в контроллере MVC или на странице Razor.</span><span class="sxs-lookup"><span data-stu-id="d5db5-111">[C# 9 record types](/dotnet/csharp/whats-new/csharp-9#record-types) can be used with model binding in an MVC controller or a Razor Page.</span></span> <span data-ttu-id="d5db5-112">Типы записей — это хороший способ моделирования передаваемых по сети данных.</span><span class="sxs-lookup"><span data-stu-id="d5db5-112">Record types are a good way to model data being transmitted over the network.</span></span>

<span data-ttu-id="d5db5-113">Например, в следующем контроллере `PersonController` используется тип записи `Person` с привязкой модели и проверкой формы:</span><span class="sxs-lookup"><span data-stu-id="d5db5-113">For example, the following `PersonController` uses the `Person` record type with model binding and form validation:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

<span data-ttu-id="d5db5-114">Файл `Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d5db5-114">The `Person/Index.cshtml` file:</span></span>

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a><span data-ttu-id="d5db5-115">Усовершенствования DynamicRouteValueTransformer</span><span class="sxs-lookup"><span data-stu-id="d5db5-115">Improvements to DynamicRouteValueTransformer</span></span>

<span data-ttu-id="d5db5-116">В ASP.NET Core 3.1 появился <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> в качестве способа использования пользовательской конечной точки для динамического выбора действия контроллера MVC или страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="d5db5-116">ASP.NET Core 3.1 introduced <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> as a way to use custom endpoint to dynamically select an MVC controller action or a Razor page.</span></span> <span data-ttu-id="d5db5-117">Приложения ASP.NET Core 5.0 могут передавать состояние в `DynamicRouteValueTransformer` и фильтровать выбранный набор конечных точек.</span><span class="sxs-lookup"><span data-stu-id="d5db5-117">ASP.NET Core 5.0 apps can pass state to a `DynamicRouteValueTransformer` and filter the set of endpoints chosen.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="d5db5-118">Прочее</span><span class="sxs-lookup"><span data-stu-id="d5db5-118">Miscellaneous</span></span>

* <span data-ttu-id="d5db5-119">Этот атрибут [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) можно применить к свойствам в модели страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="d5db5-119">The [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute can be applied to properties on a Razor Page model.</span></span>
* <span data-ttu-id="d5db5-120">Параметры и свойства, привязанные из текста, считаются обязательными по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d5db5-120">Parameters and properties bound from the body are considered required by default.</span></span> <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a><span data-ttu-id="d5db5-121">Веб-интерфейс API</span><span class="sxs-lookup"><span data-stu-id="d5db5-121">Web API</span></span>

### <a name="openapi-specification-on-by-default"></a><span data-ttu-id="d5db5-122">Спецификация OpenAPI включена по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d5db5-122">OpenAPI Specification on by default</span></span>

<span data-ttu-id="d5db5-123">[Спецификация OpenAPI](http://spec.openapis.org/oas/v3.0.3) является отраслевым стандартом для описания API-интерфейсов HTTP и их интеграции в сложные бизнес-процессы или со сторонними производителями.</span><span class="sxs-lookup"><span data-stu-id="d5db5-123">[OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3) is an industry standard for describing HTTP APIs and integrating them into complex business processes or with third parties.</span></span> <span data-ttu-id="d5db5-124">OpenAPI широко поддерживается всеми поставщиками облачных служб и многими реестрами API.</span><span class="sxs-lookup"><span data-stu-id="d5db5-124">OpenAPI is widely supported by all cloud providers and many API registries.</span></span> <span data-ttu-id="d5db5-125">В приложениях, создающих документы OpenAPI из веб-API, представлено множество новых возможностей, в которых можно использовать эти API-интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="d5db5-125">Apps that emit OpenAPI documents from web APIs have a variety of new opportunities in which those APIs can be used.</span></span> <span data-ttu-id="d5db5-126">В условиях партнерства с издателями проекта [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) с открытым кодом шаблон API для ASP.NET Core содержит зависимость NuGet от [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d5db5-126">In partnership with the maintainers of the open-source project [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), the ASP.NET Core API template contains a NuGet dependency on [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="d5db5-127">Swashbuckle — это популярный пакет NuGet с открытым кодом, который динамически создает документы OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="d5db5-127">Swashbuckle is a popular open-source NuGet package that emits OpenAPI documents dynamically.</span></span> <span data-ttu-id="d5db5-128">Он выполняет это путем самоанализа через контроллеры API и создания документа OpenAPI во время выполнения или во время сборки, используя интерфейс командной строки Swashbuckle.</span><span class="sxs-lookup"><span data-stu-id="d5db5-128">Swashbuckle does this by introspecting over the API controllers and generating the OpenAPI document at run-time, or at build time using the Swashbuckle CLI.</span></span>

<span data-ttu-id="d5db5-129">В ASP.NET Core 5.0 шаблоны веб-API по умолчанию поддерживают OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="d5db5-129">In ASP.NET Core 5.0, the web API templates enable the OpenAPI support by default.</span></span> <span data-ttu-id="d5db5-130">Чтобы отключить OpenAPI:</span><span class="sxs-lookup"><span data-stu-id="d5db5-130">To disable OpenAPI:</span></span>

* <span data-ttu-id="d5db5-131">Из командной строки.</span><span class="sxs-lookup"><span data-stu-id="d5db5-131">From the command line:</span></span>

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* <span data-ttu-id="d5db5-132">Из Visual Studio: снимите флажок **Enable OpenAPI support** (Включить поддержку OpenAPI).</span><span class="sxs-lookup"><span data-stu-id="d5db5-132">From Visual Studio: Uncheck **Enable OpenAPI support**.</span></span>

<span data-ttu-id="d5db5-133">Все файлы *.csproj*, созданные для проектов веб-API, содержат ссылку на пакет NuGet [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/).</span><span class="sxs-lookup"><span data-stu-id="d5db5-133">All *.csproj* files created for web API projects contain the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet package reference.</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

<span data-ttu-id="d5db5-134">Код, созданный шаблоном, содержит код в `Startup.ConfigureServices`, который активирует создание документа OpenAPI:</span><span class="sxs-lookup"><span data-stu-id="d5db5-134">The template generated code contains code in `Startup.ConfigureServices` that activates OpenAPI document generation:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

<span data-ttu-id="d5db5-135">Метод `Startup.Configure` добавляет ПО промежуточного слоя Swashbuckle, которое активирует:</span><span class="sxs-lookup"><span data-stu-id="d5db5-135">The `Startup.Configure` method adds the Swashbuckle middleware, which enables the:</span></span>

* <span data-ttu-id="d5db5-136">процесс создания документа;</span><span class="sxs-lookup"><span data-stu-id="d5db5-136">Document generation process.</span></span>
* <span data-ttu-id="d5db5-137">страницу пользовательского интерфейса Swagger по умолчанию в режиме разработки.</span><span class="sxs-lookup"><span data-stu-id="d5db5-137">Swagger UI page by default in development mode.</span></span>

<span data-ttu-id="d5db5-138">Созданный шаблоном код не станет по случайности предоставлять описание API при публикации в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="d5db5-138">The template generated code won't accidentally expose the API's description when publishing to production.</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a><span data-ttu-id="d5db5-139">Импорт Управления API Azure</span><span class="sxs-lookup"><span data-stu-id="d5db5-139">Azure API Management Import</span></span>

<span data-ttu-id="d5db5-140">Если проекты API для ASP.NET Core включают OpenAPI, Visual Studio 2019 версии 16.8 и более поздних публикаций автоматически предлагают дополнительный этап в потоке публикации.</span><span class="sxs-lookup"><span data-stu-id="d5db5-140">When ASP.NET Core API projects enable OpenAPI, the Visual Studio 2019 version 16.8 and later publishing automatically offer an additional step in the publishing flow.</span></span> <span data-ttu-id="d5db5-141">Разработчики, использующие [Управление API Azure](xref:tutorials/publish-to-azure-api-management-using-vs), имеют возможность автоматического импорта API-интерфейсов в Управление API Azure во время потока публикации:</span><span class="sxs-lookup"><span data-stu-id="d5db5-141">Developers who use [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) have an opportunity to automatically import the APIs into Azure API Management during the publish flow:</span></span>

![Импорт и публикация Управления API Azure](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a><span data-ttu-id="d5db5-143">Улучшенный интерфейс запуска для проектов веб-API</span><span class="sxs-lookup"><span data-stu-id="d5db5-143">Better launch experience for web API projects</span></span>

<span data-ttu-id="d5db5-144">Если OpenAPI включен по умолчанию, интерфейс запуска приложений (F5) для разработчиков веб-API значительно улучшается.</span><span class="sxs-lookup"><span data-stu-id="d5db5-144">With OpenAPI enabled by default, the app launching experience (F5) for web API developers significantly improves.</span></span> <span data-ttu-id="d5db5-145">В ASP.NET Core 5.0 шаблон веб-API предварительно настроен для загрузки страницы пользовательского интерфейса Swagger.</span><span class="sxs-lookup"><span data-stu-id="d5db5-145">With ASP.NET Core 5.0, the web API template comes pre-configured to load up the Swagger UI page.</span></span> <span data-ttu-id="d5db5-146">На странице пользовательского интерфейса Swagger содержится документация, добавленная для опубликованного API, и обеспечивается тестирование API-интерфейсов одним щелчком мыши.</span><span class="sxs-lookup"><span data-stu-id="d5db5-146">The Swagger UI page provides both the documentation added for the published API, and enables testing the APIs with a single click.</span></span>

![Представление swagger/index.html](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a><span data-ttu-id="d5db5-148">Усовершенствования в области производительности</span><span class="sxs-lookup"><span data-stu-id="d5db5-148">Performance improvements</span></span>

<span data-ttu-id="d5db5-149">В .NET 5 мы внесли значительные улучшения в производительность среды выполнения Blazor WebAssembly, делая основной акцент на сложной отрисовке пользовательского интерфейса и сериализации JSON.</span><span class="sxs-lookup"><span data-stu-id="d5db5-149">For .NET 5, we made significant improvements to Blazor WebAssembly runtime performance with a specific focus on complex UI rendering and JSON serialization.</span></span> <span data-ttu-id="d5db5-150">По результатам наших тестов производительности в большинстве сценариев скорость Blazor WebAssembly в .NET 5 в два-три раза выше.</span><span class="sxs-lookup"><span data-stu-id="d5db5-150">In our performance tests, Blazor WebAssembly in .NET 5 is two to three times faster for most scenarios.</span></span> <span data-ttu-id="d5db5-151">Дополнительные сведения см. в блоге ASP.NET [об обновлениях ASP.NET Core в .NET 5 (релиз-кандидат 1)](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span><span class="sxs-lookup"><span data-stu-id="d5db5-151">For more information, see [ASP.NET Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span></span>

### <a name="css-isolation"></a><span data-ttu-id="d5db5-152">Изоляция CSS</span><span class="sxs-lookup"><span data-stu-id="d5db5-152">CSS isolation</span></span>

<span data-ttu-id="d5db5-153">Теперь Blazor поддерживает определение стилей CSS, областью действия которых является данный компонент.</span><span class="sxs-lookup"><span data-stu-id="d5db5-153">Blazor now supports defining CSS styles that are scoped to a given component.</span></span> <span data-ttu-id="d5db5-154">Благодаря стилям CSS для отдельных компонентов проще ориентироваться в стилях в приложении и избежать непредвиденных побочных эффектов от применения глобальных стилей.</span><span class="sxs-lookup"><span data-stu-id="d5db5-154">Component-specific CSS styles make it easier to reason about the styles in an app and to avoid unintentional side effects of global styles.</span></span> <span data-ttu-id="d5db5-155">Для получения дополнительной информации см. <xref:blazor/components/css-isolation>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-155">For more information, see <xref:blazor/components/css-isolation>.</span></span>

### <a name="new-inputfile-component"></a><span data-ttu-id="d5db5-156">Новый компонент `InputFile`</span><span class="sxs-lookup"><span data-stu-id="d5db5-156">New `InputFile` component</span></span>

<span data-ttu-id="d5db5-157">Компонент `InputFile` позволяет считывать один или несколько файлов, выбранных пользователем для отправки.</span><span class="sxs-lookup"><span data-stu-id="d5db5-157">The `InputFile` component allows reading one or more files selected by a user for upload.</span></span> <span data-ttu-id="d5db5-158">Для получения дополнительной информации см. <xref:blazor/file-uploads>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-158">For more information, see <xref:blazor/file-uploads>.</span></span>

### <a name="new-inputradio-and-inputradiogroup-components"></a><span data-ttu-id="d5db5-159">Новые компоненты `InputRadio` и `InputRadioGroup`</span><span class="sxs-lookup"><span data-stu-id="d5db5-159">New `InputRadio` and `InputRadioGroup` components</span></span>

<span data-ttu-id="d5db5-160">Blazor содержит встроенные компоненты `InputRadio` и `InputRadioGroup`, которые упрощают привязку данных к группам переключателей со встроенной проверкой.</span><span class="sxs-lookup"><span data-stu-id="d5db5-160">Blazor has built-in `InputRadio` and `InputRadioGroup` components that simplify data binding to radio button groups with integrated validation.</span></span> <span data-ttu-id="d5db5-161">Для получения дополнительной информации см. <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-161">For more information, see <xref:blazor/forms-validation>.</span></span>

### <a name="component-virtualization"></a><span data-ttu-id="d5db5-162">Виртуализация компонентов</span><span class="sxs-lookup"><span data-stu-id="d5db5-162">Component virtualization</span></span>

<span data-ttu-id="d5db5-163">Повысьте воспринимаемую производительность отрисовки компонентов с помощью встроенной поддержки виртуализации платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="d5db5-163">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="d5db5-164">Для получения дополнительной информации см. <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-164">For more information, see <xref:blazor/components/virtualization>.</span></span>

### <a name="ontoggle-event-support"></a><span data-ttu-id="d5db5-165">Поддержка события `ontoggle`</span><span class="sxs-lookup"><span data-stu-id="d5db5-165">`ontoggle` event support</span></span>

<span data-ttu-id="d5db5-166">События Blazor теперь поддерживают событие DOM `ontoggle`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-166">Blazor events now support the `ontoggle` DOM event.</span></span> <span data-ttu-id="d5db5-167">Для получения дополнительной информации см. <xref:blazor/components/event-handling#event-argument-types>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-167">For more information, see <xref:blazor/components/event-handling#event-argument-types>.</span></span>

### <a name="set-ui-focus-in-no-locblazor-apps"></a><span data-ttu-id="d5db5-168">Установка фокуса пользовательского интерфейса в приложениях Blazor</span><span class="sxs-lookup"><span data-stu-id="d5db5-168">Set UI focus in Blazor apps</span></span>

<span data-ttu-id="d5db5-169">Используйте удобный метод `FocusAsync` со ссылками на элемент, чтобы установить фокус пользовательского интерфейса на этот элемент.</span><span class="sxs-lookup"><span data-stu-id="d5db5-169">Use the `FocusAsync` convenience method on element references to set the UI focus to that element.</span></span> <span data-ttu-id="d5db5-170">Для получения дополнительной информации см. <xref:blazor/components/event-handling#focus-an-element>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-170">For more information, see <xref:blazor/components/event-handling#focus-an-element>.</span></span>

### <a name="custom-validation-class-attributes"></a><span data-ttu-id="d5db5-171">Настраиваемые атрибуты класса проверки</span><span class="sxs-lookup"><span data-stu-id="d5db5-171">Custom validation class attributes</span></span>

<span data-ttu-id="d5db5-172">Имена настраиваемых классов проверки полезны при интеграции с платформами CSS, такими как Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="d5db5-172">Custom validation class names are useful when integrating with CSS frameworks, such as Bootstrap.</span></span> <span data-ttu-id="d5db5-173">Для получения дополнительной информации см. <xref:blazor/forms-validation#custom-validation-class-attributes>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-173">For more information, see <xref:blazor/forms-validation#custom-validation-class-attributes>.</span></span>

### <a name="iasyncdisposable-support"></a><span data-ttu-id="d5db5-174">Поддержка IAsyncDisposable</span><span class="sxs-lookup"><span data-stu-id="d5db5-174">IAsyncDisposable support</span></span>

<span data-ttu-id="d5db5-175">Теперь компоненты Blazor поддерживают интерфейс <xref:System.IAsyncDisposable> для асинхронного выпуска выделенных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="d5db5-175">Blazor components now support the <xref:System.IAsyncDisposable> interface for the asynchronous release of allocated resources.</span></span>

### <a name="javascript-isolation-and-object-references"></a><span data-ttu-id="d5db5-176">Изоляция  JavaScript и ссылки на объекты</span><span class="sxs-lookup"><span data-stu-id="d5db5-176">JavaScript isolation and object references</span></span>

<span data-ttu-id="d5db5-177">Blazor реализует изоляцию JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="d5db5-177">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="d5db5-178">Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-178">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

### <a name="form-components-support-display-name"></a><span data-ttu-id="d5db5-179">Поддержка компонентами форм отображаемого имени</span><span class="sxs-lookup"><span data-stu-id="d5db5-179">Form components support display name</span></span>

<span data-ttu-id="d5db5-180">Следующие встроенные компоненты поддерживают отображаемые имена с помощью параметра `DisplayName`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-180">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* `InputDate`
* `InputNumber`
* `InputSelect`

<span data-ttu-id="d5db5-181">Для получения дополнительной информации см. <xref:blazor/forms-validation#display-name-support>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-181">For more information, see <xref:blazor/forms-validation#display-name-support>.</span></span>

### <a name="catch-all-route-parameters"></a><span data-ttu-id="d5db5-182">Параметры маршрута catch-all</span><span class="sxs-lookup"><span data-stu-id="d5db5-182">Catch-all route parameters</span></span>

<span data-ttu-id="d5db5-183">В компонентах поддерживаются параметры маршрута catch-all, которые захватывают пути в нескольких папках.</span><span class="sxs-lookup"><span data-stu-id="d5db5-183">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="d5db5-184">Для получения дополнительной информации см. <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-184">For more information, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="debugging-improvements"></a><span data-ttu-id="d5db5-185">Усовершенствования отладки</span><span class="sxs-lookup"><span data-stu-id="d5db5-185">Debugging improvements</span></span>

<span data-ttu-id="d5db5-186">В ASP.NET Core 5.0 улучшена отладка приложений Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="d5db5-186">Debugging Blazor WebAssembly apps is improved in ASP.NET Core 5.0.</span></span> <span data-ttu-id="d5db5-187">Кроме того, сейчас отладка поддерживается в Visual Studio для Mac.</span><span class="sxs-lookup"><span data-stu-id="d5db5-187">Additionally, debugging is now supported on Visual Studio for Mac.</span></span> <span data-ttu-id="d5db5-188">Для получения дополнительной информации см. <xref:blazor/debug>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-188">For more information, see <xref:blazor/debug>.</span></span>

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a><span data-ttu-id="d5db5-189">Microsoft Identity версии 2.0 и MSAL версии 2.0</span><span class="sxs-lookup"><span data-stu-id="d5db5-189">Microsoft Identity v2.0 and MSAL v2.0</span></span>

<span data-ttu-id="d5db5-190">Теперь для безопасности Blazor используется Microsoft Identity версии 2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) и [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) и MSAL версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="d5db5-190">Blazor security now uses Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) and [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) and MSAL v2.0.</span></span> <span data-ttu-id="d5db5-191">Дополнительные сведения см. в статье о [безопасности Blazor и узле Identity](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="d5db5-191">For more information, see the topics in the [Blazor Security and Identity node](xref:blazor/security/index).</span></span>

### <a name="protected-browser-storage-for-no-locblazor-server"></a><span data-ttu-id="d5db5-192">Защищенное хранилище браузера для Blazor Server</span><span class="sxs-lookup"><span data-stu-id="d5db5-192">Protected Browser Storage for Blazor Server</span></span>

<span data-ttu-id="d5db5-193">Теперь приложения Blazor Server могут использовать встроенную поддержку для хранения сведений о состоянии приложения в браузере, который уже защищен от незаконного изменения благодаря защите данных в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5db5-193">Blazor Server apps can now use built-in support for storing app state in the browser that has been protected from tampering using ASP.NET Core data protection.</span></span> <span data-ttu-id="d5db5-194">Данные могут храниться либо в локальном хранилище браузера, либо в хранилище сеансов.</span><span class="sxs-lookup"><span data-stu-id="d5db5-194">Data can be stored in either local browser storage or session storage.</span></span> <span data-ttu-id="d5db5-195">Для получения дополнительной информации см. <xref:blazor/state-management>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-195">For more information, see <xref:blazor/state-management>.</span></span>

### <a name="no-locblazor-webassembly-prerendering"></a><span data-ttu-id="d5db5-196">Предварительная отрисовка Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d5db5-196">Blazor WebAssembly prerendering</span></span>

<span data-ttu-id="d5db5-197">Благодаря улучшенной интеграции компонентов в моделях размещения приложения Blazor WebAssembly теперь могут предварительно отрисовывать выходные данные на сервере.</span><span class="sxs-lookup"><span data-stu-id="d5db5-197">Component integration is improved across hosting models, and Blazor WebAssembly apps can now prerender output on the server.</span></span> <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a><span data-ttu-id="d5db5-198">Улучшенная обрезка и компоновка</span><span class="sxs-lookup"><span data-stu-id="d5db5-198">Trimming/linking improvements</span></span>

<span data-ttu-id="d5db5-199">Blazor WebAssembly выполняет обрезку и компоновку промежуточного языка (IL) во время сборки, чтобы затем удалить ненужный IL из выходных сборок приложения.</span><span class="sxs-lookup"><span data-stu-id="d5db5-199">Blazor WebAssembly performs Intermediate Language (IL) trimming/linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="d5db5-200">В выпуске ASP.NET Core 5.0 Blazor WebAssembly выполняет улучшенную обрезку с помощью дополнительных параметров конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d5db5-200">With the release of ASP.NET Core 5.0, Blazor WebAssembly performs improved trimming with additional configuration options.</span></span> <span data-ttu-id="d5db5-201">Дополнительные сведения см. в статьях <xref:blazor/host-and-deploy/configure-trimmer> и [Параметры обрезки](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="d5db5-201">For more information, see <xref:blazor/host-and-deploy/configure-trimmer> and [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

### <a name="browser-compatibility-analyzer"></a><span data-ttu-id="d5db5-202">Анализатор совместимости с браузерами</span><span class="sxs-lookup"><span data-stu-id="d5db5-202">Browser compatibility analyzer</span></span>

<span data-ttu-id="d5db5-203">Приложения Blazor WebAssembly предназначены для использования в полной контактной зоне API .NET, но из-за ограничений песочницы браузера поддерживаются не все API-интерфейсы .NET.</span><span class="sxs-lookup"><span data-stu-id="d5db5-203">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="d5db5-204">При выполнении в WebAssembly неподдерживаемые API-интерфейсы вызывают <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-204">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="d5db5-205">Анализатор совместимости платформ предупреждает разработчика, когда приложение использует API-интерфейсы, которые не поддерживаются целевыми платформами приложения.</span><span class="sxs-lookup"><span data-stu-id="d5db5-205">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="d5db5-206">Для получения дополнительной информации см. <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-206">For more information, see <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="d5db5-207">Сборки с отложенной загрузкой</span><span class="sxs-lookup"><span data-stu-id="d5db5-207">Lazy load assemblies</span></span>

<span data-ttu-id="d5db5-208">Производительность запуска приложения Blazor WebAssembly можно повысить, отложив загрузку некоторых сборок приложений, пока они не потребуются.</span><span class="sxs-lookup"><span data-stu-id="d5db5-208">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they're required.</span></span> <span data-ttu-id="d5db5-209">Для получения дополнительной информации см. <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-209">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="updated-globalization-support"></a><span data-ttu-id="d5db5-210">Обновленная поддержка глобализации</span><span class="sxs-lookup"><span data-stu-id="d5db5-210">Updated globalization support</span></span>

<span data-ttu-id="d5db5-211">Поддержка глобализации доступна для Blazor WebAssembly на основе ICU (международных компонентов для Юникода).</span><span class="sxs-lookup"><span data-stu-id="d5db5-211">Globalization support is available for Blazor WebAssembly based on International Components for Unicode (ICU).</span></span> <span data-ttu-id="d5db5-212">Для получения дополнительной информации см. <xref:blazor/globalization-localization>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-212">For more information, see <xref:blazor/globalization-localization>.</span></span>

## <a name="grpc"></a><span data-ttu-id="d5db5-213">gRPC</span><span class="sxs-lookup"><span data-stu-id="d5db5-213">gRPC</span></span>

<span data-ttu-id="d5db5-214">В [gRPC](https://grpc.io/) внесено много усовершенствований производительности.</span><span class="sxs-lookup"><span data-stu-id="d5db5-214">Many preformance improvements have been made in [gRPC](https://grpc.io/).</span></span> <span data-ttu-id="d5db5-215">Дополнительные сведения см. в статье [Улучшения производительности gRPC в .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span><span class="sxs-lookup"><span data-stu-id="d5db5-215">For more information, see [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span></span>

<span data-ttu-id="d5db5-216">Дополнительные сведения о gRPC см. в статье <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-216">For more gRPC information, see <xref:grpc/index>.</span></span>

## SignalR

### <a name="no-locsignalr-hub-filters"></a><span data-ttu-id="d5db5-217">Фильтры концентраторов SignalR</span><span class="sxs-lookup"><span data-stu-id="d5db5-217">SignalR Hub filters</span></span>

<span data-ttu-id="d5db5-218">Фильтры концентраторов SignalR, называемые конвейерами концентраторов в ASP.NET SignalR, — это возможность, которая позволяет выполнять код до и после вызова методов концентратора.</span><span class="sxs-lookup"><span data-stu-id="d5db5-218">SignalR Hub filters, called Hub pipelines in ASP.NET SignalR, is a feature that allows code to run before and after Hub methods are called.</span></span> <span data-ttu-id="d5db5-219">Выполнение кода до и после вызова методов концентратора аналогично тому, как ПО промежуточного слоя может выполнять код до и после HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="d5db5-219">Running code before and after Hub methods are called is similar to how middleware has the ability to run code before and after an HTTP request.</span></span> <span data-ttu-id="d5db5-220">Распространенные варианты использования включают ведение журнала, обработку ошибок и проверку аргументов.</span><span class="sxs-lookup"><span data-stu-id="d5db5-220">Common uses include logging, error handling, and argument validation.</span></span>

<span data-ttu-id="d5db5-221">Подробные сведения см. в статье [Использование фильтров концентраторов в ASP.NET Core SignalR](xref:signalr/hub-filters).</span><span class="sxs-lookup"><span data-stu-id="d5db5-221">For more information, see [Use hub filters in ASP.NET Core SignalR](xref:signalr/hub-filters).</span></span>

### <a name="no-locsignalr-parallel-hub-invocations"></a><span data-ttu-id="d5db5-222">Параллельные вызовы концентраторов SignalR</span><span class="sxs-lookup"><span data-stu-id="d5db5-222">SignalR parallel hub invocations</span></span>

<span data-ttu-id="d5db5-223">ASP.NET Core SignalR теперь может обрабатывать параллельные вызовы концентраторов.</span><span class="sxs-lookup"><span data-stu-id="d5db5-223">ASP.NET Core SignalR is now capable of handling parallel hub invocations.</span></span> <span data-ttu-id="d5db5-224">Поведение по умолчанию можно изменить, чтобы разрешить клиентам одновременно вызывать несколько методов концентратора:</span><span class="sxs-lookup"><span data-stu-id="d5db5-224">The default behavior can be changed to allow clients to invoke more than one hub method at a time:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a><span data-ttu-id="d5db5-225">Добавлена поддержка MessagePack в клиент Java для SignalR</span><span class="sxs-lookup"><span data-stu-id="d5db5-225">Added Messagepack support in SignalR Java client</span></span>

<span data-ttu-id="d5db5-226">Новый пакет [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack) добавляет поддержку MessagePack в клиент Java для SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5db5-226">A new package, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adds MessagePack support to the SignalR Java client.</span></span> <span data-ttu-id="d5db5-227">Чтобы использовать протокол концентратора MessagePack, добавьте `.withHubProtocol(new MessagePackHubProtocol())` в построитель подключений:</span><span class="sxs-lookup"><span data-stu-id="d5db5-227">To use the MessagePack hub protocol, add `.withHubProtocol(new MessagePackHubProtocol())` to the connection builder:</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* <span data-ttu-id="d5db5-228">Перезагружаемые конечные точки с помощью конфигурации. Kestrel может обнаружить изменения в конфигурации, переданные [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A), отменить привязку к имеющимся конечным точкам и привязать к новым конечным точкам, не требуя перезапуска приложения, если для параметра `reloadOnChange` задано значение `true`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-228">Reloadable endpoints via configuration: Kestrel can detect changes to configuration passed to [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) and unbind from existing endpoints and bind to new endpoints without requiring an application restart when the `reloadOnChange` parameter is `true`.</span></span> <span data-ttu-id="d5db5-229">По умолчанию при использовании <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> или <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> Kestrel привязывается к подразделу конфигурации ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) с включенным параметром `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-229">By default when using <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> or <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, Kestrel binds to the ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) configuration subsection with `reloadOnChange` enabled.</span></span> <span data-ttu-id="d5db5-230">Приложения должны передавать `reloadOnChange: true` при вызове `KestrelServerOptions.Configure` вручную для получения перезагружаемых конечных точек.</span><span class="sxs-lookup"><span data-stu-id="d5db5-230">Apps must pass `reloadOnChange: true` when calling `KestrelServerOptions.Configure` manually to get reloadable endpoints.</span></span>
* <span data-ttu-id="d5db5-231">Улучшения заголовков ответов HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d5db5-231">HTTP/2 response headers improvements.</span></span> <span data-ttu-id="d5db5-232">Дополнительные сведения см. в следующем разделе [Улучшения в области производительности](#performance-improvements).</span><span class="sxs-lookup"><span data-stu-id="d5db5-232">For more information, see [Performance improvements](#performance-improvements) in the next section.</span></span>
* <span data-ttu-id="d5db5-233">Поддержка дополнительных типов конечных точек в транспортировке сокетов. В дополнение к новому API, представленному в <xref:System.Net.Sockets?displayProperty=nameWithType>, транспортировка сокетов по умолчанию в [Kestrel](xref:fundamentals/servers/kestrel) позволяет привязаться как к имеющимся дескрипторам файлов, так и к сокетам домена Unix.</span><span class="sxs-lookup"><span data-stu-id="d5db5-233">Support for additional endpoints types in the sockets transport: Adding to the new API introduced in <xref:System.Net.Sockets?displayProperty=nameWithType>, the sockets default transport in [Kestrel](xref:fundamentals/servers/kestrel) allows binding to both existing file handles and Unix domain sockets.</span></span> <span data-ttu-id="d5db5-234">Поддержка привязки к имеющимся дескрипторам файлов позволяет использовать имеющуюся интеграцию `Systemd`, не требуя транспортировки `libuv`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-234">Support for binding to existing file handles enables using the existing `Systemd` integration without requiring the `libuv` transport.</span></span>
* <span data-ttu-id="d5db5-235">Декодирование настраиваемого заголовка в Kestrel. Приложения могут указать, какие <xref:System.Text.Encoding> использовать для интерпретации входящих заголовков на основе имени заголовка, а не по умолчанию в UTF-8.</span><span class="sxs-lookup"><span data-stu-id="d5db5-235">Custom header decoding in Kestrel: Apps can specify which <xref:System.Text.Encoding> to use to interpret incoming headers based on the header name instead of defaulting to UTF-8.</span></span> <span data-ttu-id="d5db5-236">Задайте в качестве значения заголовка</span><span class="sxs-lookup"><span data-stu-id="d5db5-236">Set the</span></span> <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> <span data-ttu-id="d5db5-237">свойство `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector`, чтобы указать используемую кодировку:</span><span class="sxs-lookup"><span data-stu-id="d5db5-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` property to specify which encoding to use:</span></span>
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a><span data-ttu-id="d5db5-238">Параметры для конечной точки Kestrel с помощью конфигурации</span><span class="sxs-lookup"><span data-stu-id="d5db5-238">Kestrel endpoint-specific options via configuration</span></span>

<span data-ttu-id="d5db5-239">Добавлена поддержка настройки параметров для каждой конечной точки Kestrel с помощью [конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d5db5-239">Support has been added for configuring Kestrel’s endpoint-specific options via [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d5db5-240">Конфигурации для каждой конечной точки включают:</span><span class="sxs-lookup"><span data-stu-id="d5db5-240">The endpoint-specific configurations includes the:</span></span>

* <span data-ttu-id="d5db5-241">используемые протоколы HTTP;</span><span class="sxs-lookup"><span data-stu-id="d5db5-241">HTTP protocols used</span></span>
* <span data-ttu-id="d5db5-242">используемые протоколы TLS;</span><span class="sxs-lookup"><span data-stu-id="d5db5-242">TLS protocols used</span></span>
* <span data-ttu-id="d5db5-243">выбранный сертификат;</span><span class="sxs-lookup"><span data-stu-id="d5db5-243">Certificate selected</span></span>
* <span data-ttu-id="d5db5-244">режим сертификата клиента.</span><span class="sxs-lookup"><span data-stu-id="d5db5-244">Client certificate mode</span></span>

<span data-ttu-id="d5db5-245">Конфигурация позволяет определить, какой сертификат выбирается на основе указанного имени сервера.</span><span class="sxs-lookup"><span data-stu-id="d5db5-245">Configuration allows specifying which certificate is selected based on the specified server name.</span></span> <span data-ttu-id="d5db5-246">Имя сервера входит в состав расширения указания имени сервера для протокола TLS, как указано клиентом.</span><span class="sxs-lookup"><span data-stu-id="d5db5-246">The server name is part of the Server Name Indication (SNI) extension to the TLS protocol as indicated by the client.</span></span> <span data-ttu-id="d5db5-247">Конфигурация Kestrel также поддерживает префикс подстановочного знака в имени узла.</span><span class="sxs-lookup"><span data-stu-id="d5db5-247">Kestrel's configuration also supports a wildcard prefix in the host name.</span></span>

<span data-ttu-id="d5db5-248">В следующем примере показано, как задать для каждой конечной точки использование файла конфигурации:</span><span class="sxs-lookup"><span data-stu-id="d5db5-248">The following example shows how to specify endpoint-specific using a configuration file:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

<span data-ttu-id="d5db5-249">Указание имени сервера — это расширение TLS для включения виртуального домена в состав согласования SSL.</span><span class="sxs-lookup"><span data-stu-id="d5db5-249">Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="d5db5-250">Это фактически означает, что виртуальное доменное имя или имя узла можно использовать для определения конечной точки сети.</span><span class="sxs-lookup"><span data-stu-id="d5db5-250">What this effectively means is that the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="d5db5-251">Усовершенствования в области производительности</span><span class="sxs-lookup"><span data-stu-id="d5db5-251">Performance improvements</span></span>

### <a name="http2"></a><span data-ttu-id="d5db5-252">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d5db5-252">HTTP/2</span></span>

* <span data-ttu-id="d5db5-253">Значительное сокращение распределений в пути к коду HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d5db5-253">Significant reductions in allocations in the HTTP/2 code path.</span></span>
* <span data-ttu-id="d5db5-254">Поддержка [динамического сжатия HPack](https://tools.ietf.org/html/rfc7541) заголовков ответов HTTP/2 в [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="d5db5-254">Support for [HPack dynamic compression](https://tools.ietf.org/html/rfc7541) of HTTP/2 response headers in [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="d5db5-255">Дополнительные сведения см. в разделе [Размер таблицы заголовка](xref:fundamentals/servers/kestrel/options#header-table-size) и записи блога [HPACK: возможность Silent Killer в HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span><span class="sxs-lookup"><span data-stu-id="d5db5-255">For more information, see [Header table size](xref:fundamentals/servers/kestrel/options#header-table-size) and [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span></span>
* <span data-ttu-id="d5db5-256">Отправка кадров проверки связи HTTP/2. HTTP/2 имеет механизм отправки кадров проверки связи, чтобы неактивное соединение по-прежнему работало.</span><span class="sxs-lookup"><span data-stu-id="d5db5-256">Sending HTTP/2 PING frames: HTTP/2 has a mechanism for sending PING frames to ensure an idle connection is still functional.</span></span> <span data-ttu-id="d5db5-257">Обеспечение работоспособного соединения особенно полезно при работе с долгосрочными потоками, которые часто находятся в неактивном состоянии, но только периодически видят действия, например потоки gRPC.</span><span class="sxs-lookup"><span data-stu-id="d5db5-257">Ensuring a viable connection is especially useful when working with long-lived streams that are often idle but only intermittently see activity, for example, gRPC streams.</span></span> <span data-ttu-id="d5db5-258">Приложения могут отправить периодические кадры проверки связи в [Kestrel](xref:fundamentals/servers/kestrel), установив ограничения на <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>:</span><span class="sxs-lookup"><span data-stu-id="d5db5-258">Apps can send periodic PING frames in [Kestrel](xref:fundamentals/servers/kestrel) by setting limits on <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>:</span></span>

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a><span data-ttu-id="d5db5-259">Контейнеры</span><span class="sxs-lookup"><span data-stu-id="d5db5-259">Containers</span></span>

<span data-ttu-id="d5db5-260">До выпуска .NET 5.0 создание и публикация документа *Dockerfile* для приложения ASP.NET Core, необходимого для получения всего пакета SDK для .NET Core и образа ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5db5-260">Prior to .NET 5.0, building and publishing a *Dockerfile* for an ASP.NET Core app required pulling the entire .NET Core SDK and the ASP.NET Core image.</span></span> <span data-ttu-id="d5db5-261">В этом выпуске количество извлекаемых байтов образов пакета SDK сокращено, а байты, полученные для образа ASP.NET Core, как правило, устраняются.</span><span class="sxs-lookup"><span data-stu-id="d5db5-261">With this release, pulling the SDK images bytes is reduced and the bytes pulled for the ASP.NET Core image is largely eliminated.</span></span> <span data-ttu-id="d5db5-262">Дополнительные сведения об этом см. в [комментарии к проблеме на GitHub](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span><span class="sxs-lookup"><span data-stu-id="d5db5-262">For more information, see [this GitHub issue comment](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="d5db5-263">Аутентификация и авторизация</span><span class="sxs-lookup"><span data-stu-id="d5db5-263">Authentication and authorization</span></span>

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a><span data-ttu-id="d5db5-264">Проверка подлинности Azure Active Directory с помощью Microsoft.Identity.Web</span><span class="sxs-lookup"><span data-stu-id="d5db5-264">Azure Active Directory authentication with Microsoft.Identity.Web</span></span>

<span data-ttu-id="d5db5-265">Шаблоны проектов ASP.NET Core теперь интегрируются с <xref:Microsoft.Identity.Web?displayProperty=fullName> для управления проверкой подлинности с помощью [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="d5db5-265">The ASP.NET Core project templates now integrate with <xref:Microsoft.Identity.Web?displayProperty=fullName> to handle authentication with [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span></span> <span data-ttu-id="d5db5-266">[Пакет Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) предоставляет:</span><span class="sxs-lookup"><span data-stu-id="d5db5-266">The [Microsoft.Identity.Web package](https://www.nuget.org/packages/Microsoft.Identity.Web/) provides:</span></span>

* <span data-ttu-id="d5db5-267">Улучшенный интерфейс проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="d5db5-267">A better experience for authentication through Azure AD.</span></span>
* <span data-ttu-id="d5db5-268">Более простой способ доступа к ресурсам Azure от имени пользователей, включая [Microsoft Graph](/graph/overview).</span><span class="sxs-lookup"><span data-stu-id="d5db5-268">An easier way to access Azure resources on behalf of your users, including [Microsoft Graph](/graph/overview).</span></span> <span data-ttu-id="d5db5-269">См. [пример Microsoft.Identity.Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), который начинается с базового входа и продолжается через мультитенантность с использованием API-интерфейсов Azure, Microsoft Graph и защиту собственных API-интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="d5db5-269">See the [Microsoft.Identity.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), which starts with a basic login and advances through multi-tenancy, using Azure APIs, using Microsoft Graph, and protecting your own APIs.</span></span> <span data-ttu-id="d5db5-270">Пакет `Microsoft.Identity.Web` доступен вместе с .NET 5.</span><span class="sxs-lookup"><span data-stu-id="d5db5-270">`Microsoft.Identity.Web` is available alongside .NET 5.</span></span>

### <a name="allow-anonymous-access-to-an-endpoint"></a><span data-ttu-id="d5db5-271">Разрешение анонимного доступа к конечной точке</span><span class="sxs-lookup"><span data-stu-id="d5db5-271">Allow anonymous access to an endpoint</span></span>

<span data-ttu-id="d5db5-272">Метод расширения `AllowAnonymous` предоставляет анонимный доступ к конечной точке:</span><span class="sxs-lookup"><span data-stu-id="d5db5-272">The `AllowAnonymous` extension method allows anonymous access to an endpoint:</span></span>

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a><span data-ttu-id="d5db5-273">Пользовательская обработка сбоев авторизации</span><span class="sxs-lookup"><span data-stu-id="d5db5-273">Custom handling of authorization failures</span></span>

<span data-ttu-id="d5db5-274">Пользовательская обработка сбоев авторизации стала еще проще благодаря новому интерфейсу [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs), который вызывается [ПО промежуточного слоя](xref:fundamentals/middleware/index) [авторизации](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A).</span><span class="sxs-lookup"><span data-stu-id="d5db5-274">Custom handling of authorization failures is now easier with the new [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) interface that is invoked by the [authorization](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="d5db5-275">Реализация по умолчанию остается неизменной, но пользовательский обработчик можно зарегистрировать во внедрении зависимостей, что позволяет использовать пользовательские HTTP-ответы в зависимости от причины сбоя авторизации.</span><span class="sxs-lookup"><span data-stu-id="d5db5-275">The default implementation remains the same, but a custom handler can be registered in [Dependency injection, which allows custom HTTP responses based on why authorization failed.</span></span> <span data-ttu-id="d5db5-276">См. [этот пример](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs), в котором демонстрируется использование `IAuthorizationMiddlewareResultHandler`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-276">See [this sample](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) that demonstrates usage of the `IAuthorizationMiddlewareResultHandler`.</span></span>

### <a name="authorization-when-using-endpoint-routing"></a><span data-ttu-id="d5db5-277">Авторизация при использовании маршрутизации конечных точек</span><span class="sxs-lookup"><span data-stu-id="d5db5-277">Authorization when using endpoint routing</span></span>

<span data-ttu-id="d5db5-278">Авторизация при использовании маршрутизации конечных точек теперь получает `HttpContext`, а не экземпляр конечной точки.</span><span class="sxs-lookup"><span data-stu-id="d5db5-278">Authorization when using endpoint routing now receives the `HttpContext` rather than the endpoint instance.</span></span> <span data-ttu-id="d5db5-279">Это позволяет ПО промежуточного слоя авторизации получить доступ к `RouteData` и другим свойствам `HttpContext`, которые были недоступны через класс <xref:Microsoft.AspNetCore.Http.Endpoint>.</span><span class="sxs-lookup"><span data-stu-id="d5db5-279">This allows the authorization middleware to access the `RouteData` and other properties of the `HttpContext` that were not accessible though the <xref:Microsoft.AspNetCore.Http.Endpoint> class.</span></span> <span data-ttu-id="d5db5-280">Конечную точку можно получить из контекста, используя [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span><span class="sxs-lookup"><span data-stu-id="d5db5-280">The endpoint can be fetched from the context using [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span></span>

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a><span data-ttu-id="d5db5-281">Управление доступом на основе ролей с помощью проверки подлинности Kerberos и протокола LDAP в Linux</span><span class="sxs-lookup"><span data-stu-id="d5db5-281">Role-based access control with Kerberos authentication and LDAP on Linux</span></span>

<span data-ttu-id="d5db5-282">См. раздел [Проверка подлинности Kerberos и управление доступом на основе ролей (RBAC).](xref:security/authentication/windowsauth#rbac)</span><span class="sxs-lookup"><span data-stu-id="d5db5-282">See [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac)</span></span>

## <a name="api-improvements"></a><span data-ttu-id="d5db5-283">Усовершенствования API</span><span class="sxs-lookup"><span data-stu-id="d5db5-283">API improvements</span></span>

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a><span data-ttu-id="d5db5-284">Методы расширения JSON для HttpRequest и HttpResponse</span><span class="sxs-lookup"><span data-stu-id="d5db5-284">JSON extension methods for HttpRequest and HttpResponse</span></span>

<span data-ttu-id="d5db5-285">Данные JSON можно считывать и записывать из `HttpRequest` и `HttpResponse` с помощью новых методов расширения <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> и `WriteAsJsonAsync`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-285">JSON data can be read and written to from an `HttpRequest` and `HttpResponse` using the new <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> and `WriteAsJsonAsync` extension methods.</span></span> <span data-ttu-id="d5db5-286">Эти методы расширения используют сериализатор [System.Text.Json](xref:System.Text.Json) для обработки данных JSON.</span><span class="sxs-lookup"><span data-stu-id="d5db5-286">These extension methods use the [System.Text.Json](xref:System.Text.Json) serializer to handle the JSON data.</span></span> <span data-ttu-id="d5db5-287">Новый метод расширения `HasJsonContentType` также может проверять, имеет ли запрос тип содержимого JSON.</span><span class="sxs-lookup"><span data-stu-id="d5db5-287">The new `HasJsonContentType` extension method can also check if a request has a JSON content type.</span></span>

<span data-ttu-id="d5db5-288">Методы расширения JSON можно объединять с [маршрутизацией конечных точек](xref:fundamentals/routing) для создания API-интерфейсов JSON в стиле программирования, который мы называем \***маршрут к коду** _.</span><span class="sxs-lookup"><span data-stu-id="d5db5-288">The JSON extension methods can be combined with [endpoint routing](xref:fundamentals/routing) to create JSON APIs in a style of programming we call \***route to code** _.</span></span> <span data-ttu-id="d5db5-289">Это новый вариант для разработчиков, которые хотят создавать базовые API-интерфейсы JSON простым способом.</span><span class="sxs-lookup"><span data-stu-id="d5db5-289">It is a new option for developers who want to create basic JSON APIs in a lightweight way.</span></span> <span data-ttu-id="d5db5-290">Например, веб-приложение, имеющее только несколько конечных точек, может использовать маршрут к коду, а не все функции MVC ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5db5-290">For example, a web app that has only a handful of endpoints might choose to use route to code rather than the full functionality of ASP.NET Core MVC:</span></span>

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

<span data-ttu-id="d5db5-291">Дополнительные сведения о новых методах расширения JSON и маршруте к коду см. в [этом видео о .NET](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span><span class="sxs-lookup"><span data-stu-id="d5db5-291">For more information on the new JSON extension methods and route to code, see [this .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span></span>

### <a name="systemdiagnosticsactivity"></a><span data-ttu-id="d5db5-292">System.Diagnostics.Activity</span><span class="sxs-lookup"><span data-stu-id="d5db5-292">System.Diagnostics.Activity</span></span>

<span data-ttu-id="d5db5-293"><xref:System.Diagnostics.Activity?displayProperty=fullName> теперь по умолчанию имеет формат W3C.</span><span class="sxs-lookup"><span data-stu-id="d5db5-293">The default format for <xref:System.Diagnostics.Activity?displayProperty=fullName> now defaults to the W3C format.</span></span> <span data-ttu-id="d5db5-294">Благодаря этому распределенная трассировка в ASP.NET Core поддерживает взаимодействие с другими платформами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d5db5-294">This makes distributed tracing support in ASP.NET Core interoperable with more frameworks by default.</span></span>

### <a name="frombodyattribute"></a><span data-ttu-id="d5db5-295">FromBodyAttribute</span><span class="sxs-lookup"><span data-stu-id="d5db5-295">FromBodyAttribute</span></span>

<span data-ttu-id="d5db5-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> теперь поддерживает настройку параметра, который позволяет считать такие параметры или свойства необязательными:</span><span class="sxs-lookup"><span data-stu-id="d5db5-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> now supports configuring an option that allows these parameters or properties to be considered optional:</span></span>

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a><span data-ttu-id="d5db5-297">Прочие улучшения</span><span class="sxs-lookup"><span data-stu-id="d5db5-297">Miscellaneous improvements</span></span>

<span data-ttu-id="d5db5-298">Мы начали применять [заметки, допускающие значение NULL](/dotnet/csharp/nullable-references#attributes-describe-apis), к сборкам ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5db5-298">We’ve started applying [nullable annotations](/dotnet/csharp/nullable-references#attributes-describe-apis) to ASP.NET Core assemblies.</span></span> <span data-ttu-id="d5db5-299">Мы планируем добавить заметки к большей части контактной зоны общедоступного API на платформе .NET 5.</span><span class="sxs-lookup"><span data-stu-id="d5db5-299">We plan to annotate most of the common public API surface of the .NET 5 framework.</span></span> <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a><span data-ttu-id="d5db5-300">Управление активацией класса Startup</span><span class="sxs-lookup"><span data-stu-id="d5db5-300">Control Startup class activation</span></span>

<span data-ttu-id="d5db5-301">Добавлена дополнительная перегрузка <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A>, которая разрешает приложению предоставлять фабричный метод для управления активацией класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-301">An additional <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> overload has been added that lets an app provide a factory method for controlling `Startup` class activation.</span></span> <span data-ttu-id="d5db5-302">Управление активацией класса `Startup` полезно для передачи дополнительных параметров в `Startup`, которые инициализируются вместе с узлом:</span><span class="sxs-lookup"><span data-stu-id="d5db5-302">Controlling `Startup` class activation is useful to pass additional parameters to `Startup` that are initialized along with the host:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a><span data-ttu-id="d5db5-303">Автоматическое обновление с помощью dotnet watch</span><span class="sxs-lookup"><span data-stu-id="d5db5-303">Auto refresh with dotnet watch</span></span>

<span data-ttu-id="d5db5-304">В .NET 5 при запуске [dotnet watch](xref:tutorials/dotnet-watch) в проекте ASP.NET Core запускается браузер по умолчанию и выполняется автоматическое обновление браузера по мере внесения изменений в код.</span><span class="sxs-lookup"><span data-stu-id="d5db5-304">In .NET 5, running [dotnet watch](xref:tutorials/dotnet-watch) on an ASP.NET Core project both launches the default browser and auto refreshes the browser as changes are made to the code.</span></span> <span data-ttu-id="d5db5-305">Это означает, что вы можете:</span><span class="sxs-lookup"><span data-stu-id="d5db5-305">This means you can:</span></span>

<span data-ttu-id="d5db5-306">Открыть проект ASP.NET Core в текстовом редакторе.</span><span class="sxs-lookup"><span data-stu-id="d5db5-306">_ Open an ASP.NET Core project in a text editor.</span></span>
* <span data-ttu-id="d5db5-307">Выполните `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="d5db5-307">Run `dotnet watch`.</span></span>
* <span data-ttu-id="d5db5-308">Сосредоточиться на изменениях кода, пока инструментарий обрабатывает перестроение, перезапуск и перезагрузку приложения.</span><span class="sxs-lookup"><span data-stu-id="d5db5-308">Focus on the code changes while the tooling handles rebuilding, restarting, and reloading the app.</span></span>

### <a name="console-logger-formatter"></a><span data-ttu-id="d5db5-309">Форматировщик средства ведения журнала консоли</span><span class="sxs-lookup"><span data-stu-id="d5db5-309">Console Logger Formatter</span></span>

<span data-ttu-id="d5db5-310">В библиотеке `Microsoft.Extensions.Logging` для поставщика журнала консоли внесено несколько усовершенствований.</span><span class="sxs-lookup"><span data-stu-id="d5db5-310">Improvements have been made to the console log provider in the `Microsoft.Extensions.Logging` library.</span></span> <span data-ttu-id="d5db5-311">Теперь разработчики могут реализовать пользовательский `ConsoleFormatter`, чтобы полностью контролировать форматирование и цветовое выделение выходных данных консоли.</span><span class="sxs-lookup"><span data-stu-id="d5db5-311">Developers can now implement a custom `ConsoleFormatter` to exercise complete control over formatting and colorization of the console output.</span></span> <span data-ttu-id="d5db5-312">API-интерфейсы форматировщика позволяют выполнять обширное форматирование путем реализации подмножества escape-последовательностей VT-100.</span><span class="sxs-lookup"><span data-stu-id="d5db5-312">The formatter APIs allow for rich formatting by implementing a subset of the VT-100 escape sequences.</span></span> <span data-ttu-id="d5db5-313">VT-100 поддерживается в большинстве современных терминалов.</span><span class="sxs-lookup"><span data-stu-id="d5db5-313">VT-100 is supported by most modern terminals.</span></span> <span data-ttu-id="d5db5-314">Средство ведения журнала консоли может анализировать escape-последовательности неподдерживаемых терминалов, позволяя разработчикам создавать единый форматировщик для всех терминалов.</span><span class="sxs-lookup"><span data-stu-id="d5db5-314">The console logger can parse out escape sequences on unsupported terminals allowing developers to author a single formatter for all terminals.</span></span>

### <a name="json-console-logger"></a><span data-ttu-id="d5db5-315">Средство ведения журнала консоли JSON</span><span class="sxs-lookup"><span data-stu-id="d5db5-315">JSON Console Logger</span></span>

<span data-ttu-id="d5db5-316">Помимо поддержки пользовательских форматировщиков, мы также добавили встроенный форматировщик JSON, который выводит структурированные журналы JSON в консоли.</span><span class="sxs-lookup"><span data-stu-id="d5db5-316">In addition to support for custom formatters, we’ve also added a built-in JSON formatter that emits structured JSON logs to the console.</span></span> <span data-ttu-id="d5db5-317">В следующем примере кода показано, как переключиться со средства ведения журнала по умолчанию на JSON:</span><span class="sxs-lookup"><span data-stu-id="d5db5-317">The following code shows how to switch from the default logger to JSON:</span></span>

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

<span data-ttu-id="d5db5-318">Сообщения журнала, выводимые на консоль, имеют формат JSON:</span><span class="sxs-lookup"><span data-stu-id="d5db5-318">Log messages emitted to the console are JSON formatted:</span></span>

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
