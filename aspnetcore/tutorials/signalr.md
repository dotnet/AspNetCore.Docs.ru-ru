---
title: Начало работы с SignalR ASP.NET Core
author: bradygaster
description: В этом учебнике рассказывается как создать приложение чата, которое использует SignalR для ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 1c77648f809562389667da452bdbf3f25f67c558
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689322"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a>Учебник. Начало работы с SignalR ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

В этом учебнике описаны основы создания приложения, работающего в режиме реального времени, с помощью SignalR. Вы научитесь:

> [!div class="checklist"]
> * Создайте веб-проект.
> * добавлять клиентскую библиотеку SignalR.
> * создавать концентратор SignalR.
> * настраивать проект для использования SignalR;
> * Добавлять код для отправки сообщений из любого клиента всем подключенным клиентам.

В итоге вы получите работающее приложение чата:

![SignalR Пример приложения](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a>Создание проекта веб-приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

  * В меню выберите **Файл > Создать проект**.
  * В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** и нажмите **Далее**.
  * В диалоговом окне **Настроить новый проект** укажите имя проекта *SignalRChat*, а затем выберите **Создать**.
  * В диалоговом окне **Создание веб-приложения ASP.NET Core** выберите платформы **.NET Core** и **ASP.NET Core 3.1**.
  * Выберите **Веб-приложение**, чтобы создать проект, который использует Razor Pages, и нажмите **Создать**.

  ![Диалоговое окно создания проекта в Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

  * Откройте [интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal) и перейдите в папку, в которой будет создаваться папка нового проекта.
  * Выполните следующие команды:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

  * В меню выберите **Файл > Создать решение**.
  * Выберите **.NET Core > Приложение > Веб-приложение** (не выбирайте **Веб-приложение (модель — представление — контроллер)** ), а затем выберите **Далее**.
  * Убедитесь, что для параметра **Требуемая версия .NET Framework** указано **.NET Core 3.1**, а затем нажмите кнопку **Далее**.
  * Присвойте проекту имя *SignalRChat* и нажмите кнопку **Создать**.

---

## <a name="add-the-no-locsignalr-client-library"></a>Добавление клиентской библиотеки SignalR

Серверная библиотека SignalR входит в состав общей платформы ASP.NET Core 3.1. Клиентская библиотека JavaScript не добавляется в проект автоматически. В рамках этого руководства вы будете использовать диспетчер библиотек (LibMan), чтобы получить клиентскую библиотеку из *unpkg*. unpkg — это сеть доставки содержимого, которая позволяет доставить любое содержимое из npm (диспетчера пакетов Node.js).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

  * В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Добавить** > **Клиентская библиотека**.
  * В диалоговом окне **Add Client-Side Library** (Добавить клиентскую библиотеку) для параметра **Поставщик** выберите **unpkg**.
  * Для параметра **Библиотека** введите `@microsoft/signalr@latest`.
  * Щелкните **Choose specific files** (Выбрать определенные файлы), разверните папку *dist/browser* и выберите *signalr.js* и *signalr.min.js*.
  * В поле **Целевое расположение** укажите *wwwroot/js/signalr/* и нажмите кнопку **Установить**.

  ![Диалоговое окно добавления клиентской библиотеки — выбор библиотеки](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan создает папку *wwwroot/js/signalr* и копирует в нее выбранные файлы.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

  * В интегрированном терминале выполните следующую команду, чтобы установить LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

  * Выполните команду ниже, чтобы получить клиентскую библиотеку SignalR, используя LibMan. Возможно, придется подождать несколько секунд, прежде чем появятся выходные данные.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  В параметрах указываются следующие свойства:
  * использование поставщика unpkg;
  * копирование файлов в назначение *wwwroot/js/signalr*;
  * копирование только выбранных файлов.

  Выходные данные выглядят примерно так, как в следующем примере:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

  * В **терминале** выполните следующую команду, чтобы установить LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

  * Перейдите в папку проекта (где расположен файл *SignalRChat.csproj*).

  * Выполните команду ниже, чтобы получить клиентскую библиотеку SignalR, используя LibMan.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  В параметрах указываются следующие свойства:
  * использование поставщика unpkg;
  * копирование файлов в назначение *wwwroot/js/signalr*;
  * копирование только выбранных файлов.

  Выходные данные выглядят примерно так, как в следующем примере:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a>Создание концентратора SignalR

*hub* — это класс, который служит в качестве конвейера высокого уровня для обработки взаимодействия между клиентом и сервером.

  * В папке проекта SignalRChat создайте папку *Hubs*.
  * В папке *Hubs* создайте файл *ChatHub.cs* со следующим кодом:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  Класс `ChatHub` наследуется от класса SignalR `Hub`. Класс `Hub` управляет подключениями, группами и обменом сообщениями.

  Метод `SendMessage` может вызываться подключенным клиентом, чтобы отправить сообщение всем клиентам. Далее в этом учебника показан клиентский код JavaScript, который вызывает метод. Код SignalR является асинхронным, поэтому обеспечивает максимальную масштабируемость.

## <a name="configure-no-locsignalr"></a>Настройка SignalR

Сервер SignalR должен быть настроен для передачи запросов SignalR к SignalR.

* Добавьте следующий выделенный код в файл *Startup.cs*.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  В результате SignalR будет добавлен в системы внедрения зависимостей и маршрутизации ASP.NET Core.

## <a name="add-no-locsignalr-client-code"></a>Добавление клиентского кода SignalR

* Замените содержимое в файле *Pages\Index.cshtml* следующим кодом:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  Предыдущий код:

  * Создает текстовые поля для имени и текста сообщения и кнопку отправки.
  * Создает список с `id="messagesList"` для отображения сообщений, полученных от концентратора SignalR.
  * Содержит ссылки на скрипты для SignalR и код приложения *chat.js*, который создается в следующем шаге.

* В папке *wwwroot/js* создайте файл *chat.js* со следующим кодом:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  Предыдущий код:

  * Создает и запускает подключение.
  * Добавляет к кнопке отправки обработчик, который отправляет сообщения в концентратор.
  * Добавляет к объекту подключения обработчик, который получает сообщения из концентратора и добавляет их в список.

## <a name="run-the-app"></a>Запуск приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Нажмите клавиши **CTRL+F5**, чтобы запустить приложение без отладки.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* В интегрированном терминале выполните следующую команду:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

  * В меню выберите **Запуск > Запуск без отладки**.

---

  * Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.
  * Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить сообщение**.
  Имя и сообщение отображаются на обеих страницах мгновенно.

  ![SignalR Пример приложения](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Если приложение не работает, откройте средства разработчика для браузера (F12) и перейдите в консоль. Вы можете увидеть ошибки, связанные с вашим кодом HTML и JavaScript. Предположим, вы поместили *signalr.js* не в ту папку, которую указали. В этом случае ссылка на этот файл не будет работать, и вы увидите сообщение об ошибке 404 в консоли.
>   ![ошибка: signalr.js не найден](signalr/_static/3.x/f12-console.png)
> * Если возникает ошибка ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY в Chrome, выполните эти команды, чтобы обновить сертификат разработки:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этом учебнике описаны основы создания приложения, работающего в режиме реального времени, с помощью SignalR. Вы научитесь: 

> [!div class="checklist"]  
> * Создайте веб-проект.   
> * добавлять клиентскую библиотеку SignalR.   
> * создавать концентратор SignalR. 
> * настраивать проект для использования SignalR; 
> * Добавлять код для отправки сообщений из любого клиента всем подключенным клиентам.  
В итоге вы получите приложение для работы с чатом: ![SignalR Пример приложения](signalr/_static/2.x/signalr-get-started-finished.png)   

## <a name="prerequisites"></a>Предварительные требования    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Создайте веб-проект. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* В меню выберите **Файл > Создать проект**. 

* В диалоговом окне **Новый проект** выберите **Установленные > Visual C# > Веб > Веб-приложение ASP.NET Core**. Присвойте проекту имя *SignalRChat*.   

  ![Диалоговое окно создания проекта в Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Выберите **Веб-приложение**, чтобы создать проект, который использует Razor Pages.   

* Выберите целевую платформу **.NET Core**, **ASP.NET Core 2.2** и нажмите кнопку **ОК**.    

  ![Диалоговое окно создания проекта в Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Откройте [интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal) и перейдите в папку, в которой будет создаваться папка нового проекта.  

* Выполните следующие команды:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)   

* В меню выберите **Файл > Создать решение**.    

* Выберите **.NET Core > Приложение > Веб-приложение ASP.NET Core** (не выбирайте **Веб-приложение ASP.NET Core (MVC)** ).  

* Выберите **Далее**.  

* Присвойте проекту имя *SignalRChat* и нажмите кнопку **Создать**. 

--- 

## <a name="add-the-no-locsignalr-client-library"></a>Добавление клиентской библиотеки SignalR 

Библиотека сервера SignalR входит в состав метапакета `Microsoft.AspNetCore.App`. Клиентская библиотека JavaScript не добавляется в проект автоматически. В рамках этого руководства вы будете использовать диспетчер библиотек (LibMan), чтобы получить клиентскую библиотеку из *unpkg*. unpkg — это сеть доставки содержимого, которая позволяет доставить любое содержимое из npm (диспетчера пакетов Node.js).   

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Добавить** > **Клиентская библиотека**.  

* В диалоговом окне **Add Client-Side Library** (Добавить клиентскую библиотеку) для параметра **Поставщик** выберите **unpkg**. 

* В поле **Библиотека** введите `@microsoft/signalr@3` и выберите последнюю версию, но не предварительную.  

  ![Диалоговое окно добавления клиентской библиотеки — выбор библиотеки](signalr/_static/2.x/libman1.png)   

* Щелкните **Choose specific files** (Выбрать определенные файлы), разверните папку *dist/browser* и выберите *signalr.js* и *signalr.min.js*. 

* В поле **Целевое расположение** укажите *wwwroot/lib/signalr/* и нажмите кнопку **Установить**.    

  ![Диалоговое окно добавления клиентской библиотеки — выбор файлов и назначения](signalr/_static/2.x/libman2.png) 

  LibMan создает папку *wwwroot/lib/signalr* и копирует в нее выбранные файлы.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* В интегрированном терминале выполните следующую команду, чтобы установить LibMan.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Выполните команду ниже, чтобы получить клиентскую библиотеку SignalR, используя LibMan. Возможно, придется подождать несколько секунд, прежде чем появятся выходные данные. 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  В параметрах указываются следующие свойства: 
  * использование поставщика unpkg; 
  * копирование файлов в назначение *wwwroot/lib/signalr*;    
  * копирование только выбранных файлов.  

  Выходные данные выглядят примерно так, как в следующем примере:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)   

* В **терминале** выполните следующую команду, чтобы установить LibMan. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Перейдите в папку проекта (где расположен файл *SignalRChat.csproj*).   

* Выполните команду ниже, чтобы получить клиентскую библиотеку SignalR, используя LibMan.    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  В параметрах указываются следующие свойства: 
  * использование поставщика unpkg; 
  * копирование файлов в назначение *wwwroot/lib/signalr*;    
  * копирование только выбранных файлов.  

  Выходные данные выглядят примерно так, как в следующем примере:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a>Создание концентратора SignalR   

*hub* — это класс, который служит в качестве конвейера высокого уровня для обработки взаимодействия между клиентом и сервером.   

* В папке проекта SignalRChat создайте папку *Hubs*.  

* В папке *Hubs* создайте файл *ChatHub.cs* со следующим кодом: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  Класс `ChatHub` наследуется от класса SignalR `Hub`. Класс `Hub` управляет подключениями, группами и обменом сообщениями.  

  Метод `SendMessage` может вызываться подключенным клиентом, чтобы отправить сообщение всем клиентам. Далее в этом учебника показан клиентский код JavaScript, который вызывает метод. Код SignalR является асинхронным, поэтому обеспечивает максимальную масштабируемость.    

## <a name="configure-no-locsignalr"></a>Настройка SignalR  

Сервер SignalR должен быть настроен для передачи запросов SignalR к SignalR.    

* Добавьте следующий выделенный код в файл *Startup.cs*.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  В результате SignalR будет добавлен в систему внедрения зависимостей ASP.NET Core и конвейер ПО промежуточного слоя.  

## <a name="add-no-locsignalr-client-code"></a>Добавление клиентского кода SignalR    

* Замените содержимое в файле *Pages\Index.cshtml* следующим кодом:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  Предыдущий код:   

  * Создает текстовые поля для имени и текста сообщения и кнопку отправки.  
  * Создает список с `id="messagesList"` для отображения сообщений, полученных от концентратора SignalR.   
  * Содержит ссылки на скрипты для SignalR и код приложения *chat.js*, который создается в следующем шаге.    

* В папке *wwwroot/js* создайте файл *chat.js* со следующим кодом:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  Предыдущий код:   

  * Создает и запускает подключение.    
  * Добавляет к кнопке отправки обработчик, который отправляет сообщения в концентратор. 
  * Добавляет к объекту подключения обработчик, который получает сообщения из концентратора и добавляет их в список.  

## <a name="run-the-app"></a>Запуск приложения  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Нажмите клавиши **CTRL+F5**, чтобы запустить приложение без отладки.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* В интегрированном терминале выполните следующую команду:    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* В меню выберите **Запуск > Запуск без отладки**.

---

* Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.

* Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить сообщение**.  

  Имя и сообщение отображаются на обеих страницах мгновенно.   

  ![SignalR Пример приложения](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Если приложение не работает, откройте средства разработчика для браузера (F12) и перейдите в консоль. Вы можете увидеть ошибки, связанные с вашим кодом HTML и JavaScript. Предположим, вы поместили *signalr.js* не в ту папку, которую указали. В этом случае ссылка на этот файл не будет работать, и вы увидите сообщение об ошибке 404 в консоли.   
> ![ошибка: signalr.js не найден](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Дополнительные ресурсы 
* [Версия учебника на YouTube](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
