---
title: Использование SignalR для ASP.NET Core с размещенным приложением Blazor WebAssembly
author: guardrex
description: Создание приложения чата, которое использует SignalR для ASP.NET Core с Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797357"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a>Использование SignalR для ASP.NET Core с размещенным приложением Blazor WebAssembly

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

В этом руководстве описаны основы того, как с помощью SignalR и Blazor WebAssembly создать приложение, работающее в режиме реального времени. Вы научитесь:

> [!div class="checklist"]
> * создавать проект размещенного приложения Blazor WebAssembly;
> * Добавление клиентской библиотеки SignalR
> * добавлять концентратор SignalR;
> * добавлять службы и конечную точку SignalR для концентратора SignalR;
> * добавлять код компонента Razor для чата.

Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16.8 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* [Visual Studio для Mac 8.8 или более поздней версии](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16.6 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* [Visual Studio для Mac 8.6 или более поздней версии](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>Создание проекта размещенного приложения Blazor WebAssembly

Следуйте указаниям по выбору инструментов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Требуются Visual Studio 16.8 или более поздней версии и пакет SDK для .NET Core 5.0.0 или более поздней версии.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Требуются Visual Studio 16.6 или более поздней версии и пакет SDK для .NET Core 3.1.300 или более поздней версии.

::: moniker-end

1. Создайте новый проект.

1. Выберите **Приложение Blazor** и нажмите кнопку **Далее**.

1. Введите `BlazorSignalRApp` в поле **Имя проекта**. Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое. Выберите **Создать**.

1. Выберите шаблон **приложения Blazor WebAssembly** .

1. В разделе **Дополнительно** установите флажок **Размещенный проект ASP.NET Core**.

1. Нажмите кнопку **создания**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. В командной оболочке выполните следующую команду:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Откройте папку проекта приложения в Visual Studio Code.

1. Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**. Visual Studio Code автоматически добавляет папку `.vscode` с созданными файлами `launch.json` и `tasks.json`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. Установите последнюю версию [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и выполните следующие действия.

1. Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.

1. На боковой панели выберите **Интернет и консоль** > **Приложение**.

1. Выберите шаблон **приложения Blazor WebAssembly** . Выберите **Далее**.

1. Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**. Установите флажок **Размещенный проект ASP.NET Core**. Выберите **Далее**.

1. В поле **Имя проекта** присвойте имя приложению `BlazorSignalRApp`. Нажмите кнопку **создания**.

   При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить. Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.

1. Откройте проект. Для этого перейдите к его папке и откройте файл решения проекта (`.sln`).

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

В командной оболочке выполните следующую команду:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>Добавление клиентской библиотеки SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. В **обозревателе решений** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.

1. Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.

1. Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.

1. В результатах поиска выберите пакет [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и нажмите **Установить**.

1. Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.

1. Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. На боковой панели **Решение** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.

1. Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.

1. Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.

1. В результатах поиска установите флажок рядом с пакетом [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и выберите **Добавить пакет**.

1. Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

В командной оболочке выполните следующие команды:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Добавление пакета System.Text.Encodings.Web

Из-за проблемы с разрешением пакета при использовании [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 в приложении ASP.NET Core 3,1 для проекта `BlazorSignalRApp.Server` требуется ссылка на пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web). Проблема будет устранена в будущем выпуске .NET 5 с исправлениями. См. дополнительные сведения о том, как [System.Text.Json определяет netcoreapp3.0 без зависимостей (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).

Чтобы добавить [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) в проект `BlazorSignalRApp.Server` размещенного решения Blazor ASP.NET Core 3.1, следуйте указаниям по выбору инструментов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. В **обозревателе решений** щелкните проект `BlazorSignalRApp.Server` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.

1. Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.

1. Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.

1. В результатах поиска выберите пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) и нажмите **Установить**.

1. Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.

1. Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. На боковой панели **Решение** щелкните проект `BlazorSignalRApp.Server` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.

1. Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.

1. Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.

1. В результатах поиска установите флажок рядом с пакетом [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) и выберите **Добавить пакет**.

1. Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

В командной оболочке выполните следующую команду:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a>добавлять концентратор SignalR;

В проекте `BlazorSignalRApp.Server` создайте папку `Hubs` (plural) и добавьте следующий класс `ChatHub` (`Hubs/ChatHub.cs`):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>Добавление служб и конечной точки для концентратора SignalR

1. В проекте `BlazorSignalRApp.Server` откройте файл `Startup.cs`.

1. Добавьте пространство имен для класса `ChatHub` в начало файла:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. В `Startup.Configure`:

   * Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.
   * Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. В `Startup.Configure`:

   * Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.
   * Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>добавлять код компонента Razor для чата.

1. В проекте `BlazorSignalRApp.Client` откройте файл `Pages/Index.razor`.

::: moniker range=">= aspnetcore-5.0"

1. Замените разметку следующим кодом:

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Замените разметку следующим кодом:

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Запустите приложение

Следуйте указаниям по выбору инструментов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. В **обозревателе решений** выберите проект `BlazorSignalRApp.Server`. Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.

1. Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения. Имя и сообщение отображаются на обеих страницах мгновенно:

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. Когда VS Code предлагает создать профиль запуска для серверного приложения (`.vscode/launch.json`), запись `program` выглядит примерно так, как показано ниже, чтобы указать сборку приложения (`{APPLICATION NAME}.Server.dll`):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Когда VS Code предлагает создать профиль запуска для серверного приложения (`.vscode/launch.json`), запись `program` выглядит примерно так, как показано ниже, чтобы указать сборку приложения (`{APPLICATION NAME}.Server.dll`):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.

1. Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения. Имя и сообщение отображаются на обеих страницах мгновенно:

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. На боковой панели **Решение** выберите проект `BlazorSignalRApp.Server`. Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.

1. Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения. Имя и сообщение отображаются на обеих страницах мгновенно:

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

1. В командной оболочке выполните следующие команды:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения. Имя и сообщение отображаются на обеих страницах мгновенно:

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как:

> [!div class="checklist"]
> * создавать проект размещенного приложения Blazor WebAssembly;
> * Добавление клиентской библиотеки SignalR
> * добавлять концентратор SignalR;
> * добавлять службы и конечную точку SignalR для концентратора SignalR;
> * добавлять код компонента Razor для чата.

Дополнительные сведения о создании приложений Blazor см. в документации по Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [Проверка подлинности маркера носителя с помощью сервера Identity, WebSockets и отправляемыми сервером событиями](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:signalr/introduction>
* [Согласование независимо от источника для проверки подлинности для SignalR](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
