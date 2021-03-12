---
title: Просмотр компонентов в ASP.NET Core
author: rick-anderson
description: Узнайте, как компоненты представления используются в ASP.NET Core и как добавить их в приложения.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
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
uid: mvc/views/view-components
ms.openlocfilehash: 1d0e0da3a5d047d7457e7ca7587c81029e33cb69
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586011"
---
# <a name="view-components-in-aspnet-core"></a>Просмотр компонентов в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/view-components/sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="view-components"></a>Компоненты представлений

Компоненты представлений похожи на частичные представления, но при этом гораздо функциональнее. Компоненты представлений не используют привязку модели и зависят только от данных, предоставляемых при их вызове. Эта статья написана с использованием контроллеров и представлений, но компоненты представлений также работают со Razor страницами.

Компонент представлений:

* Выполняет отрисовку фрагмента, а не всего отклика.
* Включает те же преимущества разделения функций и тестирования, которые доступны между контроллером и представлением.
* Может иметь параметры и бизнес-логику.
* Обычно вызывается со страницы макета.

Компоненты представлений предназначены для случаев, когда имеется многоразовая логика отрисовки, которая слишком сложна для частичного представления, например:

* Динамические меню навигации
* Облако тегов (где выполняется запрос к базе данных)
* Панель входа
* Корзина для покупок
* Недавно опубликованные статьи
* Содержимое боковой панели в типичном блоге
* Панель входа, которая должна отображаться на каждой странице и содержит ссылки для выхода или входа в зависимости от состояния входа пользователя

Компонент представления состоит из двух частей: класса (обычно производного от [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) и возвращаемого результата (обычно это представление). Как и контроллеры, компонент представления может быть объектом POCO, но большинству разработчиков потребуются преимущества методов и свойств, доступные при наследовании от `ViewComponent`.

При рассмотрении того, соответствуют ли компоненты представления спецификациям приложения, рекомендуется использовать Razor компоненты. Razor компоненты также сочетают разметку с кодом C# для создания повторно используемых единиц пользовательского интерфейса. Razor компоненты предназначены для повышения производительности разработки при предоставлении логики пользовательского интерфейса и компоновки на стороне клиента. Для получения дополнительной информации см. <xref:blazor/components/index>. Сведения о том, как внедрить Razor компоненты в приложение MVC или Razor pages, см <xref:blazor/components/prerendering-and-integration?pivots=server> . в разделе.

## <a name="creating-a-view-component"></a>Создание компонента представления

Этот раздел содержит общие требования к созданию компонента представления. Далее в этой статье мы подробно рассмотрим каждый шаг и создадим компонент представления.

### <a name="the-view-component-class"></a>Класс компонентов представлений

Класс компонентов представлений можно создать одним из следующих способов:

* Наследование от *ViewComponent*
* Декорирование класса с помощью атрибута `[ViewComponent]` или наследование от класса с помощью атрибута `[ViewComponent]`
* Создание класса, где имя заканчивается суффиксом *ViewComponent*

Как и контроллеры, компоненты представлений должны быть открытыми, невложенными и неабстрактными классами. Имя компонента представления — это имя класса с удаленным суффиксом "ViewComponent". Его можно также можно задать явно с помощью свойства `ViewComponentAttribute.Name`.

Класс компонентов представлений:

* Полностью поддерживает [внедрение зависимостей](../../fundamentals/dependency-injection.md) в конструкторе.

* Не участвует в жизненном цикле контроллера, поэтому в компоненте представления невозможно использовать [фильтры](../controllers/filters.md).

### <a name="view-component-methods"></a>Методы компонентов представлений

Компонент представления задает свою логику в методе `InvokeAsync`, возвращающем `Task<IViewComponentResult>`, или в синхронном методе `Invoke`, который возвращает `IViewComponentResult`. Параметры берутся непосредственно из вызова компонента представления, а не из привязки модели. Компонент представления никогда не обрабатывает запрос напрямую. Как правило, компонент представления инициализирует модель и передает ее в представление, вызвав метод `View`. Если кратко, методы компонентов представлений:

* Определяют метод `InvokeAsync`, который возвращает `Task<IViewComponentResult>`, или синхронный метод `Invoke`, возвращающий `IViewComponentResult`.
* Обычно инициализирует модель и передает ее в представление, вызывая `ViewComponent` `View` метод.
* Получают параметры из вызывающего метода, а не HTTP. Это связано с тем, что привязка модели отсутствует.
* Недоступны непосредственно в качестве конечной точки HTTP. Вызываются из кода (обычно в представлении). Компонент представления никогда не обрабатывает запрос.
* Перегружаются по сигнатуре, а не по сведениям из текущего HTTP-запроса.

### <a name="view-search-path"></a>Путь поиска представления

Среда выполнения ищет представление по следующим путям:

* /Views/{Имя контроллера}/Components/{Имя компонента представления}/{Имя представления}
* /Views/Shared/Components/{Имя компонента представления}/{Имя представления}
* /Pages/Shared/Components/{Имя компонента представления}/{Имя представления}

Путь поиска применяется к проектам с использованием контроллеров и представлений и Razor страниц.

По умолчанию для компонента представления используется имя *Default*, то есть файл представления обычно называется *Default.cshtml*. При создании результата компонента представления или при вызове метода `View` можно указать другое имя представления.

Мы рекомендуем назвать файл представления *Default.cshtml* и использовать путь *Views/Shared/Components/{Имя компонента представления}/{Имя представления}*. Компонент представления `PriorityList` в этом примере использует представление компонента представления *Views/Shared/Components/PriorityList/Default.cshtml*.

### <a name="customize-the-view-search-path"></a>Настройка пути поиска представления

Чтобы настроить путь поиска представления, измените Razor <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> коллекцию. Например, чтобы найти представления в пути "/Components/{имя компонента представления}/{имя представления}", добавьте в коллекцию новый элемент:

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

В приведенном выше коде заполнитель {0} представляет путь "Components/{имя компонента представления}/{имя представления}".

## <a name="invoking-a-view-component"></a>Вызов компонента представления

Чтобы использовать компонент представления, вызовите следующий код внутри представления:

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

Эти параметры передаются в метод `InvokeAsync`. Компонент представления `PriorityList`, разрабатываемый в рамках этой статьи, вызывается из файла представления *Views/ToDo/Index.cshtml*. Следующий код вызывает метод `InvokeAsync` с двумя параметрами:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a>Вызов компонента представления в качестве вспомогательной функции тегов

Для ASP.NET Core 1.1 и более поздних версий можно вызвать компонент представления в качестве [вспомогательной функции тегов](xref:mvc/views/tag-helpers/intro):

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

Параметры методов и классов, указанные в стиле Pascal, для вспомогательных функций тегов преобразуются в [кебаб-стиль](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). Вспомогательная функция тегов для вызова компонента представления использует элемент `<vc></vc>`. Компонент представления задан следующим образом:

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

Чтобы использовать компонент представления в качестве вспомогательного приложения тегов, зарегистрируйте с помощью директивы `@addTagHelper`сборку, содержащую этот компонент представления. Если компонент представления находится в сборке с именем `MyWebApp`, добавьте в файл *_ViewImports.cshtml* следующую директиву:

```cshtml
@addTagHelper *, MyWebApp
```

Зарегистрировать компонент представления в качестве вспомогательной функции тегов можно в любом файле, который ссылается на этот компонент представления. Раздел [Управление областью вспомогательной функции тегов](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) содержит дополнительные сведения о том, как зарегистрировать вспомогательные функции тегов.

Метод `InvokeAsync`, используемый в этом учебнике:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

В разметке вспомогательной функции тегов:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

В предыдущем примере компонент представления `PriorityList` становится `priority-list`. Параметры передаются в компонент представления как атрибуты в кебаб-стиле.

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a>Вызов компонента представления непосредственно из контроллера

Компоненты представлений обычно вызываются из представления, но их можно вызывать непосредственно из метода контроллера. Если компоненты представлений не определяют конечные точки, например контроллеры, можно легко реализовать действие контроллера, возвращающее содержимое `ViewComponentResult`.

В этом примере компонент представления вызывается непосредственно из контроллера:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>Пошаговое руководство. Создание простого компонента представления

[Скачайте](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/view-components/sample) начальный код и выполните его сборку и тестирование. Это простой проект с `ToDo` контроллером, отображающим список элементов *TODO* .

![Список дел](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>Добавление класса ViewComponent

Создайте папку *ViewComponents* и добавьте следующий класс `PriorityListViewComponent`:

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

Примечания к коду:

* Классы компонентов представлений могут находиться в **любой** папке проекта.
* Так как имя класса PriorityList **ViewComponent** заканчивается суффиксом **ViewComponent**, среда выполнения использует строку "PriorityList" при ссылке на компонент класса из представления. Я подробнее расскажу об этом чуть позже.
* Атрибут `[ViewComponent]` может изменить имя, используемое для ссылки на компонент представления. Например, мы могли назвать класс `XYZ` и применить атрибут `ViewComponent`:

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* Приведенный выше атрибут `[ViewComponent]` предписывает средству выбора компонентов представлений использовать имя `PriorityList` при поиске представлений, связанных с данным компонентом, и строку "PriorityList" при ссылке на компонент класса из представления. Я подробнее расскажу об этом чуть позже.
* Компонент использует [внедрение зависимостей](../../fundamentals/dependency-injection.md) для предоставления контекста данных.
* `InvokeAsync` предоставляет метод, который возможно вызывать из представления и который может принять произвольное число аргументов.
* Метод `InvokeAsync` возвращает набор элементов `ToDo`, удовлетворяющих параметрам `isDone` и `maxPriority`.

### <a name="create-the-view-component-razor-view"></a>Создание представления компонента представления Razor

* Создайте папку *Views/Shared/Components*. Она **должна** называться *Components*.

* Создайте папку *Views/Shared/Components/PriorityList*. Ее имя должно соответствовать имени класса представлений компонентов или имени класса без суффикса (если мы следовали соглашению и использовали суффикс *ViewComponent* в имени класса). Если вы использовали атрибут `ViewComponent`, имя класса должно соответствовать его обозначению.

* Создайте представление views */Shared/Components/PriorityList/Default. cshtml* Razor :


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   RazorПредставление принимает список `TodoItem` и отображает их. Если метод `InvokeAsync` компонента представления не передает имя представления (как в нашем примере), по соглашению используется имя *Default*. Далее в этом учебнике я покажу, как передать имя представления. Чтобы переопределить стиль по умолчанию для конкретного контроллера, добавьте представление в папку представления для конкретного контроллера (например, views */TODO/Components/PriorityList/Default. cshtml)*.

    Если компонент представления связан с конкретным контроллером, его можно добавить в папку этого контроллера (*Views/ToDo/Components/PriorityList/Default.cshtml*).

* Добавьте объект, `div` содержащий вызов компонента списка приоритетов, в нижнюю часть файла *views/TODO/index. cshtml* :

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

`@await Component.InvokeAsync` в разметке показывает синтаксис для вызова компонентов представлений. Первым аргументом является имя компонента, который требуется вызвать. Последующие параметры передаются в компонент. `InvokeAsync` может занять произвольное число аргументов.

Тестирование приложения. Следующий рисунок показывает список дел и элементы с приоритетом:

![список дел и элементы с приоритетом](view-components/_static/pi.png)

Кроме того, компонент представления можно вызвать непосредственно из контроллера:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![элементы с приоритетом из действия IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>Указание имени представления

Сложному представлению компонента в некоторых условиях может потребоваться указать нестандартное представление. Ниже показано, как указать представление "PVC" из метода `InvokeAsync`. Обновите метод `InvokeAsync` в классе `PriorityListViewComponent`.

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

Скопируйте файл *Views/Shared/Components/PriorityList/Default.cshtml* в представление *Views/Shared/Components/PriorityList/PVC.cshtml*. Добавьте заголовок, чтобы указать используемое представление PVC.

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

Измените *Views/ToDo/Index.cshtml*:

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

Запустите приложение и проверьте представление PVC.

![Компонент представления с приоритетом](view-components/_static/pvc.png)

Если представление PVC не отображается, убедитесь, что вы вызываете компонент представления с приоритетом 4 или выше.

### <a name="examine-the-view-path"></a>Проверка пути к представлению

* Задайте для параметра приоритета значение 3 или меньше, чтобы представление с приоритетом не возвращалось.
* Временно переименуйте *Views/ToDo/Components/PriorityList/Default.cshtml* в *1Default.cshtml*.
* Проверьте приложение, при этом происходит следующая ошибка:

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* Скопируйте *views/TODO/Components/PriorityList/1Default. cshtml* в *views/Shared/Components/PriorityList/Default. cshtml*.
* Добавьте разметку в представление компонента представления дел *Shared*, чтобы указать, что это представление из папки *Shared*.
* Протестируйте представление компонента **Shared**.

![Выходные данные дел с представлением компонента Shared](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a>Как избежать жестко запрограммированных строк

Чтобы обеспечить безопасность во время компиляции, можно заменить жестко заданное имя компонента представления на имя класса. Создайте компонент представления без суффикса "ViewComponent":

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

Добавьте `using` оператор в Razor файл представления и используйте `nameof` оператор:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a>Выполнение синхронных задач

Платформа обрабатывает вызов синхронного метода `Invoke`, если не требуется асинхронное выполнение работы. Следующий метод создает синхронный компонент представления `Invoke`:

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

В файле компонента представления Razor перечислены строки, передаваемые `Invoke` методу (*Views/Home/Components/PriorityList/Default. cshtml*):

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

Компонент представления вызывается в Razor файле (например, *Views/Home/Index. cshtml*) с помощью одного из следующих подходов:

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [Вспомогательное приложение тегов](xref:mvc/views/tag-helpers/intro)

Чтобы использовать подход <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>, вызовите `Component.InvokeAsync`:

::: moniker-end

::: moniker range="< aspnetcore-1.1"

Компонент представления вызывается в Razor файле (например, *Views/Home/Index. cshtml*) с <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .

Вызов `Component.InvokeAsync`:

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

Чтобы использовать вспомогательное приложение тэгов зарегистрируйте с помощью директивы `@addTagHelper`сборку, содержащую этот компонент представления (он находится в сборке с именем `MyWebApp`):

```cshtml
@addTagHelper *, MyWebApp
```

Используйте вспомогательную функцию тега компонента View в Razor файле разметки:

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

Сигнатура метода `PriorityList.Invoke` синхронна, но Razor находит и вызывает метод `Component.InvokeAsync` в файле разметки.

## <a name="all-view-component-parameters-are-required"></a>Все параметры компонентов представления обязательны

Каждый параметр в компоненте представления является обязательным атрибутом. Также см. [эту проблему в GitHub](https://github.com/dotnet/AspNetCore/issues/5011). Если какой-либо параметр отсутствует:

* Сигнатура метода `InvokeAsync` не совпадет, поэтому метод не будет выполняться.
* Компонент ViewComponent не отобразит никакой разметки.
* Не будут выдаваться ошибки.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Внедрение зависимостей в представления](xref:mvc/views/dependency-injection)
