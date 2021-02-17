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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551297"
---
* `-F|--no-formatting`

  Флаг, при установке которого форматирование HTTP-ответа не применяется.

* `-h|--header`

  Задает заголовок HTTP-запроса. Поддерживаются следующие два формата значений:

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  Указывает файл, в который должен быть записан текст HTTP-ответа. Например, `--response:body "C:\response.json"`. Если файл не существует, он создается.

* `--response:headers`

  Указывает файл, в который должны быть записаны заголовки HTTP-ответа. Например, `--response:headers "C:\response.txt"`. Если файл не существует, он создается.

* `-s|--streaming`

  Флаг, при установке которого включается потоковая передача HTTP-ответа.
