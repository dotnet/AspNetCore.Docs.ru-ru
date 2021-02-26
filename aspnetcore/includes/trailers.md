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
<span data-ttu-id="d3a27-101">Трейлеры HTTP похожи на заголовки HTTP, за исключением того, что они отправляются после отправки текста ответа.</span><span class="sxs-lookup"><span data-stu-id="d3a27-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="d3a27-102">Для IIS и HTTP.sys поддерживаются только трейлеры ответов HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d3a27-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="d3a27-103">В приведенном выше примере кода:</span><span class="sxs-lookup"><span data-stu-id="d3a27-103">In the preceding example code:</span></span>

* <span data-ttu-id="d3a27-104">`SupportsTrailers` обеспечивает поддержку трейлеров для ответа;</span><span class="sxs-lookup"><span data-stu-id="d3a27-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="d3a27-105">`DeclareTrailer` добавляет заданное имя трейлера в заголовок ответа `Trailer`.</span><span class="sxs-lookup"><span data-stu-id="d3a27-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="d3a27-106">Объявлять трейлеры ответа необязательно, но рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="d3a27-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="d3a27-107">Вызов `DeclareTrailer` должен производиться перед отправкой заголовков ответа.</span><span class="sxs-lookup"><span data-stu-id="d3a27-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="d3a27-108">`AppendTrailer` добавляет трейлер.</span><span class="sxs-lookup"><span data-stu-id="d3a27-108">`AppendTrailer` appends the trailer.</span></span>
