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
ms.openlocfilehash: ce0a993093812c9a477d85d363827988ae9bd536
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552385"
---
Страница индекса (`wwwroot/index.html`) содержит сценарий, определяющий `AuthenticationService` в JavaScript. `AuthenticationService` обрабатывает низкоуровневые сведения о протоколе OIDC. Приложение внутренне вызывает методы, определенные в сценарии для выполнения операций проверки подлинности.

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
