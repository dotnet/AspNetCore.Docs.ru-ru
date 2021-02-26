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
ms.openlocfilehash: bdb60608ba4bd99b24e458f0543b1f4226cadf44
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552092"
---
<span data-ttu-id="7ce42-101">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="7ce42-101">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="7ce42-102">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="7ce42-102">For authenticated users:</span></span>
  * <span data-ttu-id="7ce42-103">отображает имя текущего пользователя;</span><span class="sxs-lookup"><span data-stu-id="7ce42-103">Displays the current username.</span></span>
  * <span data-ttu-id="7ce42-104">отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="7ce42-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="7ce42-105">Для анонимных пользователей предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="7ce42-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
