---
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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551282"
---
::: moniker range=">= aspnetcore-3.0"

Запустите программу Identity формирования шаблонов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **Обозреватель решений** щелкните правой кнопкой мыши проект > **Добавить** > **Новый** шаблонный элемент.
* В левой области диалогового окна **Добавление шаблона** выберите **Identity** > **Добавить**.
* В диалоговом окне **Добавить Identity** выберите нужные параметры.
  * Выберите существующую страницу макета, чтобы файл макета не перезаписывался неверной разметкой. При выборе существующего файла *\_ Layout. cshtml* он **не** перезаписывается. Пример:
    * `~/Pages/Shared/_Layout.cshtml` для Razor страниц или Blazor Server проектов с Razor инфраструктурой существующих страниц
    * `~/Views/Shared/_Layout.cshtml` для проектов или Blazor Server проектов MVC с существующей инфраструктурой MVC
* Чтобы использовать существующий контекст данных, выберите по меньшей мере один файл для переопределения. Необходимо выбрать по крайней мере один файл для добавления контекста данных.
  * Выберите класс контекста данных.
  * Выберите **Добавить**.
* Чтобы создать новый контекст пользователя и, возможно, создать настраиваемый класс пользователя для Identity :
  * Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**. Примите значение по умолчанию или укажите класс (например, `MyApplication.Data.ApplicationDbContext` ).
  * Выберите **Добавить**.

Примечание. Если вы создаете новый контекст пользователя, вам не нужно выбирать файл для переопределения.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Если вы ранее не устанавливали шаблон ASP.NET Core, установите его сейчас:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Добавьте необходимые ссылки на пакет NuGet в файл проекта (*CSPROJ*). Выполните следующие команды в каталоге проекта:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Выполните следующую команду, чтобы вывести список Identity параметров шаблона:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

В папке проекта запустите Identity механизм формирования шаблонов с нужными параметрами. Например, чтобы настроить удостоверение с пользовательским интерфейсом по умолчанию и минимальным числом файлов, выполните следующую команду. Используйте правильное полное имя для контекста базы данных:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

В PowerShell используется точка с запятой в качестве разделителя команд. При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки. Пример:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

При запуске Identity механизма формирования шаблонов без указания `--files` флага или `--useDefaultUI` флага все доступные Identity страницы пользовательского интерфейса будут созданы в проекте.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Запустите программу Identity формирования шаблонов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **Обозреватель решений** щелкните правой кнопкой мыши проект > **Добавить** > **Новый** шаблонный элемент.
* В левой области диалогового окна **Добавление шаблона** выберите **Identity** > **Добавить**.
* В диалоговом окне **Добавить Identity** выберите нужные параметры.
  * Выберите существующую страницу макета, или файл макета будет перезаписан с неверной разметкой. При выборе существующего файла *\_ Layout. cshtml* он **не** перезаписывается. Пример:
    * `~/Pages/Shared/_Layout.cshtml` для Razor страниц
    * `~/Views/Shared/_Layout.cshtml` для проектов MVC
* Чтобы использовать существующий контекст данных, выберите по меньшей мере один файл для переопределения. Необходимо выбрать по крайней мере один файл для добавления контекста данных.
  * Выберите класс контекста данных.
  * Выберите **Добавить**.
* Чтобы создать новый контекст пользователя и, возможно, создать настраиваемый класс пользователя для Identity :
  * Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**. Примите значение по умолчанию или укажите класс (например, `MyApplication.Data.ApplicationDbContext` ).
  * Выберите **Добавить**.

Примечание. Если вы создаете новый контекст пользователя, вам не нужно выбирать файл для переопределения.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Если вы ранее не устанавливали шаблон ASP.NET Core, установите его сейчас:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Добавьте ссылку на пакет в [Microsoft. VisualStudio. Web. стратегию. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) в файл проекта (*CSPROJ*). Выполните следующие команды в каталоге проекта:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Выполните следующую команду, чтобы вывести список Identity параметров шаблона:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

В папке проекта запустите Identity механизм формирования шаблонов с нужными параметрами. Например, чтобы настроить удостоверение с пользовательским интерфейсом по умолчанию и минимальным числом файлов, выполните следующую команду. Используйте правильное полное имя для контекста базы данных:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

В PowerShell используется точка с запятой в качестве разделителя команд. При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки. Пример:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

При запуске Identity механизма формирования шаблонов без указания `--files` флага или `--useDefaultUI` флага все доступные Identity страницы пользовательского интерфейса будут созданы в проекте.

---

::: moniker-end
