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
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>Совместное использование проверки подлинности cookie между приложениями ASP.NET

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

Веб-сайты часто состоят из отдельных веб-приложений, работающих вместе. Чтобы обеспечить единый вход, веб-приложения на сайте должны совместно использовать проверку подлинности cookie . Для поддержки этого сценария в стеке защиты данных разрешено совместное использование cookie проверки подлинности Katana и ASP.NET Core cookie билетов проверки подлинности.

В следующих примерах:

* Для имени проверки подлинности cookie задается общее значение `.AspNet.SharedCookie` .
* Значение `AuthenticationType` задается `Identity.Application` явно или по умолчанию.
* Общее имя приложения позволяет системе защиты данных совместно использовать ключи защиты данных ( `SharedCookieApp` ).
* `Identity.Application` используется в качестве схемы проверки подлинности. Независимо от используемой схемы, ее необходимо использовать постоянно *в* общих приложениях и в cookie качестве схемы по умолчанию или путем явного задания. Схема используется при шифровании и расшифровке cookie s, поэтому согласованная схема должна использоваться в приложениях.
* Используется общее место хранения [ключей защиты данных](xref:security/data-protection/implementation/key-management) .
  * В ASP.NET Core приложениях <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> используется для задания места хранения ключей.
  * В .NET Framework приложениях по Cookie промежуточного слоя для проверки подлинности использует реализацию <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . `DataProtectionProvider` предоставляет службы защиты данных для шифрования и расшифровки полезных данных проверки подлинности cookie . `DataProtectionProvider`Экземпляр изолирован от системы защиты данных, используемой другими частями приложения. [Датапротектионпровидер. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) принимает, <xref:System.IO.DirectoryInfo> чтобы указать расположение для хранилища ключей защиты данных.
* `DataProtectionProvider` требуется пакет NuGet [Microsoft. AspNetCore. MDAC. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * В ASP.NET Core приложения 2. x сослаться на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).
  * В .NET Framework приложения добавьте ссылку на пакет в [Microsoft. AspNetCore. "Защита. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Задает общее имя приложения.

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>Совместное использование проверки подлинности cookie с помощью ASP.NET Core Identity

Применяя ASP.NET Core Identity, помните следующие правила.

* Ключи защиты данных и имя приложения должны быть общими для всех приложений. В следующих примерах для метода предоставляется общий путь к хранилищу ключей <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> . Используйте <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> для настройки общего имени общего приложения ( `SharedCookieApp` в следующих примерах). Для получения дополнительной информации см. <xref:security/data-protection/configuration/overview>.
* Используйте <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> метод расширения, чтобы настроить службу защиты данных для cookie s.
* По умолчанию используется тип проверки подлинности `Identity.Application` .

В `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

**Примечание.** Предыдущие инструкции не работают с `ITicketStore` ( `CookieAuthenticationOptions.SessionStore` ).  Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/21163).

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>Совместное использование проверки подлинности cookie без ASP.NET Core Identity

Если вы используете cookie s напрямую без ASP.NET Core Identity , настройте защиту данных и проверку подлинности в `Startup.ConfigureServices` . В следующем примере тип проверки подлинности имеет значение `Identity.Application` :

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

## <a name="share-no-loccookies-across-different-base-paths"></a>Совместное использование cookie в разных базовых путях

При проверке подлинности cookie по умолчанию используется [HttpRequest. пасбасе](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [ Cookie . Путь](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Если приложение cookie должно совместно использоваться в разных базовых путях, `Path` необходимо переопределить:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>Совместное использование cookie между поддоменами

При размещении приложений, совместно использующих cookie поддомены, укажите общий домен в [ Cookie . Свойство домена](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) . Чтобы предоставить общий доступ к cookie s в приложениях `contoso.com` , например `first_subdomain.contoso.com` и `second_subdomain.contoso.com` , укажите для него значение `Cookie.Domain` `.contoso.com` :

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Шифрование неактивных ключей защиты данных

Для рабочих развертываний настройте `DataProtectionProvider` для шифрования неактивных ключей с помощью DPAPI или x509. Для получения дополнительной информации см. <xref:security/data-protection/implementation/key-encryption-at-rest>. В следующем примере отпечаток сертификата предоставляется для <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>Совместное использование проверки подлинности cookie между ASP.NET 4. x и приложениями ASP.NET Core

Приложения ASP.NET 4. x, использующие Cookie по промежуточного слоя проверки подлинности Katana, можно настроить для создания проверки подлинности cookie , совместимых с Cookie промежуточным по ASP.NET Core проверки подлинности. Это позволяет обновлять отдельные приложения большого сайта за несколько этапов, предоставляя возможность беспрепятственного единого входа на сайте.

Когда приложение использует по Cookie промежуточного слоя проверки подлинности Katana, оно вызывает `UseCookieAuthentication` в файле *Startup.auth.CS* проекта. Проекты веб-приложений ASP.NET 4. x, созданные с помощью Visual Studio 2013 и позднее, Cookie по умолчанию используют по промежуточного слоя проверки подлинности Katana. Хотя `UseCookieAuthentication` является устаревшим и не поддерживается для ASP.NET Core приложений, вызов `UseCookieAuthentication` в приложении ASP.NET 4. x, который использует по Cookie промежуточного слоя проверки подлинности Katana, является допустимым.

Приложение ASP.NET 4. x должно быть предназначено для .NET Framework 4.5.1 или более поздней версии. В противном случае не удается установить необходимые пакеты NuGet.

Чтобы предоставить общий доступ к проверкам подлинности cookie между приложением ASP.NET 4. x и ASP.NET Core приложением, настройте ASP.NET Core приложение, как указано в разделе [совместное использование проверки подлинности cookie между приложениями ASP.NET Core](#share-authentication-cookies-with-aspnet-core-identity) , а затем настройте приложение ASP.NET 4. x следующим образом.

Убедитесь, что пакеты приложения обновлены до последних выпусков. Установите пакет [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) в каждое приложение ASP.NET 4. x.

Нахождение и изменение вызова `UseCookieAuthentication` :

* Измените cookie имя, чтобы оно совпадало с именем, используемым по Cookie промежуточного слоя проверки подлинности ASP.NET Core ( `.AspNet.SharedCookie` в примере).
* В следующем примере тип проверки подлинности имеет значение `Identity.Application` .
* Укажите экземпляр, `DataProtectionProvider` инициализированный в общем расположении хранилища ключей для защиты данных.
* Убедитесь, что для имени приложения задано общее имя приложения, используемое всеми приложениями, которые совместно используют проверку подлинности cookie ( `SharedCookieApp` в примере).

Если не задано значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` и `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , задайте <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> утверждение, которое различает уникальных пользователей.

*App_Start/стартуп.АУС.КС*:

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

При создании удостоверения пользователя тип проверки подлинности ( `Identity.Application` ) должен соответствовать типу, определенному в `AuthenticationType` наборе, `UseCookieAuthentication` в *App_Start/стартуп.АУС.КС*.

*Модели/ Identity Models.cs*:

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

## <a name="use-a-common-user-database"></a>Использование общей пользовательской базы данных

Если приложения используют одну и ту же Identity схему (та же версия Identity ), убедитесь, что Identity система для каждого приложения указывает на одну и ту же базу данных пользователя. В противном случае система идентификации выдает ошибки во время выполнения, когда пытается сопоставить информацию в проверке подлинности cookie с данными в своей базе данных.

Если Identity схема отличается от приложений, обычно поскольку приложения используют разные Identity версии, общий доступ к общей базе данных на основе последней версии Identity невозможен без повторного сопоставления и добавления столбцов в схемах других приложений Identity . Часто более эффективно обновлять другие приложения, чтобы использовать последнюю версию, чтобы общая Identity база данных могла использоваться приложениями.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:host-and-deploy/web-farm>
