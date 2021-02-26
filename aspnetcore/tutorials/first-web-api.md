---
title: Учебник. Создание веб-API с помощью ASP.NET Core
author: rick-anderson
description: Узнайте, как создать веб-API с помощью ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 1f7c7db857090ff0a174d37b86e1265bab40b4fd
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564073"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Учебник. Создание веб-API с помощью ASP.NET Core

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Майк Уоссон](https://github.com/mikewasson) (Mike Wasson)

В этом учебнике приводятся основные сведения о создании веб-API с помощью ASP.NET Core.

::: moniker range=">= aspnetcore-5.0"

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * Создание проекта веб-API.
> * Добавление класса модели и контекста базы данных.
> * Формирование шаблонов контроллера с использованием методов CRUD.
> * Настройка маршрутизации, URL-пути и возвращаемых значений.
> * Вызов веб-API с помощью Postman.

В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.

## <a name="overview"></a>Обзор

В этом руководстве создается следующий API-интерфейс:

|API | Описание | Текст запроса | Текст ответа |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Получение всех элементов задач | Отсутствуют | Массив элементов задач|
|`GET /api/TodoItems/{id}` | Получение объекта по идентификатору | Отсутствуют | Элемент задачи|
|`POST /api/TodoItems` | Добавление нового элемента | Элемент задачи | Элемент задачи |
|`PUT /api/TodoItems/{id}` | Обновление существующего элемента &nbsp; | Элемент задачи | Отсутствуют |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Удаление элемента &nbsp; &nbsp; | Отсутствуют | Отсутствуют|

На следующем рисунке показана структура приложения.

![Клиент представлен прямоугольником слева. Он отправляет запрос и получает ответ от приложения (прямоугольник справа). В прямоугольнике приложения также расположены три прямоугольника, представляющих контроллер, модель и уровень доступа к данным. Запрос поступает на контроллер приложения, который выполняет операции чтения и записи между контроллером и уровнем доступа к данным. Модель сериализуется и возвращается клиенту в ответе.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a>Создайте веб-проект.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В меню **Файл** выберите пункт **Создать** > **Проект**.
* Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.
* Назовите проект *TodoApi* и нажмите **Создать**.
* Убедитесь, что в диалоговом окне **Создание веб-приложения ASP.NET Core** выбраны платформы **.NET Core** и **ASP.NET Core 5.0**. Выберите шаблон **API** и нажмите кнопку **Создать**.

![Диалоговое окно создания проекта VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.
* Выполните следующие команды:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.

  Предыдущие команды:

  * Создает новый проект веб-API и открывает его в Visual Studio Code.
  * Добавляет пакеты NuGet, которые понадобятся в следующем разделе.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* В диалоговом окне **Configure the new ASP.NET Core Web API** (Настройка нового веб-API ASP.NET Core) в качестве **целевой платформы** выберите последнюю версию .NET Core 5.x. Выберите **Далее**.

* Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Откройте окно терминала в папке проекта и выполните следующую команду.

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a>Тестирование проекта

Шаблон проекта создает API `WeatherForecast` с поддержкой [Swagger](xref:tutorials/web-api-help-pages-using-swagger).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.

[!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio запустит:

* веб-сервер IIS Express;
* браузер по умолчанию и перейдет к `https://localhost:<port>/swagger/index.html`, где `<port>` — это номер порта, выбранный случайным образом.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

Нажмите клавиши CTRL+F5, чтобы запустить приложение. В браузере перейдите по следующему URL-адресу: [https://localhost:5001/swagger](https://localhost:5001/swagger).

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение. Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом. Появится ошибка HTTP 404 (Не найдено). Добавьте `/swagger` к URL-адресу (измените URL-адрес на `https://localhost:<port>/swagger`).

---

Откроется страница Swagger `/swagger/index.html`. Выберите **Get** (Получить) > **Try it out** (Попробовать) > **Execute** (Выполнить). На странице отобразятся:

* команда [Curl](https://curl.haxx.se/) для тестирования API WeatherForecast;
* URL-адрес для тестирования API WeatherForecast;
* код, текст и заголовки ответа;
* раскрывающийся список с типами мультимедиа и примером значения и схемы.

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
Swagger используется для создания полезной документации и страниц справки для веб-API. В этом учебнике рассматривается создание веб-API. Дополнительные сведения о Swagger: <xref:tutorials/web-api-help-pages-using-swagger>.

Скопируйте и вставьте **URL-адрес запроса** в адресную строку браузера: `https://localhost:<port>/WeatherForecast`

Возвращаемые данные JSON будут выглядеть примерно так:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a>Обновление launchUrl

В файле *Properties/launchSettings.json* обновите `launchUrl` с `"swagger"` на `"api/TodoItems"`.

```json
"launchUrl": "api/TodoItems",
```

Поскольку Swagger удален, предыдущая разметка изменит URL-адрес, который запускается в методе GET контроллера, добавленного в следующих разделах.

## <a name="add-a-model-class"></a>Добавление класса модели

*Модель* — это набор классов, представляющих данные, которыми управляет приложение. Модель этого приложения содержит единственный класс `TodoItem`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **обозревателе решений** щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Назовите папку *Models* .

* Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**. Присвойте классу имя *TodoItem* и выберите **Добавить**.

* Замените код шаблона следующим кодом:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Добавьте папку с именем *Models* .

* Добавьте в папку *Models* класс `TodoItem` со следующим кодом:

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Назовите папку *Models* .

  ![Новая папка](first-web-api-mac/_static/folder.png)

* Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.

* Назовите класс *TodoItem* и нажмите **Создать**.

* Замените код шаблона следующим кодом:

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.

Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .

## <a name="add-a-database-context"></a>Добавление контекста базы данных

*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных. Этот класс является производным от класса <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>Добавление пакетов NuGet

* В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.
* Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.InMemory** в поле поиска.
* На панели слева выберите **Microsoft.EntityFrameworkCore.InMemory**.
* Установите флажок **Проект** на правой панели и выберите **Установить**.

![Диспетчер пакетов NuGet](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a>Добавление контекста базы данных TodoContext

* Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**. Назовите класс *TodoContext* и нажмите **Добавить**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* Добавьте класс `TodoContext` в папку *Models* .

---

* Введите следующий код:

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Регистрация контекста базы данных

В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection). Контейнер предоставляет службу контроллерам.

Обновите файл *Startup.cs*, используя следующий код:

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Предыдущий код:

* Удаляет вызовы Swagger.
* Удалите неиспользуемые объявления `using`.
* Добавляет контекст базы данных в контейнер внедрения зависимостей.
* Указывает, что контекст базы данных будет использовать базу данных в памяти.

## <a name="scaffold-a-controller"></a>Формирование шаблонов контроллера

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Щелкните папку *Controllers* правой кнопкой мыши.
* Щелкните **Добавить** > **Создать шаблонный элемент**.
* Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.
* В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:

  * Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.
  * Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.
  * Нажмите **Добавить**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Выполните следующие команды:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Предыдущие команды:

* добавляют пакеты NuGet, необходимые для формирования шаблонов;
* устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);
* формируют шаблоны для `TodoItemsController`.

---

Сформированный код:

* Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute). Этот атрибут указывает, что контроллер отвечает на запросы веб-API. Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.
* Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер. Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.

Шаблоны ASP.NET Core для:

* Контроллеры с представлениями включают `[action]` в шаблоне маршрута.
* Контроллеры API не включают `[action]` в шаблоне маршрута.

Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута. То есть имя связанного метода действия не используется в соответствующем маршруте.

## <a name="update-the-posttodoitem-create-method"></a>Обновление метода создания PostTodoItem

Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute). Этот метод получает значение элемента списка дел из текста HTTP-запроса.

Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:

* В случае успеха возвращает [код состояния HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201). HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.
* Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location). Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи. Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Указывает действие `GetTodoItem` для создания URI заголовка `Location`. Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.

### <a name="install-postman"></a>Установка Postman

В этом учебнике для тестирования веб-API используется Postman.

* Установка [Postman](https://www.getpostman.com/downloads/)
* Запустите веб-приложение.
* Запустите Postman.
* Отключение параметра **Проверка SSL-сертификата**
  * В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.
    > [!WARNING]
    > После тестирования контроллера снова включите проверку SSL-сертификата.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Тестирование PostTodoItem с использованием Postman

* Создайте новый запрос.
* Установите HTTP-метод `POST`.
* Задайте для URI значение `https://localhost:<port>/api/TodoItems`. Например, `https://localhost:5001/api/TodoItems`.
* Откройте вкладку **Тело**.
* Установите переключатель **без обработки**.
* Задайте тип **JSON (приложение/json)** .
* В теле запроса введите код JSON для элемента списка дел:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Нажмите кнопку **Отправить**.

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Тестирование URI заголовка расположения

URI заголовка расположения можно протестировать в браузере. Скопируйте и вставьте URI заголовка расположения в строку браузера.

Чтобы протестировать в Postman, выполните следующие действия.

* Перейдите на вкладку **Заголовки** в области **Ответ**.
* Скопируйте значение заголовка **Расположение**:

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* Установите HTTP-метод `GET`.
* Задайте для URI значение `https://localhost:<port>/api/TodoItems/1`. Например, `https://localhost:5001/api/TodoItems/1`.
* Нажмите кнопку **Отправить**.

## <a name="examine-the-get-methods"></a>Знакомство с методами GET

Реализуются две конечные точки GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman. Пример:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

При вызове `GetTodoItems` возвращается примерно такой ответ:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Тестирование Get с использованием Postman

* Создайте новый запрос.
* Укажите метод HTTP **GET**.
* Задайте для URI запроса значение `https://localhost:<port>/api/TodoItems`. Например, `https://localhost:5001/api/TodoItems`.
* Выберите режим **Представление с двумя областями** в Postman.
* Нажмите кнопку **Отправить**.

Это приложение использует выполняющуюся в памяти базу данных. Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных. Если данные не возвращаются, данные для приложения получаются методом [POST](#post).

## <a name="routing-and-url-paths"></a>Маршрутизация и пути URL

Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET. Путь URL для каждого метода формируется следующим образом:

* Возьмите строку шаблона в атрибуте `Route` контроллера:

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller"). В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems". В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.
* Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути. В этом примере шаблон не используется. Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи. При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Возвращаемые значения

Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа. Код ответа для этого типа возвращаемого значения равен [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), что свидетельствует об отсутствии необработанных исключений. Необработанные исключения преобразуются в ошибки 5xx.

Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP. Например, метод `GetTodoItem` может возвращать два разных значения состояния:

* Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает код ошибки [ 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.
* В противном случае метод возвращает код 200 с телом ответа JSON. При возвращении `item` возвращается ответ HTTP 200.

## <a name="the-puttodoitem-method"></a>Метод PutTodoItem

Проверьте метод `PutTodoItem`.

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT. Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения. Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.

### <a name="test-the-puttodoitem-method"></a>Тестирование метода PutTodoItem

В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения. При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент. Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.

Обновите элемент списка дел с идентификатором 1 и присвойте ему имя `"feed fish"`:

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

На следующем рисунке показан процесс обновления Postman:

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Метод DeleteTodoItem

Проверьте метод `DeleteTodoItem`.

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Тестирование метода DeleteTodoItem

Удалите элемент списка дел с помощью Postman:

* Укажите метод `DELETE`.
* Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).
* Нажмите кнопку **Отправить**.

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a>Предотвращение избыточной публикации

В настоящее время пример приложения предоставляет весь объект `TodoItem`. Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели. Это связано с несколькими причинами, и безопасность является основной. Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления. В этой статье используется **DTO**.

DTO можно использовать для следующего:

* Предотвращение избыточной публикации.
* Скрытие свойств, которые не предназначены для просмотра клиентами.
* Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.
* Сведение графов объектов, содержащих вложенные объекты. Сведенные графы объектов могут быть удобнее для клиентов.

Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.

Убедитесь, что вы можете отправить и получить секретное поле.

Создайте модель DTO:

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Обновите `TodoItemsController` для использования `TodoItemDTO`:

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Убедитесь, что вы можете отправить или получить секретное поле.

## <a name="call-the-web-api-with-javascript"></a>Вызов веб-API с помощью JavaScript

См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * Создание проекта веб-API.
> * Добавление класса модели и контекста базы данных.
> * Формирование шаблонов контроллера с использованием методов CRUD.
> * Настройка маршрутизации, URL-пути и возвращаемых значений.
> * Вызов веб-API с помощью Postman.

В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.

## <a name="overview"></a>Обзор

В этом руководстве создается следующий API-интерфейс:

|API | Описание | Текст запроса | Текст ответа |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Получение всех элементов задач | Отсутствуют | Массив элементов задач|
|`GET /api/TodoItems/{id}` | Получение объекта по идентификатору | Отсутствуют | Элемент задачи|
|`POST /api/TodoItems` | Добавление нового элемента | Элемент задачи | Элемент задачи |
|`PUT /api/TodoItems/{id}` | Обновление существующего элемента &nbsp; | Элемент задачи | Отсутствуют |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Удаление элемента &nbsp; &nbsp; | Отсутствуют | Отсутствуют|

На следующем рисунке показана структура приложения.

![Клиент представлен прямоугольником слева. Он отправляет запрос и получает ответ от приложения (прямоугольник справа). В прямоугольнике приложения также расположены три прямоугольника, представляющих контроллер, модель и уровень доступа к данным. Запрос поступает на контроллер приложения, который выполняет операции чтения и записи между контроллером и уровнем доступа к данным. Модель сериализуется и возвращается клиенту в ответе.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Создайте веб-проект.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В меню **Файл** выберите пункт **Создать** > **Проект**.
* Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.
* Назовите проект *TodoApi* и нажмите **Создать**.
* В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**. Выберите шаблон **API** и нажмите кнопку **Создать**.

![Диалоговое окно создания проекта VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.
* Выполните следующие команды:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.

  Предыдущие команды:

  * Создает новый проект веб-API и открывает его в Visual Studio Code.
  * Добавляет пакеты NuGet, которые понадобятся в следующем разделе.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 3.x. Выберите **Далее**.

* Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Откройте окно терминала в папке проекта и выполните следующую команду.

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Тестирование API

Шаблон проекта создает API `WeatherForecast`. Вызовите метод `Get` из браузера для тестирования приложения.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Нажмите клавиши CTRL+F5, чтобы запустить приложение. Visual Studio запустит браузер и перейдет к `https://localhost:<port>/WeatherForecast`, где `<port>` — это номер порта, выбранный случайным образом.

Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**. В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Нажмите клавиши CTRL+F5, чтобы запустить приложение. В браузере перейдите по следующему URL-адресу: `https://localhost:5001/WeatherForecast`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение. Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом. Появится ошибка HTTP 404 (Не найдено). Добавьте `/WeatherForecast` к URL-адресу (измените URL-адрес на `https://localhost:<port>/WeatherForecast`).

---

Возвращаемые данные JSON будут выглядеть примерно так:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Добавление класса модели

*Модель* — это набор классов, представляющих данные, которыми управляет приложение. Модель этого приложения содержит единственный класс `TodoItem`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **обозревателе решений** щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Назовите папку *Models* .

* Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**. Присвойте классу имя *TodoItem* и выберите **Добавить**.

* Замените код шаблона следующим кодом:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Добавьте папку с именем *Models* .

* Добавьте в папку *Models* класс `TodoItem` со следующим кодом:

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Назовите папку *Models* .

  ![Новая папка](first-web-api-mac/_static/folder.png)

* Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.

* Назовите класс *TodoItem* и нажмите **Создать**.

* Замените код шаблона следующим кодом:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.

Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .

## <a name="add-a-database-context"></a>Добавление контекста базы данных

*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных. Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>Добавление пакетов NuGet

* В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.
* Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.InMemory** в поле поиска.
* На панели слева выберите **Microsoft.EntityFrameworkCore.InMemory**.
* Установите флажок **Проект** на правой панели и выберите **Установить**.

![Диспетчер пакетов NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Добавление контекста базы данных TodoContext

* Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**. Назовите класс *TodoContext* и нажмите **Добавить**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* Добавьте класс `TodoContext` в папку *Models* .

---

* Введите следующий код:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Регистрация контекста базы данных

В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection). Контейнер предоставляет службу контроллерам.

Обновите файл *Startup.cs*, используя следующий выделенный код:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Предыдущий код:

* Удалите неиспользуемые объявления `using`.
* Добавляет контекст базы данных в контейнер внедрения зависимостей.
* Указывает, что контекст базы данных будет использовать базу данных в памяти.

## <a name="scaffold-a-controller"></a>Формирование шаблонов контроллера

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Щелкните папку *Controllers* правой кнопкой мыши.
* Щелкните **Добавить** > **Создать шаблонный элемент**.
* Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.
* В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:

  * Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.
  * Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.
  * Нажмите **Добавить**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Выполните следующие команды:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Предыдущие команды:

* добавляют пакеты NuGet, необходимые для формирования шаблонов;
* устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);
* формируют шаблоны для `TodoItemsController`.

---

Сформированный код:

* Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute). Этот атрибут указывает, что контроллер отвечает на запросы веб-API. Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.
* Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер. Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.

Шаблоны ASP.NET Core для:

* Контроллеры с представлениями включают `[action]` в шаблоне маршрута.
* Контроллеры API не включают `[action]` в шаблоне маршрута.

Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута. То есть имя связанного метода действия не используется в соответствующем маршруте.

## <a name="examine-the-posttodoitem-create-method"></a>Знакомство с методом создания PostTodoItem

Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute). Этот метод получает значение элемента списка дел из текста HTTP-запроса.

Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:

* В случае успеха возвращает код состояния HTTP 201. HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.
* Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location). Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи. Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Указывает действие `GetTodoItem` для создания URI заголовка `Location`. Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.

### <a name="install-postman"></a>Установка Postman

В этом учебнике для тестирования веб-API используется Postman.

* Установка [Postman](https://www.getpostman.com/downloads/)
* Запустите веб-приложение.
* Запустите Postman.
* Отключение параметра **Проверка SSL-сертификата**
  * В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.
    > [!WARNING]
    > После тестирования контроллера снова включите проверку SSL-сертификата.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Тестирование PostTodoItem с использованием Postman

* Создайте новый запрос.
* Установите HTTP-метод `POST`.
* Задайте для URI значение `https://localhost:<port>/api/TodoItems`. Например, `https://localhost:5001/api/TodoItems`.
* Откройте вкладку **Тело**.
* Установите переключатель **без обработки**.
* Задайте тип **JSON (приложение/json)** .
* В теле запроса введите код JSON для элемента списка дел:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Нажмите кнопку **Отправить**.

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a>Тестирование URI заголовка расположения с помощью Postman

* Перейдите на вкладку **Заголовки** в области **Ответ**.
* Скопируйте значение заголовка **Расположение**:

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* Установите HTTP-метод `GET`.
* Задайте для URI значение `https://localhost:<port>/api/TodoItems/1`. Например, `https://localhost:5001/api/TodoItems/1`.
* Нажмите кнопку **Отправить**.

## <a name="examine-the-get-methods"></a>Знакомство с методами GET

Эти методы реализуют две конечные точки GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman. Пример:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

При вызове `GetTodoItems` возвращается примерно такой ответ:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Тестирование Get с использованием Postman

* Создайте новый запрос.
* Укажите метод HTTP **GET**.
* Задайте для URI запроса значение `https://localhost:<port>/api/TodoItems`. Например, `https://localhost:5001/api/TodoItems`.
* Выберите режим **Представление с двумя областями** в Postman.
* Нажмите кнопку **Отправить**.

Это приложение использует выполняющуюся в памяти базу данных. Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных. Если данные не возвращаются, данные для приложения получаются методом [POST](#post).

## <a name="routing-and-url-paths"></a>Маршрутизация и пути URL

Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET. Путь URL для каждого метода формируется следующим образом:

* Возьмите строку шаблона в атрибуте `Route` контроллера:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller"). В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems". В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.
* Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути. В этом примере шаблон не используется. Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи. При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Возвращаемые значения 

Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа. Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений. Необработанные исключения преобразуются в ошибки 5xx.

Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP. Например, метод `GetTodoItem` может возвращать два разных значения состояния:

* Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).
* В противном случае метод возвращает код 200 с телом ответа JSON. При возвращении `item` возвращается ответ HTTP 200.

## <a name="the-puttodoitem-method"></a>Метод PutTodoItem

Проверьте метод `PutTodoItem`.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT. Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения. Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.

### <a name="test-the-puttodoitem-method"></a>Тестирование метода PutTodoItem

В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения. При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент. Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.

Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

На следующем рисунке показан процесс обновления Postman:

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Метод DeleteTodoItem

Проверьте метод `DeleteTodoItem`.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Тестирование метода DeleteTodoItem

Удалите элемент списка дел с помощью Postman:

* Укажите метод `DELETE`.
* Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).
* Нажмите кнопку **Отправить**.

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a>Предотвращение избыточной публикации

В настоящее время пример приложения предоставляет весь объект `TodoItem`. Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели. Это связано с несколькими причинами, и безопасность является основной. Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления. В этой статье используется **DTO**.

DTO можно использовать для следующего:

* Предотвращение избыточной публикации.
* Скрытие свойств, которые не предназначены для просмотра клиентами.
* Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.
* Сведение графов объектов, содержащих вложенные объекты. Сведенные графы объектов могут быть удобнее для клиентов.

Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.

Убедитесь, что вы можете отправить и получить секретное поле.

Создайте модель DTO:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Обновите `TodoItemsController` для использования `TodoItemDTO`:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Убедитесь, что вы можете отправить или получить секретное поле.

## <a name="call-the-web-api-with-javascript"></a>Вызов веб-API с помощью JavaScript

См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * Создание проекта веб-API.
> * Добавление класса модели и контекста базы данных.
> * Добавление контроллера.
> * Добавление методов CRUD.
> * Настройка маршрутизации и путей URL.
> * Указание возвращаемых значений.
> * Вызов веб-API с помощью Postman.
> * Вызовите веб-API с помощью JavaScript.

В конечном итоге вы получите веб-API, обеспечивающий управление элементами списка дел, хранящимися в реляционной базе данных.

## <a name="overview-21"></a>2\.1. Обзор

В этом руководстве создается следующий API-интерфейс:

|API | Описание | Текст запроса | Текст ответа |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Получение всех элементов задач | Отсутствуют | Массив элементов задач|
|GET /api/TodoItems/{id} | Получение объекта по идентификатору | Отсутствуют | Элемент задачи|
|POST /api/TodoItems | Добавление нового элемента | Элемент задачи | Элемент задачи |
|PUT /api/TodoItems/{id} | Обновление существующего элемента &nbsp; | Элемент задачи | Отсутствуют |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | Удаление элемента &nbsp; &nbsp; | Отсутствуют | Отсутствуют|

На следующем рисунке показана структура приложения.

![Клиент представлен прямоугольником слева. Он отправляет запрос и получает ответ от приложения (прямоугольник справа). В прямоугольнике приложения также расположены три прямоугольника, представляющих контроллер, модель и уровень доступа к данным. Запрос поступает на контроллер приложения, который выполняет операции чтения и записи между контроллером и уровнем доступа к данным. Модель сериализуется и возвращается клиенту в ответе.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a>2\.1. Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a>2\.1. Создание веб-проекта

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В меню **Файл** выберите пункт **Создать** > **Проект**.
* Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.
* Назовите проект *TodoApi* и нажмите **Создать**.
* В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 2.2**. Выберите шаблон **API** и нажмите кнопку **Создать**. **Не** выбирайте **Включение поддержки Docker**.

![Диалоговое окно создания проекта VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.
* Выполните следующие команды:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  С помощью этих команд создается новый проект веб-API и открывается новый экземпляр Visual Studio Code в новой папке проекта.

* При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.
  
* В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 2.x. Выберите **Далее**.

* Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a>2\.1. Тестирование API

Шаблон проекта создает API `values`. Вызовите метод `Get` из браузера для тестирования приложения.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Нажмите клавиши CTRL+F5, чтобы запустить приложение. Visual Studio запустит браузер и перейдет к `https://localhost:<port>/api/values`, где `<port>` — это номер порта, выбранный случайным образом.

Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**. В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Нажмите клавиши CTRL+F5, чтобы запустить приложение. В браузере перейдите по следующему URL-адресу: `https://localhost:5001/api/values`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение. Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом. Появится ошибка HTTP 404 (Не найдено). Добавьте `/api/values` к URL-адресу (измените URL-адрес на `https://localhost:<port>/api/values`).

---

Возвращается следующий файл JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a>2\.1. Добавление класса модели

*Модель* — это набор классов, представляющих данные, которыми управляет приложение. Модель этого приложения содержит единственный класс `TodoItem`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **обозревателе решений** щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Назовите папку *Models* .

* Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**. Присвойте классу имя *TodoItem* и выберите **Добавить**.

* Замените код шаблона следующим кодом:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Добавьте папку с именем *Models* .

* Добавьте в папку *Models* класс `TodoItem` со следующим кодом:

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Назовите папку *Models* .

  ![Новая папка](first-web-api-mac/_static/folder.png)

* Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.

* Назовите класс *TodoItem* и нажмите **Создать**.

* Замените код шаблона следующим кодом:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.

Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .

## <a name="add-a-database-context-21"></a>2\.1. Добавление контекста базы данных

*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных. Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**. Назовите класс *TodoContext* и нажмите **Добавить**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* Добавьте класс `TodoContext` в папку *Models* .

---

* Замените код шаблона следующим кодом:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a>2\.1. Регистрация контекста базы данных

В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection). Контейнер предоставляет службу контроллерам.

Обновите файл *Startup.cs*, используя следующий выделенный код:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Предыдущий код:

* Удалите неиспользуемые объявления `using`.
* Добавляет контекст базы данных в контейнер внедрения зависимостей.
* Указывает, что контекст базы данных будет использовать базу данных в памяти.

## <a name="add-a-controller-21"></a>2\.1. Добавление контроллера

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Щелкните папку *Controllers* правой кнопкой мыши.
* Щелкните **Добавить** > **Создать элемент**.
* В диалоговом окне **Добавить новый элемент** выберите шаблон **Класс контроллера API**.
* Присвойте классу имя *TodoController* и выберите **Добавить**.

  ![Диалоговое окно добавления элемента с контроллером в поле поиска и выбранным контроллером веб-API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* В папке *Controllers* создайте класс с именем `TodoController`.

---

* Замените код шаблона следующим кодом:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Предыдущий код:

* Определяет класс контроллера API без методов.
* Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute). Этот атрибут указывает, что контроллер отвечает на запросы веб-API. Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.
* Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер. Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.
* Добавляет элемент `Item1` в базу данных, если она пуста. Этот код находится в конструкторе и выполняется каждый раз при обнаружении нового HTTP-запроса. Если вы удалите все элементы, конструктор создаст `Item1` при следующем вызове метода API. Поэтому может создаться впечатление, что удаление не было выполнено, хотя это не так.

## <a name="add-get-methods-21"></a>2\.1. Добавление методов Get

Чтобы получить API, который извлекает элементы списка дел, добавьте следующие методы в класс `TodoController`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Эти методы реализуют две конечные точки GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Если приложение все еще выполняется, оно останавливается. Затем оно запускается снова с последними изменениями.

Протестируйте приложение, вызвав эти две конечные точки в браузере. Пример:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

При вызове `GetTodoItems` возвращается следующий ответ HTTP:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a>2\.1. Маршрутизация и пути URL

Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET. Путь URL для каждого метода формируется следующим образом:

* Возьмите строку шаблона в атрибуте `Route` контроллера:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller"). В этом примере класс контроллера носит имя **Todo**, а сам контроллер, соответственно, — "todo". В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.
* Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути. В этом примере шаблон не используется. Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи. При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a>2\.1. Возвращаемые значения

Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа. Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений. Необработанные исключения преобразуются в ошибки 5xx.

Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP. Например, метод `GetTodoItem` может возвращать два разных значения состояния:

* Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).
* В противном случае метод возвращает код 200 с телом ответа JSON. При возвращении `item` возвращается ответ HTTP 200.

## <a name="test-the-gettodoitems-method-21"></a>2\.1. Тестирование метода GetTodoItems

В этом учебнике для тестирования веб-API используется Postman.

* Установите [Postman](https://www.getpostman.com/downloads/).
* Запустите веб-приложение.
* Запустите Postman.
* Отключите параметр **Проверка SSL-сертификата**.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* В меню **Postman** > **Настройки** (вкладка **Общие**) отключите параметр **Проверка SSL-сертификата**. Кроме того, можно выбрать значок гаечного ключа и выбрать **Параметры**, а затем отключить проверку SSL-сертификата.

---
  
> [!WARNING]
> После тестирования контроллера снова включите проверку SSL-сертификата.

* Создайте новый запрос.
  * Укажите метод HTTP **GET**.
  * Задайте для URI запроса значение `https://localhost:<port>/api/todo`. Например, `https://localhost:5001/api/todo`.
* Выберите режим **Представление с двумя областями** в Postman.
* Нажмите кнопку **Отправить**.

![Postman с запросом Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a>2\.1. Добавление метода Create

Добавьте следующий метод `PostTodoItem` в *Controllers/TodoController.cs*: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute). Этот метод получает значение элемента списка дел из текста HTTP-запроса.

Метод `CreatedAtAction`:

* В случае успеха возвращает код состояния HTTP 201. HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.
* Добавляет заголовок `Location` в ответ. Заголовок `Location` расположения указывает URI вновь созданной задачи. Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Указывает действие `GetTodoItem` для создания URI заголовка `Location`. Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a>2\.1. Тестирование метода PostTodoItem

* Выполните построение проекта.
* В Postman укажите метод HTTP `POST`.
* Задайте для URI значение `https://localhost:<port>/api/Todo`. Например, `https://localhost:5001/api/Todo`.
* Откройте вкладку **Тело**.
* Установите переключатель **без обработки**.
* Задайте тип **JSON (приложение/json)** .
* В теле запроса введите код JSON для элемента списка дел:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Нажмите кнопку **Отправить**.

  ![Postman с запросом Create](first-web-api/_static/create.png)

  Если вы получаете ошибку 405 "Недопустимый метод", это может свидетельствовать о том, что после добавления метода `PostTodoItem` не была выполнена компиляция проекта.

### <a name="test-the-location-header-uri-21"></a>2\.1. Тестирование URI заголовка расположения

* Перейдите на вкладку **Заголовки** в области **Ответ**.
* Скопируйте значение заголовка **Расположение**:

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

* Укажите метод GET.
* Задайте для URI значение `https://localhost:<port>/api/TodoItems/2`. Например, `https://localhost:5001/api/TodoItems/2`.
* Нажмите кнопку **Отправить**.

## <a name="add-a-puttodoitem-method-21"></a>2\.1. Добавление метода PutTodoItem

Добавьте приведенный ниже метод `PutTodoItem`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT. Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения. Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.

### <a name="test-the-puttodoitem-method-21"></a>2\.1. Тестирование метода PutTodoItem

В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения. При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент. Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.

Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

На следующем рисунке показан процесс обновления Postman:

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a>2\.1. Добавление метода DeleteTodoItem

Добавьте приведенный ниже метод `DeleteTodoItem`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem`Ответ[ — 204 (нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method-21"></a>2\.1. Тестирование метода DeleteTodoItem

Удалите элемент списка дел с помощью Postman:

* Укажите метод `DELETE`.
* Укажите URI удаляемого объекта (например, `https://localhost:5001/api/todo/1`).
* Нажмите кнопку **Отправить**.

В этом примере приложения вы можете удалить все элементы. Однако в случае удаления последнего элемента в момент следующего вызова API конструктор класса модели создаст новый элемент.

## <a name="call-the-web-api-with-javascript-21"></a>2\.1. Вызов веб-API с помощью JavaScript

В этом разделе описано, как добавить HTML-страницу, которая использует JavaScript для вызова веб-API. jQuery инициирует запрос. JavaScript изменяет страницу, используя сведения из ответа API.

Настройте приложение для [обслуживания статических файлов](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) и [включения сопоставления файлов по умолчанию](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), обновив *Startup.cs* следующим выделенным кодом:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Создайте папку *wwwroot* в каталоге проекта.

Добавьте HTML-файл *index.html* в каталог *wwwroot*. Замените его содержимое следующей разметкой:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*. Замените его содержимое следующим кодом:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:

* Откройте файл *Properties\launchSettings.json*.
* Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.

В этом примере вызываются все методы CRUD в веб-API. Ниже приводится пояснение вызовов API.

### <a name="get-a-list-of-to-do-items-21"></a>2\.1. Получение списка элементов задач

jQuery отправляет запрос HTTP GET к веб-API, который возвращает ответ JSON, представляющий массив элементов списка дел. В случае успешного запроса используется функция обратного вызова `success`. При обратном вызове в модель DOM вносятся данные о задачах.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a>2\.1. Добавление элемента задачи

jQuery отправляет запрос HTTP POST с элементом списка дел в тексте запроса. Для параметров `accepts` и `contentType` указывается `application/json`, чтобы указать тип носителя при получении и отправке. Элемент списка дел преобразуется в JSON с помощью [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Если интерфейс API возвращает код состояния успешного выполнения, вызывается функция `getData` для обновления HTML-таблицы.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a>2\.1. Обновление элемента задачи

Обновление элемента списка дел выполняется аналогично его добавлению. `url` изменяется, чтобы добавить уникальный идентификатор для элемента, а в качестве `type` устанавливается `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a>2\.1. Удаление элемента задачи

Чтобы удалить элемент списка дел, установите для `type` в вызове AJAX значение `DELETE` и укажите уникальный идентификатор элемента в URL-адресе.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a>2\.1. Добавление поддержки аутентификации в веб-API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a>2\.1. Дополнительные ресурсы

[Просмотреть или скачать пример кода для этого учебника](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).

Дополнительные сведения см. в следующих ресурсах:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Версия руководства на YouTube](https://www.youtube.com/watch?v=TTkhEyGBfAk)
