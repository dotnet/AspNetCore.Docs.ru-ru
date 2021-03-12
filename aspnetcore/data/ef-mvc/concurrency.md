---
title: Учебник. Использование ASP.NET Core MVC с EF Core. Обработка параллелизма
description: Это руководство описывает, как обрабатывать конфликты, когда несколько пользователей одновременно изменяют одну сущность.
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
uid: data/ef-mvc/concurrency
ms.openlocfilehash: cc93069899953d04d1df4e79a282c349a163b06e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586778"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="bed3c-103">Учебник. Использование ASP.NET Core MVC с EF Core. Обработка параллелизма</span><span class="sxs-lookup"><span data-stu-id="bed3c-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="bed3c-104">В предыдущих учебниках вы узнали, как обновлять данные.</span><span class="sxs-lookup"><span data-stu-id="bed3c-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="bed3c-105">Это руководство описывает, как обрабатывать конфликты, когда несколько пользователей одновременно изменяют одну сущность.</span><span class="sxs-lookup"><span data-stu-id="bed3c-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="bed3c-106">Вы создадите веб-страницы, которые работают с сущностью Department и обрабатывают ошибки параллелизма.</span><span class="sxs-lookup"><span data-stu-id="bed3c-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="bed3c-107">На следующих рисунках показаны страницы "Edit" (Редактирование) и "Delete" (Удаление), включая некоторые сообщения, которые отображаются при возникновении конфликта параллелизма.</span><span class="sxs-lookup"><span data-stu-id="bed3c-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Страница редактирования кафедры](concurrency/_static/edit-error.png)

![Страница удаления кафедры](concurrency/_static/delete-error.png)

<span data-ttu-id="bed3c-110">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="bed3c-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bed3c-111">Дополнительные сведения о конфликтах параллелизма</span><span class="sxs-lookup"><span data-stu-id="bed3c-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="bed3c-112">Добавление свойства отслеживания</span><span class="sxs-lookup"><span data-stu-id="bed3c-112">Add a tracking property</span></span>
> * <span data-ttu-id="bed3c-113">Создание представлений и контроллера кафедр</span><span class="sxs-lookup"><span data-stu-id="bed3c-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="bed3c-114">Обновление представления указателя</span><span class="sxs-lookup"><span data-stu-id="bed3c-114">Update Index view</span></span>
> * <span data-ttu-id="bed3c-115">Обновление методов редактирования</span><span class="sxs-lookup"><span data-stu-id="bed3c-115">Update Edit methods</span></span>
> * <span data-ttu-id="bed3c-116">Обновление представления редактирования</span><span class="sxs-lookup"><span data-stu-id="bed3c-116">Update Edit view</span></span>
> * <span data-ttu-id="bed3c-117">Тестирование конфликтов параллелизма</span><span class="sxs-lookup"><span data-stu-id="bed3c-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="bed3c-118">Обновление страницы удаления</span><span class="sxs-lookup"><span data-stu-id="bed3c-118">Update the Delete page</span></span>
> * <span data-ttu-id="bed3c-119">Обновление представлений Details и Create</span><span class="sxs-lookup"><span data-stu-id="bed3c-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bed3c-120">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="bed3c-120">Prerequisites</span></span>

* [<span data-ttu-id="bed3c-121">Обновление связанных данных</span><span class="sxs-lookup"><span data-stu-id="bed3c-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="bed3c-122">Конфликты параллелизма</span><span class="sxs-lookup"><span data-stu-id="bed3c-122">Concurrency conflicts</span></span>

<span data-ttu-id="bed3c-123">Конфликт параллелизма возникает, когда один пользователь отображает данные сущности, чтобы изменить их, а другой пользователь обновляет данные той же сущности до того, как изменение первого пользователя будет записано в базу данных.</span><span class="sxs-lookup"><span data-stu-id="bed3c-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="bed3c-124">Если не включить обнаружение таких конфликтов, то пользователь, обновляющий базу данных последним, перезаписывает изменения другого пользователя.</span><span class="sxs-lookup"><span data-stu-id="bed3c-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="bed3c-125">Во многих приложениях такой риск допустим: при небольшом числе пользователей или обновлений, а также в случае, если перезапись некоторых изменений не является критической, стоимость реализации параллелизма может перевесить его преимущества.</span><span class="sxs-lookup"><span data-stu-id="bed3c-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="bed3c-126">В этом случае вам не нужно настраивать приложение для обработки конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="bed3c-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="bed3c-127">Пессимистичный параллелизм (блокировка)</span><span class="sxs-lookup"><span data-stu-id="bed3c-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="bed3c-128">Если приложению нужно предотвратить случайную потерю данных в сценариях параллелизма, одним из способов сделать это являются блокировки базы данных.</span><span class="sxs-lookup"><span data-stu-id="bed3c-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="bed3c-129">Это называется пессимистичным параллелизмом.</span><span class="sxs-lookup"><span data-stu-id="bed3c-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="bed3c-130">Например, перед чтением строки из базы данных вы запрашиваете блокировку для доступа для обновления или только для чтения.</span><span class="sxs-lookup"><span data-stu-id="bed3c-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="bed3c-131">Если заблокировать строку для обновления, другие пользователи не могут заблокировать ее для обновления или только для чтения, так как получат копию данных, которые находятся в процессе изменения.</span><span class="sxs-lookup"><span data-stu-id="bed3c-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="bed3c-132">Если заблокировать строку только для чтения, другие пользователи также могут заблокировать ее только для чтения, но не для обновления.</span><span class="sxs-lookup"><span data-stu-id="bed3c-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="bed3c-133">Управление блокировками имеет недостатки.</span><span class="sxs-lookup"><span data-stu-id="bed3c-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="bed3c-134">Оно может оказаться сложным с точки зрения программирования.</span><span class="sxs-lookup"><span data-stu-id="bed3c-134">It can be complex to program.</span></span> <span data-ttu-id="bed3c-135">Оно требует значительных ресурсов управления базами данных, а также может вызвать проблемы с производительностью по мере увеличения числа пользователей приложения.</span><span class="sxs-lookup"><span data-stu-id="bed3c-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="bed3c-136">Поэтому не все системы управления базами данных поддерживают пессимистичный параллелизм.</span><span class="sxs-lookup"><span data-stu-id="bed3c-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="bed3c-137">В Entity Framework Core нет встроенной поддержки этой функции, и данное руководство не рассказывает, как ее реализовать.</span><span class="sxs-lookup"><span data-stu-id="bed3c-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="bed3c-138">Оптимистическая блокировка</span><span class="sxs-lookup"><span data-stu-id="bed3c-138">Optimistic Concurrency</span></span>

<span data-ttu-id="bed3c-139">Альтернативой пессимистичному параллелизму является оптимистичный параллелизм (оптимистическая блокировка).</span><span class="sxs-lookup"><span data-stu-id="bed3c-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="bed3c-140">Оптимистическая блокировка допускает появление конфликтов параллелизма, а затем обрабатывает их соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="bed3c-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="bed3c-141">Например, Мария посещает страницу изменения кафедры и изменяет бюджет кафедры английской языка с 350 000,00 USD на 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="bed3c-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Изменение бюджета на 0](concurrency/_static/change-budget.png)

<span data-ttu-id="bed3c-143">Прежде чем Мария нажимает кнопку **Save** (Сохранить), Дмитрий заходит на ту же страницу и изменяет значение в поле "Start Date" (Дата начала) с 9/1/2007 на 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="bed3c-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Изменение начальной даты на 2013 г.](concurrency/_static/change-date.png)

<span data-ttu-id="bed3c-145">Сначала Мария нажимает кнопку **Save** (Сохранить) и видит свое изменение, когда браузер возвращается на страницу индекса.</span><span class="sxs-lookup"><span data-stu-id="bed3c-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Бюджет изменен на нуль](concurrency/_static/budget-zero.png)

<span data-ttu-id="bed3c-147">Затем Дмитрий нажимает кнопку **Save** (Сохранить) на странице редактирования, где все еще отображается бюджет 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="bed3c-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="bed3c-148">Дальнейший ход событий определяется порядком обработки конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="bed3c-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="bed3c-149">Некоторые параметры перечислены ниже:</span><span class="sxs-lookup"><span data-stu-id="bed3c-149">Some of the options include the following:</span></span>

* <span data-ttu-id="bed3c-150">Вы можете отслеживать, для какого свойства пользователь изменил и обновил только соответствующие столбцы в базе данных.</span><span class="sxs-lookup"><span data-stu-id="bed3c-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="bed3c-151">В этом примере сценария данные не будут потеряны, так как эти два пользователя обновляли разные свойства.</span><span class="sxs-lookup"><span data-stu-id="bed3c-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="bed3c-152">Когда какой-либо пользователь просмотрит кафедру английского языка в следующий раз, он увидит изменения, внесенные как Марией, так и Дмитрием — дату начала 9/1/2013 и бюджет в нуль долларов США.</span><span class="sxs-lookup"><span data-stu-id="bed3c-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="bed3c-153">Этот метод обновления помогает снизить число конфликтов, которые могут привести к потере данных, но не позволяет избежать такой потери, когда конкурирующие изменения вносятся в одно свойство сущности.</span><span class="sxs-lookup"><span data-stu-id="bed3c-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="bed3c-154">То, работает ли Entity Framework в таком режиме, зависит от того, как вы реализуете код обновления.</span><span class="sxs-lookup"><span data-stu-id="bed3c-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="bed3c-155">В веб-приложении это часто нецелесообразно, так как может потребоваться обрабатывать большой объем состояний, чтобы отслеживать все исходные значения свойств для сущности, а также новые значения.</span><span class="sxs-lookup"><span data-stu-id="bed3c-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="bed3c-156">Обработка большого объема состояний может повлиять на производительность приложения, так как требует ресурсов сервера или должна быть включена непосредственно в веб-страницу (например, в скрытые поля) или файл cookie.</span><span class="sxs-lookup"><span data-stu-id="bed3c-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="bed3c-157">Вы можете позволить изменению Дмитрия перезаписать изменение Марии.</span><span class="sxs-lookup"><span data-stu-id="bed3c-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="bed3c-158">Когда какой-либо пользователь просмотрит кафедру английского языка в следующий раз, он увидит дату 9/1/2013 и восстановленное значение 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="bed3c-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="bed3c-159">Такой подход называется *победой клиента* или *сохранением последнего внесенного изменения*.</span><span class="sxs-lookup"><span data-stu-id="bed3c-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="bed3c-160">(Все значения из клиента имеют приоритет над данными в хранилище.) Как отмечено во введении к этому разделу, если вы не пишете код для обработки параллелизма, она выполняется автоматически.</span><span class="sxs-lookup"><span data-stu-id="bed3c-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="bed3c-161">Вы можете запретить обновление изменения Дмитрия в базе данных.</span><span class="sxs-lookup"><span data-stu-id="bed3c-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="bed3c-162">Как правило, следует отобразить сообщение об ошибке, показать текущее состояние данных и позволить ему повторно применить свои изменения, если они ему нужны.</span><span class="sxs-lookup"><span data-stu-id="bed3c-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="bed3c-163">Это называется *победой хранилища*.</span><span class="sxs-lookup"><span data-stu-id="bed3c-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="bed3c-164">(Значения в хранилище имеют приоритет над данными, передаваемыми клиентом.) В этом руководстве вы реализуете сценарий победы хранилища.</span><span class="sxs-lookup"><span data-stu-id="bed3c-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="bed3c-165">Данный метод гарантирует, что никакие изменения не перезаписываются без оповещения пользователя о случившемся.</span><span class="sxs-lookup"><span data-stu-id="bed3c-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="bed3c-166">Обнаружение конфликтов параллелизма</span><span class="sxs-lookup"><span data-stu-id="bed3c-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="bed3c-167">Конфликты можно разрешать путем обработки исключений `DbConcurrencyException`, выдаваемых Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="bed3c-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="bed3c-168">Чтобы определить, когда именно нужно выдавать исключения, платформа Entity Framework должна быть в состоянии обнаруживать конфликты.</span><span class="sxs-lookup"><span data-stu-id="bed3c-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="bed3c-169">Поэтому нужно соответствующим образом настроить базу данных и модель данных.</span><span class="sxs-lookup"><span data-stu-id="bed3c-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="bed3c-170">Ниже приведены некоторые варианты для реализации обнаружения конфликтов:</span><span class="sxs-lookup"><span data-stu-id="bed3c-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="bed3c-171">Включите в таблицу базы данных столбец отслеживания, который позволяет определять, когда была изменена строка.</span><span class="sxs-lookup"><span data-stu-id="bed3c-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="bed3c-172">Затем можно настроить Entity Framework для включения этого столбца в предложение Where команд SQL Update или Delete.</span><span class="sxs-lookup"><span data-stu-id="bed3c-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="bed3c-173">Типом данных для столбца отслеживания обычно является `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="bed3c-174">Значение `rowversion` является последовательным номером, увеличивающимся при каждом обновлении строки.</span><span class="sxs-lookup"><span data-stu-id="bed3c-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="bed3c-175">В команде Update или Delete предложение Where содержит исходное значение столбца отслеживания (исходную версию строки).</span><span class="sxs-lookup"><span data-stu-id="bed3c-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="bed3c-176">Если обновляемая строка была изменена другим пользователем, значение в столбце `rowversion` отличается от исходного значения, поэтому оператору Update или Delete не удается найти строку для обновления из-за предложения Where.</span><span class="sxs-lookup"><span data-stu-id="bed3c-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="bed3c-177">Когда платформа Entity Framework обнаруживает, что ни одна из строк не была обновлена с помощью команды Update или Delete (то есть число затронутых строк равно нулю), она интерпретирует это как конфликт параллелизма.</span><span class="sxs-lookup"><span data-stu-id="bed3c-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="bed3c-178">Настройте Entity Framework для включения исходного значения каждого столбца таблицы в предложение Where команд Update и Delete.</span><span class="sxs-lookup"><span data-stu-id="bed3c-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="bed3c-179">Как и в случае с первым вариантом, если с момента первого чтения в строке что-либо изменилось, предложение Where не возвращает строку для обновления, что платформа Entity Framework интерпретирует как конфликт параллелизма.</span><span class="sxs-lookup"><span data-stu-id="bed3c-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="bed3c-180">Для таблиц базы данных со множеством столбцов этот подход может привести к очень большим предложениям Where и потребовать обрабатывать большой объем состояний.</span><span class="sxs-lookup"><span data-stu-id="bed3c-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="bed3c-181">Как было указано ранее, обслуживание большого объема состояний может негативно повлиять на производительность приложения.</span><span class="sxs-lookup"><span data-stu-id="bed3c-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="bed3c-182">Поэтому в общем случае данный подход не рекомендуется, кроме того, он не применяется и в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="bed3c-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="bed3c-183">Если вы хотите реализовать этот подход к параллелизму, нужно пометить все свойства, не относящиеся к первичному ключу, в сущности, где требуется отслеживать параллелизм, добавив к ним атрибут `ConcurrencyCheck`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="bed3c-184">Это изменение позволяет Entity Framework включить все столбцы в предложение Where SQL операторов Update и Delete.</span><span class="sxs-lookup"><span data-stu-id="bed3c-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="bed3c-185">В оставшейся части этого руководства вам предстоит добавить свойство отслеживания `rowversion` в сущность Department, создать контроллер и представления, а также проверить правильность работы решения.</span><span class="sxs-lookup"><span data-stu-id="bed3c-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="bed3c-186">Добавление свойства отслеживания</span><span class="sxs-lookup"><span data-stu-id="bed3c-186">Add a tracking property</span></span>

<span data-ttu-id="bed3c-187">В *Models/Department.cs* добавьте свойство отслеживания RowVersion:</span><span class="sxs-lookup"><span data-stu-id="bed3c-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="bed3c-188">Атрибут `Timestamp` указывает, что этот столбец будет включен в предложение Where команд Update и Delete, отправляемых в базу данных.</span><span class="sxs-lookup"><span data-stu-id="bed3c-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="bed3c-189">Этот атрибут называется `Timestamp`, так как предыдущие версии SQL Server использовали тип данных `timestamp` SQL, пока его не сменил `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="bed3c-190">Тип .NET для `rowversion` — это массив байтов.</span><span class="sxs-lookup"><span data-stu-id="bed3c-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="bed3c-191">Если вы предпочитаете использовать текучий API, можно воспользоваться методом `IsConcurrencyToken` (в *Data/SchoolContext.cs*), чтобы указать свойство отслеживания, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="bed3c-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="bed3c-192">Добавив свойство, вы изменили модель базы данных, поэтому нужно выполнить еще одну миграцию.</span><span class="sxs-lookup"><span data-stu-id="bed3c-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="bed3c-193">Сохраните изменения, выполните сборку проекта и введите в командном окне следующие команды:</span><span class="sxs-lookup"><span data-stu-id="bed3c-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="bed3c-194">Создание представлений и контроллера кафедр</span><span class="sxs-lookup"><span data-stu-id="bed3c-194">Create Departments controller and views</span></span>

<span data-ttu-id="bed3c-195">Сформируйте шаблоны для контроллера кафедр и представлений, как делали это раньше для учащихся, курсов и преподавателей.</span><span class="sxs-lookup"><span data-stu-id="bed3c-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Формирование шаблона кафедр](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="bed3c-197">В файле *DepartmentsController.cs* измените все четыре экземпляра "FirstMidName" на "FullName", чтобы раскрывающиеся списки для администратора кафедры содержали полное имя преподавателя, а не только фамилию.</span><span class="sxs-lookup"><span data-stu-id="bed3c-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="bed3c-198">Обновление представления указателя</span><span class="sxs-lookup"><span data-stu-id="bed3c-198">Update Index view</span></span>

<span data-ttu-id="bed3c-199">Подсистема формирования шаблонов создала столбец RowVersion для представления индекса, однако это поле не должно отображаться.</span><span class="sxs-lookup"><span data-stu-id="bed3c-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="bed3c-200">Замените код в файле *Views/Departments/Index.cshtml* на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="bed3c-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="bed3c-201">Он изменяет заголовок на "Departments" (Кафедры), удаляет столбец RowVersion и отображает администратору имя и фамилию, а не только имя.</span><span class="sxs-lookup"><span data-stu-id="bed3c-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="bed3c-202">Обновление методов редактирования</span><span class="sxs-lookup"><span data-stu-id="bed3c-202">Update Edit methods</span></span>

<span data-ttu-id="bed3c-203">Добавьте `AsNoTracking` в оба метода — HttpGet `Edit` и `Details`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="bed3c-204">В методе HttpGet `Edit` добавьте безотложную загрузку для администратора.</span><span class="sxs-lookup"><span data-stu-id="bed3c-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="bed3c-205">Замените существующий код в методе HttpPost`Edit` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="bed3c-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="bed3c-206">Код начинает пытаться считать кафедру для обновления.</span><span class="sxs-lookup"><span data-stu-id="bed3c-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="bed3c-207">Если метод `FirstOrDefaultAsync` возвращает значение null, кафедра была удалена другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="bed3c-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="bed3c-208">В этом случае код использует переданные значения из формы для создания сущности кафедры, чтобы страницу "Edit" (Редактирование) можно было отобразить повторно с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="bed3c-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="bed3c-209">Кроме того, повторно создать сущность кафедры не нужно, если вы выводите только сообщение об ошибке без повторного отображения полей кафедры.</span><span class="sxs-lookup"><span data-stu-id="bed3c-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="bed3c-210">Представление сохраняет исходное значение `RowVersion` в скрытом поле, а данный метод получает это значение в параметре `rowVersion`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="bed3c-211">Перед вызовом `SaveChanges` нужно поместить это исходное значение свойства `RowVersion` в коллекцию `OriginalValues` для сущности.</span><span class="sxs-lookup"><span data-stu-id="bed3c-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="bed3c-212">Когда позднее Entity Framework создает команду SQL UPDATE, она будет содержать предложение WHERE, которое ищет строку с исходным значением `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="bed3c-213">Если команда UPDATE не затрагивает никакие строки (нет строк, имеющих исходное значение `RowVersion`), Entity Framework выдает исключение `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="bed3c-214">Код в блоке catch для этого исключения возвращает затронутую сущность Department, имеющую обновленные значения из свойства `Entries` в объекте исключения.</span><span class="sxs-lookup"><span data-stu-id="bed3c-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="bed3c-215">Коллекция `Entries` будет иметь всего один объект `EntityEntry`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="bed3c-216">Вы можете использовать его для получения новых значений, введенных пользователем, и текущих значений в базе данных.</span><span class="sxs-lookup"><span data-stu-id="bed3c-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="bed3c-217">Код добавляет настраиваемое сообщение об ошибке для каждого столбца, для которого значения базы данных отличаются от введенных пользователем на странице "Edit" (Редактирование) (здесь для краткости показано всего одно поле).</span><span class="sxs-lookup"><span data-stu-id="bed3c-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="bed3c-218">Наконец, код задает для `RowVersion` объекта `departmentToUpdate` новое значение, полученное из базы данных.</span><span class="sxs-lookup"><span data-stu-id="bed3c-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="bed3c-219">Это новое значение `RowVersion` будет сохранено в скрытом поле при повторном отображении страницы "Edit" (Редактирование). Когда пользователь в следующий раз нажимает кнопку **Save** (Сохранить), перехватываются только те ошибки параллелизма, которые возникли с момента повторного отображения страницы "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="bed3c-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="bed3c-220">Оператор `ModelState.Remove` является обязательным, так как `ModelState` имеет старое значение `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="bed3c-221">На представлении значение `ModelState` для поля имеет приоритет над значениями свойств модели, если они присутствуют вместе.</span><span class="sxs-lookup"><span data-stu-id="bed3c-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="bed3c-222">Обновление представления редактирования</span><span class="sxs-lookup"><span data-stu-id="bed3c-222">Update Edit view</span></span>

<span data-ttu-id="bed3c-223">Внесите следующие изменения в файл *Views/Departments/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bed3c-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="bed3c-224">Добавьте скрытое поле для сохранения значения свойства `RowVersion` сразу после скрытого поля для свойства `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="bed3c-225">Добавьте пункт "Select Administrator" (Выбрать администратора) в раскрывающийся список.</span><span class="sxs-lookup"><span data-stu-id="bed3c-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="bed3c-226">Тестирование конфликтов параллелизма</span><span class="sxs-lookup"><span data-stu-id="bed3c-226">Test concurrency conflicts</span></span>

<span data-ttu-id="bed3c-227">Запустите приложение и перейдите на страницу индекса кафедр.</span><span class="sxs-lookup"><span data-stu-id="bed3c-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="bed3c-228">Щелкните правой кнопкой мыши гиперссылку **Edit** (Изменить) для кафедры английского языка и выберите пункт **Открыть на новой вкладке**, а затем щелкните гиперссылку **Edit** (Изменить) для этой кафедры.</span><span class="sxs-lookup"><span data-stu-id="bed3c-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="bed3c-229">Теперь на обеих вкладках браузера отображаются одинаковые сведения.</span><span class="sxs-lookup"><span data-stu-id="bed3c-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="bed3c-230">Измените поле на первой вкладке браузера и нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="bed3c-230">Change a field in the first browser tab and click **Save**.</span></span>

![Страница "Edit" (Редактирование) кафедры 1 после изменения](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="bed3c-232">В браузере отображается страница индекса с измененным значением.</span><span class="sxs-lookup"><span data-stu-id="bed3c-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="bed3c-233">Измените поле на второй вкладке браузера.</span><span class="sxs-lookup"><span data-stu-id="bed3c-233">Change a field in the second browser tab.</span></span>

![Страница "Edit" (Редактирование) кафедры 2 после изменения](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="bed3c-235">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="bed3c-235">Click **Save**.</span></span> <span data-ttu-id="bed3c-236">Отображается сообщение об ошибке:</span><span class="sxs-lookup"><span data-stu-id="bed3c-236">You see an error message:</span></span>

![Сообщение об ошибке для страницы "Edit" (Редактирование) кафедры](concurrency/_static/edit-error.png)

<span data-ttu-id="bed3c-238">Снова нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="bed3c-238">Click **Save** again.</span></span> <span data-ttu-id="bed3c-239">Сохраняется значение, введенное на второй вкладке браузера.</span><span class="sxs-lookup"><span data-stu-id="bed3c-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="bed3c-240">Сохраненные значения отображаются при открытии страницы индекса.</span><span class="sxs-lookup"><span data-stu-id="bed3c-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="bed3c-241">Обновление страницы удаления</span><span class="sxs-lookup"><span data-stu-id="bed3c-241">Update the Delete page</span></span>

<span data-ttu-id="bed3c-242">Для страницы "Delete" (Удаление) платформа Entity Framework обнаруживает конфликты параллелизма, вызванные схожим изменением кафедры.</span><span class="sxs-lookup"><span data-stu-id="bed3c-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="bed3c-243">Когда метод HttpGet `Delete` отображает представление подтверждения, оно содержит исходное значение `RowVersion` в скрытом поле.</span><span class="sxs-lookup"><span data-stu-id="bed3c-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="bed3c-244">Затем это значение становится доступным для метода HttpPost `Delete`, вызываемого при подтверждении удаления пользователем.</span><span class="sxs-lookup"><span data-stu-id="bed3c-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="bed3c-245">Когда Entity Framework создает команду SQL DELETE, она включает предложение WHERE с исходным значением `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="bed3c-246">Если команда не затрагивает ни одной строки (подразумевается изменение строки после отображения страницы подтверждения удаления), возникает исключение параллелизма и вызывается метод HttpGet `Delete`, у которого для флага ошибки установлено значение true, чтобы повторно отобразить страницу подтверждения с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="bed3c-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="bed3c-247">Отсутствие затронутых строк также может быть вызвано тем, что строка была удалена другим пользователем, и в этом случае сообщение об ошибке не отображается.</span><span class="sxs-lookup"><span data-stu-id="bed3c-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="bed3c-248">Обновление методов Delete в контроллере кафедр</span><span class="sxs-lookup"><span data-stu-id="bed3c-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="bed3c-249">В файле *DepartmentsController.cs* замените код метода HttpGet `Delete` приведенным ниже кодом:</span><span class="sxs-lookup"><span data-stu-id="bed3c-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="bed3c-250">Этот метод принимает необязательный параметр, который указывает, отображается ли страница повторно после ошибки параллелизма.</span><span class="sxs-lookup"><span data-stu-id="bed3c-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="bed3c-251">Если этот флаг имеет значение true, а указанная кафедра больше не существует, она была удалена другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="bed3c-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="bed3c-252">В этом случае код выполняет перенаправление на страницу индекса.</span><span class="sxs-lookup"><span data-stu-id="bed3c-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="bed3c-253">Если этот флаг имеет значение true, а кафедра существует, она была изменена другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="bed3c-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="bed3c-254">В этом случае код отправляет сообщение об ошибке в представление, используя `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="bed3c-255">Замените код в методе HttpPost`Delete` (с именем `DeleteConfirmed`) следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="bed3c-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="bed3c-256">В шаблонном коде, который вы только что заменили, этот метод принимал только идентификатор записи:</span><span class="sxs-lookup"><span data-stu-id="bed3c-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="bed3c-257">Вы изменили этот параметр на экземпляр Department, созданный связывателем модели.</span><span class="sxs-lookup"><span data-stu-id="bed3c-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="bed3c-258">Это предоставляет EF доступ к значению свойства RowVersion в дополнение к ключу записи.</span><span class="sxs-lookup"><span data-stu-id="bed3c-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="bed3c-259">Вы также изменили имя метода действия с `DeleteConfirmed` на `Delete`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="bed3c-260">Шаблонный код использовал имя `DeleteConfirmed`, чтобы предоставить методу HttpPost уникальную сигнатуру.</span><span class="sxs-lookup"><span data-stu-id="bed3c-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="bed3c-261">(Среда CLR требует, чтобы перегруженные методы имели разные параметры метода.) Теперь, когда сигнатуры являются уникальными, можно придерживаться соглашения MVC и использовать одинаковое имя для методов delete HttpGet и HttpPost.</span><span class="sxs-lookup"><span data-stu-id="bed3c-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="bed3c-262">Если кафедра уже удалена, метод `AnyAsync` возвращает значение false, а приложение просто возвращается к методу Index.</span><span class="sxs-lookup"><span data-stu-id="bed3c-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="bed3c-263">При перехвате ошибки параллелизма код повторно отображает страницу подтверждения удаления и предоставляет флаг, указывающий, что нужно отобразить сообщение об ошибке параллелизма.</span><span class="sxs-lookup"><span data-stu-id="bed3c-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="bed3c-264">Обновление представления удаления</span><span class="sxs-lookup"><span data-stu-id="bed3c-264">Update the Delete view</span></span>

<span data-ttu-id="bed3c-265">В файле *Views/Departments/Delete.cshtml* замените шаблонный код приведенным ниже кодом, который добавляет поле сообщения об ошибке и скрытые поля для свойств DepartmentID и RowVersion.</span><span class="sxs-lookup"><span data-stu-id="bed3c-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="bed3c-266">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="bed3c-266">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="bed3c-267">Этот код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="bed3c-267">This makes the following changes:</span></span>

* <span data-ttu-id="bed3c-268">Добавляет сообщение об ошибке между заголовками `h2` и `h3`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="bed3c-269">Заменяет FirstMidName на FullName в поле **Administrator** (Администратор).</span><span class="sxs-lookup"><span data-stu-id="bed3c-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="bed3c-270">Удаляет поле RowVersion.</span><span class="sxs-lookup"><span data-stu-id="bed3c-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="bed3c-271">Добавляет скрытое поле для свойства `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="bed3c-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="bed3c-272">Запустите приложение и перейдите на страницу индекса кафедр.</span><span class="sxs-lookup"><span data-stu-id="bed3c-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="bed3c-273">Щелкните правой кнопкой мыши гиперссылку **Delete** (Удалить) для кафедры английского языка и выберите пункт **Открыть на новой вкладке**, а затем на первой вкладке щелкните гиперссылку **Edit** (Изменить) для этой кафедры.</span><span class="sxs-lookup"><span data-stu-id="bed3c-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="bed3c-274">В первом окне измените одно из значений и нажмите кнопку **Save** (Сохранить):</span><span class="sxs-lookup"><span data-stu-id="bed3c-274">In the first window, change one of the values, and click **Save**:</span></span>

![Страница "Edit" (Редактирование) кафедры после изменения и до удаления](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="bed3c-276">На второй вкладке нажмите кнопку **Delete** (Удалить).</span><span class="sxs-lookup"><span data-stu-id="bed3c-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="bed3c-277">Вы видите сообщение об ошибке параллелизма, а значения кафедры обновляются с использованием актуальных сведений из базы данных.</span><span class="sxs-lookup"><span data-stu-id="bed3c-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Страница подтверждения удаления кафедры с ошибкой параллелизма](concurrency/_static/delete-error.png)

<span data-ttu-id="bed3c-279">Если нажать кнопку **Delete** (Удалить) еще раз, вы будете перенаправлены на страницу индекса, которая показывает, что кафедра была удалена.</span><span class="sxs-lookup"><span data-stu-id="bed3c-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="bed3c-280">Обновление представлений Details и Create</span><span class="sxs-lookup"><span data-stu-id="bed3c-280">Update Details and Create views</span></span>

<span data-ttu-id="bed3c-281">При необходимости вы можете очистить шаблонный код в представлениях Details и Create.</span><span class="sxs-lookup"><span data-stu-id="bed3c-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="bed3c-282">Замените код в *Views/Departments/Details.cshtml*, чтобы удалить столбец RowVersion и отобразить полное имя администратора.</span><span class="sxs-lookup"><span data-stu-id="bed3c-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="bed3c-283">Замените код в *Views/Departments/Create.cshtml*, чтобы добавить параметр "Select" (Выбрать) в раскрывающийся список.</span><span class="sxs-lookup"><span data-stu-id="bed3c-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="bed3c-284">Получите код</span><span class="sxs-lookup"><span data-stu-id="bed3c-284">Get the code</span></span>

[<span data-ttu-id="bed3c-285">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="bed3c-285">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="bed3c-286">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="bed3c-286">Additional resources</span></span>

 <span data-ttu-id="bed3c-287">Дополнительные сведения об обработке параллелизма в EF Core см. в разделе [Конфликты параллелизма](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="bed3c-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="bed3c-288">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="bed3c-288">Next steps</span></span>

<span data-ttu-id="bed3c-289">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="bed3c-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bed3c-290">Дополнительные сведения о конфликтах параллелизма</span><span class="sxs-lookup"><span data-stu-id="bed3c-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="bed3c-291">Добавление свойства отслеживания</span><span class="sxs-lookup"><span data-stu-id="bed3c-291">Added a tracking property</span></span>
> * <span data-ttu-id="bed3c-292">Создание представлений и контроллера кафедр</span><span class="sxs-lookup"><span data-stu-id="bed3c-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="bed3c-293">Обновление представления указателя</span><span class="sxs-lookup"><span data-stu-id="bed3c-293">Updated Index view</span></span>
> * <span data-ttu-id="bed3c-294">Обновление методов редактирования</span><span class="sxs-lookup"><span data-stu-id="bed3c-294">Updated Edit methods</span></span>
> * <span data-ttu-id="bed3c-295">Обновление представления редактирования</span><span class="sxs-lookup"><span data-stu-id="bed3c-295">Updated Edit view</span></span>
> * <span data-ttu-id="bed3c-296">Тестирование конфликтов параллелизма</span><span class="sxs-lookup"><span data-stu-id="bed3c-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="bed3c-297">Обновление страницы удаления</span><span class="sxs-lookup"><span data-stu-id="bed3c-297">Updated the Delete page</span></span>
> * <span data-ttu-id="bed3c-298">Обновление представлений сведений и создания</span><span class="sxs-lookup"><span data-stu-id="bed3c-298">Updated Details and Create views</span></span>

<span data-ttu-id="bed3c-299">В следующем учебнике описано, как реализовать наследование "одна таблица на иерархию" для сущностей Instructor и Student.</span><span class="sxs-lookup"><span data-stu-id="bed3c-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="bed3c-300">Далее: Реализация наследования типа "одна таблица на иерархию"</span><span class="sxs-lookup"><span data-stu-id="bed3c-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
