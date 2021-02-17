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
ms.openlocfilehash: b6f6bc2e094c9070e0ea57b507f558313f19bc15
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552416"
---
<span data-ttu-id="0841c-101">[Identity](xref:security/authentication/identity)На ASP.NET Core во многом не влияет [SameSite cookie s](xref:security/samesite) , за исключением сложных сценариев, таких как `IFrames` или `OpenIdConnect` интеграция.</span><span class="sxs-lookup"><span data-stu-id="0841c-101">ASP.NET Core [Identity](xref:security/authentication/identity) is largely unaffected by [SameSite cookies](xref:security/samesite) except for advanced scenarios like `IFrames` or `OpenIdConnect` integration.</span></span>

<span data-ttu-id="0841c-102">При использовании `Identity` ***не*** добавляйте какие бы то ни было cookie поставщики или вызовы ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` , `Identity` за это отвечает.</span><span class="sxs-lookup"><span data-stu-id="0841c-102">When using `Identity`, do ***not*** add any cookie providers or call ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)`, `Identity` takes care of that.</span></span>