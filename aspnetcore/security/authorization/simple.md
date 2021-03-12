---
title: Простая Авторизация в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать атрибут авторизации для ограничения доступа к ASP.NET Core контроллерам и действиям.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/simple
ms.openlocfilehash: eaf36822edc47f7238c356e167b05ac44f93175d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587571"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="25310-103">Простая Авторизация в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25310-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="25310-104">Авторизация в ASP.NET Core контролируется с помощью <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> и различных параметров.</span><span class="sxs-lookup"><span data-stu-id="25310-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="25310-105">В простейшей форме применение `[Authorize]` атрибута к контроллеру, действию или Razor странице ограничивает доступ к этому компоненту для любого пользователя, прошедшего проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="25310-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="25310-106">Например, следующий код ограничивает доступ к `AccountController` любому пользователю, прошедшему проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="25310-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="25310-107">Если вы хотите применить авторизацию к действию, а не к контроллеру, примените `AuthorizeAttribute` атрибут к самому действию:</span><span class="sxs-lookup"><span data-stu-id="25310-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="25310-108">Теперь доступ к функции могут получить только пользователи, прошедшие проверку подлинности `Logout` .</span><span class="sxs-lookup"><span data-stu-id="25310-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="25310-109">Можно также использовать атрибут, `AllowAnonymous` чтобы разрешить пользователям без проверки подлинности выполнять отдельные действия.</span><span class="sxs-lookup"><span data-stu-id="25310-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="25310-110">Пример:</span><span class="sxs-lookup"><span data-stu-id="25310-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="25310-111">Это позволит использовать только пользователей, прошедших проверку подлинности `AccountController` , за исключением `Login` действия, доступного для всех, независимо от их состояния с проверкой подлинности или без проверки подлинности или анонимного пользователя.</span><span class="sxs-lookup"><span data-stu-id="25310-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="25310-112">`[AllowAnonymous]` обходит все инструкции авторизации.</span><span class="sxs-lookup"><span data-stu-id="25310-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="25310-113">Если объединить `[AllowAnonymous]` и любой `[Authorize]` атрибут, `[Authorize]` атрибуты игнорируются.</span><span class="sxs-lookup"><span data-stu-id="25310-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="25310-114">Например, при применении `[AllowAnonymous]` на уровне контроллера все `[Authorize]` атрибуты на том же контроллере (или в любом действии в нем) игнорируются.</span><span class="sxs-lookup"><span data-stu-id="25310-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a><span data-ttu-id="25310-115">Атрибут authorize и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="25310-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="25310-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ***Не*** может применяться к Razor обработчикам страниц.</span><span class="sxs-lookup"><span data-stu-id="25310-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can ***not*** be applied to Razor Page handlers.</span></span> <span data-ttu-id="25310-117">Например, `[Authorize]` нельзя применить к `OnGet` , или к `OnPost` любому другому обработчику страницы.</span><span class="sxs-lookup"><span data-stu-id="25310-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span> <span data-ttu-id="25310-118">Рекомендуется использовать ASP.NET Core контроллер MVC для страниц с различными требованиями к авторизации для разных обработчиков.</span><span class="sxs-lookup"><span data-stu-id="25310-118">Consider using an ASP.NET Core MVC controller for pages with different authorization requirements for different handlers.</span></span>

<span data-ttu-id="25310-119">Для применения авторизации к Razor методам обработчика страниц можно использовать следующие два подхода:</span><span class="sxs-lookup"><span data-stu-id="25310-119">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

* <span data-ttu-id="25310-120">Используйте отдельные страницы для обработчиков страниц, для которых необходима другая авторизация.</span><span class="sxs-lookup"><span data-stu-id="25310-120">Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="25310-121">Перемещение общего содержимого в одно или несколько [частичных представлений](xref:mvc/views/partial).</span><span class="sxs-lookup"><span data-stu-id="25310-121">Move shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="25310-122">По возможности рекомендуется использовать этот подход.</span><span class="sxs-lookup"><span data-stu-id="25310-122">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="25310-123">Для содержимого, которое должно совместно использовать общую страницу, напишите фильтр, выполняющий авторизацию как часть [иасинкпажефилтер. онпажехандлерселектионасинк](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="25310-123">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="25310-124">Этот подход демонстрируется в проекте [пажехандлераус](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub:</span><span class="sxs-lookup"><span data-stu-id="25310-124">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="25310-125">[Аусоризеиндекспажехандлерфилтер](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) реализует фильтр авторизации:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="25310-125">The [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="25310-126">Атрибут [[аусоризепажехандлер]](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) применяется к `OnGet` обработчику страницы: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="25310-126">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="25310-127">Пример подхода [пажехандлераус](https://github.com/pranavkm/PageHandlerAuth) ***не:***</span><span class="sxs-lookup"><span data-stu-id="25310-127">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does ***not***:</span></span>
> * <span data-ttu-id="25310-128">Составьте атрибуты авторизации, применяемые к странице, модели страницы или глобально.</span><span class="sxs-lookup"><span data-stu-id="25310-128">Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="25310-129">Составление атрибутов авторизации приводит к многократному выполнению проверки подлинности и авторизации, когда `AuthorizeAttribute` `AuthorizeFilter` на страницу также применяются еще один экземпляр или экземпляры.</span><span class="sxs-lookup"><span data-stu-id="25310-129">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="25310-130">Работа в сочетании с остальными ASP.NET Core системы проверки подлинности и авторизации.</span><span class="sxs-lookup"><span data-stu-id="25310-130">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="25310-131">Необходимо убедиться, что использование этого подхода правильно работает для вашего приложения.</span><span class="sxs-lookup"><span data-stu-id="25310-131">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="25310-132">Поддержка `AuthorizeAttribute` обработчиков страниц в не планируется Razor .</span><span class="sxs-lookup"><span data-stu-id="25310-132">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
