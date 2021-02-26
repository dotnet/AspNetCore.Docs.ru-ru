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
ms.openlocfilehash: dda8606501f315b8882909169b42598791ef6fef
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552800"
---
В Windows самозаверяющие сертификаты создаются с помощью [командлета PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Примеры, которые не поддерживаются, см. в разделе [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

В macOS, Linux и Windows сертификаты создаются с помощью [OpenSSL](https://www.openssl.org/).
