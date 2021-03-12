---
title: Учебник. Использование ASP.NET MVC с EF  Core. Создание сложной модели данных
description: В этом руководстве вы добавите дополнительные сущности и связи, а также настроите модель данных, указав правила форматирования, проверки и сопоставления.
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
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: a881ff28d41a272ade559c60efbd884f2a3c4e3e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587792"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="cc4ee-103">Учебник. Использование ASP.NET MVC с EF  Core. Создание сложной модели данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="cc4ee-104">В предыдущих руководствах вы работали с простой моделью данных, состоящей из трех сущностей.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="cc4ee-105">В этом руководстве вы добавите дополнительные сущности и связи, а также настроите модель данных, указав правила форматирования, проверки и сопоставления базы данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="cc4ee-106">По завершении работы классы сущностей сформируют готовую модель данных, приведенную на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Схема сущностей](complex-data-model/_static/diagram.png)

<span data-ttu-id="cc4ee-108">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cc4ee-109">Настройка модели данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-109">Customize the Data model</span></span>
> * <span data-ttu-id="cc4ee-110">Изменения сущности Student</span><span class="sxs-lookup"><span data-stu-id="cc4ee-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="cc4ee-111">Создание сущности Instructor</span><span class="sxs-lookup"><span data-stu-id="cc4ee-111">Create Instructor entity</span></span>
> * <span data-ttu-id="cc4ee-112">Создание сущности OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="cc4ee-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="cc4ee-113">Изменение сущности Course</span><span class="sxs-lookup"><span data-stu-id="cc4ee-113">Modify Course entity</span></span>
> * <span data-ttu-id="cc4ee-114">Создание сущности Department</span><span class="sxs-lookup"><span data-stu-id="cc4ee-114">Create Department entity</span></span>
> * <span data-ttu-id="cc4ee-115">Изменение сущности Enrollment</span><span class="sxs-lookup"><span data-stu-id="cc4ee-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="cc4ee-116">Обновление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-116">Update the database context</span></span>
> * <span data-ttu-id="cc4ee-117">Начальное заполнение базы данных тестовыми данными</span><span class="sxs-lookup"><span data-stu-id="cc4ee-117">Seed database with test data</span></span>
> * <span data-ttu-id="cc4ee-118">Добавление миграции</span><span class="sxs-lookup"><span data-stu-id="cc4ee-118">Add a migration</span></span>
> * <span data-ttu-id="cc4ee-119">Изменение строки подключения</span><span class="sxs-lookup"><span data-stu-id="cc4ee-119">Change the connection string</span></span>
> * <span data-ttu-id="cc4ee-120">Обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc4ee-121">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="cc4ee-121">Prerequisites</span></span>

* [<span data-ttu-id="cc4ee-122">Использование миграций EF Core</span><span class="sxs-lookup"><span data-stu-id="cc4ee-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="cc4ee-123">Настройка модели данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-123">Customize the Data model</span></span>

<span data-ttu-id="cc4ee-124">В этом разделе вы узнаете, как настроить модель данных с помощью атрибутов, которые указывают правила форматирования, проверки и сопоставления базы данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="cc4ee-125">Затем в нескольких последующих разделах вы создаете всю модель данных School целиком, добавив атрибуты к уже созданным классам и создав классы для остальных типов сущностей в модели.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="cc4ee-126">Атрибут DataType</span><span class="sxs-lookup"><span data-stu-id="cc4ee-126">The DataType attribute</span></span>

<span data-ttu-id="cc4ee-127">Сейчас для дат зачисления студентов учащихся все веб-страницы отображают время и дату, хотя для этого поля достаточно одной даты.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="cc4ee-128">Используя атрибуты заметок к данным, вы можете внести в код одно изменение, позволяющее исправить формат отображения в каждом представлении, где отображаются эти данные.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="cc4ee-129">Чтобы рассмотреть соответствующий пример, вы добавите атрибут в свойство `EnrollmentDate` класса `Student`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="cc4ee-130">В *Models/Student.cs* добавьте оператор `using` для пространства имен `System.ComponentModel.DataAnnotations`, а также атрибуты `DataType` и `DisplayFormat` для свойства `EnrollmentDate`, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="cc4ee-131">Атрибут `DataType` позволяет указать тип данных с более точным определением по сравнению со встроенным типом базы данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="cc4ee-132">В этом случае требуется отслеживать только дату, а не дату и время.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="cc4ee-133">В перечислении `DataType` представлено множество типов данных, таких как Date, Time, PhoneNumber, Currency, EmailAddress и других.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="cc4ee-134">Атрибут `DataType` также обеспечивает автоматическое предоставление функций для определенных типов в приложении.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="cc4ee-135">Например, может быть создана ссылка `mailto:` для `DataType.EmailAddress`. Также в браузерах с поддержкой HTML5 может быть предоставлен селектор даты для `DataType.Date`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="cc4ee-136">Атрибут `DataType` создает атрибуты HTML 5 `data-`, которые используются браузерами с поддержкой HTML 5.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="cc4ee-137">Атрибуты `DataType` не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="cc4ee-138">`DataType.Date` не задает формат отображаемой даты.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="cc4ee-139">По умолчанию поле данных отображается с использованием форматов, установленных в CultureInfo сервера.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="cc4ee-140">С помощью атрибута `DisplayFormat` можно явно указать формат даты:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="cc4ee-141">Параметр `ApplyFormatInEditMode` указывает, что формат также должен применяться при отображении значения в текстовом поле для редактирования.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="cc4ee-142">(В некоторых случаях такое поведение нежелательно. Например, в текстовом поле для редактирования денежных значений обычно не требуется отображать символ валюты.)</span><span class="sxs-lookup"><span data-stu-id="cc4ee-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="cc4ee-143">Атрибут `DisplayFormat` можно использовать отдельно, однако чаще всего его рекомендуется применять вместе с атрибутом `DataType`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="cc4ee-144">Атрибут `DataType` передает семантику данных (в отличие от способа их вывода на экран) и дает следующие преимущества по сравнению с `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="cc4ee-145">Поддержка функций HTML5 в браузере (отображение элемента управления календарем, соответствующего языковому стандарту символа валюты, ссылок электронной почты, проверки на стороне клиента и т. д.).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="cc4ee-146">По умолчанию формат отображения данных в браузере определяется в соответствии с установленным языковым стандартом.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="cc4ee-147">Дополнительные сведения см. в документации по [вспомогательной функции тегов \<input>](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="cc4ee-148">Запустите приложение, перейдите на страницу индекса студентов учащихся и обратите внимание, что время для дат зачисления больше не отображается.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="cc4ee-149">Аналогичная ситуация будет наблюдаться в любом представлении, использующем модель Student.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-149">The same will be true for any view that uses the Student model.</span></span>

![Страница указателя учащихся с датами без времени](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="cc4ee-151">Атрибут StringLength</span><span class="sxs-lookup"><span data-stu-id="cc4ee-151">The StringLength attribute</span></span>

<span data-ttu-id="cc4ee-152">С помощью атрибутов также можно указать правила проверки данных и сообщения об ошибках проверки.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="cc4ee-153">Атрибут `StringLength` задает максимальную длину в базе данных и обеспечивает проверку на стороне клиента и на стороне сервера для ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="cc4ee-154">В этом атрибуте также можно указать минимальную длину строки, но это минимальное значение не влияет на схему базы данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="cc4ee-155">Предположим, вы хотите сделать так, чтобы пользователи не вводили больше 50 символов для имени.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="cc4ee-156">Чтобы задать это ограничение, добавьте атрибуты `StringLength` в свойства `LastName` и `FirstMidName`, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="cc4ee-157">Атрибут `StringLength` не запрещает пользователю ввести пробел в качестве имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="cc4ee-158">Атрибут `RegularExpression` можно использовать для применения ограничений к входным данным.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="cc4ee-159">Например, следующий код требует, чтобы первый символ был прописной буквой, а остальные символы были буквенными:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="cc4ee-160">Атрибут `MaxLength` предоставляет функциональность, аналогичную атрибуту `StringLength`, но не обеспечивает проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="cc4ee-161">Модель базы данных изменилась до такой степени, что требуется изменение схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="cc4ee-162">Миграции позволяют обновить схему без потери данных, которые вы могли добавить в базу данных с помощью пользовательского интерфейса приложения.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="cc4ee-163">Сохраните изменения и выполните сборку проекта.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-163">Save your changes and build the project.</span></span> <span data-ttu-id="cc4ee-164">Затем откройте командное окно в папке проекта и введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="cc4ee-165">Команда `migrations add` выдает предупреждение о возможной потере данных, так как это изменение сокращает максимальную длину для двух столбцов.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="cc4ee-166">Функция миграций создает файл с именем *\<timeStamp>_MaxLengthOnNames.cs*.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="cc4ee-167">Он содержит в методе `Up` код, который обновит базу данных в соответствии с текущей моделью данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="cc4ee-168">Команда `database update` запустила этот код.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="cc4ee-169">Метка времени, добавленная в качестве префикса к имени файла миграций, используется платформой Entity Framework для упорядочения миграций.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="cc4ee-170">Вы можете создать несколько миграций перед выполнением команды update-database, после чего все миграции применяются в порядке их создания.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="cc4ee-171">Запустите приложение, выберите вкладку **Students** (Учащиеся), щелкните **Create New** (Создать) и попробуйте ввести любое имя длиннее 50 символов.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="cc4ee-172">Приложение должно отобразить ошибку.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="cc4ee-173">Атрибут Column</span><span class="sxs-lookup"><span data-stu-id="cc4ee-173">The Column attribute</span></span>

<span data-ttu-id="cc4ee-174">Вы также можете использовать атрибуты, чтобы управлять сопоставлением классов и свойств с базой данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="cc4ee-175">Предположим, что вы использовали имя `FirstMidName` для поля имени, так как это поле также может содержать отчество.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="cc4ee-176">Но вам нужно, чтобы столбец базы данных назывался `FirstName`, так как к этому имени привыкли пользователи, которые будут составлять нерегламентированные запросы к базе данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="cc4ee-177">Чтобы выполнить это сопоставление, можно использовать атрибут `Column`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="cc4ee-178">Атрибут `Column` указывает, что при создании базы данных столбец таблицы `Student`, сопоставляемый со свойством `FirstMidName`, будет называться `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="cc4ee-179">Другими словами, когда ваш код ссылается на `Student.FirstMidName`, данные будут браться из столбца `FirstName` таблицы `Student` или обновляться в нем.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="cc4ee-180">Если не указать имена столбцов, им назначается имя, совпадающее с именем свойства.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="cc4ee-181">В файле *Student.cs* добавьте оператор `using` для `System.ComponentModel.DataAnnotations.Schema` и атрибут имени столбца в свойство `FirstMidName`, как показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="cc4ee-182">Добавление атрибута `Column` изменяет модель для поддержки `SchoolContext`, поэтому она не будет соответствовать базе данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="cc4ee-183">Сохраните изменения и выполните сборку проекта.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-183">Save your changes and build the project.</span></span> <span data-ttu-id="cc4ee-184">Затем откройте командное окно в папке проекта и введите следующие команды, чтобы создать другую миграцию:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="cc4ee-185">В **обозревателе объектов SQL Server** откройте конструктор таблиц учащихся, дважды щелкнув таблицу **Student** (Учащийся).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Таблица учащихся в SSOX после миграций](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="cc4ee-187">До применения двух первых миграций столбцы имен имели тип nvarchar(MAX).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="cc4ee-188">Теперь они относятся к типу nvarchar(50), а имя столбца изменилось с FirstMidName на FirstName.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="cc4ee-189">Если попытаться выполнить компиляцию до создания всех классов сущностей в следующих разделах, могут возникнуть ошибки компилятора.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="cc4ee-190">Изменения сущности Student</span><span class="sxs-lookup"><span data-stu-id="cc4ee-190">Changes to Student entity</span></span>

![Сущность Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="cc4ee-192">В *Models/Student.cs* замените добавленный ранее код на приведенный ниже.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="cc4ee-193">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="cc4ee-194">Атрибут Required</span><span class="sxs-lookup"><span data-stu-id="cc4ee-194">The Required attribute</span></span>

<span data-ttu-id="cc4ee-195">Атрибут `Required` делает свойства имен обязательными полями.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="cc4ee-196">Атрибут `Required` не нужен для типов, не допускающих значения null, например для типов значений (DateTime, int, double, float и т. д.).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="cc4ee-197">Типы, которые не могут принимать значение null, автоматически обрабатываются как обязательные поля.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="cc4ee-198">Для применения `MinimumLength` нужно использовать атрибут `Required` с `MinimumLength`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="cc4ee-199">Атрибут Display</span><span class="sxs-lookup"><span data-stu-id="cc4ee-199">The Display attribute</span></span>

<span data-ttu-id="cc4ee-200">Атрибут `Display` указывает, что заголовки для текстовых полей должны иметь вид "First Name" (Имя), "Last Name" (Фамилия), "Full Name" (Полное имя) и "Enrollment Date" (Дата зачисления) вместо имени свойства в каждом экземпляре (в котором не используется пробел для разделения слов).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="cc4ee-201">Вычисляемое свойство FullName</span><span class="sxs-lookup"><span data-stu-id="cc4ee-201">The FullName calculated property</span></span>

<span data-ttu-id="cc4ee-202">`FullName` — это вычисляемое свойство, которое возвращает значение, созданное путем объединения двух других свойств.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="cc4ee-203">Поэтому оно имеет только метод доступа get, и в базе данных не будет создан столбец `FullName`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="cc4ee-204">Создание сущности Instructor</span><span class="sxs-lookup"><span data-stu-id="cc4ee-204">Create Instructor entity</span></span>

![Сущность Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="cc4ee-206">Создайте файл *Models/Instructor.cs*, заменив код шаблона следующим:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="cc4ee-207">Обратите внимание, что некоторые свойства являются одинаковыми в сущностях Student и Instructor.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="cc4ee-208">В руководстве по [реализации наследования](inheritance.md) далее в этой серии вы выполните рефакторинг данного кода, чтобы устранить избыточность.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="cc4ee-209">Несколько атрибутов можно расположить на одной строке, поэтому записать атрибуты `HireDate` можно следующим образом:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="cc4ee-210">Свойства навигации CourseAssignments и OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="cc4ee-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="cc4ee-211">`CourseAssignments` и `OfficeAssignment` — это свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="cc4ee-212">Преподаватель может проводить любое количество курсов, поэтому `CourseAssignments` определен как коллекция.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="cc4ee-213">Если свойство навигации может содержать несколько сущностей, оно должно иметь тип списка, допускающий добавление, удаление и обновление записей.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="cc4ee-214">Вы можете указать тип `ICollection<T>` либо, например, тип `List<T>` или `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="cc4ee-215">Если указан тип `ICollection<T>`, платформа EF по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="cc4ee-216">Причина того, что это сущности `CourseAssignment`, описана ниже в разделе о связях многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="cc4ee-217">Бизнес-правила университета Contoso указывают, что преподаватель может иметь не более одного кабинета, поэтому свойство `OfficeAssignment` содержит отдельную сущность OfficeAssignment (которая может иметь значение null, если кабинет не назначен).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="cc4ee-218">Создание сущности OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="cc4ee-218">Create OfficeAssignment entity</span></span>

![Сущность OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="cc4ee-220">Создайте файл *Models/OfficeAssignment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="cc4ee-221">Атрибут Key</span><span class="sxs-lookup"><span data-stu-id="cc4ee-221">The Key attribute</span></span>

<span data-ttu-id="cc4ee-222">Между сущностями Instructor и OfficeAssignment действует связь один к нулю или к одному.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="cc4ee-223">Назначение кабинета существует только в связи с преподавателем, которому оно назначено, поэтому его первичный ключ также является внешним ключом для сущности Instructor.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="cc4ee-224">Однако Entity Framework не распознает InstructorID в качестве первичного ключа этой сущности автоматически, так как ее имя не соответствует соглашению об именовании ID или classnameID.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="cc4ee-225">Таким образом, атрибут `Key` используется для определения ее в качестве ключа:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="cc4ee-226">Атрибут `Key` также можно использовать, если сущность имеет собственный первичный ключ, но нужно задать для свойства имя, отличное от classnameID или ID.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="cc4ee-227">По умолчанию EF считает ключ созданным не базой данных, так как столбец предназначен для идентифицирующего отношения.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="cc4ee-228">Свойство навигации Instructor</span><span class="sxs-lookup"><span data-stu-id="cc4ee-228">The Instructor navigation property</span></span>

<span data-ttu-id="cc4ee-229">Сущность Instructor имеет свойство навигации `OfficeAssignment`, допускающее значение null (так как у преподавателя может не быть назначения кабинета), а сущность OfficeAssignment имеет свойство навигации `Instructor`, не допускающее значение null (так как назначение кабинета не может существовать без преподавателя — `InstructorID` не допускает значение null).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="cc4ee-230">Когда сущность Instructor имеет связанную сущность OfficeAssignment, каждая из них имеет ссылку на другую в своем свойстве навигации.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="cc4ee-231">Можно поместить атрибут `[Required]` в свойство навигации Instructor, чтобы указать, что должен присутствовать связанный преподаватель, однако это необязательно, так как внешний ключ `InstructorID` (который также является ключом для этой таблицы) не допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="cc4ee-232">Изменение сущности Course</span><span class="sxs-lookup"><span data-stu-id="cc4ee-232">Modify Course entity</span></span>

![Сущность Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="cc4ee-234">В *Models/Course.cs* замените добавленный ранее код на приведенный ниже.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="cc4ee-235">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="cc4ee-236">Сущность курса имеет свойство внешнего ключа `DepartmentID`, указывающее на связанную сущность Department, а также она имеет свойство навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="cc4ee-237">Платформа Entity Framework не требует добавлять свойство внешнего ключа в модель данных при наличии свойства навигации для связанной сущности.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="cc4ee-238">EF автоматически создает внешние ключи в базе данных по мере необходимости, а также создает для них [теневые свойства](/ef/core/modeling/shadow-properties).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="cc4ee-239">Однако наличие внешнего ключа в модели данных позволяет сделать обновления проще и эффективнее.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="cc4ee-240">Например, при извлечении сущности Course для редактирования сущность Department имеет значение null, если вы ее не загружаете. Таким образом, при обновлении сущности Course вам потребуется сначала получить сущность Department.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="cc4ee-241">Если свойство внешнего ключа `DepartmentID` включено в модель данных, получать сущность Department перед обновлением не нужно.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="cc4ee-242">Атрибут DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="cc4ee-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="cc4ee-243">Атрибут `DatabaseGenerated` с параметром `None` в свойстве `CourseID` указывает, что значения первичного ключа предоставлены пользователем, а не созданы базой данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="cc4ee-244">По умолчанию Entity Framework предполагает, что значения первичного ключа созданы базой данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="cc4ee-245">Именно это и требуется для большинства сценариев.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="cc4ee-246">Однако для сущностей Course вы будете использовать определяемый пользователем номер курса, например серия 1000 для одной кафедры, серия 2000 для другой и так далее.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="cc4ee-247">Атрибут `DatabaseGenerated` также можно использовать для создания значения по умолчанию, как в случае, когда столбцы базы данных используются для записи даты, когда строка была создана или обновлена.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="cc4ee-248">Дополнительные сведения см. в разделе [Созданные свойства](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="cc4ee-249">Свойства внешнего ключа и навигации</span><span class="sxs-lookup"><span data-stu-id="cc4ee-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="cc4ee-250">Свойства внешнего ключа (FK) и свойства навигации в сущности Course отражают следующие связи:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="cc4ee-251">Курс назначается одной кафедре, поэтому по указанным выше причинам имеется внешний ключ `DepartmentID` и свойство навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="cc4ee-252">На курс может быть зачислено любое количество учащихся, поэтому свойство навигации `Enrollments` является коллекцией:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="cc4ee-253">Курс могут вести несколько преподавателей, поэтому свойство навигации `CourseAssignments` является коллекцией (тип `CourseAssignment` описан [ниже](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="cc4ee-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="cc4ee-254">Создание сущности Department</span><span class="sxs-lookup"><span data-stu-id="cc4ee-254">Create Department entity</span></span>

![Сущность Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="cc4ee-256">Создайте файл *Models/Department.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="cc4ee-257">Атрибут Column</span><span class="sxs-lookup"><span data-stu-id="cc4ee-257">The Column attribute</span></span>

<span data-ttu-id="cc4ee-258">Ранее атрибут `Column` использовался, чтобы изменить сопоставление имени столбца.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="cc4ee-259">В коде для сущности Department атрибут `Column` используется для изменения сопоставления типов данных SQL, поэтому столбец будет определяться с помощью типа money SQL Server в базе данных:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="cc4ee-260">Сопоставление столбцов обычно не требуется, так как Entity Framework выбирает подходящий тип данных SQL Server на основе типа среды CLR, определяемого вами для свойства.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="cc4ee-261">Тип `decimal` среды CLR сопоставляется с типом `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="cc4ee-262">Но в этом случае вы знаете, что столбец будет содержать суммы в валюте, хотя для этого лучше подходит тип данных money.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="cc4ee-263">Свойства внешнего ключа и навигации</span><span class="sxs-lookup"><span data-stu-id="cc4ee-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="cc4ee-264">Свойства внешнего ключа и навигации отражают следующие связи:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="cc4ee-265">Кафедра может иметь или не иметь администратора, и администратор всегда является преподавателем.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="cc4ee-266">Поэтому `InstructorID` свойство включается в качестве внешнего ключа в сущность Instructor, а после `int` обозначения типа добавляется знак вопроса, указывающий, что свойство допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="cc4ee-267">Свойство навигации называется `Administrator`, но содержит сущность Instructor:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="cc4ee-268">Кафедра может иметь несколько курсов, поэтому доступно свойство навигации Courses:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="cc4ee-269">По соглашению Entity Framework разрешает каскадное удаление для внешних ключей, не допускающих значение null, и связей многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="cc4ee-270">Это может привести к циклическим правилам каскадного удаления, которые вызывают исключение при попытке добавить миграцию.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="cc4ee-271">Например, если вы не определили свойство Department.InstructorID как допускающее значение NULL, EF настраивает правило каскадного удаления для удаления кафедры при удалении преподавателя, что вам не нужно.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="cc4ee-272">Если бизнес-правила требуют, чтобы свойство `InstructorID` не допускало значение null, используйте следующий оператор текучего API, чтобы отключить каскадное удаление для этой связи:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="cc4ee-273">Изменение сущности Enrollment</span><span class="sxs-lookup"><span data-stu-id="cc4ee-273">Modify Enrollment entity</span></span>

![Сущность Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="cc4ee-275">В *Models/Enrollment.cs* замените добавленный ранее код на приведенный ниже:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="cc4ee-276">Свойства внешнего ключа и навигации</span><span class="sxs-lookup"><span data-stu-id="cc4ee-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="cc4ee-277">Свойства внешнего ключа и навигации отражают следующие связи:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="cc4ee-278">Запись зачисления предназначена для одного курса, поэтому доступно свойство первичного ключа `CourseID` и свойство навигации `Course`:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="cc4ee-279">Запись зачисления предназначена для одного учащегося, поэтому доступно свойство первичного ключа `StudentID` и свойство навигации `Student`:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="cc4ee-280">Связи "многие ко многим"</span><span class="sxs-lookup"><span data-stu-id="cc4ee-280">Many-to-Many relationships</span></span>

<span data-ttu-id="cc4ee-281">Между сущностями Student и Course имеется связь многие ко многим, а сущность Enrollment выступает в качестве таблицы соединения многие ко многим *с полезными данными* в базе данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="cc4ee-282">Фраза "с полезными данными" означает, что таблица Enrollment содержит дополнительные данные, кроме внешних ключей для присоединяемых таблиц (в данном случае — первичный ключ и свойство Grade).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="cc4ee-283">На следующем рисунке показано, как выглядят эти связи на схеме сущностей.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="cc4ee-284">(Эта схема была создана с помощью Entity Framework Power Tools для EF 6.x. Создание схемы не является частью руководства, оно просто используется здесь в качестве примера.)</span><span class="sxs-lookup"><span data-stu-id="cc4ee-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Связь многие ко многим между Student и Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="cc4ee-286">Каждая линия связи имеет 1 на одном конце и звездочку (\*) на другом, указывая характер один ко многим.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="cc4ee-287">Если таблица Enrollment не включала в себя сведения об оценках, ей потребуется содержать всего два внешних ключа (CourseID и StudentID).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="cc4ee-288">В данном случае это будет таблица соединения многие ко многим без полезных данных (ее также называют чистой таблицей соединения) в базе данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="cc4ee-289">Между сущностями Instructor и Course действует связь многие ко многим, и следующим шагом является создание класса сущности, выступающего в качестве таблицы соединения без полезных данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="cc4ee-290">(EF 6.x поддерживает неявные таблицы соединения для связей многие ко многим, но EF Core — нет.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="cc4ee-291">Дополнительные сведения см. в [обсуждении в репозитории EF Core на сайте GitHub](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="cc4ee-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="cc4ee-292">Сущность CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="cc4ee-292">The CourseAssignment entity</span></span>

![Сущность CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="cc4ee-294">Создайте файл *Models/CourseAssignment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="cc4ee-295">Имена для сущностей соединения</span><span class="sxs-lookup"><span data-stu-id="cc4ee-295">Join entity names</span></span>

<span data-ttu-id="cc4ee-296">В базе данных для связи многие ко многим между Instructor и Courses нужна таблица соединения, которая должна быть представлена набором сущностей.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="cc4ee-297">Обычно для сущности соединения используется имя `EntityName1EntityName2`, которое в данном случае будет иметь значение `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="cc4ee-298">Однако рекомендуется выбрать имя, которое описывает эту связь.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="cc4ee-299">Модели данных создаются простыми и разрастаются, а соединения без полезных данных часто начинают включать эти данные позднее.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="cc4ee-300">Если вначале задать описательное имя сущности, его не потребуется менять позднее.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="cc4ee-301">Оптимально, если сущность соединения имеет собственное естественное имя (возможно, из одного слова) в бизнес-среде.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="cc4ee-302">Например, Books и Customers можно связать через Ratings.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="cc4ee-303">Для этой связи `CourseAssignment` подходит лучше, чем `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="cc4ee-304">Составной ключ</span><span class="sxs-lookup"><span data-stu-id="cc4ee-304">Composite key</span></span>

<span data-ttu-id="cc4ee-305">Так как внешние ключи не допускают значение null и совместно однозначно определяют каждую строку таблицы, отдельный первичный ключ не требуется.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="cc4ee-306">Свойства *InstructorID* и *CourseID* должны выступать в качестве составного первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="cc4ee-307">Единственным способом указать составные первичные ключи для EF является *текучий API* (с помощью атрибутов это сделать невозможно).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="cc4ee-308">Настройка составного первичного ключа описана в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="cc4ee-309">Составной ключ позволяет использовать несколько строк для одного курса и несколько строк для одного преподавателя, а также не позволяет использовать несколько строк для одного преподавателя и курса.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="cc4ee-310">Сущность соединения `Enrollment` определяет собственный первичный ключ, поэтому подобные дубликаты возможны.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="cc4ee-311">Для предотвращения таких повторяющихся значений добавьте уникальный индекс для полей внешнего ключа или настройте `Enrollment` с первичным составным ключом аналогично `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="cc4ee-312">Дополнительные сведения см. в разделе [Индексы](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="cc4ee-313">Обновление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-313">Update the database context</span></span>

<span data-ttu-id="cc4ee-314">Добавьте выделенный ниже код в файл *Data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="cc4ee-315">Этот код добавляет новые сущности и настраивает составной первичный ключ сущности CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="cc4ee-316">Сведения об альтернативе текучему API</span><span class="sxs-lookup"><span data-stu-id="cc4ee-316">About a fluent API alternative</span></span>

<span data-ttu-id="cc4ee-317">Код в предыдущем методе `OnModelCreating` класса `DbContext` использует для настройки поведения EF *текучий API*.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="cc4ee-318">Этот API называется "текучим", так как часто используется для объединения серии вызовов методов в один оператор, как показано в этом примере из [документации по EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span><span class="sxs-lookup"><span data-stu-id="cc4ee-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="cc4ee-319">В этом руководстве текучий API используется только для сопоставления базы данных, которое невозможно выполнить с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="cc4ee-320">Однако текучий API позволяет задать большинство правил форматирования, проверки и сопоставления, которые можно указать с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="cc4ee-321">Некоторые атрибуты, такие как `MinimumLength`, невозможно применить с текучим API.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="cc4ee-322">Как упоминалось ранее, `MinimumLength` не изменяет схему, а лишь применяет правило проверки на стороне клиента и сервера.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="cc4ee-323">Некоторые разработчики предпочитают использовать текучий API монопольно, чтобы оставить свои классы сущностей "чистыми".</span><span class="sxs-lookup"><span data-stu-id="cc4ee-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="cc4ee-324">Атрибуты и текучий API можно смешивать, и существует несколько конфигураций, которые можно реализовать только с помощью текучего API. На практике рекомендуется выбрать один из этих двух подходов и использовать его максимально согласованно.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="cc4ee-325">Если вы используете оба, обратите внимание, что при любом конфликте текучий API переопределяет атрибуты.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="cc4ee-326">Дополнительные сведения о сравнении атрибутов и текучего API см. в разделе [Методы конфигурации](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="cc4ee-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="cc4ee-327">Схема сущностей, показывающая связи</span><span class="sxs-lookup"><span data-stu-id="cc4ee-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="cc4ee-328">Ниже показана схема, создаваемая средствами Entity Framework Power Tools для завершенной модели School.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Схема сущностей](complex-data-model/_static/diagram.png)

<span data-ttu-id="cc4ee-330">Кроме линий связей один ко многим (1 к \*), здесь можно видеть линию связи один к нулю или к одному (1 к 0..1) между сущностями Instructor и OfficeAssignment, а также линию связи нуль или один ко многим (0..1 to \*) между сущностями Instructor и Department.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="cc4ee-331">Начальное заполнение базы данных тестовыми данными</span><span class="sxs-lookup"><span data-stu-id="cc4ee-331">Seed database with test data</span></span>

<span data-ttu-id="cc4ee-332">Замените код в файле *Data/DbInitializer.cs* на приведенный ниже, чтобы предоставить начальные данные для созданных вами сущностей.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="cc4ee-333">Как можно было заметить в первом руководстве, основная часть кода просто создает объекты сущности и загружает демонстрационные данные в свойства для тестирования.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="cc4ee-334">Обратите внимание, как обрабатываются связи многие ко многим: код формирует связи, создавая сущности в наборах сущностей соединения `Enrollments` и `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="cc4ee-335">Добавление миграции</span><span class="sxs-lookup"><span data-stu-id="cc4ee-335">Add a migration</span></span>

<span data-ttu-id="cc4ee-336">Сохраните изменения и выполните сборку проекта.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-336">Save your changes and build the project.</span></span> <span data-ttu-id="cc4ee-337">Затем откройте командное окно в папке проекта и введите команду `migrations add` (команду update-database пока не выполняйте):</span><span class="sxs-lookup"><span data-stu-id="cc4ee-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="cc4ee-338">Вы получаете предупреждение о возможной потере данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="cc4ee-339">Если попытаться выполнить команду `database update` на этом этапе (пока этого делать не нужно), возникнет следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="cc4ee-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="cc4ee-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="cc4ee-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'. (Оператор ALTER TABLE конфликтовал с ограничением FOREIGN KEY "FK_dbo.Course_dbo.Department_DepartmentID". Конфликт возник в столбце "DepartmentID" таблицы "dbo.Department" базы данных "ContosoUniversity".)</span><span class="sxs-lookup"><span data-stu-id="cc4ee-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="cc4ee-342">Иногда при выполнении миграций с существующими данными необходимо вставить данные-заглушки в базу данных для соблюдения ограничений внешнего ключа.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="cc4ee-343">Созданный код в методе `Up` добавляет внешний ключ DepartmentID, не допускающий значение null, в таблицу Course.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="cc4ee-344">Если при запуске кода в таблице Course уже имеются строки, операция `AddColumn` завершается неудачей, так как SQL Server не знает, какое значение поставить в столбце, который не допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="cc4ee-345">В этом учебнике вы запустите миграцию в новую базу данных, но в реальном приложении потребуется обеспечить обработку существующих данных в этой миграции, соответствующий пример приведен ниже.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="cc4ee-346">Чтобы заставить эту миграцию работать с существующими данными, нужно изменить код, чтобы присвоить новому столбцу значение по умолчанию, а также создать кафедру-заглушку с именем "Temp" для использования по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="cc4ee-347">В результате существующие строки Course будут связаны с кафедрой "Temp" после выполнения метода `Up`.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="cc4ee-348">Откройте файл *{метка_времени}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="cc4ee-349">Закомментируйте строку кода, которая добавляет столбец DepartmentID в таблицу Course.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="cc4ee-350">Добавьте выделенный ниже код после кода, создающего таблицу Department:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="cc4ee-351">В реальном приложении вам потребуется написать код или сценарии для добавления строк Department, а также для связи строк Course с новыми строками Department.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="cc4ee-352">После этого кафедра "Temp" и значение по умолчанию в столбце Course.DepartmentID вам больше не понадобятся.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="cc4ee-353">Сохраните изменения и выполните сборку проекта.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="cc4ee-354">Изменение строки подключения</span><span class="sxs-lookup"><span data-stu-id="cc4ee-354">Change the connection string</span></span>

<span data-ttu-id="cc4ee-355">Теперь у вас есть новый код в классе `DbInitializer`, который добавляет начальные данные для новых сущностей в пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="cc4ee-356">Чтобы указать EF создать пустую базу данных, в файле *appsettings.json* измените имя базы данных в строке подключения на ContosoUniversity3 или другое имя, которое вы еще не использовали на компьютере, с которым работаете.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="cc4ee-357">Сохраните изменения в файл *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="cc4ee-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="cc4ee-358">Вместо изменения имени базы данных можно удалить ее.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="cc4ee-359">Воспользуйтесь **обозревателем объектов SQL Server** (SSOX) или командой интерфейса командной строки `database drop`:</span><span class="sxs-lookup"><span data-stu-id="cc4ee-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="cc4ee-360">Обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-360">Update the database</span></span>

<span data-ttu-id="cc4ee-361">После изменения имени базы данных или ее удаления запустите команду `database update` в командном окне, чтобы выполнить миграции.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="cc4ee-362">Запустите приложение, чтобы метод `DbInitializer.Initialize` запустился и заполнил новую базу данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="cc4ee-363">Откройте базу данных в SSOX, как уже делали это раньше, а затем разверните узел **Таблицы**, чтобы увидеть все созданные таблицы.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="cc4ee-364">(Если SSOX уже был открыт, нажмите кнопку **Обновить**.)</span><span class="sxs-lookup"><span data-stu-id="cc4ee-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![Таблицы в SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="cc4ee-366">Запустите приложение, чтобы активировать код инициализатора, заполняющий базу данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="cc4ee-367">Щелкните правой кнопкой мыши таблицу **CourseAssignment** и выберите пункт **Просмотреть данные**, чтобы убедиться в наличии данных.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![Данные CourseAssignment в SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="cc4ee-369">Получите код</span><span class="sxs-lookup"><span data-stu-id="cc4ee-369">Get the code</span></span>

[<span data-ttu-id="cc4ee-370">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="cc4ee-371">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="cc4ee-371">Next steps</span></span>

<span data-ttu-id="cc4ee-372">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cc4ee-373">Настройка модели данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-373">Customized the Data model</span></span>
> * <span data-ttu-id="cc4ee-374">Изменения сущности Student</span><span class="sxs-lookup"><span data-stu-id="cc4ee-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="cc4ee-375">Создание сущности Instructor</span><span class="sxs-lookup"><span data-stu-id="cc4ee-375">Created Instructor entity</span></span>
> * <span data-ttu-id="cc4ee-376">Создание сущности OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="cc4ee-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="cc4ee-377">Изменение сущности Course</span><span class="sxs-lookup"><span data-stu-id="cc4ee-377">Modified Course entity</span></span>
> * <span data-ttu-id="cc4ee-378">Создание сущности Department</span><span class="sxs-lookup"><span data-stu-id="cc4ee-378">Created Department entity</span></span>
> * <span data-ttu-id="cc4ee-379">Изменение сущности Enrollment</span><span class="sxs-lookup"><span data-stu-id="cc4ee-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="cc4ee-380">Обновление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-380">Updated the database context</span></span>
> * <span data-ttu-id="cc4ee-381">Начальное заполнение базы данных тестовыми данными</span><span class="sxs-lookup"><span data-stu-id="cc4ee-381">Seeded database with test data</span></span>
> * <span data-ttu-id="cc4ee-382">Добавление миграции</span><span class="sxs-lookup"><span data-stu-id="cc4ee-382">Added a migration</span></span>
> * <span data-ttu-id="cc4ee-383">Изменение строки подключения</span><span class="sxs-lookup"><span data-stu-id="cc4ee-383">Changed the connection string</span></span>
> * <span data-ttu-id="cc4ee-384">Обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="cc4ee-384">Updated the database</span></span>

<span data-ttu-id="cc4ee-385">В следующем руководстве описано, как получить доступ к связанным данным.</span><span class="sxs-lookup"><span data-stu-id="cc4ee-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="cc4ee-386">Далее: Получение доступа к связанным данным</span><span class="sxs-lookup"><span data-stu-id="cc4ee-386">Next: Access related data</span></span>](read-related-data.md)
