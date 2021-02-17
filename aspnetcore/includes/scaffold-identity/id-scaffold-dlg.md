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
<span data-ttu-id="c82f5-101">Запустите программу Identity формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="c82f5-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c82f5-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c82f5-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c82f5-103">В **Обозреватель решений** щелкните правой кнопкой мыши проект > **Добавить**  >  **Новый** шаблонный элемент.</span><span class="sxs-lookup"><span data-stu-id="c82f5-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c82f5-104">В левой области диалогового окна **Добавление нового шаблона элемента** выберите **Identity**  >  **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c82f5-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c82f5-105">В диалоговом окне **Добавить Identity** выберите нужные параметры.</span><span class="sxs-lookup"><span data-stu-id="c82f5-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="c82f5-106">Выберите существующую страницу макета, или файл макета будет перезаписан с неверной разметкой:</span><span class="sxs-lookup"><span data-stu-id="c82f5-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="c82f5-107">`~/Pages/Shared/_Layout.cshtml` для Razor страниц</span><span class="sxs-lookup"><span data-stu-id="c82f5-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="c82f5-108">`~/Views/Shared/_Layout.cshtml` для проектов MVC</span><span class="sxs-lookup"><span data-stu-id="c82f5-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="c82f5-109">Blazor Server приложения, созданные на основе Blazor Server шаблона ( `blazorserver` ), не настраиваются для Razor страниц или MVC по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c82f5-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="c82f5-110">Оставьте запись страницы макета пустой.</span><span class="sxs-lookup"><span data-stu-id="c82f5-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="c82f5-111">Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="c82f5-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="c82f5-112">Примите значение по умолчанию или укажите класс (например, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="c82f5-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="c82f5-113">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c82f5-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c82f5-114">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="c82f5-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c82f5-115">Если вы ранее не устанавливали шаблон ASP.NET Core, установите его сейчас:</span><span class="sxs-lookup"><span data-stu-id="c82f5-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c82f5-116">Добавьте необходимые ссылки на пакет NuGet в файл проекта (*CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="c82f5-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="c82f5-117">Выполните следующие команды в каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="c82f5-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="c82f5-118">Выполните следующую команду, чтобы вывести список Identity параметров шаблона:</span><span class="sxs-lookup"><span data-stu-id="c82f5-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="c82f5-119">В папке проекта запустите Identity механизм формирования шаблонов с нужными параметрами.</span><span class="sxs-lookup"><span data-stu-id="c82f5-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="c82f5-120">Например, чтобы настроить удостоверение с пользовательским интерфейсом по умолчанию и минимальным числом файлов, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="c82f5-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
