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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552228"
---
## <a name="share-interop-code-in-a-class-library"></a>Совместное использование кода взаимодействия в библиотеке классов

Код взаимодействия с JS можно добавить в библиотеку классов и затем совместно использовать в пакете NuGet.

Библиотека классов обрабатывает внедрение ресурсов JavaScript в готовой сборке. Файлы JavaScript помещаются в папку `wwwroot`. Ресурсы внедряются с помощью инструментов при создании библиотеки.

Сформированный пакет NuGet указывается в файле проекта приложения так же, как и любой пакет NuGet. После восстановления пакета код приложения может осуществлять вызовы в JavaScript так же, как в C#.

Дополнительные сведения см. в разделе <xref:blazor/components/class-libraries>.
