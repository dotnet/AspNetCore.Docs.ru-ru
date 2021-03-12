---
title: Учебник. Начало работы с Razor Pages в ASP.NET Core
author: rick-anderson
description: В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.
ms.author: riande
ms.date: 09/15/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 61ff4edfaedb22ea88d6bb0be2390ff734879942
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588442"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Учебник. Начало работы с Razor Pages в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-5.0"
В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.

Дополнительные сведения, предназначенные для разработчиков, которые знакомы с контроллерами и представлениями, см. в статье [Введение в Razor Pages](xref:razor-pages/index).

Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.  

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).

В этом учебнике рассмотрены следующие задачи.

> [!div class="checklist"]
> * Создание веб-приложения Razor Pages.
> * Запустите приложение.
> * Анализ файлов проекта.

Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, улучшение которого описано в последующих руководствах.

![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Создание веб-приложения Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Запустите Visual Studio и щелкните **Создать проект** Дополнительные сведения см. в статье [Создание проекта в Visual Studio](/visualstudio/ide/create-new-project).

   ![Создание проекта в начальном окне](razor-pages-start/_static/5/start-window-create-new-project.png)

1. В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** и нажмите **Далее**.

    ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. В диалоговом окне **Настроить новый проект** введите `RazorPagesMovie` в поле **Имя проекта**. Важно присвоить проекту имя *RazorPagesMovie*, включая сопоставление регистра букв, чтобы пространство имени соответствовало при копировании и вставке примера кода.

1. Нажмите кнопку **создания**.

    ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

1. В диалоговом окне **Создайте веб-приложение ASP.NET Core** сделайте следующее:
    1. В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.
    1. **Веб-приложение**.
    1. **Create** (создать).

     ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/5/npx.png)

    Создается следующий начальный проект:

    ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).

1. Перейдите в каталог `cd`, в котором находится проект.

1. Выполните следующие команды:

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.
   * Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. Щелкните **Файл** > **Новое решение**.

    ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.

    ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. В диалоговом окне **Configure the new Web Application** (Настройка нового веб-приложения):

    1. Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.
    1. Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию .NET 5.x.
    1. Выберите **Далее**.

1. Укажите название проекта *RazorPagesMovie* и нажмите кнопку **Создать**.

    ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Запуск приложения

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Анализ файлов проекта

Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.

### <a name="pages-folder"></a>Папка Pages

Содержит страницы Razor и вспомогательные файлы. Каждая страница Razor — это пара файлов.

* Файл *.cshtml* с разметкой HTML и кодом C# использует синтаксис Razor.
* Файл *.cshtml.cs* с кодом C#, который обрабатывает события страницы.

Имена вспомогательных файлов начинаются с символа подчеркивания. Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц. Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы. Для получения дополнительной информации см. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Папка wwwroot

Содержит статические ресурсы, такие как HTML-файлы, файлы JavaScript и CSS-файлы. Для получения дополнительной информации см. <xref:fundamentals/static-files>.

### appsettings.json

Содержит данные конфигурации, например строки подключения. Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Содержит точку входа для приложения. Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

содержит код, задающий поведение приложения. Для получения дополнительной информации см. <xref:fundamentals/startup>.

## <a name="next-steps"></a>Следующие шаги

> [!div class="step-by-step"]
> [Далее: Добавление модели](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.

Дополнительные сведения, предназначенные для разработчиков, которые знакомы с контроллерами и представлениями, см. в статье [Введение в Razor Pages](xref:razor-pages/index).

Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.  

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).

В этом учебнике рассмотрены следующие задачи.

> [!div class="checklist"]
> * Создание веб-приложения Razor Pages.
> * Запустите приложение.
> * Анализ файлов проекта.

Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.

![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Создание веб-приложения Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.
* Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.
  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Присвойте проекту имя **RazorPagesMovie**. Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.
  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

* Выберите в раскрывающемся списке пункт **ASP.NET Core 3.1**, затем — **Веб-приложение** и нажмите кнопку **Создать**.

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/3/npx.png)

  Создается следующий начальный проект:

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Перейдите в каталог `cd`, в котором находится проект.

* Выполните следующие команды:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.
  * Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.

* Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?** Выберите ответ **Да**.

  Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

  ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.

  ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* В диалоговом окне **Configure the new Web Application** (Настройка нового веб-приложения):

  * Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.
  * Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.

  Выберите **Далее**.

* Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.

  ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Запуск приложения

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Анализ файлов проекта

Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.

### <a name="pages-folder"></a>Папка Pages

Содержит страницы Razor и вспомогательные файлы. Каждая страница Razor — это пара файлов.

* Файл *.cshtml* с разметкой HTML и кодом C# использует синтаксис Razor.
* Файл *.cshtml.cs* с кодом C#, который обрабатывает события страницы.

Имена вспомогательных файлов начинаются с символа подчеркивания. Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц. Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы. Для получения дополнительной информации см. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Папка wwwroot

Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы. Для получения дополнительной информации см. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Содержит данные конфигурации, например строки подключения. Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Содержит точку входа для программы. Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

содержит код, задающий поведение приложения. Для получения дополнительной информации см. <xref:fundamentals/startup>.

## <a name="next-steps"></a>Следующие шаги

> [!div class="step-by-step"]
> [Далее: Добавление модели](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Это первый учебник из серии. В этой [серии](xref:tutorials/razor-pages/index) приводятся основные сведения о сборке веб-приложения Razor Pages в ASP.NET Core.

Дополнительные сведения, предназначенные для разработчиков, которые знакомы с контроллерами и представлениями, см. в статье [Введение в Razor Pages](xref:razor-pages/index).

Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.  

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).

В этом учебнике рассмотрены следующие задачи.

> [!div class="checklist"]
> * Создание веб-приложения Razor Pages.
> * Запустите приложение.
> * Анализ файлов проекта.

Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.

![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Создание веб-приложения Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.

* Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Присвойте проекту имя **RazorPagesMovie**. Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

* Выберите в раскрывающемся списке пункт **ASP.NET Core 2.2**, затем — **Веб-приложение** и нажмите кнопку **Создать**.

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Создается следующий начальный проект:

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Перейдите в каталог `cd`, в котором находится проект.

* Выполните следующие команды:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.
  * Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.

* Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?** Выберите ответ **Да**.

  Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.

* В диалоговом окне **Configure the new Web Application** (Настройка нового веб-приложения):

  * Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.
  * Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 2.x.

  Выберите **Далее**.

* Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Запуск приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.

  Запуск приложения с помощью клавиш <kbd>CTRL+F5</kbd> (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде. Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение. В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`. Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера. Localhost обслуживает только веб-запросы с локального компьютера. Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.

* На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.

  Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/homeGDPR2.2.png)

  На следующем рисунке показано приложение после согласия на отслеживание.

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Нажмите клавиши <kbd>CTRL+F5</kbd>, чтобы выполнить запуск без отладчика.

  Запуск приложения с помощью клавиш <kbd>CTRL+F5</kbd> (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде. Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.

  Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), затем — браузер и переходит к `http://localhost:5001`. В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`. Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера. Localhost обслуживает только веб-запросы с локального компьютера.

* На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.

  Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/homeGDPR2.2.png)

  На следующем рисунке показано приложение после принятия отслеживания:

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Нажмите клавиши **Cmd-Opt-F5**, чтобы выполнить запуск без отладчика.

  Запуск приложения с помощью клавиш <kbd>Cmd+Opt+F5</kbd> (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде. Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.

  Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), затем — браузер и переходит к `http://localhost:5001`.

* На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.

  Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/homeGDPR2.2_safari.png)

  На следующем рисунке показано приложение после согласия на отслеживание.

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Анализ файлов проекта

Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.

### <a name="pages-folder"></a>Папка Pages

Содержит страницы Razor и вспомогательные файлы. Каждая страница Razor — это пара файлов.

* Файл *.cshtml* с разметкой HTML и кодом C# использует синтаксис Razor.
* Файл *.cshtml.cs* с кодом C#, который обрабатывает события страницы.

Имена вспомогательных файлов начинаются с символа подчеркивания. Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц. Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы. Для получения дополнительной информации см. <xref:mvc/views/layout>.

Razor Pages являются производными от класса `PageModel`. Как правило, класс, производный от `PageModel`, называется `<PageName>Model`.

### <a name="wwwroot-folder"></a>Папка wwwroot

Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы. Для получения дополнительной информации см. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Содержит данные конфигурации, например строки подключения. Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Содержит точку входа для программы. Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Содержит код, который настраивает поведение приложения, например, требуется ли согласие для файлов cookie. Для получения дополнительной информации см. <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="step-by-step"]
> [Далее: Добавление модели](xref:tutorials/razor-pages/model)

::: moniker-end
