---
title: Адрес списка надежных IP-адресов клиента для ASP.NET Core
author: damienbod
description: Узнайте, как создавать по промежуточного слоя или фильтры действий для проверки удаленных IP-адресов по списку утвержденных IP-адресов.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
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
uid: security/ip-safelist
ms.openlocfilehash: f89191cff1faa5b55ee9031a6a6d0749a74e43c0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587545"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="94e2c-103">Адрес списка надежных IP-адресов клиента для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94e2c-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="94e2c-104">[(Damien Бауден](https://twitter.com/damien_bod) и [Tom Dykstra)](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="94e2c-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="94e2c-105">В этой статье показано три способа реализации списка надежных IP-адресов (также известного как список разрешений) в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="94e2c-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="94e2c-106">В прилагаемом примере приложения демонстрируются все три подхода.</span><span class="sxs-lookup"><span data-stu-id="94e2c-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="94e2c-107">Вы можете использовать:</span><span class="sxs-lookup"><span data-stu-id="94e2c-107">You can use:</span></span>

* <span data-ttu-id="94e2c-108">По промежуточного слоя для проверки удаленного IP-адреса каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="94e2c-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="94e2c-109">Фильтры действий MVC для проверки удаленного IP-адреса запросов для конкретных контроллеров или методов действий.</span><span class="sxs-lookup"><span data-stu-id="94e2c-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="94e2c-110">Razor Страницы фильтруется для проверки удаленного IP-адреса запросов к Razor страницам.</span><span class="sxs-lookup"><span data-stu-id="94e2c-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="94e2c-111">В каждом случае строка, содержащая утвержденные IP-адреса клиентов, сохраняется в параметре приложения.</span><span class="sxs-lookup"><span data-stu-id="94e2c-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="94e2c-112">По промежуточного слоя или фильтра:</span><span class="sxs-lookup"><span data-stu-id="94e2c-112">The middleware or filter:</span></span>

* <span data-ttu-id="94e2c-113">Выполняет синтаксический анализ строки в массиве.</span><span class="sxs-lookup"><span data-stu-id="94e2c-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="94e2c-114">Проверяет, существует ли удаленный IP-адрес в массиве.</span><span class="sxs-lookup"><span data-stu-id="94e2c-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="94e2c-115">Доступ разрешен, если массив содержит IP-адрес.</span><span class="sxs-lookup"><span data-stu-id="94e2c-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="94e2c-116">В противном случае возвращается код состояния HTTP 403 запрещено.</span><span class="sxs-lookup"><span data-stu-id="94e2c-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="94e2c-117">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/ip-safelist/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="94e2c-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="94e2c-118">Адрес списка надежных IP-адресов</span><span class="sxs-lookup"><span data-stu-id="94e2c-118">IP address safelist</span></span>

<span data-ttu-id="94e2c-119">В примере приложения адрес списка надежных IP-адресов:</span><span class="sxs-lookup"><span data-stu-id="94e2c-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="94e2c-120">Определяется `AdminSafeList` свойством в *appsettings.json* файле.</span><span class="sxs-lookup"><span data-stu-id="94e2c-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="94e2c-121">Строка с разделителями-точкой с запятой, которая может содержать адреса [протокола IP версии 4 (IPv4)](https://wikipedia.org/wiki/IPv4) и [протокола IP версии 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .</span><span class="sxs-lookup"><span data-stu-id="94e2c-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="94e2c-122">В предыдущем примере разрешены адреса IPv4 и и `127.0.0.1` `192.168.1.5` IPv6-адрес замыкания на себя `::1` (сжатый формат для `0:0:0:0:0:0:0:1` ).</span><span class="sxs-lookup"><span data-stu-id="94e2c-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="94e2c-123">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="94e2c-123">Middleware</span></span>

<span data-ttu-id="94e2c-124">`Startup.Configure`Метод добавляет пользовательский `AdminSafeListMiddleware` тип по промежуточного слоя в конвейер запросов приложения.</span><span class="sxs-lookup"><span data-stu-id="94e2c-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="94e2c-125">Списка надежных отправителей извлекается с помощью поставщика конфигурации .NET Core и передается в качестве параметра конструктора.</span><span class="sxs-lookup"><span data-stu-id="94e2c-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="94e2c-126">По промежуточного слоя анализирует строку в массив и ищет удаленный IP-адрес в массиве.</span><span class="sxs-lookup"><span data-stu-id="94e2c-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="94e2c-127">Если удаленный IP-адрес не найден, по промежуточного слоя возвращает HTTP 403 запрещено.</span><span class="sxs-lookup"><span data-stu-id="94e2c-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="94e2c-128">Этот процесс проверки обходится для HTTP-запросов GET.</span><span class="sxs-lookup"><span data-stu-id="94e2c-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="94e2c-129">Фильтр действий</span><span class="sxs-lookup"><span data-stu-id="94e2c-129">Action filter</span></span>

<span data-ttu-id="94e2c-130">Если требуется управление доступом к спискам надежных отправителей для конкретных контроллеров MVC или методов действий, используйте фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="94e2c-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="94e2c-131">Пример:</span><span class="sxs-lookup"><span data-stu-id="94e2c-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="94e2c-132">В `Startup.ConfigureServices` добавьте фильтр действий в коллекцию фильтров MVC.</span><span class="sxs-lookup"><span data-stu-id="94e2c-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="94e2c-133">В следующем примере `ClientIpCheckActionFilter` добавляется фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="94e2c-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="94e2c-134">Объект списка надежных отправителей и экземпляр средства ведения журнала консоли передаются как параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="94e2c-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="94e2c-135">Затем фильтр действий можно применить к контроллеру или методу действия с помощью атрибута [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :</span><span class="sxs-lookup"><span data-stu-id="94e2c-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="94e2c-136">В примере приложения фильтр действий применяется к `Get` методу действия контроллера.</span><span class="sxs-lookup"><span data-stu-id="94e2c-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="94e2c-137">При тестировании приложения путем отправки:</span><span class="sxs-lookup"><span data-stu-id="94e2c-137">When you test the app by sending:</span></span>

* <span data-ttu-id="94e2c-138">Запрос HTTP GET, `[ServiceFilter]` атрибут проверяет IP-адрес клиента.</span><span class="sxs-lookup"><span data-stu-id="94e2c-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="94e2c-139">Если доступ разрешен для `Get` метода действия, то в фильтре действий и методе действия создается разновидность следующих выходных данных консоли:</span><span class="sxs-lookup"><span data-stu-id="94e2c-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="94e2c-140">Команда HTTP-запроса, отличная от GET, по `AdminSafeListMiddleware` промежуточного слоя проверяет IP-адрес клиента.</span><span class="sxs-lookup"><span data-stu-id="94e2c-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="94e2c-141">Razor Фильтр страниц</span><span class="sxs-lookup"><span data-stu-id="94e2c-141">Razor Pages filter</span></span>

<span data-ttu-id="94e2c-142">Если требуется управлять доступом к спискам надежных отправителей для Razor приложения страниц, используйте Razor Фильтр страниц.</span><span class="sxs-lookup"><span data-stu-id="94e2c-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="94e2c-143">Пример:</span><span class="sxs-lookup"><span data-stu-id="94e2c-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="94e2c-144">В `Startup.ConfigureServices` включите Razor Фильтр страниц, добавив его в коллекцию фильтров MVC.</span><span class="sxs-lookup"><span data-stu-id="94e2c-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="94e2c-145">В следующем примере `ClientIpCheckPageFilter` Razor добавляется фильтр страниц.</span><span class="sxs-lookup"><span data-stu-id="94e2c-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="94e2c-146">Объект списка надежных отправителей и экземпляр средства ведения журнала консоли передаются как параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="94e2c-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="94e2c-147">Когда  Razor запрашивается страница индекса примера приложения, Razor Фильтр страниц проверяет IP-адрес клиента.</span><span class="sxs-lookup"><span data-stu-id="94e2c-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="94e2c-148">Фильтр создает разновидность следующих выходных данных консоли:</span><span class="sxs-lookup"><span data-stu-id="94e2c-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="94e2c-149">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="94e2c-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="94e2c-150">Фильтры действий</span><span class="sxs-lookup"><span data-stu-id="94e2c-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
