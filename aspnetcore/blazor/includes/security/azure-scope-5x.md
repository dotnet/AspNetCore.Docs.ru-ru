---
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
ms.openlocfilehash: 5964554c36e2242b70faee390374828acd2bd860
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552864"
---
При работе с API сервера, зарегистрированным в AAD, и регистрации приложения AAD в клиенте, который использует [неподтвержденный домен издателя](/azure/active-directory/develop/howto-configure-publisher-domain), URI идентификатора для приложения API сервера будет в формате `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`, а не `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`. В таком случае область маркера доступа по умолчанию в `Program.Main` (`Program.cs`) приложения *`Client`* будет выглядеть следующим образом:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

Чтобы настроить приложение API сервера для соответствующей аудитории, в файле параметров приложения API *`Server`* (`appsettings.json`) задайте для параметра `Audience` значение аудитории приложения, предоставленное на портале Azure.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

В предыдущем примере конфигурации в конце значения `Audience` **не** указана область по умолчанию (`/{DEFAULT SCOPE}`).

Пример.

`Program.Main` (`Program.cs`) приложения *`Client`* :

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

Укажите в файле параметров приложения API *`Server`* (`appsettings.json`) соответствующее значение аудитории (`Audience`):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

В предыдущем примере в конце значения `Audience` **не** указана область по умолчанию (`/API.Access`).
