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
ms.openlocfilehash: 7caea4089d3624d4c02db4b8adbe9edb73f3d31a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552554"
---
> [!NOTE]
> <span data-ttu-id="a3d55-101">Если запрашиваемый язык и региональные параметры не поддерживаются, перед запуском ASP.NET Core 3.0 веб-приложения записывают по одному журналу типа `LogLevel.Warning` на запрос.</span><span class="sxs-lookup"><span data-stu-id="a3d55-101">Prior to ASP.NET Core 3.0 web apps write one log of type `LogLevel.Warning` per request if the requested culture is unsupported.</span></span> <span data-ttu-id="a3d55-102">Запись одного `LogLevel.Warning` на запрос может привести к большим файлам журнала с избыточной информацией.</span><span class="sxs-lookup"><span data-stu-id="a3d55-102">Logging one `LogLevel.Warning` per request is can make large log files with redundant information.</span></span> <span data-ttu-id="a3d55-103">Эту реакцию на событие изменено в ASP.NET 3.0.</span><span class="sxs-lookup"><span data-stu-id="a3d55-103">This behavior has been changed in ASP.NET 3.0.</span></span> <span data-ttu-id="a3d55-104">`RequestLocalizationMiddleware` записывает журнал типа `LogLevel.Debug`, который сокращает размер рабочих журналов.</span><span class="sxs-lookup"><span data-stu-id="a3d55-104">The `RequestLocalizationMiddleware` writes a log of type `LogLevel.Debug`, which reduces the size of production logs.</span></span>
