---
title: Авторизация с помощью определенной схемы в ASP.NET Core
author: rick-anderson
description: В этой статье объясняется, как ограничить идентификацию определенной схемой при работе с несколькими методами проверки подлинности.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: c4cbec1b829fb8fd47f7b6924b6870bd5dd7097d
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057308"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>Авторизация с помощью определенной схемы в ASP.NET Core

Общие сведения о схемах проверки подлинности в ASP.NET Core см. в разделе [Схема проверки подлинности](xref:security/authentication/index#authentication-scheme).

В некоторых сценариях, таких как одностраничные приложения (одностраничные приложения), обычно используется несколько методов проверки подлинности. Например, приложение может использовать cookie проверку подлинности на основе проверки подлинности при входе и JWT Bearer для запросов JavaScript. В некоторых случаях приложение может иметь несколько экземпляров обработчика проверки подлинности. Например, два cookie обработчика, где один из них содержит базовое удостоверение, и один создается при активации многофакторной проверки подлинности (MFA). MFA может активироваться, так как пользователь запросил операцию, требующую дополнительной защиты. Дополнительные сведения о применении MFA, когда пользователь запрашивает ресурс, требующий MFA, см. в разделе "Защита от проблем GitHub" [с MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).

Схема проверки подлинности называется, когда служба проверки подлинности настроена во время проверки подлинности. Например:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

В приведенном выше коде были добавлены два обработчика проверки подлинности: один для cookie s и один для носителя.

>[!NOTE]
>При указании схемы по умолчанию `HttpContext.User` задается свойство, которому присваивается это удостоверение. Если такое поведение не требуется, отключите его, вызвав форму без параметров `AddAuthentication` .

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Выбор схемы с помощью атрибута авторизации

В точке авторизации приложение указывает, какой обработчик следует использовать. Выберите обработчик, с помощью которого приложение будет выполнять авторизацию, передав список схем проверки подлинности с разделителями-запятыми в `[Authorize]` . `[Authorize]`Атрибут указывает схему или схемы проверки подлинности, которые будут использоваться независимо от того, настроено ли значение по умолчанию. Например:

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

В предыдущем примере cookie выполняются обработчики и носителя, а также возможность создания и добавления удостоверения для текущего пользователя. Если указать только одну схему, выполняется соответствующий обработчик.

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

В приведенном выше коде выполняется только обработчик со схемой "Bearer". Все cookie удостоверения на основе данных игнорируются.

## <a name="selecting-the-scheme-with-policies"></a>Выбор схемы с политиками

Если вы предпочитаете указывать нужные схемы в [политике](xref:security/authorization/policies), вы можете задать `AuthenticationSchemes` коллекцию при добавлении политики:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

В предыдущем примере политика "Over18" выполняется только для удостоверения, созданного обработчиком "Bearer". Используйте политику, задав `[Authorize]` `Policy` свойство атрибута:

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Использование нескольких схем проверки подлинности

Некоторым приложениям может потребоваться поддержка нескольких типов проверки подлинности. Например, приложение может выполнять проверку подлинности пользователей из Azure Active Directory и из базы данных пользователей. Другим примером является приложение, которое выполняет проверку подлинности пользователей как с службы федерации Active Directory (AD FS), так Azure Active Directory B2C. В этом случае приложение должно принять токен носителя JWT из нескольких издателей.

Добавьте все схемы проверки подлинности, которые вы хотите принять. Например, следующий код `Startup.ConfigureServices` добавляет две схемы проверки подлинности JWT Bearer с разными издателями:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> В схеме проверки подлинности по умолчанию регистрируется только одна проверка подлинности носителя JWT `JwtBearerDefaults.AuthenticationScheme` . Дополнительную проверку подлинности необходимо зарегистрировать с помощью уникальной схемы проверки подлинности.

Следующим шагом является обновление политики авторизации по умолчанию для принятия обеих схем проверки подлинности. Например:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

По мере переопределения политики авторизации по умолчанию можно использовать `[Authorize]` атрибут в контроллерах. Затем контроллер принимает запросы с маркером JWT, выданным первым или вторым издателем.

::: moniker-end

Сведения об использовании нескольких схем проверки подлинности см. в [этой статье](https://github.com/dotnet/aspnetcore/issues/26002) на сайте GitHub.
