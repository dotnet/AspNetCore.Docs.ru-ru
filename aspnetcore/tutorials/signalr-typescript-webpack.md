---
title: Использование ASP.NET Core SignalR с TypeScript и webpack
author: ssougnez
description: В рамках этого учебника вы настроите средство webpack для создания пакета и сборки веб-приложения ASP.NET Core SignalR, клиент которого написан на языке TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
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
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 9d52b72c669a9345cc7c5386876db22af55a97c7
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589755"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>Использование ASP.NET Core SignalR с TypeScript и webpack

Авторы: [Себастьен Сунье (Sébastien Sougnez)](https://twitter.com/ssougnez) и [Скотт Эдди (Scott Addie)](https://twitter.com/Scott_Addie)

С помощью средства [Webpack](https://webpack.js.org/) разработчики могут создавать пакеты и выполнять сборку ресурсов на стороне клиента для веб-приложения. В этом учебнике демонстрируется использование средства webpack для создания пакета и сборки веб-приложения ASP.NET Core SignalR, клиент которого написан на языке [TypeScript](https://www.typescriptlang.org/).

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * сформировать шаблон для начального приложения ASP.NET Core SignalR;
> * настроить клиент TypeScript SignalR;
> * Настроить конвейер сборки с использованием Webpack
> * настроить сервер SignalR.
> * Обеспечить взаимодействие между клиентом и сервером

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([как скачивать](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**
* [Пакет SDK для .NET Core 3.0 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) с [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Пакет SDK для .NET Core 3.0 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core)
* [C# для Visual Studio Code версии 1.17.1 или более поздней](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [Node.js](https://nodejs.org/) с [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Создание веб-приложения ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Настройте Visual Studio на поиск npm в переменной среды *PATH*. По умолчанию Visual Studio использует версию npm, которая находится в его каталоге установки. В Visual Studio выполните следующие инструкции:

1. Запустите Visual Studio. В начальном окне выберите **Продолжить без кода**.
1. Щелкните **Сервис** > **Параметры** > **Проекты и решения** > **Управление веб-пакетами** > **Внешние веб-инструменты**.
1. Выберите элемент *$(PATH)* в списке. Щелкните стрелку вверх, чтобы переместить этот элемент на вторую позицию в списке, и нажмите **OK**.

    ![Конфигурация Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Настройка Visual Studio завершена.

1. Перейдите в меню **Файл** > **Создать** > **Проект** и выберите шаблон **Веб-приложение ASP.NET Core**. Выберите **Далее**.
1. Присвойте проекту имя *SignalRWebPack* и щелкните **Создать**.
1. Выберите *.NET Core* в раскрывающемся списке целевых платформ и *ASP.NET Core 3.1* в раскрывающемся списке версий платформ. Выберите шаблон **Пустой** и щелкните **Создать**.

Добавить пакет `Microsoft.TypeScript.MSBuild` в проект:

1. В **обозревателе решений** (справа) щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**. На вкладке **Обзор** найдите `Microsoft.TypeScript.MSBuild`, а затем нажмите кнопку **Установить** справа, чтобы установить пакет.

Visual Studio добавляет пакет NuGet в узел **Зависимости** в **обозревателе решений**, разрешая компиляцию TypeScript в проекте.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Во **встроенном терминале** выполните следующую команду:

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* Команда `dotnet new` создает пустое веб-приложение ASP.NET Core в каталоге *SignalRWebPack*.
* Команда `code` открывает папку *SignalRWebPack* в текущем экземпляре Visual Studio Code.

Во **встроенном терминале** выполните следующую команду .NET Core CLI:

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

Предыдущая команда добавляет пакет [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/), разрешая компиляцию TypeScript в проекте.

---

## <a name="configure-webpack-and-typescript"></a>Настройка Webpack и TypeScript

Далее следует настроить преобразование TypeScript в JavaScript и создание пакета ресурсов на стороне клиента.

1. В корневом элементе проекта выполните следующую команду, чтобы создать файл *package.json*:

    ```console
    npm init -y
    ```

1. Добавьте выделенное свойство в файл *package.json* и сохраните изменения:

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    Если свойству `private` присвоено значение `true`, на следующем шаге не будут отображаться предупреждения об установке пакета.

1. Установите необходимые пакеты npm. Выполните следующую команду в корневом элементе проекта:

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Сведения о команде, на которые следует обратить внимание:

    * Для каждого имени пакета номер версии указывается после знака `@`. npm устанавливает указанные версии пакета.
    * Использование параметра `-E` позволяет отключить установленное по умолчанию поведение npm, предусматривающее запись операторов диапазона [семантического управления версиями](https://semver.org/) в файл *package.json*. Например, `"webpack": "4.41.5"` используется вместо `"webpack": "^4.41.5"`. Этот параметр позволяет исключить непреднамеренное обновление до более новых версий пакета.

    Дополнительные сведения см. в документации по [npm-install](https://docs.npmjs.com/cli/install).

1. Замените свойство `scripts` в файле *package.json* следующим кодом:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Некоторые пояснения к скриптам:

    * `build`. Создает пакет ваших ресурсов на стороне клиента в режиме разработки и отслеживает изменения. Наблюдатель за файлами выполняет повторное создание пакета при каждом изменении файла проекта. Параметр `mode` позволяет отключить оптимизации в рабочей среде, такие как встряхивание дерева и минификация. В среде разработки следует использовать только `build`.
    * `release`. Создает пакет ресурсов на стороне клиента в рабочем режиме.
    * `publish`. запускает скрипт `release` для создания пакета ресурсов на стороне клиента в рабочем режиме. Этот скрипт вызывает команду [publish](/dotnet/core/tools/dotnet-publish) интерфейса командной строки .NET Core для публикации приложения.

1. Создайте в корневом элементе проекта файл *webpack.config.js* со следующим кодом:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    Приведенный выше файл определяет конфигурацию компиляции Webpack. Сведения о конфигурации, на которые следует обратить внимание:

    * Свойство `output` переопределяет значение по умолчанию для *dist*. Вместо этого пакет выводится в каталог *wwwroot*.
    * Массив `resolve.extensions` содержит элемент *.js* для импорта кода JavaScript клиента SignalR.

1. Создайте новый каталог *src* в корневом каталоге проекта, чтобы сохранить клиентские ресурсы проекта.

1. Создайте файл *src/index.html* со следующими исправлениями.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    Приведенный выше HTML-файл определяет стереотипную разметку главной страницы.

1. Создайте каталог *src/css*. В нем будут храниться файлы *.css* проекта.

1. Создайте файл *src/css/main.css* со следующими CSS:

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    Приведенный выше файл *main.css* определяет стиль приложения.

1. Создайте файл *src/tsconfig.json* со следующим JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    Приведенный выше код определяет конфигурацию компилятора TypeScript для получения совместимого с [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 кода JavaScript.

1. Создайте файл *src/index.ts* со следующим кодом:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Приведенный выше код TypeScript извлекает ссылки на элементы модели DOM и присоединяет два обработчика событий:

    * `keyup`. Это событие возникает в том случае, если пользователь вводит текст текстовое поле `tbMessage`. Функция `send` вызывается, когда пользователь нажимает клавишу **ВВОД**.
    * `click`. это событие возникает, когда пользователь нажимает кнопку **Отправить**. Вызывается функция `send`.

## <a name="configure-the-app"></a>Настройка приложения

1. В `Startup.Configure` добавьте вызовы к [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   Приведенный выше код позволяет серверу размещать и обслуживать файл *index.html*.  Файл обрабатывается независимо от того, вводит ли пользователь полный или корневой URL-адрес веб-приложения.

1. В конце `Startup.Configure` сопоставьте маршрут */hub* с концентратором `ChatHub`. Замените код, отображающий *Hello World!* , следующей строкой: 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. В `Startup.ConfigureServices` вызовите [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Создайте новый каталог с именем *Hubs* в корневом каталоге проекта *SignalRWebPack/* для хранения концентратора SignalR.

1. Создайте концентратор *Hubs/ChatHub.cs* со следующим кодом:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Добавьте следующий оператор `using` в самое начало файла *Startup.cs*, чтобы разрешить ссылку `ChatHub`:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Обеспечение взаимодействия между клиентом и сервером

Приложение в настоящее время отображает простую форму для отправки сообщений, но еще не работает. Сервер прослушивает конкретный маршрут, но ничего не делает с отправленными сообщениями.

1. Выполните следующую команду в корневом элементе проекта:

    ```console
    npm i @microsoft/signalr @types/node
    ```

    Предыдущая команда устанавливает:

     * [Клиент TypeScript SignalR](https://www.npmjs.com/package/@microsoft/signalr), который позволяет клиенту отправлять сообщения на сервер.
     * Определения типа TypeScript для Node.js, обеспечивающие проверку типов Node.js во время компиляции.

1. Добавьте выделенный код в файл *src/index.ts*:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Приведенный выше код поддерживает получение сообщений от сервера. Класс `HubConnectionBuilder` создает новый построитель для настройки подключения к серверу. Функция `withUrl` настраивает URL-адрес концентратора.

    SignalR обеспечивает обмен сообщениями между клиентом и сервером. Каждому сообщению присваивается определенное имя. Например, сообщения с именем `messageReceived` могут выполнять логику отображения новых сообщений в соответствующей зоне. Прослушивание определенных сообщений реализуется с помощью функции `on`. Возможно прослушивание любого числа имен сообщений. Кроме того, можно передавать параметры сообщения, например имя его автора или содержимое полученного сообщения. После того как клиент получает сообщение, создается новый элемент `div`, в атрибуте `innerHTML` которого содержатся имя автора и содержимое сообщения. Этот элемент добавляется в основной элемент `div`, который используется для отображения сообщений.

1. Теперь клиент может принимать сообщения. Далее следует настроить его для отправки сообщений. Добавьте выделенный код в файл *src/index.ts*:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    Для отправки сообщения через соединение WebSockets необходимо вызвать метод `send`. Первый параметр этого метода содержит имя сообщения. Другие параметры заполняются данными сообщения. В этом примере на сервер отправляется сообщение, идентифицированное как `newMessage`. Это сообщение содержит имя пользователя, а также данные, введенные этим пользователем в текстовое поле. Если отправка выполняется успешно, значение текстового поля очищается.

1. Добавьте метод `NewMessage` в класс `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    Приведенный выше код осуществляет широковещательную рассылку полученных сервером сообщений всем подключенным пользователям. Универсальный метод `on` для получения всех сообщений не требуется. Имя метода указывается после суффиксов имени сообщения.

    В этом примере клиент TypeScript отправляет сообщение, идентифицированное как `newMessage`. Метод C# `NewMessage` ожидает данные, отправленные клиентом. Выполняется вызов метода [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) для [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Полученные сообщения отправляются всем клиентам, подключенным к концентратору.

## <a name="test-the-app"></a>Тестирование приложения

Чтобы проверить работоспособность приложения, выполните следующие шаги.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Запустите средство Webpack в режиме *release*. Выполните следующие команды в окне **Консоль диспетчера пакетов** в корневом элементе проекта. Если вы не в корневом каталоге проекта, введите перед этой командой `cd SignalRWebPack`.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Выберите **Отладка** > **Запуск без отладки**, чтобы запустить приложение в браузере, не присоединяя отладчик. Файл *wwwroot/index.html* обрабатывается по адресу `http://localhost:<port_number>`.

   Если во время компиляции возникают ошибки, попробуйте закрыть и снова открыть решение. 

1. Откройте другой экземпляр любого браузера. Вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите произвольный текст в поле **Сообщение** и нажмите кнопку **Отправить**. На обеих страницах мгновенно отображаются имя пользователя и сообщение.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Запустите средство Webpack в режиме *release*, выполнив следующую команду в корневом элементе проекта:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Выполните построение и запустите приложение, выполнив следующую команду в корневом элементе проекта:

    ```dotnetcli
    dotnet run
    ```

    Веб-сервер запускает приложение и делает его доступным на узле localhost.

1. Откройте браузер с адресом `http://localhost:<port_number>`. Обрабатывается файл *wwwroot/index.html*. Скопируйте URL-адрес из адресной строки.

1. Откройте другой экземпляр любого браузера. Вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите произвольный текст в поле **Сообщение** и нажмите кнопку **Отправить**. На обеих страницах мгновенно отображаются имя пользователя и сообщение.

---

![Сообщение, отображаемое в обоих окнах браузера](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**
* [Пакет SDK для .NET Core 2.2 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) с [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Пакет SDK для .NET Core 2.2 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core)
* [C# для Visual Studio Code версии 1.17.1 или более поздней](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [Node.js](https://nodejs.org/) с [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Создание веб-приложения ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Настройте Visual Studio на поиск npm в переменной среды *PATH*. По умолчанию Visual Studio использует версию npm, которая находится в его каталоге установки. В Visual Studio выполните следующие инструкции:

1. Щелкните **Сервис** > **Параметры** > **Проекты и решения** > **Управление веб-пакетами** > **Внешние веб-инструменты**.
1. Выберите элемент *$(PATH)* в списке. Щелкните стрелку вверх, чтобы переместить этот элемент на вторую позицию в списке.

    ![Конфигурация Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Настройка Visual Studio завершена. Теперь следует создать проект.

1. Перейдите в меню **Файл** > **Создать** > **Проект** и выберите шаблон **Веб-приложение ASP.NET Core**.
1. Присвойте проекту имя *SignalRWebPack* и щелкните **Создать**.
1. Выберите *.NET Core* в раскрывающемся списке целевых платформ и выберите *ASP.NET Core 2.2* в раскрывающемся списке средства выбора платформы. Выберите шаблон **Пустой** и щелкните **Создать**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Во **встроенном терминале** выполните следующую команду:

```dotnetcli
dotnet new web -o SignalRWebPack
```

В каталоге *SignalRWebPack* создается пустое веб-приложение ASP.NET Core, ориентированное на платформу .NET Core.

---

## <a name="configure-webpack-and-typescript"></a>Настройка Webpack и TypeScript

Далее следует настроить преобразование TypeScript в JavaScript и создание пакета ресурсов на стороне клиента.

1. В корневом элементе проекта выполните следующую команду, чтобы создать файл *package.json*:

    ```console
    npm init -y
    ```

1. Добавьте выделенное свойство в файл *package.json*:

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    Если свойству `private` присвоено значение `true`, на следующем шаге не будут отображаться предупреждения об установке пакета.

1. Установите необходимые пакеты npm. Выполните следующую команду в корневом элементе проекта:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Сведения о команде, на которые следует обратить внимание:

    * Для каждого имени пакета номер версии указывается после знака `@`. npm устанавливает указанные версии пакета.
    * Использование параметра `-E` позволяет отключить установленное по умолчанию поведение npm, предусматривающее запись операторов диапазона [семантического управления версиями](https://semver.org/) в файл *package.json*. Например, `"webpack": "4.29.3"` используется вместо `"webpack": "^4.29.3"`. Этот параметр позволяет исключить непреднамеренное обновление до более новых версий пакета.

    Дополнительные сведения см. в документации по [npm-install](https://docs.npmjs.com/cli/install).

1. Замените свойство `scripts` в файле *package.json* следующим кодом:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Некоторые пояснения к скриптам:

    * `build`. Создает пакет ваших ресурсов на стороне клиента в режиме разработки и отслеживает изменения. Наблюдатель за файлами выполняет повторное создание пакета при каждом изменении файла проекта. Параметр `mode` позволяет отключить оптимизации в рабочей среде, такие как встряхивание дерева и минификация. В среде разработки следует использовать только `build`.
    * `release`. Создает пакет ресурсов на стороне клиента в рабочем режиме.
    * `publish`. запускает скрипт `release` для создания пакета ресурсов на стороне клиента в рабочем режиме. Этот скрипт вызывает команду [publish](/dotnet/core/tools/dotnet-publish) интерфейса командной строки .NET Core для публикации приложения.

1. Создайте в корневом элементе проекта файл *webpack.config.js* со следующим кодом:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    Приведенный выше файл определяет конфигурацию компиляции Webpack. Сведения о конфигурации, на которые следует обратить внимание:

    * Свойство `output` переопределяет значение по умолчанию для *dist*. Вместо этого пакет выводится в каталог *wwwroot*.
    * Массив `resolve.extensions` содержит элемент *.js* для импорта кода JavaScript клиента SignalR.

1. Создайте новый каталог *src* в корневом каталоге проекта, чтобы сохранить клиентские ресурсы проекта.

1. Создайте файл *src/index.html* со следующими исправлениями.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    Приведенный выше HTML-файл определяет стереотипную разметку главной страницы.

1. Создайте каталог *src/css*. В нем будут храниться файлы *.css* проекта.

1. Создайте файл *src/css/main.css* со следующими исправлениями:

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    Приведенный выше файл *main.css* определяет стиль приложения.

1. Создайте файл *src/tsconfig.json* со следующим JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    Приведенный выше код определяет конфигурацию компилятора TypeScript для получения совместимого с [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 кода JavaScript.

1. Создайте файл *src/index.ts* со следующим кодом:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Приведенный выше код TypeScript извлекает ссылки на элементы модели DOM и присоединяет два обработчика событий:

    * `keyup`. Это событие возникает в том случае, если пользователь вводит текст в текстовое поле `tbMessage`. Функция `send` вызывается, когда пользователь нажимает клавишу **ВВОД**.
    * `click`. это событие возникает, когда пользователь нажимает кнопку **Отправить**. Вызывается функция `send`.

## <a name="configure-the-aspnet-core-app"></a>Настройка приложения ASP.NET Core

1. Код в методе `Startup.Configure` отображает текст *Hello World!* . Замените вызов метода `app.Run` вызовами методов [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Приведенный выше код позволяет серверу обнаруживать и обрабатывать файл *index.html* независимо от того, вводит ли пользователь полный URL-адрес или URL-адрес корня веб-приложения.

1. В `Startup.ConfigureServices` вызовите [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_). Этот метод добавляет в проект службы SignalR.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Сопоставьте маршрут */hub* с концентратором `ChatHub`. Добавьте в конец метода `Startup.Configure` следующие строки:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. Создайте новый каталог *Hubs* в корневом элементе проекта. Этот каталог служит для хранения концентратора SignalR, созданного на предыдущем шаге.

1. Создайте концентратор *Hubs/ChatHub.cs* со следующим кодом:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылку `ChatHub`:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Обеспечение взаимодействия между клиентом и сервером

На данный момент приложение отображает простую форму для отправки сообщений. При попытке сделать это ничего не происходит. Сервер прослушивает конкретный маршрут, но ничего не делает с отправленными сообщениями.

1. Выполните следующую команду в корневом элементе проекта:

    ```console
    npm install @aspnet/signalr
    ```

    Команда выше устанавливает [клиент TypeScript SignalR](https://www.npmjs.com/package/@microsoft/signalr), который позволяет клиенту отправлять сообщения на сервер.

1. Добавьте выделенный код в файл *src/index.ts*:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Приведенный выше код поддерживает получение сообщений от сервера. Класс `HubConnectionBuilder` создает новый построитель для настройки подключения к серверу. Функция `withUrl` настраивает URL-адрес концентратора.

    SignalR обеспечивает обмен сообщениями между клиентом и сервером. Каждому сообщению присваивается определенное имя. Например, сообщения с именем `messageReceived` могут выполнять логику отображения новых сообщений в соответствующей зоне. Прослушивание определенных сообщений реализуется с помощью функции `on`. Возможно прослушивание любого числа имен сообщений. Кроме того, можно передавать параметры сообщения, например имя его автора или содержимое полученного сообщения. После того как клиент получает сообщение, создается новый элемент `div`, в атрибуте `innerHTML` которого содержатся имя автора и содержимое сообщения. Новое сообщение добавляется в основной элемент `div`, который используется для отображения сообщений.

1. Теперь клиент может принимать сообщения. Далее следует настроить его для отправки сообщений. Добавьте выделенный код в файл *src/index.ts*:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    Для отправки сообщения через соединение WebSockets необходимо вызвать метод `send`. Первый параметр этого метода содержит имя сообщения. Другие параметры заполняются данными сообщения. В этом примере на сервер отправляется сообщение, идентифицированное как `newMessage`. Это сообщение содержит имя пользователя, а также данные, введенные этим пользователем в текстовое поле. Если отправка выполняется успешно, значение текстового поля очищается.

1. Добавьте метод `NewMessage` в класс `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    Приведенный выше код осуществляет широковещательную рассылку полученных сервером сообщений всем подключенным пользователям. Универсальный метод `on` для получения всех сообщений не требуется. Имя метода указывается после суффиксов имени сообщения.

    В этом примере клиент TypeScript отправляет сообщение, идентифицированное как `newMessage`. Метод C# `NewMessage` ожидает данные, отправленные клиентом. Выполняется вызов метода [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) для [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Полученные сообщения отправляются всем клиентам, подключенным к концентратору.

## <a name="test-the-app"></a>Тестирование приложения

Чтобы проверить работоспособность приложения, выполните следующие шаги.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Запустите средство Webpack в режиме *release*. Выполните следующие команды в окне **Консоль диспетчера пакетов** в корневом элементе проекта. Если вы не в корневом каталоге проекта, введите перед этой командой `cd SignalRWebPack`.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Выберите **Отладка** > **Запуск без отладки**, чтобы запустить приложение в браузере, не присоединяя отладчик. Файл *wwwroot/index.html* обрабатывается по адресу `http://localhost:<port_number>`.

1. Откройте другой экземпляр любого браузера. Вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите произвольный текст в поле **Сообщение** и нажмите кнопку **Отправить**. На обеих страницах мгновенно отображаются имя пользователя и сообщение.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Запустите средство Webpack в режиме *release*, выполнив следующую команду в корневом элементе проекта:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Выполните построение и запустите приложение, выполнив следующую команду в корневом элементе проекта:

    ```dotnetcli
    dotnet run
    ```

    Веб-сервер запускает приложение и делает его доступным на узле localhost.

1. Откройте браузер с адресом `http://localhost:<port_number>`. Обрабатывается файл *wwwroot/index.html*. Скопируйте URL-адрес из адресной строки.

1. Откройте другой экземпляр любого браузера. Вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите произвольный текст в поле **Сообщение** и нажмите кнопку **Отправить**. На обеих страницах мгновенно отображаются имя пользователя и сообщение.

---

![Сообщение, отображаемое в обоих окнах браузера](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
