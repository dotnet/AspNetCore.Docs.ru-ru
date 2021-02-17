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
ms.openlocfilehash: 9c977e5407f9a3dc562ef0fb1127fefaa0dc5fc2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552697"
---
<a name="ddav"></a>
### <a name="disable-default-account-verification"></a><span data-ttu-id="29f13-101">Отключить проверку учетной записи по умолчанию</span><span class="sxs-lookup"><span data-stu-id="29f13-101">Disable default account verification</span></span>

<span data-ttu-id="29f13-102">При использовании шаблонов по умолчанию пользователь перенаправляется к, `Account.RegisterConfirmation` где можно выбрать ссылку для подтверждения учетной записи.</span><span class="sxs-lookup"><span data-stu-id="29f13-102">With the default templates, the user is redirected to the `Account.RegisterConfirmation` where they can select a link to have the account confirmed.</span></span> <span data-ttu-id="29f13-103">Значение по умолчанию `Account.RegisterConfirmation` используется ***только*** для тестирования, автоматическая проверка учетной записи должна быть отключена в рабочем приложении.</span><span class="sxs-lookup"><span data-stu-id="29f13-103">The default `Account.RegisterConfirmation` is used ***only*** for testing, automatic account verification should be disabled in a production app.</span></span>

<span data-ttu-id="29f13-104">Чтобы запросить подтвержденную учетную запись и предотвратить немедленный вход при регистрации, установите `DisplayConfirmAccountLink = false` в */Ареас/ Identity /Пажес/аккаунт/регистерконфирматион.кштмл.КС*:</span><span class="sxs-lookup"><span data-stu-id="29f13-104">To require a confirmed account and prevent immediate login at registration, set `DisplayConfirmAccountLink = false` in */Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs*:</span></span>

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]