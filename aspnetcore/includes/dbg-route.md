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
ms.openlocfilehash: 087c5a7dd20bc653a05c337dde905716032e00b3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551395"
---
## <a name="debug-diagnostics"></a><span data-ttu-id="ab010-101">Отладка диагностики</span><span class="sxs-lookup"><span data-stu-id="ab010-101">Debug diagnostics</span></span>

<span data-ttu-id="ab010-102">Для подробного вывода диагностики построения маршрутов задайте для `Logging:LogLevel:Microsoft` значение `Debug`.</span><span class="sxs-lookup"><span data-stu-id="ab010-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="ab010-103">Например, в среде разработки задайте уровень ведения журнала в *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="ab010-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
