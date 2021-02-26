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
<span data-ttu-id="1ebb5-101">При работе с API сервера, зарегистрированным в AAD, и регистрации приложения AAD в клиенте, который использует [неподтвержденный домен издателя](/azure/active-directory/develop/howto-configure-publisher-domain), URI идентификатора для приложения API сервера будет в формате `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`, а не `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span><span class="sxs-lookup"><span data-stu-id="1ebb5-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="1ebb5-102">В таком случае область маркера доступа по умолчанию в `Program.Main` (`Program.cs`) приложения *`Client`* будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="1ebb5-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="1ebb5-103">Чтобы настроить приложение API сервера для соответствующей аудитории, в файле параметров приложения API *`Server`* (`appsettings.json`) задайте для параметра `Audience` значение аудитории приложения, предоставленное на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="1ebb5-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

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

<span data-ttu-id="1ebb5-104">В предыдущем примере конфигурации в конце значения `Audience` **не** указана область по умолчанию (`/{DEFAULT SCOPE}`).</span><span class="sxs-lookup"><span data-stu-id="1ebb5-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="1ebb5-105">Пример.</span><span class="sxs-lookup"><span data-stu-id="1ebb5-105">Example:</span></span>

<span data-ttu-id="1ebb5-106">`Program.Main` (`Program.cs`) приложения *`Client`* :</span><span class="sxs-lookup"><span data-stu-id="1ebb5-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="1ebb5-107">Укажите в файле параметров приложения API *`Server`* (`appsettings.json`) соответствующее значение аудитории (`Audience`):</span><span class="sxs-lookup"><span data-stu-id="1ebb5-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

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

<span data-ttu-id="1ebb5-108">В предыдущем примере в конце значения `Audience` **не** указана область по умолчанию (`/API.Access`).</span><span class="sxs-lookup"><span data-stu-id="1ebb5-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
