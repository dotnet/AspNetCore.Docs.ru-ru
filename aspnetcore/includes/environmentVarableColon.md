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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536312"
---
Разделитель `:` не работает с иерархическими ключами переменных среды на всех платформах. `__` (двойной символ подчеркивания):

* Поддерживается всеми платформами. Например, разделитель `:` не поддерживается [Bash](https://linuxhint.com/bash-environment-variables/), а `__` — поддерживается.
* Автоматически заменяется на `:`