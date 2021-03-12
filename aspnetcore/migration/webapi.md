---
title: Переход с веб-API ASP.NET на ASP.NET Core
author: ardalis
description: Узнайте, как перенести реализацию веб-API с веб-API ASP.NET 4. x на ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 3bd34f677271ddfc00e6e65ddf3a5e3c10eec863
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588787"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Переход с веб-API ASP.NET на ASP.NET Core

[Скотта Эдди (](https://twitter.com/scott_addie) и [Виктор Смит](https://ardalis.com/)

Веб-API ASP.NET 4. x — это служба HTTP, которая достигает широкого спектра клиентов, включая браузеры и мобильные устройства. ASP.NET Core объединяет модели приложений MVC и веб-API ASP.NET 4. x в единую модель программирования, известную как ASP.NET Core MVC. В этой статье описываются шаги, необходимые для перехода с веб-API ASP.NET 4. x на ASP.NET Core MVC.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/webapi/sample) ([как скачивать](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Обзор проекта веб-API ASP.NET 4. x

В этой статье используется проект *продуктсапп* , созданный в [Начало работы с веб-API ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). В этом проекте базовый проект веб-API ASP.NET 4. x настраивается следующим образом.

В *Global.asax.CS* выполняется вызов `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`Класс находится в папке *App_Start* и имеет статический `Register` метод:

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

Предыдущий класс:

* Настраивает [маршрутизацию атрибутов](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), хотя в действительности она не используется.
* Настраивает таблицу маршрутизации.
В примере кода требуется, чтобы URL-адреса совпадали с форматом `/api/{controller}/{id}` , а — `{id}` необязательным.

В следующих разделах демонстрируется перенос проекта веб-API в ASP.NET Core MVC.

## <a name="create-the-destination-project"></a>Создание целевого проекта

Создайте новое пустое решение в Visual Studio и добавьте проект веб-API ASP.NET 4. x для миграции:

1. В меню **Файл** выберите пункт **Создать** > **Проект**.
1. Выберите шаблон **пустое решение** и нажмите кнопку **Далее**.
1. Назовите решение *вебапимигратион*. Нажмите кнопку **создания**.
1. Добавьте существующий проект *продуктсапп* в решение.

Добавьте новый проект API для переноса в:

1. Добавьте в решение новый проект **веб-приложения ASP.NET Core** .
1. В диалоговом окне **Настройка нового проекта** назовите проект *Продуктскоре* и выберите **создать**.
1. В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**. Выберите шаблон проекта **API** и нажмите кнопку **Создать**.
1. Удалите файлы примеров *WeatherForecast.CS* и *Controllers/веасерфорекастконтроллер. CS* из нового проекта *продуктскоре* .

Решение теперь содержит два проекта. В следующих разделах описывается перенос содержимого проекта *продуктсапп* в проект *продуктскоре* .

## <a name="migrate-configuration"></a>Миграция конфигурации

ASP.NET Core не использует папку *App_Start* или файл *Global. asax* . Кроме того, файл *web.config* добавляется во время публикации.

Класс `Startup`:

* Заменяет *Global. asax*.
* Обрабатывает все задачи запуска приложения.

Для получения дополнительной информации см. <xref:fundamentals/startup>.

## <a name="migrate-models-and-controllers"></a>Перенос моделей и контроллеров

В следующем коде показано, что `ProductsController` нужно обновить для ASP.NET Core:

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

Обновите `ProductsController` для ASP.NET Core:

1. Скопируйте *Controllers/продуктсконтроллер. CS* и папку *Models* из исходного проекта в новый.
1. Измените корневое пространство имен скопированных файлов на `ProductsCore` .
1. Обновите `using ProductsApp.Models;` инструкцию до `using ProductsCore.Models;` .

Следующие компоненты не существуют в ASP.NET Core:

* Класс `ApiController`
* Пространство имен `System.Web.Http`
* Интерфейс `IHttpActionResult`

Внесите следующие изменения:

1. Измените `ApiController` на <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Добавьте, `using Microsoft.AspNetCore.Mvc;` чтобы разрешить `ControllerBase` ссылку.
1. Удалите `using System.Web.Http;`.
1. Измените `GetProduct` тип возвращаемого значения действия с `IHttpActionResult` на `ActionResult<Product>` .
1. Упростите `GetProduct` `return` инструкцию действия следующим образом:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Настройка маршрутизации

Шаблон проекта ASP.NET Core *API* содержит конфигурацию маршрутизации конечных точек в созданном коде.

Следующие <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> вызовы и <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> вызывают:

* Регистрация сопоставления маршрута и выполнения конечной точки в конвейере по [промежуточного слоя](xref:fundamentals/middleware/index) .
* Замените файл *App_Start/вебапиконфиг.КС* проекта *продуктсапп* .

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

Настройте маршрутизацию следующим образом.

1. Пометьте `ProductsController` класс следующими атрибутами:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Предыдущий [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) атрибут настраивает шаблон маршрутизации атрибутов контроллера. [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Атрибут делает маршрутизацию атрибутов требованием для всех действий в этом контроллере.

    Маршрутизация атрибутов поддерживает маркеры, такие как `[controller]` и `[action]` . Во время выполнения каждый токен заменяется именем контроллера или действия соответственно, к которому был применен атрибут. Токены:
    * Сократите число волшебных строк в проекте.
    * Убедитесь, что маршруты синхронизированы с соответствующими контроллерами и действиями при применении рефакторинга автоматического переименования.
1. Включить HTTP-запросы GET к `ProductController` действиям:
    * Примените [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) атрибут к `GetAllProducts` действию.
    * Примените `[HttpGet("{id}")]` атрибут к `GetProduct` действию.

Запустите перенесенный проект и перейдите к `/api/products` . Появится полный список из трех продуктов. Перейдите по адресу `/api/products/1`. Появится первый продукт.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Обзор проекта веб-API ASP.NET 4. x

В этой статье используется проект *продуктсапп* , созданный в [Начало работы с веб-API ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). В этом проекте базовый проект веб-API ASP.NET 4. x настраивается следующим образом.

В *Global.asax.CS* выполняется вызов `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`Класс находится в папке *App_Start* и имеет статический `Register` метод:

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

Этот класс настраивает [маршрутизацию атрибутов](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), хотя на самом деле она не используется в проекте. Он также настраивает таблицу маршрутизации, используемую веб-API ASP.NET. В этом случае ASP.NET 4. x Web API ждет, что URL-адреса будут соответствовать формату `/api/{controller}/{id}` , с `{id}` необязательным.

В следующих разделах демонстрируется перенос проекта веб-API в ASP.NET Core MVC.

## <a name="create-the-destination-project"></a>Создание целевого проекта

В Visual Studio выполните следующие действия.

* Последовательно выберите **файл**  >  **создать**  >  **проект**  >  **другие типы проектов**  >  **решения Visual Studio**. Выберите **пустое решение** и назовите решение *вебапимигратион*. Нажмите кнопку **ОК** .
* Добавьте существующий проект *продуктсапп* в решение.
* Добавьте в решение новый проект **веб-приложения ASP.NET Core** . Выберите целевую платформу **.NET Core** в раскрывающемся списке и выберите шаблон проекта **API** . Назовите проект *продуктскоре* и нажмите кнопку **ОК** .

Решение теперь содержит два проекта. В следующих разделах описывается перенос содержимого проекта *продуктсапп* в проект *продуктскоре* .

## <a name="migrate-configuration"></a>Миграция конфигурации

ASP.NET Core не использует:

* *App_Startная* папка или файл *Global. asax*
* *web.config* файл добавляется во время публикации.

Класс `Startup`:

* Заменяет *Global. asax*.
* Обрабатывает все задачи запуска приложения.

Для получения дополнительной информации см. <xref:fundamentals/startup>.

В ASP.NET Core MVC маршрутизация атрибутов включается по умолчанию при <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> вызове в `Startup.Configure` . Следующий `UseMvc` вызов заменяет файл *App_Start/Вебапиконфиг.КС* проекта *продуктсапп* :

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a>Перенос моделей и контроллеров

В следующем коде показано `ProductsController` Обновление для ASP.NET Core. [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]

Обновите `ProductsController` для ASP.NET Core:

1. Скопируйте *Controllers/продуктсконтроллер. CS* из исходного проекта в новый.
1. Скопируйте папку *Models* из исходного проекта в новую.
1. Измените корневое пространство имен скопированных файлов на `ProductsCore` .
1. Обновите `using ProductsApp.Models;` инструкцию до `using ProductsCore.Models;` .

Следующие компоненты не существуют в ASP.NET Core:

* Класс `ApiController`
* Пространство имен `System.Web.Http`
* Интерфейс `IHttpActionResult`

Внесите следующие изменения:

1. Измените `ApiController` на <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Добавьте, `using Microsoft.AspNetCore.Mvc;` чтобы разрешить `ControllerBase` ссылку.
1. Удалите `using System.Web.Http;`.
1. Измените `GetProduct` тип возвращаемого значения действия с `IHttpActionResult` на `ActionResult<Product>` .
1. Упростите `GetProduct` `return` инструкцию действия следующим образом:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Настройка маршрутизации

Настройте маршрутизацию следующим образом.

1. Пометьте `ProductsController` класс следующими атрибутами:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Предыдущий [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) атрибут настраивает шаблон маршрутизации атрибутов контроллера. [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Атрибут делает маршрутизацию атрибутов требованием для всех действий в этом контроллере.

    Маршрутизация атрибутов поддерживает маркеры, такие как `[controller]` и `[action]` . Во время выполнения каждый токен заменяется именем контроллера или действия соответственно, к которому был применен атрибут. Токены уменьшают количество волшебных строк в проекте. Кроме того, токены обеспечивают синхронизацию маршрутов с соответствующими контроллерами и действиями при применении рефакторинга автоматического переименования.
1. Задайте для режима совместимости проекта значение ASP.NET Core 2,2:

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Предыдущее изменение:

    * Требуется для использования `[ApiController]` атрибута на уровне контроллера.
    * Может привести к возможным нарушениям поведений, появившимся в ASP.NET Core 2,2.
1. Включить HTTP-запросы GET к `ProductController` действиям:
    * Примените [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) атрибут к `GetAllProducts` действию.
    * Примените `[HttpGet("{id}")]` атрибут к `GetProduct` действию.

Запустите перенесенный проект и перейдите к `/api/products` . Появится полный список из трех продуктов. Перейдите по адресу `/api/products/1`. Появится первый продукт.

## <a name="compatibility-shim"></a>Оболочка совместимости

Библиотека [Microsoft. AspNetCore. MVC. вебапикомпатшим](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) предоставляет оболочку совместимости для перемещения проектов веб-API ASP.NET 4. x в ASP.NET Core. Оболочка совместимости расширяет ASP.NET Core для поддержки ряда соглашений от ASP.NET 4. x Web API 2. Пример, переданный ранее в этом документе, достаточно прост в том, что оболочка совместимости не была нужна. Для более крупных проектов использование оболочки совместимости может оказаться полезным для временного разделения зазора API между ASP.NET Core и ASP.NET 4. x Web API 2.

Оболочка совместимости веб-API предназначена для использования в качестве временной меры для поддержки переноса больших проектов веб-API ASP.NET 4. x в ASP.NET Core. Со временем проекты должны быть обновлены для использования шаблонов ASP.NET Core, а не полагаться на оболочку совместимости.

Функции обеспечения совместимости, включенные в `Microsoft.AspNetCore.Mvc.WebApiCompatShim` :

* Добавляет `ApiController` тип, поэтому не требуется обновлять базовые типы контроллеров.
* Включает привязку модели в стиле веб-API. ASP.NET Core функция привязки модели MVC аналогична ASP.NET 4. x MVC 5 по умолчанию. Оболочка совместимости изменяет привязку модели так, чтобы она была похожа на соглашения о привязке модели Web API 2 ASP.NET 4. x. Например, сложные типы автоматически привязываются из текста запроса.
* Расширяет привязку модели, чтобы действия контроллера могли принимать параметры типа `HttpRequestMessage` .
* Добавляет модули форматирования сообщений, позволяя действиям возвращать результаты типа `HttpResponseMessage` .
* Добавляет дополнительные методы ответа, которые могут использоваться действиями веб-API 2 для обслуживания ответов:
  * `HttpResponseMessage` Каждый
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Методы результата действия:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Добавляет экземпляр `IContentNegotiator` в контейнер внедрения зависимостей (DI) приложения и делает доступными типы, связанные с согласованием содержимого, из [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Примерами таких типов являются `DefaultContentNegotiator` и `MediaTypeFormatter` .

Чтобы использовать оболочку совместимости, выполните следующие действия.

1. Установите пакет NuGet [Microsoft. AspNetCore. MVC. вебапикомпатшим](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Зарегистрируйте службы оболочки совместимости с контейнером внедрения приложения, вызвав `services.AddMvc().AddWebApiConventions()` в `Startup.ConfigureServices` .
1. Определите зависящие от веб-API маршруты с помощью в в `MapWebApiRoute` `IRouteBuilder` `IApplicationBuilder.UseMvc` вызове приложения.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
