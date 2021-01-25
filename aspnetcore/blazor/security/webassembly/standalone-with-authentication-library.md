---
title: Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности
author: guardrex
description: Узнайте, как защитить изолированное приложение ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности.
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 3da9ea045de996602ead052f6f13ffc999273a50
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252491"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности

Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)

*При использовании Azure Active Directory (AAD) и Azure Active Directory B2C (AAD B2C) не следуйте указаниям в этой статье. См. статьи, посвященные AAD и AAD B2C, в этом разделе оглавления.*

Чтобы создать [изолированное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), которое использует библиотеку [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication), выполните рекомендации с учетом используемых средств.

> [!NOTE]
> Поставщик Identity (IP) должен использовать [OpenID Connect (OIDC)](https://openid.net/connect/). Например, поставщик IP Facebook несовместим с OIDC, поэтому к нему не применяются рекомендации, приведенные в этом разделе. Для получения дополнительной информации см. <xref:blazor/security/webassembly/index#authentication-library>.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:

1. Выбрав шаблон **Приложение Blazor WebAssembly** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.

1. Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении**, чтобы хранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code или .NET Core CLI](#tab/visual-studio-code+netcore-cli)

Создайте новый проект Blazor WebAssembly с механизмом аутентификации в пустой папке. Задайте механизм аутентификации `Individual` с параметром `-au|--auth`, чтобы сохранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core:

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
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

---

## <a name="authentication-package"></a>Пакет проверки подлинности

Когда приложение создается для использования отдельных учетных записей пользователей, оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в файле проекта приложения. В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.

При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).

## <a name="authentication-service-support"></a>Поддержка службы проверки подлинности

Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication). Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

Конфигурация предоставлена в файле `wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Пример OIDC Google OAuth 2.0:

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

URI перенаправления (`https://localhost:5001/authentication/login-callback`) регистрируется в [консоли API Google](https://console.developers.google.com/apis/dashboard) в разделе **Учетные данные** >  **`{NAME}`**  > **Разрешенные URI перенаправления**, где `{NAME}` — это имя клиента приложения в списке **идентификаторов клиентов OAuth 2.0** приложения в консоли Google API.

Поддержка проверки подлинности для изолированных приложений обеспечивается посредством OpenID Connect (OIDC). Метод <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения с помощью OIDC. Значения, необходимые для настройки приложения, можно получить от поставщика удостоверений, совместимого с OIDC. Получите эти значения при регистрации приложения, которая обычно производится на веб-портале.

## <a name="access-token-scopes"></a>Области маркеров доступа

Шаблон Blazor WebAssembly автоматически настраивает области по умолчанию для `openid` и `profile`.

Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API. Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера по умолчанию <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:

* [Запрашивание дополнительных маркеров доступа](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Присоединение маркеров к исходящим запросам](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>Страница индексов

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>Компонент RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Компонент LoginDisplay

Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.

* Для пользователей, прошедших проверку подлинности:
  * отображает имя текущего пользователя;
  * отображает кнопку для выхода из приложения.
* Для анонимных пользователей предоставляет возможность входа в систему.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a>Компонент проверки подлинности

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/security/webassembly/additional-scenarios>
* [Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:
  * Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.
  * Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.
