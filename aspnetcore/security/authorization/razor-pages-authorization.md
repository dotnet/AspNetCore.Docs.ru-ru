---
title: Razor Страницы соглашения об авторизации в ASP.NET Core
author: rick-anderson
description: Узнайте, как управлять доступом к страницам с помощью соглашений, которые разрешают пользователей, и разрешить анонимным пользователям доступ к страницам или папкам страниц.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: ffcb16b626773da69c45b8ab5dbd7c3cdc84bb5f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589118"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="496cf-103">Razor Страницы соглашения об авторизации в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="496cf-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="496cf-104">Одним из способов управления доступом в Razor приложении для страниц является использование соглашений об авторизации при запуске.</span><span class="sxs-lookup"><span data-stu-id="496cf-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="496cf-105">Эти соглашения позволяют авторизовать пользователей и разрешить анонимным пользователям доступ к отдельным страницам или папкам страниц.</span><span class="sxs-lookup"><span data-stu-id="496cf-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="496cf-106">Соглашения, описанные в этом разделе, автоматически применяют [фильтры авторизации](xref:mvc/controllers/filters#authorization-filters) для управления доступом.</span><span class="sxs-lookup"><span data-stu-id="496cf-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="496cf-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="496cf-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="496cf-108">Пример приложения использует [ cookie проверку подлинности без ASP.NET Core Identity ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="496cf-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="496cf-109">Понятия и примеры, приведенные в этом разделе, применяются одинаково к приложениям, использующим ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="496cf-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="496cf-110">Для использования ASP.NET Core Identity следуйте указаниям в статье <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="496cf-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="496cf-111">Требовать авторизацию для доступа к странице</span><span class="sxs-lookup"><span data-stu-id="496cf-111">Require authorization to access a page</span></span>

<span data-ttu-id="496cf-112">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> соглашение, чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> на страницу по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="496cf-113">Указанный путь — это путь к обработчику представлений, который является Razor корневым относительным путем для страниц без расширения и содержит только косую черту.</span><span class="sxs-lookup"><span data-stu-id="496cf-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="496cf-114">Чтобы указать [политику авторизации](xref:security/authorization/policies), используйте [перегрузку аусоризепаже](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="496cf-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="496cf-115"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Можно применить к классу модели страницы с помощью `[Authorize]` атрибута Filter.</span><span class="sxs-lookup"><span data-stu-id="496cf-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="496cf-116">Дополнительные сведения см. в разделе [авторизация атрибута фильтра](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="496cf-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="496cf-117">Требовать авторизацию для доступа к папке страниц</span><span class="sxs-lookup"><span data-stu-id="496cf-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="496cf-118">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> соглашение, чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> ко всем страницам в папке по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="496cf-119">Указанный путь — это путь к обработчику представлений, который является Razor корневым относительном путем страниц.</span><span class="sxs-lookup"><span data-stu-id="496cf-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="496cf-120">Чтобы указать [политику авторизации](xref:security/authorization/policies), используйте [перегрузку аусоризефолдер](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="496cf-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="496cf-121">Требовать авторизацию для доступа к странице области</span><span class="sxs-lookup"><span data-stu-id="496cf-121">Require authorization to access an area page</span></span>

<span data-ttu-id="496cf-122">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> соглашение для добавления <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> на страницу области по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="496cf-123">Имя страницы — это путь к файлу без расширения относительно корневого каталога страниц для указанной области.</span><span class="sxs-lookup"><span data-stu-id="496cf-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="496cf-124">Например, имя страницы для файлов *областей/ Identity /Пажес/манаже/аккаунтс.кштмл* — */манаже/аккаунтс*.</span><span class="sxs-lookup"><span data-stu-id="496cf-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="496cf-125">Чтобы указать [политику авторизации](xref:security/authorization/policies), используйте [перегрузку аусоризеареапаже](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="496cf-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="496cf-126">Требовать авторизацию для доступа к папке областей</span><span class="sxs-lookup"><span data-stu-id="496cf-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="496cf-127">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> соглашение, чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> ко всем областям в папке по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="496cf-128">Путь к папке — это путь к папке относительно корневого каталога страниц для указанной области.</span><span class="sxs-lookup"><span data-stu-id="496cf-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="496cf-129">Например, путь к папке для файлов в разделе *Areas/ Identity /Пажес/манаже/* — */манаже*.</span><span class="sxs-lookup"><span data-stu-id="496cf-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="496cf-130">Чтобы указать [политику авторизации](xref:security/authorization/policies), используйте [перегрузку аусоризеареафолдер](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="496cf-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="496cf-131">Разрешить анонимный доступ к странице</span><span class="sxs-lookup"><span data-stu-id="496cf-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="496cf-132">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> соглашение, чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> к странице по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="496cf-133">Указанный путь — это путь к обработчику представлений, который является Razor корневым относительным путем для страниц без расширения и содержит только косую черту.</span><span class="sxs-lookup"><span data-stu-id="496cf-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="496cf-134">Разрешить анонимный доступ к папке страниц</span><span class="sxs-lookup"><span data-stu-id="496cf-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="496cf-135">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> соглашение, чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> ко всем страницам в папке по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="496cf-136">Указанный путь — это путь к обработчику представлений, который является Razor корневым относительном путем страниц.</span><span class="sxs-lookup"><span data-stu-id="496cf-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="496cf-137">Примечание о комбинировании авторизации и анонимного доступа</span><span class="sxs-lookup"><span data-stu-id="496cf-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="496cf-138">Допустимо указать, что для папки страниц требуется авторизация, а затем указать, что страница в этой папке разрешает анонимный доступ:</span><span class="sxs-lookup"><span data-stu-id="496cf-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="496cf-139">Обратная, однако, недопустима.</span><span class="sxs-lookup"><span data-stu-id="496cf-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="496cf-140">Нельзя объявить папку страниц для анонимного доступа, а затем указать страницу в этой папке, для которой требуется авторизация:</span><span class="sxs-lookup"><span data-stu-id="496cf-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="496cf-141">Не удается выполнить авторизацию на частной странице.</span><span class="sxs-lookup"><span data-stu-id="496cf-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="496cf-142">Если к <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> странице применяются и и, и, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Управление имеет приоритет и управляет доступом.</span><span class="sxs-lookup"><span data-stu-id="496cf-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="496cf-143">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="496cf-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="496cf-144">Одним из способов управления доступом в Razor приложении для страниц является использование соглашений об авторизации при запуске.</span><span class="sxs-lookup"><span data-stu-id="496cf-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="496cf-145">Эти соглашения позволяют авторизовать пользователей и разрешить анонимным пользователям доступ к отдельным страницам или папкам страниц.</span><span class="sxs-lookup"><span data-stu-id="496cf-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="496cf-146">Соглашения, описанные в этом разделе, автоматически применяют [фильтры авторизации](xref:mvc/controllers/filters#authorization-filters) для управления доступом.</span><span class="sxs-lookup"><span data-stu-id="496cf-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="496cf-147">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="496cf-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="496cf-148">Пример приложения использует [ cookie проверку подлинности без ASP.NET Core Identity ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="496cf-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="496cf-149">Понятия и примеры, приведенные в этом разделе, применяются одинаково к приложениям, использующим ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="496cf-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="496cf-150">Для использования ASP.NET Core Identity следуйте указаниям в статье <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="496cf-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="496cf-151">Требовать авторизацию для доступа к странице</span><span class="sxs-lookup"><span data-stu-id="496cf-151">Require authorization to access a page</span></span>

<span data-ttu-id="496cf-152">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> соглашение с, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> к странице по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="496cf-153">Указанный путь — это путь к обработчику представлений, который является Razor корневым относительным путем для страниц без расширения и содержит только косую черту.</span><span class="sxs-lookup"><span data-stu-id="496cf-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="496cf-154">Чтобы указать [политику авторизации](xref:security/authorization/policies), используйте [перегрузку аусоризепаже](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="496cf-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="496cf-155"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Можно применить к классу модели страницы с помощью `[Authorize]` атрибута Filter.</span><span class="sxs-lookup"><span data-stu-id="496cf-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="496cf-156">Дополнительные сведения см. в разделе [авторизация атрибута фильтра](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="496cf-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="496cf-157">Требовать авторизацию для доступа к папке страниц</span><span class="sxs-lookup"><span data-stu-id="496cf-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="496cf-158">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> соглашение с, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> ко всем страницам в папке по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="496cf-159">Указанный путь — это путь к обработчику представлений, который является Razor корневым относительном путем страниц.</span><span class="sxs-lookup"><span data-stu-id="496cf-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="496cf-160">Чтобы указать [политику авторизации](xref:security/authorization/policies), используйте [перегрузку аусоризефолдер](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="496cf-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="496cf-161">Требовать авторизацию для доступа к странице области</span><span class="sxs-lookup"><span data-stu-id="496cf-161">Require authorization to access an area page</span></span>

<span data-ttu-id="496cf-162">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> соглашение с, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> на страницу области по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="496cf-163">Имя страницы — это путь к файлу без расширения относительно корневого каталога страниц для указанной области.</span><span class="sxs-lookup"><span data-stu-id="496cf-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="496cf-164">Например, имя страницы для файлов *областей/ Identity /Пажес/манаже/аккаунтс.кштмл* — */манаже/аккаунтс*.</span><span class="sxs-lookup"><span data-stu-id="496cf-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="496cf-165">Чтобы указать [политику авторизации](xref:security/authorization/policies), используйте [перегрузку аусоризеареапаже](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="496cf-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="496cf-166">Требовать авторизацию для доступа к папке областей</span><span class="sxs-lookup"><span data-stu-id="496cf-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="496cf-167">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> соглашение с, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> ко всем областям в папке по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="496cf-168">Путь к папке — это путь к папке относительно корневого каталога страниц для указанной области.</span><span class="sxs-lookup"><span data-stu-id="496cf-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="496cf-169">Например, путь к папке для файлов в разделе *Areas/ Identity /Пажес/манаже/* — */манаже*.</span><span class="sxs-lookup"><span data-stu-id="496cf-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="496cf-170">Чтобы указать [политику авторизации](xref:security/authorization/policies), используйте [перегрузку аусоризеареафолдер](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="496cf-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="496cf-171">Разрешить анонимный доступ к странице</span><span class="sxs-lookup"><span data-stu-id="496cf-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="496cf-172">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> соглашение с, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> к странице по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="496cf-173">Указанный путь — это путь к обработчику представлений, который является Razor корневым относительным путем для страниц без расширения и содержит только косую черту.</span><span class="sxs-lookup"><span data-stu-id="496cf-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="496cf-174">Разрешить анонимный доступ к папке страниц</span><span class="sxs-lookup"><span data-stu-id="496cf-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="496cf-175">Используйте <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> соглашение с, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> чтобы добавить <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> ко всем страницам в папке по указанному пути:</span><span class="sxs-lookup"><span data-stu-id="496cf-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="496cf-176">Указанный путь — это путь к обработчику представлений, который является Razor корневым относительном путем страниц.</span><span class="sxs-lookup"><span data-stu-id="496cf-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="496cf-177">Примечание о комбинировании авторизации и анонимного доступа</span><span class="sxs-lookup"><span data-stu-id="496cf-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="496cf-178">Допустимо указать, что папка страниц, для которых требуется авторизация, а не указывает, что страница в этой папке разрешает анонимный доступ:</span><span class="sxs-lookup"><span data-stu-id="496cf-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="496cf-179">Обратная, однако, недопустима.</span><span class="sxs-lookup"><span data-stu-id="496cf-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="496cf-180">Нельзя объявить папку страниц для анонимного доступа, а затем указать страницу в этой папке, для которой требуется авторизация:</span><span class="sxs-lookup"><span data-stu-id="496cf-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="496cf-181">Не удается выполнить авторизацию на частной странице.</span><span class="sxs-lookup"><span data-stu-id="496cf-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="496cf-182">Если к <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> странице применяются и и, и, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Управление имеет приоритет и управляет доступом.</span><span class="sxs-lookup"><span data-stu-id="496cf-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="496cf-183">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="496cf-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
