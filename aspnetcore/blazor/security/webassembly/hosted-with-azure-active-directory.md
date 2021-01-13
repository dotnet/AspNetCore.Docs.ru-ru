---
title: Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory
author: guardrex
description: Узнайте о том, как защитить размещенное приложение ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e65be6e2ddc1a9de6f0ba20fe50f63b650e0bff5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97792039"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory

Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)

В этой статье описывается, как создать [размещенное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), использующее [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Для приложений Blazor WebAssembly, созданных в Visual Studio, которые настроены для поддержки учетных записей в каталоге AAD организации, сейчас Visual Studio неправильно настраивает проекты решений или регистрации приложений на портале Azure при создании проекта. Эта проблема будет устранена в следующем выпуске Visual Studio.
>
> В этой статье показано, как создать решение и регистрации приложений на портале Azure с помощью команды `dotnet new` .NET CLI, а также так создать такие регистрации на портале Azure вручную.
>
> Если вы предпочитаете создать решение и регистрации приложений Azure в среде Visual Studio до ее обновления, **_ознакомьтесь с каждым разделом этой статьи_** и подтвердите или обновите конфигурацию или регистрацию приложений после того, как Visual Studio создаст решение.

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a>Регистрация приложений в AAD и создание решения

### <a name="create-a-tenant"></a>Создание клиента

Следуйте инструкциям в разделе [Краткое руководство. Настройка клиента](/azure/active-directory/develop/quickstart-create-new-tenant), чтобы создать клиент в AAD.

### <a name="register-a-server-api-app"></a>Регистрация приложения API сервера

Зарегистрируйте приложение AAD для *серверного приложения API*:

1. В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.
1. Укажите **имя** приложения (например, **Blazor Server AAD**).
1. Выберите **поддерживаемые типы учетных записей**. Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).
1. В этом сценарии *приложению API сервера* не требуется **URI перенаправления**, поэтому в раскрывающемся списке оставьте значение **Интернет** и не вводите URI-перенаправления.
1. Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.
1. Выберите **Зарегистрировать**.

Запишите следующие сведения:

* Идентификатор *приложения API сервера* (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).
* Идентификатор каталога (клиента) (например, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`).
* Основной домен AAD/домен издателя/домен клиента (например, `contoso.onmicrosoft.com`). домен доступен в качестве **домена издателя** в колонке **Фирменная символика** на портале Azure для зарегистрированного приложения.

В разделе **Разрешения API** удалите разрешение **Microsoft Graph** > **User.Read**, поскольку приложению не требуется вход или доступ к профилю пользователя.

В разделе **Предоставление API**:

1. Нажмите **Добавить группу**.
1. Выберите **Сохранить и продолжить**.
1. Укажите значение в поле **Имя области** (например, `API.Access`).
1. Укажите значение в поле **Отображаемое имя согласия администратора** (например, `Access API`).
1. Укажите значение в поле **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.`).
1. Убедитесь в том, что параметру **Состояние** присвоено значение **Включено**.
1. Выберите **Добавить область**.

Запишите следующие сведения:

* URI идентификатора приложения (например, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` или предоставленное вами значение);
* Имя области (например, `API.Access`)

### <a name="register-a-client-app"></a>зарегистрируете клиентское приложение;

Зарегистрируйте приложение AAD для *клиентского приложения*:

::: moniker range=">= aspnetcore-5.0"

1. В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.
1. Укажите **имя** приложения (например, **BlazorКлиентский AAD**).
1. Выберите **поддерживаемые типы учетных записей**. Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).
1. Выберите в раскрывающемся списке **URI перенаправления** значение **Одностраничное приложение (SPA)** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`. Порт по умолчанию для приложения, работающего на Kestrel, — 5001. Если приложение выполняется на другом порте Kestrel, используйте порт приложения. Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения *`Server`* на панели **Отладка**. Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления. В разделе [Создание приложения](#create-the-app) появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.
1. Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.
1. Выберите **Зарегистрировать**.

Запишите идентификатор приложения *`Client`* (клиента) (например, `4369008b-21fa-427c-abaa-9b53bf58e538`).

В разделе **Аутентификация** > **Конфигурации платформ** > **Одностраничное приложение (SPA)** сделайте следующее:

1. Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.
1. В разделе **Неявное предоставление разрешения** убедитесь, что флажки **Маркеры доступа** и **Токены идентификаторов** **не** установлены.
1. Для остальных параметров можно оставить значения по умолчанию.
1. Нажмите кнопку **Сохранить**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.
1. Укажите **имя** приложения (например, **BlazorКлиентский AAD**).
1. Выберите **поддерживаемые типы учетных записей**. Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).
1. В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`. Порт по умолчанию для приложения, работающего на Kestrel, — 5001. Если приложение выполняется на другом порте Kestrel, используйте порт приложения. Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения *`Server`* на панели **Отладка**. Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления. В разделе [Создание приложения](#create-the-app) появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.
1. Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.
1. Выберите **Зарегистрировать**.

Запишите идентификатор приложения *`Client`* (клиента) (например, `4369008b-21fa-427c-abaa-9b53bf58e538`).

В разделе **Аутентификация** > **Конфигурации платформ** > **Интернет** сделайте следующее:

1. Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.
1. В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.
1. Для остальных параметров можно оставить значения по умолчанию.
1. Нажмите кнопку **Сохранить**.

::: moniker-end

В разделе **Разрешения API** выполните следующие действия:

1. Убедитесь, что у приложения имеется разрешение **Microsoft Graph** > **User.Read**.
1. Выберите **Добавить разрешение**, а затем — **Мои API**.
1. В столбце *Имя* выберите **Приложение API сервера** (например, **Blazor Server AAD**).
1. Откройте список **API**.
1. Разрешите доступ к API (например, `API.Access`).
1. Выберите **Добавить разрешения**.
1. Нажмите кнопку **Предоставить согласие администратора для {имя клиента}** . Выберите **Да** для подтверждения.

### <a name="create-the-app"></a>Создание приложения

В пустой папке замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Заполнитель                  | Название на портале Azure                                     | Пример                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | Идентификатор приложения (клиента) для приложения *`Client`*        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | Имя области                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | Идентификатор приложения (клиента) для *приложения API сервера*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | универсальный код ресурса (URI) идентификатора приложения.&dagger;                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | Основной домен/домен издателя/домен клиента                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | Идентификатор каталога (клиента)                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

&dagger;Шаблон Blazor WebAssembly автоматически добавляет схему `api://` к аргументу URI идентификатора приложения, переданному в команде `dotnet new`. Если при предоставлении URI идентификатора приложения для заполнителя `{SERVER API APP ID URI}` используется схема `api://`, удалите эту схему (`api://`) из аргумента, как показано в примере значения в предыдущей таблице. Если URI идентификатора приложения является пользовательским значением или имеет какую-то другую схему (например, `https://` для домена недоверенного издателя, подобного `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), необходимо вручную обновить URI области по умолчанию и удалить схему `api://` после того, как приложение *`Client`* будет создано на основе шаблона. Дополнительные сведения см. в примечании в разделе [Области маркеров доступа](#access-token-scopes). Возможно, шаблон Blazor WebAssembly будет изменен в следующем выпуске ASP.NET Core с учетом таких сценариев. Дополнительные сведения см. в статье [Двойная схема URI идентификатора приложения с шаблоном WASM Blazor (размещенное решение для одной организации) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Может потребоваться изменить конфигурацию при использовании клиента Azure с непроверенным доменом издателя, как описано в разделе [Параметры приложения](#app-settings).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Может потребоваться изменить конфигурацию при использовании клиента Azure с непроверенным доменом издателя, как описано в разделе [Области маркеров доступа](#access-token-scopes).

::: moniker-end

> [!NOTE]
> На портале Azure **URI перенаправления** в конфигурации платформы приложения *`Client`* настроен для использования порта 5001, предназначенного для приложений, которые выполняются на сервере Kestrel с параметрами по умолчанию.
>
> Если приложение *`Client`* выполняется на случайном порту IIS Express, порт для приложения можно просмотреть в свойствах *приложения API Server* в области **Отладка**.
>
> Если порт не был настроен ранее с использованием известного порта приложения *`Client`* , вернитесь к регистрации приложения *`Client`* на портале Azure и обновите URI перенаправления, указав правильный порт.

## <a name="server-app-configuration"></a>Конфигурация приложения *`Server`*

*Этот раздел относится к приложению **`Server`** решения.*

### <a name="authentication-package"></a>Пакет проверки подлинности

::: moniker range=">= aspnetcore-5.0"

Поддержка аутентификации и авторизации для вызовов веб-API ASP.NET Core на платформе Identity Майкрософт предоставляется следующими пакетами:

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте NuGet.org.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Поддержка проверки подлинности и авторизации вызовов веб-API ASP.NET Core предоставляется пакетом [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

::: moniker-end

### <a name="authentication-service-support"></a>Поддержка службы проверки подлинности

::: moniker range=">= aspnetcore-5.0"

Метод `AddAuthentication` настраивает службы проверки подлинности в приложении и обработчик носителя JWT в качестве метода проверки подлинности по умолчанию. Метод <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> настраивает службы для защиты веб-API с помощью платформы Identity Майкрософт версии 2.0. Этот метод ожидает получить раздел `AzureAd` в конфигурации приложения, где настроены параметры для инициализации методов аутентификации.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Метод `AddAuthentication` настраивает службы проверки подлинности в приложении и обработчик носителя JWT в качестве метода проверки подлинности по умолчанию. Метод <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, создаваемых Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> и <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> позволяют обеспечить, что:

* Приложение пытается проанализировать и проверить маркеры в входящих запросах.
* Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>User.Identity.Name

По умолчанию API приложения *`Server`* заполняет `User.Identity.Name` значением из типа утверждения `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (например, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).

Чтобы настроить приложение на получение значения из типа утверждения `name`:

* Добавьте пространство имен для <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> в `Startup.cs`:

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* Настройте <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices`:

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Настройте <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices`:

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a>Параметры приложения

::: moniker range=">= aspnetcore-5.0"

В файле `appsettings.json` содержатся параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

Пример.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/blazor/includes/security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

В файле `appsettings.json` содержатся параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Пример.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a>Контроллер WeatherForecast

Контроллер WeatherForecast (*Controllers/WeatherForecastController.cs*) предоставляет защищенный API с атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), применяемым к контроллеру. **Важно** понять следующее:

* Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.
* Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), используемый в приложении Blazor WebAssembly, служит подсказкой для приложения о том, что пользователь должен пройти авторизацию, чтобы приложение работало правильно.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Конфигурация приложения *`Client`*

*Этот раздел относится к приложению **`Client`** решения.*

### <a name="authentication-package"></a>Пакет проверки подлинности

Когда приложение создается для использования рабочих или учебных учетных записей (`SingleOrg`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)). В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.

При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в приложение.

### <a name="authentication-service-support"></a>Поддержка службы проверки подлинности

Добавлена поддержка экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при выполнении запросов к серверному проекту.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.ServerAPI`).

Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal). Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения. Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.

Конфигурация предоставлена в файле `wwwroot/appsettings.json`:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Пример.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Области маркеров доступа

Области маркеров доступа по умолчанию представляют собой список областей маркеров доступа, которые:

* включены по умолчанию в запросе на вход;
* используются для предоставления маркера доступа сразу после проверки подлинности.

Все области должны входить в одно и то же приложение для каждого правила Azure Active Directory. При необходимости можно добавить дополнительные области для дополнительных приложений API:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Шаблон Blazor WebAssembly автоматически добавляет схему `api://` к аргументу URI идентификатора приложения, переданному в команде `dotnet new`. При создании приложения на основе шаблона проекта Blazor убедитесь, что для области маркера доступа по умолчанию указано правильное значение URI идентификатора пользовательского приложения, указанное на портале Azure, или значение в **одном** из следующих форматов:
>
> * Если домен издателя каталога является **доверенным**, то для области маркера доступа по умолчанию, как правило, задано значение, аналогичное приведенному в следующем примере, где `API.Access` — это имя области по умолчанию:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Проверьте значение двойной схемы (`api://api://...`). При наличии двойной схемы удалите первую схему `api://` из значения.
>
> * Если домен издателя каталога является **недоверенным**, то для области маркера доступа по умолчанию, как правило, задано значение, аналогичное приведенному в следующем примере, где `API.Access` — это имя области по умолчанию:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Проверьте значение на наличие дополнительной схемы `api://` (`api://https://contoso.onmicrosoft.com/...`). При наличии дополнительной схемы `api://` удалите схему `api://` из значения.
>
> Возможно, шаблон Blazor WebAssembly будет изменен в следующем выпуске ASP.NET Core с учетом таких сценариев. Дополнительные сведения см. в статье [Двойная схема URI идентификатора приложения с шаблоном WASM Blazor (размещенное решение для одной организации) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

Укажите дополнительные области с помощью `AdditionalScopesToConsent`:

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:

* [Запрашивание дополнительных маркеров доступа](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Присоединение маркеров к исходящим запросам](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>Режим входа

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a>Страница индексов

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a>Компонент RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Компонент LoginDisplay

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a>Компонент проверки подлинности

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a>Компонент FetchData

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a>Запуск приложения

Запустите приложение из серверного проекта. При использовании Visual Studio выполните одно из следующих действий.

* На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.
* В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/security/webassembly/additional-scenarios>
* [Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Документация по платформе удостоверений Майкрософт](/azure/active-directory/develop/)
* [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app)
