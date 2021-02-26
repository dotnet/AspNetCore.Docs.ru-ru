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
ms.openlocfilehash: 9b7b302485a5c9ab7d1b1da6ce4d8ab7d1c26f9c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551910"
---
<span data-ttu-id="4c7ff-101">Компонент `RedirectToLogin` (`Shared/RedirectToLogin.razor`):</span><span class="sxs-lookup"><span data-stu-id="4c7ff-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="4c7ff-102">управляет перенаправлением неавторизованных пользователей на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="4c7ff-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="4c7ff-103">Сохраняет текущий URL-адрес, к которому пользователь пытается получить доступ, чтобы его можно было вернуть на эту страницу, если проверка подлинности прошла успешно.</span><span class="sxs-lookup"><span data-stu-id="4c7ff-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
