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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552321"
---
ПО промежуточного слоя перенаправления заголовков должно выполняться до другого ПО промежуточного слоя. Такой порядок гарантирует, что ПО промежуточного слоя, полагающееся на сведения о перенаправленных заголовках, может использовать значения заголовков для обработки. Сведения о запуске ПО промежуточного слоя перенаправления заголовков после ПО промежуточного слоя диагностики и обработки ошибок см. в разделе [Порядок ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#fhmo).