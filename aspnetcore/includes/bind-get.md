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
ms.openlocfilehash: c6880852f63b1e91789667506f01680608933226
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552714"
---
> [!WARNING]
> В целях безопасности вам следует задать привязку данных запроса `GET` к свойствам страничной модели. Проверьте введенные данные пользователя, прежде чем сопоставлять их со свойствами. Привязка `GET` полезна при обращении к сценариям, использующим строку запроса или значения маршрутов.
>
> Чтобы привязать свойство к запросам `GET`, задайте для свойства `SupportsGet` атрибута [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) значение `true`:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Дополнительные сведения см. в видео, посвященном [обсуждению привязки к запросу GET](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s), на канале YouTube.
