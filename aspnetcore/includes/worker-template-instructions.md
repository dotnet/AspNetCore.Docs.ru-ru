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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551345"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Создайте новый проект.
1. Выберите **службу рабочей роли**. Выберите **Далее**.
1. В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию. Щелкните **Создать**.
1. В диалоговом окне **Создать службу рабочей роли** выберите **Создать**.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. Создайте новый проект.
1. В разделе **.NET Core** на боковой панели выберите **Приложение**.
1. В разделе **ASP.NET Core** выберите **Рабочая роль**. Выберите **Далее**.
1. Для параметра **Требуемая версия .NET Framework** выберите **.NET Core 3.0** или более поздней версии. Выберите **Далее**.
1. Введите имя в поле **Имя проекта**. Щелкните **Создать**.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Используйте шаблон службы рабочей роли (`worker`) с командой [dotnet new](/dotnet/core/tools/dotnet-new) из командной оболочки. В приведенном ниже примере создается приложение службы рабочей роли с именем `ContosoWorker`. Папка для приложения `ContosoWorker` создается автоматически при выполнении команды.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
