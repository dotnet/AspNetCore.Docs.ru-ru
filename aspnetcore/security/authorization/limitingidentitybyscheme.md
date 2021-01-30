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
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="5044a-103">Авторизация с помощью определенной схемы в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5044a-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="5044a-104">Общие сведения о схемах проверки подлинности в ASP.NET Core см. в разделе [Схема проверки подлинности](xref:security/authentication/index#authentication-scheme).</span><span class="sxs-lookup"><span data-stu-id="5044a-104">For an introduction to authentication schemes in ASP.NET Core, see [Authentication scheme](xref:security/authentication/index#authentication-scheme).</span></span>

<span data-ttu-id="5044a-105">В некоторых сценариях, таких как одностраничные приложения (одностраничные приложения), обычно используется несколько методов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5044a-105">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="5044a-106">Например, приложение может использовать cookie проверку подлинности на основе проверки подлинности при входе и JWT Bearer для запросов JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5044a-106">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="5044a-107">В некоторых случаях приложение может иметь несколько экземпляров обработчика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5044a-107">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="5044a-108">Например, два cookie обработчика, где один из них содержит базовое удостоверение, и один создается при активации многофакторной проверки подлинности (MFA).</span><span class="sxs-lookup"><span data-stu-id="5044a-108">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="5044a-109">MFA может активироваться, так как пользователь запросил операцию, требующую дополнительной защиты.</span><span class="sxs-lookup"><span data-stu-id="5044a-109">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="5044a-110">Дополнительные сведения о применении MFA, когда пользователь запрашивает ресурс, требующий MFA, см. в разделе "Защита от проблем GitHub" [с MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span><span class="sxs-lookup"><span data-stu-id="5044a-110">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="5044a-111">Схема проверки подлинности называется, когда служба проверки подлинности настроена во время проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5044a-111">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="5044a-112">Например:</span><span class="sxs-lookup"><span data-stu-id="5044a-112">For example:</span></span>

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

<span data-ttu-id="5044a-113">В приведенном выше коде были добавлены два обработчика проверки подлинности: один для cookie s и один для носителя.</span><span class="sxs-lookup"><span data-stu-id="5044a-113">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="5044a-114">При указании схемы по умолчанию `HttpContext.User` задается свойство, которому присваивается это удостоверение.</span><span class="sxs-lookup"><span data-stu-id="5044a-114">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="5044a-115">Если такое поведение не требуется, отключите его, вызвав форму без параметров `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="5044a-115">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="5044a-116">Выбор схемы с помощью атрибута авторизации</span><span class="sxs-lookup"><span data-stu-id="5044a-116">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="5044a-117">В точке авторизации приложение указывает, какой обработчик следует использовать.</span><span class="sxs-lookup"><span data-stu-id="5044a-117">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="5044a-118">Выберите обработчик, с помощью которого приложение будет выполнять авторизацию, передав список схем проверки подлинности с разделителями-запятыми в `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="5044a-118">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="5044a-119">`[Authorize]`Атрибут указывает схему или схемы проверки подлинности, которые будут использоваться независимо от того, настроено ли значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5044a-119">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="5044a-120">Например:</span><span class="sxs-lookup"><span data-stu-id="5044a-120">For example:</span></span>

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

<span data-ttu-id="5044a-121">В предыдущем примере cookie выполняются обработчики и носителя, а также возможность создания и добавления удостоверения для текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="5044a-121">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="5044a-122">Если указать только одну схему, выполняется соответствующий обработчик.</span><span class="sxs-lookup"><span data-stu-id="5044a-122">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="5044a-123">В приведенном выше коде выполняется только обработчик со схемой "Bearer".</span><span class="sxs-lookup"><span data-stu-id="5044a-123">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="5044a-124">Все cookie удостоверения на основе данных игнорируются.</span><span class="sxs-lookup"><span data-stu-id="5044a-124">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="5044a-125">Выбор схемы с политиками</span><span class="sxs-lookup"><span data-stu-id="5044a-125">Selecting the scheme with policies</span></span>

<span data-ttu-id="5044a-126">Если вы предпочитаете указывать нужные схемы в [политике](xref:security/authorization/policies), вы можете задать `AuthenticationSchemes` коллекцию при добавлении политики:</span><span class="sxs-lookup"><span data-stu-id="5044a-126">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="5044a-127">В предыдущем примере политика "Over18" выполняется только для удостоверения, созданного обработчиком "Bearer".</span><span class="sxs-lookup"><span data-stu-id="5044a-127">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="5044a-128">Используйте политику, задав `[Authorize]` `Policy` свойство атрибута:</span><span class="sxs-lookup"><span data-stu-id="5044a-128">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="5044a-129">Использование нескольких схем проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="5044a-129">Use multiple authentication schemes</span></span>

<span data-ttu-id="5044a-130">Некоторым приложениям может потребоваться поддержка нескольких типов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5044a-130">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="5044a-131">Например, приложение может выполнять проверку подлинности пользователей из Azure Active Directory и из базы данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="5044a-131">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="5044a-132">Другим примером является приложение, которое выполняет проверку подлинности пользователей как с службы федерации Active Directory (AD FS), так Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="5044a-132">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="5044a-133">В этом случае приложение должно принять токен носителя JWT из нескольких издателей.</span><span class="sxs-lookup"><span data-stu-id="5044a-133">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="5044a-134">Добавьте все схемы проверки подлинности, которые вы хотите принять.</span><span class="sxs-lookup"><span data-stu-id="5044a-134">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="5044a-135">Например, следующий код `Startup.ConfigureServices` добавляет две схемы проверки подлинности JWT Bearer с разными издателями:</span><span class="sxs-lookup"><span data-stu-id="5044a-135">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

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
> <span data-ttu-id="5044a-136">В схеме проверки подлинности по умолчанию регистрируется только одна проверка подлинности носителя JWT `JwtBearerDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="5044a-136">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="5044a-137">Дополнительную проверку подлинности необходимо зарегистрировать с помощью уникальной схемы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5044a-137">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="5044a-138">Следующим шагом является обновление политики авторизации по умолчанию для принятия обеих схем проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5044a-138">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="5044a-139">Например:</span><span class="sxs-lookup"><span data-stu-id="5044a-139">For example:</span></span>

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

<span data-ttu-id="5044a-140">По мере переопределения политики авторизации по умолчанию можно использовать `[Authorize]` атрибут в контроллерах.</span><span class="sxs-lookup"><span data-stu-id="5044a-140">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="5044a-141">Затем контроллер принимает запросы с маркером JWT, выданным первым или вторым издателем.</span><span class="sxs-lookup"><span data-stu-id="5044a-141">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

<span data-ttu-id="5044a-142">Сведения об использовании нескольких схем проверки подлинности см. в [этой статье](https://github.com/dotnet/aspnetcore/issues/26002) на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="5044a-142">See [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/26002) on using multiple authentication schemes.</span></span>
