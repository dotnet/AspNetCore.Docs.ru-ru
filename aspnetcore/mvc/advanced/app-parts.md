---
title: Совместное использование контроллеров, представлений, Razor страниц и других приложений с частями приложения в ASP.NET Core
author: rick-anderson
description: Совместное использование контроллеров, представлений, Razor страниц и других приложений с частями приложения в ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 23bc1db6a184e7babe87e2d311a8ac4a59e78dd0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588364"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a>Совместное использование контроллеров, представлений, Razor страниц и других приложений с частями приложения

::: moniker range=">= aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([как скачивать](xref:index#how-to-download-a-sample))

*Часть приложения* — это абстракция для ресурсов приложения. Части приложений позволяют ASP.NET Core обнаруживать контроллеры, просматривать компоненты, вспомогательные функции тегов, Razor страницы, источники компиляции Razor и многое другое. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> является частью приложения. `AssemblyPart` инкапсулирует ссылку на сборку и предоставляет типы и ссылки на компиляцию.

[Поставщики компонентов](#fp) работают с частями приложения для заполнения компонентов приложения ASP.NET Core. Основной вариант использования частей приложения заключается в настройке приложения для обнаружения (или запрета загрузки) компонентов ASP.NET Core из сборки. Например, может потребоваться совместное использование общих функциональных возможностей несколькими приложениями. С помощью частей приложения можно совместно использовать сборку (библиотеку DLL), содержащую контроллеры, представления, Razor страницы, источники компиляций Razor, вспомогательные функции тегов и многое другое, с несколькими приложениями. Желательно предоставить общий доступ к сборке для дублирования кода в нескольких проектах.

Приложения ASP.NET Core загружают компоненты из <xref:System.Web.WebPages.ApplicationPart>. Класс <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> представляет часть приложения, поддерживаемую сборкой.

## <a name="load-aspnet-core-features"></a>Загрузка компонентов ASP.NET Core

Используйте классы <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> и <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> для обнаружения и загрузки компонентов ASP.NET Core (контроллеры, компоненты представления и т. д.). <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> отслеживает доступные части приложения и поставщики компонентов. Функция `ApplicationPartManager` настраивается в `Startup.ConfigureServices`.

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

Следующий код предоставляет альтернативный подход к настройке `ApplicationPartManager` с использованием `AssemblyPart`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Предыдущие два примера кода загружают `SharedController` из сборки. `SharedController` не находится в проекте приложения. См. пример [решения WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) для загрузки.

### <a name="include-views"></a>Включение представлений

Используйте [ Razor библиотеку классов](xref:razor-pages/ui-class) для включения представлений в сборку.

### <a name="prevent-loading-resources"></a>Запрет загрузки ресурсов

Части приложения можно использовать для *запрета* загрузки ресурсов в определенной сборке или расположении. Добавьте или удалите элементы коллекции <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>, чтобы скрыть ресурсы или сделать их доступными. Порядок записей в коллекции `ApplicationParts` не имеет значения. Настройте класс `ApplicationPartManager` перед его использованием для конфигурации служб в контейнере. Например, настройте класс `ApplicationPartManager` перед вызовом метода `AddControllersAsServices`. Вызовите метод `Remove` в коллекции `ApplicationParts`, чтобы удалить ресурс.

`ApplicationPartManager` содержит части для:

* сборки приложения и зависимых сборок;
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>Поставщики компонентов

Поставщики компонентов приложений проверяют части приложения и предоставляют для них компоненты. Доступны встроенные поставщики компонентов для следующих компонентов ASP.NET Core:

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class`[ Razor Компиледитемфеатурепровидер](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Поставщики компонентов наследуют от <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, где `T` является типом компонента. Поставщики компонентов можно реализовать для любого из перечисленных выше типов компонентов. Порядок поставщиков компонентов в `ApplicationPartManager.FeatureProviders` может повлиять на поведение во время выполнения. Поставщики, добавленные позднее, могут реагировать на действия, выполняемые ранее добавленными поставщиками.

### <a name="display-available-features"></a>отображение доступных компонентов

Компоненты, доступные для приложения, можно перечислить путем запроса `ApplicationPartManager` с помощью [внедрения зависимостей](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

В [примере для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) используется приведенный выше код для отображения компонентов приложения:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Обнаружение в частях приложения

Ошибки HTTP 404 нередко возникают при разработке с использованием частей приложений. Обычно эти ошибки вызваны отсутствием существенного требования к способу обнаружения частей приложений. Если приложение возвращает ошибку HTTP 404, убедитесь, что выполнены следующие требования:

* Для параметра `applicationName` должна быть задана корневая сборка, используемая для обнаружения. Корневая сборка, используемая для обнаружения, обычно является сборкой точки входа.
* Корневая сборка должна иметь ссылку на части, используемые для обнаружения. Ссылка может быть прямой или транзитивной.
* Корневая сборка должна ссылаться на веб-пакет SDK. Платформа имеет логику, которая помечает в корневой сборке атрибуты, используемые для обнаружения.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([как скачивать](xref:index#how-to-download-a-sample))

*Часть приложения* — это абстракция для ресурсов приложения. Части приложений позволяют ASP.NET Core обнаруживать контроллеры, просматривать компоненты, вспомогательные функции тегов, Razor страницы, источники компиляции Razor и многое другое. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) — это часть приложения. `AssemblyPart` инкапсулирует ссылку на сборку и предоставляет типы и ссылки на компиляцию.

*Поставщики компонентов* работают с частями приложения для заполнения компонентов приложения ASP.NET Core. Основной вариант использования частей приложения заключается в настройке приложения для обнаружения (или запрета загрузки) компонентов ASP.NET Core из сборки. Например, может потребоваться совместное использование общих функциональных возможностей несколькими приложениями. С помощью частей приложения можно совместно использовать сборку (библиотеку DLL), содержащую контроллеры, представления, Razor страницы, источники компиляций Razor, вспомогательные функции тегов и многое другое, с несколькими приложениями. Желательно предоставить общий доступ к сборке для дублирования кода в нескольких проектах.

Приложения ASP.NET Core загружают компоненты из <xref:System.Web.WebPages.ApplicationPart>. Класс <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> представляет часть приложения, поддерживаемую сборкой.

## <a name="load-aspnet-core-features"></a>Загрузка компонентов ASP.NET Core

Используйте классы `ApplicationPart` и `AssemblyPart` для обнаружения и загрузки компонентов ASP.NET Core (контроллеры, компоненты представления и т. д.). <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> отслеживает доступные части приложения и поставщики компонентов. Функция `ApplicationPartManager` настраивается в `Startup.ConfigureServices`.

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Следующий код предоставляет альтернативный подход к настройке `ApplicationPartManager` с использованием `AssemblyPart`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Предыдущие два примера кода загружают `SharedController` из сборки. `SharedController` не находится в проекте приложения. См. пример [решения WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) для загрузки.

### <a name="include-views"></a>Включение представлений

Используйте [ Razor библиотеку классов](xref:razor-pages/ui-class) для включения представлений в сборку.

### <a name="prevent-loading-resources"></a>Запрет загрузки ресурсов

Части приложения можно использовать для *запрета* загрузки ресурсов в определенной сборке или расположении. Добавьте или удалите элементы коллекции <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>, чтобы скрыть ресурсы или сделать их доступными. Порядок записей в коллекции `ApplicationParts` не имеет значения. Настройте класс `ApplicationPartManager` перед его использованием для конфигурации служб в контейнере. Например, настройте класс `ApplicationPartManager` перед вызовом метода `AddControllersAsServices`. Вызовите метод `Remove` в коллекции `ApplicationParts`, чтобы удалить ресурс.

В следующем коде для удаления `MyDependentLibrary` из приложения используется <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager` содержит части для:

* сборки приложения и зависимых сборок;
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Поставщики компонентов приложений

Поставщики компонентов приложений проверяют части приложения и предоставляют для них компоненты. Доступны встроенные поставщики компонентов для следующих компонентов ASP.NET Core:

* [Контроллеры](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Вспомогательные функции тегов](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Просмотр компонентов](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Поставщики компонентов наследуют от <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, где `T` является типом компонента. Поставщики компонентов можно реализовать для любого из перечисленных выше типов компонентов. Порядок поставщиков компонентов в `ApplicationPartManager.FeatureProviders` может повлиять на поведение во время выполнения. Поставщики, добавленные позднее, могут реагировать на действия, выполняемые ранее добавленными поставщиками.

### <a name="display-available-features"></a>отображение доступных компонентов

Компоненты, доступные для приложения, можно перечислить путем запроса `ApplicationPartManager` с помощью [внедрения зависимостей](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

В [примере для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) используется приведенный выше код для отображения компонентов приложения:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Обнаружение в частях приложения

Ошибки HTTP 404 нередко возникают при разработке с использованием частей приложений. Обычно эти ошибки вызваны отсутствием существенного требования к способу обнаружения частей приложений. Если приложение возвращает ошибку HTTP 404, убедитесь, что выполнены следующие требования:

* Для параметра `applicationName` должна быть задана корневая сборка, используемая для обнаружения. Корневая сборка, используемая для обнаружения, обычно является сборкой точки входа.
* Корневая сборка должна иметь ссылку на части, используемые для обнаружения. Ссылка может быть прямой или транзитивной.
* Корневая сборка должна ссылаться на веб-пакет SDK.
  * Платформа ASP.NET Core имеет специальную логику, которая помечает в корневой сборке атрибуты, используемые для обнаружения.

::: moniker-end
