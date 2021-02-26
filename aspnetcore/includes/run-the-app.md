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
ms.openlocfilehash: d05b0f327eb1670847125e6a73ef7277ebd069b5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552727"
---
# <a name="visual-studio"></a>[<span data-ttu-id="41c1e-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41c1e-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="41c1e-102">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="41c1e-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="41c1e-103">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="41c1e-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="41c1e-104">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="41c1e-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="41c1e-105">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="41c1e-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="41c1e-106">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="41c1e-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="41c1e-107">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="41c1e-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="41c1e-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41c1e-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="41c1e-109">Нажмите клавиши **CTRL-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="41c1e-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="41c1e-110">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="41c1e-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="41c1e-111">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="41c1e-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="41c1e-112">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="41c1e-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="41c1e-113">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="41c1e-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41c1e-114">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="41c1e-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="41c1e-115">Чтобы выполнить запуск без отладчика, нажмите клавиши **OPT+CMD+RETURN** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="41c1e-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="41c1e-116">Вы также можете в строке меню выбрать **Запуск > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="41c1e-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="41c1e-117">Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="41c1e-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---
