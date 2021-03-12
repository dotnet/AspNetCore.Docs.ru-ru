---
title: Учебник. Использование ASP.NET MVC с EF Core. Обновление связанных данных
description: В этом руководстве описано обновление связанных данных путем обновления полей внешнего ключа и свойств навигации.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: dadfceca2431ace558700ae06417f01c99751419
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588286"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Учебник. Использование ASP.NET MVC с EF Core. Обновление связанных данных

В предыдущем руководстве вы отобразили связанные данные. В этом руководстве описано обновление связанных данных путем обновления полей внешнего ключа и свойств навигации.

На следующих рисунках изображены некоторые из страниц, с которыми вы будете работать.

![Страница редактирования курса](update-related-data/_static/course-edit.png)

![Страница редактирования преподавателя](update-related-data/_static/instructor-edit-courses.png)

В этом учебнике рассмотрены следующие задачи.

> [!div class="checklist"]
> * Настройка страниц курсов
> * Добавление страницы редактирования данных о преподавателях
> * Добавление курсов на страницу редактирования
> * Обновление страницы удаления
> * Добавление расположения кабинета и курсов на страницу создания

## <a name="prerequisites"></a>Предварительные требования

* [Чтение связанных данных](read-related-data.md)

## <a name="customize-courses-pages"></a>Настройка страниц курсов

Создаваемая сущность курса должна иметь связь с существующей кафедрой. Чтобы упростить эту задачу, шаблонный код включает методы контроллеров, а также представления "Create" (Создание) и "Edit" (Редактирование) с раскрывающимся списком для выбора кафедры. Раскрывающийся список задает свойство внешнего ключа `Course.DepartmentID`, и это все, что нужно Entity Framework для загрузки свойства навигации `Department` с соответствующей сущностью Department. Вы будете использовать этот шаблонный код, немного его изменив, чтобы добавить обработку ошибок и сортировку раскрывающегося списка.

В *CoursesController.cs* удалите методы Create и Edit и замените их следующим кодом:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

После метода HttpPost `Edit` создайте метод, загружающий сведения о кафедре для раскрывающегося списка.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

Метод `PopulateDepartmentsDropDownList` возвращает список всех кафедр, отсортированных по имени, создает коллекцию `SelectList` для раскрывающегося списка и передает ее в представление в `ViewBag`. Этот метод принимает необязательный параметр `selectedDepartment`, позволяющий вызывающему коду указать элемент, который будет выбран при отрисовке раскрывающегося списка. Представление передаст имя "DepartmentID" во вспомогательную функцию тегов `<select>`, после чего ей станет известно, что нужно искать в объекте `ViewBag` коллекцию `SelectList` с именем "DepartmentID".

Метод HttpGet `Create` вызывает метод `PopulateDepartmentsDropDownList` без установки выбранного элемента, так как кафедра для нового курса еще не задана:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

Метод HttpGet `Edit` задает выбранный элемент на основе идентификатора кафедры, который уже назначен редактируемому курсу:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Методы HttpPost для `Create` и `Edit` также содержат код, который задает выбранный элемент, когда они повторно отображают страницу после ошибки. Это гарантирует, что при повторном отображении страницы для вывода сообщения об ошибке сохраняется выбор кафедры.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Добавление .AsNoTrackin в методы Details и Delete

Чтобы оптимизировать производительность страниц "Details" (Сведения) и "Delete" (Удаление) курса, добавьте вызовы `AsNoTracking` в методы `Details` и HttpGet `Delete`.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Изменение представлений курса

Во *Views/Courses/Create.cshtml* добавьте параметр "Select Department" (Выбрать кафедру) в раскрывающийся список **Department** (Кафедра), измените заголовок с **DepartmentID** на **Department** и добавьте сообщение о проверке.

[!code-cshtml[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-35)]

Во *Views/Courses/Edit.cshtml* внесите для поля "Department" (Кафедра) изменение, аналогичное внесенному в *Create.cshtml*.

Кроме того, добавьте во *Views/Courses/Edit.cshtml* поле номера курса перед полем **Title** (Название). Так как номер курса является первичным ключом, он отображается, но не может быть изменен.

[!code-cshtml[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Представление "Edit" (Редактирование) уже содержит скрытое поле (`<input type="hidden">`) для номера курса. Добавление вспомогательной функции тегов `<label>` не устраняет потребность в этом скрытом поле, так как не приводит к включению номера курса в передаваемые данные, когда пользователь нажимает кнопку **Save** (Сохранить) на странице **Edit** (Редактирование).

Во *Views/Courses/Delete.cshtml* добавьте поле номера курса в верхней части страницы и измените идентификатор кафедры на ее имя.

[!code-cshtml[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

Во *Views/Courses/Details.cshtml* внесите изменение, аналогичное внесенному в *Delete.cshtml*.

### <a name="test-the-course-pages"></a>Тестирование страниц курса

Запустите приложение, выберите вкладку **Courses** (Курсы), щелкните **Create New** (Создать) и введите данные для нового курса:

![Страница "Create" (Создание) курса](update-related-data/_static/course-create.png)

Нажмите кнопку **Создать**. Отображается страница индекса курсов, где в список добавлен новый курс. Название кафедры в списке страницы индекса поступает из свойства навигации, показывая, что связь установлена правильно.

Нажмите кнопку **Edit** (Изменить) на странице индекса курсов.

![Страница редактирования курса](update-related-data/_static/course-edit.png)

Измените данные на странице и нажмите кнопку **Save** (Сохранить). Отображается страница индекса курсов с обновленными данными о курсах.

## <a name="add-instructors-edit-page"></a>Добавление страницы редактирования данных о преподавателях

При редактировании записи преподавателя может потребоваться обновить назначенный преподавателю кабинет. Сущность Instructor имеет связь один к нулю или к одному с сущностью OfficeAssignment, что означает, что код должен обрабатывать следующие ситуации:

* Если пользователь сбрасывает назначение кабинета, которое изначально имело некоторое значение, удалите сущность OfficeAssignment.

* Если пользователь вводит значение для назначения кабинета, которое изначально было пустым, создайте сущность OfficeAssignment.

* Если пользователь изменяет значение для назначения кабинета, измените значение в существующей сущности OfficeAssignment.

### <a name="update-the-instructors-controller"></a>Обновление контроллера преподавателей

В *InstructorsController.cs* измените код метода HttpGet `Edit`, чтобы он загружал свойство навигации `OfficeAssignment` сущности Instructor и вызывал `AsNoTracking`:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

Замените метод HttpPost `Edit` следующим кодом, чтобы обрабатывать обновления назначения кабинета:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Этот код выполняет следующее:

* Изменяет имя метода на `EditPost`, так как сигнатура теперь аналогична методу HttpGet `Edit` (атрибут `ActionName` указывает, что URL-адрес `/Edit/` по-прежнему используется).

* Получает текущую сущность Instructor из базы данных, используя безотложную загрузку для свойства навигации `OfficeAssignment`. Это аналогично тому, что вы сделали в методе HttpGet `Edit`.

* Обновляет извлеченную сущность Instructor, используя значения из связывателя модели. Перегрузка `TryUpdateModel` позволяет объявить включаемые свойства. Это защищает от чрезмерной передачи данных, как описано во [втором руководстве](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* Если расположение кабинета отсутствует, задает для свойства Instructor.OfficeAssignment значение null, что приводит к удалению связанной строки в таблице OfficeAssignment.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* Сохраняет изменения в базу данных.

### <a name="update-the-instructor-edit-view"></a>Обновление представления редактирования преподавателя

В конце *Views/Instructors/Edit.cshtml*, перед кнопкой **Save** (Сохранить), добавьте новое поле для редактирования расположения кабинета:

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Запустите приложение, выберите вкладку **Instructors** (Преподаватели), а затем щелкните **Edit** (Изменить) для преподавателя. Измените значение **Office Location** (Расположение кабинета) и нажмите кнопку **Save** (Сохранить).

![Страница редактирования преподавателя](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Добавление курсов на страницу редактирования

Преподаватели могут вести любое число курсов. Теперь вы улучшите страницу редактирования преподавателя, добавив возможность изменять назначения курсов с помощью группы флажков, как показано на следующем снимке экрана:

![Страница редактирования преподавателя с курсами](update-related-data/_static/instructor-edit-courses.png)

Сущности Course и Instructor имеют связь многие ко многим. Для добавления и удаления связей можно добавлять сущности в список объединенного набора сущностей CourseAssignments и удалять их из него.

Пользовательский интерфейс, позволяющий изменить назначенные для преподавателя курсы, представляет собой группу флажков. Отображается флажок для каждого курса в базе данных, а флажки для курсов, назначенных данному преподавателю, установлены. Пользователь может устанавливать и снимать флажки, изменяя назначения курсов. Если бы количество курсов было значительно больше, возможно, вам потребовалось бы использовать другой метод отображения данных в этом представлении, но вы бы использовали тот же самый способ управления сущностью объединения для создания и удаления связей.

### <a name="update-the-instructors-controller"></a>Обновление контроллера преподавателей

Чтобы предоставить данные в представлении для списка флажков, нужно использовать класс модели представления.

Создайте в папке *SchoolViewModels* файл *AssignedCourseData.cs* и замените существующий код следующим:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

В файле *InstructorsController.cs* замените код метода HttpGet `Edit` приведенным ниже кодом. Изменения выделены.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Код добавляет безотложную загрузку для свойства навигации `Courses` и вызывает новый метод `PopulateAssignedCourseData` для предоставления сведений массиву флажков с помощью класса модели представления `AssignedCourseData`.

Код в методе `PopulateAssignedCourseData` считывает все сущности Course, чтобы загрузить список курсов, используя класс модели представления. Для каждого курса код проверяет, существует ли этот курс в свойстве навигации `Courses` преподавателя. Чтобы создать эффективную подстановку при проверке того, назначен ли курс преподавателю, назначаемые курсы помещаются в коллекцию `HashSet`. У курсов, назначенных преподавателю, для свойства `Assigned` задается значение true. Представление будет использовать это свойство, чтобы определить, какие флажки нужно отображать как выбранные. Наконец, список передается в представление в `ViewData`.

Добавьте код, выполняемый, когда пользователь нажимает кнопку **Save** (Сохранить). Замените метод `EditPost` на следующий код и добавьте новый метод, который обновляет свойство навигации `Courses` для сущности Instructor.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

Сигнатура метода теперь отличается от метода HttpGet `Edit`, поэтому имя метода изменяется с `EditPost` обратно на `Edit`.

Так как представление не содержит коллекцию сущностей Course, связыватель модели не может автоматически обновить свойство навигации `CourseAssignments`. Вместо использования связывателя модели для обновления свойства навигации `CourseAssignments` вы делаете это в новом методе `UpdateInstructorCourses`. Поэтому нужно исключить свойство `CourseAssignments` из привязки модели. Это не требует внесения никаких изменений в код, вызывающем `TryUpdateModel`, так как вы используете перегрузку, требующую явного утверждения, а `CourseAssignments` отсутствует в списке включений.

Если никакие флажки не выбраны, код в `UpdateInstructorCourses` инициализирует свойство навигации `CourseAssignments` с использованием пустой коллекции и возвращает следующее:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

После этого код в цикле проходит по всем курсам в базе данных и сравнивает каждый из них с теми, которые сейчас назначены преподавателю, в противоположность тем, которые были выбраны в представлении. Чтобы упростить эффективную подстановку, последние две коллекции хранятся в объектах `HashSet`.

Если флажок для курса был установлен, но курс отсутствует в свойстве навигации `Instructor.CourseAssignments`, этот курс добавляется в коллекцию в свойстве навигации.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Если флажок для курса не был установлен, но курс присутствует в свойстве навигации `Instructor.CourseAssignments`, этот курс удаляется из свойства навигации.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Обновление представлений преподавателя

Во *Views/Instructors/Edit.cshtml* добавьте поле **Courses** (Курсы) с массивом флажков, добавив приведенный ниже код сразу после элементов `div` для поля **Office** (Кабинет) и перед элементом `div` для кнопки **Save** (Сохранить).

<a id="notepad"></a>
> [!NOTE]
> При вставке кода в Visual Studio разрывы строк могут поменяться, нарушая код. Если код выглядит иначе после вставки, нажмите клавиши CTRL + Z один раз для отмены автоматического форматирования. Это исправляет разрывы строк, благодаря чему код приобретает показанный здесь вид. Выравнивать отступы необязательно, однако строки `@:</tr><tr>`, `@:<td>`, `@:</td>` и `@:</tr>` должны находиться на одной строке, как показано здесь. В противном случае возникает ошибка времени выполнения. Выделите блок нового кода и три раза нажмите клавишу TAB, чтобы выровнять его с существующим кодом. Эта проблема исправлена в Visual Studio 2019.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Этот код создает таблицу HTML с тремя столбцами. Каждый столбец содержит флажок, за которым идет заголовок с номером и названием курса. Все флажки имеют одинаковое имя ("selectedCourses"), уведомляющее связывателя модели, что их следует рассматривать как группу. Атрибуту значения для каждого флажка присваивается значение `CourseID`. При передаче страницы связыватель модели передает контроллеру массив, содержащий значения `CourseID` только для выбранных флажков.

При первичной отрисовке для флажков курсов, назначенных преподавателю, заданы атрибуты, в результате чего они отображаются установленными (выбранными).

Запустите приложение, выберите вкладку **Instructors** (Преподаватели), а затем щелкните **Edit** (Изменить) для преподавателя, чтобы открыть страницу **Edit** (Редактирование).

![Страница редактирования преподавателя с курсами](update-related-data/_static/instructor-edit-courses.png)

Измените некоторые назначения курсов и нажмите кнопку "Save" (Сохранить). Вносимые вами изменения отражаются на странице индекса.

> [!NOTE]
> Описываемый здесь подход к редактированию данных курсов для преподавателя эффективен при ограниченном числе курсов. Для коллекций большего размера следовало бы применять другой пользовательский интерфейс и другой метод обновления.

## <a name="update-delete-page"></a>Обновление страницы удаления

В файле *InstructorsController.cs* удалите метод `DeleteConfirmed` и вставьте вместо него приведенный ниже код.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Этот код вносит следующие изменения:

* Выполняет безотложную загрузку для свойства навигации `CourseAssignments`. Вам нужно включить его, иначе EF не будет знать о связанных сущностях `CourseAssignment` и не удалит их. Чтобы избежать необходимости считывать их, можно настроить каскадное удаление в базе данных.

* Если преподаватель, которого требуется удалить, назначен в качестве администратора любой из кафедр, удаляется назначение преподавателя из таких кафедр.

## <a name="add-office-location-and-courses-to-create-page"></a>Добавление расположения кабинета и курсов на страницу создания

В файле *InstructorsController.cs* удалите методы HttpGet и HttpPost `Create` и вставьте вместо них следующий код:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Этот код аналогичен коду для методов `Edit`, за исключением того, что изначально никакие курсы не выбраны. Метод HttpGet `Create` вызывает метод `PopulateAssignedCourseData` не потому, что могут быть выбраны курсы, а чтобы предоставить пустую коллекцию для цикла `foreach` в представлении (в противном случае код представления выдаст исключение пустой ссылки).

Метод HttpPost `Create` добавляет каждый выбранный курс в свойство навигации `CourseAssignments` до того, как выполнить поиск ошибок проверки и добавить нового преподавателя в базу данных. Курсы добавляются даже при наличии ошибок модели, поэтому когда имеются такие ошибки (например, пользователь ввел недопустимую дату) и страница отображается повторно с сообщением об ошибке, все выбранные курсы восстанавливаются автоматически.

Обратите внимание, что для добавления курсов в свойство навигации `CourseAssignments` нужно инициализировать это свойство как пустую коллекцию:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Это можно сделать не только в коде контроллера, но и в модели Instructor, изменив метод получения свойств для автоматического создания коллекции, если она не существует, как показано в следующем примере:

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

При подобном изменении свойства `CourseAssignments` можно удалить код явной инициализации свойства в контроллере.

Во *Views/Instructor/Create.cshtml* добавьте текстовое поле для расположения кабинета и флажки для курсов перед кнопкой "Submit" (Отправить). Как и в случае со страницей редактирования, [исправьте форматирование, если Visual Studio переформатирует код при вставке](#notepad).

[!code-cshtml[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Проверьте работу, запустив приложение и создав преподавателя.

## <a name="handling-transactions"></a>Обработка транзакций

Как описано в [руководстве по CRUD](crud.md), платформа Entity Framework реализует транзакции неявно. Если вам требуется дополнительный контроль, например в сценариях с операциями, выполняемыми в транзакции вне платформы Entity Framework, ознакомьтесь с разделом [Транзакции](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Получите код

[Скачайте или ознакомьтесь с готовым приложением.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Следующие шаги

В этом учебнике рассмотрены следующие задачи.

> [!div class="checklist"]
> * Настройка страниц курсов
> * Добавление страницы редактирования преподавателей
> * Добавление курсов на страницу редактирования
> * Обновление страницы удаления
> * Добавление расположения кабинета и курсов на страницу создания

В следующем учебнике описано, как обрабатывать конфликты параллелизма.

> [!div class="nextstepaction"]
> [Обработка конфликтов параллелизма](concurrency.md)
