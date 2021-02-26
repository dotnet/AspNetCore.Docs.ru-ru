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
## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="bc73b-101">Совместное использование кода взаимодействия в библиотеке классов</span><span class="sxs-lookup"><span data-stu-id="bc73b-101">Share interop code in a class library</span></span>

<span data-ttu-id="bc73b-102">Код взаимодействия с JS можно добавить в библиотеку классов и затем совместно использовать в пакете NuGet.</span><span class="sxs-lookup"><span data-stu-id="bc73b-102">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="bc73b-103">Библиотека классов обрабатывает внедрение ресурсов JavaScript в готовой сборке.</span><span class="sxs-lookup"><span data-stu-id="bc73b-103">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="bc73b-104">Файлы JavaScript помещаются в папку `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="bc73b-104">The JavaScript files are placed in the `wwwroot` folder.</span></span> <span data-ttu-id="bc73b-105">Ресурсы внедряются с помощью инструментов при создании библиотеки.</span><span class="sxs-lookup"><span data-stu-id="bc73b-105">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="bc73b-106">Сформированный пакет NuGet указывается в файле проекта приложения так же, как и любой пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="bc73b-106">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="bc73b-107">После восстановления пакета код приложения может осуществлять вызовы в JavaScript так же, как в C#.</span><span class="sxs-lookup"><span data-stu-id="bc73b-107">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="bc73b-108">Дополнительные сведения см. в разделе <xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="bc73b-108">For more information, see <xref:blazor/components/class-libraries>.</span></span>
