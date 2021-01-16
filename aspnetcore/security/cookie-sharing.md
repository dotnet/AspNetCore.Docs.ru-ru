---
title: Совместное использование проверки подлинности cookie между приложениями ASP.NET
author: rick-anderson
description: Узнайте, как совместно использовать проверку подлинности для cookie ASP.NET 4. x и ASP.NET Core приложений.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252998"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="afa84-103">Совместное использование проверки подлинности cookie между приложениями ASP.NET</span><span class="sxs-lookup"><span data-stu-id="afa84-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="afa84-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="afa84-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="afa84-105">Веб-сайты часто состоят из отдельных веб-приложений, работающих вместе.</span><span class="sxs-lookup"><span data-stu-id="afa84-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="afa84-106">Чтобы обеспечить единый вход, веб-приложения на сайте должны совместно использовать проверку подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="afa84-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="afa84-107">Для поддержки этого сценария в стеке защиты данных разрешено совместное использование cookie проверки подлинности Katana и ASP.NET Core cookie билетов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="afa84-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="afa84-108">В следующих примерах:</span><span class="sxs-lookup"><span data-stu-id="afa84-108">In the examples that follow:</span></span>

* <span data-ttu-id="afa84-109">Для имени проверки подлинности cookie задается общее значение `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="afa84-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="afa84-110">Значение `AuthenticationType` задается `Identity.Application` явно или по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="afa84-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="afa84-111">Общее имя приложения позволяет системе защиты данных совместно использовать ключи защиты данных ( `SharedCookieApp` ).</span><span class="sxs-lookup"><span data-stu-id="afa84-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="afa84-112">`Identity.Application` используется в качестве схемы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="afa84-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="afa84-113">Независимо от используемой схемы, ее необходимо использовать постоянно *в* общих приложениях и в cookie качестве схемы по умолчанию или путем явного задания.</span><span class="sxs-lookup"><span data-stu-id="afa84-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="afa84-114">Схема используется при шифровании и расшифровке cookie s, поэтому согласованная схема должна использоваться в приложениях.</span><span class="sxs-lookup"><span data-stu-id="afa84-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="afa84-115">Используется общее место хранения [ключей защиты данных](xref:security/data-protection/implementation/key-management) .</span><span class="sxs-lookup"><span data-stu-id="afa84-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="afa84-116">В ASP.NET Core приложениях <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> используется для задания места хранения ключей.</span><span class="sxs-lookup"><span data-stu-id="afa84-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="afa84-117">В .NET Framework приложениях по Cookie промежуточного слоя для проверки подлинности использует реализацию <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="afa84-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="afa84-118">`DataProtectionProvider` предоставляет службы защиты данных для шифрования и расшифровки полезных данных проверки подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="afa84-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="afa84-119">`DataProtectionProvider`Экземпляр изолирован от системы защиты данных, используемой другими частями приложения.</span><span class="sxs-lookup"><span data-stu-id="afa84-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="afa84-120">[Датапротектионпровидер. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) принимает, <xref:System.IO.DirectoryInfo> чтобы указать расположение для хранилища ключей защиты данных.</span><span class="sxs-lookup"><span data-stu-id="afa84-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="afa84-121">`DataProtectionProvider` требуется пакет NuGet [Microsoft. AspNetCore. MDAC. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="afa84-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="afa84-122">В ASP.NET Core приложения 2. x сослаться на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="afa84-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="afa84-123">В .NET Framework приложения добавьте ссылку на пакет в [Microsoft. AspNetCore. "Защита. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="afa84-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="afa84-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Задает общее имя приложения.</span><span class="sxs-lookup"><span data-stu-id="afa84-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="afa84-125">Совместное использование проверки подлинности cookie с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="afa84-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="afa84-126">Применяя ASP.NET Core Identity, помните следующие правила.</span><span class="sxs-lookup"><span data-stu-id="afa84-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="afa84-127">Ключи защиты данных и имя приложения должны быть общими для всех приложений.</span><span class="sxs-lookup"><span data-stu-id="afa84-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="afa84-128">В следующих примерах для метода предоставляется общий путь к хранилищу ключей <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> .</span><span class="sxs-lookup"><span data-stu-id="afa84-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="afa84-129">Используйте <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> для настройки общего имени общего приложения ( `SharedCookieApp` в следующих примерах).</span><span class="sxs-lookup"><span data-stu-id="afa84-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="afa84-130">Для получения дополнительной информации см. <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="afa84-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="afa84-131">Используйте <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> метод расширения, чтобы настроить службу защиты данных для cookie s.</span><span class="sxs-lookup"><span data-stu-id="afa84-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="afa84-132">По умолчанию используется тип проверки подлинности `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="afa84-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="afa84-133">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="afa84-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="afa84-134">**Примечание.** Предыдущие инструкции не работают с `ITicketStore` ( `CookieAuthenticationOptions.SessionStore` ).</span><span class="sxs-lookup"><span data-stu-id="afa84-134">**Note:** The preceding instructions don't work with `ITicketStore` (`CookieAuthenticationOptions.SessionStore`).</span></span>  <span data-ttu-id="afa84-135">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span><span class="sxs-lookup"><span data-stu-id="afa84-135">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span></span>

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="afa84-136">Совместное использование проверки подлинности cookie без ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="afa84-136">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="afa84-137">Если вы используете cookie s напрямую без ASP.NET Core Identity , настройте защиту данных и проверку подлинности в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="afa84-137">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="afa84-138">В следующем примере тип проверки подлинности имеет значение `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="afa84-138">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="afa84-139">Совместное использование cookie в разных базовых путях</span><span class="sxs-lookup"><span data-stu-id="afa84-139">Share cookies across different base paths</span></span>

<span data-ttu-id="afa84-140">При проверке подлинности cookie по умолчанию используется [HttpRequest. пасбасе](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [ Cookie . Путь](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="afa84-140">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="afa84-141">Если приложение cookie должно совместно использоваться в разных базовых путях, `Path` необходимо переопределить:</span><span class="sxs-lookup"><span data-stu-id="afa84-141">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="afa84-142">Совместное использование cookie между поддоменами</span><span class="sxs-lookup"><span data-stu-id="afa84-142">Share cookies across subdomains</span></span>

<span data-ttu-id="afa84-143">При размещении приложений, совместно использующих cookie поддомены, укажите общий домен в [ Cookie . Свойство домена](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) .</span><span class="sxs-lookup"><span data-stu-id="afa84-143">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="afa84-144">Чтобы предоставить общий доступ к cookie s в приложениях `contoso.com` , например `first_subdomain.contoso.com` и `second_subdomain.contoso.com` , укажите для него значение `Cookie.Domain` `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="afa84-144">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="afa84-145">Шифрование неактивных ключей защиты данных</span><span class="sxs-lookup"><span data-stu-id="afa84-145">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="afa84-146">Для рабочих развертываний настройте `DataProtectionProvider` для шифрования неактивных ключей с помощью DPAPI или x509.</span><span class="sxs-lookup"><span data-stu-id="afa84-146">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="afa84-147">Для получения дополнительной информации см. <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="afa84-147">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="afa84-148">В следующем примере отпечаток сертификата предоставляется для <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="afa84-148">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="afa84-149">Совместное использование проверки подлинности cookie между ASP.NET 4. x и приложениями ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="afa84-149">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="afa84-150">Приложения ASP.NET 4. x, использующие Cookie по промежуточного слоя проверки подлинности Katana, можно настроить для создания проверки подлинности cookie , совместимых с Cookie промежуточным по ASP.NET Core проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="afa84-150">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="afa84-151">Это позволяет обновлять отдельные приложения большого сайта за несколько этапов, предоставляя возможность беспрепятственного единого входа на сайте.</span><span class="sxs-lookup"><span data-stu-id="afa84-151">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="afa84-152">Когда приложение использует по Cookie промежуточного слоя проверки подлинности Katana, оно вызывает `UseCookieAuthentication` в файле *Startup.auth.CS* проекта.</span><span class="sxs-lookup"><span data-stu-id="afa84-152">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="afa84-153">Проекты веб-приложений ASP.NET 4. x, созданные с помощью Visual Studio 2013 и позднее, Cookie по умолчанию используют по промежуточного слоя проверки подлинности Katana.</span><span class="sxs-lookup"><span data-stu-id="afa84-153">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="afa84-154">Хотя `UseCookieAuthentication` является устаревшим и не поддерживается для ASP.NET Core приложений, вызов `UseCookieAuthentication` в приложении ASP.NET 4. x, который использует по Cookie промежуточного слоя проверки подлинности Katana, является допустимым.</span><span class="sxs-lookup"><span data-stu-id="afa84-154">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="afa84-155">Приложение ASP.NET 4. x должно быть предназначено для .NET Framework 4.5.1 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="afa84-155">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="afa84-156">В противном случае не удается установить необходимые пакеты NuGet.</span><span class="sxs-lookup"><span data-stu-id="afa84-156">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="afa84-157">Чтобы предоставить общий доступ к проверкам подлинности cookie между приложением ASP.NET 4. x и ASP.NET Core приложением, настройте ASP.NET Core приложение, как указано в разделе [совместное использование проверки подлинности cookie между приложениями ASP.NET Core](#share-authentication-cookies-with-aspnet-core-identity) , а затем настройте приложение ASP.NET 4. x следующим образом.</span><span class="sxs-lookup"><span data-stu-id="afa84-157">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="afa84-158">Убедитесь, что пакеты приложения обновлены до последних выпусков.</span><span class="sxs-lookup"><span data-stu-id="afa84-158">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="afa84-159">Установите пакет [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) в каждое приложение ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="afa84-159">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="afa84-160">Нахождение и изменение вызова `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="afa84-160">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="afa84-161">Измените cookie имя, чтобы оно совпадало с именем, используемым по Cookie промежуточного слоя проверки подлинности ASP.NET Core ( `.AspNet.SharedCookie` в примере).</span><span class="sxs-lookup"><span data-stu-id="afa84-161">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="afa84-162">В следующем примере тип проверки подлинности имеет значение `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="afa84-162">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="afa84-163">Укажите экземпляр, `DataProtectionProvider` инициализированный в общем расположении хранилища ключей для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="afa84-163">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="afa84-164">Убедитесь, что для имени приложения задано общее имя приложения, используемое всеми приложениями, которые совместно используют проверку подлинности cookie ( `SharedCookieApp` в примере).</span><span class="sxs-lookup"><span data-stu-id="afa84-164">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="afa84-165">Если не задано значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` и `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , задайте <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> утверждение, которое различает уникальных пользователей.</span><span class="sxs-lookup"><span data-stu-id="afa84-165">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="afa84-166">*App_Start/стартуп.АУС.КС*:</span><span class="sxs-lookup"><span data-stu-id="afa84-166">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="afa84-167">При создании удостоверения пользователя тип проверки подлинности ( `Identity.Application` ) должен соответствовать типу, определенному в `AuthenticationType` наборе, `UseCookieAuthentication` в *App_Start/стартуп.АУС.КС*.</span><span class="sxs-lookup"><span data-stu-id="afa84-167">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="afa84-168">*Модели/ Identity Models.cs*:</span><span class="sxs-lookup"><span data-stu-id="afa84-168">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="afa84-169">Использование общей пользовательской базы данных</span><span class="sxs-lookup"><span data-stu-id="afa84-169">Use a common user database</span></span>

<span data-ttu-id="afa84-170">Если приложения используют одну и ту же Identity схему (та же версия Identity ), убедитесь, что Identity система для каждого приложения указывает на одну и ту же базу данных пользователя.</span><span class="sxs-lookup"><span data-stu-id="afa84-170">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="afa84-171">В противном случае система идентификации выдает ошибки во время выполнения, когда пытается сопоставить информацию в проверке подлинности cookie с данными в своей базе данных.</span><span class="sxs-lookup"><span data-stu-id="afa84-171">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="afa84-172">Если Identity схема отличается от приложений, обычно поскольку приложения используют разные Identity версии, общий доступ к общей базе данных на основе последней версии Identity невозможен без повторного сопоставления и добавления столбцов в схемах других приложений Identity .</span><span class="sxs-lookup"><span data-stu-id="afa84-172">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="afa84-173">Часто более эффективно обновлять другие приложения, чтобы использовать последнюю версию, чтобы общая Identity база данных могла использоваться приложениями.</span><span class="sxs-lookup"><span data-stu-id="afa84-173">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="afa84-174">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="afa84-174">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
