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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552267"
---
```console
npm run release
```

Эта команда создает ресурсы на стороне клиента, которые будут обслуживаться при выполнении приложения. Эти ресурсы помещаются в папку *wwwroot*.

Веб-пакет выполнил следующие задачи:

* Очистка содержимого каталога *wwwroot*.
* Преобразование TypeScript в JavaScript (этот процесс называется *транспилированием*).
* Корректировка созданного кода JavaScript в целях уменьшения размера файла (этот процесс называется *минификацией*).
* Копирование обработанных файлов JavaScript, CSS и HTML из каталога *src* в *wwwroot*.
* Внедрение следующих элементов в файл *wwwroot/index.html*:
  * Тег `<link>`, ссылающийся на файл *wwwroot/main.\<hash\>.css*. Этот тег размещается непосредственно после закрывающего тега `</head>`.
  * Тег `<script>`, ссылающийся на сокращенный файл *wwwroot/main.\<hash\>.js*. Этот тег размещается непосредственно после закрывающего тега `</body>`.