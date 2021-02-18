---
title: Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory
author: guardrex
description: Узнайте, как защитить изолированное приложение ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 21d6e6fd9859cf4daf28e0bc3cf70562192844dd
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280929"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a>Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory

В этой статье описывается, как создать [изолированное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), использующее [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Для приложений Blazor WebAssembly, созданных в Visual Studio и настроенных для поддержки учетных записей в каталоге AAD организации, сейчас Visual Studio неправильно настраивает регистрации приложений на портале Azure при создании проекта. Эта проблема будет устранена в следующем выпуске Visual Studio.
>
> В этой статье показано, как создать решение и регистрацию приложения на портале Azure с помощью команды `dotnet new` .NET CLI, а также как создать такую регистрацию на портале Azure вручную.
>
> Если вы предпочитаете создать решение и регистрации приложения Azure в среде Visual Studio до ее обновления, **_ознакомьтесь с каждым разделом этой статьи_** и подтвердите или обновите конфигурацию и регистрацию приложения после того, как Visual Studio создаст решение.

Зарегистрируйте приложение AAD на портале Azure в разделе **Azure Active Directory** > **Регистрация приложений**.

1. Укажите **имя** приложения (например, **BlazorИзолированный AAD**).
1. Выберите **поддерживаемые типы учетных записей**. Можно выбрать **Учетные записи только в этом каталоге организации**.
1. Выберите в раскрывающемся списке **URI перенаправления** значение **Одностраничное приложение (SPA)** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`. Порт по умолчанию для приложения, работающего на Kestrel, — 5001. Если приложение выполняется на другом порте Kestrel, используйте порт приложения. Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка**. Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления. В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.
1. Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.
1. Выберите **Зарегистрировать**.

Запишите следующие сведения:

* Идентификатор приложения (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).
* Идентификатор каталога (клиента) (например, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`).

В разделе **Аутентификация** > **Конфигурации платформ** > **Одностраничное приложение (SPA)** сделайте следующее:

1. Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.
1. В разделе **Неявное предоставление разрешения** убедитесь, что флажки **Маркеры доступа** и **Токены идентификаторов** **не** установлены.
1. Для остальных параметров можно оставить значения по умолчанию.
1. Нажмите кнопку **Сохранить**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Зарегистрируйте приложение AAD на портале Azure в разделе **Azure Active Directory** > **Регистрация приложений**.

1. Укажите **имя** приложения (например, **BlazorИзолированный AAD**).
1. Выберите **поддерживаемые типы учетных записей**. Можно выбрать **Учетные записи только в этом каталоге организации**.
1. В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`. Порт по умолчанию для приложения, работающего на Kestrel, — 5001. Если приложение выполняется на другом порте Kestrel, используйте порт приложения. Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка**. Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления. В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.
1. Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.
1. Выберите **Зарегистрировать**.

Запишите следующие сведения:

* Идентификатор приложения (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).
* Идентификатор каталога (клиента) (например, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`).

В разделе **Аутентификация** > **Конфигурации платформ** > **Интернет** сделайте следующее:

1. Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.
1. В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.
1. Для остальных параметров можно оставить значения по умолчанию.
1. Нажмите кнопку **Сохранить**.

::: moniker-end

Создайте приложение в пустой папке. Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Заполнитель   | Название на портале Azure       | Пример                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Идентификатор приложения (клиента) | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | Идентификатор каталога (клиента)   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.

> [!NOTE]
> На портале Azure **URI перенаправления** в конфигурации платформы приложений настроен для использования порта 5001 для приложений, которые выполняются на сервере Kestrel с параметрами по умолчанию.
>
> Если приложение выполняется на случайном порту IIS Express, порт для приложения указан в свойствах приложения на панели **Отладка**.
>
> Если порт не был настроен ранее с помощью известного порта приложения, вернитесь к регистрации приложения на портале Azure и обновите URI перенаправления, указав правильный порт.

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-AAD.md)]

::: moniker-end

После создания приложения вы сможете:

* Входить в приложение, используя учетную запись пользователя AAD.
* Запрашивать маркеры доступа для API Майкрософт. Дополнительные сведения можно найти в разделе
  * [Области маркеров доступа](#access-token-scopes)
  * [Краткое руководство. Настройка приложения для предоставления интерфейсов веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Пакет проверки подлинности

Когда приложение создается для использования рабочих или учебных учетных записей (`SingleOrg`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)). В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.

При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в приложение.

## <a name="authentication-service-support"></a>Поддержка службы проверки подлинности

Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal). Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения. Значения, необходимые для настройки приложения, можно получить из конфигурации AAD при регистрации приложения.

Конфигурация предоставлена в файле `wwwroot/appsettings.json`:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Пример.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a>Области маркеров доступа

Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API. Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера доступа по умолчанию <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

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

## <a name="login-mode"></a>Режим входа

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>Страница индексов

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>Компонент RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Компонент LoginDisplay

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a>Компонент проверки подлинности

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/security/webassembly/additional-scenarios>
* [Создание пользовательской версии библиотеки Authentication.MSAL для JavaScript](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Документация по платформе удостоверений Майкрософт](/azure/active-directory/develop/)
