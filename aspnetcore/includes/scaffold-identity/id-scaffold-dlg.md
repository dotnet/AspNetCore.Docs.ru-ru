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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552659"
---
Запустите программу Identity формирования шаблонов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **Обозреватель решений** щелкните правой кнопкой мыши проект > **Добавить**  >  **Новый** шаблонный элемент.
* В левой области диалогового окна **Добавление нового шаблона элемента** выберите **Identity**  >  **Добавить**.
* В диалоговом окне **Добавить Identity** выберите нужные параметры.
  * Выберите существующую страницу макета, или файл макета будет перезаписан с неверной разметкой:
    * `~/Pages/Shared/_Layout.cshtml` для Razor страниц
    * `~/Views/Shared/_Layout.cshtml` для проектов MVC
    * Blazor Server приложения, созданные на основе Blazor Server шаблона ( `blazorserver` ), не настраиваются для Razor страниц или MVC по умолчанию. Оставьте запись страницы макета пустой.
  * Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**. Примите значение по умолчанию или укажите класс (например, `MyApplication.Data.ApplicationDbContext` ).
* Выберите **Добавить**.

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

В папке проекта запустите Identity механизм формирования шаблонов с нужными параметрами. Например, чтобы настроить удостоверение с пользовательским интерфейсом по умолчанию и минимальным числом файлов, выполните следующую команду:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
