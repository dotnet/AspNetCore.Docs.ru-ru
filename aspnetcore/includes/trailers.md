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
