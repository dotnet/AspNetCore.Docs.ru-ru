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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552321"
---
<span data-ttu-id="d54f7-101">ПО промежуточного слоя перенаправления заголовков должно выполняться до другого ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="d54f7-101">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="d54f7-102">Такой порядок гарантирует, что ПО промежуточного слоя, полагающееся на сведения о перенаправленных заголовках, может использовать значения заголовков для обработки.</span><span class="sxs-lookup"><span data-stu-id="d54f7-102">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="d54f7-103">Сведения о запуске ПО промежуточного слоя перенаправления заголовков после ПО промежуточного слоя диагностики и обработки ошибок см. в разделе [Порядок ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#fhmo).</span><span class="sxs-lookup"><span data-stu-id="d54f7-103">To run Forwarded Headers Middleware after diagnostics and error handling middleware, see [Forwarded Headers Middleware order](xref:host-and-deploy/proxy-load-balancer#fhmo).</span></span>