---
title: Часть 7. Добавление поиска в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 7 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: dbd970dcf8877d1bb7dc99ba5d31bd311f8a7663
ms.sourcegitcommit: 2fea9bfe6127bbbdbb438406c82529b2bc331944
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065549"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a>Часть 7. Добавление поиска в приложение MVC ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этом разделе вы добавите в метод действия `Index` возможности поиска, которые позволяют выполнять поиск фильмов по *жанру* или *названию*.

Обновите метод `Index`, находящийся в файле *Controllers/ MoviesController.cs*, добавив следующий код:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

В первой строке метода действия `Index` создается запрос [LINQ](/dotnet/standard/using-linq) для выбора фильмов:

```csharp
var movies = from m in _context.Movie
             select m;
```

Этот запрос *только* определяется в этой точке и **не** выполняется для базы данных.

Если параметр `searchString` содержит строку, запрос фильмов изменяется для фильтрации по значению в строке поиска:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

Приведенный выше код `s => s.Title.Contains()` представляет собой [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Лямбда-выражения используются в запросах [LINQ](/dotnet/standard/using-linq) на основе методов в качестве аргументов стандартных методов операторов запроса, таких как метод [Where](/dotnet/api/system.linq.enumerable.where) или `Contains` (используется в приведенном выше коде). Запросы LINQ не выполняются, если они определяются или изменяются путем вызова метода, например `Where`, `Contains` или `OrderBy`. Вместо этого выполнение запроса откладывается.  Это означает, что вычисление выражения откладывается до тех пор, пока не будет выполнена итерация его реализованного значения или пока не будет вызван метод `ToListAsync`. Дополнительные сведения об отложенном и немедленном выполнении запросов см. в разделе [Выполнение запроса](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Примечание. Метод [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) выполняется в базе данных, а не в коде C#, приведенном выше. Регистр символов запроса учитывается в зависимости от параметров базы данных и сортировки. В SQL Server метод[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) сопоставляется с [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), в котором регистр символов не учитывается. В SQLite при параметрах сортировки по умолчанию регистр символов учитывается.

Перейдите к `/Movies/Index`. Добавьте в URL-адрес строку запроса, например `?searchString=Ghost`. Отображаются отфильтрованные фильмы.

![Представление Index](~/tutorials/first-mvc-app/search/_static/ghost.png)

Если вы изменили сигнатуру метода `Index` и включили в нее параметр с именем `id`, параметр `id` будет соответствовать необязательному заполнителю `{id}` для маршрутов по умолчанию, который задан в файле *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?highlight=5&name=snippet_route)]

Измените параметр на `id`, а все вхождения `searchString` — на `id`.

Предыдущий метод `Index`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Обновленный метод `Index` с параметром `id`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Теперь можно передать заголовок поиска в качестве данных маршрута (сегмент URL-адреса) вместо значения строки запроса.

![Представление Index, в URL-адрес которого добавлено слово ghost, возвращает два фильма: Ghostbusters и Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Тем не менее пользователи вряд ли будут каждый раз изменять URL-адрес для поиска фильмов. Итак, теперь вам необходимо добавить элементы пользовательского интерфейса для удобства фильтрации фильмов. Если вы изменили сигнатуру метода `Index` для тестирования передачи параметра `ID` с привязкой к маршруту, измените ее снова, чтобы она снова принимала параметр `searchString`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Откройте файл *Views/Movies/Index.cshtml* и добавьте разметку `<form>`, которая выделена ниже:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

Тег HTML `<form>` использует [вспомогательную функцию тега Form](xref:mvc/views/working-with-forms), чтобы при отправке формы строка фильтра передавалась в действие `Index` контроллера movies. Сохраните изменения и протестируйте фильтр.

![Представление Index со словом ghost в текстовом поле фильтра по названию](~/tutorials/first-mvc-app/search/_static/filter.png)

Вопреки ожиданиям, перегрузка `[HttpPost]` для метода `Index` отсутствует. Она не нужна, поскольку метод не изменяет состояние приложения и просто выполняет фильтрацию данных.

Можно добавить следующий метод `[HttpPost] Index`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

Параметр `notUsed` используется для создания перегрузки метода `Index`. Это мы обсудим далее в этом учебнике.

При добавлении этого метода вызывающий метод действия будет сопоставлять метод `[HttpPost] Index`, а метод `[HttpPost] Index` будет выполняться, как показано на рисунке ниже.

![Окно браузера с ответом приложения From HttpPost Index: фильтр по слову ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

Тем не менее при добавлении этой версии `[HttpPost]` метода `Index` существует ограничение на общую реализацию. Допустим, вам необходимо добавить в закладки конкретный поиск или отправить друзьям ссылку, по которой они могут просмотреть аналогичный отфильтрованный список фильмов. Обратите внимание, что URL-адрес запроса HTTP POST совпадает с URL-адресом запроса GET (localhost:{ПОРТ}/Movies/Index) — в URL-адресе отсутствуют сведения о поиске. Данные строки поиска отправляются на сервер в виде [значения поля формы](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Вы можете проверить это с помощью средств разработчика для браузера или [инструмента Fiddler](https://www.telerik.com/fiddler). На рисунке ниже показаны средства разработчика для браузера Chrome:

![Вкладка "Сеть" средств разработчика в Microsoft Edge с телом запроса со значением searchString, равным ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

В теле запроса отображается параметр поиска и маркер [XSRF](xref:security/anti-request-forgery). Обратите внимание, что, как описывается в предыдущем руководстве, [вспомогательная функция тега Form](xref:mvc/views/working-with-forms) создает маркер защиты от подделки [XSRF](xref:security/anti-request-forgery). Поскольку мы не изменяем данные, проверять маркер безопасности в методе контроллера не нужно.

Так как параметр поиска находится в теле запроса, а не в URL-адресе, эти сведения о поиске нельзя добавить в закладки или открыть для общего доступа. Для устранения этой проблемы укажите, что запрос типа `HTTP GET` должен находиться в файле *Views/Movies/Index.cshtml*.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

После отправки поиска URL-адрес содержит строку поискового запроса. Поиск также переносится в метод `HttpGet Index`, даже если у вас определен метод `HttpPost Index`.

![Окно браузера с фрагментом searchString=ghost в URL-адресе, которое возвращает фильмы Ghostbusters и Ghostbusters 2, с текстом ghost в названии](~/tutorials/first-mvc-app/search/_static/search_get.png)

В следующем примере разметки показано изменение тега `form`:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Добавление поиска по жанру

Добавьте следующий класс `MovieGenreViewModel` в папку *Models*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Модель представления фильмов по жанру будет содержать:

* Список фильмов.
* Объект `SelectList` со списком жанров. В этом списке пользователь может выбрать жанр фильма.
* Объект `MovieGenre`, содержащий выбранный жанр.
* `SearchString`, содержащий текст, который пользователи вводят в поле поиска.

Замените метод `Index` в файле `MoviesController.cs` следующим кодом:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Следующий код определяет запрос `LINQ`, который извлекает все жанры из базы данных.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

Объект `SelectList` со списком жанров создается путем проецирования отдельных жанров (это необходимо, чтобы исключить повторяющиеся жанры).

Когда пользователь выполняет поиск элемента, значение поиска сохраняется в поле поиска.

## <a name="add-search-by-genre-to-the-index-view"></a>Добавление поиска по жанру в представление индекса

Обновите файл `Index.cshtml`, находящийся в папке *Views/Movies/* , следующим образом:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Проверьте лямбда-выражение, которое используется в следующем вспомогательном методе HTML:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

В предыдущем коде вспомогательный метод HTML `DisplayNameFor` проверяет свойство `Title`, указанное в лямбда-выражении, и определяет отображаемое имя. Поскольку лямбда-выражение проверяется, а не вычисляется, в том случае, если `model`, `model.Movies` или `model.Movies[0]` имеют значение `null` или пусты, не происходит нарушение прав доступа. При вычислении лямбда-выражения (например, `@Html.DisplayFor(modelItem => item.Title)`) вычисляются значения для свойств модели.

Проверьте работу приложения, выполнив поиск по жанру, по названию фильма и по обоим этим параметрам:

![Окно браузера с результатами https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Назад](controller-methods-views.md)
> [Вперед](new-field.md)
