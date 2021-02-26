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
ms.openlocfilehash: d5e1630d6a9e412c40831aa3a66aaed7524ff501
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552372"
---
Трейлеры HTTP похожи на заголовки HTTP, за исключением того, что они отправляются после отправки текста ответа. Для IIS и HTTP.sys поддерживаются только трейлеры ответов HTTP/2.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

В приведенном выше примере кода:

* `SupportsTrailers` обеспечивает поддержку трейлеров для ответа;
* `DeclareTrailer` добавляет заданное имя трейлера в заголовок ответа `Trailer`. Объявлять трейлеры ответа необязательно, но рекомендуется. Вызов `DeclareTrailer` должен производиться перед отправкой заголовков ответа.
* `AppendTrailer` добавляет трейлер.
