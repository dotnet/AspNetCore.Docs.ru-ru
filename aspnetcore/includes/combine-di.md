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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536317"
---
<a name="csc"></a>

<span data-ttu-id="58597-101">Рассмотрим следующий метод `ConfigureServices`, который регистрирует службы и настраивает параметры:</span><span class="sxs-lookup"><span data-stu-id="58597-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="58597-102">Связанные группы регистраций можно переместить в метод расширения для регистрации служб.</span><span class="sxs-lookup"><span data-stu-id="58597-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="58597-103">Например, службы конфигурации добавляются в следующий класс:</span><span class="sxs-lookup"><span data-stu-id="58597-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="58597-104">Остальные службы регистрируются в аналогичном классе.</span><span class="sxs-lookup"><span data-stu-id="58597-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="58597-105">Следующий метод `ConfigureServices` использует новые методы расширения для регистрации служб:</span><span class="sxs-lookup"><span data-stu-id="58597-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="58597-106">**_Примечание._** Каждый метод расширения `services.Add{GROUP_NAME}` добавляет и, возможно, настраивает службы.</span><span class="sxs-lookup"><span data-stu-id="58597-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="58597-107">Например, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> добавляет контроллеры MVC служб с необходимыми представлениями, а <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> — службы, требуемые для работы Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="58597-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="58597-108">Рекомендуем придерживаться этого соглашения об именовании в приложениях.</span><span class="sxs-lookup"><span data-stu-id="58597-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="58597-109">Поместите методы расширения в пространство имен <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>, чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="58597-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>
