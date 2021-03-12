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
# <a name="simple-authorization-in-aspnet-core"></a>Простая Авторизация в ASP.NET Core

<a name="security-authorization-simple"></a>

Авторизация в ASP.NET Core контролируется с помощью <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> и различных параметров. В простейшей форме применение `[Authorize]` атрибута к контроллеру, действию или Razor странице ограничивает доступ к этому компоненту для любого пользователя, прошедшего проверку подлинности.

Например, следующий код ограничивает доступ к `AccountController` любому пользователю, прошедшему проверку подлинности.

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

Если вы хотите применить авторизацию к действию, а не к контроллеру, примените `AuthorizeAttribute` атрибут к самому действию:

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

Теперь доступ к функции могут получить только пользователи, прошедшие проверку подлинности `Logout` .

Можно также использовать атрибут, `AllowAnonymous` чтобы разрешить пользователям без проверки подлинности выполнять отдельные действия. Пример:

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

Это позволит использовать только пользователей, прошедших проверку подлинности `AccountController` , за исключением `Login` действия, доступного для всех, независимо от их состояния с проверкой подлинности или без проверки подлинности или анонимного пользователя.

> [!WARNING]
> `[AllowAnonymous]` обходит все инструкции авторизации. Если объединить `[AllowAnonymous]` и любой `[Authorize]` атрибут, `[Authorize]` атрибуты игнорируются. Например, при применении `[AllowAnonymous]` на уровне контроллера все `[Authorize]` атрибуты на том же контроллере (или в любом действии в нем) игнорируются.

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a>Атрибут authorize и Razor Pages

<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ***Не*** может применяться к Razor обработчикам страниц. Например, `[Authorize]` нельзя применить к `OnGet` , или к `OnPost` любому другому обработчику страницы. Рекомендуется использовать ASP.NET Core контроллер MVC для страниц с различными требованиями к авторизации для разных обработчиков.

Для применения авторизации к Razor методам обработчика страниц можно использовать следующие два подхода:

* Используйте отдельные страницы для обработчиков страниц, для которых необходима другая авторизация. Перемещение общего содержимого в одно или несколько [частичных представлений](xref:mvc/views/partial). По возможности рекомендуется использовать этот подход.
* Для содержимого, которое должно совместно использовать общую страницу, напишите фильтр, выполняющий авторизацию как часть [иасинкпажефилтер. онпажехандлерселектионасинк](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A). Этот подход демонстрируется в проекте [пажехандлераус](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub:
  * [Аусоризеиндекспажехандлерфилтер](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) реализует фильтр авторизации:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * Атрибут [[аусоризепажехандлер]](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) применяется к `OnGet` обработчику страницы: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> Пример подхода [пажехандлераус](https://github.com/pranavkm/PageHandlerAuth) ***не:***
> * Составьте атрибуты авторизации, применяемые к странице, модели страницы или глобально. Составление атрибутов авторизации приводит к многократному выполнению проверки подлинности и авторизации, когда `AuthorizeAttribute` `AuthorizeFilter` на страницу также применяются еще один экземпляр или экземпляры.
> * Работа в сочетании с остальными ASP.NET Core системы проверки подлинности и авторизации. Необходимо убедиться, что использование этого подхода правильно работает для вашего приложения.

Поддержка `AuthorizeAttribute` обработчиков страниц в не планируется Razor . 
