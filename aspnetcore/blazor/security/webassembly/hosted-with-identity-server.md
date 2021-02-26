---
title: Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью IdentityServer
author: guardrex
description: Узнайте, как защитить размещенное приложение ASP.NET Core Blazor WebAssembly с помощью Identity Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: c74711c10fe399718600f879c3d9151bfb1abd42
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281006"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Identity Server

В этой статье объясняется, как создать новое [размещенное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), которое использует [IdentityServer](https://identityserver.io/) для аутентификации пользователей и вызовов API.

> [!NOTE]
> Чтобы настроить изолированное или размещенное приложение Blazor WebAssembly для использования существующего внешнего экземпляра Identity Server, следуйте рекомендациям в статье <xref:blazor/security/webassembly/standalone-with-authentication-library>.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:

1. Выбрав шаблон **Приложение Blazor WebAssembly** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.

1. Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении**, чтобы хранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core.

1. Установите флажок **С размещением в ASP.NET Core** в разделе **Дополнительно**.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code или .NET Core CLI](#tab/visual-studio-code+netcore-cli)

Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации в пустой папке, укажите механизм аутентификации `Individual` с параметром `-au|--auth` для хранения пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core:

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| Заполнитель  | Пример        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.

См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:

1. На шаге **Настройка нового приложения Blazor WebAssembly** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.

1. Приложение будет создано для отдельных пользователей, хранимых в приложении, с помощью [Identity](xref:security/authentication/identity) ASP.NET Core.

1. Установите флажок **С размещением в ASP.NET Core**.

---

## <a name="server-app-configuration"></a>Конфигурация приложения *`Server`*

В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.

### <a name="startup-class"></a>Класс Startup

В классе `Startup` представлены следующие дополнения:

* В `Startup.ConfigureServices`:

  * ASP.NET Core Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer с дополнительным вспомогательным методом <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>, который устанавливает соглашения ASP.NET Core по умолчанию поверх IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Аутентификация с помощью дополнительного вспомогательного метода <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* В `Startup.Configure`:

  * ПО промежуточного слоя IdentityServer предоставляет конечные точки OpenID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * ПО промежуточного слоя для проверки подлинности отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:

    ```csharp
    app.UseAuthentication();
    ```

  * ПО промежуточного слоя для проверки подлинности обеспечивает возможности авторизации:

    ```csharp
    app.UseAuthorization();
    ```

### <a name="azure-app-service-on-linux"></a>Служба приложений Azure в Linux

При развертывании в Службе приложений Azure в Linux издателя нужно указать явно. Для получения дополнительной информации см. <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

### <a name="addapiauthorization"></a>AddApiAuthorization

Вспомогательный метод <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core. IdentityServer — это функциональная и расширяемая платформа для повышения уровня безопасности приложений. IdentityServer указывает на ненужную сложность в некоторых распространенных сценариях. Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые можно рассматривать в качестве хорошей отправной точки. Если нужно изменить требования к аутентификации, можно воспользоваться широкими возможностями IdentityServer для настройки аутентификации в соответствии с требованиями приложения.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Вспомогательный метод <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию. Политика настроена так, чтобы разрешить Identity обработку всех запросов, направляемых по любому вложенному пути в пространстве URL-адресов Identity `/Identity`. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> обрабатывает все остальные запросы. Кроме того, этот метод:

* Регистрирует ресурс API `{APPLICATION NAME}API` в IdentityServer с областью по умолчанию `{APPLICATION NAME}API`.
* Настраивает промежуточное ПО для маркера носителя JWT для проверки маркеров, выданных IdentityServer для приложения.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

В `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) к классу применяется [атрибут `[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute). Атрибут указывает, что пользователь должен быть авторизован на основе политики по умолчанию, чтобы получить доступ к ресурсу. Политика авторизации по умолчанию настроена на использование схемы проверки подлинности по умолчанию, которая настраивается <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>. Вспомогательный метод настраивает <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> в качестве обработчика по умолчанию для запросов к приложению.

### <a name="applicationdbcontext"></a>ApplicationDbContext

В `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) <xref:Microsoft.EntityFrameworkCore.DbContext> расширяет <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer. Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных классов Identity <xref:Microsoft.EntityFrameworkCore.DbContext> и настройте контекст для включения схемы Identity путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` в методе <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

В `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) выполняется подготовка конечной точки клиента для обслуживания параметров OIDC.

### <a name="app-settings"></a>Параметры приложения

В файле параметров приложения (`appsettings.json`) в корневом каталоге проекта в разделе `IdentityServer` описывается список настроенных клиентов. В следующем примере есть один клиент. Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth. Профиль указывает на настраиваемый тип приложения. Профиль используется внутри для обозначения соглашений, упрощающих процесс настройки сервера. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.Client`).

## <a name="client-app-configuration"></a>Конфигурация приложения *`Client`*

### <a name="authentication-package"></a>Пакет проверки подлинности

Когда приложение создается для использования отдельных учетных записей пользователей (`Individual`), оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в файле проекта приложения. В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.

При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).

### <a name="httpclient-configuration"></a>Конфигурация `HttpClient`

В `Program.Main` (`Program.cs`) именованный <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) настроен для предоставления экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при отправке запросов к API сервера:

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> Если вы хотите настроить приложение Blazor WebAssembly на использование существующего экземпляра IdentityServer, который не является частью размещенного решения Blazor, измените регистрацию базового адреса <xref:System.Net.Http.HttpClient> с <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) на URL-адрес конечной точки авторизации API серверного приложения.

### <a name="api-authorization-support"></a>Поддержка авторизации API

Поддержка проверки подлинности пользователей подключается в контейнере службы с помощью метода расширения, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication). Этот метод настраивает службы, необходимые для взаимодействия с существующей системой авторизации.

```csharp
builder.Services.AddApiAuthorization();
```

По умолчанию конфигурация приложения загружается по соглашению от `_configuration/{client-id}`. По соглашению в качестве идентификатора клиента используется имя сборки приложения. Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.

### <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a>Страница индексов

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a>Компонент RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Компонент LoginDisplay

Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.

* Для пользователей, прошедших проверку подлинности:
  * Отображает имя текущего пользователя.
  * Отображает ссылку на страницу профиля пользователя в ASP.NET Core Identity.
  * Отображает кнопку для выхода из приложения.
* Для анонимных пользователей:
  * Предоставляет возможность регистрации.
  * Предоставляет возможность входа в систему.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Компонент проверки подлинности

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a>Компонент FetchData

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a>Запуск приложения

Запустите приложение из серверного проекта. При использовании Visual Studio выполните одно из следующих действий.

* На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.
* В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.

## <a name="name-and-role-claim-with-api-authorization"></a>Утверждение имени и роли с помощью авторизации API

### <a name="custom-user-factory"></a>Настраиваемая фабрика пользователей

В приложении *`Client`* создайте настраиваемую фабрику пользователей. Identity Server отправляет несколько ролей в виде массива JSON в одном утверждении `role`. Одна роль отправляется как строковое значение в утверждении. Фабрика создает отдельное утверждение `role` для каждой из ролей пользователя.

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

В приложении *`Client`* зарегистрируйте фабрику в `Program.Main` (`Program.cs`):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

В приложении *`Server`* вызовите <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> в построителе Identity, который добавляет службы, связанные с ролями:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Настройка Identity Server

Воспользуйтесь **одним** из перечисленных ниже подходов.

* [Параметры авторизации API](#api-authorization-options)
* [Служба профилей](#profile-service)

#### <a name="api-authorization-options"></a>Параметры авторизации API

В приложении *`Server`* :

* Настройте Identity Server для размещения утверждений `name` и `role` в маркере идентификации и маркере доступа.
* Запретите сопоставление по умолчанию для ролей в обработчике маркера JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Служба профилей

В приложении *`Server`* создайте реализацию `ProfileService`.

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

В приложении *`Server`* зарегистрируйте службу профиля в `Startup.ConfigureServices`:

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Использование механизмов авторизации

На этом этапе подходы с авторизацией компонентов в приложении *`Client`* являются функциональными. Любой из механизмов авторизации в компонентах может использовать роль для авторизации пользователя:

* [Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)
* [Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)
* [Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)

  Поддерживается тестирование с использованием нескольких ролей:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

Для `User.Identity.Name` в приложении *`Client`* указывается имя пользователя, которое обычно является адресом электронной почты для входа.

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain-and-certificate"></a>Размещение в Службе приложений Azure с использованием личного домена и сертификата

В следующем руководстве описывается:

* как развернуть размещенное приложение Blazor WebAssembly с Identity Server в [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) с использованием личного домена;
* как создать и использовать TLS-сертификат для обмена данными по протоколу HTTPS с браузерами. Хотя руководство посвящено использованию сертификата с личным доменом, рекомендации также можно применять при работе с доменом приложений Azure по умолчанию, например `contoso.azurewebsites.net`.

В этом сценарии размещения **не** используйте один и тот же сертификат для [ключа подписывания токена Identity Server](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) и защищенного обмена данными по протоколу HTTPS между сайтом и браузерами.

* Использование разных сертификатов в этих целях рекомендуется из соображений безопасности, так как позволяет изолировать закрытые ключи разного назначения.
* Управление TLS-сертификатами для обмена данными с браузерами производится независимо и не влияет на подписывание маркеров Identity Server.
* Когда [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) предоставляет сертификат приложению Службы приложений для привязки к личному домену, Identity Server не может получить тот же сертификат из Azure Key Vault для подписывания токена. Хотя Identity Server можно настроить так, чтобы он использовал тот же TLS-сертификат по физическому пути, размещать сертификаты безопасности в системе управления версиями **не рекомендуется, в большинстве случаев этого следует избегать**.

Далее в Azure Key Vault создается самозаверяющий сертификат исключительно для подписывания токена Identity Server. Конфигурация Identity Server использует сертификат из хранилища ключей посредством хранилища сертификатов `My` > `CurrentUser` приложения. Другие сертификаты, используемые для трафика HTTPS личных доменов, создаются и настраиваются отдельно от сертификата подписывания Identity Server.

Чтобы настроить приложение, Службу приложений Azure и Azure Key Vault для размещения в личном домене с HTTPS, выполните указанные ниже действия.

1. Создайте [план Службы приложений](/azure/app-service/overview-hosting-plans) уровня `Basic B1` или выше. Для использования личных доменов Службе приложений требуется уровень служб `Basic B1` или более высокий.
1. Создайте PFX-сертификат для безопасного обмена данными между сайтом и браузером (по протоколу HTTPS) с полным доменным именем (FQDN) сайта, которое контролирует организация (например, `www.contoso.com`), в качестве общего имени. Создайте сертификат с указанными ниже параметрами.
   * Ключ использует:
     * проверку цифровой подписи (`digitalSignature`);
     * шифрование ключей (`keyEncipherment`).
   * Расширенное использование ключа:
     * проверка подлинности клиента (1.3.6.1.5.5.7.3.2);
     * проверка подлинности сервера (1.3.6.1.5.5.7.3.1).

   Чтобы создать сертификат, используйте один из следующих подходов или любое другое подходящее средство или веб-службу.

   * [Хранилище ключей Azure](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [MakeCert в Windows](/windows/desktop/seccrypto/makecert)
   * [OpenSSL](https://www.openssl.org)

   Запишите пароль, который будет использоваться позже для импорта сертификата в Azure Key Vault.

   Дополнительные сведения о сертификатах Azure Key Vault см. в статье [Azure Key Vault. Сертификаты](/azure/key-vault/certificates/).
1. Создайте хранилище Azure Key Vault или используйте уже имеющееся в подписке Azure.
1. В области **Сертификаты** хранилища ключей импортируйте сертификат сайта PFX. Запишите отпечаток сертификата, который будет использоваться позже в конфигурации приложения.
1. В Azure Key Vault создайте самозаверяющий сертификат для подписывания токена Identity Server. Укажите **имя сертификата** и **субъект**. **Субъект** указывается в формате `CN={COMMON NAME}`, где заполнитель `{COMMON NAME}` — это общее имя сертификата. Общим именем может быть любая буквенно-цифровая строка. Например, `CN=IdentityServerSigning` — допустимый **субъект** сертификата. В разделе **Расширенная настройка политик** оставьте значения по умолчанию. Запишите отпечаток сертификата, который будет использоваться позже в конфигурации приложения.
1. Перейдите к Службе приложений Azure на портале Azure и создайте Службу приложений со следующей конфигурацией:
   * **Опубликовать** — `Code`;
   * **Стек времени выполнения** — среда выполнения приложения;
   * **Номер SKU и размер** — уровень Службы приложений не ниже `Basic B1`.  Для использования личных доменов Службе приложений требуется уровень служб `Basic B1` или более высокий.
1. После того как в Azure будет создана Служба приложений, откройте **конфигурацию приложения** и добавьте новый параметр, указав записанные ранее отпечатки сертификатов. Ключ параметра — `WEBSITE_LOAD_CERTIFICATES`. В значении параметра разделяйте отпечатки запятыми, как показано в следующем примере.
   * Ключ: `WEBSITE_LOAD_CERTIFICATES`.
   * Значение: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`

   На портале Azure параметры приложения сохраняются в два шага: сохраните параметр `WEBSITE_LOAD_CERTIFICATES`, а затем в верхней части колонки нажмите кнопку **Сохранить**.
1. Выберите **параметры TLS/SSL** приложения. Выберите **Сертификаты закрытых ключей (PFX)** . Дважды выполните процесс **Импорт сертификата Key Vault**, чтобы импортировать и сертификат сайта для обмена данными по протоколу HTTPS, и самозаверяющий сертификат сайта для подписывания токена Identity Server.
1. Перейдите к колонке **Личные домены**. На веб-сайте регистратора своего домена используйте **IP-адрес** и **идентификатор проверки личного домена** для настройки домена. Типичная конфигурация домена включает в себя следующие параметры:
   * **запись A** с **узлом** `@` и значением IP-адреса с портала Azure;
   * **запись TXT** с **узлом** `asuid` и значением идентификатора проверки, созданным Azure и предоставленным на портале Azure.

   Надлежащим образом сохраните изменения на веб-сайте регистратора домена. На веб-сайтах некоторых регистраторов записи домена сохраняются в два шага: сначала нужно по отдельности сохранить одну запись или несколько, а затем обновить регистрацию домена с помощью специальной кнопки.
1. Вернитесь к колонке **Личные домены** на портале Azure. Нажмите кнопку **Добавить личный домен**. Выберите параметр **Запись A**. Укажите домен и выберите команду **Проверить**. Если записи домена верны и распространены в Интернете, на портале можно будет нажать кнопку **Добавить личный домен**.

   На распространение изменений регистрации домена на серверах доменных имен (DNS) в Интернете после их обработки регистратором домена может потребоваться несколько дней. Если записи домена не были обновлены в течение трех рабочих дней, убедитесь в том, что они были правильно заданы на сайте регистратора, и обратитесь в его службу поддержки клиентов.
1. В колонке **Личные домены** **СОСТОЯНИЕ SSL** домена должно иметь значение `Not Secure`. Щелкните ссылку **Добавить привязку**. Выберите HTTPS-сертификат сайта из хранилища ключей для привязки личного домена.
1. В Visual Studio откройте файл параметров приложения для *серверного* проекта (`appsettings.json` или `appsettings.Production.json`). В конфигурации Identity Server добавьте приведенный ниже раздел `Key`. Укажите **субъект** самозаверяющего сертификата для ключа `Name`. В следующем примере в хранилище ключей сертификату присвоено общее имя `IdentityServerSigning`, поэтому **субъект** — `CN=IdentityServerSigning`.

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. В Visual Studio создайте [профиль публикации](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) Службы приложений Azure для *серверного* проекта. В строке меню выберите **Сборка** > **Опубликовать** > **Создать** > **Azure** > **Служба приложений Azure** (Windows или Linux). Когда Visual Studio подключится к подписке Azure, вы сможете настроить **представление** ресурсов Azure по **типу ресурса**. В списке **Веб-приложение** найдите Службу приложений для приложения и выберите ее. Нажмите кнопку **Готово**.
1. Когда в Visual Studio снова отобразится окно **Публикация**, зависимости хранилища ключей и службы базы данных SQL Server будут обнаружены автоматически.

   Для службы хранилища ключей не требуется изменять настройки по умолчанию.

   В целях тестирования с приложением можно развернуть локальную базу данных [SQLite](https://www.sqlite.org/index.html), которая настраивается по умолчанию на основе шаблона Blazor. Дополнительной настройки она не требует. Настройка другой базы данных для Identity Server в рабочей среде выходит за рамки этой статьи. Дополнительные сведения см. в ресурсах, посвященных базам данных, в следующих наборах документации.
   * [Служба приложений](/azure/app-service/)
   * [Сервер Identity](https://identityserver4.readthedocs.io/en/latest/)

1. Щелкните ссылку **Изменить** под именем профиля развертывания в верхней части окна. Измените URL-адрес назначения на URL-адрес личного домена сайта (например, `https://www.contoso.com`). Сохраните параметры.
1. Опубликуйте приложение. В Visual Studio откроется окно браузера, и будет выполнен запрос к сайту в личном домене.

В документации по Azure приводятся дополнительные сведения об использовании служб Azure и личных доменов с привязкой TLS в Службе приложений, включая сведения об использовании записей CNAME вместо записей A. Дополнительные сведения см. в следующих ресурсах:

* [Документация по Службе приложений](/azure/app-service/)
* [Руководство по Сопоставление существующего настраиваемого DNS-имени со Службой приложений Azure](/azure/app-service/app-service-web-tutorial-custom-domain)
* [Защита пользовательского доменного имени с помощью привязки TLS/SSL в Службе приложений Azure](/azure/app-service/configure-ssl-bindings)
* [Хранилище ключей Azure](/azure/key-vault/)

Мы рекомендуем использовать новое окно браузера в режиме InPrivate или инкогнито для каждого тестового запуска после изменения приложения, его конфигурации или служб Azure на портале Azure. Оставшиеся после предыдущего тестового запуска файлы cookie могут привести к ошибкам проверки подлинности или авторизации при тестировании сайта, даже если его конфигурация верна. Дополнительные сведения о том, как настроить Visual Studio для открытия нового окна браузера в режиме InPrivate или инкогнито для каждого тестового запуска, см. в разделе [Файлы Cookie и данные сайта](#cookies-and-site-data).

При изменении конфигурации Службы приложений на портале Azure обновления обычно вступают в силу быстро, но не мгновенно. Иногда, чтобы изменение конфигурации вступило в силу, необходимо немного подождать, пока Служба приложений перезапустится.

При устранении проблемы с загрузкой сертификата выполните приведенную ниже команду в командной оболочке PowerShell [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) на портале Azure. Она выводит список сертификатов, к которым приложение может получить доступ в хранилище сертификатов `My` > `CurrentUser`. Выходные данные включают в себя субъекты и отпечатки сертификатов, которые могут быть полезны при отладке приложения.

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Развертывание в Службе приложений Azure](xref:security/authentication/identity/spa#deploy-to-production)
* [Импорт сертификата из Key Vault (документация Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:
  * Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.
  * Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.
