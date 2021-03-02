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
ms.openlocfilehash: d632ab0604f81f7b6067d4535b0f5da0afe2e0ad
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552944"
---
Компонент `App` (`App.razor`) аналогичен компоненту `App`, который находится в приложениях Blazor Server:

* Компонент <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> управляет предоставлением <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> остальным приложениям.
* Компонент <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> гарантирует, что текущий пользователь имеет право доступа к определенной странице или иным образом работать с компонентом `RedirectToLogin`.
* Компонент `RedirectToLogin` управляет перенаправлением неавторизованных пользователей на страницу входа.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!include[](../prefer-exact-matches.md)]
