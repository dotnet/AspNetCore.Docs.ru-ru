---
title: Создание приложения Blazor со списком дел
author: guardrex
description: Узнайте, как создать приложение Blazor по шагам.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2021
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 260d921316d6fadecbd42db11048593b19a5ddee
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394438"
---
# <a name="build-a-blazor-todo-list-app"></a>Создание приложения Blazor со списком дел

В этом учебнике показано, как создать и изменить приложение Blazor. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создавать проект приложения Blazor со списком задач;
> * изменять компоненты Razor;
> * использовать обработку событий и привязку данных в компонентах;
> * Использование маршрутизации в приложении Blazor

Когда вы выполните задачи из этого руководства, у вас будет работающее приложение списка задач.

## <a name="prerequisites"></a>Предварительные требования

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a>Создание приложения Blazor со списком задач

1. Создайте в командной строке новое приложение Blazor с именем `TodoList`:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Предыдущая команда создает папку с именем `TodoList` и параметром `-o|--output` для хранения приложения. Папка `TodoList` находится в *корневой папке* проекта. Перейдите в папку `TodoList` с помощью следующей команды:

   ```dotnetcli
   cd TodoList
   ```

1. Добавьте в приложение новый компонент Razor `Todo` с помощью следующей команды.

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   Параметр `-n|--name` в предыдущей команде задает имя нового компонента Razor. Новый компонент создается в папке `Pages` проекта с параметром `-o|--output`.

   > [!IMPORTANT]
   > Имена файлов компонентов Razor должны начинаться с прописной буквы. Откройте папку `Pages` и убедитесь, что имя файла компонента `Todo` начинается с заглавной буквы `T`. Имя файла должно быть `Todo.razor`.

1. Откройте компонент `Todo` в любом редакторе файлов и добавьте директиву `@page` Razor в начало файла с относительным URL-адресом `/todo`.

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   Сохраните файл `Pages/Todo.razor`.

1. Добавьте компонент `Todo` на панель навигации.

   Компонент `NavMenu` используется в макете этого приложения. Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении. Когда в приложении загружается URL-адрес компонента, компонент `NavLink` предоставляет соответствующее указание в пользовательском интерфейсе.

   В неупорядоченном списке (`<ul>...</ul>`) компонента `NavMenu` добавьте указанный ниже элемент списка (`<li>...</li>`) и компонент `NavLink` для компонента `Todo`.

   В `Shared/NavMenu.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   Сохраните файл `Shared/NavMenu.razor`.

1. Скомпилируйте и запустите приложение, выполнив команду [`dotnet watch run`](xref:tutorials/dotnet-watch) в командной оболочке из папки `TodoList`. После запуска приложения перейдите на страницу списка дел, щелкнув ссылку **`Todo`** на панели навигации приложения. В результате будет загружена страница по адресу `/todo`.

   Оставьте приложение выполняться в командной оболочке. При каждом сохранении файла приложение автоматически перестраивается. Браузер временно утрачивает подключение к приложению на время компиляции и перезапуска. При восстановлении подключения страница в браузере автоматически перезагружается.

1. Добавьте в корень проекта (папка `TodoList`) файл `TodoItem.cs`, который будет содержать класс для элемента списка дел. Используйте следующий код C# для класса `TodoItem`.

   `TodoItem.cs`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > При использовании Visual Studio для создания файла `TodoItem.cs` и класса `TodoItem` используйте один из следующих подходов:
   >
   > * Удалите пространство имен, создаваемое Visual Studio для класса.
   > * Воспользуйтесь кнопкой **Копировать** в предыдущем блоке кода и замените все содержимое файла, создаваемого Visual Studio.

1. Вернитесь к компоненту `Todo` и выполните указанные ниже задачи.

   * Добавьте поле для элементов списка дел в блоке `@code`. Компонент `Todo` использует это поле для сохранения состояния списка дел.
   * Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел. Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. Сохраните файл `TodoItem.cs` и обновленный файл `Pages/Todo.razor`. При сохранении файлов в командной оболочке будет автоматически выполнена повторная сборка приложения. Браузер временно утратит подключение к приложению, но когда подключение будет восстановлено, браузер перезагрузит страницу.

1. При нажатии кнопки **`Add todo`** ничего не происходит, так как к кнопке не подключен обработчик событий.

1. Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`. Теперь при нажатии кнопки вызывается метод C# `AddTodo`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. Чтобы получить заголовок нового элемента списка дел, добавьте строковое поле `newTodo` в начало блока `@code`.

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   Измените текстовый элемент `<input>`, чтобы привязать `newTodo` с помощью атрибута `@bind`.

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список. Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. Сохраните файл `Pages/Todo.razor`. В командной оболочке будет автоматически выполнена повторная сборка приложения. После повторного подключения браузера к приложению страница в браузере перезагрузится.

1. Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач. Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`. Замените `@todo.Title` элементом `<input>`, который привязан к `todo.Title` с помощью `@bind`.

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. Обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Готовый компонент `Todo` (`Pages/Todo.razor`):

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. Сохраните файл `Pages/Todo.razor`. В командной оболочке будет автоматически выполнена повторная сборка приложения. После повторного подключения браузера к приложению страница в браузере перезагрузится.

1. Добавьте элементы, измените их и пометьте элементы списка дел как завершенные, чтобы протестировать компонент.

1. По окончании завершите работу приложения в командной оболочке. Многие командные оболочки принимают команду клавиатуры <kbd>CTRL</kbd>+<kbd>C</kbd> для остановки приложения.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * создавать проект приложения Blazor со списком задач;
> * изменять компоненты Razor;
> * использовать обработку событий и привязку данных в компонентах;
> * Использование маршрутизации в приложении Blazor

Подробные сведения об инструментах для ASP.NET Core Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
