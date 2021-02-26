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

<span data-ttu-id="b1959-101">Эта команда создает ресурсы на стороне клиента, которые будут обслуживаться при выполнении приложения.</span><span class="sxs-lookup"><span data-stu-id="b1959-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="b1959-102">Эти ресурсы помещаются в папку *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b1959-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="b1959-103">Веб-пакет выполнил следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="b1959-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="b1959-104">Очистка содержимого каталога *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b1959-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="b1959-105">Преобразование TypeScript в JavaScript (этот процесс называется *транспилированием*).</span><span class="sxs-lookup"><span data-stu-id="b1959-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="b1959-106">Корректировка созданного кода JavaScript в целях уменьшения размера файла (этот процесс называется *минификацией*).</span><span class="sxs-lookup"><span data-stu-id="b1959-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="b1959-107">Копирование обработанных файлов JavaScript, CSS и HTML из каталога *src* в *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b1959-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="b1959-108">Внедрение следующих элементов в файл *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="b1959-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="b1959-109">Тег `<link>`, ссылающийся на файл *wwwroot/main.\<hash\>.css*.</span><span class="sxs-lookup"><span data-stu-id="b1959-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="b1959-110">Этот тег размещается непосредственно после закрывающего тега `</head>`.</span><span class="sxs-lookup"><span data-stu-id="b1959-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="b1959-111">Тег `<script>`, ссылающийся на сокращенный файл *wwwroot/main.\<hash\>.js*.</span><span class="sxs-lookup"><span data-stu-id="b1959-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="b1959-112">Этот тег размещается непосредственно после закрывающего тега `</body>`.</span><span class="sxs-lookup"><span data-stu-id="b1959-112">This tag is placed immediately before the closing `</body>` tag.</span></span>