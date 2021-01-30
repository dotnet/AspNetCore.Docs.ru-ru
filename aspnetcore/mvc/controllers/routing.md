---
title: Маршрутизация к действиям контроллера в ASP.NET Core
author: rick-anderson
description: Узнайте, как в MVC ASP.NET Core используется ПО промежуточного слоя маршрутизации для сопоставления URL-адресов входящих запросов с действиями.
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: 04ece9628265135efd0dd92d29c8b14fc897329e
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057360"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Маршрутизация к действиям контроллера в ASP.NET Core

Авторы: [Райан Новак](https://github.com/rynowak) (Ryan Nowak), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-3.0"

Контроллеры ASP.NET Core используют по [промежуточного слоя](xref:fundamentals/middleware/index) маршрутизации для сопоставления URL-адресов входящих запросов и сопоставления их с [действиями](#action).  Шаблоны маршрутов:

* Определяются в коде запуска или атрибутах.
* Описание способов сопоставления путей URL-адресов с [действиями](#action).
* Используются для создания URL-адресов для ссылок. Созданные ссылки обычно возвращаются в ответах.

Действия либо [направляются по соглашению](#cr) , либо [маршрутизируются по атрибуту](#ar). При размещении маршрута на контроллере или [действии](#action) он пересылается по атрибуту. Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).

Этот документ:

* Объясняет взаимодействие между MVC и маршрутизацией.
  * Как обычные приложения MVC используют функции маршрутизации.
  * Рассматриваются оба:
    * [Обычная маршрутизация](#cr) обычно используется с контроллерами и представлениями.
    * *Маршрутизация атрибутов* , используемая с API-интерфейсами RESTful. Если вы в основном заинтересованы в маршрутизации для API-интерфейсов RESTFUL, перейдите к разделу [Маршрутизация атрибутов для интерфейсов API для остальных](#ar) компонентов.
  * Дополнительные сведения о маршрутизации см. в разделе [Маршрутизация](xref:fundamentals/routing) .
* Относится к системе маршрутизации по умолчанию, добавленной в ASP.NET Core 3,0, называемую маршрутизацией конечных точек. В целях совместимости можно использовать контроллеры с предыдущей версией маршрутизации. Инструкции см. в [руководству по миграции 2.2-3.0](xref:migration/22-to-30) . Справочные материалы по устаревшей системе маршрутизации см. в [версии 2,2 этого документа](xref:mvc/controllers/routing?view=aspnetcore-2.2) .

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Настройка обычного маршрута

`Startup.Configure` При использовании [обычной маршрутизации](#crd)обычно имеет код, аналогичный приведенному ниже.

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Внутри вызова метод <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> используется для создания одного маршрута. Единственный маршрут называется `default` Route. Большинство приложений с контроллерами и представлениями используют шаблон маршрута, аналогичный `default` маршруту. Интерфейсы API-интерфейсов для интерфейса остальных должны использовать [маршрутизацию атрибутов](#ar).

Шаблон маршрута `"{controller=Home}/{action=Index}/{id?}"` :

* Соответствует URL-пути, например `/Products/Details/5`
* Извлекает значения маршрута `{ controller = Products, action = Details, id = 5 }` путем маркировки пути. Извлечение значений маршрута приводит к совпадению, если у приложения есть контроллер с именем `ProductsController` и `Details` действие:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5` модель привязывает значение, `id = 5` чтобы задать `id` параметр `5` . Дополнительные сведения см. в разделе [Привязка модели](xref:mvc/models/model-binding) .
* `{controller=Home}` Определяет `Home` как значение по умолчанию `controller` .
* `{action=Index}` Определяет `Index` как значение по умолчанию `action` .
*  `?`Символ в `{id?}` определяет `id` как необязательный.
  * Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления. Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).
* Соответствует пути URL-адреса `/` .
* Создает значения маршрута `{ controller = Home, action = Index }` .

Значения для `controller` и `action` используют значения по умолчанию. `id` не создает значение, так как в URL-пути нет соответствующего сегмента. `/` соответствует, только если существует `HomeController` действие и `Index` :

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Используя предыдущее определение контроллера и шаблон маршрута, `HomeController.Index` действие выполняется для следующих URL-путей:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

URL-путь `/` использует `Home` контроллеры и действие шаблона маршрута по умолчанию `Index` . URL-путь `/Home` использует действие по умолчанию шаблона маршрута `Index` .

Универсальный метод <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Меняющей

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Маршрутизация настраивается с помощью по <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> промежуточного слоя и. Использование контроллеров:
>
> * Вызовите <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> внутри `UseEndpoints` , чтобы сопоставлять [атрибуты, перенаправляемые](#ar) контроллерам.
> * Вызовите метод <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> или <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> , чтобы связать как [соглашение с маршрутизацией](#cr) , так и [перенаправляемые](#ar) контроллеры.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Маршрутизация на основе соглашений

Стандартная маршрутизация используется с контроллерами и представлениями. Маршрут `default`:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

Предыдущий маршрут является примером *обычного маршрута*. Он называется *обычной маршрутизацией* , так как он устанавливает *соглашение* для URL-путей:

* Первый сегмент пути, `{controller=Home}` сопоставляется с именем контроллера.
* Второй сегмент, `{action=Index}` сопоставляется с именем [действия](#action) .
* Третий сегмент `{id?}` используется для необязательного `id` . `?`В `{id?}` делает его необязательным. `id` используется для соотнесения с сущностью модели.

При использовании этого `default` маршрута путь URL-адреса:

* `/Products/List` сопоставляется с `ProductsController.List` действием.
* `/Blog/Article/17` сопоставляется с `BlogController.Article` и, как правило, модель привязывает `id` параметр к 17.

Это сопоставление:

* Основаны **только** на именах контроллера и [действий](#action) .
* Не основано на пространствах имен, расположениях исходных файлов или параметров метода.

Использование обычной маршрутизации с маршрутом по умолчанию позволяет создавать приложения без необходимости создания нового шаблона URL-адреса для каждого действия. Для приложения с действиями в стиле [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) с согласованностью URL-адресов на контроллерах:

* Помогает упростить код.
* Делает пользовательский интерфейс более предсказуемым.

> [!WARNING]
> `id`В предыдущем коде шаблон маршрута определен как необязательный. Действия могут выполняться без дополнительного идентификатора, предоставленного в качестве части URL-адреса. Как правило, если `id` параметр опущен из URL-адреса:
>
> * `id` задается `0` привязкой модели.
> * Сущность не найдена в соответствующей базе данных `id == 0` .
>
> [Маршрутизация атрибутов](#ar) обеспечивает детальный контроль, чтобы сделать идентификатор необходимым для некоторых действий, а не для других. По соглашению в документации содержатся необязательные параметры, например, `id` если они, скорее всего, будут отображаться в правильном использовании.

Для большинства приложений следует выбрать базовую описательную схему маршрутизации таким образом, чтобы URL-адреса были удобочитаемыми и осмысленными. Традиционный маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`.

* Поддерживает основную и описательную схемы маршрутизации.
* Является отправной точкой для приложений на базе пользовательского интерфейса.
* — Единственный шаблон маршрута, необходимый для многих приложений пользовательского веб-интерфейса. Для больших веб-приложений пользовательского интерфейса часто требуется еще один маршрут, использующий [области](#areas) .

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> и <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :

* Автоматически назначить значение **заказа** своим конечным точкам в соответствии с порядком их вызова.

Маршрутизация конечных точек в ASP.NET Core 3.0 и более поздних версий

* Не имеет концепции маршрутов.
* Не предоставляет гарантий упорядочения для выполнения расширяемости, все конечные точки обрабатываются одновременно.

Чтобы увидеть, как встроенные реализации маршрутизации, такие как <xref:Microsoft.AspNetCore.Routing.Route>, сопоставляются с запросами, включите [ведение журнала](xref:fundamentals/logging/index).

[Маршрутизация атрибутов](#ar) объясняется далее в этом документе.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Несколько обычных маршрутов

В можно добавить несколько [обычных маршрутов](#cr) `UseEndpoints` , добавив дополнительные вызовы в <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> и <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> . Это позволяет определить несколько соглашений или добавить традиционные маршруты, предназначенные для конкретного [действия](#action), например:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

`blog`Маршрут в приведенном выше коде является **выделенным обычным маршрутом**. Он называется выделенным обычным маршрутом по следующим причинам.

* В нем используется [Обычная маршрутизация](#cr).
* Он предназначен для конкретного [действия](#action).

Поскольку `controller` и `action` не отображаются в шаблоне маршрута в `"blog/{*article}"` качестве параметров:

* Они могут иметь только значения по умолчанию `{ controller = "Blog", action = "Article" }` .
* Этот маршрут всегда сопоставлен с действием `BlogController.Article` .

`/Blog`, `/Blog/Article` и `/Blog/{any-string}` — это единственные URL-пути, соответствующие маршруту блога.

Предшествующий пример:

* `blog` маршрут имеет более высокий приоритет для совпадений, чем `default` маршрут, так как он добавляется первым.
* — Пример маршрутизации в [стиле "в формате"](https://developer.mozilla.org/docs/Glossary/Slug) , в которой имя статьи является частью URL-адреса.

> [!WARNING]
> В ASP.NET Core 3,0 и более поздних версиях маршрутизация не выполняет:
> * Определите концепцию, называемую *маршрутом*. `UseRouting` добавляет соответствие маршрута в конвейер ПО промежуточного слоя. По `UseRouting` промежуточного слоя просматривает набор конечных точек, определенных в приложении, и выбирает наилучшее совпадение конечных точек на основе запроса.
> * Предоставьте гарантии порядка выполнения расширяемости, например <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> или <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .
>
>См. раздел о [маршрутизации](xref:fundamentals/routing) для справочных материалов по маршрутизации.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Стандартный порядок маршрутизации

Обычная маршрутизация соответствует только комбинации действий и контроллера, определенных приложением. Это предназначено для упрощения случаев, когда обычные маршруты перекрываются.
Добавление маршрутов с помощью <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> и <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> автоматически назначает значение порядка своим конечным точкам в соответствии с порядком их вызова. Совпадает с более высоким приоритетом на маршруте, который отображается ранее. При маршрутизации на основе соглашений учитывается порядок. Как правило, маршруты с областями следует размещать раньше, так как они более специфичны, чем маршруты без области. [Выделенные традиционные маршруты](#dcr) с перекрестными параметрами маршрутов `{*article}` , например, могут сделать маршрут слишком [жадным](xref:fundamentals/routing#greedy), то есть сопоставлять URL-адреса, которые вы хотели бы сопоставить с другими маршрутами. Помещайте жадные маршруты позже в таблице маршрутов, чтобы предотвратить жадные соответствия.

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Разрешение неоднозначных действий

При совпадении двух конечных точек через маршрутизацию маршрутизация должна выполнить одно из следующих действий:

* Выберите лучший кандидат.
* Создание исключения.

Например:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Предыдущий контроллер определяет два соответствующих действия:

* URL-путь `/Products33/Edit/17`
* Перенаправление данных `{ controller = Products33, action = Edit, id = 17 }` .

Это типичный шаблон для контроллеров MVC:

* `Edit(int)` Отображает форму для изменения продукта.
* `Edit(int, Product)` обрабатывает опубликованную форму.

Чтобы разрешить правильный маршрут, выполните следующие действия.

* `Edit(int, Product)` выбирается, если запрос является HTTP `POST` .
* `Edit(int)` выбран, если [команда HTTP](#verb) является любым другим. `Edit(int)` обычно вызывается через `GET` .

Объект <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , предоставляется для маршрутизации, чтобы его можно было выбрать в зависимости от метода HTTP запроса. `HttpPostAttribute` `Edit(int, Product)` Чем больше, тем лучше соответствует `Edit(int)` .

Важно понимать роль таких атрибутов, как `HttpPostAttribute` . Аналогичные атрибуты определяются для других [HTTP-команд](#verb). В [обычной маршрутизации](#cr)для действий используется одно и то же имя действия, когда они входят в форму отображения, Рабочий процесс отправки формы. Например, см. раздел [изучение двух методов действия Edit](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Если службе маршрутизации не удается выбрать лучший вариант, <xref:System.Reflection.AmbiguousMatchException> создается исключение, в котором перечисляются несколько совпадающих конечных точек.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Имена обычных маршрутов

Строки  `"blog"` и `"default"` в следующих примерах являются обычными именами маршрутов:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Имена маршрутов придают маршруту логическое имя. Именованный маршрут можно использовать для создания URL-адресов. Использование именованного маршрута упрощает создание URL-адресов, когда порядок маршрутов может усложнить создание URL-адреса. Имена маршрутов должны быть уникальными для всего приложения.

Имена маршрутов:

* Не влияют на сопоставление URL-адресов или обработку запросов.
* Используются только для создания URL-адресов.

Концепция имени маршрута представлена в маршрутизации как [иендпоинтнамеметадата](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). Термины **имя маршрута** и **имя конечной точки**:

* Являются взаимозаменяемыми.
* То, какой из них используется в документации и коде, зависит от описываемого API.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Маршрутизация атрибутов для интерфейсов API RESTFUL

Для моделирования функциональных возможностей приложения в качестве набора ресурсов, в которых операции представлены [HTTP-командами](#verb), интерфейсы API-интерфейса должны использовать маршрутизацию атрибутов.

При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов. Следующий `StartUp.Configure` код является типичным для REST API и используется в следующем примере:

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

В приведенном выше коде <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> вызывается внутри, `UseEndpoints` чтобы сопоставлять атрибуты, перенаправляемые контроллерами.

В следующем примере:

* Используется предыдущий `Configure` метод.
* `HomeController` совпадает с набором URL-адресов, похожим на стандартный маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}` .

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

`HomeController.Index`Действие выполняется для любого из URL-путей,, `/` `/Home` `/Home/Index` или `/Home/Index/3` .

В этом примере демонстрируется ключевое различие в программировании между маршрутизацией атрибутов и [обычной маршрутизацией](#cr). Маршрутизация атрибутов требует больше входных данных для указания маршрута. Стандартный маршрут по умолчанию обрабатывает маршруты более кратко. Однако маршрутизация атрибутов разрешает и требует точного контроля над тем, какие шаблоны маршрутов применяются к каждому [действию](#action).

При использовании маршрутизации атрибутов контроллер и имена действий воспроизводятся, если не используется [замена токенов](#routing-token-replacement-templates-ref-label) . В следующем примере совпадают те же URL-адреса, что и в предыдущем примере:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

В следующем коде используется замена токенов для `action` и `controller` :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Следующий код применяется `[Route("[controller]/[action]")]` к контроллеру:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

В приведенном выше коде `Index` шаблоны методов должны находиться в начале `/` или `~/` в шаблонах маршрутов. Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.

Сведения о выборе шаблона маршрутов см. в разделе [приоритет шаблона маршрута](xref:fundamentals/routing#rtp) .

## <a name="reserved-routing-names"></a>Зарезервированные имена маршрутизации

Следующие ключевые слова являются зарезервированными именами параметров маршрута при использовании контроллеров или Razor страниц:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Использование `page` в качестве параметра маршрута с маршрутизацией атрибутов является распространенной ошибкой. Это приводит к несогласованности и путанице при формировании URL-адреса.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Специальные имена параметров используются при формировании URL-адресов, чтобы определить, относится ли операция создания URL-адреса к Razor странице или к контроллеру.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Шаблоны HTTP-команд

ASP.NET Core содержит следующие шаблоны HTTP-команд:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[Хттппут]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[Хттпделете]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[Хттфеад]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[Хттппатч]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Шаблоны маршрутов

ASP.NET Core имеет следующие шаблоны маршрутов:

* Все [шаблоны HTTP-команд](#verb) являются шаблонами маршрутов.
* [Направлены](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Маршрутизация атрибутов с помощью атрибутов глагола HTTP

Рассмотрим следующий контроллер:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

В приведенном выше коде:

* Каждое действие содержит `[HttpGet]` атрибут, который ограничивает сопоставление только запросами HTTP GET.
* `GetProduct`Действие включает `"{id}"` шаблон, поэтому `id` добавляется к `"api/[controller]"` шаблону на контроллере. Шаблон методов — `"api/[controller]/"{id}""` . Поэтому это действие соответствует только запросам Get для формы `/api/test2/xyz` , `/api/test2/123` , `/api/test2/{any string}` и т. д.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* `GetIntProduct`Действие содержит `"int/{id:int}")` шаблон. `:int`Часть шаблона ограничивает `id` значения маршрута строками, которые могут быть преобразованы в целые числа. Запрос GET к `/api/test2/int/abc` :
  * Не соответствует этому действию.
  * Возвращает ошибку [404 не найден](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* `GetInt2Product`Действие содержит `{id}` в шаблоне, но не ограничивает `id` значения, которые можно преобразовать в целое число. Запрос GET к `/api/test2/int2/abc` :
  * Соответствует этому маршруту.
  * Привязка модели не может преобразовать `abc` в целое число. `id`Параметр метода имеет тип Integer.
  * Возвращает [400 Неверный запрос](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , так как привязка модели не смогла преобразовать `abc` в целое число.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Маршрутизация атрибутов может использовать <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> такие атрибуты <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , как, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> и <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> . Все атрибуты [HTTP-команды](#verb) принимают шаблон маршрута. В следующем примере показаны два действия, которые соответствуют одному шаблону маршрута:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Использование URL-пути `/products3` :

* `MyProductsController.ListProducts`Действие выполняется, когда [HTTP-команда](#verb) имеет значение `GET` .
* `MyProductsController.CreateProduct`Действие выполняется, когда [HTTP-команда](#verb) имеет значение `POST` .

При создании REST API в редких случаях необходимо использовать `[Route(...)]` метод действия, поскольку действие принимает все методы HTTP. Лучше использовать более конкретный [атрибут HTTP-команды](#verb) , чтобы точно определить, что поддерживает API. Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.

Для моделирования функциональных возможностей приложения в качестве набора ресурсов, в которых операции представлены HTTP-командами, интерфейсы API-интерфейса должны использовать маршрутизацию атрибутов. Это означает, что многие операции, например GET и POST для одного и того же логического ресурса, используют один и тот же URL-адрес. Маршрутизация с помощью атрибутов обеспечивает необходимый уровень контроля, позволяющий тщательно разработать схему общедоступных конечных точек API-интерфейса.

Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута. В следующем примере требуется в `id` качестве части URL-пути:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

`Products2ApiController.GetProduct(int)`Действие:

* Выполняется с URL-путем, например `/products2/3`
* Не выполняется с URL-адресом `/products2` .

Атрибут [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) позволяет выполнять действие для ограничения поддерживаемых типов содержимого запросов. Дополнительные сведения см. [в разделе Определение поддерживаемых типов содержимого запросов с помощью атрибута использования](xref:web-api/index#consumes).

 Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).

Дополнительные сведения о см `[ApiController]` . в разделе [атрибут ApiController](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Имя маршрута

В следующем коде определяется имя маршрута`Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута. Имена маршрутов:

* Не влияют на поведение маршрутизации в соответствии с URL-адресом.
* Используются только для создания URL-адресов.

Имена маршрутов должны быть уникальными в пределах приложения.

Сравните предыдущий код с обычным маршрутом по умолчанию, который определяет `id` параметр как необязательный ( `{id?}` ). Возможность точного указания интерфейсов API имеет свои преимущества, такие как разрешение `/products` и `/products/5` возможность отправки в различные действия.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Объединение маршрутов атрибутов

Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий. Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий. В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

В предыдущем примере:

* Путь URL-адреса `/products` может совпадать `ProductsApi.ListProducts`
* Путь URL-адреса `/products/5` может совпадать `ProductsApi.GetProduct(int)` .

Оба эти действия соответствуют только HTTP `GET` , так как они помечены `[HttpGet]` атрибутом.

Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру. Следующий пример соответствует набору URL-путей, аналогичному маршруту по умолчанию.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

В следующей таблице описаны `[Route]` атрибуты в приведенном выше коде.

| attribute               | Объединяет с `[Route("Home")]` | Определение шаблона маршрута |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Да | `"Home"` |
| `[Route("Index")]` | Да | `"Home/Index"` |
| `[Route("/")]` | **Нет** | `""` |
| `[Route("About")]` | Да | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Порядок маршрута атрибута

Маршрутизация создает дерево и сопоставляет все конечные точки одновременно:

* Записи маршрутов ведут себя так, как если бы они были размещены в идеальном порядке.
* Наиболее конкретные маршруты могут выполняться до более общих маршрутов.

Например, маршрут атрибута, например, `blog/search/{topic}` более специфичен, чем маршрут атрибута, например `blog/{*article}` . `blog/search/{topic}`Маршрут имеет более высокий приоритет, чем по умолчанию, так как он является более конкретным. При использовании [обычной маршрутизации](#cr)разработчик несет ответственность за размещение маршрутов в нужном порядке.

Маршруты атрибутов могут настраивать порядок с помощью <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> Свойства. Все указанные в платформе [атрибуты маршрутов](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) включают `Order` . Маршруты обрабатываются в порядке возрастания значения свойства `Order`. Порядок по умолчанию — `0`. Настройка маршрута с помощью `Order = -1` запусков перед маршрутами, которые не задают порядок. Настройка маршрута с помощью команды `Order = 1` выполняется после упорядочения маршрутов по умолчанию.

**Избегайте** в зависимости от `Order` . Если для правильной маршрутизации URL-адресу приложения требуются явные значения порядка, скорее всего, это приведет к путанице с клиентами. Как правило, маршрутизация атрибутов выбирает правильный маршрут с сопоставлением URL-адресов. Если порядок по умолчанию, используемый для создания URL-адреса, не работает, использование имени маршрута в качестве переопределения обычно проще, чем применение `Order` Свойства.

Рассмотрим следующие два контроллера, которые определяют сопоставление маршрутов `/home` .

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

`/home`При запросе с помощью приведенного выше кода создается исключение, аналогичное следующему:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Добавление `Order` к одному из атрибутов маршрута разрешает неоднозначность:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

В приведенном выше коде `/home` запускает `HomeController.Index` конечную точку. Для получения `MyDemoController.MyIndex` запроса `/home/MyIndex` . **Примечание.**

* Приведенный выше код представляет собой пример или низкую структуру маршрутизации. Он использовался для иллюстрации `Order` Свойства.
* `Order`Свойство разрешает неоднозначность, но этот шаблон не может быть сопоставлен. Лучше удалить `[Route("Home")]` шаблон.

См. страницы соглашения о маршрутах [ Razor и приложениях. порядок](xref:razor-pages/razor-pages-conventions#route-order) маршрута для получения сведений о порядке маршрутов со Razor страницами.

В некоторых случаях возвращается ошибка HTTP 500 с неоднозначными маршрутами. Используйте [ведение журнала](xref:fundamentals/logging/index) , чтобы узнать, какие конечные точки вызывают `AmbiguousMatchException` .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Замена токенов в шаблонах маршрутов [контроллер], [действие], [область]

Для удобства маршруты к атрибутам поддерживают *замену маркера* путем заключения маркера в квадратные скобки ( `[` , `]` ). Маркеры `[action]` , `[area]` и `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

В приведенном выше коде:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Отвечающ `/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Отвечающ `/Products0/Edit/{id}`

Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов. Предыдущий пример ведет себя так же, как и следующий код:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Маршруты на основе атрибутов могут также сочетаться с наследованием. Это мощное сочетание с заменой токенов. Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`создает уникальное имя маршрута для каждого действия:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 создает уникальное имя маршрута для каждого действия.

Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Использование преобразователя параметров для настройки замены токенов

Замену токенов можно настроить, используя преобразователь параметров. Преобразователь параметров реализует <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> и преобразует значения параметров. Например, `SlugifyParameterTransformer` преобразователь настраиваемых параметров изменяет `SubscriptionManagement` значение маршрута на `subscription-management` :

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> является соглашением для модели приложения, которое:

* Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.
* Настраивает значения токена маршрут атрибута при замене.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Предыдущий `ListAll` метод соответствует `/subscription-management/list-all` .

`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Определение служебной версии см. в [веб-документах MDN в служебной системе](https://developer.mozilla.org/docs/Glossary/Slug) .

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Несколько маршрутов атрибутов

Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию. Наиболее распространенный случай использования этой возможности — имитация поведения маршрута по умолчанию на основе соглашения, как показано в следующем примере:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Размещение нескольких атрибутов маршрута на контроллере означает, что каждый из них объединяется с каждым атрибутом маршрута в методах действия:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Все ограничения маршрута [HTTP-команд](#verb) реализуют `IActionConstraint` .

Когда в действие помещаются несколько атрибутов маршрута, реализующих, <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> выполните следующие действия.

* Каждое ограничение действия объединяется с шаблоном маршрута, примененным к контроллеру.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Использование нескольких маршрутов в действиях может показаться полезным и мощным, поэтому лучше обеспечить базовое и четкое определение пространства URL-адресов вашего приложения. Используйте несколько маршрутов **в действиях** , если это необходимо, например, для поддержки существующих клиентов.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов

Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

В приведенном выше коде `[HttpPost("product14/{id:int}")]` применяет ограничение маршрута. `Products14Controller.ShowProduct`Действие сопоставляется только с URL-путями, такими как `/product14/3` . Часть шаблона маршрута `{id:int}` ограничивает этот сегмент только целыми числами.

Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Настраиваемые атрибуты маршрута с помощью Ираутетемплатепровидер

Все [атрибуты маршрута](#rt) реализуют <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> . Среда выполнения ASP.NET Core:

* Ищет атрибуты классов контроллеров и методов действий при запуске приложения.
* Использует атрибуты, реализующие `IRouteTemplateProvider` для создания начального набора маршрутов.

Реализуйте `IRouteTemplateProvider` , чтобы определить пользовательские атрибуты маршрута. Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Предыдущий `Get` метод возвращает значение `Order = 2, Template = api/MyTestApi` .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Использование модели приложения для настройки маршрутов к атрибутам

Модель приложения:

* Объектная модель, создаваемая при запуске.
* Содержит все метаданные, используемые ASP.NET Core для маршрутизации и выполнения действий в приложении.

Модель приложения включает все данные, собранные из атрибутов маршрута. Данные из атрибутов маршрута предоставляются `IRouteTemplateProvider` реализацией. Именован

* Можно написать, чтобы изменить модель приложения, чтобы настроить маршрутизацию.
* Считываются при запуске приложения.

В этом разделе показан простой пример настройки маршрутизации с помощью модели приложения. Следующий код делает маршруты примерно в соответствии со структурой папок проекта.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Следующий код предотвращает `namespace` применение соглашения к контроллерам, которые передаются по атрибуту:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Например, следующий контроллер не использует `NamespaceRoutingConvention` :

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

Метод `NamespaceRoutingConvention.Apply`:

* Не выполняет никаких действий, если контроллер является перенаправляемым атрибутом.
* Задает шаблон контроллеров, основанный на `namespace` , с `namespace` удалением базы.

`NamespaceRoutingConvention`Можно применять в `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Например, рассмотрим следующий контроллер:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

В приведенном выше коде:

* Основанием `namespace` является `My.Application` .
* Полное имя предыдущего контроллера — `My.Application.Admin.Controllers.UsersController` .
* `NamespaceRoutingConvention`Задает шаблон контроллеров `Admin/Controllers/Users/[action]/{id?` .

`NamespaceRoutingConvention`Также можно применить в качестве атрибута в контроллере:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Смешанная маршрутизация с помощью атрибутов и на основе соглашений

ASP.NET Core приложения могут сочетать использование обычной маршрутизации и маршрутизации атрибутов. Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.

Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов. При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов. Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот. **Любой** атрибут маршрута на контроллере делает **все** действия в атрибуте Controller перенаправлены.

Маршрутизация атрибутов и Обычная маршрутизация используют один и тот же механизм маршрутизации.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Создание URL-адресов и значения окружения

Приложения могут использовать функции создания URL-адресов маршрутизации для создания URL-ссылок на действия. Создание URL-адресов устраняет прописано URL-адреса, делая код более надежным и сопровождаемым. В этом разделе рассматриваются функции создания URL-адресов, предоставляемые MVC, и описываются только основные принципы работы формирования URL-адресов. Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).

<xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Интерфейс является базовым элементом инфраструктуры между MVC и маршрутизацией для создания URL-адресов. Экземпляр `IUrlHelper` доступен через `Url` свойство в контроллерах, представлениях и компонентах представления.

В следующем примере `IUrlHelper` интерфейс используется с помощью `Controller.Url` свойства для создания URL-адреса для другого действия.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Если приложение использует стандартный маршрут по умолчанию, значением `url` переменной является строка URL-пути `/UrlGeneration/Destination` . Этот путь URL-адреса создается при маршрутизации путем объединения:

* Значения маршрута из текущего запроса, которые называются **внешними значениями**.
* Значения, передаваемые в `Url.Action` шаблон маршрута и подставляемые этими значениями:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения. Параметр маршрута, который не имеет значения, может:

* Используйте значение по умолчанию, если таковое имеется.
* Пропускается, если он необязателен. Например, `id` из шаблона маршрута `{controller}/{action}/{id?}` .

Создание URL-адреса завершается ошибкой, если ни один из обязательных параметров маршрута не имеет соответствующего значения. Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.

В предыдущем примере `Url.Action` предполагается [Обычная маршрутизация](#cr). Создание URL-адреса работает аналогично [маршрутизации атрибутов](#ar), хотя понятия различны. С обычной маршрутизацией:

* Значения маршрута используются для расширения шаблона.
* Значения маршрута для `controller` и `action` обычно отображаются в этом шаблоне. Это работает потому, что URL-адреса, соответствующие маршрутизации, соответствуют соглашению.

В следующем примере используется маршрутизация атрибутов:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

`Source`Действие в предыдущем коде создает `custom/url/to/destination` .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> был добавлен в ASP.NET Core 3,0 в качестве альтернативы `IUrlHelper` . `LinkGenerator` предлагает аналогичные, но более гибкие функции. Каждый метод в `IUrlHelper` имеет также соответствующее семейство методов `LinkGenerator` .

### <a name="generating-urls-by-action-name"></a>Формирование URL-адресов по имени действия

[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [линкженератор. жетпасбяктион](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)и все связанные перегрузки предназначены для создания целевой конечной точки путем указания имени контроллера и имени действия.

При использовании `Url.Action` значения текущего маршрута для `controller` и `action` предоставляются средой выполнения:

* Значение `controller` и `action` являются частью как [значений окружающих](#ambient) элементов, так и значений. Метод `Url.Action` всегда использует текущие значения `action` и `controller` и создает URL-путь, который направляет в текущее действие.

Маршрутизация пытается использовать значения во внешних значениях для заполнения сведений, которые не были предоставлены при формировании URL-адреса. Рассмотрим маршрут, аналогичный `{a}/{b}/{c}/{d}` значениям окружения `{ a = Alice, b = Bob, c = Carol, d = David }` :

* Маршрутизация содержит достаточно информации для создания URL-адреса без дополнительных значений.
* Маршрутизация содержит достаточно информации, так как все параметры маршрута имеют значение.

Если значение `{ d = Donovan }` Добавлено:

* Значение `{ d = David }` игнорируется.
* Путь к созданному URL-адресу — `Alice/Bob/Carol/Donovan` .

**Предупреждение**: URL-пути являются иерархическими. В предыдущем примере, если `{ c = Cheryl }` добавляется значение:

* Оба значения `{ c = Carol, d = David }` игнорируются.
* Больше нет значения для `d` , и создание URL-адреса завершается неудачей.
* Необходимо указать требуемые значения `c` и `d` для создания URL-адреса.  

Возможно, вы намерены столкнуться с этой проблемой с маршрутом по умолчанию `{controller}/{action}/{id?}` . Эта проблема возникает редко, поскольку `Url.Action` всегда явно указывает `controller` `action` значение и.

Несколько перегрузок [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) принимают объект значений маршрута, чтобы предоставить значения для параметров маршрута, отличных `controller` от `action` и. Объект значений маршрута часто используется с `id` . Например, `Url.Action("Buy", "Products", new { id = 17 })`. Объект значений маршрута:

* По соглашению обычно является объектом анонимного типа.
* Может быть `IDictionary<>` или [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Приведенный выше код создает `/Products/Buy/17?color=red` .

Следующий код создает абсолютный URL-адрес:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Чтобы создать абсолютный URL-адрес, используйте один из следующих способов.

* Перегрузка, принимающая `protocol` . Например, приведенный выше код.
* [Линкженератор. жетурибяктион](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), который по умолчанию создает абсолютные URI.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Создание URL-адресов по маршруту

Приведенный выше код демонстрирует создание URL-адреса путем передачи контроллера и имени действия. `IUrlHelper` также предоставляет семейство методов [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) . Эти методы похожи на [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), но не копируют текущие значения `action` и `controller` в значения маршрута. Наиболее распространенное использование `Url.RouteUrl` :

* Указывает имя маршрута для создания URL-адреса.
* Обычно не указывает имя контроллера или действия.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Следующий Razor файл создает ссылку HTML на `Destination_Route` :

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a>Создание URL-адресов в HTML и Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> предоставляет <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> методы [HTML. Бегинформ](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) и [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) для создания `<form>` элементов и `<a>` соответственно. Эти методы используют метод [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) для создания URL-адреса и принимают аналогичные аргументы. Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.

Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`. Обе они реализуются с помощью интерфейса `IUrlHelper`. Дополнительные сведения см. [в разделе вспомогательные функции тегов в формах](xref:mvc/views/working-with-forms) .

Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Создание URL-адресов в результатах действий

В предыдущих примерах было показано использование `IUrlHelper` в контроллере. Наиболее распространенным использованием контроллера является создание URL-адреса как части результата действия.

Базовые классы <xref:Microsoft.AspNetCore.Mvc.ControllerBase> и <xref:Microsoft.AspNetCore.Mvc.Controller> предоставляют удобные методы для результатов действий, ссылающихся на другое действие. Один из типичных способов использования — перенаправление после приема входных данных пользователя:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Действия, выполняемые фабриками, такие как <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> и, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> следуют аналогичному шаблону для методов в `IUrlHelper` .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Выделенные маршруты на основе соглашений

В [обычной маршрутизации](#cr) может использоваться особый тип определения маршрута, называемый [выделенным обычным маршрутом](#dcr). В следующем примере маршрут с именем `blog` является выделенным обычным маршрутом:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Используя приведенные выше определения маршрутов, `Url.Action("Index", "Home")` создает URL-путь `/` с помощью `default` маршрута, но почему? Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.

[Выделенные традиционные маршруты](#dcr) полагаются на особое поведение значений по умолчанию, которые не имеют соответствующего параметра маршрута, который предотвращает слишком [жадную](xref:fundamentals/routing#greedy) маршрутизацию при формировании URL-адресов. В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет. Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию. Создание URL-адреса с помощью `blog` завершается ошибкой, так как значения `{ controller = Home, action = Index }` не совпадают `{ controller = Blog, action = Article }` . После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Области

[Области](xref:mvc/controllers/areas) — это функция MVC, используемая для упорядочивания связанных функций в группе в виде отдельных:

* Пространство имен маршрутизации для действий контроллера.
* Структура папок для представлений.

Использование областей позволяет приложению иметь несколько контроллеров с одинаковым именем, если они имеют разные области. При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`. В этом разделе обсуждается взаимодействие маршрутизации с областями. Сведения об использовании областей с представлениями см. в разделе [области](xref:mvc/controllers/areas) .

В следующем примере MVC настраивается для использования стандартного маршрута по умолчанию и `area` маршрута для `area` именованного `Blog` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

В приведенном выше коде <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> вызывается для создания `"blog_route"` . Вторым параметром `"Blog"` является имя области.

При сопоставлении URL-пути `/Manage/Users/AddUser` , например, `"blog_route"` маршрут формирует значения маршрута `{ area = Blog, controller = Users, action = AddUser }` . `area`Значение маршрута создается значением по умолчанию для `area` . Маршрут, созданный с помощью, `MapAreaControllerRoute` эквивалентен следующему:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

Метод `MapAreaControllerRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`). Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.

При маршрутизации на основе соглашений учитывается порядок. Как правило, маршруты с областями следует размещать раньше, так как они более специфичны, чем маршруты без области.

Используя предыдущий пример, значения маршрута `{ area = Blog, controller = Users, action = AddUser }` соответствуют следующему действию:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Атрибут [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) указывает, что контроллер является частью области. Этот контроллер находится в `Blog` области. Контроллеры без `[Area]` атрибута не являются членами какой-либо области и не  совпадают, если `area` значение маршрута предоставляется службой маршрутизации. В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Пространство имен каждого контроллера показано здесь для полноты. Если предыдущие контроллеры используют одно и то же пространство имен, создается ошибка компилятора. Имена пространств классов не влияют на маршрутизацию в MVC.

Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`. Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.

<a name="aa"></a>

В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.

При выполнении действия внутри области значение маршрута для `area` будет доступно как [значение окружения](#ambient) для маршрутизации, используемой для формирования URL-адреса. Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Следующий код создает URL-адрес для `/Zebra/Users/AddUser` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Определение действия

Открытые методы на контроллере, за исключением тех, которые имеют атрибут [недействия](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , являются действиями.

## <a name="sample-code"></a>Образец кода

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* [Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([как скачивать](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В ASP.NET Core MVC используется [ПО промежуточного слоя](xref:fundamentals/middleware/index) маршрутизации для сопоставления URL-адресов входящих запросов с действиями. Маршруты определяются в коде запуска или атрибутах. Они описывают то, как пути URL-адресов должны сопоставляться с действиями. С помощью маршрутов также формируются URL-адреса (для ссылок), отправляемые в ответах.

Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов. При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов. Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).

В этом документе описывается взаимодействие между MVC и маршрутизацией и использование возможностей маршрутизации в типичных приложениях MVC. Подробные сведения о расширенной маршрутизации см. в статье [Маршрутизация](xref:fundamentals/routing).

## <a name="setting-up-routing-middleware"></a>Настройка ПО промежуточного слоя маршрутизации

Метод *Configure* содержит код, который выглядит примерно так:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

В вызове `UseMvc` метод `MapRoute` используется для создания одного маршрута, который называется маршрутом `default`. В большинстве приложений MVC используется маршрут с шаблоном, сходным с маршрутом `default`.

Шаблон маршрута `"{controller=Home}/{action=Index}/{id?}"` может сопоставлять путь URL-адреса, например `/Products/Details/5`, и извлекает значения маршрута `{ controller = Products, action = Details, id = 5 }` путем разбивки пути на лексемы. MVC попытается найти контроллер с именем `ProductsController` и выполнить действие `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Обратите внимание на то, что в этом примере привязка модели будет использовать значение `id = 5`, чтобы присвоить параметру `id` значение `5` при вызове действия. Дополнительные сведения см. в разделе [Привязка модели](../models/model-binding.md).

Использование маршрута `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Шаблон маршрута:

* `{controller=Home}` определяет `Home` в качестве объекта `controller` по умолчанию.

* `{action=Index}` определяет `Index` в качестве объекта `action` по умолчанию.

* `{id?}` определяет необязательный параметр `id`.

Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления. Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).

`"{controller=Home}/{action=Index}/{id?}"` может сопоставляться с путем URL-адреса `/` и выдает значения маршрута `{ controller = Home, action = Index }`. Для объектов `controller` и `action` используются значения по умолчанию. `id` не имеет значения, так как в пути URL-адреса нет соответствующего сегмента. MVC будет использовать эти значения маршрута для выбора действия `HomeController` и `Index`:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

При использовании этого определения контроллера и шаблона маршрута действие `HomeController.Index` будет выполняться для любых из следующих путей URL-адресов:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Универсальный метод `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Можно использовать вместо следующего метода:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` и `UseMvcWithDefaultRoute` добавляют экземпляр `RouterMiddleware` в конвейер ПО промежуточного слоя. MVC не взаимодействует с ПО промежуточного слоя напрямую, а использует маршрутизацию для обработки запросов. MVC подключается к маршрутам посредством экземпляра `MvcRouteHandler`. Код метода `UseMvc` имеет примерно следующий вид:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

Метод `UseMvc` не определяет маршруты напрямую, а добавляет заполнитель в коллекцию маршрутов для маршрута `attribute`. Перегрузка `UseMvc(Action<IRouteBuilder>)` позволяет добавлять собственные маршруты, а также поддерживает маршрутизацию с помощью атрибутов.  Метод `UseMvc` и все его варианты добавляют заполнитель для маршрута на основе атрибутов. Маршрутизация с помощью атрибутов доступна всегда вне зависимости от того, как настроен метод `UseMvc`. Метод `UseMvcWithDefaultRoute` определяет маршрут по умолчанию и поддерживает маршрутизацию с помощью атрибутов. В разделе [Маршрутизация с помощью атрибутов](#attribute-routing-ref-label) приводятся более подробные сведения о маршрутизации с помощью атрибутов.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Маршрутизация на основе соглашений

Маршрут `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Приведенный выше код является примером обычной маршрутизации. Этот стиль называется обычной маршрутизацией, так как он устанавливает *соглашение* для URL-путей:

* Первый сегмент пути сопоставляется с именем контроллера.
* Второй сопоставляется с именем действия.
* Третий сегмент используется для необязательного `id` . `id` сопоставляется с сущностью модели.

При использовании этого маршрута `default` путь URL-адреса `/Products/List` сопоставляется с действием `ProductsController.List`, а путь `/Blog/Article/17` сопоставляется с `BlogController.Article`. Такое сопоставление основывается **только** на именах контроллера и действия, но не на пространствах имен, расположениях исходных файлов или параметрах метода.

> [!TIP]
> Использование маршрутизации на основе соглашений с маршрутом по умолчанию позволяет быстро создавать приложение, не придумывая новый шаблон URL-адреса для каждого определяемого действия. В случае с приложением с действиями в стиле CRUD единообразие URL-адресов для всех контроллеров позволяет упростить код и сделать пользовательский интерфейс более предсказуемым.

> [!WARNING]
> Параметр `id` определяется в шаблоне маршрута как необязательный. Это означает, что действия могут выполняться, даже если идентификатор не указан в URL-адресе. Как правило, если параметр `id` отсутствует в URL-адресе, привязка модели присваивает ему значение `0`, и в результате в базе данных не будет найдена сущность, соответствующая `id == 0`. Маршрутизация с помощью атрибутов обеспечивает детальный контроль, позволяя настраивать идентификатор как обязательный лишь для некоторых действий. В документации необязательные параметры, такие как `id`, будут включаться, только если они, скорее всего, могут использоваться в соответствующей ситуации.

## <a name="multiple-routes"></a>Несколько маршрутов

В метод `UseMvc` можно добавить несколько маршрутов, добавив дополнительные вызовы `MapRoute`. Таким образом можно определить несколько соглашений или добавить маршруты на основе соглашений, предназначенные для определенного действия, например:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Маршрут `blog` здесь — это *выделенный маршрут на основе соглашения*. Это означает, что он использует систему маршрутизации на основе соглашений, но предназначен для определенного действия. Так как параметры `controller` и `action` отсутствуют в шаблоне маршрута, они могут иметь только значения по умолчанию, поэтому этот маршрут всегда будет сопоставляться с действием `BlogController.Article`.

Маршруты в коллекции маршрутов упорядочены и обрабатываются в порядке добавления. Поэтому в этом примере маршрут `blog` будет проверяться перед маршрутом `default`.

> [!NOTE]
> *Выделенные традиционные маршруты* часто используют **перекрестные** параметры маршрута `{*article}` , например для записи оставшейся части пути URL-адреса. Из-за этого маршрут может оказаться слишком универсальным, то есть он будет соответствовать URL-адресам, которые должны сопоставляться с другими маршрутами. Чтобы избежать этой проблемы, такие универсальные маршруты следует помещать в конце таблицы маршрутов.

### <a name="fallback"></a>Резервирование

В ходе обработки запроса MVC проверяет, можно ли найти контроллер и действие в приложении с помощью предоставленных значений маршрута. Если нет действия, соответствующего значениям, маршрут считается не сопоставленным, и проверяется следующий маршрут. Этот процесс называется *откатом* и призван упростить обработку случаев, когда маршруты на основе соглашений перекрываются.

### <a name="disambiguating-actions"></a>Разрешение неоднозначности действий

Если при маршрутизации найдены два соответствующих действия, платформа MVC должна устранить неоднозначность, выбрав наиболее подходящее из них, или создать исключение. Например:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Этот контроллер определяет два действия, которые соответствуют пути URL-адреса `/Products/Edit/17` и маршрутизируют данные `{ controller = Products, action = Edit, id = 17 }`. Это типичный шаблон для контроллеров MVC, в котором метод `Edit(int)` отображает форму для изменения сведений о продукте, а метод `Edit(int, Product)` обрабатывает отправленную форму. Чтобы это было возможным, платформа MVC должна выбрать `Edit(int, Product)` для HTTP-запроса `POST` и `Edit(int)` для любой другой HTTP-команды.

`HttpPostAttribute` (`[HttpPost]`) — это реализация интерфейса `IActionConstraint`, которая позволяет выбрать действие, только если HTTP-команда — `POST`. Наличие интерфейса `IActionConstraint` делает метод `Edit(int, Product)` более подходящим вариантом, чем `Edit(int)`, поэтому `Edit(int, Product)` будет проверяться первым.

Пользовательские реализации `IActionConstraint` потребуется создавать только в особых ситуациях, однако важно понимать роль таких атрибутов, как `HttpPostAttribute`, — аналогичные атрибуты определены для других HTTP-команд. При маршрутизации на основе соглашений для действий часто используются одинаковые имена в рамках рабочего процесса `show form -> submit form`. Удобство такого шаблона станет очевидным после ознакомления с разделом [Сведения об интерфейсе IActionConstraint](#understanding-iactionconstraint).

Если найдено несколько совпадений и MVC не может определить наиболее подходящий маршрут, создается исключение `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Имена маршрутов

Строки `"blog"` и `"default"` в следующих примерах представляют собой имена маршрутов:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Имя маршрута — это логическое имя, которое позволяет использовать именованный маршрут для формирования URL-адреса. Имена значительно упрощают создание URL-адресов, если оно представляет сложность из-за порядка маршрутов. Имена маршрутов должны быть уникальными в пределах приложения.

Имена маршрутов не влияют на сопоставление URL-адресов или обработку запросов; они служат только для формирования URL-адресов. В статье [Маршрутизация](xref:fundamentals/routing) приводятся более подробные сведения о формировании URL-адресов, в том числе во вспомогательных объектах MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Маршрутизация с помощью атрибутов

При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов. В приведенном ниже примере в методе `Configure` используется `app.UseMvc();`, и маршрут не передается. `HomeController` будет соответствовать набору URL-адресов, аналогичных тем, которым соответствует маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

Действие `HomeController.Index()` будет выполняться для любого из путей URL-адресов `/`, `/Home` или `/Home/Index`.

> [!NOTE]
> В этом примере показано ключевое различие между маршрутизацией с помощью атрибутов и маршрутизацией на основе соглашений. При использовании маршрутизации с помощью атрибутов для указания маршрута требуется больше входных данных; маршрут по умолчанию на основе соглашения более лаконичен. Однако маршрутизация с помощью атрибутов обеспечивает более точный контроль над тем, какие шаблоны маршрутов применяются к каждому действию, и требует такого контроля.

В случае с маршрутизацией с помощью атрибутов имя контроллера и имена действий **не** имеют значения при выборе действия. В этом примере сопоставление будет производиться с теми же URL-адресами, что и в предыдущем.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Приведенные выше шаблоны маршрутов не определяют параметры маршрутов для `action`, `area` и `controller`. По сути, эти параметры маршрутов не разрешены в маршрутах на основе атрибутов. Так как шаблон маршрута уже связан с действием, не имеет смысла анализировать имя действия в URL-адресе.

## <a name="attribute-routing-with-httpverb-attributes"></a>Маршрутизация с помощью атрибутов Http[Verb]

При маршрутизации с помощью атрибутов также могут использоваться атрибуты `Http[Verb]`, такие как `HttpPostAttribute`. Все эти атрибуты могут принимать шаблон маршрута. В этом примере показаны два действия, которые совпадают с одним шаблоном маршрута:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Для такого пути URL-адреса, как `/products`, действие `ProductsApi.ListProducts` будет выполнено, если HTTP-командой является `GET`, а действие `ProductsApi.CreateProduct` будет выполнено, если HTTP-командой является `POST`. При маршрутизации с помощью атрибутов URL-адрес сначала сопоставляется с набором шаблоном маршрутов, определяемых атрибутами маршрутов. После того как найден соответствующий шаблон маршрута, применяются ограничения `IActionConstraint` для определения действий, которые могут быть выполнены.

> [!TIP]
> При разработке REST API редко приходится использовать `[Route(...)]` для метода действия, так как действие принимает все методы HTTP. Предпочтительнее применять более конкретные атрибуты `Http*Verb*Attributes`, чтобы точно определить поддерживаемые API возможности. Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.

Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута. В этом примере параметр `id` является обязательным в пути URL-адреса.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Действие `ProductsApi.GetProduct(int)` будет выполнено для такого пути URL-адреса, как `/products/3`, но не для такого пути URL-адреса, как `/products`. Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).

## <a name="route-name"></a>Имя маршрута

В следующем коде определяется *имя маршрута*`Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута. Они не влияют на то, как производится сопоставление URL-адресов при маршрутизации, и служат только для формирования URL-адресов. Имена маршрутов должны быть уникальными в пределах приложения.

> [!NOTE]
> Сравните это поведение с *маршрутом по умолчанию* на основе соглашения, в котором параметр `id` определяется как необязательный (`{id?}`). Такая возможность точно указывать интерфейсы API имеет преимущества. Например, она позволяет направлять `/products` и `/products/5` в разные действия.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Объединение маршрутов

Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий. Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий. В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

В этом примере путь URL-адреса `/products` может соответствовать `ProductsApi.ListProducts`, а путь URL-адреса `/products/5` — `ProductsApi.GetProduct(int)`. Оба эти действия соответствуют только HTTP-запросу `GET`, так как они помечены атрибутом `HttpGetAttribute`.

Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру. Этот пример соответствует такому же набору путей URL-адресов, что и *маршрут по умолчанию*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Упорядочение маршрутов на основе атрибутов

В отличие от обычных маршрутов, которые выполняются в определенном порядке, маршрутизация атрибутов создает дерево и сопоставляет все маршруты одновременно. Это равносильно тому, как если бы записи маршрутов находились в идеальном порядке: наиболее конкретные маршруты имеют возможность выполнения перед более общими.

Например, такой маршрут, как `blog/search/{topic}`, является более конкретным по сравнению с `blog/{*article}`. С логической точки зрения, маршрут `blog/search/{topic}` по умолчанию выполняется первым, так как это единственная разумная очередность. При использовании маршрутизации на основе соглашений разработчик отвечает за расположение маршрутов в нужном порядке.

При маршрутизации с помощью атрибутов порядок может настраиваться с помощью свойства `Order` всех атрибутов маршрутов, предоставляемых платформой. Маршруты обрабатываются в порядке возрастания значения свойства `Order`. Порядок по умолчанию — `0`. Маршрут, для которого задано значение `Order = -1`, будет выполняться перед маршрутами, для которых порядок не задан. Маршрут, для которого задано значение `Order = 1`, будет выполняться после маршрутов с порядком по умолчанию.

> [!TIP]
> Старайтесь не использовать свойство `Order`. Если для правильной маршрутизации в пространстве URL-адресов требуются явно заданные значения порядка, скорее всего, это будет вызывать путаницу и в среде клиентов. Как правило, при маршрутизации с помощью атрибутов правильный маршрут выбирается посредством сопоставления URL-адресов. Если порядок по умолчанию для формирования URL-адресов не работает, использовать имя маршрута в качестве переопределения, как правило, проще, чем применять свойство `Order`.

Средства маршрутизации в Razor Pages и контроллере MVC имеют общую реализацию. Сведения о порядке маршрутов в Razor разделах страницы см. в разделе [ Razor Маршрутизация и соглашения о приложениях: порядок маршрутов](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Замена токенов в шаблонах маршрутов ([controller], [action], [area])

Для удобства маршруты к атрибутам поддерживают *замену маркера* путем заключения маркера в квадратные скобки ( `[` , `]` ). Токены `[action]`, `[area]` и `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут. В следующем примере действия могут соответствовать путям URL-адресов, как описано в комментариях:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов. Приведенный выше пример будет работать так же, как следующий код:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Маршруты на основе атрибутов могут также сочетаться с наследованием. Эта возможность особенно эффективна при использовании вместе с заменой токенов.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов. `[Route("[controller]/[action]", Name="[controller]_[action]")]` создает уникальное имя маршрута для каждого действия.

Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Использование преобразователя параметров для настройки замены токенов

Замену токенов можно настроить, используя преобразователь параметров. Преобразователь параметров реализует `IOutboundParameterTransformer` и преобразует значения параметров. Например, пользовательский преобразователь параметра `SlugifyParameterTransformer` изменяет значение маршрута `SubscriptionManagement` на `subscription-management`.

`RouteTokenTransformerConvention` является соглашением для модели приложения, которое:

* Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.
* Настраивает значения токена маршрут атрибута при замене.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
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

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Несколько маршрутов

Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию. Наиболее распространенный случай использования этой возможности — имитация поведения *маршрута по умолчанию на основе соглашения*, как показано в следующем примере:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Добавление нескольких атрибутов маршрута для контроллера означает, что каждый из них будет объединяться с каждым из атрибутов маршрута, определенных для методов действий.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Если несколько атрибутов маршрута (реализующих интерфейс `IActionConstraint`) добавлены для действия, каждое ограничение действия объединяется с шаблоном маршрута из атрибута, в котором оно определено.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Хотя использование нескольких маршрутов к действиям может показаться очень эффективной возможностью, лучше, чтобы пространство URL-адресов приложения оставалось простым и четко организованным. Используйте несколько маршрутов к действиям только там, где это необходимо, например для поддержки существующих клиентов.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов

Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Пользовательские атрибуты маршрута с использованием `IRouteTemplateProvider`

Все атрибуты маршрутов, предоставляемые платформой ( `[Route(...)]`, `[HttpGet(...)]` и т. д.), реализуют интерфейс `IRouteTemplateProvider`. MVC ищет атрибуты в классах контроллеров и методах действий при запуске приложения и использует те из них, которые реализуют интерфейс `IRouteTemplateProvider`, для формирования начального набора маршрутов.

Вы можете реализовать интерфейс `IRouteTemplateProvider` для определения собственных атрибутов маршрутов. Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Атрибут в приведенном выше примере автоматически присваивает шаблону `Template` значение `"api/[controller]"` при применении `[MyApiController]`.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Настройка маршрутов на основе атрибутов с помощью модели приложения

*Модель приложения* — это объектная модель, которая создается при запуске со всеми метаданными, используемыми платформой MVC для маршрутизации и выполнения действий. *Модель приложения* включает в себя все данные, собранные из атрибутов маршрутов (посредством интерфейса `IRouteTemplateProvider`). Вы можете создать *соглашения*, чтобы изменить модель приложения при запуске с целью настройки маршрутизации. В этом разделе приводится простой пример настройки маршрутизации с помощью модели приложения.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Смешанная маршрутизация с помощью атрибутов и на основе соглашений

В приложениях MVC маршрутизация с помощью атрибутов и маршрутизация на основе соглашений могут использоваться вместе. Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.

Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов. При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов. Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот. **Любой** атрибут маршрута контроллера делает все действия в атрибуте контроллера маршрутизируемыми.

> [!NOTE]
> Два типа систем маршрутизации отличает процесс, выполняемый после нахождения URL-адреса, соответствующего шаблону маршрута. При маршрутизации на основе соглашений значения маршрута из соответствия используются для выбора действия и контроллера из таблицы подстановки, содержащей все действия с маршрутизацией на основе соглашений. При маршрутизации с помощью атрибутов каждый шаблон уже связан с действием, и дальнейшая подстановка не требуется.

## <a name="complex-segments"></a>Сложные сегменты

Сложные сегменты (например, `[Route("/dog{token}cat")]`) обрабатываются путем "нежадного" сопоставления литералов справа налево. Описание см. в [исходном коде](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296). Дополнительные сведения см. в [этой статье](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Формирование URL-адреса

Приложения MVC могут использовать функции формирования URL-адреса, предоставляемые системой маршрутизации, для создания URL-ссылок на действия. Формирование URL-адресов устраняет необходимость в их жестком задании, что делает код надежнее и проще в обслуживании. В этом разделе рассматриваются функции формирования URL-адреса, предоставляемые платформой MVC, и описываются лишь базовые принципы их работы. Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).

Интерфейс `IUrlHelper` — это базовый компонент инфраструктуры, обеспечивающий взаимодействие между MVC и системой маршрутизации для формирования URL-адресов. Доступ к экземпляру `IUrlHelper` в контроллерах, представлениях и компонентах представлений можно получить посредством свойства `Url`.

В этом примере интерфейс `IUrlHelper` используется посредством свойства `Controller.Url` для формирования URL-адреса другого действия.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Если в приложении применяется маршрут по умолчанию на основе соглашения, значением переменной `url` будет строка с путем URL-адреса `/UrlGeneration/Destination`. Этот путь URL-адреса создается системой маршрутизации путем объединения значений маршрута из текущего запроса (значения окружения) со значениями, переданными в `Url.Action`, и подстановки этих значений в шаблон маршрута.

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения. Параметр маршрута, у которого нет значения, может принимать значение по умолчанию, если оно имеется, или пропускаться, если он является необязательным (как в случае с параметром `id` в этом примере). Сформировать URL-адрес не удастся, если у любого из обязательных параметров маршрута не будет соответствующего значения. Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.

В примере `Url.Action` выше предполагается использование маршрутизации на основе соглашений, однако формирование URL-адреса происходит аналогичным образом и в случае с маршрутизацией с помощью атрибутов, хотя принципы иные. При применении маршрутизации на основе соглашений шаблон расширяется с помощью значений маршрута, и значения маршрута для `controller` и `action` обычно находятся в этом шаблоне. Это возможно по той причине, что URL-адреса, сопоставленные системой маршрутизации, следуют *соглашению*. При маршрутизации с помощью атрибутов значения маршрута для `controller` и `action` не могут находиться в шаблоне. Вместо этого они применяются для поиска нужного шаблона.

В этом примере используется маршрутизация с помощью атрибутов:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC создает таблицу подстановки, содержащую все действия с маршрутизацией с помощью атрибутов, и сопоставляет значения `controller` и `action` для выбора шаблона маршрута, с помощью которого должен формироваться URL-адрес. В приведенном выше примере создается URL-адрес `custom/url/to/destination`.

### <a name="generating-urls-by-action-name"></a>Формирование URL-адресов по имени действия

`Url.Action` (`IUrlHelper` . `Action`) и все связанные перегрузки предполагают необходимость указания целевого объекта путем задания имени контроллера и имени действия.

> [!NOTE]
> При использовании метода `Url.Action` текущие значения маршрута для `controller` и `action` уже определены: значения `controller` и `action` включены как в *значения окружения, так и в* **обычные** *значения*. Метод `Url.Action` всегда использует текущие значения `action` и `controller` и формирует путь URL-адреса, ведущий к текущему действию.

При формировании URL-адреса система маршрутизации пытается использовать значения окружения для заполнения сведений, которые вы не предоставили. При использовании такого маршрута, как `{a}/{b}/{c}/{d}`, и значений окружения `{ a = Alice, b = Bob, c = Carol, d = David }` система маршрутизации имеет достаточно информации для формирования URL-адреса без дополнительных значений, так как все параметры маршрута имеют значения. Если добавить значение `{ d = Donovan }`, значение `{ d = David }` не будет учитываться, и будет сформирован путь URL-адреса `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Пути URL-адресов являются иерархическими. Если в приведенном выше примере добавить значение `{ c = Cheryl }`, пропущены будут оба значения `{ c = Carol, d = David }`. В этом случае параметр `d` больше не имеет значения и сформировать URL-адрес не удастся. Потребуется указать нужные значения для параметров `c` и `d`.  Эта проблема может возникнуть с маршрутом по умолчанию (`{controller}/{action}/{id?}`), но на практике она встречается редко, так как `Url.Action` всегда явным образом задает значения `controller` и `action`.

Более длинные перегрузки `Url.Action` также принимают дополнительный объект *значений маршрута* для предоставления значений параметров маршрута, отличных от `controller` и `action`. Чаще всего он применяется с параметром `id`, например `Url.Action("Buy", "Products", new { id = 17 })`. В соответствии с соглашением объект *значений маршрута* обычно имеет анонимный тип, но это может быть также экземпляр `IDictionary<>` или *обычный объект .NET*. Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Чтобы создать абсолютный URL-адрес, используйте перегрузку, принимающую `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Формирование URL-адресов по маршруту

В приведенном выше коде демонстрировалось формирование URL-адреса путем передачи имен контроллера и действия. Интерфейс `IUrlHelper` также предоставляет семейство методов `Url.RouteUrl`. Эти методы похожи на `Url.Action`, но они не копируют текущие значения `action` и `controller` в значения маршрута. Наиболее распространенный способ их применения — указание имени определенного маршрута, который должен использоваться для формирования URL-адреса, как правило, *без* указания имени контроллера или действия.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Формирование URL-адресов в HTML

Интерфейс `IHtmlHelper` предоставляет методы `HtmlHelper``Html.BeginForm` и `Html.ActionLink` для создания элементов `<form>` и `<a>` соответственно. Эти методы используют метод `Url.Action` для формирования URL-адреса и принимают одинаковые аргументы. Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.

Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`. Обе они реализуются с помощью интерфейса `IUrlHelper`. Дополнительные сведения см. в статье [Работа с формами](../views/working-with-forms.md).

Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Формирование URL-адресов в результатах действий

В предыдущих примерах было продемонстрировано использование интерфейса `IUrlHelper` в контроллере, хотя чаще всего URL-адреса формируются в контроллерах в рамках результата действия.

Базовые классы `ControllerBase` и `Controller` предоставляют удобные методы для результатов действий, ссылающихся на другое действие. Одним из типичных способов их применения является перенаправление после принятия входных данных пользователя.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Фабричные методы результатов действий по своей структуре похожи на методы интерфейса `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Выделенные маршруты на основе соглашений

При маршрутизации на основе соглашений может использоваться особый тип определения маршрута — *выделенный маршрут на основе соглашения*. В приведенном ниже примере маршрут с именем `blog` является выделенным маршрутом на основе соглашения.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

При использовании таких определений маршрутов метод `Url.Action("Index", "Home")` создаст путь URL-адреса `/` с маршрутом `default`. В чем же причина? Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.

В случае с выделенными маршрутами на основе соглашений используется особое поведение значений по умолчанию, для которых нет соответствующих параметров маршрута. Благодаря ему маршрут не может быть слишком универсальным при формировании URL-адреса. В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет. Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию. Сформировать URL-адрес с помощью `blog` не удастся, так как значения `{ controller = Home, action = Index }` не соответствуют `{ controller = Blog, action = Article }`. После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Области

[Области](areas.md) — это возможность MVC, которая служит для объединения связанных функций в группу в виде отдельного пространства имен маршрутизации (для действий контроллеров) и структуры папок (для представлений). Благодаря областям приложение может иметь несколько контроллеров с одинаковыми именами, если у них будут разные *области*. При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`. В этом разделе рассматривается взаимодействие системы маршрутизации с областями. Подробные сведения об использовании областей с представлениями см. в статье [Области](areas.md).

В следующем примере в MVC настраивается использование маршрута на основе соглашения по умолчанию и *маршрута области* для области с именем `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Результатом сопоставления первого маршрута с таким путем URL-адреса, как `/Manage/Users/AddUser`, будут значения маршрута `{ area = Blog, controller = Users, action = AddUser }`. Значение маршрута `area` получается на основе значения по умолчанию для параметра `area`. По сути, маршрут, создаваемый методом `MapAreaRoute`, эквивалентен следующему:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

Метод `MapAreaRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`). Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.

> [!TIP]
> При маршрутизации на основе соглашений учитывается порядок. Как правило, маршруты с областями должны находиться раньше в таблице маршрутов, так как они более точные, чем маршруты без областей.

В предыдущем примере значения маршрута будут соответствовать следующему действию:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` обозначает контроллер в рамках области. В таком случае говорят, что контроллер находится в области `Blog`. Контроллеры без атрибута `[Area]` не входят ни в одну область и **не** будут соответствовать маршруту, если система маршрутизации предоставляет значение маршрута `area`. В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Пространство имен каждого контроллера приведено здесь для полноты. В противном случае между контроллерами возник бы конфликт именования, и произошла бы ошибка компилятора. Имена пространств классов не влияют на маршрутизацию в MVC.

Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`. Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.

> [!NOTE]
> В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.

При выполнении действия внутри области значение маршрута для `area` будет доступно как *значение окружения*, которое система маршрутизации использует для формирования URL-адреса. Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Основные сведения об интерфейсе IActionConstraint

> [!NOTE]
> В этом разделе описываются внутренние принципы работы платформы и то, как MVC выбирает действие, которое необходимо выполнить. Типичному приложению пользовательская реализация `IActionConstraint` не требуется.

Скорее всего, вы уже пользовались интерфейсом `IActionConstraint`, даже если не знакомы с ним. Атрибут `[HttpGet]` и сходные атрибуты `[Http-VERB]` реализуют интерфейс `IActionConstraint`, чтобы ограничить выполнение метода действия.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

В случае с маршрутом по умолчанию на основе соглашения путь URL-адреса `/Products/Edit` даст значения `{ controller = Products, action = Edit }`, которые будут соответствовать **обоим** приведенным здесь действиям. В терминологии `IActionConstraint` можно сказать, что оба действия считаются кандидатами, так как они оба соответствуют данным маршрута.

Когда метод `HttpGetAttribute` выполняется, он сообщает, что *Edit()* является соответствием для запроса *GET*, но не является соответствием для каких-либо иных HTTP-команд. Для действия `Edit(...)` ограничения не определены, поэтому оно соответствует любой HTTP-команде. Поэтому если рассматривать запрос `POST`, соответствием будет только `Edit(...)`. Однако в случае с запросом `GET` соответствовать могут оба действия, хотя действие с `IActionConstraint` всегда считается *имеющим приоритет*. Таким образом, поскольку действие `Edit()` имеет атрибут `[HttpGet]`, оно считается более конкретным и будет выбрано в случае соответствия обоих действий.

По сути, интерфейс `IActionConstraint` представляет собой своего рода *перегрузку*, но вместо перегрузки методов с одинаковыми именами он перегружает действия, соответствующие одному и тому же URL-адресу. При маршрутизации с помощью атрибутов также применяется интерфейс `IActionConstraint`, в результате чего кандидатами могут считаться действия из разных контроллеров.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Реализация IActionConstraint

Самый простой способ реализовать интерфейс `IActionConstraint` — создать класс, производный от `System.Attribute`, и добавить его в действия и контроллеры. MVC автоматически обнаруживает экземпляры `IActionConstraint`, применяемые как атрибуты. Вы можете использовать модель приложения для применения ограничений, и это, пожалуй, самый гибкий подход, так как он позволяет метапрограммировать способ их применения.

В следующем примере ограничение выбирает действие на основе *кода страны* из данных маршрута. [Полный пример можно найти в GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Вы отвечаете за реализацию метода `Accept` и определение очередности применения ограничений. В этом случае метод `Accept` возвращает значение `true`, означающее, что действие является соответствующим при соответствии значения маршрута `country`. Таким образом, он позволяет возвращаться к действию без атрибута, чем отличается от метода `RouteValueAttribute`. В примере показано, что если определено действие `en-US`, то для кода страны `fr-FR` будет выбран более общий контроллер, к которому не применяется ограничение `[CountrySpecific(...)]`.

Свойство `Order` определяет, к какому *этапу* относится ограничение. Ограничения действий применяются группами в соответствии со значением `Order`. Например, все предоставляемые платформой атрибуты HTTP-методов имеют одинаковое значение `Order`, благодаря чему они выполняются на одном этапе. Чтобы реализовать требуемые политики, можно использовать любое количество этапов.

> [!TIP]
> Чтобы выбрать значение `Order`, подумайте, должно ли ограничение применяться перед выполнением HTTP-методов. Чем меньше значение, тем раньше применяется ограничение.

::: moniker-end
