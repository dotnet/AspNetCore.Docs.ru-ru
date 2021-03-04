---
title: Примеры проверки подлинности для ASP.NET Core
author: rick-anderson
description: Содержит ссылки на примеры проверки подлинности в репозитории ASP.NET Core.
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110122"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="e279e-103">Примеры проверки подлинности для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e279e-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="e279e-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="e279e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e279e-105">[Репозиторий ASP.NET Core](https://github.com/dotnet/aspnetcore) содержит следующие примеры проверки подлинности ( `main` ветвь):</span><span class="sxs-lookup"><span data-stu-id="e279e-105">The [ASP.NET Core repository](https://github.com/dotnet/aspnetcore) contains the following authentication samples (`main` branch):</span></span>

* [<span data-ttu-id="e279e-106">Преобразование утверждений</span><span class="sxs-lookup"><span data-stu-id="e279e-106">Claims transformation</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="e279e-107">[Cookie идентификаци](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="e279e-107">[Cookie authentication](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="e279e-108">Пользовательский ответ на ошибку авторизации</span><span class="sxs-lookup"><span data-stu-id="e279e-108">Custom authorization failure response</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [<span data-ttu-id="e279e-109">Поставщик настраиваемой политики — Иаусоризатионполиципровидер</span><span class="sxs-lookup"><span data-stu-id="e279e-109">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="e279e-110">Схемы и параметры динамической проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="e279e-110">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="e279e-111">[Внешние утверждения](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="e279e-111">[External claims](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="e279e-112">Выбор между cookie и другой схемой проверки подлинности на основе запроса</span><span class="sxs-lookup"><span data-stu-id="e279e-112">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="e279e-113">Разрешает доступ к статическим файлам</span><span class="sxs-lookup"><span data-stu-id="e279e-113">Restricts access to static files</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a><span data-ttu-id="e279e-114">Получение и запуск примеров</span><span class="sxs-lookup"><span data-stu-id="e279e-114">Obtain and run the samples</span></span>

<span data-ttu-id="e279e-115">Примеры ссылок, приведенные в этой статье, содержат образцы для предстоящего выпуска ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e279e-115">The sample links provided in this article provide samples for the upcoming release of ASP.NET Core.</span></span> <span data-ttu-id="e279e-116">Чтобы получить пример для текущего выпуска или более ранней версии, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e279e-116">To obtain a sample for the current release or a prior release, perform the following steps:</span></span>

* <span data-ttu-id="e279e-117">Выберите ветвь выпуска [репозитория ASP.NET Core](https://github.com/dotnet/aspnetcore)] ( https://github.com/dotnet/aspnetcore) .</span><span class="sxs-lookup"><span data-stu-id="e279e-117">Select the release branch of the [ASP.NET Core repository](https://github.com/dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore).</span></span> <span data-ttu-id="e279e-118">Например, `release/5.0` ветвь содержит примеры для выпуска ASP.NET Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="e279e-118">For example, the `release/5.0` branch contains the samples for the ASP.NET Core 5.0 release.</span></span>
* <span data-ttu-id="e279e-119">Клонировать или скачать репозиторий ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e279e-119">Clone or download the ASP.NET Core repository.</span></span>
* <span data-ttu-id="e279e-120">Убедитесь, что в локальной системе установлена версия [пакет SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core) , соответствующая клону репозитория ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e279e-120">On your local system, verify installation of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="e279e-121">Перейдите к примеру в `aspnetcore/src/Security/samples` папке и запустите пример с помощью [ `dotnet run` команды](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="e279e-121">Navigate to a sample in `aspnetcore/src/Security/samples` folder and run the sample with the [`dotnet run` command](/dotnet/core/tools/dotnet-run).</span></span>
