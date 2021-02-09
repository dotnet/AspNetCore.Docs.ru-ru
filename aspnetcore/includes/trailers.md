<span data-ttu-id="00c71-101">Трейлеры HTTP похожи на заголовки HTTP, за исключением того, что они отправляются после отправки текста ответа.</span><span class="sxs-lookup"><span data-stu-id="00c71-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="00c71-102">Для IIS и HTTP.sys поддерживаются только трейлеры ответов HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="00c71-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="00c71-103">В приведенном выше примере кода:</span><span class="sxs-lookup"><span data-stu-id="00c71-103">In the preceding example code:</span></span>

* <span data-ttu-id="00c71-104">`SupportsTrailers` обеспечивает поддержку трейлеров для ответа;</span><span class="sxs-lookup"><span data-stu-id="00c71-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="00c71-105">`DeclareTrailer` добавляет заданное имя трейлера в заголовок ответа `Trailer`.</span><span class="sxs-lookup"><span data-stu-id="00c71-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="00c71-106">Объявлять трейлеры ответа необязательно, но рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="00c71-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="00c71-107">Вызов `DeclareTrailer` должен производиться перед отправкой заголовков ответа.</span><span class="sxs-lookup"><span data-stu-id="00c71-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="00c71-108">`AppendTrailer` добавляет трейлер.</span><span class="sxs-lookup"><span data-stu-id="00c71-108">`AppendTrailer` appends the trailer.</span></span>
