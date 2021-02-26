---
title: Настройка средства обрезки для ASP.NET Core Blazor
author: guardrex
description: Узнайте, как управлять компоновщиком (средством обрезки) для промежуточного языка (IL) при сборке приложения Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975223"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="1aede-103">Настройка средства обрезки для ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="1aede-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="1aede-104">Blazor WebAssembly выполняет обрезку [промежуточного языка (IL)](/dotnet/standard/managed-code#intermediate-language--execution) для уменьшения размера публикуемых выходных данных.</span><span class="sxs-lookup"><span data-stu-id="1aede-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="1aede-105">По умолчанию усечение выполняется при публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="1aede-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="1aede-106">Обрезка может иметь негативные последствия.</span><span class="sxs-lookup"><span data-stu-id="1aede-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="1aede-107">В приложениях, использующих отражение, обрезка зачастую не позволяет определить необходимые типы для отражения во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="1aede-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="1aede-108">Чтобы обрезать такие приложения, средство обрезки должно быть уведомлено о любых типах, необходимых для отражения как в коде, так и в пакетах или платформах, от которых зависит приложение.</span><span class="sxs-lookup"><span data-stu-id="1aede-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="1aede-109">Обрезка также не реагирует на динамическое поведение приложения во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="1aede-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="1aede-110">Чтобы обеспечить правильную работу обрезанного приложения после его развертывания, следует часто тестировать публикуемые выходные данные во время разработки.</span><span class="sxs-lookup"><span data-stu-id="1aede-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="1aede-111">Сведения о настройке средства обрезки см. в статье [Параметры обрезки](/dotnet/core/deploying/trimming-options) в документации по основным понятиям .NET, в которую входят рекомендации по следующим темам:</span><span class="sxs-lookup"><span data-stu-id="1aede-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="1aede-112">Отключение обрезки для всего приложения со свойством `<PublishTrimmed>` в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="1aede-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="1aede-113">Управление тем, как агрессивно неиспользуемый IL отбрасывается с помощью усечения.</span><span class="sxs-lookup"><span data-stu-id="1aede-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="1aede-114">Остановка усечения отдельных сборок.</span><span class="sxs-lookup"><span data-stu-id="1aede-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="1aede-115">"Корневые" сборки для усечения.</span><span class="sxs-lookup"><span data-stu-id="1aede-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="1aede-116">Отображение предупреждений для отраженных типов путем присвоения свойству `<SuppressTrimAnalysisWarnings>` значения `false` в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="1aede-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="1aede-117">Управление обрезкой символов и поддержкой отладчика.</span><span class="sxs-lookup"><span data-stu-id="1aede-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="1aede-118">Настройка функций средства обрезки для усечения функций библиотеки инфраструктуры.</span><span class="sxs-lookup"><span data-stu-id="1aede-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1aede-119">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1aede-119">Additional resources</span></span>

* [<span data-ttu-id="1aede-120">Обрезка автономных развертываний и исполняемых файлов</span><span class="sxs-lookup"><span data-stu-id="1aede-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
