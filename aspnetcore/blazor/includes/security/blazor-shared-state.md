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
ms.openlocfilehash: 5ff4e4368d9e6d7c8525ae4ef0625d176a256a85
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551260"
---
<span data-ttu-id="620cd-101">Серверные приложения Blazor находятся в памяти сервера.</span><span class="sxs-lookup"><span data-stu-id="620cd-101">Blazor server apps live in server memory.</span></span> <span data-ttu-id="620cd-102">Это означает, что в одном процессе размещается несколько приложений.</span><span class="sxs-lookup"><span data-stu-id="620cd-102">That means that there are multiple apps hosted within the same process.</span></span> <span data-ttu-id="620cd-103">Для каждого сеанса приложения Blazor запускает канал с собственной областью контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="620cd-103">For each app session, Blazor starts a circuit with its own DI container scope.</span></span> <span data-ttu-id="620cd-104">Это означает, что службы с областью действия являются уникальными для каждого сеанса Blazor.</span><span class="sxs-lookup"><span data-stu-id="620cd-104">That means that scoped services are unique per Blazor session.</span></span>

> [!WARNING]
> <span data-ttu-id="620cd-105">Не рекомендуется совместное использование состояния приложениями на одном сервере с помощью отдельных служб, если только вы не принимаете строгие меры предосторожности, поскольку при таком подходе могут возникать уязвимости, например утечка состояния пользователя между каналами.</span><span class="sxs-lookup"><span data-stu-id="620cd-105">We don't recommend apps on the same server share state using singleton services unless extreme care is taken, as this can introduce security vulnerabilities, such as leaking user state across circuits.</span></span>

<span data-ttu-id="620cd-106">В приложениях Blazor можно использовать отдельные службы с отслеживанием состояния, если они предназначены специально для этого.</span><span class="sxs-lookup"><span data-stu-id="620cd-106">You can use stateful singleton services in Blazor apps if they are specifically designed for it.</span></span> <span data-ttu-id="620cd-107">Например, можно использовать кэш памяти как отдельный элемент, поскольку ему требуется ключ для доступа к определенной записи. При этом предполагается, что пользователи не контролируют, какие ключи кэша используются.</span><span class="sxs-lookup"><span data-stu-id="620cd-107">For example, it's ok to use a memory cache as a singleton because it requires a key to access a given entry, assuming users don't have control of what cache keys are used.</span></span>

<span data-ttu-id="620cd-108">**Кроме того, в целях безопасности не следует использовать <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> в приложениях Blazor.**</span><span class="sxs-lookup"><span data-stu-id="620cd-108">**Additionally, again for security reasons, you must not use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> within Blazor apps.**</span></span> <span data-ttu-id="620cd-109">Приложения Blazor запускаются вне контекста конвейера ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="620cd-109">Blazor apps run outside of the context of the ASP.NET Core pipeline.</span></span> <span data-ttu-id="620cd-110">Доступность <xref:Microsoft.AspNetCore.Http.HttpContext> в <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> не гарантируется. Также не гарантируется, что в этом интерфейсе содержится контекст запуска приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="620cd-110">The <xref:Microsoft.AspNetCore.Http.HttpContext> isn't guaranteed to be available within the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, nor is it guaranteed to be holding the context that started the Blazor app.</span></span>

<span data-ttu-id="620cd-111">Рекомендуемый способ передачи состояния запроса в приложение Blazor — через параметры корневого компонента при первоначальной отрисовке приложения:</span><span class="sxs-lookup"><span data-stu-id="620cd-111">The recommended way to pass request state to the Blazor app is through parameters to the root component in the initial rendering of the app:</span></span>

* <span data-ttu-id="620cd-112">Определите класс со всеми данными, которые необходимо передать в приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="620cd-112">Define a class with all the data you want to pass to the Blazor app.</span></span>
* <span data-ttu-id="620cd-113">Заполните эти данные со страницы Razor, используя <xref:Microsoft.AspNetCore.Http.HttpContext>, доступный на данный момент.</span><span class="sxs-lookup"><span data-stu-id="620cd-113">Populate that data from the Razor page using the <xref:Microsoft.AspNetCore.Http.HttpContext> available at that time.</span></span>
* <span data-ttu-id="620cd-114">Передайте данные в приложение Blazor в качестве параметра в корневой компонент (приложение).</span><span class="sxs-lookup"><span data-stu-id="620cd-114">Pass the data to the Blazor app as a parameter to the root component (App).</span></span>
* <span data-ttu-id="620cd-115">Определите параметр в корневом компоненте для хранения данных, передаваемых в приложение.</span><span class="sxs-lookup"><span data-stu-id="620cd-115">Define a parameter in the root component to hold the data being passed to the app.</span></span>
* <span data-ttu-id="620cd-116">Используйте данные, относящиеся к пользователю, в приложении или скопируйте эти данные в службу с заданной областью в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, чтобы их можно было использовать в приложении.</span><span class="sxs-lookup"><span data-stu-id="620cd-116">Use the user-specific data within the app; or alternatively, copy that data into a scoped service within <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> so that it can be used across the app.</span></span>

<span data-ttu-id="620cd-117">Дополнительные сведения и примеры с кодом см. в разделе <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="620cd-117">For more information and example code, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>
