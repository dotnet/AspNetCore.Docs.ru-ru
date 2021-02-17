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
### <a name="disable-default-account-verification"></a>Отключить проверку учетной записи по умолчанию

При использовании шаблонов по умолчанию пользователь перенаправляется к, `Account.RegisterConfirmation` где можно выбрать ссылку для подтверждения учетной записи. Значение по умолчанию `Account.RegisterConfirmation` используется ***только*** для тестирования, автоматическая проверка учетной записи должна быть отключена в рабочем приложении.

Чтобы запросить подтвержденную учетную запись и предотвратить немедленный вход при регистрации, установите `DisplayConfirmAccountLink = false` в */Ареас/ Identity /Пажес/аккаунт/регистерконфирматион.кштмл.КС*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]