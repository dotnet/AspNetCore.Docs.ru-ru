---
title: Соглашения для маршрутов и приложений Razor Pages в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать соглашения поставщика модели маршрутов и приложений для управления маршрутизацией, обнаружением и обработкой страниц.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c75e2f1de522f80f4d8e13cf3f60c99cc6b7196a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589417"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Соглашения для маршрутов и приложений Razor Pages в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Узнайте, как использовать [соглашения поставщика модели маршрутов и приложений](xref:mvc/controllers/application-model#conventions) страниц для управления маршрутизацией, обнаружением и обработкой страниц в приложениях Razor Pages.

Когда вам необходимо настроить уникальные маршруты для отдельных страниц, используйте [соглашение AddPageRoute](#configure-a-page-route), описанное далее в этом разделе.

Чтобы указать маршрут страницы, добавить сегменты маршрута или добавить параметры в маршрут, используйте директиву `@page` страницы. Дополнительные сведения см. в разделе [Пользовательские маршруты](xref:razor-pages/index#custom-routes).

Существуют зарезервированные слова, которые нельзя использовать в качестве сегментов маршрутов или имен параметров. Дополнительные сведения см. в разделе [Маршрутизация: зарезервированные имена маршрутизации](xref:mvc/controllers/routing#reserved-routing-names).

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

| Сценарий | Что демонстрирует пример |
| -------- | --------------------------- |
| [Соглашения для моделей](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Добавление шаблона маршрута и заголовка к страницам приложения. |
| [Соглашения для действий с маршрутами страниц](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Добавление шаблона маршрута к страницам в папке и к одной странице. |
| [Соглашения для действий модели страниц](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (класс фильтра, лямбда-выражение или фабрика фильтров)</li></ul> | Добавление заголовка к страницам в папке, добавление заголовка к одной странице и настройка [фабрики фильтров](xref:mvc/controllers/filters#ifilterfactory) для добавления заголовка к страницам приложения. |

Соглашения Razor Pages настраиваются с помощью перегрузки <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>, которая настраивает <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> в `Startup.ConfigureServices`. Следующие примеры соглашений описаны далее в этом разделе:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add( ... );
        options.Conventions.AddFolderRouteModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageRouteModelConvention(
            "/About", model => { ... });
        options.Conventions.AddPageRoute(
            "/Contact", "TheContactPage/{text?}");
        options.Conventions.AddFolderApplicationModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageApplicationModelConvention(
            "/About", model => { ... });
        options.Conventions.ConfigureFilter(model => { ... });
        options.Conventions.ConfigureFilter( ... );
    });
}
```

## <a name="route-order"></a>Порядок маршрутов

Для маршрутов указывается свойство <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>, определяющее порядок обработки (сопоставления маршрутов).

| Номер            | Поведение |
| :--------------: | -------- |
| -1               | Маршрут обрабатывается до обработки других маршрутов. |
| 0                | Порядок не задан (значение по умолчанию). Если свойство `Order` не задано (`Order = null`), значение `Order` по умолчанию для маршрута равно 0 (нулю). |
| 1, 2, &hellip; n | Указывает порядок обработки маршрутов. |

Маршруты обрабатываются в соответствии с соглашением.

* Маршруты обрабатываются по порядку (–1, 0, 1, 2 &hellip; n).
* Если маршруты имеют одинаковые значения свойства `Order`, сначала сопоставляется наиболее конкретный маршрут, а затем более общие.
* Если URL-адресу запроса соответствуют маршруты с одинаковым значением свойства `Order` и одинаковым количеством параметров, они обрабатываются в том порядке, в котором были добавлены в коллекцию <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

По возможности старайтесь не использовать строго определенный порядок обработки маршрутов. Как правило, при маршрутизации правильный маршрут выбирается посредством сопоставления URL-адресов. Если для правильной маршрутизации запросов необходимо задать свойства `Order` маршрута, схема маршрутизации приложения, скорее всего, будет малопонятна клиентам и сложна в обслуживании. Старайтесь упростить схему маршрутизации приложения. В примере приложения требуется явно определенный порядок обработки маршрутов для демонстрации нескольких сценариев маршрутизации на основе одного приложения. Однако в рабочих приложениях следует избегать задания свойства `Order` для маршрутов.

Средства маршрутизации в Razor Pages и контроллере MVC имеют общую реализацию. Сведения о порядке маршрутизации в MVC см. в разделе [Маршрутизация к действиям контроллера: упорядочение маршрутов на основе атрибутов](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Соглашения для моделей

Добавьте делегат для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, чтобы добавить [соглашения для модели](xref:mvc/controllers/application-model#conventions), применяемые к Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Добавление соглашения для модели маршрутов ко всем страницам

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, чтобы создать и добавить <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> в коллекцию экземпляров <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, которые применяются при формировании модели маршрутов страниц.

В примере приложения шаблон маршрута `{globalTemplate?}` добавляется ко всем страницам приложения:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Свойству <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> задано значение `1`. Это обеспечивает описанное ниже поведение сопоставления маршрутов в примере приложения.

* Шаблон маршрута для `TheContactPage/{text?}` добавляется далее в разделе. Маршрут страницы Contact по умолчанию имеет порядок `null` (`Order = 0`), поэтому он сопоставляется до шаблона маршрута `{globalTemplate?}`.
* Шаблон маршрута `{aboutTemplate?}` добавляется далее в разделе. Шаблону `{aboutTemplate?}` задано свойство `Order`, равное `2`. При запросе страницы About по адресу `/About/RouteDataValue` значение RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["aboutTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.
* Шаблон маршрута `{otherPagesTemplate?}` добавляется далее в разделе. Шаблону `{otherPagesTemplate?}` задано свойство `Order`, равное `2`. При запросе любой страницы в папке *Pages/OtherPages* с параметром маршрута (например, `/OtherPages/Page1/RouteDataValue`), RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["otherPagesTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.

По возможности не задавайте свойство `Order`, чтобы оно имело значение `Order = 0`. Предоставьте системе маршрутизации возможность выбирать правильный маршрут.

Параметры Razor Pages, такие как добавление <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, добавляются при добавлении Razor Pages в коллекцию служб в `Startup.ConfigureServices`. Пример см. в [образце приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Запросите страницу About по адресу `localhost:5000/About/GlobalRouteValue` из примера и проверьте результат:

![Запрос страницы About с сегментом маршрута GlobalRouteValue. Отображенная страница показывает, что значение данных маршрута получено в методе OnGet страницы.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Добавление соглашения для модели приложений ко всем страницам

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, чтобы создать и добавить <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> в коллекцию экземпляров <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, которые применяются при формировании модели приложения страницы.

Для демонстрации этих и других соглашений далее в этом разделе в пример приложения включен класс `AddHeaderAttribute`. Конструктор класса принимает строку `name` и массив строк `values`. Эти значения используются в его методе `OnResultExecuting` для задания заголовка ответа. Класс показан полностью в разделе [Соглашения для действий модели страниц](#page-model-action-conventions) далее.

Пример приложения использует класс `AddHeaderAttribute`, чтобы добавить заголовок `GlobalHeader` ко всем страницам в приложении:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Запросите страницу About по адресу `localhost:5000/About` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответа страницы About, показывающие, что добавлен заголовок GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Добавление соглашения для модели обработчика ко всем страницам

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, чтобы создать и добавить <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> в коллекцию экземпляров <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, которые применяются при формировании модели обработчика страниц.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Соглашения для действий с маршрутами страниц

Поставщик модели маршрутов по умолчанию, который является производным от <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>, использует соглашения, создающие точки расширения для настройки маршрутов страниц.

### <a name="folder-route-model-convention"></a>Соглашение для модели маршрутов папки

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, вызывающий действие в классе <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> для всех страниц в указанной папке.

Пример приложения использует <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> для добавления шаблона маршрута `{otherPagesTemplate?}` к страницам в папке *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Свойству <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> задано значение `2`. Это гарантирует, что при указании одного значения маршрута приоритетно выбирается значение данных маршрута из шаблона `{globalTemplate?}`, которому ранее в разделе было присвоено значение `1`. При запросе страницы в папке *Pages/OtherPages* со значением параметра маршрута (например, `/OtherPages/Page1/RouteDataValue`), RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["otherPagesTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.

По возможности не задавайте свойство `Order`, чтобы оно имело значение `Order = 0`. Предоставьте системе маршрутизации возможность выбирать правильный маршрут.

Запросите страницу Page1 по адресу `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` из примера и проверьте результат:

![Страница Page1 в папке OtherPages запрашивается с сегментом маршрута GlobalRouteValue и OtherPagesRouteValue. Отображенная страница показывает, что значения данных маршрута получены в методе OnGet страницы.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Соглашение для модели маршрутов страницы

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, вызывающий действие в классе <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> для страницы с указанным именем.

Пример приложения использует `AddPageRouteModelConvention` для добавления шаблона маршрута `{aboutTemplate?}` к странице About:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Свойству <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> задано значение `2`. Это гарантирует, что при указании одного значения маршрута приоритетно выбирается значение данных маршрута из шаблона `{globalTemplate?}`, которому ранее в разделе было присвоено значение `1`. При запросе страницы About со значением параметра маршрута по адресу `/About/RouteDataValue`, RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["aboutTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.

По возможности не задавайте свойство `Order`, чтобы оно имело значение `Order = 0`. Предоставьте системе маршрутизации возможность выбирать правильный маршрут.

Запросите страницу About по адресу `localhost:5000/About/GlobalRouteValue/AboutRouteValue` из примера и проверьте результат:

![Страница About запрашивается с сегментами маршрута для GlobalRouteValue и AboutRouteValue. Отображенная страница показывает, что значения данных маршрута получены в методе OnGet страницы.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Использование преобразователя параметров для настройки маршрутов страницы

Маршруты страниц, создаваемые платформой ASP.NET Core, можно настроить, используя преобразователь параметров. Преобразователь параметров реализует `IOutboundParameterTransformer` и преобразует значения параметров. Например, пользовательский преобразователь параметра `SlugifyParameterTransformer` изменяет значение маршрута `SubscriptionManagement` на `subscription-management`.

Соглашение для модели маршрутов страницы `PageRouteTransformerConvention` применяет преобразователь параметров к сегментам имен папок и файлов автоматически создаваемых маршрутов страниц в приложении. Например, для файла Razor Pages по пути */Pages/SubscriptionManagement/ViewAll.cshtml* маршрут меняется с `/SubscriptionManagement/ViewAll` на `/subscription-management/view-all`.

`PageRouteTransformerConvention` преобразует только те сегменты маршрута страницы, которые создаются автоматически на основе имен папок и файлов в Razor Pages. Он не преобразует сегменты маршрута, добавленные с помощью директивы `@page`. Это соглашение также не преобразует маршруты, добавленные с помощью <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

`PageRouteTransformerConvention` регистрируется в качестве параметра в `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>Настройка маршрута страницы

Используйте метод <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>, чтобы настроить маршрут к странице по указанному пути страницы. Созданные ссылки на страницу будут использовать заданный вами маршрут. Для установления маршрута метод `AddPageRoute` использует `AddPageRouteModelConvention`.

Пример приложения создает маршрут к `/TheContactPage` для файла *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

На страницу Contact также можно перейти с помощью `/Contact` через маршрут по умолчанию.

Настраиваемый маршрут на страницу Contact в примере приложения позволяет использовать необязательный сегмент маршрута `text` (`{text?}`). Страница также включает этот необязательный сегмент в своей директиве `@page` на случай, если посетитель переходит на страницу по маршруту `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Обратите внимание, что URL-адрес, созданный для ссылки **Contact** на отображенной странице, отражает обновленный маршрут:

![Ссылка Contact на панели навигации в примере приложения](razor-pages-conventions/_static/contact-link.png)

![В ссылке Contact в показанном HTML видно, что href имеет значение "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Перейдите на страницу Contact по ее обычному маршруту `/Contact` или по настроенному маршруту `/TheContactPage`. Если вы укажете дополнительный сегмент маршрута `text`, страница отобразит этот сегмент в HTML-кодировке:

![Пример в браузере Microsoft Edge с указанием необязательного сегмента маршрута "text" со значением "TextValue" в URL-адресе. Отображенная страница показывает значение сегмента "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Соглашения для действий модели страниц

Поставщик модели страниц по умолчанию, который реализует <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, использует соглашения, создающие точки расширения для настройки моделей страниц. Эти соглашения полезны при создании и изменении для обнаружения и обработки страниц.

В примерах этого раздела образец приложения использует класс `AddHeaderAttribute`, который является <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> и применяет заголовок ответа:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Образец показывает использование соглашений для применения атрибута ко всем страницам в папке и к одной странице.

**Соглашение для модели приложений папки**

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, вызывающий действие в экземплярах <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> для всех страниц в указанной папке.

Пример демонстрирует использование `AddFolderApplicationModelConvention` путем добавления заголовка `OtherPagesHeader` к страницам в папке *OtherPages* приложения:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Запросите страницу Page1 по адресу `localhost:5000/OtherPages/Page1` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответа страницы OtherPages/Page1, показывающие добавленный заголовок OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Соглашение для модели приложений страницы**

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, вызывающий действие в классе <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> для страницы с указанным именем.

Пример демонстрирует использование `AddPageApplicationModelConvention` путем добавления заголовка `AboutHeader` на страницу About:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Запросите страницу About по адресу `localhost:5000/About` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответа страницы About, показывающие, что добавлен заголовок AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Настройка фильтра**

Для настройки применяемого фильтра используется метод <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>. Вы можете создать класс фильтра, но пример приложения показывает реализацию фильтра в лямбда-выражении, которое реализовано "за кулисами" в качестве фабрики, возвращающей фильтр:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Модель приложений страницы используется для проверки относительного пути на наличие сегментов, ведущих к странице Page2 в папке *OtherPages*. Если условие выполняется, добавляется заголовок. Если нет, применяется `EmptyFilter`.

`EmptyFilter` является [фильтром действий](xref:mvc/controllers/filters#action-filters). Так как в Razor Pages фильтры действий не учитываются, `EmptyFilter` не оказывает действия, как и задумано, если путь не содержит `OtherPages/Page2`.

Запросите страницу Page2 по адресу `localhost:5000/OtherPages/Page2` из примера и посмотрите заголовки, чтобы проверить результат:

![В ответ для страницы Page2 добавлен заголовок OtherPagesPage2Header.](razor-pages-conventions/_static/page2-filter-header.png)

**Настройка фабрики фильтров**

Метод <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> позволяет настроить указанное производство так, чтобы [фильтры](xref:mvc/controllers/filters) применялись ко всем страницам Razor Pages.

В примере приложения приведен образец использования [фабрики фильтров](xref:mvc/controllers/filters#ifilterfactory) путем добавления на страницы приложения заголовка `FilterFactoryHeader` с двумя значениями:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Запросите страницу About по адресу `localhost:5000/About` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответов на странице About показывают два добавленных заголовка FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Фильтры MVC и фильтр страниц (IPageFilter)

[Фильтры действий](xref:mvc/controllers/filters#action-filters) MVC игнорируются в Razor Pages, так как Razor Pages использует методы обработчиков. Доступны для использования другие типы фильтров MVC: фильтры [авторизации](xref:mvc/controllers/filters#authorization-filters), [исключений](xref:mvc/controllers/filters#exception-filters), [ресурсов](xref:mvc/controllers/filters#resource-filters) и [результатов](xref:mvc/controllers/filters#result-filters). Дополнительные сведения см. в разделе [Фильтры](xref:mvc/controllers/filters).

Фильтр страниц (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) — это фильтр, применяемый к страницам Razor Pages. Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Узнайте, как использовать [соглашения поставщика модели маршрутов и приложений](xref:mvc/controllers/application-model#conventions) страниц для управления маршрутизацией, обнаружением и обработкой страниц в приложениях Razor Pages.

Когда вам необходимо настроить уникальные маршруты для отдельных страниц, используйте [соглашение AddPageRoute](#configure-a-page-route), описанное далее в этом разделе.

Чтобы указать маршрут страницы, добавить сегменты маршрута или добавить параметры в маршрут, используйте директиву `@page` страницы. Дополнительные сведения см. в разделе [Пользовательские маршруты](xref:razor-pages/index#custom-routes).

Существуют зарезервированные слова, которые нельзя использовать в качестве сегментов маршрутов или имен параметров. Дополнительные сведения см. в разделе [Маршрутизация: зарезервированные имена маршрутизации](xref:fundamentals/routing#reserved-routing-names).

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

| Сценарий | Что демонстрирует пример |
| -------- | --------------------------- |
| [Соглашения для моделей](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Добавление шаблона маршрута и заголовка к страницам приложения. |
| [Соглашения для действий с маршрутами страниц](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Добавление шаблона маршрута к страницам в папке и к одной странице. |
| [Соглашения для действий модели страниц](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (класс фильтра, лямбда-выражение или фабрика фильтров)</li></ul> | Добавление заголовка к страницам в папке, добавление заголовка к одной странице и настройка [фабрики фильтров](xref:mvc/controllers/filters#ifilterfactory) для добавления заголовка к страницам приложения. |

Соглашения Razor Pages добавляются и настраиваются с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> в <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> в коллекции служб в классе `Startup`. Следующие примеры соглашений описаны далее в этом разделе:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Порядок маршрутов

Для маршрутов указывается свойство <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>, определяющее порядок обработки (сопоставления маршрутов).

| Номер            | Поведение |
| :--------------: | -------- |
| -1               | Маршрут обрабатывается до обработки других маршрутов. |
| 0                | Порядок не задан (значение по умолчанию). Если свойство `Order` не задано (`Order = null`), значение `Order` по умолчанию для маршрута равно 0 (нулю). |
| 1, 2, &hellip; n | Указывает порядок обработки маршрутов. |

Маршруты обрабатываются в соответствии с соглашением.

* Маршруты обрабатываются по порядку (–1, 0, 1, 2 &hellip; n).
* Если маршруты имеют одинаковые значения свойства `Order`, сначала сопоставляется наиболее конкретный маршрут, а затем более общие.
* Если URL-адресу запроса соответствуют маршруты с одинаковым значением свойства `Order` и одинаковым количеством параметров, они обрабатываются в том порядке, в котором были добавлены в коллекцию <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

По возможности старайтесь не использовать строго определенный порядок обработки маршрутов. Как правило, при маршрутизации правильный маршрут выбирается посредством сопоставления URL-адресов. Если для правильной маршрутизации запросов необходимо задать свойства `Order` маршрута, схема маршрутизации приложения, скорее всего, будет малопонятна клиентам и сложна в обслуживании. Старайтесь упростить схему маршрутизации приложения. В примере приложения требуется явно определенный порядок обработки маршрутов для демонстрации нескольких сценариев маршрутизации на основе одного приложения. Однако в рабочих приложениях следует избегать задания свойства `Order` для маршрутов.

Средства маршрутизации в Razor Pages и контроллере MVC имеют общую реализацию. Сведения о порядке маршрутизации в MVC см. в разделе [Маршрутизация к действиям контроллера: упорядочение маршрутов на основе атрибутов](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Соглашения для моделей

Добавьте делегат для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, чтобы добавить [соглашения для модели](xref:mvc/controllers/application-model#conventions), применяемые к Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Добавление соглашения для модели маршрутов ко всем страницам

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, чтобы создать и добавить <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> в коллекцию экземпляров <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, которые применяются при формировании модели маршрутов страниц.

В примере приложения шаблон маршрута `{globalTemplate?}` добавляется ко всем страницам приложения:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Свойству <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> задано значение `1`. Это обеспечивает описанное ниже поведение сопоставления маршрутов в примере приложения.

* Шаблон маршрута для `TheContactPage/{text?}` добавляется далее в разделе. Маршрут страницы Contact по умолчанию имеет порядок `null` (`Order = 0`), поэтому он сопоставляется до шаблона маршрута `{globalTemplate?}`.
* Шаблон маршрута `{aboutTemplate?}` добавляется далее в разделе. Шаблону `{aboutTemplate?}` задано свойство `Order`, равное `2`. При запросе страницы About по адресу `/About/RouteDataValue` значение RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["aboutTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.
* Шаблон маршрута `{otherPagesTemplate?}` добавляется далее в разделе. Шаблону `{otherPagesTemplate?}` задано свойство `Order`, равное `2`. При запросе любой страницы в папке *Pages/OtherPages* с параметром маршрута (например, `/OtherPages/Page1/RouteDataValue`), RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["otherPagesTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.

По возможности не задавайте свойство `Order`, чтобы оно имело значение `Order = 0`. Предоставьте системе маршрутизации возможность выбирать правильный маршрут.

Параметры Razor Pages, такие как добавление <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, добавляются при добавлении MVC в коллекцию служб в `Startup.ConfigureServices`. Пример см. в [образце приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Запросите страницу About по адресу `localhost:5000/About/GlobalRouteValue` из примера и проверьте результат:

![Запрос страницы About с сегментом маршрута GlobalRouteValue. Отображенная страница показывает, что значение данных маршрута получено в методе OnGet страницы.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Добавление соглашения для модели приложений ко всем страницам

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, чтобы создать и добавить <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> в коллекцию экземпляров <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, которые применяются при формировании модели приложения страницы.

Для демонстрации этих и других соглашений далее в этом разделе в пример приложения включен класс `AddHeaderAttribute`. Конструктор класса принимает строку `name` и массив строк `values`. Эти значения используются в его методе `OnResultExecuting` для задания заголовка ответа. Класс показан полностью в разделе [Соглашения для действий модели страниц](#page-model-action-conventions) далее.

Пример приложения использует класс `AddHeaderAttribute`, чтобы добавить заголовок `GlobalHeader` ко всем страницам в приложении:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Запросите страницу About по адресу `localhost:5000/About` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответа страницы About, показывающие, что добавлен заголовок GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Добавление соглашения для модели обработчика ко всем страницам

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, чтобы создать и добавить <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> в коллекцию экземпляров <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, которые применяются при формировании модели обработчика страниц.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Соглашения для действий с маршрутами страниц

Поставщик модели маршрутов по умолчанию, который является производным от <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>, использует соглашения, создающие точки расширения для настройки маршрутов страниц.

### <a name="folder-route-model-convention"></a>Соглашение для модели маршрутов папки

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, вызывающий действие в классе <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> для всех страниц в указанной папке.

Пример приложения использует <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> для добавления шаблона маршрута `{otherPagesTemplate?}` к страницам в папке *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Свойству <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> задано значение `2`. Это гарантирует, что при указании одного значения маршрута приоритетно выбирается значение данных маршрута из шаблона `{globalTemplate?}`, которому ранее в разделе было присвоено значение `1`. При запросе страницы в папке *Pages/OtherPages* со значением параметра маршрута (например, `/OtherPages/Page1/RouteDataValue`), RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["otherPagesTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.

По возможности не задавайте свойство `Order`, чтобы оно имело значение `Order = 0`. Предоставьте системе маршрутизации возможность выбирать правильный маршрут.

Запросите страницу Page1 по адресу `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` из примера и проверьте результат:

![Страница Page1 в папке OtherPages запрашивается с сегментом маршрута GlobalRouteValue и OtherPagesRouteValue. Отображенная страница показывает, что значения данных маршрута получены в методе OnGet страницы.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Соглашение для модели маршрутов страницы

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, вызывающий действие в классе <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> для страницы с указанным именем.

Пример приложения использует `AddPageRouteModelConvention` для добавления шаблона маршрута `{aboutTemplate?}` к странице About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Свойству <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> задано значение `2`. Это гарантирует, что при указании одного значения маршрута приоритетно выбирается значение данных маршрута из шаблона `{globalTemplate?}`, которому ранее в разделе было присвоено значение `1`. При запросе страницы About со значением параметра маршрута по адресу `/About/RouteDataValue`, RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["aboutTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.

По возможности не задавайте свойство `Order`, чтобы оно имело значение `Order = 0`. Предоставьте системе маршрутизации возможность выбирать правильный маршрут.

Запросите страницу About по адресу `localhost:5000/About/GlobalRouteValue/AboutRouteValue` из примера и проверьте результат:

![Страница About запрашивается с сегментами маршрута для GlobalRouteValue и AboutRouteValue. Отображенная страница показывает, что значения данных маршрута получены в методе OnGet страницы.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Использование преобразователя параметров для настройки маршрутов страницы

Маршруты страниц, создаваемые платформой ASP.NET Core, можно настроить, используя преобразователь параметров. Преобразователь параметров реализует `IOutboundParameterTransformer` и преобразует значения параметров. Например, пользовательский преобразователь параметра `SlugifyParameterTransformer` изменяет значение маршрута `SubscriptionManagement` на `subscription-management`.

Соглашение для модели маршрутов страницы `PageRouteTransformerConvention` применяет преобразователь параметров к сегментам имен папок и файлов автоматически создаваемых маршрутов страниц в приложении. Например, для файла Razor Pages по пути */Pages/SubscriptionManagement/ViewAll.cshtml* маршрут меняется с `/SubscriptionManagement/ViewAll` на `/subscription-management/view-all`.

`PageRouteTransformerConvention` преобразует только те сегменты маршрута страницы, которые создаются автоматически на основе имен папок и файлов в Razor Pages. Он не преобразует сегменты маршрута, добавленные с помощью директивы `@page`. Это соглашение также не преобразует маршруты, добавленные с помощью <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

`PageRouteTransformerConvention` регистрируется в качестве параметра в `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>Настройка маршрута страницы

Используйте метод <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>, чтобы настроить маршрут к странице по указанному пути страницы. Созданные ссылки на страницу будут использовать заданный вами маршрут. Для установления маршрута метод `AddPageRoute` использует `AddPageRouteModelConvention`.

Пример приложения создает маршрут к `/TheContactPage` для файла *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

На страницу Contact также можно перейти с помощью `/Contact` через маршрут по умолчанию.

Настраиваемый маршрут на страницу Contact в примере приложения позволяет использовать необязательный сегмент маршрута `text` (`{text?}`). Страница также включает этот необязательный сегмент в своей директиве `@page` на случай, если посетитель переходит на страницу по маршруту `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Обратите внимание, что URL-адрес, созданный для ссылки **Contact** на отображенной странице, отражает обновленный маршрут:

![Ссылка Contact на панели навигации в примере приложения](razor-pages-conventions/_static/contact-link.png)

![В ссылке Contact в показанном HTML видно, что href имеет значение "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Перейдите на страницу Contact по ее обычному маршруту `/Contact` или по настроенному маршруту `/TheContactPage`. Если вы укажете дополнительный сегмент маршрута `text`, страница отобразит этот сегмент в HTML-кодировке:

![Пример в браузере Microsoft Edge с указанием необязательного сегмента маршрута "text" со значением "TextValue" в URL-адресе. Отображенная страница показывает значение сегмента "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Соглашения для действий модели страниц

Поставщик модели страниц по умолчанию, который реализует <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, использует соглашения, создающие точки расширения для настройки моделей страниц. Эти соглашения полезны при создании и изменении для обнаружения и обработки страниц.

В примерах этого раздела образец приложения использует класс `AddHeaderAttribute`, который является <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> и применяет заголовок ответа:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Образец показывает использование соглашений для применения атрибута ко всем страницам в папке и к одной странице.

**Соглашение для модели приложений папки**

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, вызывающий действие в экземплярах <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> для всех страниц в указанной папке.

Пример демонстрирует использование `AddFolderApplicationModelConvention` путем добавления заголовка `OtherPagesHeader` к страницам в папке *OtherPages* приложения:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Запросите страницу Page1 по адресу `localhost:5000/OtherPages/Page1` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответа страницы OtherPages/Page1, показывающие добавленный заголовок OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Соглашение для модели приложений страницы**

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, вызывающий действие в классе <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> для страницы с указанным именем.

Пример демонстрирует использование `AddPageApplicationModelConvention` путем добавления заголовка `AboutHeader` на страницу About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Запросите страницу About по адресу `localhost:5000/About` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответа страницы About, показывающие, что добавлен заголовок AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Настройка фильтра**

Для настройки применяемого фильтра используется метод <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>. Вы можете создать класс фильтра, но пример приложения показывает реализацию фильтра в лямбда-выражении, которое реализовано "за кулисами" в качестве фабрики, возвращающей фильтр:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Модель приложений страницы используется для проверки относительного пути на наличие сегментов, ведущих к странице Page2 в папке *OtherPages*. Если условие выполняется, добавляется заголовок. Если нет, применяется `EmptyFilter`.

`EmptyFilter` является [фильтром действий](xref:mvc/controllers/filters#action-filters). Так как в Razor Pages фильтры действий не учитываются, `EmptyFilter` не оказывает действия, как и задумано, если путь не содержит `OtherPages/Page2`.

Запросите страницу Page2 по адресу `localhost:5000/OtherPages/Page2` из примера и посмотрите заголовки, чтобы проверить результат:

![В ответ для страницы Page2 добавлен заголовок OtherPagesPage2Header.](razor-pages-conventions/_static/page2-filter-header.png)

**Настройка фабрики фильтров**

Метод <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> позволяет настроить указанное производство так, чтобы [фильтры](xref:mvc/controllers/filters) применялись ко всем страницам Razor Pages.

В примере приложения приведен образец использования [фабрики фильтров](xref:mvc/controllers/filters#ifilterfactory) путем добавления на страницы приложения заголовка `FilterFactoryHeader` с двумя значениями:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Запросите страницу About по адресу `localhost:5000/About` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответов на странице About показывают два добавленных заголовка FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Фильтры MVC и фильтр страниц (IPageFilter)

[Фильтры действий](xref:mvc/controllers/filters#action-filters) MVC игнорируются в Razor Pages, так как Razor Pages использует методы обработчиков. Доступны для использования другие типы фильтров MVC: фильтры [авторизации](xref:mvc/controllers/filters#authorization-filters), [исключений](xref:mvc/controllers/filters#exception-filters), [ресурсов](xref:mvc/controllers/filters#resource-filters) и [результатов](xref:mvc/controllers/filters#result-filters). Дополнительные сведения см. в разделе [Фильтры](xref:mvc/controllers/filters).

Фильтр страниц (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) — это фильтр, применяемый к страницам Razor Pages. Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Узнайте, как использовать [соглашения поставщика модели маршрутов и приложений](xref:mvc/controllers/application-model#conventions) страниц для управления маршрутизацией, обнаружением и обработкой страниц в приложениях Razor Pages.

Когда вам необходимо настроить уникальные маршруты для отдельных страниц, используйте [соглашение AddPageRoute](#configure-a-page-route), описанное далее в этом разделе.

Чтобы указать маршрут страницы, добавить сегменты маршрута или добавить параметры в маршрут, используйте директиву `@page` страницы. Дополнительные сведения см. в разделе [Пользовательские маршруты](xref:razor-pages/index#custom-routes).

Существуют зарезервированные слова, которые нельзя использовать в качестве сегментов маршрутов или имен параметров. Дополнительные сведения см. в разделе [Маршрутизация: зарезервированные имена маршрутизации](xref:fundamentals/routing#reserved-routing-names).

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

| Сценарий | Что демонстрирует пример |
| -------- | --------------------------- |
| [Соглашения для моделей](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Добавление шаблона маршрута и заголовка к страницам приложения. |
| [Соглашения для действий с маршрутами страниц](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Добавление шаблона маршрута к страницам в папке и к одной странице. |
| [Соглашения для действий модели страниц](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (класс фильтра, лямбда-выражение или фабрика фильтров)</li></ul> | Добавление заголовка к страницам в папке, добавление заголовка к одной странице и настройка [фабрики фильтров](xref:mvc/controllers/filters#ifilterfactory) для добавления заголовка к страницам приложения. |

Соглашения Razor Pages добавляются и настраиваются с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> в <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> в коллекции служб в классе `Startup`. Следующие примеры соглашений описаны далее в этом разделе:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Порядок маршрутов

Для маршрутов указывается свойство <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>, определяющее порядок обработки (сопоставления маршрутов).

| Номер            | Поведение |
| :--------------: | -------- |
| -1               | Маршрут обрабатывается до обработки других маршрутов. |
| 0                | Порядок не задан (значение по умолчанию). Если свойство `Order` не задано (`Order = null`), значение `Order` по умолчанию для маршрута равно 0 (нулю). |
| 1, 2, &hellip; n | Указывает порядок обработки маршрутов. |

Маршруты обрабатываются в соответствии с соглашением.

* Маршруты обрабатываются по порядку (–1, 0, 1, 2 &hellip; n).
* Если маршруты имеют одинаковые значения свойства `Order`, сначала сопоставляется наиболее конкретный маршрут, а затем более общие.
* Если URL-адресу запроса соответствуют маршруты с одинаковым значением свойства `Order` и одинаковым количеством параметров, они обрабатываются в том порядке, в котором были добавлены в коллекцию <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

По возможности старайтесь не использовать строго определенный порядок обработки маршрутов. Как правило, при маршрутизации правильный маршрут выбирается посредством сопоставления URL-адресов. Если для правильной маршрутизации запросов необходимо задать свойства `Order` маршрута, схема маршрутизации приложения, скорее всего, будет малопонятна клиентам и сложна в обслуживании. Старайтесь упростить схему маршрутизации приложения. В примере приложения требуется явно определенный порядок обработки маршрутов для демонстрации нескольких сценариев маршрутизации на основе одного приложения. Однако в рабочих приложениях следует избегать задания свойства `Order` для маршрутов.

Средства маршрутизации в Razor Pages и контроллере MVC имеют общую реализацию. Сведения о порядке маршрутизации в MVC см. в разделе [Маршрутизация к действиям контроллера: упорядочение маршрутов на основе атрибутов](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Соглашения для моделей

Добавьте делегат для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, чтобы добавить [соглашения для модели](xref:mvc/controllers/application-model#conventions), применяемые к Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Добавление соглашения для модели маршрутов ко всем страницам

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, чтобы создать и добавить <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> в коллекцию экземпляров <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, которые применяются при формировании модели маршрутов страниц.

В примере приложения шаблон маршрута `{globalTemplate?}` добавляется ко всем страницам приложения:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Свойству <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> задано значение `1`. Это обеспечивает описанное ниже поведение сопоставления маршрутов в примере приложения.

* Шаблон маршрута для `TheContactPage/{text?}` добавляется далее в разделе. Маршрут страницы Contact по умолчанию имеет порядок `null` (`Order = 0`), поэтому он сопоставляется до шаблона маршрута `{globalTemplate?}`.
* Шаблон маршрута `{aboutTemplate?}` добавляется далее в разделе. Шаблону `{aboutTemplate?}` задано свойство `Order`, равное `2`. При запросе страницы About по адресу `/About/RouteDataValue` значение RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["aboutTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.
* Шаблон маршрута `{otherPagesTemplate?}` добавляется далее в разделе. Шаблону `{otherPagesTemplate?}` задано свойство `Order`, равное `2`. При запросе любой страницы в папке *Pages/OtherPages* с параметром маршрута (например, `/OtherPages/Page1/RouteDataValue`), RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["otherPagesTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.

По возможности не задавайте свойство `Order`, чтобы оно имело значение `Order = 0`. Предоставьте системе маршрутизации возможность выбирать правильный маршрут.

Параметры Razor Pages, такие как добавление <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, добавляются при добавлении MVC в коллекцию служб в `Startup.ConfigureServices`. Пример см. в [образце приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Запросите страницу About по адресу `localhost:5000/About/GlobalRouteValue` из примера и проверьте результат:

![Запрос страницы About с сегментом маршрута GlobalRouteValue. Отображенная страница показывает, что значение данных маршрута получено в методе OnGet страницы.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Добавление соглашения для модели приложений ко всем страницам

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, чтобы создать и добавить <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> в коллекцию экземпляров <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, которые применяются при формировании модели приложения страницы.

Для демонстрации этих и других соглашений далее в этом разделе в пример приложения включен класс `AddHeaderAttribute`. Конструктор класса принимает строку `name` и массив строк `values`. Эти значения используются в его методе `OnResultExecuting` для задания заголовка ответа. Класс показан полностью в разделе [Соглашения для действий модели страниц](#page-model-action-conventions) далее.

Пример приложения использует класс `AddHeaderAttribute`, чтобы добавить заголовок `GlobalHeader` ко всем страницам в приложении:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Запросите страницу About по адресу `localhost:5000/About` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответа страницы About, показывающие, что добавлен заголовок GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Добавление соглашения для модели обработчика ко всем страницам

Используйте свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, чтобы создать и добавить <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> в коллекцию экземпляров <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, которые применяются при формировании модели обработчика страниц.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Соглашения для действий с маршрутами страниц

Поставщик модели маршрутов по умолчанию, который является производным от <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>, использует соглашения, создающие точки расширения для настройки маршрутов страниц.

### <a name="folder-route-model-convention"></a>Соглашение для модели маршрутов папки

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, вызывающий действие в классе <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> для всех страниц в указанной папке.

Пример приложения использует <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> для добавления шаблона маршрута `{otherPagesTemplate?}` к страницам в папке *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Свойству <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> задано значение `2`. Это гарантирует, что при указании одного значения маршрута приоритетно выбирается значение данных маршрута из шаблона `{globalTemplate?}`, которому ранее в разделе было присвоено значение `1`. При запросе страницы в папке *Pages/OtherPages* со значением параметра маршрута (например, `/OtherPages/Page1/RouteDataValue`), RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["otherPagesTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.

По возможности не задавайте свойство `Order`, чтобы оно имело значение `Order = 0`. Предоставьте системе маршрутизации возможность выбирать правильный маршрут.

Запросите страницу Page1 по адресу `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` из примера и проверьте результат:

![Страница Page1 в папке OtherPages запрашивается с сегментом маршрута GlobalRouteValue и OtherPagesRouteValue. Отображенная страница показывает, что значения данных маршрута получены в методе OnGet страницы.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Соглашение для модели маршрутов страницы

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, вызывающий действие в классе <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> для страницы с указанным именем.

Пример приложения использует `AddPageRouteModelConvention` для добавления шаблона маршрута `{aboutTemplate?}` к странице About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Свойству <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> для <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> задано значение `2`. Это гарантирует, что при указании одного значения маршрута приоритетно выбирается значение данных маршрута из шаблона `{globalTemplate?}`, которому ранее в разделе было присвоено значение `1`. При запросе страницы About со значением параметра маршрута по адресу `/About/RouteDataValue`, RouteDataValue загружается в `RouteData.Values["globalTemplate"]` (`Order = 1`), а не в `RouteData.Values["aboutTemplate"]` (`Order = 2`). Это происходит из-за значения свойства `Order`.

По возможности не задавайте свойство `Order`, чтобы оно имело значение `Order = 0`. Предоставьте системе маршрутизации возможность выбирать правильный маршрут.

Запросите страницу About по адресу `localhost:5000/About/GlobalRouteValue/AboutRouteValue` из примера и проверьте результат:

![Страница About запрашивается с сегментами маршрута для GlobalRouteValue и AboutRouteValue. Отображенная страница показывает, что значения данных маршрута получены в методе OnGet страницы.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Настройка маршрута страницы

Используйте метод <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>, чтобы настроить маршрут к странице по указанному пути страницы. Созданные ссылки на страницу будут использовать заданный вами маршрут. Для установления маршрута метод `AddPageRoute` использует `AddPageRouteModelConvention`.

Пример приложения создает маршрут к `/TheContactPage` для файла *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

На страницу Contact также можно перейти с помощью `/Contact` через маршрут по умолчанию.

Настраиваемый маршрут на страницу Contact в примере приложения позволяет использовать необязательный сегмент маршрута `text` (`{text?}`). Страница также включает этот необязательный сегмент в своей директиве `@page` на случай, если посетитель переходит на страницу по маршруту `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Обратите внимание, что URL-адрес, созданный для ссылки **Contact** на отображенной странице, отражает обновленный маршрут:

![Ссылка Contact на панели навигации в примере приложения](razor-pages-conventions/_static/contact-link.png)

![В ссылке Contact в показанном HTML видно, что href имеет значение "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Перейдите на страницу Contact по ее обычному маршруту `/Contact` или по настроенному маршруту `/TheContactPage`. Если вы укажете дополнительный сегмент маршрута `text`, страница отобразит этот сегмент в HTML-кодировке:

![Пример в браузере Microsoft Edge с указанием необязательного сегмента маршрута "text" со значением "TextValue" в URL-адресе. Отображенная страница показывает значение сегмента "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Соглашения для действий модели страниц

Поставщик модели страниц по умолчанию, который реализует <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, использует соглашения, создающие точки расширения для настройки моделей страниц. Эти соглашения полезны при создании и изменении для обнаружения и обработки страниц.

В примерах этого раздела образец приложения использует класс `AddHeaderAttribute`, который является <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> и применяет заголовок ответа:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Образец показывает использование соглашений для применения атрибута ко всем страницам в папке и к одной странице.

**Соглашение для модели приложений папки**

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, вызывающий действие в экземплярах <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> для всех страниц в указанной папке.

Пример демонстрирует использование `AddFolderApplicationModelConvention` путем добавления заголовка `OtherPagesHeader` к страницам в папке *OtherPages* приложения:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Запросите страницу Page1 по адресу `localhost:5000/OtherPages/Page1` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответа страницы OtherPages/Page1, показывающие добавленный заголовок OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Соглашение для модели приложений страницы**

Используйте метод <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>, чтобы создать и добавить интерфейс <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, вызывающий действие в классе <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> для страницы с указанным именем.

Пример демонстрирует использование `AddPageApplicationModelConvention` путем добавления заголовка `AboutHeader` на страницу About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Запросите страницу About по адресу `localhost:5000/About` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответа страницы About, показывающие, что добавлен заголовок AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Настройка фильтра**

Для настройки применяемого фильтра используется метод <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>. Вы можете создать класс фильтра, но пример приложения показывает реализацию фильтра в лямбда-выражении, которое реализовано "за кулисами" в качестве фабрики, возвращающей фильтр:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Модель приложений страницы используется для проверки относительного пути на наличие сегментов, ведущих к странице Page2 в папке *OtherPages*. Если условие выполняется, добавляется заголовок. Если нет, применяется `EmptyFilter`.

`EmptyFilter` является [фильтром действий](xref:mvc/controllers/filters#action-filters). Так как в Razor Pages фильтры действий не учитываются, `EmptyFilter` не оказывает действия, как и задумано, если путь не содержит `OtherPages/Page2`.

Запросите страницу Page2 по адресу `localhost:5000/OtherPages/Page2` из примера и посмотрите заголовки, чтобы проверить результат:

![В ответ для страницы Page2 добавлен заголовок OtherPagesPage2Header.](razor-pages-conventions/_static/page2-filter-header.png)

**Настройка фабрики фильтров**

Метод <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> позволяет настроить указанное производство так, чтобы [фильтры](xref:mvc/controllers/filters) применялись ко всем страницам Razor Pages.

В примере приложения приведен образец использования [фабрики фильтров](xref:mvc/controllers/filters#ifilterfactory) путем добавления на страницы приложения заголовка `FilterFactoryHeader` с двумя значениями:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Запросите страницу About по адресу `localhost:5000/About` из примера и посмотрите заголовки, чтобы проверить результат:

![Заголовки ответов на странице About показывают два добавленных заголовка FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Фильтры MVC и фильтр страниц (IPageFilter)

[Фильтры действий](xref:mvc/controllers/filters#action-filters) MVC игнорируются в Razor Pages, так как Razor Pages использует методы обработчиков. Доступны для использования другие типы фильтров MVC: фильтры [авторизации](xref:mvc/controllers/filters#authorization-filters), [исключений](xref:mvc/controllers/filters#exception-filters), [ресурсов](xref:mvc/controllers/filters#resource-filters) и [результатов](xref:mvc/controllers/filters#result-filters). Дополнительные сведения см. в разделе [Фильтры](xref:mvc/controllers/filters).

Фильтр страниц (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) — это фильтр, применяемый к страницам Razor Pages. Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
