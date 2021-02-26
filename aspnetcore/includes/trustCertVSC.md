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
* <span data-ttu-id="746ae-101">Настройте доверие сертификату разработки HTTPS с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="746ae-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="746ae-102">Предыдущая команда не работает в Linux.</span><span class="sxs-lookup"><span data-stu-id="746ae-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="746ae-103">Сведения о настройке доверия к сертификату см. в документации по вашему дистрибутиву Linux.</span><span class="sxs-lookup"><span data-stu-id="746ae-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="746ae-104">Приведенная выше команда отображает следующее диалоговое окно.</span><span class="sxs-lookup"><span data-stu-id="746ae-104">The preceding command displays the following dialog:</span></span>

  ![Диалоговое окно "Предупреждение о безопасности"](~/getting-started/_static/cert.png)

* <span data-ttu-id="746ae-106">Выберите **Да**, если согласны доверять сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="746ae-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="746ae-107">Дополнительные сведения см. в разделе [Настройка доверия к сертификату разработки HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="746ae-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]