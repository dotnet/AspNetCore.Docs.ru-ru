---
title: Пример SameSite ASP.NET Core 2,1 MVC cookie
author: rick-anderson
description: Пример SameSite ASP.NET Core 2,1 MVC cookie
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: 8f819d283e136a63ad9f82d6432a93866210b36b
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110109"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="9941c-103">Пример SameSite ASP.NET Core 2,1 MVC cookie</span><span class="sxs-lookup"><span data-stu-id="9941c-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="9941c-104">ASP.NET Core 2,1 имеет встроенную поддержку атрибута [SameSite](https://www.owasp.org/index.php/SameSite) , но она была записана в исходный стандарт.</span><span class="sxs-lookup"><span data-stu-id="9941c-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="9941c-105">[Исправленное поведение](https://github.com/dotnet/aspnetcore/issues/8212) изменило значение параметра `SameSite.None` , чтобы выдать атрибут sameSite со значением `None` , а не выдавать значение вообще.</span><span class="sxs-lookup"><span data-stu-id="9941c-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="9941c-106">Если вы не хотите выпустить значение, можно задать `SameSite` для свойства значение cookie -1.</span><span class="sxs-lookup"><span data-stu-id="9941c-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="9941c-107">Написание атрибута SameSite</span><span class="sxs-lookup"><span data-stu-id="9941c-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="9941c-108">Ниже приведен пример того, как записать атрибут SameSite в cookie :</span><span class="sxs-lookup"><span data-stu-id="9941c-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```csharp
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="9941c-109">Настройка Cookie проверки подлинности и состояния сеанса cookie</span><span class="sxs-lookup"><span data-stu-id="9941c-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="9941c-110">Cookie Проверка подлинности, состояние сеанса и [различные другие компоненты](../samesite.md?view=aspnetcore-2.1) задают свои параметры sameSite Cookie с помощью параметров, например</span><span class="sxs-lookup"><span data-stu-id="9941c-110">Cookie authentication, session state and [various other components](../samesite.md?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="9941c-111">В приведенном выше коде как cookie Проверка подлинности, так и состояние сеанса устанавливают атрибут sameSite в, выдает `None` атрибут со `None` значением, а также присвойте атрибуту Secure значение true.</span><span class="sxs-lookup"><span data-stu-id="9941c-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="9941c-112">Запуск примера</span><span class="sxs-lookup"><span data-stu-id="9941c-112">Run the sample</span></span>

<span data-ttu-id="9941c-113">При запуске [примера проекта](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)Загрузите отладчик браузера на начальной странице и используйте его для просмотра cookie коллекции для сайта.</span><span class="sxs-lookup"><span data-stu-id="9941c-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="9941c-114">Чтобы сделать это в пограничных и Chrome, нажмите на `F12` `Application` вкладку и щелкните URL-адрес сайта в `Cookies` параметре в `Storage` разделе.</span><span class="sxs-lookup"><span data-stu-id="9941c-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Отладчик браузера::: No-Loc (cookie)::: List](BrowserDebugger.png)

<span data-ttu-id="9941c-116">На приведенном выше изображении можно увидеть, что cookie созданный примером при нажатии кнопки "создать SameSite Cookie " имеет значение атрибута SameSite `Lax` , совпадающее со значением, заданным в [образце кода](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="9941c-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-cookies"></a><a name="interception"></a><span data-ttu-id="9941c-117">Перехват cookie s</span><span class="sxs-lookup"><span data-stu-id="9941c-117">Intercepting cookies</span></span>

<span data-ttu-id="9941c-118">Чтобы перехватить cookie , чтобы изменить значение None в соответствии с его поддержкой в агенте браузера пользователя, необходимо использовать по `CookiePolicy` промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="9941c-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="9941c-119">Он должен быть помещен в конвейер HTTP-запросов **перед** всеми компонентами, которые записывают cookie и настраиваются в `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="9941c-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="9941c-120">Чтобы вставить его в конвейер, используйте `app.UseCookiePolicy()` `Configure(IApplicationBuilder, IHostingEnvironment)` метод в [Startup.CS](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="9941c-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="9941c-121">Пример:</span><span class="sxs-lookup"><span data-stu-id="9941c-121">For example:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="9941c-122">Затем в `ConfigureServices(IServiceCollection services)` cookie политике настройте политику для вызова вспомогательного класса, когда cookie добавляются или удаляются.</span><span class="sxs-lookup"><span data-stu-id="9941c-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="9941c-123">Пример:</span><span class="sxs-lookup"><span data-stu-id="9941c-123">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="9941c-124">Вспомогательная функция `CheckSameSite(HttpContext, CookieOptions)` :</span><span class="sxs-lookup"><span data-stu-id="9941c-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="9941c-125">Вызывается, когда cookie s добавляется к запросу или удаляется из запроса.</span><span class="sxs-lookup"><span data-stu-id="9941c-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="9941c-126">Проверяет, `SameSite` имеет ли свойство значение `None` .</span><span class="sxs-lookup"><span data-stu-id="9941c-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="9941c-127">Если параметр `SameSite` имеет значение `None` , а для текущего агента пользователя известно, что он не поддерживает значение атрибута None.</span><span class="sxs-lookup"><span data-stu-id="9941c-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="9941c-128">Проверка выполняется с помощью класса [самеситесуппорт](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="9941c-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="9941c-129">Задает, чтобы не выдавало `SameSite` значение, задав для свойства `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="9941c-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="9941c-130">Нацеливание на платформа .NET Framework</span><span class="sxs-lookup"><span data-stu-id="9941c-130">Targeting .NET Framework</span></span>

<span data-ttu-id="9941c-131">ASP.NET Core и System. Web (ASP.NET 4. x) имеют независимые реализации SameSite.</span><span class="sxs-lookup"><span data-stu-id="9941c-131">ASP.NET Core and System.Web (ASP.NET 4.x) have independent implementations of SameSite.</span></span> <span data-ttu-id="9941c-132">Исправления SameSite KB для платформа .NET Framework не требуются, если используется ASP.NET Core и не является требованием к версии System. Web SameSite минимальной платформы (платформа .NET Framework 4.7.2), применяемым к ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9941c-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET Framework 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="9941c-133">ASP.NET Core в .NET требует обновления зависимостей пакетов NuGet для получения соответствующих исправлений.</span><span class="sxs-lookup"><span data-stu-id="9941c-133">ASP.NET Core on .NET requires updating NuGet package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="9941c-134">Чтобы получить ASP.NET Core изменения для платформа .NET Framework убедитесь в наличии прямой ссылки на исправленные пакеты и версии (2.1.14 или более поздней версии 2,1).</span><span class="sxs-lookup"><span data-stu-id="9941c-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="9941c-135">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="9941c-135">More Information</span></span>
 
<span data-ttu-id="9941c-136">[Обновления Chrome](https://www.chromium.org/updates/same-site) 
 [Документация по](../samesite.md?view=aspnetcore-2.1) 
 ASP.NET Core SameSite [ASP.NET Core 2,1 SameSite объявление об изменениях](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="9941c-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](../samesite.md?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>