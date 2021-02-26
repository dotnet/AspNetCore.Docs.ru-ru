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
| <span data-ttu-id="617da-101">Параметр</span><span class="sxs-lookup"><span data-stu-id="617da-101">Option</span></span>               | <span data-ttu-id="617da-102">Описание</span><span class="sxs-lookup"><span data-stu-id="617da-102">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="617da-103">--model или -m</span><span class="sxs-lookup"><span data-stu-id="617da-103">--model or -m</span></span>  | <span data-ttu-id="617da-104">Класс модели, который необходимо использовать.</span><span class="sxs-lookup"><span data-stu-id="617da-104">Model class to use.</span></span> |
| <span data-ttu-id="617da-105">--dataContext или -dc</span><span class="sxs-lookup"><span data-stu-id="617da-105">--dataContext or -dc</span></span>  | <span data-ttu-id="617da-106">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="617da-106">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="617da-107">--bootstrapVersion или -b</span><span class="sxs-lookup"><span data-stu-id="617da-107">--bootstrapVersion or -b</span></span>  | <span data-ttu-id="617da-108">Определяет версию начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="617da-108">Specifies the bootstrap version.</span></span> <span data-ttu-id="617da-109">Допустимые значения: `3` или `4`.</span><span class="sxs-lookup"><span data-stu-id="617da-109">Valid values are `3` or `4`.</span></span> <span data-ttu-id="617da-110">Значение по умолчанию — `4`.</span><span class="sxs-lookup"><span data-stu-id="617da-110">Default is `4`.</span></span> <span data-ttu-id="617da-111">Если требуемая версия отсутствует, создается каталог *wwwroot* с файлами начальной загрузки этой версии.</span><span class="sxs-lookup"><span data-stu-id="617da-111">If needed and not present, a *wwwroot* directory is created that includes the bootstrap files of the specified version.</span></span> |
| <span data-ttu-id="617da-112">--referenceScriptLibraries или -scripts</span><span class="sxs-lookup"><span data-stu-id="617da-112">--referenceScriptLibraries or -scripts</span></span> |  <span data-ttu-id="617da-113">Указание библиотек скриптов в создаваемых представлениях.</span><span class="sxs-lookup"><span data-stu-id="617da-113">Reference script libraries in the generated views.</span></span> <span data-ttu-id="617da-114">Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания.</span><span class="sxs-lookup"><span data-stu-id="617da-114">Adds `_ValidationScriptsPartial` to Edit and Create pages.</span></span> |
| <span data-ttu-id="617da-115">--layout или -l</span><span class="sxs-lookup"><span data-stu-id="617da-115">--layout or -l</span></span> | <span data-ttu-id="617da-116">Страница пользовательского макета, которую необходимо использовать.</span><span class="sxs-lookup"><span data-stu-id="617da-116">Custom Layout page to use.</span></span> |
| <span data-ttu-id="617da-117">--useDefaultLayout или -udl</span><span class="sxs-lookup"><span data-stu-id="617da-117">--useDefaultLayout or -udl</span></span> | <span data-ttu-id="617da-118">Использовать макет по умолчанию для представлений.</span><span class="sxs-lookup"><span data-stu-id="617da-118">Use the default layout for the views.</span></span> |
| <span data-ttu-id="617da-119">--force или -f</span><span class="sxs-lookup"><span data-stu-id="617da-119">--force or -f</span></span> | <span data-ttu-id="617da-120">Перезаписать существующие файлы.</span><span class="sxs-lookup"><span data-stu-id="617da-120">Overwrite existing files.</span></span> |
| <span data-ttu-id="617da-121">--relativeFolderPath или -outDir</span><span class="sxs-lookup"><span data-stu-id="617da-121">--relativeFolderPath or -outDir</span></span> | <span data-ttu-id="617da-122">Относительный путь к папке выходных данных из проекта, в котором создаются файлы.</span><span class="sxs-lookup"><span data-stu-id="617da-122">The relative output folder path from project where the file are generated.</span></span> <span data-ttu-id="617da-123">Если этот параметр не задан, файлы создаются в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="617da-123">If not specified, files are generated in the project folder.</span></span> |