---
title: Общие сведения о проверке подлинности для одностраничных приложений на ASP.NET Core
author: javiercn
description: Используйте Identity приложение с одним страничным приложением, размещенным в ASP.NET Core приложении.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658668"
---
# <a name="authentication-and-authorization-for-spas"></a>Проверка подлинности и авторизация для одностраничные приложения

Шаблоны ASP.NET Core 3,1 и более поздних версий обеспечивают проверку подлинности в одностраничных приложениях (одностраничные приложения) с помощью поддержки авторизации API. ASP.NET Core Identityдля проверки подлинности и сохранения пользователей в сочетании с [ Identity сервером](https://identityserver.io/) для реализации OpenID Connect Connect.

В шаблоны проектов " **угловой** " и " **отреагировать** " был добавлен параметр проверки подлинности, аналогичный параметру проверки подлинности в шаблонах проектов **веб-приложения (модель-представление-контроллер)** (MVC) и **веб-приложение** ( Razor страницы). Допустимые значения параметра: **None** и **индивидуальных**. Шаблон проекта **React.js и Redux** в настоящее время не поддерживает параметр проверки подлинности.

## <a name="create-an-app-with-api-authorization-support"></a>Создание приложения с поддержкой авторизации API

Проверку подлинности и авторизацию пользователя можно использовать как в радиальном, так и на одностраничные приложения. Откройте командную оболочку и выполните следующую команду:

**Угловой**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**Реагировать**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

Предыдущая команда создает приложение ASP.NET Core с каталогом *ClientApp* , СОДЕРЖАЩИМ значение SPA.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Общее описание компонентов ASP.NET Core приложения

В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.

### <a name="startup-class"></a>Класс Startup

В следующих примерах кода используются [Microsoft. AspNetCore. апиаусоризатион. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) Пакет NuGet сервера. В примерах настраивается проверка подлинности и авторизация API с помощью <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> методов расширения и. Проекты, использующие шаблоны проектов "реагирующий" или "угловой SPA" с проверкой подлинности, включают ссылку на этот пакет.

`Startup`Класс имеет следующие дополнения:

* Внутри `Startup.ConfigureServices` метода:
  * Identity с помощью пользовательского интерфейса по умолчанию:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityСервер с дополнительным `AddApiAuthorization` вспомогательным методом, который устанавливает некоторые соглашения ASP.NET Core по умолчанию поверх Identity сервера:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Аутентификация с помощью дополнительного вспомогательного метода `AddIdentityServerJwt`, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Внутри `Startup.Configure` метода:
  * По промежуточного слоя для проверки подлинности, которое отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:

    ```csharp
    app.UseAuthentication();
    ```

  * По Identity промежуточного слоя сервера, предоставляющего конечные точки подключения OpenID Connect:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a>Служба приложений Azure в Linux

Для развертываний службы приложений Azure в Linux укажите издателя явным образом в `Startup.ConfigureServices` :

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

В приведенном выше коде `{AUTHORITY}` местозаполнитель используется <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> при выполнении вызовов OpenID Connect Connect.

Пример.

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a>AddApiAuthorization

Этот вспомогательный метод настраивает Identity сервер для использования нашей поддерживаемой конфигурации. IdentityServer — это функциональная и расширяемая платформа для повышения уровня безопасности приложений. В то же время это делает ненужную сложность для наиболее распространенных сценариев. Следовательно, для вас предоставляется набор соглашений и параметров конфигурации, которые считаются хорошей отправной точкой. После изменения требований к проверке подлинности все возможности Identity сервера по-прежнему доступны для настройки проверки подлинности в соответствии с вашими потребностями.

### <a name="addno-locidentityserverjwt"></a>AddIdentityServerJwt

Этот вспомогательный метод настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию. Политика настроена таким путем, чтобы разрешить Identity обработку всех запросов, направляемых по любому вложенному пути в Identity пространстве URL-адресов "/ Identity ". `JwtBearerHandler` обрабатывает все остальные запросы. Кроме того, этот метод регистрирует `<<ApplicationName>>API` ресурс API с Identity сервером с областью по умолчанию `<<ApplicationName>>API` и настраивает по промежуточного слоя маркера носителя JWT для проверки маркеров, выданных Identity сервером для приложения.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

В файле *контроллерс\веасерфорекастконтроллер.КС* Обратите внимание на `[Authorize]` атрибут, примененный к классу, который указывает, что пользователь должен быть полномочным, исходя из политики по умолчанию для доступа к ресурсу. Политика авторизации по умолчанию должна быть настроена для использования схемы проверки подлинности по умолчанию, которая настраивается в описанной `AddIdentityServerJwt` выше схеме политики, что делает этот `JwtBearerHandler` вспомогательный метод обработчиком по умолчанию для запросов к приложению.

### <a name="applicationdbcontext"></a>ApplicationDbContext

В файле *дата\аппликатиондбконтекст.КС* Обратите внимание на то же, что `DbContext` используется в Identity с исключением, которое оно расширяет `ApiAuthorizationDbContext` (более производным классом от `IdentityDbContext` ), чтобы включить схему для Identity сервера.

Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных Identity `DbContext` классов и настройте контекст для включения Identity схемы путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` метода в методе.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

В файле *контроллерс\оидкконфигуратионконтроллер.КС* Обратите внимание на конечную точку, подготовленную для обслуживания параметров OIDC, которые необходимо использовать клиенту.

### appsettings.json

В *appsettings.json* файле корневого каталога проекта имеется новый `IdentityServer` раздел, описывающий список настроенных клиентов. В следующем примере есть один клиент. Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth. Профиль указывает на настраиваемый тип приложения. Он используется внутренне для обозначения соглашений, упрощающих процесс настройки сервера. Существует несколько доступных профилей, как описано в разделе [Профили приложений](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.jsна

В *appsettings.Development.js* в файле корневого каталога проекта имеется `IdentityServer` раздел, описывающий ключ, используемый для подписи маркеров. При развертывании в рабочей среде ключ должен быть подготовлен и развернут вместе с приложением, как описано в разделе [развертывание в производство](#deploy-to-production) .

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Общее описание углового приложения

Поддержка проверки подлинности и авторизации API в угловом шаблоне находится в своем собственном вспомогательном модуле в каталоге *клиентапп\срк\апи-аусоризатион* . Модуль состоит из следующих элементов:

* 3 компонента:
  * *Login. Component. TS*: обрабатывает поток входа в приложение.
  * *logout. Component. TS*: обрабатывает поток выхода из приложения.
  * *имя входа. Component. TS*: мини-приложение, которое отображает один из следующих наборов ссылок:
    * Управление профилями пользователей и ссылки для выхода, когда пользователь прошел проверку подлинности.
    * Регистрация и вход в систему, если пользователь не прошел проверку подлинности.
* Предохранитель маршрута `AuthorizeGuard` , который можно добавить к маршрутам и требует проверки подлинности пользователя перед посещением маршрута.
* Перехватчик HTTP `AuthorizeInterceptor` , который прикрепляет маркер доступа к исходящим HTTP-запросам, направленным на API, когда пользователь прошел проверку подлинности.
* Служба `AuthorizeService` , которая обрабатывает сведения о процессе проверки подлинности на более низком уровне и предоставляет сведения о пользователе, прошедшем проверку подлинности, в оставшейся части приложения для использования.
* Угловой модуль, который определяет маршруты, связанные с элементами проверки подлинности приложения. Он предоставляет компонент меню входа, перехватчик, условие и службу для использования в остальной части приложения.

## <a name="general-description-of-the-react-app"></a>Общее описание приложения для реагирования

Поддержка проверки подлинности и авторизации API в шаблоне отклика находится в каталоге *клиентапп\срк\компонентс\апи-аусоризатион* . Он состоит из следующих элементов:

* 4 компонента:
  * *Login.js*: обрабатывает поток входа в приложение.
  * *Logout.js*: обрабатывает поток выхода из приложения.
  * *LoginMenu.js*: мини-приложение, отображающее один из следующих наборов ссылок:
    * Управление профилями пользователей и ссылки для выхода, когда пользователь прошел проверку подлинности.
    * Регистрация и вход в систему, если пользователь не прошел проверку подлинности.
  * *AuthorizeRoute.js*: компонент маршрута, для которого требуется проверка подлинности пользователя перед отрисовкой компонента, указанного в `Component` параметре.
* Экспортированный `authService` экземпляр класса `AuthorizeService` , который обрабатывает сведения о процессе проверки подлинности на более низком уровне и предоставляет сведения о пользователе, прошедшем проверку подлинности, в оставшейся части приложения для использования.

Теперь, когда вы видели основные компоненты решения, вы можете более подробно изучить отдельные сценарии для приложения.

## <a name="require-authorization-on-a-new-api"></a>Требовать авторизацию для нового API

По умолчанию система настроена для простоты авторизации новых API-интерфейсов. Для этого создайте новый контроллер и добавьте `[Authorize]` атрибут в класс Controller или в любое действие в контроллере.

## <a name="customize-the-api-authentication-handler"></a>Настройка обработчика проверки подлинности API

Чтобы настроить конфигурацию обработчика JWT API, настройте его <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> экземпляр:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

Обработчик JWT API создает события, которые обеспечивают контроль над процессом проверки подлинности с помощью `JwtBearerEvents` . Чтобы обеспечить поддержку авторизации API, `AddIdentityServerJwt` регистрирует собственные обработчики событий.

Чтобы настроить обработку события, заключите существующий обработчик событий в требуемую дополнительную логику. Пример:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

В приведенном выше коде `OnTokenValidated` обработчик событий заменяется пользовательской реализацией. Эта реализация:

1. Вызывает исходную реализацию, предоставляемую службой поддержки авторизации API.
1. Запустите собственную пользовательскую логику.

## <a name="protect-a-client-side-route-angular"></a>Защита маршрута на стороне клиента (угловой)

Защита маршрута на стороне клиента выполняется путем добавления защиты авторизовать в список условий, выполняемых при настройке маршрута. В качестве примера можно увидеть, как `fetch-data` настраивается маршрут в главном модуле приложения.

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Важно упомянуть, что защита маршрута не защищает фактическую конечную точку (к которой по-прежнему требуется `[Authorize]` примененный атрибут), но она только запрещает пользователю переходить к конкретному маршруту на стороне клиента, если он не прошел проверку подлинности.

## <a name="authenticate-api-requests-angular"></a>Проверка подлинности запросов API (угловой)

Проверка подлинности запросов к API, размещенным вместе с приложением, выполняется автоматически с помощью перехватчика клиента HTTP, определенного приложением.

## <a name="protect-a-client-side-route-react"></a>Защита маршрута на стороне клиента (реакция)

Защитите клиентский маршрут, используя компонент, `AuthorizeRoute` а не обычный `Route` компонент. Например, обратите внимание, что `fetch-data` маршрут настроен в `App` компоненте:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Защита маршрута:

* Не защищает фактическую конечную точку (к которой по-прежнему требуется `[Authorize]` примененный атрибут).
* Не позволяет пользователю перейти к данному маршруту на стороне клиента, если он не прошел проверку подлинности.

## <a name="authenticate-api-requests-react"></a>Проверка подлинности запросов API (реакция)

Проверка подлинности запросов с реагированием выполняется путем первоначального импорта `authService` экземпляра из `AuthorizeService` . Маркер доступа извлекается из `authService` и присоединяется к запросу, как показано ниже. В окне «реагирующие компоненты» эта работа обычно выполняется в `componentDidMount` методе жизненного цикла или в результате какого-либо взаимодействия с пользователем.

### <a name="import-the-authservice-into-your-component"></a>Импорт Ауссервице в компонент

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Получение и присоединение маркера доступа к ответу

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Развертывание в рабочую среду

Чтобы развернуть приложение в рабочей среде, необходимо подготовить следующие ресурсы:

* База данных для хранения Identity учетных записей пользователей и Identity серверов.
* Рабочий сертификат, используемый для подписи маркеров.
  * Для этого сертификата нет особых требований; Это может быть самозаверяющий сертификат или сертификат, подготовленный через центр сертификации.
  * Его можно создать с помощью стандартных средств, таких как PowerShell или OpenSSL.
  * Его можно установить в хранилище сертификатов на целевых компьютерах или развернуть в виде *PFX* -файла с надежным паролем.

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a>Пример. Развертывание в поставщик услуг размещения веб-сайтов, не относящегося к Azure

На панели размещения веб-сайтов создайте или загрузите сертификат. Затем в *appsettings.json* файле приложения измените `IdentityServer` раздел, включив в него ключевые сведения. Пример:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

В предыдущем примере:

* `StoreName` представляет имя хранилища сертификатов, в котором хранится сертификат. В этом случае он указывает на хранилище веб-хостинга.
* `StoreLocation` представляет место загрузки сертификата ( `CurrentUser` в данном случае).
* `Name` соответствует отличительной теме сертификата.

### <a name="example-deploy-to-azure-app-service"></a>Пример. Развертывание в службе приложений Azure

В этом разделе описывается развертывание приложения в службе приложений Azure с помощью сертификата, хранящегося в хранилище сертификатов. Чтобы изменить приложение для загрузки сертификата из хранилища сертификатов, при настройке приложения в портал Azure на более позднем этапе требуется план обслуживания уровня "Стандартный" или более подходящий.

В *appsettings.json* файле приложения измените `IdentityServer` раздел, включив в него ключевые сведения:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* Имя хранилища представляет имя хранилища сертификатов, в котором хранится сертификат. В этом случае он указывает на личное хранилище пользователей.
* Расположение хранилища представляет место загрузки сертификата из ( `CurrentUser` или `LocalMachine` ).
* Свойство Name в сертификате соответствует отличительной теме сертификата.

Чтобы выполнить развертывание в службе приложений Azure, выполните действия, описанные в разделе [развертывание приложения в Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), в котором объясняется, как создать необходимые ресурсы Azure и развернуть приложение в рабочей среде.

После выполнения указанных выше инструкций приложение развертывается в Azure, но еще не работает. Сертификат, используемый приложением, должен быть настроен в портал Azure. Перейдите к отпечатку сертификата и выполните действия, описанные в разделе [Загрузка сертификатов](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Хотя эти действия упоминают SSL, в портал Azure, где можно передать подготовленный сертификат для использования с приложением, имеется раздел **частные сертификаты** .

После настройки приложения и параметров приложения в портал Azure перезапустите приложение на портале.

## <a name="other-configuration-options"></a>Другие параметры конфигурации

Поддержка авторизации API основана на Identity сервере с набором соглашений, значениями по умолчанию и усовершенствованиями для упрощения работы одностраничные приложения. Нет нужды говорить, что все возможности сервера доступны в фоновом режиме, Identity если ASP.NET Core интеграции не охватывают ваш сценарий. Поддержка ASP.NET Coreов сосредоточена на «первых» приложениях, где все приложения создаются и развертываются в нашей Организации. Таким образом, поддержка не предоставляется для таких вещей, как согласие или Федерация. Для этих сценариев используйте Identity сервер и следуйте его документации.

### <a name="application-profiles"></a>Профили приложений

Профили приложений — это предопределенные конфигурации для приложений, которые дополнительно определяют свои параметры. В настоящее время поддерживаются следующие профили:

* `IdentityServerSPA`— Представляет защищенный, размещенный вместе с Identity сервером объект как единое целое.
  * Значение `redirect_uri` по умолчанию — `/authentication/login-callback` .
  * Значение `post_logout_redirect_uri` по умолчанию — `/authentication/logout-callback` .
  * Набор областей включает `openid` , `profile` и все области, определенные для API-интерфейсов в приложении.
  * Набор разрешенных типов ответов OIDC — `id_token token` или каждый из них по отдельности ( `id_token` , `token` ).
  * Допустимый режим ответа — `fragment` .
* `SPA`— Это SPA, которая не размещена на Identity сервере.
  * Набор областей включает `openid` , `profile` и все области, определенные для API-интерфейсов в приложении.
  * Набор разрешенных типов ответов OIDC — `id_token token` или каждый из них по отдельности ( `id_token` , `token` ).
  * Допустимый режим ответа — `fragment` .
* `IdentityServerJwt`— Представляет API, размещенный вместе с Identity сервером.
  * В приложении настроена одна область, которая по умолчанию имеет имя приложения.
* `API`— Представляет API, который не размещен на Identity сервере.
  * В приложении настроена одна область, которая по умолчанию имеет имя приложения.

### <a name="configuration-through-appsettings"></a>Настройка с помощью AppSettings

Настройте приложения в системе конфигурации, добавив их в список `Clients` или `Resources` .

Настройте каждое клиентское `redirect_uri` `post_logout_redirect_uri` свойство и, как показано в следующем примере:

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

При настройке ресурсов можно настроить области для ресурса, как показано ниже.

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Настройка с помощью кода

Можно также настроить клиенты и ресурсы с помощью кода, используя перегрузку `AddApiAuthorization` , которая принимает действие для настройки параметров.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
