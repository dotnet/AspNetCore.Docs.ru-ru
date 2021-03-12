---
title: Статические файлы в ASP.NET Core
author: rick-anderson
description: Узнайте, как в веб-приложениях ASP.NET Core обслуживать и защищать статические файлы, а также как настраивать ПО промежуточного слоя по размещению статических файлов.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 807cffb2f9b3bf89ff06c62e76d51d4040b8d91a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589014"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="2588b-103">Статические файлы в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2588b-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2588b-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="2588b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="2588b-105">Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2588b-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="2588b-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2588b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="2588b-107">Обслуживание статических файлов</span><span class="sxs-lookup"><span data-stu-id="2588b-107">Serve static files</span></span>

<span data-ttu-id="2588b-108">Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="2588b-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="2588b-109">Каталог по умолчанию — `{content root}/wwwroot`, но его можно изменить с помощью метода <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="2588b-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="2588b-110">См. разделы [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2588b-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="2588b-111">Метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> устанавливает текущий каталог в качестве корневого каталога содержимого:</span><span class="sxs-lookup"><span data-stu-id="2588b-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="2588b-112">Предыдущий код создан с помощью шаблона веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="2588b-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="2588b-113">Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2588b-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="2588b-114">Например, шаблоны проекта **Web Application** содержат несколько папок в папке `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="2588b-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="2588b-115">Мы рекомендуем создать папку *wwwroot/images* и добавить в нее файл *wwwroot/images/MyImage.jpg*.</span><span class="sxs-lookup"><span data-stu-id="2588b-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="2588b-116">Формат URI для доступа к файлу в папке `images`: `https://<hostname>/images/<image_file_name>`.</span><span class="sxs-lookup"><span data-stu-id="2588b-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="2588b-117">Например: `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="2588b-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="2588b-118">Обслуживание файлов в корневом каталоге веб-сайта</span><span class="sxs-lookup"><span data-stu-id="2588b-118">Serve files in web root</span></span>

<span data-ttu-id="2588b-119">Шаблоны веб-приложений по умолчанию вызывают метод <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure`, который позволяет обслуживать статические файлы:</span><span class="sxs-lookup"><span data-stu-id="2588b-119">The default web app templates call the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2588b-120">Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые.</span><span class="sxs-lookup"><span data-stu-id="2588b-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="2588b-121">Следующая разметка ссылается на *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="2588b-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="2588b-122">В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2588b-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="2588b-123">Обслуживание файлов вне корневого веб-каталога</span><span class="sxs-lookup"><span data-stu-id="2588b-123">Serve files outside of web root</span></span>

<span data-ttu-id="2588b-124">Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="2588b-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="2588b-125">В запросе можно получить доступ к файлу `red-rose.jpg`, настроив ПО промежуточного слоя для статических файлов следующим образом:</span><span class="sxs-lookup"><span data-stu-id="2588b-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="2588b-126">В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="2588b-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="2588b-127">Запрос к `https://<hostname>/StaticFiles/images/red-rose.jpg` обслуживает файл *red-rose.jpg*.</span><span class="sxs-lookup"><span data-stu-id="2588b-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="2588b-128">Следующая разметка ссылается на *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="2588b-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="2588b-129">Установка заголовков HTTP-ответов</span><span class="sxs-lookup"><span data-stu-id="2588b-129">Set HTTP response headers</span></span>

<span data-ttu-id="2588b-130">Для установки заголовков HTTP-ответов можно использовать объект <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="2588b-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="2588b-131">Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="2588b-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="2588b-132">Статические файлы являются общедоступными для кэширования в течение 600 с:</span><span class="sxs-lookup"><span data-stu-id="2588b-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="2588b-134">Авторизация статических файлов</span><span class="sxs-lookup"><span data-stu-id="2588b-134">Static file authorization</span></span>

<span data-ttu-id="2588b-135">Шаблоны ASP.NET Core вызывают <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> перед вызовом <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>.</span><span class="sxs-lookup"><span data-stu-id="2588b-135">The ASP.NET Core templates call <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> before calling <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>.</span></span> <span data-ttu-id="2588b-136">Большинство приложений используют этот шаблон.</span><span class="sxs-lookup"><span data-stu-id="2588b-136">Most apps follow this pattern.</span></span> <span data-ttu-id="2588b-137">При вызове ПО промежуточного слоя статического файла перед ПО промежуточного слоя авторизации:</span><span class="sxs-lookup"><span data-stu-id="2588b-137">When the Static File Middleware is called before the authorization middleware:</span></span>

  * <span data-ttu-id="2588b-138">для статических файлов не выполняются проверки авторизации;</span><span class="sxs-lookup"><span data-stu-id="2588b-138">No authorization checks are performed on the static files.</span></span>
  * <span data-ttu-id="2588b-139">статические файлы, обслуживаемые ПО промежуточного слоя статического файла, например те, которые находятся в `wwwroot`, являются общедоступными.</span><span class="sxs-lookup"><span data-stu-id="2588b-139">Static files served by the Static File Middleware, such as those those under `wwwroot`, are publicly accessible.</span></span>
  
<span data-ttu-id="2588b-140">Для обслуживания статических файлов на основе авторизации:</span><span class="sxs-lookup"><span data-stu-id="2588b-140">To serve static files based on authorization:</span></span>

  * <span data-ttu-id="2588b-141">сохраните их за пределами `wwwroot`;</span><span class="sxs-lookup"><span data-stu-id="2588b-141">Store them outside of `wwwroot`.</span></span>
  * <span data-ttu-id="2588b-142">вызовите `UseStaticFiles`, указав путь, после вызова `UseAuthorization`;</span><span class="sxs-lookup"><span data-stu-id="2588b-142">Call `UseStaticFiles`, specifying a path, after calling `UseAuthorization`.</span></span>
  * <span data-ttu-id="2588b-143">задайте [резервную политику авторизации](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="2588b-143">Set the [fallback authorization policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  <span data-ttu-id="2588b-144">В приведенном выше коде резервная политика авторизации требует проверки подлинности ***всех*** пользователей.</span><span class="sxs-lookup"><span data-stu-id="2588b-144">In the preceding code, the fallback authorization policy requires ***all*** users to be authenticated.</span></span> <span data-ttu-id="2588b-145">Конечные точки, такие как контроллеры, страницы Razor и т. д., которые определяют собственные требования к авторизации, не используют резервную политику авторизации.</span><span class="sxs-lookup"><span data-stu-id="2588b-145">Endpoints such as controllers, Razor Pages, etc that specify their own authorization requirements don't use the fallback authorization policy.</span></span> <span data-ttu-id="2588b-146">Например, Razor Pages, контроллеры или методы действий с `[AllowAnonymous]` или `[Authorize(PolicyName="MyPolicy")]` используют примененный атрибут авторизации вместо резервной политики авторизации.</span><span class="sxs-lookup"><span data-stu-id="2588b-146">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authorization attribute rather than the fallback authorization policy.</span></span>

  <span data-ttu-id="2588b-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> добавляет <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> к текущему экземпляру, что обеспечивает проверку подлинности текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="2588b-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

  <span data-ttu-id="2588b-148">Статические ресурсы в `wwwroot` являются общедоступными, так как ПО промежуточного слоя для статического файла по умолчанию (`app.UseStaticFiles();`) вызывается перед `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="2588b-148">Static assets under `wwwroot` are publicly accessible because the default Static File Middleware (`app.UseStaticFiles();`) is called before `UseAuthentication`.</span></span> <span data-ttu-id="2588b-149">Для статических ресурсов в папке *MyStaticFiles* требуется проверка подлинности.</span><span class="sxs-lookup"><span data-stu-id="2588b-149">Static assets in the *MyStaticFiles* folder require authentication.</span></span> <span data-ttu-id="2588b-150">Это показано в следующем [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples).</span><span class="sxs-lookup"><span data-stu-id="2588b-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) demonstrates this.</span></span>

<span data-ttu-id="2588b-151">Альтернативный подход к обработке файлов на основе авторизации:</span><span class="sxs-lookup"><span data-stu-id="2588b-151">An alternative approach to serve files based on authorization is to:</span></span>

  * <span data-ttu-id="2588b-152">Сохраните файлы за пределами каталога `wwwroot` в любом каталоге, к которому имеет доступ ПО промежуточного слоя для статических файлов.</span><span class="sxs-lookup"><span data-stu-id="2588b-152">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
  * <span data-ttu-id="2588b-153">Обслуживайте их через метод действия, к которому применима авторизация, и получите объект <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="2588b-153">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="2588b-154">Просмотр каталогов</span><span class="sxs-lookup"><span data-stu-id="2588b-154">Directory browsing</span></span>

<span data-ttu-id="2588b-155">Просмотр каталогов позволяет просматривать список каталогов в указанных каталогах.</span><span class="sxs-lookup"><span data-stu-id="2588b-155">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="2588b-156">По соображениям безопасности просмотр каталогов отключен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2588b-156">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="2588b-157">См. раздел [Безопасность статических файлов](#considerations).</span><span class="sxs-lookup"><span data-stu-id="2588b-157">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="2588b-158">Включите просмотр каталогов с помощью:</span><span class="sxs-lookup"><span data-stu-id="2588b-158">Enable directory browsing with:</span></span>

* <span data-ttu-id="2588b-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> в `Startup.ConfigureServices`;</span><span class="sxs-lookup"><span data-stu-id="2588b-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="2588b-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> в `Startup.Configure`;</span><span class="sxs-lookup"><span data-stu-id="2588b-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="2588b-161">Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса `https://<hostname>/MyImages`, со ссылками на все файлы и папки:</span><span class="sxs-lookup"><span data-stu-id="2588b-161">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![просмотр каталогов](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="2588b-163">Обслуживание документов по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2588b-163">Serve default documents</span></span>

<span data-ttu-id="2588b-164">Страница по умолчанию является для пользователей отправной точкой на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="2588b-164">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="2588b-165">Чтобы обслуживать страницу по умолчанию из `wwwroot` без полного URI, вызовите метод <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>:</span><span class="sxs-lookup"><span data-stu-id="2588b-165">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2588b-166">Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="2588b-166">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="2588b-167">`UseDefaultFiles` — это средство переопределения URL-адресов, которое не обслуживает файл.</span><span class="sxs-lookup"><span data-stu-id="2588b-167">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="2588b-168">При использовании `UseDefaultFiles` запросы к папке в `wwwroot` будут искать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="2588b-168">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="2588b-169">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="2588b-169">*default.htm*</span></span>
* <span data-ttu-id="2588b-170">*default.html*</span><span class="sxs-lookup"><span data-stu-id="2588b-170">*default.html*</span></span>
* <span data-ttu-id="2588b-171">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="2588b-171">*index.htm*</span></span>
* <span data-ttu-id="2588b-172">*index.html*</span><span class="sxs-lookup"><span data-stu-id="2588b-172">*index.html*</span></span>

<span data-ttu-id="2588b-173">Первый найденный файл из списка будет обслужен, как будто был введен полный URI.</span><span class="sxs-lookup"><span data-stu-id="2588b-173">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="2588b-174">URL-адрес в браузере будет соответствовать запрошенному URI.</span><span class="sxs-lookup"><span data-stu-id="2588b-174">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="2588b-175">Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="2588b-175">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="2588b-176">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="2588b-176">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="2588b-177">Использование UseFileServer для документов по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2588b-177">UseFileServer for default documents</span></span>

<span data-ttu-id="2588b-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="2588b-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="2588b-179">Вызовите `app.UseFileServer`, чтобы включить обслуживание статических файлов и файла по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2588b-179">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="2588b-180">Просмотр каталогов отключен.</span><span class="sxs-lookup"><span data-stu-id="2588b-180">Directory browsing isn't enabled.</span></span> <span data-ttu-id="2588b-181">В следующем примере кода демонстрируется `Startup.Configure` с `UseFileServer`:</span><span class="sxs-lookup"><span data-stu-id="2588b-181">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2588b-182">Следующий пример кода позволяет обслуживать статические файлы, файл по умолчанию и просмотр каталогов:</span><span class="sxs-lookup"><span data-stu-id="2588b-182">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="2588b-183">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="2588b-183">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2588b-184">Пусть имеется следующая иерархия каталогов:</span><span class="sxs-lookup"><span data-stu-id="2588b-184">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="2588b-185">Следующий пример кода позволяет обслуживать статические файлы, файл по умолчанию и просмотр каталогов `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="2588b-185">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="2588b-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> должно вызываться, когда свойство `EnableDirectoryBrowsing` имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="2588b-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="2588b-187">При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:</span><span class="sxs-lookup"><span data-stu-id="2588b-187">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="2588b-188">URI</span><span class="sxs-lookup"><span data-stu-id="2588b-188">URI</span></span>            |      <span data-ttu-id="2588b-189">Ответ</span><span class="sxs-lookup"><span data-stu-id="2588b-189">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="2588b-190">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="2588b-190">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="2588b-191">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="2588b-191">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="2588b-192">Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, `https://<hostname>/StaticFiles` возвращает список содержимого каталога с доступными для перехода ссылками:</span><span class="sxs-lookup"><span data-stu-id="2588b-192">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Список статических файлов](static-files/_static/db2.png)

<span data-ttu-id="2588b-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из целевого URI без `/` в конце в целевой URI с `/` в конце.</span><span class="sxs-lookup"><span data-stu-id="2588b-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="2588b-195">Например, из `https://<hostname>/StaticFiles` в `https://<hostname>/StaticFiles/`.</span><span class="sxs-lookup"><span data-stu-id="2588b-195">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="2588b-196">Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце (`/`).</span><span class="sxs-lookup"><span data-stu-id="2588b-196">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="2588b-197">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="2588b-197">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="2588b-198">Класс <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> содержит свойство `Mappings`, которое используется для сопоставления расширений файлов и типов содержимого MIME.</span><span class="sxs-lookup"><span data-stu-id="2588b-198">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="2588b-199">В следующем примере несколько расширений файлов сопоставляются с известными типами MIME.</span><span class="sxs-lookup"><span data-stu-id="2588b-199">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="2588b-200">Расширение *.rtf* заменяется, а *.mp4* удаляется:</span><span class="sxs-lookup"><span data-stu-id="2588b-200">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="2588b-201">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="2588b-201">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="2588b-202">См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="2588b-202">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="2588b-203">Нестандартные типы содержимого</span><span class="sxs-lookup"><span data-stu-id="2588b-203">Non-standard content types</span></span>

<span data-ttu-id="2588b-204">ПО промежуточного слоя для статических файлов распознает почти 400 известных типов содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="2588b-204">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="2588b-205">Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере.</span><span class="sxs-lookup"><span data-stu-id="2588b-205">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="2588b-206">Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*.</span><span class="sxs-lookup"><span data-stu-id="2588b-206">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="2588b-207">Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.</span><span class="sxs-lookup"><span data-stu-id="2588b-207">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="2588b-208">Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:</span><span class="sxs-lookup"><span data-stu-id="2588b-208">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="2588b-209">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="2588b-209">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="2588b-210">При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.</span><span class="sxs-lookup"><span data-stu-id="2588b-210">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="2588b-211">При включении <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> возникает угроза безопасности.</span><span class="sxs-lookup"><span data-stu-id="2588b-211">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="2588b-212">По умолчанию он отключен, и его использование не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="2588b-212">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="2588b-213">Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.</span><span class="sxs-lookup"><span data-stu-id="2588b-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="2588b-214">Предоставление файлов из нескольких расположений</span><span class="sxs-lookup"><span data-stu-id="2588b-214">Serve files from multiple locations</span></span>

<span data-ttu-id="2588b-215">В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="2588b-215">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="2588b-216">Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для обслуживания файлов из других расположений.</span><span class="sxs-lookup"><span data-stu-id="2588b-216">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="2588b-217">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="2588b-217">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="2588b-218">Безопасность статических файлов</span><span class="sxs-lookup"><span data-stu-id="2588b-218">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="2588b-219">Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации.</span><span class="sxs-lookup"><span data-stu-id="2588b-219">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="2588b-220">Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="2588b-220">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="2588b-221">Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="2588b-221">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="2588b-222">Весь каталог и его подкаталоги становятся общедоступными.</span><span class="sxs-lookup"><span data-stu-id="2588b-222">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="2588b-223">Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как `<content_root>/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="2588b-223">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="2588b-224">Отделите эти файлы от представлений MVC, Razor Pages, файлов конфигурации и т. д.</span><span class="sxs-lookup"><span data-stu-id="2588b-224">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="2588b-225">К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе.</span><span class="sxs-lookup"><span data-stu-id="2588b-225">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="2588b-226">Например, в Windows регистр не учитывается, а в macOS и Linux учитывается.</span><span class="sxs-lookup"><span data-stu-id="2588b-226">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="2588b-227">Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов.</span><span class="sxs-lookup"><span data-stu-id="2588b-227">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="2588b-228">Обработчик статических файлов IIS не используется и не выполняет обработку запросов.</span><span class="sxs-lookup"><span data-stu-id="2588b-228">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="2588b-229">Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:</span><span class="sxs-lookup"><span data-stu-id="2588b-229">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="2588b-230">Перейдите к компоненту **Модули**.</span><span class="sxs-lookup"><span data-stu-id="2588b-230">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="2588b-231">Выберите в списке модуль **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="2588b-231">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="2588b-232">Нажмите кнопку **Удалить** в боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="2588b-232">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="2588b-233">Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться.</span><span class="sxs-lookup"><span data-stu-id="2588b-233">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="2588b-234">Это может случиться, если, например, не был развернут файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="2588b-234">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="2588b-235">Размещайте файлы с кодом, включая файлы *.cs* и *.cshtml*, за пределами [корневого веб-каталога](xref:fundamentals/index#web-root) проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="2588b-235">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="2588b-236">Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом.</span><span class="sxs-lookup"><span data-stu-id="2588b-236">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="2588b-237">Это предотвращает утечку серверного кода.</span><span class="sxs-lookup"><span data-stu-id="2588b-237">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2588b-238">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2588b-238">Additional resources</span></span>

* [<span data-ttu-id="2588b-239">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="2588b-239">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="2588b-240">Введение в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2588b-240">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2588b-241">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="2588b-241">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="2588b-242">Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую.</span><span class="sxs-lookup"><span data-stu-id="2588b-242">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="2588b-243">Для обслуживания таких файлов требуется настроить некоторые параметры.</span><span class="sxs-lookup"><span data-stu-id="2588b-243">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="2588b-244">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2588b-244">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="2588b-245">Обслуживание статических файлов</span><span class="sxs-lookup"><span data-stu-id="2588b-245">Serve static files</span></span>

<span data-ttu-id="2588b-246">Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="2588b-246">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="2588b-247">Каталог по умолчанию — *{корневой_каталог_содержимого}/wwwroot*, но его можно изменить с помощью метода <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="2588b-247">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="2588b-248">Дополнительные сведения см. в разделах [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2588b-248">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="2588b-249">Веб-узел приложения должен знать о расположении корневого каталога содержимого.</span><span class="sxs-lookup"><span data-stu-id="2588b-249">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="2588b-250">Метод `WebHost.CreateDefaultBuilder` устанавливает текущий каталог в качестве корневого каталога содержимого:</span><span class="sxs-lookup"><span data-stu-id="2588b-250">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="2588b-251">Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2588b-251">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="2588b-252">Например, шаблон проекта **Web Application** содержит несколько папок в папке `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="2588b-252">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="2588b-253">Формат URI для доступа к файлу во вложенной папке *images*: *http://\<server_address>/images/\<image_file_name>* .</span><span class="sxs-lookup"><span data-stu-id="2588b-253">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="2588b-254">Например, *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="2588b-254">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="2588b-255">Если код предназначен для .NET Framework, добавьте в проект пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/).</span><span class="sxs-lookup"><span data-stu-id="2588b-255">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="2588b-256">Если код предназначен для .NET Core, то этот пакет уже включен в метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2588b-256">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="2588b-257">Настройте [ПО промежуточного слоя](xref:fundamentals/middleware/index), позволяющее обслуживать статические файлы.</span><span class="sxs-lookup"><span data-stu-id="2588b-257">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="2588b-258">Обслуживание файлов в корневом веб-каталоге</span><span class="sxs-lookup"><span data-stu-id="2588b-258">Serve files inside of web root</span></span>

<span data-ttu-id="2588b-259">Вызовите метод <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2588b-259">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="2588b-260">Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые.</span><span class="sxs-lookup"><span data-stu-id="2588b-260">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="2588b-261">Следующая разметка ссылается на *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="2588b-261">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="2588b-262">В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2588b-262">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="2588b-263">Обслуживание файлов вне корневого веб-каталога</span><span class="sxs-lookup"><span data-stu-id="2588b-263">Serve files outside of web root</span></span>

<span data-ttu-id="2588b-264">Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="2588b-264">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="2588b-265">В запросе можно получить доступ к файлу *banner1.svg*, настроив ПО промежуточного слоя для статических файлов следующим образом:</span><span class="sxs-lookup"><span data-stu-id="2588b-265">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="2588b-266">В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="2588b-266">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="2588b-267">Запрос *http://\<server_address>/StaticFiles/images/banner1.svg* обслуживает файл *banner1.svg*.</span><span class="sxs-lookup"><span data-stu-id="2588b-267">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="2588b-268">Следующая разметка ссылается на *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="2588b-268">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="2588b-269">Установка заголовков HTTP-ответов</span><span class="sxs-lookup"><span data-stu-id="2588b-269">Set HTTP response headers</span></span>

<span data-ttu-id="2588b-270">Для установки заголовков HTTP-ответов можно использовать объект <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="2588b-270">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="2588b-271">Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="2588b-271">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="2588b-272">Метод <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> существует в пакете [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="2588b-272">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="2588b-273">Файлы, к которым был предоставлен доступ, кэшируются в течение 10 минут (600 секунд) в среде разработки:</span><span class="sxs-lookup"><span data-stu-id="2588b-273">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="2588b-275">Авторизация статических файлов</span><span class="sxs-lookup"><span data-stu-id="2588b-275">Static file authorization</span></span>

<span data-ttu-id="2588b-276">ПО промежуточного слоя для статических файлов не предоставляет возможности авторизации.</span><span class="sxs-lookup"><span data-stu-id="2588b-276">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="2588b-277">Все обслуживаемые им файлы, включая расположенные в *wwwroot*, находятся в открытом доступе.</span><span class="sxs-lookup"><span data-stu-id="2588b-277">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="2588b-278">Для обслуживания файлов с авторизацией:</span><span class="sxs-lookup"><span data-stu-id="2588b-278">To serve files based on authorization:</span></span>

* <span data-ttu-id="2588b-279">Сохраните файлы в любом каталоге за пределами каталога *wwwroot*, к которому имеет доступ ПО промежуточного слоя для статических файлов.</span><span class="sxs-lookup"><span data-stu-id="2588b-279">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="2588b-280">Обслуживайте их через метод действия, к которому применима авторизация.</span><span class="sxs-lookup"><span data-stu-id="2588b-280">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="2588b-281">Получите объект <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="2588b-281">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="2588b-282">Просмотр каталогов</span><span class="sxs-lookup"><span data-stu-id="2588b-282">Enable directory browsing</span></span>

<span data-ttu-id="2588b-283">Просмотр каталогов позволяет пользователям веб-приложениям просматривать файлы и каталоги внутри определенного каталога.</span><span class="sxs-lookup"><span data-stu-id="2588b-283">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="2588b-284">По соображениям безопасности просмотр каталогов отключен по умолчанию (см. раздел [Особенности](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="2588b-284">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="2588b-285">Включите просмотр каталогов, вызвав метод <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2588b-285">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="2588b-286">Добавьте необходимые службы путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> из `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2588b-286">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="2588b-287">Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<server_address>/MyImages*, со ссылками на все файлы и папки:</span><span class="sxs-lookup"><span data-stu-id="2588b-287">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![просмотр каталогов](static-files/_static/dir-browse.png)

<span data-ttu-id="2588b-289">Информацию об угрозе безопасности при включении просмотра каталогов см. в разделе [Особенности](#considerations).</span><span class="sxs-lookup"><span data-stu-id="2588b-289">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="2588b-290">Обратите внимание на два вызова метода `UseStaticFiles` в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="2588b-290">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="2588b-291">Первый вызов включает обслуживание статических файлов в папке *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="2588b-291">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="2588b-292">Второй вызов включает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<server_address>/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="2588b-292">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="2588b-293">Обслуживание документа по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2588b-293">Serve a default document</span></span>

<span data-ttu-id="2588b-294">Домашняя страница по умолчанию является для пользователей логической отправной точкой при посещении веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="2588b-294">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="2588b-295">Для обслуживания страницы по умолчанию без указания полного имени URI вызовите метод <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> из `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2588b-295">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="2588b-296">Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="2588b-296">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="2588b-297">Метод `UseDefaultFiles` фактически не обслуживает файл, а только перезаписывает URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="2588b-297">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="2588b-298">Для обслуживания файла включите ПО промежуточного слоя для статических файлов, вызвав метод `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="2588b-298">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="2588b-299">При вызове метода `UseDefaultFiles` запросы к папке будут искать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="2588b-299">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="2588b-300">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="2588b-300">*default.htm*</span></span>
* <span data-ttu-id="2588b-301">*default.html*</span><span class="sxs-lookup"><span data-stu-id="2588b-301">*default.html*</span></span>
* <span data-ttu-id="2588b-302">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="2588b-302">*index.htm*</span></span>
* <span data-ttu-id="2588b-303">*index.html*</span><span class="sxs-lookup"><span data-stu-id="2588b-303">*index.html*</span></span>

<span data-ttu-id="2588b-304">Первый найденный файл из списка будет обслужен, как будто был введен полный URI.</span><span class="sxs-lookup"><span data-stu-id="2588b-304">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="2588b-305">URL-адрес в браузере будет соответствовать запрошенному URI.</span><span class="sxs-lookup"><span data-stu-id="2588b-305">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="2588b-306">Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="2588b-306">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="2588b-307">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="2588b-307">UseFileServer</span></span>

<span data-ttu-id="2588b-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="2588b-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="2588b-309">Следующий пример кода позволяет обслуживать статические файлы и файл по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2588b-309">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="2588b-310">Просмотр каталогов отключен.</span><span class="sxs-lookup"><span data-stu-id="2588b-310">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="2588b-311">Следующий код отличается от предыдущей перегрузки метода без параметров включением просмотра каталогов:</span><span class="sxs-lookup"><span data-stu-id="2588b-311">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="2588b-312">Пусть имеется следующая иерархия каталогов:</span><span class="sxs-lookup"><span data-stu-id="2588b-312">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="2588b-313">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="2588b-313">**wwwroot**</span></span>
  * <span data-ttu-id="2588b-314">**css**</span><span class="sxs-lookup"><span data-stu-id="2588b-314">**css**</span></span>
  * <span data-ttu-id="2588b-315">**images**</span><span class="sxs-lookup"><span data-stu-id="2588b-315">**images**</span></span>
  * <span data-ttu-id="2588b-316">**js**</span><span class="sxs-lookup"><span data-stu-id="2588b-316">**js**</span></span>
* <span data-ttu-id="2588b-317">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="2588b-317">**MyStaticFiles**</span></span>
  * <span data-ttu-id="2588b-318">**images**</span><span class="sxs-lookup"><span data-stu-id="2588b-318">**images**</span></span>
    * <span data-ttu-id="2588b-319">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="2588b-319">*banner1.svg*</span></span>
  * <span data-ttu-id="2588b-320">*default.html*</span><span class="sxs-lookup"><span data-stu-id="2588b-320">*default.html*</span></span>

<span data-ttu-id="2588b-321">Следующий пример кода включает обслуживание статических файлов, файлы по умолчанию и просмотр каталога `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="2588b-321">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="2588b-322">Метод `AddDirectoryBrowser` должен вызываться при значении `true` свойства `EnableDirectoryBrowsing`:</span><span class="sxs-lookup"><span data-stu-id="2588b-322">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="2588b-323">При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:</span><span class="sxs-lookup"><span data-stu-id="2588b-323">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="2588b-324">URI</span><span class="sxs-lookup"><span data-stu-id="2588b-324">URI</span></span>            |                             <span data-ttu-id="2588b-325">Ответ</span><span class="sxs-lookup"><span data-stu-id="2588b-325">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="2588b-326">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="2588b-326">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="2588b-327">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="2588b-327">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="2588b-328">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="2588b-328">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="2588b-329">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="2588b-329">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="2588b-330">Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, *http://\<server_address>/StaticFiles* возвращает список содержимого каталога с доступными для перехода ссылками:</span><span class="sxs-lookup"><span data-stu-id="2588b-330">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Список статических файлов](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="2588b-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из `http://{SERVER ADDRESS}/StaticFiles` (без косой черты в конце) в `http://{SERVER ADDRESS}/StaticFiles/` (с косой чертой в конце).</span><span class="sxs-lookup"><span data-stu-id="2588b-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="2588b-333">Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце.</span><span class="sxs-lookup"><span data-stu-id="2588b-333">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="2588b-334">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="2588b-334">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="2588b-335">Класс <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> содержит свойство `Mappings`, которое используется для сопоставления расширений файлов и типов содержимого MIME.</span><span class="sxs-lookup"><span data-stu-id="2588b-335">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="2588b-336">В следующем примере несколько расширений файлов регистрируются в известные типы MIME.</span><span class="sxs-lookup"><span data-stu-id="2588b-336">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="2588b-337">Расширение *.rtf* заменяется, а *.mp4* удаляется.</span><span class="sxs-lookup"><span data-stu-id="2588b-337">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="2588b-338">См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="2588b-338">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="2588b-339">Сведения об использовании настраиваемых <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> и настройке других <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> в приложениях Blazor Server см. в разделе <xref:blazor/fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="2588b-339">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="2588b-340">Нестандартные типы содержимого</span><span class="sxs-lookup"><span data-stu-id="2588b-340">Non-standard content types</span></span>

<span data-ttu-id="2588b-341">ПО промежуточного слоя для статических файлов воспринимает почти 400 известных типов содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="2588b-341">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="2588b-342">Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере.</span><span class="sxs-lookup"><span data-stu-id="2588b-342">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="2588b-343">Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*.</span><span class="sxs-lookup"><span data-stu-id="2588b-343">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="2588b-344">Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.</span><span class="sxs-lookup"><span data-stu-id="2588b-344">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="2588b-345">Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:</span><span class="sxs-lookup"><span data-stu-id="2588b-345">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="2588b-346">При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.</span><span class="sxs-lookup"><span data-stu-id="2588b-346">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="2588b-347">При включении <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> возникает угроза безопасности.</span><span class="sxs-lookup"><span data-stu-id="2588b-347">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="2588b-348">По умолчанию он отключен, и его использование не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="2588b-348">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="2588b-349">Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.</span><span class="sxs-lookup"><span data-stu-id="2588b-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="2588b-350">Предоставление файлов из нескольких расположений</span><span class="sxs-lookup"><span data-stu-id="2588b-350">Serve files from multiple locations</span></span>

<span data-ttu-id="2588b-351">В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="2588b-351">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="2588b-352">Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для предоставления файлов из других расположений.</span><span class="sxs-lookup"><span data-stu-id="2588b-352">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="2588b-353">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="2588b-353">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="2588b-354">Особенности</span><span class="sxs-lookup"><span data-stu-id="2588b-354">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="2588b-355">Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации.</span><span class="sxs-lookup"><span data-stu-id="2588b-355">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="2588b-356">Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="2588b-356">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="2588b-357">Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="2588b-357">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="2588b-358">Весь каталог и его подкаталоги становятся общедоступными.</span><span class="sxs-lookup"><span data-stu-id="2588b-358">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="2588b-359">Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="2588b-359">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="2588b-360">Отделите эти файлы от представлений MVC, страниц Razor Pages (только для версии 2.x), файлов конфигурации и т. д.</span><span class="sxs-lookup"><span data-stu-id="2588b-360">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="2588b-361">К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе.</span><span class="sxs-lookup"><span data-stu-id="2588b-361">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="2588b-362">Например, в Windows не учитывается регистр, а в macOS и Linux &mdash; учитывается.</span><span class="sxs-lookup"><span data-stu-id="2588b-362">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="2588b-363">Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов.</span><span class="sxs-lookup"><span data-stu-id="2588b-363">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="2588b-364">Обработчик статических файлов IIS не используется.</span><span class="sxs-lookup"><span data-stu-id="2588b-364">The IIS static file handler isn't used.</span></span> <span data-ttu-id="2588b-365">Не существует возможности обработать запросы до того, как их обработает модуль.</span><span class="sxs-lookup"><span data-stu-id="2588b-365">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="2588b-366">Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:</span><span class="sxs-lookup"><span data-stu-id="2588b-366">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="2588b-367">Перейдите к компоненту **Модули**.</span><span class="sxs-lookup"><span data-stu-id="2588b-367">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="2588b-368">Выберите в списке модуль **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="2588b-368">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="2588b-369">Нажмите кнопку **Удалить** в боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="2588b-369">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="2588b-370">Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться.</span><span class="sxs-lookup"><span data-stu-id="2588b-370">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="2588b-371">Это может случиться, если, например, не был развернут файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="2588b-371">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="2588b-372">Размещайте файлы с кодом (включая *CS* и *CSHTML*) за пределами [корневого каталога документов](xref:fundamentals/index#web-root) проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="2588b-372">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="2588b-373">Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом.</span><span class="sxs-lookup"><span data-stu-id="2588b-373">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="2588b-374">Это предотвращает утечку серверного кода.</span><span class="sxs-lookup"><span data-stu-id="2588b-374">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2588b-375">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2588b-375">Additional resources</span></span>

* [<span data-ttu-id="2588b-376">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="2588b-376">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="2588b-377">Введение в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2588b-377">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
