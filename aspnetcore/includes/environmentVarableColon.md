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
<span data-ttu-id="05072-101">Разделитель `:` не работает с иерархическими ключами переменных среды на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="05072-101">The `:` separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="05072-102">`__` (двойной символ подчеркивания):</span><span class="sxs-lookup"><span data-stu-id="05072-102">`__`, the double underscore, is:</span></span>

* <span data-ttu-id="05072-103">Поддерживается всеми платформами.</span><span class="sxs-lookup"><span data-stu-id="05072-103">Supported by all platforms.</span></span> <span data-ttu-id="05072-104">Например, разделитель `:` не поддерживается [Bash](https://linuxhint.com/bash-environment-variables/), а `__` — поддерживается.</span><span class="sxs-lookup"><span data-stu-id="05072-104">For example, the `:` separator is not supported by [Bash](https://linuxhint.com/bash-environment-variables/), but `__` is.</span></span>
* <span data-ttu-id="05072-105">Автоматически заменяется на `:`</span><span class="sxs-lookup"><span data-stu-id="05072-105">Automatically replaced by a `:`</span></span>