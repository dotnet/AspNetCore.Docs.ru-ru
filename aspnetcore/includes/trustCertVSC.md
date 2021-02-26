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
ms.openlocfilehash: df4a9a7db8097ea765b2d0f404305b771f994ddf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552722"
---
* Настройте доверие сертификату разработки HTTPS с помощью следующей команды:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  Предыдущая команда не работает в Linux. Сведения о настройке доверия к сертификату см. в документации по вашему дистрибутиву Linux.

  Приведенная выше команда отображает следующее диалоговое окно.

  ![Диалоговое окно "Предупреждение о безопасности"](~/getting-started/_static/cert.png)

* Выберите **Да**, если согласны доверять сертификату разработки.

  Дополнительные сведения см. в разделе [Настройка доверия к сертификату разработки HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]