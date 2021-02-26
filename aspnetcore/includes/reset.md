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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551401"
---
Сброс позволяет серверу сбросить запрос HTTP/2 с указанным кодом ошибки. Сброшенный запрос считается прерванным.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` в предыдущем примере кода задает код ошибки `INTERNAL_ERROR`. Дополнительные сведения о кодах ошибок HTTP/2 см. в [соответствующем разделе спецификации HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).