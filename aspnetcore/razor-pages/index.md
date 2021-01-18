---
title: Введение в Razor Pages в ASP.NET Core
author: Rick-Anderson
description: Здесь приведены сведения о применении в ASP.NET Core функции Razor Pages, которая делает создание кодов сценариев для страниц проще и эффективнее по сравнению с MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: f8cdbbffae9b291923a6d425fef5526b0ec88f61
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253193"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a>Введение в Razor Pages в ASP.NET Core


Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak)

Razor Pages делает создание кодов сценариев для страниц проще и эффективнее по сравнению с использованием контроллеров и представлений.

Если вам нужно руководство, использующее подход "модель-представление-контроллер", см. статью [Начало работы с MVC в ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc).

Этот документ содержит вводные сведения о Razor Pages. Это не пошаговое руководство. Если некоторые разделы покажутся вам слишком сложными, см. [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start). Общие сведения об ASP.NET Core см. в разделе [Введение в ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Предварительные требования

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Создание проекта Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Из командной строки выполните команду `dotnet new webapp`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

---

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages активируется в файле *Startup.cs*:

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Рассмотрим простую страницу: <a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Приведенный выше код выглядит как [Razor файл представления](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями. Он отличается от него только директивой [`@page`](xref:mvc/views/razor#page). Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер. `@page` должна быть первой директивой Razor на странице. `@page` влияет на поведение всех остальных конструкций [Razor](xref:mvc/views/razor). Имена файлов Razor Pages имеют суффикс *.cshtml*.

Похожая страница с использованием класса `PageModel` показана в следующих двух файлах. Файл *Pages/Index2.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Модель страницы *Pages/Index2.cshtml.cs*:

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *CS*. Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*. Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.

Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе. В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.

| Имя файла и путь               | Соответствующий URL |
| ----------------- | ------------ |
| */Pages/index.cshtml* | `/` или `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` или `/Store/Index` |

Примечания.

* Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.
* Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.

## <a name="write-a-basic-form"></a>Создание простой формы

Razor Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения. [Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages. Рассмотрим страницу с простой формой связи для модели `Contact`.

В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).

Для работы с базой данных в памяти требуется пакет NuGet `Microsoft.EntityFrameworkCore.InMemory`.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Модель данных:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Контекст базы данных:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Файл представления *Pages/Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Модель страницы *Pages/Create.cshtml.cs*:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.

Класс `PageModel` позволяет разделять логику страницы и ее представление. Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации. Это разделение позволяет:

* управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).
* [Модульное тестирование](xref:test/razor-pages-tests)

Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму). Можно добавить методы обработчика для любой HTTP-команды. Наиболее распространенные обработчики

* `OnGet` — инициализация необходимого для страницы состояния. В приведенном выше коде метод `OnGet` отображает страницу Razor *CreateModel.cshtml*.
* `OnPost` — обработка отправленных через форму данных.

Суффикс `Async` не является обязательным, но часто используется для асинхронных функций. Этот код типичен для Razor Pages.

Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:

* Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.
* Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation) и результаты действий, одинаково работают с контроллерами и Razor Pages. 

Предыдущий метод `OnPostAsync`:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Простая схема `OnPostAsync`:

Проверка на наличие ошибок проверки.

* Если ошибок нет, сохранение данных и перенаправление.
* Если есть ошибки, отображение страницы с сообщениями проверки. Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.

Файл представления *Pages/Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Преобразованный HTML из *Pages/Create.cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

В приведенном выше коде, разместив форму:

* С допустимыми данными:

  * Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>. `RedirectToPage` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Является результатом действия.
    * Аналогичен `RedirectToAction` или `RedirectToRoute` (используется в контроллерах и представлениях).
    * Настраивается для страниц. В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`). Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).

* С ошибками проверки, передаваемыми на сервер:

  * Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>. `Page` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`. `PageResult` — тип возвращаемого значения по умолчанию для метода обработчика. Метод обработчика, вернувший `void`, визуализирует страницу.
  * В предыдущем примере публикация формы без значения приводит к тому, что [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) возвращает значение false. В этом примере на клиенте не отображаются ошибки проверки. Обработка ошибок проверки рассматривается далее в этом документе.

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* С ошибками проверки, обнаруженными при проверке на стороне клиента:

  * Данные **не** отправляются на сервер.
  * Проверка на стороне клиента описана далее в этом документе.

Для указания согласия на привязку модели в свойстве `Customer` используется атрибут [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute):

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`**не** следует использовать в моделях, содержащих свойства, которые не должны изменяться клиентом. Дополнительные сведения см. в [этом разделе](xref:data/ef-rp/crud#overposting).

По умолчанию Razor привязывает свойства ко всем командам, кроме `GET`. Привязка к свойствам устраняет необходимость в написании кода для преобразования данных HTTP в тип модели. Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.

[!INCLUDE[](~/includes/bind-get.md)]

Просмотр файла представления *Pages/Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* В приведенном выше коде [вспомогательная функция тега ввода](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` привязывает элемент `<input>` HTML к выражению модели `Customer.Name`.
* Директива [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) обеспечивает доступность вспомогательных функций тегов.

### <a name="the-home-page"></a>Домашняя страница

*Index.cshtml* — это домашняя страница:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

Связанный класс `PageModel` (*Index.cshtml.cs*):

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Файл *index.cshtml* содержит следующую разметку:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования. Эта ссылка содержит данные о маршруте с идентификатором контактного лица. Например, `https://localhost:5001/Edit/1`. [Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.

Файл *Index.cshtml* содержит разметку для создания кнопки удаления у каждого контакта клиента:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

Отображаемый HTML:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Во время обработки кнопки удаления в HTML ее [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) включает параметры для следующего:

* Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.
* Параметр `handler`, указанный атрибутом `asp-page-handler`.

При выборе кнопки на сервер отправляется запрос формы `POST`. По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.

Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`. Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

Метод `OnPostDeleteAsync`:

* Получает `id` из строки запроса.
* Отправляет в базу данных запрос контакта клиента с `FindAsync`.
* Если контакт клиента найден, он удаляется, и база данных обновляется.
* Вызывает <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> для перенаправления на корневую страницу индекса (`/Index`).

### <a name="the-editcshtml-file"></a>Файл Edit.cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

Первая строка содержит директиву `@page "{id:int}"`. Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`. Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено). Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:

 ```cshtml
@page "{id:int?}"
```

Файл *Edit.cshtml.cs*:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Проверка

Правила проверки:

* Декларативно задаются в классе Model.
* Применяются везде в приложении.

Пространство имен <xref:System.ComponentModel.DataAnnotations> предоставляет набор встроенных атрибутов проверки, которые декларативно применяются к классу или свойству. Кроме того, DataAnnotations содержит атрибуты форматирования (такие как [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)), которые обеспечивают форматирование и не предназначены для проверки.

Рассмотрим модель `Customer`:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Используя следующий файл представления *Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Предыдущий код:

* Включает jQuery и скрипты проверки jQuery.
* Использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) `<div />` и `<span />` для следующих задач:

  * Проверка на стороне клиента.
  * Отображение ошибок при проверке.

* Генерирует следующий HTML:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

При публикации формы создания без значения имени в форме отображается сообщение об ошибке: "Поле имени является обязательным". в форме. Если на клиенте включен JavaScript, браузер отображает ошибку без отправки на сервер.

Атрибут `[StringLength(10)]` создает `data-val-length-max="10"` в отображаемом HTML-коде. `data-val-length-max` не дает браузерам ввести больше заданной максимальной длины. Если для изменения и воспроизведения записи используется средство, например [Fiddler](https://www.telerik.com/fiddler), выполните следующие действия:

* С именем, превышающим 10.
* Возвращается сообщение об ошибке: "Имя поля должно быть строкой с максимальной длиной 10". .

Рассмотрим следующую модель `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Атрибуты проверки определяют поведение для свойств модели, к которым они применяются:

* Атрибуты `Required` и `MinimumLength` указывают, что свойство должно иметь значение. Тем не менее, чтобы удовлетворить требованиям проверки, пользователю достаточно ввести пробел.
* Атрибут `RegularExpression` ограничивает набор допустимых для ввода символов. В приведенном выше коде в Genre:

  * должны использоваться только буквы;
  * первая буква должна быть прописной; пробелы, цифры и специальные символы не допускаются.

* В `RegularExpression` Rating:

  * первый символ должен быть прописной буквой;
  * допускаются специальные символы и цифры, а также последующие пробелы. Значение "PG-13" допустимо для рейтинга, но недопустимо для жанра.

* Атрибут `Range` ограничивает диапазон значений.
* Атрибут `StringLength` задает максимальную и при необходимости минимальную длину строкового свойства.
* Типы значений (например, `decimal`, `int`, `float`, `DateTime`) по своей природе являются обязательными и не требуют атрибута `[Required]`.

На странице создания для модели `Movie` отображаются ошибки с недопустимыми значениями:

![Форма просмотра фильма с несколькими ошибками проверки jQuery на стороне клиента](~/tutorials/razor-pages/validation/_static/val.png)

Дополнительные сведения можно найти в разделе

* [Добавление проверки в приложение Movie](xref:tutorials/razor-pages/validation)
* [Проверка модели в ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Обработка запросов HEAD с помощью вызова резервного обработчика OnGet

Запросы `HEAD` позволяют получать заголовки для определенного ресурса. В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.

Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Если обработчик `OnHead` не определен, Razor Pages выполнит вызов обработчика `OnGet`.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF и Razor Pages

В Razor Pages реализована [проверка для защиты от подделки](xref:security/anti-request-forgery). [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) вставляет маркеры защиты от подделки в элементы HTML-форм.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages

Pages работает со всеми функциями подсистемы просмотра Razor. Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.

Давайте упростим нашу страницу с помощью некоторых из этих функций.

Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

Этот [макет](xref:mvc/views/layout):

* управляет макетом каждой страницы (кроме страниц с отказом от макета);
* импортирует HTML-структуры, такие как JavaScript и таблицы стилей.
* Содержимое страницы Razor отображается в том месте, где вызывается `@RenderBody()`.

Дополнительные сведения см. [здесь](xref:mvc/views/layout).

Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Макет хранится в папке *Pages/Shared*. Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница. Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.

Файл макета следует поместить в папку *Pages/Shared*.

Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*. *Views/Shared* — это шаблон представлений MVC. Razor Pages опирается на иерархию папок, а не на условные обозначения путей.

Поиск представлений в Razor Pages охватывает папку *Pages*. Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.

Добавим файл *Pages/_ViewImports.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` описывается далее в этом руководстве. Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.

<a name="namespace"></a>

Директива `@namespace`, заданная на странице:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Директива `@namespace` задает пространство имен для страницы. Включать пространство имен в директиву `@model` не требуется.

Если директива `@namespace` содержится в файле *_ViewImports.cshtml*, указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`. Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.

Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.

`@namespace` *также работает со стандартными представлениями Razor.*

Рассмотрим файл представления *Pages/Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Обновленный файл представления *Pages/Create.cshtml* с *_ViewImports.cshtml* и предыдущим файлом макета:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

В приведенном выше коде *_ViewImports. cshtml* импортировал пространство имен и вспомогательные функции тегов. Файл макета импортировал файлы JavaScript.

[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml*, который подключает проверку на стороне клиента.

Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Формирование URL-адресов для страниц

На представленной выше странице `Create` используется `RedirectToPage`:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Это приложение имеет следующую структуру файлов и папок:

* */Pages*

  * *Index.cshtml*
  * *Privacy.cshtml*
  * */Customers*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Customers/Index.cshtml*. Строка `./Index` — это относительное имя страницы, используемое для доступа к предыдущей странице. Эта строка позволяет создавать URL-адреса для страницы *Pages/Customers/Index.cshtml*. Пример:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

С помощью абсолютного имени страницы `/Index` создаются URL-адреса страницы *Pages/Index.cshtml*. Пример:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Имя страницы — это путь к странице из корневой папки */Pages*, включая начальный символ `/` (например, `/Index`). Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса. Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.

Формирование URL-адресов для страниц поддерживает относительные имена. В приведенной ниже таблице показано, какая страница индекса выбирается с помощью разных параметров `RedirectToPage` в *Pages/Customers/Create.cshtml*.

| RedirectToPage(x)| Страница |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*. Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.

Привязка относительных имен полезна при создании сайтов со сложной структурой. Если относительные имена используются для связи между страницами в папке:

* Переименование папки не нарушает относительные ссылки.
* Ссылки не нарушаются, так как не содержат имя папки.

Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Дополнительные сведения см. в разделах <xref:mvc/controllers/areas> и <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Атрибут ViewData

Данные могут передаваться на страницу с помощью атрибута <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>. Значения свойств с атрибутом `[ViewData]` хранятся в <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> и загружаются из него.

В следующем примере `AboutModel` применяет атрибут `[ViewData]` к свойству `Title`:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.

```cshtml
<h1>@Model.Title</h1>
```

В макете заголовок считывается из словаря ViewData.

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core предоставляет <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Это свойство хранит данные до тех пор, пока они не будут прочитаны. Для проверки данных без удаления можно использовать методы <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> и <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*>. `TempData` удобно использовать для перенаправления, когда данные требуются больше, чем для одного запроса.

В следующем коде значение `Message` задается с помощью `TempData`:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Несколько обработчиков на страницу

Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`. Атрибут `asp-page-handler` является дополнением к `asp-page`. Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей. `asp-page` не задается, так как пример сопоставлен с текущей страницей.

Модель страницы

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

В представленном выше коде используются *именованные методы обработчика*. Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть). В приведенном выше примере использовались методы страницы OnPost **JoinList** Async и OnPost **JoinListUC** Async. Если убрать *OnPost* и *Async*, имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Пользовательские маршруты

С помощью директивы `@page` можно сделать следующее.

* Указать пользовательский маршрут к странице. Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.
* Добавить сегменты к маршруту страницы по умолчанию. Например, к такому маршруту можно добавить сегмент item: `@page "item"`.
* Добавить параметры к маршруту страницы по умолчанию. Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.

Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути. Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.

Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика. Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`. URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

Символ `?` после `handler` означает, что параметр маршрута является необязательным.

## <a name="advanced-configuration-and-settings"></a>Расширенная конфигурация и параметры

Конфигурация и параметры в следующих разделах не требуются большинству приложений.

Для настройки расширенных параметров используйте перегрузку <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>, которая настраивает <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Используйте <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, чтобы задавать корневой каталог для страниц и добавлять для них соглашения моделей приложений. Дополнительные сведения о соглашениях см. в разделе [Соглашения об авторизации Razor Pages](xref:security/authorization/razor-pages-authorization).

Сведения о предварительной компиляции представлений см. на странице [Компиляция представлений Razor](xref:mvc/views/view-compilation).

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Указание местонахождения Razor Pages в корне каталога

По умолчанию Razor Pages находится в корне каталога */Pages*. Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*>, чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) приложения:

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Указание местонахождения Razor Pages в пользовательском корневом каталоге

Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>, чтобы указать, что Razor Pages находится в пользовательском корневом каталоге в приложении (укажите относительный путь):

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* Дополнительные общие сведения см. на странице [Учебник. Начало работы с Razor Pages в ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).
* [Атрибут authorize и Razor Pages](xref:security/authorization/simple#aarp)
* [Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).
* <xref:index>
* [Справочник по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Создание проекта Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Из командной строки выполните команду `dotnet new webapp`.

Откройте созданный файл *.csproj* в Visual Studio для Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Из командной строки выполните команду `dotnet new webapp`.

---

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages активируется в файле *Startup.cs*:

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Рассмотрим простую страницу: <a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Приведенный выше код выглядит как [файл представления Razor](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями. и отличается от него только директивой `@page`. Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер. `@page` должна быть первой директивой Razor на странице. `@page` влияет на поведение всех остальных конструкций Razor.

Похожая страница с использованием класса `PageModel` показана в следующих двух файлах. Файл *Pages/Index2.cshtml*:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Модель страницы *Pages/Index2.cshtml.cs*:

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *CS*. Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*. Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.

Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе. В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.

| Имя файла и путь               | Соответствующий URL |
| ----------------- | ------------ |
| */Pages/index.cshtml* | `/` или `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` или `/Store/Index` |

Примечания.

* Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.
* Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.

## <a name="write-a-basic-form"></a>Создание простой формы

Razor Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения. [Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages. Рассмотрим страницу с простой формой связи для модели `Contact`.

В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Модель данных:

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Контекст базы данных:

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Файл представления *Pages/Create.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Модель страницы *Pages/Create.cshtml.cs*:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.

Класс `PageModel` позволяет разделять логику страницы и ее представление. Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации. Это разделение позволяет:

* управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).
* [Модульное тестирование](xref:test/razor-pages-tests) страниц.

Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму). Методы обработчика можно добавить для любой HTTP-команды. Наиболее распространенные обработчики

* `OnGet` — инициализация необходимого для страницы состояния. Пример обработчика [OnGet](#OnGet).
* `OnPost` — обработка отправленных через форму данных.

Суффикс `Async` не является обязательным, но часто используется для асинхронных функций. Этот код типичен для Razor Pages.

Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:

* Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.
* Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation), [проверку](xref:mvc/models/validation) и результаты действий, являются общими.

Предыдущий метод `OnPostAsync`:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Простая схема `OnPostAsync`:

Проверка на наличие ошибок проверки.

* Если ошибок нет, сохранение данных и перенаправление.
* Если есть ошибки, отображение страницы с сообщениями проверки. Проверка на стороне клиента выполняется точно так же, как в традиционных приложениях MVC ASP.NET Core. Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.

После успешного ввода данных метод обработчика `OnPostAsync` вызывает метод обработчика `RedirectToPage`, чтобы получить экземпляр `RedirectToPageResult`. `RedirectToPage` — это новый результат действия, аналогичный `RedirectToAction` или `RedirectToRoute`, но настроенный для страниц. В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`). Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).

Если в отправленной форме имеются ошибки проверки (переданные на сервер), метод обработчика `OnPostAsync` вызывает метод обработчика `Page`. `Page` возвращает экземпляр `PageResult`. Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`. `PageResult` — тип возвращаемого значения по умолчанию для метода обработчика. Метод обработчика, вернувший `void`, визуализирует страницу.

Для указания согласия на привязку модели в свойстве `Customer` используется атрибут `[BindProperty]`.

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

По умолчанию Razor Pages привязывает свойства ко всем командам, кроме `GET`. Привязка к свойствам позволяет сократить объем необходимого кода. Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.

[!INCLUDE[](~/includes/bind-get.md)]

Домашняя страница (*Index.cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Связанный класс `PageModel` (*Index.cshtml.cs*):

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Файл *Index.cshtml* содержит следующую разметку для создания ссылки на правку для каждого контактного лица.

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования. Эта ссылка содержит данные о маршруте с идентификатором контактного лица. Например, `https://localhost:5001/Edit/1`. [Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor. Вспомогательные функции тегов включены с помощью `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Файл *Pages/Edit.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

Первая строка содержит директиву `@page "{id:int}"`. Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`. Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено). Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:

 ```cshtml
@page "{id:int?}"
```

Файл *Pages/Edit.cshtml.cs*:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Файл *Index.cshtml* также содержит разметку для создания кнопки удаления у каждого контакта клиента:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Во время обработки кнопки удаления в HTML ее `formaction` включает параметры для следующего:

* Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.
* Параметр `handler`, указанный атрибутом `asp-page-handler`.

Пример отображенной кнопки удаления с идентификатором контакта клиента `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

При выборе кнопки на сервер отправляется запрос формы `POST`. По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.

Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`. Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`. В приведенном ниже коде показан обработчик `OnPostDeleteAsync`:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

Метод `OnPostDeleteAsync`:

* Принимает `id` из строки запроса. Если директива страницы *index.cshtml* содержит ограничение маршрутизации `"{id:int?}"`, то `id` будет получено из данных маршрута. Данные маршрута для `id` указываются в универсальном коде ресурса (URI), например `https://localhost:5001/Customers/2`.
* Отправляет в базу данных запрос контакта клиента с `FindAsync`.
* Если контакт клиента найден, он удаляется из списка контактов. База данных обновляется.
* Вызывает `RedirectToPage` для перенаправления на корневую страницу индекса (`/Index`).

## <a name="mark-page-properties-as-required"></a>Маркировка свойств страницы как обязательных

Свойства класса `PageModel` можно отметить атрибутом [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Дополнительные сведения см. в статье [Проверка модели](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Обработка запросов HEAD с помощью вызова резервного обработчика OnGet

Запросы `HEAD` позволяют получать заголовки для определенного ресурса. В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.

Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

Если в ASP.NET Core 2.1 или более поздней версии обработчик `OnHead` не определен, Razor Pages выполнит вызов резервного обработчика `OnGet`. Это поведение включается путем вызова [SetCompatibilityVersion](xref:mvc/compatibility-version) в `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Шаблоны по умолчанию создают вызов `SetCompatibilityVersion` в ASP.NET Core 2.1 и 2.2. `SetCompatibilityVersion` задает для параметра `AllowMappingHeadRequestsToGetHandler` Razor Pages значение `true`.

Вместо включения всех возможных поведений с помощью метода `SetCompatibilityVersion` вы можете задать *конкретное* поведение. Следующий код позволяет сопоставлять запросы `HEAD` с обработчиком `OnGet`:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF и Razor Pages

Вам не придется писать отдельный код для [проверки подлинности запросов](xref:security/anti-request-forgery). Razor Pages включает создание и проверку маркеров защиты от подделок по умолчанию.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages

Pages работает со всеми функциями подсистемы просмотра Razor. Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.

Давайте упростим нашу страницу с помощью некоторых из этих функций.

Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

Этот [макет](xref:mvc/views/layout):

* управляет макетом каждой страницы (кроме страниц с отказом от макета);
* импортирует HTML-структуры, такие как JavaScript и таблицы стилей.

Дополнительные сведения см. в статье о [макете](xref:mvc/views/layout).

Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Макет хранится в папке *Pages/Shared*. Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница. Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.

Файл макета следует поместить в папку *Pages/Shared*.

Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*. *Views/Shared* — это шаблон представлений MVC. Razor Pages опирается на иерархию папок, а не на условные обозначения путей.

Поиск представлений в Razor Pages охватывает папку *Pages*. Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.

Добавим файл *Pages/_ViewImports.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` описывается далее в этом руководстве. Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.

<a name="namespace"></a>

Явное применение директивы `@namespace` на странице:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Данная директива задает пространство имен для страницы. Включать пространство имен в директиву `@model` не требуется.

Если директива `@namespace` содержится в файле *_ViewImports.cshtml*, указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`. Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.

Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.

`@namespace` *также работает со стандартными представлениями Razor.*

Исходный файл представления *Pages/Create.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Обновленный файл представления *Pages/Create.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml*, который подключает проверку на стороне клиента.

Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Формирование URL-адресов для страниц

На представленной выше странице `Create` используется `RedirectToPage`:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Это приложение имеет следующую структуру файлов и папок:

* */Pages*

  * *Index.cshtml*
  * */Customers*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Index.cshtml*. Строка `/Index` составляет часть URL-адреса для доступа к указанной выше странице. Строка `/Index` позволяет создавать URL-адреса для страницы *Pages/Index.cshtml*. Пример:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Имя страницы — это путь к странице из корневой папки */Pages*, включая начальный символ `/` (например, `/Index`). Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса. Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.

Формирование URL-адресов для страниц поддерживает относительные имена. В приведенной ниже таблице показано, какая страница индекса выбирается с каждым параметром `RedirectToPage` в *Pages/Customers/Create.cshtml*.

| RedirectToPage(x)| Страница |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*. Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Привязка относительных имен полезна при создании сайтов со сложной структурой. Если для связи между страницами в определенной папке используются относительные имена, эту папку можно переименовать. При этом все ссылки останутся рабочими (так как не включают имя папки).

Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Для получения дополнительной информации см. <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Атрибут ViewData

Данные могут передаваться на страницу с помощью атрибута [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Свойства в контроллерах или моделях Razor Page, отмеченные атрибутом `[ViewData]`, обладают собственными значениями, загружаемыми из [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

В следующем примере класс `AboutModel` содержит свойство `Title`, отмеченное атрибутом `[ViewData]`. Свойство `Title` задает заголовок страницы About.

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.

```cshtml
<h1>@Model.Title</h1>
```

В макете заголовок считывается из словаря ViewData.

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core позволяет использовать свойство [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) в [контроллере](/dotnet/api/microsoft.aspnetcore.mvc.controller). Это свойство хранит данные до тех пор, пока они не будут прочитаны. Для проверки данных без удаления можно использовать методы `Keep` и `Peek`. `TempData` удобно использовать для перенаправления, когда данные требуются больше чем для одного запроса.

В следующем коде значение `Message` задается с помощью `TempData`:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Несколько обработчиков на страницу

Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`. Атрибут `asp-page-handler` является дополнением к `asp-page`. Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей. `asp-page` не задается, так как пример сопоставлен с текущей страницей.

Модель страницы

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

В представленном выше коде используются *именованные методы обработчика*. Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть). В приведенном выше примере использовались методы страницы OnPost **JoinList** Async и OnPost **JoinListUC** Async. Если убрать *OnPost* и *Async*, имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Пользовательские маршруты

С помощью директивы `@page` можно сделать следующее.

* Указать пользовательский маршрут к странице. Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.
* Добавить сегменты к маршруту страницы по умолчанию. Например, к такому маршруту можно добавить сегмент item: `@page "item"`.
* Добавить параметры к маршруту страницы по умолчанию. Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.

Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути. Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.

Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика. Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`. URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

Символ `?` после `handler` означает, что параметр маршрута является необязательным.

## <a name="configuration-and-settings"></a>Конфигурация и параметры

Чтобы настроить дополнительные параметры, воспользуйтесь методом расширения `AddRazorPagesOptions` в построителе MVC:

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

В настоящее время `RazorPagesOptions` позволяет задавать корневую папку для страниц и добавлять для них обозначения моделей приложений. В дальнейшем мы расширим спектр его возможностей.

Сведения о предварительной компиляции представлений см. на странице [Компиляция представлений Razor](xref:mvc/views/view-compilation).

[Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Дополнительные общие сведения см. на странице [Учебник. Начало работы с Razor Pages в ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Указание местонахождения Razor Pages в корне каталога

По умолчанию Razor Pages находится в корне каталога */Pages*. Добавьте [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) приложения:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Указание местонахождения Razor Pages в пользовательском корневом каталоге

Добавьте [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в пользовательской корневой папке приложения (укажите относительный путь):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Атрибут authorize и Razor Pages](xref:security/authorization/simple#aarp)
* <xref:index>
* [Справочник по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
