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
ms.openlocfilehash: b2519eaab7a83d66e60be05b1c2deeb8094b016f
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551597"
---
<span data-ttu-id="769b0-101">Страница индекса (`wwwroot/index.html`) содержит сценарий, определяющий `AuthenticationService` в JavaScript.</span><span class="sxs-lookup"><span data-stu-id="769b0-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="769b0-102">`AuthenticationService` обрабатывает низкоуровневые сведения о протоколе OIDC.</span><span class="sxs-lookup"><span data-stu-id="769b0-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="769b0-103">Приложение внутренне вызывает методы, определенные в сценарии для выполнения операций проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="769b0-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
