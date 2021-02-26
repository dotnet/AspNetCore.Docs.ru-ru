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
ms.openlocfilehash: 41d4fd2e746e08d32d9f666faab55acc56817be2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551431"
---
<!-- Options common to Razor Pages and Controller -->
| Параметр               | Описание|
| ----------------- | ------------ |
| --model или -m  | Класс модели, который необходимо использовать. |
| --dataContext или -dc  | Класс `DbContext` для использования. |
| --bootstrapVersion или -b  | Определяет версию начальной загрузки. Допустимые значения: `3` или `4`. Значение по умолчанию — `4`. Если требуемая версия отсутствует, создается каталог *wwwroot* с файлами начальной загрузки этой версии. |
| --referenceScriptLibraries или -scripts |  Указание библиотек скриптов в создаваемых представлениях. Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания. |
| --layout или -l | Страница пользовательского макета, которую необходимо использовать. |
| --useDefaultLayout или -udl | Использовать макет по умолчанию для представлений. |
| --force или -f | Перезаписать существующие файлы. |
| --relativeFolderPath или -outDir | Относительный путь к папке выходных данных из проекта, в котором создаются файлы. Если этот параметр не задан, файлы создаются в папке проекта. |