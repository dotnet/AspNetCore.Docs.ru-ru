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
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="0cc1c-103">Создание приложения Blazor со списком дел</span><span class="sxs-lookup"><span data-stu-id="0cc1c-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="0cc1c-104">В этом учебнике показано, как создать и изменить приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-104">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="0cc1c-105">Вы узнаете, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0cc1c-106">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="0cc1c-106">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="0cc1c-107">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="0cc1c-107">Modify Razor components</span></span>
> * <span data-ttu-id="0cc1c-108">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="0cc1c-108">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="0cc1c-109">Использование маршрутизации в приложении Blazor</span><span class="sxs-lookup"><span data-stu-id="0cc1c-109">Use routing in a Blazor app</span></span>

<span data-ttu-id="0cc1c-110">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение списка задач.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-110">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0cc1c-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="0cc1c-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a><span data-ttu-id="0cc1c-112">Создание приложения Blazor со списком задач</span><span class="sxs-lookup"><span data-stu-id="0cc1c-112">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="0cc1c-113">Создайте в командной строке новое приложение Blazor с именем `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-113">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="0cc1c-114">Предыдущая команда создает папку с именем `TodoList` и параметром `-o|--output` для хранения приложения.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-114">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="0cc1c-115">Папка `TodoList` находится в *корневой папке* проекта.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-115">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="0cc1c-116">Перейдите в папку `TodoList` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-116">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="0cc1c-117">Добавьте в приложение новый компонент Razor `Todo` с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-117">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="0cc1c-118">Параметр `-n|--name` в предыдущей команде задает имя нового компонента Razor.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-118">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="0cc1c-119">Новый компонент создается в папке `Pages` проекта с параметром `-o|--output`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-119">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="0cc1c-120">Имена файлов компонентов Razor должны начинаться с прописной буквы.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-120">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="0cc1c-121">Откройте папку `Pages` и убедитесь, что имя файла компонента `Todo` начинается с заглавной буквы `T`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-121">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="0cc1c-122">Имя файла должно быть `Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-122">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="0cc1c-123">Откройте компонент `Todo` в любом редакторе файлов и добавьте директиву `@page` Razor в начало файла с относительным URL-адресом `/todo`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-123">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="0cc1c-124">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-124">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   <span data-ttu-id="0cc1c-125">Сохраните файл `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-125">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="0cc1c-126">Добавьте компонент `Todo` на панель навигации.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-126">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="0cc1c-127">Компонент `NavMenu` используется в макете этого приложения.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-127">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="0cc1c-128">Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-128">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="0cc1c-129">Когда в приложении загружается URL-адрес компонента, компонент `NavLink` предоставляет соответствующее указание в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-129">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="0cc1c-130">В неупорядоченном списке (`<ul>...</ul>`) компонента `NavMenu` добавьте указанный ниже элемент списка (`<li>...</li>`) и компонент `NavLink` для компонента `Todo`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-130">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="0cc1c-131">В `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-131">In `Shared/NavMenu.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   <span data-ttu-id="0cc1c-132">Сохраните файл `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-132">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="0cc1c-133">Скомпилируйте и запустите приложение, выполнив команду [`dotnet watch run`](xref:tutorials/dotnet-watch) в командной оболочке из папки `TodoList`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-133">Build and run the app by executing the [`dotnet watch run`](xref:tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="0cc1c-134">После запуска приложения перейдите на страницу списка дел, щелкнув ссылку **`Todo`** на панели навигации приложения. В результате будет загружена страница по адресу `/todo`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-134">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="0cc1c-135">Оставьте приложение выполняться в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-135">Leave the app running the command shell.</span></span> <span data-ttu-id="0cc1c-136">При каждом сохранении файла приложение автоматически перестраивается.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-136">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="0cc1c-137">Браузер временно утрачивает подключение к приложению на время компиляции и перезапуска.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-137">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="0cc1c-138">При восстановлении подключения страница в браузере автоматически перезагружается.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-138">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="0cc1c-139">Добавьте в корень проекта (папка `TodoList`) файл `TodoItem.cs`, который будет содержать класс для элемента списка дел.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-139">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="0cc1c-140">Используйте следующий код C# для класса `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-140">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="0cc1c-141">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-141">`TodoItem.cs`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > <span data-ttu-id="0cc1c-142">При использовании Visual Studio для создания файла `TodoItem.cs` и класса `TodoItem` используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-142">If using Visual Studio to create the `TodoItem.cs` file and `TodoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="0cc1c-143">Удалите пространство имен, создаваемое Visual Studio для класса.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-143">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="0cc1c-144">Воспользуйтесь кнопкой **Копировать** в предыдущем блоке кода и замените все содержимое файла, создаваемого Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-144">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="0cc1c-145">Вернитесь к компоненту `Todo` и выполните указанные ниже задачи.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-145">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="0cc1c-146">Добавьте поле для элементов списка дел в блоке `@code`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-146">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="0cc1c-147">Компонент `Todo` использует это поле для сохранения состояния списка дел.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-147">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="0cc1c-148">Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="0cc1c-148">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="0cc1c-149">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-149">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. <span data-ttu-id="0cc1c-150">Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-150">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="0cc1c-151">Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).</span><span class="sxs-lookup"><span data-stu-id="0cc1c-151">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. <span data-ttu-id="0cc1c-152">Сохраните файл `TodoItem.cs` и обновленный файл `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-152">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="0cc1c-153">При сохранении файлов в командной оболочке будет автоматически выполнена повторная сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-153">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="0cc1c-154">Браузер временно утратит подключение к приложению, но когда подключение будет восстановлено, браузер перезагрузит страницу.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-154">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="0cc1c-155">При нажатии кнопки **`Add todo`** ничего не происходит, так как к кнопке не подключен обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-155">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="0cc1c-156">Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-156">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="0cc1c-157">Теперь при нажатии кнопки вызывается метод C# `AddTodo`:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-157">The `AddTodo` C# method is called when the button is selected:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. <span data-ttu-id="0cc1c-158">Чтобы получить заголовок нового элемента списка дел, добавьте строковое поле `newTodo` в начало блока `@code`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-158">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   <span data-ttu-id="0cc1c-159">Измените текстовый элемент `<input>`, чтобы привязать `newTodo` с помощью атрибута `@bind`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-159">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="0cc1c-160">Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-160">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="0cc1c-161">Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-161">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. <span data-ttu-id="0cc1c-162">Сохраните файл `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-162">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="0cc1c-163">В командной оболочке будет автоматически выполнена повторная сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-163">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="0cc1c-164">После повторного подключения браузера к приложению страница в браузере перезагрузится.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-164">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="0cc1c-165">Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-165">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="0cc1c-166">Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-166">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="0cc1c-167">Замените `@todo.Title` элементом `<input>`, который привязан к `todo.Title` с помощью `@bind`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-167">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. <span data-ttu-id="0cc1c-168">Обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).</span><span class="sxs-lookup"><span data-stu-id="0cc1c-168">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="0cc1c-169">Готовый компонент `Todo` (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="0cc1c-169">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. <span data-ttu-id="0cc1c-170">Сохраните файл `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-170">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="0cc1c-171">В командной оболочке будет автоматически выполнена повторная сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-171">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="0cc1c-172">После повторного подключения браузера к приложению страница в браузере перезагрузится.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-172">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="0cc1c-173">Добавьте элементы, измените их и пометьте элементы списка дел как завершенные, чтобы протестировать компонент.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-173">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="0cc1c-174">По окончании завершите работу приложения в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-174">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="0cc1c-175">Многие командные оболочки принимают команду клавиатуры <kbd>CTRL</kbd>+<kbd>C</kbd> для остановки приложения.</span><span class="sxs-lookup"><span data-stu-id="0cc1c-175">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0cc1c-176">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="0cc1c-176">Next steps</span></span>

<span data-ttu-id="0cc1c-177">В этом руководстве вы узнали, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-177">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0cc1c-178">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="0cc1c-178">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="0cc1c-179">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="0cc1c-179">Modify Razor components</span></span>
> * <span data-ttu-id="0cc1c-180">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="0cc1c-180">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="0cc1c-181">Использование маршрутизации в приложении Blazor</span><span class="sxs-lookup"><span data-stu-id="0cc1c-181">Use routing in a Blazor app</span></span>

<span data-ttu-id="0cc1c-182">Подробные сведения об инструментах для ASP.NET Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="0cc1c-182">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
