---
title: Вспомогательная функция тега частичного представления в ASP.NET Core
author: scottaddie
description: Сведения о вспомогательной функции тега частичного представления в ASP.NET и роли каждого из его атрибутов в отрисовке частичного представления.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 184901ad0bb6188ed908d41dabf2433c5ca7c1ce
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587168"
---
# <a name="partial-tag-helper-in-aspnet-core"></a>Вспомогательная функция тега частичного представления в ASP.NET Core

Автор: [Скотт Адди](https://github.com/scottaddie) (Scott Addie)

Общие сведения о вспомогательных функциях тегов см. здесь: <xref:mvc/views/tag-helpers/intro>.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="overview"></a>Общие сведения

Вспомогательная функция тега partial используется для отрисовки [частичного представления](xref:mvc/views/partial) на Razor страницах и приложениях MVC. Примечания.

* Требуется ASP.NET Core 2.1 или более поздней версии.
* Это альтернатива [синтаксиса вспомогательной функции HTML](xref:mvc/views/partial#reference-a-partial-view).
* Отрисовка частичного представления выполняется асинхронно.

Параметры вспомогательной функции HTML для отрисовки частичного представления:

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

В примерах в этом документе используется модель *Product*:

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

Далее следуют атрибуты вспомогательной функции тега частичного представления.

## <a name="name"></a>name

Атрибут `name` является обязательным. Она указывает имя или путь для отображения частичного представления, которое будет отрисовано. Если указано имя частичного представления, запускается процесс [обнаружения представления](xref:mvc/views/overview#view-discovery). Этот процесс пропускается, если предоставлен явный путь. Все допустимые значения `name` см. в разделе [Обнаружение частичного представления](xref:mvc/views/partial#partial-view-discovery).

В приведенной ниже разметке используется явный путь, указывающий, что файл *_ProductPartial.cshtml* необходимо загрузить из папки *Shared*. При использовании атрибута [for](#for) модель передается в частичное представление для привязки.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a>для

Атрибут `for` назначает [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) для сравнения с текущей моделью. Класс `ModelExpression` выводит синтаксис `@Model.`. Например, можно использовать `for="Product"` вместо `for="@Model.Product"`. Это поведение вывода по умолчанию переопределяется с помощью символа `@` для определения встроенного выражения.

Приведенная ниже разметка загружает *_ProductPartial.cshtml*:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

Частичное представление привязывается к свойству `Product` соответствующей модели страницы:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a>model

Атрибут `model` присваивает экземпляр модели для передачи в частичное представление. Атрибут `model` нельзя использовать с атрибутом [for](#for).

В следующей разметке создается экземпляр нового объекта `Product`, который передается атрибуту `model` для привязки:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a>view-data

Атрибут `view-data` присваивает [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) для передачи в частичное представление. В следующей разметке вся коллекция ViewData становится доступной для частичного представления:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

В приведенном выше коде для значения ключа `IsNumberReadOnly` устанавливается `true`, и значение добавляется в коллекцию ViewData. Следовательно, `ViewData["IsNumberReadOnly"]` становится доступно в следующем частичном представлении:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

В этом примере значение `ViewData["IsNumberReadOnly"]` определяет, будет ли поле *Number* отображаться только для просмотра.

## <a name="migrate-from-an-html-helper"></a>Перенос из вспомогательного метода HTML

Рассмотрим следующий пример асинхронного вспомогательного метода HTML. Выполняется итерация и отображение коллекции продуктов. Для первого параметра метода `PartialAsync` загружается частичное представление *_ProductPartial.cshtml*. Экземпляр модели `Product` передается в частичное представление для привязки.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

Следующая вспомогательная функция тега частичного представления обеспечивает такую же асинхронную подготовку к просмотру, что и вспомогательный метод HTML `PartialAsync`. Атрибуту `model` назначен экземпляр модели `Product` для привязки к частичному представлению.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
