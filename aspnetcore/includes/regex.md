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
ms.openlocfilehash: 73395ab632f38fef1348b4657770eb52db5778d9
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552367"
---
> [!WARNING]
> <span data-ttu-id="e16e1-101">При использовании <xref:System.Text.RegularExpressions> для обработки ненадежных входных данных передайте время ожидания.</span><span class="sxs-lookup"><span data-stu-id="e16e1-101">When using <xref:System.Text.RegularExpressions> to process untrusted input, pass a timeout.</span></span> <span data-ttu-id="e16e1-102">Злонамеренный пользователь может предоставить входные данные для `RegularExpressions`, что делает возможными [атаки типа "отказ в обслуживании"](https://www.us-cert.gov/ncas/tips/ST04-015).</span><span class="sxs-lookup"><span data-stu-id="e16e1-102">A malicious user can provide input to `RegularExpressions` causing a [Denial-of-Service attack](https://www.us-cert.gov/ncas/tips/ST04-015).</span></span> <span data-ttu-id="e16e1-103">API платформы ASP.NET Core, использующие `RegularExpressions`, передают время ожидания.</span><span class="sxs-lookup"><span data-stu-id="e16e1-103">ASP.NET Core framework APIs that use `RegularExpressions` pass a timeout.</span></span>