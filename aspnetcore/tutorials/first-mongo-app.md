---
title: Создание веб-API с помощью ASP.NET Core и MongoDB
author: prkhandelwal
description: В этом руководстве показано, как создать веб-API ASP.NET Core с помощью базы данных NoSQL MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 489a15f2011e44ffd9f9bfe7d5410b7d79a10632
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587610"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="42aec-103">Создание веб-API с помощью ASP.NET Core и MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="42aec-104">Авторы [Пратик Ханделвал ](https://twitter.com/K2Prk) (Pratik Khandelwal) и [Скотт Эдди](https://twitter.com/Scott_Addie) (Scott Addie).</span><span class="sxs-lookup"><span data-stu-id="42aec-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42aec-105">В этом руководстве описано, как создать веб-API, который выполняет операции создания, чтения, обновления и удаления (CRUD) в базе данных NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="42aec-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="42aec-106">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="42aec-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="42aec-107">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-107">Configure MongoDB</span></span>
> * <span data-ttu-id="42aec-108">создать базу данных MongoDB;</span><span class="sxs-lookup"><span data-stu-id="42aec-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="42aec-109">определить коллекцию и схему MongoDB;</span><span class="sxs-lookup"><span data-stu-id="42aec-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="42aec-110">выполнить операции CRUD MongoDB из веб-API.</span><span class="sxs-lookup"><span data-stu-id="42aec-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="42aec-111">Настройка сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="42aec-111">Customize JSON serialization</span></span>

<span data-ttu-id="42aec-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42aec-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="42aec-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="42aec-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42aec-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42aec-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="42aec-115">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="42aec-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="42aec-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="42aec-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="42aec-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="42aec-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42aec-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="42aec-119">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="42aec-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="42aec-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42aec-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="42aec-121">C# для Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42aec-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="42aec-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42aec-123">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="42aec-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="42aec-124">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="42aec-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="42aec-125">Visual Studio для Mac 7.7 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="42aec-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="42aec-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="42aec-127">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-127">Configure MongoDB</span></span>

<span data-ttu-id="42aec-128">Если используется Windows, MongoDB по умолчанию устанавливается в папку *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="42aec-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="42aec-129">Добавьте *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* в переменную среды `Path`.</span><span class="sxs-lookup"><span data-stu-id="42aec-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="42aec-130">Это изменение обеспечит доступ к MongoDB из любого места на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="42aec-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="42aec-131">В следующих шагах используйте интерфейс mongo Shell, чтобы создать базу данных и коллекции и сохранить документы.</span><span class="sxs-lookup"><span data-stu-id="42aec-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="42aec-132">Дополнительные сведения о командах mongo Shell см. в руководстве по [работе с mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="42aec-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="42aec-133">Выберите папку на компьютере разработки для хранения данных.</span><span class="sxs-lookup"><span data-stu-id="42aec-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="42aec-134">Например, *C:\\BooksData* при работе в Windows.</span><span class="sxs-lookup"><span data-stu-id="42aec-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="42aec-135">Если такого каталога нет, создайте его.</span><span class="sxs-lookup"><span data-stu-id="42aec-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="42aec-136">В mongo Shell нельзя создавать каталоги.</span><span class="sxs-lookup"><span data-stu-id="42aec-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="42aec-137">Откройте командную оболочку.</span><span class="sxs-lookup"><span data-stu-id="42aec-137">Open a command shell.</span></span> <span data-ttu-id="42aec-138">Выполните следующую команду для подключения к MongoDB через порт 27017, заданный по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="42aec-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="42aec-139">Не забудьте заменить `<data_directory_path>` каталогом, созданным на предыдущем этапе.</span><span class="sxs-lookup"><span data-stu-id="42aec-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="42aec-140">Откройте другой экземпляр командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="42aec-140">Open another command shell instance.</span></span> <span data-ttu-id="42aec-141">Подключитесь к тестовой базе данных по умолчанию, выполнив такую команду:</span><span class="sxs-lookup"><span data-stu-id="42aec-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="42aec-142">Запустите в командной оболочке следующее:</span><span class="sxs-lookup"><span data-stu-id="42aec-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="42aec-143">Будет создана база данных с именем *BookstoreDb*, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="42aec-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="42aec-144">Если такая база данных существует, для нее уже установлено подключение для транзакций.</span><span class="sxs-lookup"><span data-stu-id="42aec-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="42aec-145">Создайте коллекцию `Books` с помощью такой команды:</span><span class="sxs-lookup"><span data-stu-id="42aec-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="42aec-146">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="42aec-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="42aec-147">Определите схему для коллекции `Books` и вставьте два документа, используя следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42aec-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="42aec-148">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="42aec-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="42aec-149">Идентификаторы в этой статье не будут соответствовать идентификаторам в запущенном примере.</span><span class="sxs-lookup"><span data-stu-id="42aec-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="42aec-150">Просмотрите документы в базе данных, используя такую команду:</span><span class="sxs-lookup"><span data-stu-id="42aec-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="42aec-151">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="42aec-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="42aec-152">Схема добавляет автоматически созданное свойство `_id` типа `ObjectId` к каждому документу.</span><span class="sxs-lookup"><span data-stu-id="42aec-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="42aec-153">База данных готова к работе.</span><span class="sxs-lookup"><span data-stu-id="42aec-153">The database is ready.</span></span> <span data-ttu-id="42aec-154">Можно приступить к созданию веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="42aec-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="42aec-155">Создание проекта веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42aec-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42aec-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42aec-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="42aec-157">Перейдите в раздел **Файл** > **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="42aec-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="42aec-158">Выберите тип проекта **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="42aec-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="42aec-159">Задайте для проекта имя *BooksApi* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="42aec-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="42aec-160">Выберите целевую платформу **.NET Core** и **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="42aec-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="42aec-161">Выберите шаблон проекта **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="42aec-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="42aec-162">Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="42aec-163">В окне **Консоль диспетчера пакетов** перейдите в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="42aec-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="42aec-164">Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:</span><span class="sxs-lookup"><span data-stu-id="42aec-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="42aec-165">Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="42aec-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="42aec-166">В командной оболочке выполните такие команды:</span><span class="sxs-lookup"><span data-stu-id="42aec-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="42aec-167">Новый проект веб-API ASP.NET Core для работы с .NET Core будет создан и открыт в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="42aec-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="42aec-168">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **Required assets to build and debug are missing from "BooksApi". Add them?** (В BooksApi отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?).</span><span class="sxs-lookup"><span data-stu-id="42aec-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="42aec-169">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="42aec-169">Select **Yes**.</span></span>
1. <span data-ttu-id="42aec-170">Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="42aec-171">Откройте **Интегрированный терминал** и перейдите в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="42aec-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="42aec-172">Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:</span><span class="sxs-lookup"><span data-stu-id="42aec-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42aec-173">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="42aec-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="42aec-174">В версии Visual Studio для Mac, предшествующей 8.6, на боковой панели выберите **Файл** > **Создать решение** >  **.NET Core** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="42aec-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="42aec-175">В версии 8.6 или более поздней на боковой панели выберите **Файл** > **Создать решение** > **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="42aec-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="42aec-176">Выберите шаблон проекта **ASP.NET Core** > **API** для C# и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="42aec-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="42aec-177">В раскрывающемся списке **Требуемая версия .NET Framework** выберите **.NET Core 3.1** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="42aec-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="42aec-178">Введите *BooksApi* в поле **Имя проекта** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="42aec-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="42aec-179">На панели **Решение** щелкните правой кнопкой мыши узел проекта **Зависимости** и выберите **Добавить пакеты**.</span><span class="sxs-lookup"><span data-stu-id="42aec-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="42aec-180">В поле поиска введите *MongoDB.Driver*, выберите пакет *MongoDB.Driver* и нажмите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="42aec-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="42aec-181">Нажмите кнопку **Принимаю** в диалоговом окне **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="42aec-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="42aec-182">Добавление модели сущности</span><span class="sxs-lookup"><span data-stu-id="42aec-182">Add an entity model</span></span>

1. <span data-ttu-id="42aec-183">Добавьте каталог *Models* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="42aec-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="42aec-184">Добавьте класс `Book` в каталог *Models* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="42aec-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="42aec-185">В классе выше свойство `Id`:</span><span class="sxs-lookup"><span data-stu-id="42aec-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="42aec-186">требуется для сопоставления объекта среды CLR с коллекцией MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="42aec-187">Помечается с помощью [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) для назначения этого свойства в качестве первичного ключа документа.</span><span class="sxs-lookup"><span data-stu-id="42aec-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="42aec-188">Помечается с помощью [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm), чтобы разрешить передачу параметра в качестве типа `string` вместо структуры [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="42aec-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="42aec-189">Mongo обрабатывает преобразование из `string` в `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="42aec-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="42aec-190">Свойство `BookName` помечено атрибутом [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="42aec-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="42aec-191">Значение атрибута `Name` представляет имя свойства в коллекции MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="42aec-192">Добавление модели конфигурации</span><span class="sxs-lookup"><span data-stu-id="42aec-192">Add a configuration model</span></span>

1. <span data-ttu-id="42aec-193">Добавьте в файл *appsettings.json* следующие значения конфигурации базы данных:</span><span class="sxs-lookup"><span data-stu-id="42aec-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="42aec-194">Добавьте в каталог *Models* файл *BookstoreDatabaseSettings.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="42aec-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="42aec-195">Предыдущий класс `BookstoreDatabaseSettings` используется для хранения значений свойств `BookstoreDatabaseSettings` файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="42aec-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="42aec-196">Свойства JSON и C# имеют одинаковые имена, что упрощает сопоставление.</span><span class="sxs-lookup"><span data-stu-id="42aec-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="42aec-197">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="42aec-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="42aec-198">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="42aec-198">In the preceding code:</span></span>

   * <span data-ttu-id="42aec-199">Экземпляр конфигурации, к которому привязан раздел `BookstoreDatabaseSettings` файла *appsettings.json* , зарегистрирован в контейнере внедрения зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="42aec-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="42aec-200">Например, свойство `ConnectionString` объекта `BookstoreDatabaseSettings` заполняется свойством `BookstoreDatabaseSettings:ConnectionString` в файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="42aec-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="42aec-201">Интерфейс `IBookstoreDatabaseSettings` регистрируется в DI с использованием [времени существования отдельной службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="42aec-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="42aec-202">При внедрении экземпляр интерфейса разрешается в объект `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="42aec-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="42aec-203">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылки `BookstoreDatabaseSettings` и `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="42aec-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="42aec-204">Добавление службы операций CRUD</span><span class="sxs-lookup"><span data-stu-id="42aec-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="42aec-205">Добавьте каталог *Services* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="42aec-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="42aec-206">Добавьте класс `BookService` в каталог *Services* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="42aec-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="42aec-207">В предыдущем коде экземпляр `IBookstoreDatabaseSettings` извлекается из DI посредством внедрения конструктора.</span><span class="sxs-lookup"><span data-stu-id="42aec-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="42aec-208">Таким образом обеспечивается доступ к значениям конфигурации в файле *appsettings.json* , которые были добавлены в разделе [Добавление модели конфигурации](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="42aec-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="42aec-209">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="42aec-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="42aec-210">В предыдущем коде класс `BookService` регистрируется в DI, чтобы обеспечить поддержку внедрения через конструктор в используемые классы.</span><span class="sxs-lookup"><span data-stu-id="42aec-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="42aec-211">Время существования отдельной службы — наиболее подходящий вариант, так как `BookService` имеет прямую зависимость от `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="42aec-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="42aec-212">В соответствии с официальными [правилами повторного использования клиента Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)`MongoClient` следует регистрировать в DI с использованием времени существования отдельной службы.</span><span class="sxs-lookup"><span data-stu-id="42aec-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="42aec-213">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылку `BookService`:</span><span class="sxs-lookup"><span data-stu-id="42aec-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="42aec-214">Класс `BookService` использует следующие члены `MongoDB.Driver` для выполнения операций CRUD в базе данных:</span><span class="sxs-lookup"><span data-stu-id="42aec-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="42aec-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): считывает экземпляр сервера для выполнения операций с базой данных.</span><span class="sxs-lookup"><span data-stu-id="42aec-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="42aec-216">Конструктор этого класса предоставляет строку подключения MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="42aec-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): представляет базу данных Mongo для выполнения операций.</span><span class="sxs-lookup"><span data-stu-id="42aec-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="42aec-218">В этом руководстве используется универсальный метод [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) интерфейса для получения доступа к данным в определенной коллекции.</span><span class="sxs-lookup"><span data-stu-id="42aec-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="42aec-219">Операции CRUD следует выполнять с коллекцией после вызова этого метода.</span><span class="sxs-lookup"><span data-stu-id="42aec-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="42aec-220">В вызове метода `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="42aec-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="42aec-221">`collection` представляет имя коллекции;</span><span class="sxs-lookup"><span data-stu-id="42aec-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="42aec-222">`TDocument` представляет тип объекта среды CLR, хранящегося в коллекции;</span><span class="sxs-lookup"><span data-stu-id="42aec-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="42aec-223">`GetCollection<TDocument>(collection)` возвращает объект [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm), представляющий коллекцию.</span><span class="sxs-lookup"><span data-stu-id="42aec-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="42aec-224">В этом руководстве следующие методы вызываются для коллекции:</span><span class="sxs-lookup"><span data-stu-id="42aec-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="42aec-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): удаляет один документ, отвечающий заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="42aec-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="42aec-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): возвращает все документы в коллекции, соответствующие заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="42aec-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="42aec-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): вставляет предоставленный объект в виде нового документа в коллекции.</span><span class="sxs-lookup"><span data-stu-id="42aec-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="42aec-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): заменяет один документ, отвечающий заданным критериям поиска, предоставленным объектом.</span><span class="sxs-lookup"><span data-stu-id="42aec-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="42aec-229">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="42aec-229">Add a controller</span></span>

<span data-ttu-id="42aec-230">Добавьте класс `BooksController` в каталог *Controllers* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="42aec-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="42aec-231">Предыдущий контроллер веб-API:</span><span class="sxs-lookup"><span data-stu-id="42aec-231">The preceding web API controller:</span></span>

* <span data-ttu-id="42aec-232">использует класс `BookService` для выполнения операций CRUD;</span><span class="sxs-lookup"><span data-stu-id="42aec-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="42aec-233">содержит методы действий для поддержки запросов HTTP GET, POST, PUT и DELETE.</span><span class="sxs-lookup"><span data-stu-id="42aec-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="42aec-234">Вызывает <xref:System.Web.Http.ApiController.CreatedAtRoute*> в методе действия `Create` для возврата ответа [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="42aec-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="42aec-235">Код состояния 201 представляет собой стандартный ответ метода HTTP POST, создающего ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="42aec-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="42aec-236">`CreatedAtRoute` также добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="42aec-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="42aec-237">Заголовок `Location` указывает универсальный код ресурса (URI) созданной книги.</span><span class="sxs-lookup"><span data-stu-id="42aec-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="42aec-238">Тестирование веб-API</span><span class="sxs-lookup"><span data-stu-id="42aec-238">Test the web API</span></span>

1. <span data-ttu-id="42aec-239">Выполните сборку и запуск приложения.</span><span class="sxs-lookup"><span data-stu-id="42aec-239">Build and run the app.</span></span>

1. <span data-ttu-id="42aec-240">Перейдите по адресу `http://localhost:<port>/api/books`, чтобы протестировать не имеющий параметров метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="42aec-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="42aec-241">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="42aec-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="42aec-242">Перейдите по адресу `http://localhost:<port>/api/books/{id here}`, чтобы протестировать перегруженный метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="42aec-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="42aec-243">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="42aec-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="42aec-244">Настройка параметров сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="42aec-244">Configure JSON serialization options</span></span>

<span data-ttu-id="42aec-245">Нужно изменить два параметра для ответов JSON, возвращаемых в разделе [Тестирование веб-API](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="42aec-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="42aec-246">Смешанный регистр имен свойств по умолчанию следует изменить в соответствии с регистром Pascal имен свойств объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="42aec-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="42aec-247">Свойство `bookName` должно возвращаться как `Name`.</span><span class="sxs-lookup"><span data-stu-id="42aec-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="42aec-248">Чтобы удовлетворить эти требования, внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="42aec-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="42aec-249">JSON.NET удален из общей платформы ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="42aec-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="42aec-250">Добавьте ссылку на пакет для [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="42aec-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="42aec-251">В `Startup.ConfigureServices` вставьте следующий выделенный код в вызов метода `AddControllers`:</span><span class="sxs-lookup"><span data-stu-id="42aec-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="42aec-252">После предыдущего изменения имена свойств в ответе сериализованного JSON веб-API соответствуют именам свойств в типе объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="42aec-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="42aec-253">Например, свойство `Author` класса `Book` сериализуется как `Author`.</span><span class="sxs-lookup"><span data-stu-id="42aec-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="42aec-254">В *Models/Book.cs* добавьте к свойству `BookName` следующий атрибут [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm):</span><span class="sxs-lookup"><span data-stu-id="42aec-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="42aec-255">Значение атрибута `[JsonProperty]`, равное `Name`, представляет имя свойства в ответе сериализованного JSON веб-API.</span><span class="sxs-lookup"><span data-stu-id="42aec-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="42aec-256">Добавьте следующий код в самое начало файла *Models/Book.cs*, чтобы разрешить ссылку на атрибут `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="42aec-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="42aec-257">Повторите действия, описанные в разделе [Тестирование веб-API](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="42aec-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="42aec-258">Обратите внимание на различие в именах свойств JSON.</span><span class="sxs-lookup"><span data-stu-id="42aec-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42aec-259">В этом руководстве описано, как создать веб-API, который выполняет операции создания, чтения, обновления и удаления (CRUD) в базе данных NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="42aec-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="42aec-260">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="42aec-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="42aec-261">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-261">Configure MongoDB</span></span>
> * <span data-ttu-id="42aec-262">создать базу данных MongoDB;</span><span class="sxs-lookup"><span data-stu-id="42aec-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="42aec-263">определить коллекцию и схему MongoDB;</span><span class="sxs-lookup"><span data-stu-id="42aec-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="42aec-264">выполнить операции CRUD MongoDB из веб-API.</span><span class="sxs-lookup"><span data-stu-id="42aec-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="42aec-265">Настройка сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="42aec-265">Customize JSON serialization</span></span>

<span data-ttu-id="42aec-266">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42aec-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="42aec-267">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="42aec-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42aec-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42aec-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="42aec-269">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="42aec-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="42aec-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="42aec-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="42aec-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="42aec-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42aec-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="42aec-273">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="42aec-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="42aec-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42aec-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="42aec-275">C# для Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42aec-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="42aec-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42aec-277">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="42aec-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="42aec-278">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="42aec-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="42aec-279">Visual Studio для Mac 7.7 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="42aec-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="42aec-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="42aec-281">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="42aec-281">Configure MongoDB</span></span>

<span data-ttu-id="42aec-282">Если используется Windows, MongoDB по умолчанию устанавливается в папку *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="42aec-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="42aec-283">Добавьте *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* в переменную среды `Path`.</span><span class="sxs-lookup"><span data-stu-id="42aec-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="42aec-284">Это изменение обеспечит доступ к MongoDB из любого места на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="42aec-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="42aec-285">В следующих шагах используйте интерфейс mongo Shell, чтобы создать базу данных и коллекции и сохранить документы.</span><span class="sxs-lookup"><span data-stu-id="42aec-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="42aec-286">Дополнительные сведения о командах mongo Shell см. в руководстве по [работе с mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="42aec-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="42aec-287">Выберите папку на компьютере разработки для хранения данных.</span><span class="sxs-lookup"><span data-stu-id="42aec-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="42aec-288">Например, *C:\\BooksData* при работе в Windows.</span><span class="sxs-lookup"><span data-stu-id="42aec-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="42aec-289">Если такого каталога нет, создайте его.</span><span class="sxs-lookup"><span data-stu-id="42aec-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="42aec-290">В mongo Shell нельзя создавать каталоги.</span><span class="sxs-lookup"><span data-stu-id="42aec-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="42aec-291">Откройте командную оболочку.</span><span class="sxs-lookup"><span data-stu-id="42aec-291">Open a command shell.</span></span> <span data-ttu-id="42aec-292">Выполните следующую команду для подключения к MongoDB через порт 27017, заданный по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="42aec-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="42aec-293">Не забудьте заменить `<data_directory_path>` каталогом, созданным на предыдущем этапе.</span><span class="sxs-lookup"><span data-stu-id="42aec-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="42aec-294">Откройте другой экземпляр командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="42aec-294">Open another command shell instance.</span></span> <span data-ttu-id="42aec-295">Подключитесь к тестовой базе данных по умолчанию, выполнив такую команду:</span><span class="sxs-lookup"><span data-stu-id="42aec-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="42aec-296">Запустите в командной оболочке следующее:</span><span class="sxs-lookup"><span data-stu-id="42aec-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="42aec-297">Будет создана база данных с именем *BookstoreDb*, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="42aec-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="42aec-298">Если такая база данных существует, для нее уже установлено подключение для транзакций.</span><span class="sxs-lookup"><span data-stu-id="42aec-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="42aec-299">Создайте коллекцию `Books` с помощью такой команды:</span><span class="sxs-lookup"><span data-stu-id="42aec-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="42aec-300">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="42aec-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="42aec-301">Определите схему для коллекции `Books` и вставьте два документа, используя следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42aec-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="42aec-302">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="42aec-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="42aec-303">Идентификаторы в этой статье не будут соответствовать идентификаторам в запущенном примере.</span><span class="sxs-lookup"><span data-stu-id="42aec-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="42aec-304">Просмотрите документы в базе данных, используя такую команду:</span><span class="sxs-lookup"><span data-stu-id="42aec-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="42aec-305">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="42aec-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="42aec-306">Схема добавляет автоматически созданное свойство `_id` типа `ObjectId` к каждому документу.</span><span class="sxs-lookup"><span data-stu-id="42aec-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="42aec-307">База данных готова к работе.</span><span class="sxs-lookup"><span data-stu-id="42aec-307">The database is ready.</span></span> <span data-ttu-id="42aec-308">Можно приступить к созданию веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="42aec-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="42aec-309">Создание проекта веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42aec-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42aec-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42aec-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="42aec-311">Перейдите в раздел **Файл** > **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="42aec-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="42aec-312">Выберите тип проекта **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="42aec-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="42aec-313">Задайте для проекта имя *BooksApi* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="42aec-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="42aec-314">Выберите **.NET Core** требуемой версии .NET Framework и **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="42aec-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="42aec-315">Выберите шаблон проекта **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="42aec-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="42aec-316">Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="42aec-317">В окне **Консоль диспетчера пакетов** перейдите в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="42aec-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="42aec-318">Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:</span><span class="sxs-lookup"><span data-stu-id="42aec-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="42aec-319">Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="42aec-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="42aec-320">В командной оболочке выполните такие команды:</span><span class="sxs-lookup"><span data-stu-id="42aec-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="42aec-321">Новый проект веб-API ASP.NET Core для работы с .NET Core будет создан и открыт в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="42aec-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="42aec-322">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **Required assets to build and debug are missing from "BooksApi". Add them?** (В BooksApi отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?).</span><span class="sxs-lookup"><span data-stu-id="42aec-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="42aec-323">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="42aec-323">Select **Yes**.</span></span>
1. <span data-ttu-id="42aec-324">Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="42aec-325">Откройте **Интегрированный терминал** и перейдите в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="42aec-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="42aec-326">Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:</span><span class="sxs-lookup"><span data-stu-id="42aec-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42aec-327">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="42aec-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="42aec-328">В версии Visual Studio для Mac, предшествующей 8.6, на боковой панели выберите **Файл** > **Создать решение** >  **.NET Core** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="42aec-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="42aec-329">В версии 8.6 или более поздней на боковой панели выберите **Файл** > **Создать решение** > **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="42aec-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="42aec-330">Выберите шаблон проекта **Веб-API ASP.NET Core** для C# и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="42aec-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="42aec-331">В раскрывающемся списке **Требуемая версия .NET Framework** выберите **.NET Core 2.2** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="42aec-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="42aec-332">Введите *BooksApi* в поле **Имя проекта** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="42aec-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="42aec-333">На панели **Решение** щелкните правой кнопкой мыши узел проекта **Зависимости** и выберите **Добавить пакеты**.</span><span class="sxs-lookup"><span data-stu-id="42aec-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="42aec-334">В поле поиска введите *MongoDB.Driver*, выберите пакет *MongoDB.Driver* и нажмите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="42aec-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="42aec-335">Нажмите кнопку **Принимаю** в диалоговом окне **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="42aec-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="42aec-336">Добавление модели сущности</span><span class="sxs-lookup"><span data-stu-id="42aec-336">Add an entity model</span></span>

1. <span data-ttu-id="42aec-337">Добавьте каталог *Models* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="42aec-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="42aec-338">Добавьте класс `Book` в каталог *Models* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="42aec-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="42aec-339">В классе выше свойство `Id`:</span><span class="sxs-lookup"><span data-stu-id="42aec-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="42aec-340">требуется для сопоставления объекта среды CLR с коллекцией MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="42aec-341">Помечается с помощью [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) для назначения этого свойства в качестве первичного ключа документа.</span><span class="sxs-lookup"><span data-stu-id="42aec-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="42aec-342">Помечается с помощью [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm), чтобы разрешить передачу параметра в качестве типа `string` вместо структуры [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="42aec-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="42aec-343">Mongo обрабатывает преобразование из `string` в `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="42aec-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="42aec-344">Свойство `BookName` помечено атрибутом [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="42aec-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="42aec-345">Значение атрибута `Name` представляет имя свойства в коллекции MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="42aec-346">Добавление модели конфигурации</span><span class="sxs-lookup"><span data-stu-id="42aec-346">Add a configuration model</span></span>

1. <span data-ttu-id="42aec-347">Добавьте в файл *appsettings.json* следующие значения конфигурации базы данных:</span><span class="sxs-lookup"><span data-stu-id="42aec-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="42aec-348">Добавьте в каталог *Models* файл *BookstoreDatabaseSettings.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="42aec-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="42aec-349">Предыдущий класс `BookstoreDatabaseSettings` используется для хранения значений свойств `BookstoreDatabaseSettings` файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="42aec-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="42aec-350">Свойства JSON и C# имеют одинаковые имена, что упрощает сопоставление.</span><span class="sxs-lookup"><span data-stu-id="42aec-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="42aec-351">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="42aec-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="42aec-352">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="42aec-352">In the preceding code:</span></span>

   * <span data-ttu-id="42aec-353">Экземпляр конфигурации, к которому привязан раздел `BookstoreDatabaseSettings` файла *appsettings.json* , зарегистрирован в контейнере внедрения зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="42aec-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="42aec-354">Например, свойство `ConnectionString` объекта `BookstoreDatabaseSettings` заполняется свойством `BookstoreDatabaseSettings:ConnectionString` в файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="42aec-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="42aec-355">Интерфейс `IBookstoreDatabaseSettings` регистрируется в DI с использованием [времени существования отдельной службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="42aec-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="42aec-356">При внедрении экземпляр интерфейса разрешается в объект `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="42aec-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="42aec-357">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылки `BookstoreDatabaseSettings` и `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="42aec-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="42aec-358">Добавление службы операций CRUD</span><span class="sxs-lookup"><span data-stu-id="42aec-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="42aec-359">Добавьте каталог *Services* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="42aec-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="42aec-360">Добавьте класс `BookService` в каталог *Services* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="42aec-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="42aec-361">В предыдущем коде экземпляр `IBookstoreDatabaseSettings` извлекается из DI посредством внедрения конструктора.</span><span class="sxs-lookup"><span data-stu-id="42aec-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="42aec-362">Таким образом обеспечивается доступ к значениям конфигурации в файле *appsettings.json* , которые были добавлены в разделе [Добавление модели конфигурации](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="42aec-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="42aec-363">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="42aec-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="42aec-364">В предыдущем коде класс `BookService` регистрируется в DI, чтобы обеспечить поддержку внедрения через конструктор в используемые классы.</span><span class="sxs-lookup"><span data-stu-id="42aec-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="42aec-365">Время существования отдельной службы — наиболее подходящий вариант, так как `BookService` имеет прямую зависимость от `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="42aec-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="42aec-366">В соответствии с официальными [правилами повторного использования клиента Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)`MongoClient` следует регистрировать в DI с использованием времени существования отдельной службы.</span><span class="sxs-lookup"><span data-stu-id="42aec-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="42aec-367">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылку `BookService`:</span><span class="sxs-lookup"><span data-stu-id="42aec-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="42aec-368">Класс `BookService` использует следующие члены `MongoDB.Driver` для выполнения операций CRUD в базе данных:</span><span class="sxs-lookup"><span data-stu-id="42aec-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="42aec-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): считывает экземпляр сервера для выполнения операций с базой данных.</span><span class="sxs-lookup"><span data-stu-id="42aec-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="42aec-370">Конструктор этого класса предоставляет строку подключения MongoDB.</span><span class="sxs-lookup"><span data-stu-id="42aec-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="42aec-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): представляет базу данных Mongo для выполнения операций.</span><span class="sxs-lookup"><span data-stu-id="42aec-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="42aec-372">В этом руководстве используется универсальный метод [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) интерфейса для получения доступа к данным в определенной коллекции.</span><span class="sxs-lookup"><span data-stu-id="42aec-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="42aec-373">Операции CRUD следует выполнять с коллекцией после вызова этого метода.</span><span class="sxs-lookup"><span data-stu-id="42aec-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="42aec-374">В вызове метода `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="42aec-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="42aec-375">`collection` представляет имя коллекции;</span><span class="sxs-lookup"><span data-stu-id="42aec-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="42aec-376">`TDocument` представляет тип объекта среды CLR, хранящегося в коллекции;</span><span class="sxs-lookup"><span data-stu-id="42aec-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="42aec-377">`GetCollection<TDocument>(collection)` возвращает объект [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm), представляющий коллекцию.</span><span class="sxs-lookup"><span data-stu-id="42aec-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="42aec-378">В этом руководстве следующие методы вызываются для коллекции:</span><span class="sxs-lookup"><span data-stu-id="42aec-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="42aec-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): удаляет один документ, отвечающий заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="42aec-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="42aec-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): возвращает все документы в коллекции, соответствующие заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="42aec-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="42aec-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): вставляет предоставленный объект в виде нового документа в коллекции.</span><span class="sxs-lookup"><span data-stu-id="42aec-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="42aec-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): заменяет один документ, отвечающий заданным критериям поиска, предоставленным объектом.</span><span class="sxs-lookup"><span data-stu-id="42aec-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="42aec-383">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="42aec-383">Add a controller</span></span>

<span data-ttu-id="42aec-384">Добавьте класс `BooksController` в каталог *Controllers* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="42aec-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="42aec-385">Предыдущий контроллер веб-API:</span><span class="sxs-lookup"><span data-stu-id="42aec-385">The preceding web API controller:</span></span>

* <span data-ttu-id="42aec-386">использует класс `BookService` для выполнения операций CRUD;</span><span class="sxs-lookup"><span data-stu-id="42aec-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="42aec-387">содержит методы действий для поддержки запросов HTTP GET, POST, PUT и DELETE.</span><span class="sxs-lookup"><span data-stu-id="42aec-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="42aec-388">Вызывает <xref:System.Web.Http.ApiController.CreatedAtRoute*> в методе действия `Create` для возврата ответа [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="42aec-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="42aec-389">Код состояния 201 представляет собой стандартный ответ метода HTTP POST, создающего ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="42aec-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="42aec-390">`CreatedAtRoute` также добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="42aec-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="42aec-391">Заголовок `Location` указывает универсальный код ресурса (URI) созданной книги.</span><span class="sxs-lookup"><span data-stu-id="42aec-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="42aec-392">Тестирование веб-API</span><span class="sxs-lookup"><span data-stu-id="42aec-392">Test the web API</span></span>

1. <span data-ttu-id="42aec-393">Выполните сборку и запуск приложения.</span><span class="sxs-lookup"><span data-stu-id="42aec-393">Build and run the app.</span></span>

1. <span data-ttu-id="42aec-394">Перейдите по адресу `http://localhost:<port>/api/books`, чтобы протестировать не имеющий параметров метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="42aec-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="42aec-395">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="42aec-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="42aec-396">Перейдите по адресу `http://localhost:<port>/api/books/{id here}`, чтобы протестировать перегруженный метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="42aec-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="42aec-397">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="42aec-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="42aec-398">Настройка параметров сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="42aec-398">Configure JSON serialization options</span></span>

<span data-ttu-id="42aec-399">Нужно изменить два параметра для ответов JSON, возвращаемых в разделе [Тестирование веб-API](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="42aec-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="42aec-400">Смешанный регистр имен свойств по умолчанию следует изменить в соответствии с регистром Pascal имен свойств объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="42aec-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="42aec-401">Свойство `bookName` должно возвращаться как `Name`.</span><span class="sxs-lookup"><span data-stu-id="42aec-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="42aec-402">Чтобы удовлетворить эти требования, внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="42aec-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="42aec-403">В `Startup.ConfigureServices` вставьте следующий выделенный код в вызов метода `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="42aec-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="42aec-404">После предыдущего изменения имена свойств в ответе сериализованного JSON веб-API соответствуют именам свойств в типе объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="42aec-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="42aec-405">Например, свойство `Author` класса `Book` сериализуется как `Author`.</span><span class="sxs-lookup"><span data-stu-id="42aec-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="42aec-406">В *Models/Book.cs* добавьте к свойству `BookName` следующий атрибут [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm):</span><span class="sxs-lookup"><span data-stu-id="42aec-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="42aec-407">Значение атрибута `[JsonProperty]`, равное `Name`, представляет имя свойства в ответе сериализованного JSON веб-API.</span><span class="sxs-lookup"><span data-stu-id="42aec-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="42aec-408">Добавьте следующий код в самое начало файла *Models/Book.cs*, чтобы разрешить ссылку на атрибут `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="42aec-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="42aec-409">Повторите действия, описанные в разделе [Тестирование веб-API](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="42aec-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="42aec-410">Обратите внимание на различие в именах свойств JSON.</span><span class="sxs-lookup"><span data-stu-id="42aec-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="42aec-411">Добавление поддержки аутентификации в веб-API</span><span class="sxs-lookup"><span data-stu-id="42aec-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="42aec-412">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="42aec-412">Next steps</span></span>

<span data-ttu-id="42aec-413">Дополнительные сведения о сборке веб-API ASP.NET Core см. в этих статьях:</span><span class="sxs-lookup"><span data-stu-id="42aec-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="42aec-414">Версия статьи на YouTube</span><span class="sxs-lookup"><span data-stu-id="42aec-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
* [<span data-ttu-id="42aec-415">Microsoft Learn. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42aec-415">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
