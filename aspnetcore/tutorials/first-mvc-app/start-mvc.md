---
title: Начало работы с MVC ASP.NET Core
author: rick-anderson
description: Сведения о начале работы с MVC ASP.NET Core.
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710796"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Начало работы с MVC ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Это первое руководство из серии материалов по веб-разработке MVC ASP.NET Core с использованием контроллеров и представлений.

Пройдя всю серию, вы создадите приложение, которое управляет базой данных фильмов и отображает ее. Вы научитесь:

> [!div class="checklist"]
> * Создание веб-приложения.
> * Добавление модели и формирование шаблона.
> * Работа с базой данных.
> * Добавление поиска и проверки.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>Создание веб-приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Запустите Visual Studio и щелкните **Создать проект**
* В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** > **Далее**.
* В диалоговом окне **Настроить новый проект** введите `MvcMovie` в поле **Имя проекта**. Важно присвоить проекту имя *MvcMovie*. Регистр символов должен соответствовать каждому из совпадений `namespace` при копировании кода.
* Щелкните **Создать**.
* В диалоговом окне **Создайте веб-приложение ASP.NET Core** сделайте следующее:
  * В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.
  * Щелкните **ASP.NET Core Web App (Model-View-Controller)** (Веб-приложение ASP.NET Core (модель — представление — контроллер)).
  * **Create** (создать).

![Создание веб-приложения ASP.NET Core ](start-mvc/_static/mvcVS19v16.9.png)

Альтернативные подходы к созданию проекта см. в статье [Создание проекта в Visual Studio](/visualstudio/ide/create-new-project).

В Visual Studio используется шаблон проекта по умолчанию для созданного проекта MVC. Созданный проект это:

* рабочее приложение;
* простой начальный проект.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

В этом руководстве предполагается, что вы умеете работать с VS Code. Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Перейдите в каталог `cd`, в котором будет находиться проект.
* Выполните следующую команду:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?** Выберите **Да**.

  * `dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.
  * `code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

  ![Новое решение macOS](start-mvc/_static/new_project_vsmac.png)

* В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.

  ![Выбор шаблона веб-приложения macOS](start-mvc/_static/web_app_template_vsmac.png)

* В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:

  * Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.
  * Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 5.x.
  * Выберите **Далее**.

* Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.

  ![Имя проекта macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Запуск приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Нажмите клавиши CTRL+F5, чтобы запустить приложение без отладчика.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview);
  * запускает приложение.

  В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`. Стандартное имя узла для локального компьютера — `localhost`. Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.

Запуск приложения без отладки путем нажатия клавиш CTRL+F5 позволяет:

* Внесите изменения в код.
* Сохраните файл.
* Быстро обновить браузер и просмотреть изменения в коде.

Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.

![Меню отладки](start-mvc/_static/debug_menu50.png)

Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.

![IIS Express](start-mvc/_static/iis_express50.png)

Пример приложения приведен на следующем рисунке:

![Домашняя или индексная страница](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Нажмите клавиши CTRL+F5 чтобы выполнить запуск без отладчика.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * запускает [Kestrel](xref:fundamentals/servers/kestrel);
  * запускает браузер;
  * перенаправляет по адресу `https://localhost:5001`.

  В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`. Стандартное имя узла для локального компьютера — `localhost`. Localhost обслуживает только веб-запросы с локального компьютера.

Запуск приложения без отладки путем нажатия клавиш CTRL+F5 позволяет:

* Внесите изменения в код.
* Сохраните файл.
* Быстро обновить браузер и просмотреть изменения в коде.

  ![Домашняя или индексная страница](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.

  Visual Studio для Mac:

  * запускает сервер [Kestrel](xref:fundamentals/servers/index#kestrel);
  * запускает браузер;
  * перенаправляет по адресу `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.

  [!INCLUDE[](~/includes/trustCertMac.md)]

  В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`. Стандартное имя узла для локального компьютера — `localhost`. Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.

В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.

Пример приложения приведен на следующем рисунке:

![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.

> [!div class="step-by-step"]
> [Далее: Добавление контроллера](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Это первое руководство из серии материалов по веб-разработке MVC ASP.NET Core с использованием контроллеров и представлений.

Пройдя всю серию, вы создадите приложение, которое управляет базой данных фильмов и отображает ее. Вы научитесь:

> [!div class="checklist"]
> * Создание веб-приложения.
> * Добавление модели и формирование шаблона.
> * Работа с базой данных.
> * Добавление поиска и проверки.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Создание веб-приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В Visual Studio выберите **Создать проект**.

* Выберите **Веб-приложение ASP.NET Core** > **Далее**.

  ![Создание нового проекта веб-приложения ASP.NET Core](start-mvc/_static/np_2.1.png)

* Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**. Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.

  ![Настройка нового проекта](start-mvc/_static/config.png)

* Выберите **Веб-приложение (модель — представление — контроллер)** . В раскрывающихся списках выберите пункты **.NET Core** и **ASP.NET Core 3.1**, а затем щелкните **Создать**.

  ![Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core ](start-mvc/_static/new_project30.png)

В Visual Studio используется шаблон проекта по умолчанию для созданного проекта MVC. Созданный проект это:

* рабочее приложение;
* простой начальный проект.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

В этом руководстве предполагается, что вы умеете работать с VS Code. Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Измените каталог `cd` на папку, в которой будет содержаться проект.
* Выполните следующую команду:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?** Выберите **Да**.

  * `dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.
  * `code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

  ![Новое решение macOS](start-mvc/_static/new_project_vsmac.png)

* В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.

  ![Выбор шаблона веб-приложения macOS](start-mvc/_static/web_app_template_vsmac.png)

* В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:

  * Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.
  * Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.
  * Выберите **Далее**.

* Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.

  ![Имя проекта macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Запуск приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Нажмите клавиши CTRL+F5, чтобы запустить приложение без отладки.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio.

  * запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview);
  * запускает приложение.

  В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`. Стандартное имя узла для локального компьютера — `localhost`. Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.

Запуск приложения без отладки путем нажатия клавиш CTRL+F5 позволяет:

* Внесите изменения в код.
* Сохраните файл.
* Быстро обновить браузер и просмотреть изменения в коде.

Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.

![Меню отладки](start-mvc/_static/debug_menu.png)

Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.

![IIS Express](start-mvc/_static/iis_express.png)

Пример приложения приведен на следующем рисунке:

![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Нажмите клавиши CTRL+F5, чтобы запустить приложение без отладки.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * запускает [Kestrel](xref:fundamentals/servers/kestrel);
  * запускает браузер;
  * перенаправляет по адресу `https://localhost:5001`.

  В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`. Стандартное имя узла для локального компьютера — `localhost`. Localhost обслуживает только веб-запросы с локального компьютера.

Запуск приложения без отладки путем нажатия клавиш CTRL+F5 позволяет:

* Внесите изменения в код.
* Сохраните файл.
* Быстро обновить браузер и просмотреть изменения в коде.

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.

  Visual Studio для Mac: запускает сервер [Kestrel](xref:fundamentals/servers/index#kestrel), открывает браузер и перенаправляет по адресу `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.

[!INCLUDE[](~/includes/trustCertMac.md)]

В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`. Стандартное имя узла для локального компьютера — `localhost`. Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт. При запуске приложения вы увидите другой номер порта.

В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.

Пример приложения приведен на следующем рисунке:

![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.

> [!div class="step-by-step"]
> [Вперед](adding-controller.md)

::: moniker-end
