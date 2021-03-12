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
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Создание веб-API с помощью ASP.NET Core и MongoDB

Авторы [Пратик Ханделвал ](https://twitter.com/K2Prk) (Pratik Khandelwal) и [Скотт Эдди](https://twitter.com/Scott_Addie) (Scott Addie).

::: moniker range=">= aspnetcore-3.0"

В этом руководстве описано, как создать веб-API, который выполняет операции создания, чтения, обновления и удаления (CRUD) в базе данных NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * Настройка MongoDB
> * создать базу данных MongoDB;
> * определить коллекцию и схему MongoDB;
> * выполнить операции CRUD MongoDB из веб-API.
> * Настройка сериализации JSON

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Пакет SDK для .NET Core 3.0 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Пакет SDK для .NET Core 3.0 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* [Пакет SDK для .NET Core 3.0 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio для Mac 7.7 или более поздней версии](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Настройка MongoDB

Если используется Windows, MongoDB по умолчанию устанавливается в папку *C:\\Program Files\\MongoDB*. Добавьте *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* в переменную среды `Path`. Это изменение обеспечит доступ к MongoDB из любого места на компьютере разработки.

В следующих шагах используйте интерфейс mongo Shell, чтобы создать базу данных и коллекции и сохранить документы. Дополнительные сведения о командах mongo Shell см. в руководстве по [работе с mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Выберите папку на компьютере разработки для хранения данных. Например, *C:\\BooksData* при работе в Windows. Если такого каталога нет, создайте его. В mongo Shell нельзя создавать каталоги.
1. Откройте командную оболочку. Выполните следующую команду для подключения к MongoDB через порт 27017, заданный по умолчанию. Не забудьте заменить `<data_directory_path>` каталогом, созданным на предыдущем этапе.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Откройте другой экземпляр командной оболочки. Подключитесь к тестовой базе данных по умолчанию, выполнив такую команду:

   ```console
   mongo
   ```

1. Запустите в командной оболочке следующее:

   ```console
   use BookstoreDb
   ```

   Будет создана база данных с именем *BookstoreDb*, если она не существует. Если такая база данных существует, для нее уже установлено подключение для транзакций.

1. Создайте коллекцию `Books` с помощью такой команды:

   ```console
   db.createCollection('Books')
   ```

   Отобразится такой результат:

   ```console
   { "ok" : 1 }
   ```

1. Определите схему для коллекции `Books` и вставьте два документа, используя следующую команду:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Отобразится такой результат:

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
   > Идентификаторы в этой статье не будут соответствовать идентификаторам в запущенном примере.

1. Просмотрите документы в базе данных, используя такую команду:

   ```console
   db.Books.find({}).pretty()
   ```

   Отобразится такой результат:

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

   Схема добавляет автоматически созданное свойство `_id` типа `ObjectId` к каждому документу.

База данных готова к работе. Можно приступить к созданию веб-API ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Создание проекта веб-API ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Перейдите в раздел **Файл** > **Создать** > **Проект**.
1. Выберите тип проекта **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.
1. Задайте для проекта имя *BooksApi* и нажмите кнопку **Создать**.
1. Выберите целевую платформу **.NET Core** и **ASP.NET Core 3.0**. Выберите шаблон проекта **API** и нажмите кнопку **Создать**.
1. Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB. В окне **Консоль диспетчера пакетов** перейдите в корневую папку проекта. Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code.](#tab/visual-studio-code)

1. В командной оболочке выполните такие команды:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Новый проект веб-API ASP.NET Core для работы с .NET Core будет создан и открыт в Visual Studio Code.

1. Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **Required assets to build and debug are missing from "BooksApi". Add them?** (В BooksApi отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?). Выберите ответ **Да**.
1. Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB. Откройте **Интегрированный терминал** и перейдите в корневую папку проекта. Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. В версии Visual Studio для Mac, предшествующей 8.6, на боковой панели выберите **Файл** > **Создать решение** >  **.NET Core** > **Приложение**. В версии 8.6 или более поздней на боковой панели выберите **Файл** > **Создать решение** > **Интернет и консоль** > **Приложение**.
1. Выберите шаблон проекта **ASP.NET Core** > **API** для C# и нажмите кнопку **Далее**.
1. В раскрывающемся списке **Требуемая версия .NET Framework** выберите **.NET Core 3.1** и нажмите кнопку **Далее**.
1. Введите *BooksApi* в поле **Имя проекта** и нажмите **Создать**.
1. На панели **Решение** щелкните правой кнопкой мыши узел проекта **Зависимости** и выберите **Добавить пакеты**.
1. В поле поиска введите *MongoDB.Driver*, выберите пакет *MongoDB.Driver* и нажмите **Добавить пакет**.
1. Нажмите кнопку **Принимаю** в диалоговом окне **Принятие условий лицензионного соглашения**.

---

## <a name="add-an-entity-model"></a>Добавление модели сущности

1. Добавьте каталог *Models* в корневую папку проекта.
1. Добавьте класс `Book` в каталог *Models* с помощью такого кода:

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

   В классе выше свойство `Id`:

   * требуется для сопоставления объекта среды CLR с коллекцией MongoDB.
   * Помечается с помощью [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) для назначения этого свойства в качестве первичного ключа документа.
   * Помечается с помощью [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm), чтобы разрешить передачу параметра в качестве типа `string` вместо структуры [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm). Mongo обрабатывает преобразование из `string` в `ObjectId`.

   Свойство `BookName` помечено атрибутом [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm). Значение атрибута `Name` представляет имя свойства в коллекции MongoDB.

## <a name="add-a-configuration-model"></a>Добавление модели конфигурации

1. Добавьте в файл *appsettings.json* следующие значения конфигурации базы данных:

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Добавьте в каталог *Models* файл *BookstoreDatabaseSettings.cs* со следующим кодом:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Предыдущий класс `BookstoreDatabaseSettings` используется для хранения значений свойств `BookstoreDatabaseSettings` файла *appsettings.json* . Свойства JSON и C# имеют одинаковые имена, что упрощает сопоставление.

1. Добавьте выделенный ниже код в `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   В приведенном выше коде:

   * Экземпляр конфигурации, к которому привязан раздел `BookstoreDatabaseSettings` файла *appsettings.json* , зарегистрирован в контейнере внедрения зависимостей (DI). Например, свойство `ConnectionString` объекта `BookstoreDatabaseSettings` заполняется свойством `BookstoreDatabaseSettings:ConnectionString` в файле *appsettings.json* .
   * Интерфейс `IBookstoreDatabaseSettings` регистрируется в DI с использованием [времени существования отдельной службы](xref:fundamentals/dependency-injection#service-lifetimes). При внедрении экземпляр интерфейса разрешается в объект `BookstoreDatabaseSettings`.

1. Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылки `BookstoreDatabaseSettings` и `IBookstoreDatabaseSettings`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Добавление службы операций CRUD

1. Добавьте каталог *Services* в корневую папку проекта.
1. Добавьте класс `BookService` в каталог *Services* с помощью такого кода:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   В предыдущем коде экземпляр `IBookstoreDatabaseSettings` извлекается из DI посредством внедрения конструктора. Таким образом обеспечивается доступ к значениям конфигурации в файле *appsettings.json* , которые были добавлены в разделе [Добавление модели конфигурации](#add-a-configuration-model).

1. Добавьте выделенный ниже код в `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   В предыдущем коде класс `BookService` регистрируется в DI, чтобы обеспечить поддержку внедрения через конструктор в используемые классы. Время существования отдельной службы — наиболее подходящий вариант, так как `BookService` имеет прямую зависимость от `MongoClient`. В соответствии с официальными [правилами повторного использования клиента Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)`MongoClient` следует регистрировать в DI с использованием времени существования отдельной службы.

1. Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылку `BookService`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Класс `BookService` использует следующие члены `MongoDB.Driver` для выполнения операций CRUD в базе данных:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): считывает экземпляр сервера для выполнения операций с базой данных. Конструктор этого класса предоставляет строку подключения MongoDB.

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): представляет базу данных Mongo для выполнения операций. В этом руководстве используется универсальный метод [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) интерфейса для получения доступа к данным в определенной коллекции. Операции CRUD следует выполнять с коллекцией после вызова этого метода. В вызове метода `GetCollection<TDocument>(collection)`:

  * `collection` представляет имя коллекции;
  * `TDocument` представляет тип объекта среды CLR, хранящегося в коллекции;

`GetCollection<TDocument>(collection)` возвращает объект [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm), представляющий коллекцию. В этом руководстве следующие методы вызываются для коллекции:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): удаляет один документ, отвечающий заданным критериям поиска.
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): возвращает все документы в коллекции, соответствующие заданным критериям поиска.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): вставляет предоставленный объект в виде нового документа в коллекции.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): заменяет один документ, отвечающий заданным критериям поиска, предоставленным объектом.

## <a name="add-a-controller"></a>Добавление контроллера

Добавьте класс `BooksController` в каталог *Controllers* с помощью такого кода:

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

Предыдущий контроллер веб-API:

* использует класс `BookService` для выполнения операций CRUD;
* содержит методы действий для поддержки запросов HTTP GET, POST, PUT и DELETE.
* Вызывает <xref:System.Web.Http.ApiController.CreatedAtRoute*> в методе действия `Create` для возврата ответа [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Код состояния 201 представляет собой стандартный ответ метода HTTP POST, создающего ресурс на сервере. `CreatedAtRoute` также добавляет заголовок `Location` в ответ. Заголовок `Location` указывает универсальный код ресурса (URI) созданной книги.

## <a name="test-the-web-api"></a>Тестирование веб-API

1. Выполните сборку и запуск приложения.

1. Перейдите по адресу `http://localhost:<port>/api/books`, чтобы протестировать не имеющий параметров метод действия `Get` контроллера. Отобразится такой ответ JSON:

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

1. Перейдите по адресу `http://localhost:<port>/api/books/{id here}`, чтобы протестировать перегруженный метод действия `Get` контроллера. Отобразится такой ответ JSON:

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Настройка параметров сериализации JSON

Нужно изменить два параметра для ответов JSON, возвращаемых в разделе [Тестирование веб-API](#test-the-web-api):

* Смешанный регистр имен свойств по умолчанию следует изменить в соответствии с регистром Pascal имен свойств объекта CLR.
* Свойство `bookName` должно возвращаться как `Name`.

Чтобы удовлетворить эти требования, внесите следующие изменения:

1. JSON.NET удален из общей платформы ASP.NET. Добавьте ссылку на пакет для [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).

1. В `Startup.ConfigureServices` вставьте следующий выделенный код в вызов метода `AddControllers`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   После предыдущего изменения имена свойств в ответе сериализованного JSON веб-API соответствуют именам свойств в типе объекта CLR. Например, свойство `Author` класса `Book` сериализуется как `Author`.

1. В *Models/Book.cs* добавьте к свойству `BookName` следующий атрибут [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm):

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Значение атрибута `[JsonProperty]`, равное `Name`, представляет имя свойства в ответе сериализованного JSON веб-API.

1. Добавьте следующий код в самое начало файла *Models/Book.cs*, чтобы разрешить ссылку на атрибут `[JsonProperty]`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Повторите действия, описанные в разделе [Тестирование веб-API](#test-the-web-api). Обратите внимание на различие в именах свойств JSON.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этом руководстве описано, как создать веб-API, который выполняет операции создания, чтения, обновления и удаления (CRUD) в базе данных NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * Настройка MongoDB
> * создать базу данных MongoDB;
> * определить коллекцию и схему MongoDB;
> * выполнить операции CRUD MongoDB из веб-API.
> * Настройка сериализации JSON

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Пакет SDK для .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Пакет SDK для .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* [Пакет SDK для .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio для Mac 7.7 или более поздней версии](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Настройка MongoDB

Если используется Windows, MongoDB по умолчанию устанавливается в папку *C:\\Program Files\\MongoDB*. Добавьте *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* в переменную среды `Path`. Это изменение обеспечит доступ к MongoDB из любого места на компьютере разработки.

В следующих шагах используйте интерфейс mongo Shell, чтобы создать базу данных и коллекции и сохранить документы. Дополнительные сведения о командах mongo Shell см. в руководстве по [работе с mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Выберите папку на компьютере разработки для хранения данных. Например, *C:\\BooksData* при работе в Windows. Если такого каталога нет, создайте его. В mongo Shell нельзя создавать каталоги.
1. Откройте командную оболочку. Выполните следующую команду для подключения к MongoDB через порт 27017, заданный по умолчанию. Не забудьте заменить `<data_directory_path>` каталогом, созданным на предыдущем этапе.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Откройте другой экземпляр командной оболочки. Подключитесь к тестовой базе данных по умолчанию, выполнив такую команду:

   ```console
   mongo
   ```

1. Запустите в командной оболочке следующее:

   ```console
   use BookstoreDb
   ```

   Будет создана база данных с именем *BookstoreDb*, если она не существует. Если такая база данных существует, для нее уже установлено подключение для транзакций.

1. Создайте коллекцию `Books` с помощью такой команды:

   ```console
   db.createCollection('Books')
   ```

   Отобразится такой результат:

   ```console
   { "ok" : 1 }
   ```

1. Определите схему для коллекции `Books` и вставьте два документа, используя следующую команду:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Отобразится такой результат:

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
   > Идентификаторы в этой статье не будут соответствовать идентификаторам в запущенном примере.

1. Просмотрите документы в базе данных, используя такую команду:

   ```console
   db.Books.find({}).pretty()
   ```

   Отобразится такой результат:

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

   Схема добавляет автоматически созданное свойство `_id` типа `ObjectId` к каждому документу.

База данных готова к работе. Можно приступить к созданию веб-API ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Создание проекта веб-API ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Перейдите в раздел **Файл** > **Создать** > **Проект**.
1. Выберите тип проекта **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.
1. Задайте для проекта имя *BooksApi* и нажмите кнопку **Создать**.
1. Выберите **.NET Core** требуемой версии .NET Framework и **ASP.NET Core 2.2**. Выберите шаблон проекта **API** и нажмите кнопку **Создать**.
1. Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB. В окне **Консоль диспетчера пакетов** перейдите в корневую папку проекта. Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code.](#tab/visual-studio-code)

1. В командной оболочке выполните такие команды:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Новый проект веб-API ASP.NET Core для работы с .NET Core будет создан и открыт в Visual Studio Code.

1. Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **Required assets to build and debug are missing from "BooksApi". Add them?** (В BooksApi отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?). Выберите ответ **Да**.
1. Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB. Откройте **Интегрированный терминал** и перейдите в корневую папку проекта. Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. В версии Visual Studio для Mac, предшествующей 8.6, на боковой панели выберите **Файл** > **Создать решение** >  **.NET Core** > **Приложение**. В версии 8.6 или более поздней на боковой панели выберите **Файл** > **Создать решение** > **Интернет и консоль** > **Приложение**.
1. Выберите шаблон проекта **Веб-API ASP.NET Core** для C# и нажмите кнопку **Далее**.
1. В раскрывающемся списке **Требуемая версия .NET Framework** выберите **.NET Core 2.2** и нажмите кнопку **Далее**.
1. Введите *BooksApi* в поле **Имя проекта** и нажмите **Создать**.
1. На панели **Решение** щелкните правой кнопкой мыши узел проекта **Зависимости** и выберите **Добавить пакеты**.
1. В поле поиска введите *MongoDB.Driver*, выберите пакет *MongoDB.Driver* и нажмите **Добавить пакет**.
1. Нажмите кнопку **Принимаю** в диалоговом окне **Принятие условий лицензионного соглашения**.

---

## <a name="add-an-entity-model"></a>Добавление модели сущности

1. Добавьте каталог *Models* в корневую папку проекта.
1. Добавьте класс `Book` в каталог *Models* с помощью такого кода:

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

   В классе выше свойство `Id`:

   * требуется для сопоставления объекта среды CLR с коллекцией MongoDB.
   * Помечается с помощью [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) для назначения этого свойства в качестве первичного ключа документа.
   * Помечается с помощью [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm), чтобы разрешить передачу параметра в качестве типа `string` вместо структуры [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm). Mongo обрабатывает преобразование из `string` в `ObjectId`.

   Свойство `BookName` помечено атрибутом [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm). Значение атрибута `Name` представляет имя свойства в коллекции MongoDB.

## <a name="add-a-configuration-model"></a>Добавление модели конфигурации

1. Добавьте в файл *appsettings.json* следующие значения конфигурации базы данных:

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Добавьте в каталог *Models* файл *BookstoreDatabaseSettings.cs* со следующим кодом:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Предыдущий класс `BookstoreDatabaseSettings` используется для хранения значений свойств `BookstoreDatabaseSettings` файла *appsettings.json* . Свойства JSON и C# имеют одинаковые имена, что упрощает сопоставление.

1. Добавьте выделенный ниже код в `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   В приведенном выше коде:

   * Экземпляр конфигурации, к которому привязан раздел `BookstoreDatabaseSettings` файла *appsettings.json* , зарегистрирован в контейнере внедрения зависимостей (DI). Например, свойство `ConnectionString` объекта `BookstoreDatabaseSettings` заполняется свойством `BookstoreDatabaseSettings:ConnectionString` в файле *appsettings.json* .
   * Интерфейс `IBookstoreDatabaseSettings` регистрируется в DI с использованием [времени существования отдельной службы](xref:fundamentals/dependency-injection#service-lifetimes). При внедрении экземпляр интерфейса разрешается в объект `BookstoreDatabaseSettings`.

1. Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылки `BookstoreDatabaseSettings` и `IBookstoreDatabaseSettings`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Добавление службы операций CRUD

1. Добавьте каталог *Services* в корневую папку проекта.
1. Добавьте класс `BookService` в каталог *Services* с помощью такого кода:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   В предыдущем коде экземпляр `IBookstoreDatabaseSettings` извлекается из DI посредством внедрения конструктора. Таким образом обеспечивается доступ к значениям конфигурации в файле *appsettings.json* , которые были добавлены в разделе [Добавление модели конфигурации](#add-a-configuration-model).

1. Добавьте выделенный ниже код в `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   В предыдущем коде класс `BookService` регистрируется в DI, чтобы обеспечить поддержку внедрения через конструктор в используемые классы. Время существования отдельной службы — наиболее подходящий вариант, так как `BookService` имеет прямую зависимость от `MongoClient`. В соответствии с официальными [правилами повторного использования клиента Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)`MongoClient` следует регистрировать в DI с использованием времени существования отдельной службы.

1. Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылку `BookService`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Класс `BookService` использует следующие члены `MongoDB.Driver` для выполнения операций CRUD в базе данных:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): считывает экземпляр сервера для выполнения операций с базой данных. Конструктор этого класса предоставляет строку подключения MongoDB.

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): представляет базу данных Mongo для выполнения операций. В этом руководстве используется универсальный метод [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) интерфейса для получения доступа к данным в определенной коллекции. Операции CRUD следует выполнять с коллекцией после вызова этого метода. В вызове метода `GetCollection<TDocument>(collection)`:

  * `collection` представляет имя коллекции;
  * `TDocument` представляет тип объекта среды CLR, хранящегося в коллекции;

`GetCollection<TDocument>(collection)` возвращает объект [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm), представляющий коллекцию. В этом руководстве следующие методы вызываются для коллекции:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): удаляет один документ, отвечающий заданным критериям поиска.
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): возвращает все документы в коллекции, соответствующие заданным критериям поиска.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): вставляет предоставленный объект в виде нового документа в коллекции.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): заменяет один документ, отвечающий заданным критериям поиска, предоставленным объектом.

## <a name="add-a-controller"></a>Добавление контроллера

Добавьте класс `BooksController` в каталог *Controllers* с помощью такого кода:

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

Предыдущий контроллер веб-API:

* использует класс `BookService` для выполнения операций CRUD;
* содержит методы действий для поддержки запросов HTTP GET, POST, PUT и DELETE.
* Вызывает <xref:System.Web.Http.ApiController.CreatedAtRoute*> в методе действия `Create` для возврата ответа [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Код состояния 201 представляет собой стандартный ответ метода HTTP POST, создающего ресурс на сервере. `CreatedAtRoute` также добавляет заголовок `Location` в ответ. Заголовок `Location` указывает универсальный код ресурса (URI) созданной книги.

## <a name="test-the-web-api"></a>Тестирование веб-API

1. Выполните сборку и запуск приложения.

1. Перейдите по адресу `http://localhost:<port>/api/books`, чтобы протестировать не имеющий параметров метод действия `Get` контроллера. Отобразится такой ответ JSON:

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

1. Перейдите по адресу `http://localhost:<port>/api/books/{id here}`, чтобы протестировать перегруженный метод действия `Get` контроллера. Отобразится такой ответ JSON:

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Настройка параметров сериализации JSON

Нужно изменить два параметра для ответов JSON, возвращаемых в разделе [Тестирование веб-API](#test-the-web-api):

* Смешанный регистр имен свойств по умолчанию следует изменить в соответствии с регистром Pascal имен свойств объекта CLR.
* Свойство `bookName` должно возвращаться как `Name`.

Чтобы удовлетворить эти требования, внесите следующие изменения:

1. В `Startup.ConfigureServices` вставьте следующий выделенный код в вызов метода `AddMvc`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   После предыдущего изменения имена свойств в ответе сериализованного JSON веб-API соответствуют именам свойств в типе объекта CLR. Например, свойство `Author` класса `Book` сериализуется как `Author`.

1. В *Models/Book.cs* добавьте к свойству `BookName` следующий атрибут [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm):

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Значение атрибута `[JsonProperty]`, равное `Name`, представляет имя свойства в ответе сериализованного JSON веб-API.

1. Добавьте следующий код в самое начало файла *Models/Book.cs*, чтобы разрешить ссылку на атрибут `[JsonProperty]`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Повторите действия, описанные в разделе [Тестирование веб-API](#test-the-web-api). Обратите внимание на различие в именах свойств JSON.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Добавление поддержки аутентификации в веб-API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о сборке веб-API ASP.NET Core см. в этих статьях:

* [Версия статьи на YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
* [Microsoft Learn. Создание веб-API с помощью ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
