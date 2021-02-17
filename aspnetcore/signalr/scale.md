---
title: ASP.NET Core SignalR производственного размещения и масштабирования
author: bradygaster
description: Узнайте, как избежать проблем с производительностью и масштабированием в приложениях, использующих ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564005"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a><span data-ttu-id="44076-103">ASP.NET Core SignalR размещение и масштабирование</span><span class="sxs-lookup"><span data-stu-id="44076-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="44076-104">[Эндрю Стантон-медперсонала](https://twitter.com/anurse), [Брейди Гастер](https://twitter.com/bradygaster)и [Tom Dykstra)](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="44076-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="44076-105">В этой статье описываются вопросы размещения и масштабирования для приложений с высоким уровнем трафика, использующих ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="44076-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="44076-106">Закрепленные сеансы</span><span class="sxs-lookup"><span data-stu-id="44076-106">Sticky Sessions</span></span>

<span data-ttu-id="44076-107">SignalR требует, чтобы все HTTP-запросы для определенного соединения обрабатывались одним и тем же процессом сервера.</span><span class="sxs-lookup"><span data-stu-id="44076-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="44076-108">Если SignalR работает на ферме серверов (несколько серверов), необходимо использовать "прикрепленные сеансы".</span><span class="sxs-lookup"><span data-stu-id="44076-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="44076-109">«Прикрепленные сеансы» также называют сходством сеансов некоторыми подсистемами балансировки нагрузки.</span><span class="sxs-lookup"><span data-stu-id="44076-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="44076-110">Служба приложений Azure использует [маршрутизацию запросов приложений](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) для маршрутизации запросов.</span><span class="sxs-lookup"><span data-stu-id="44076-110">Azure App Service uses [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="44076-111">Включение параметра "Настройка сходства" в службе приложений Azure включит "закрепленные сеансы".</span><span class="sxs-lookup"><span data-stu-id="44076-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="44076-112">Существуют следующие ситуации, в которых не требуется закрепить сеансы.</span><span class="sxs-lookup"><span data-stu-id="44076-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="44076-113">При размещении на одном сервере в одном процессе.</span><span class="sxs-lookup"><span data-stu-id="44076-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="44076-114">При использовании службы Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="44076-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="44076-115">Если все клиенты настроены на использование **только** WebSockets, **а** [параметр скипнеготиатион](xref:signalr/configuration#configure-additional-options) включен в конфигурации клиента.</span><span class="sxs-lookup"><span data-stu-id="44076-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="44076-116">Во всех остальных случаях (включая использование объединительной платы Redis) серверная среда должна быть настроена для работы с закрепленными сеансами.</span><span class="sxs-lookup"><span data-stu-id="44076-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="44076-117">Рекомендации по настройке службы приложений Azure для SignalR см. в разделе <xref:signalr/publish-to-azure-web-app> .</span><span class="sxs-lookup"><span data-stu-id="44076-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="44076-118">Ресурсы TCP-подключения</span><span class="sxs-lookup"><span data-stu-id="44076-118">TCP connection resources</span></span>

<span data-ttu-id="44076-119">Количество одновременных TCP-подключений, которое может поддерживаться веб-сервером, ограничено.</span><span class="sxs-lookup"><span data-stu-id="44076-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="44076-120">Стандартные клиенты HTTP используют *временные* подключения.</span><span class="sxs-lookup"><span data-stu-id="44076-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="44076-121">Эти подключения могут быть закрыты, когда клиент переходит в состояние простоя и снова открывается позже.</span><span class="sxs-lookup"><span data-stu-id="44076-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="44076-122">С другой стороны, SignalR подключение является *постоянным*.</span><span class="sxs-lookup"><span data-stu-id="44076-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="44076-123">SignalR соединения остаются открытыми, даже когда клиент переходит в состояние бездействия.</span><span class="sxs-lookup"><span data-stu-id="44076-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="44076-124">В приложении с большим трафиком, которое обслуживает много клиентов, эти постоянные подключения могут привести к тому, что серверы достигли максимального числа подключений.</span><span class="sxs-lookup"><span data-stu-id="44076-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="44076-125">Постоянные подключения также потребляют некоторую дополнительную память для наблюдения за каждым подключением.</span><span class="sxs-lookup"><span data-stu-id="44076-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="44076-126">Интенсивное использование ресурсов, связанных с подключением, SignalR может повлиять на другие веб-приложения, размещенные на том же сервере.</span><span class="sxs-lookup"><span data-stu-id="44076-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="44076-127">Когда SignalR открывает и сохраняет последние доступные подключения TCP, другие веб-приложения на том же сервере также будут иметь недоступные для них подключения.</span><span class="sxs-lookup"><span data-stu-id="44076-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="44076-128">Если на сервере не хватает подключений, вы увидите случайные ошибки сокета и ошибки сброса соединения.</span><span class="sxs-lookup"><span data-stu-id="44076-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="44076-129">Пример:</span><span class="sxs-lookup"><span data-stu-id="44076-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="44076-130">Чтобы SignalR использование ресурсов не вызывало ошибок в других веб-приложениях, запустите SignalR на разных серверах другие веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="44076-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="44076-131">Чтобы SignalR использование ресурсов не вызывало ошибок в SignalR приложении, необходимо выполнить масштабирование, чтобы ограничить число подключений, которое должен выполнять сервер.</span><span class="sxs-lookup"><span data-stu-id="44076-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="44076-132">Горизонтальное увеличение масштаба</span><span class="sxs-lookup"><span data-stu-id="44076-132">Scale out</span></span>

<span data-ttu-id="44076-133">Приложение, использующее, SignalR должно отследить все его подключения, что создает проблемы для фермы серверов.</span><span class="sxs-lookup"><span data-stu-id="44076-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="44076-134">Добавьте сервер, и он получает новые подключения, о которых не известны другие серверы.</span><span class="sxs-lookup"><span data-stu-id="44076-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="44076-135">Например, SignalR на каждом сервере на следующей схеме не знаются соединения на других серверах.</span><span class="sxs-lookup"><span data-stu-id="44076-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="44076-136">Когда SignalR на одном из серверов требуется отправить сообщение всем клиентам, оно передается только тем клиентам, которые подключены к этому серверу.</span><span class="sxs-lookup"><span data-stu-id="44076-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Масштабирование::: No-Loc (SignalR)::: без объединительной платы](scale/_static/scale-no-backplane.png)

<span data-ttu-id="44076-138">Варианты решения этой проблемы: [ SignalR служба Azure](#azure-signalr-service) и [Redisная Объединительная плата](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="44076-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-signalr-service"></a><span data-ttu-id="44076-139">Служба Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="44076-139">Azure SignalR Service</span></span>

<span data-ttu-id="44076-140">Служба Azure SignalR — это прокси-сервер, а не Объединительная плата.</span><span class="sxs-lookup"><span data-stu-id="44076-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="44076-141">Каждый раз, когда клиент инициирует подключение к серверу, клиент перенаправляется для подключения к службе.</span><span class="sxs-lookup"><span data-stu-id="44076-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="44076-142">Этот процесс показан на следующей схеме:</span><span class="sxs-lookup"><span data-stu-id="44076-142">That process is illustrated in the following diagram:</span></span>

![Установление соединения с Azure::: No-Loc (SignalR)::: Service](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="44076-144">В результате служба управляет всеми клиентскими подключениями, в то время как каждому серверу требуется лишь небольшое постоянное число подключений к службе, как показано на следующей схеме:</span><span class="sxs-lookup"><span data-stu-id="44076-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Клиенты, подключенные к службе, серверы, подключенные к службе](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="44076-146">Этот подход к горизонтальному масштабированию имеет несколько преимуществ по сравнению с альтернативой объединительной платы Redis:</span><span class="sxs-lookup"><span data-stu-id="44076-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="44076-147">Прикрепленные сеансы, также известные как [сходство клиентов](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), не являются обязательными, так как клиенты сразу же перенаправляются в SignalR службу Azure при подключении.</span><span class="sxs-lookup"><span data-stu-id="44076-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="44076-148">SignalRПриложение может масштабироваться в зависимости от числа отправленных сообщений, а SignalR служба Azure масштабируется для выполнения любого числа подключений.</span><span class="sxs-lookup"><span data-stu-id="44076-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service scales to handle any number of connections.</span></span> <span data-ttu-id="44076-149">Например, могут быть тысячи клиентов, но если отправлено всего несколько сообщений в секунду, SignalR приложению не придется масштабироваться на несколько серверов, чтобы обрабатывать сами подключения.</span><span class="sxs-lookup"><span data-stu-id="44076-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="44076-150">SignalRПриложение не будет использовать значительно больше ресурсов для подключения, чем веб-приложение SignalR .</span><span class="sxs-lookup"><span data-stu-id="44076-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="44076-151">По этим причинам мы рекомендуем использовать службу Azure SignalR для всех ASP.NET Core SignalR приложений, размещенных в Azure, включая службу приложений, виртуальные машины и контейнеры.</span><span class="sxs-lookup"><span data-stu-id="44076-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="44076-152">Дополнительные сведения см. в [ SignalR документации по службе Azure](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="44076-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="44076-153">Канал обмена Redis</span><span class="sxs-lookup"><span data-stu-id="44076-153">Redis backplane</span></span>

<span data-ttu-id="44076-154">[Redis](https://redis.io/) — это хранилище "ключ — значение" в памяти, которое поддерживает систему обмена сообщениями с моделью публикации и подписки.</span><span class="sxs-lookup"><span data-stu-id="44076-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="44076-155">SignalRДля пересылки сообщений на другие серверы в объединительной плате Redis используется функция Pub/подкаталогов.</span><span class="sxs-lookup"><span data-stu-id="44076-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="44076-156">Когда клиент устанавливает соединение, сведения о соединении передаются в объединительную плату.</span><span class="sxs-lookup"><span data-stu-id="44076-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="44076-157">Когда сервер хочет отправить сообщение всем клиентам, он отправляется в объединительную плату.</span><span class="sxs-lookup"><span data-stu-id="44076-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="44076-158">Объединительная плата знает все подключенные клиенты и серверы, на которых они находятся.</span><span class="sxs-lookup"><span data-stu-id="44076-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="44076-159">Он отправляет сообщение всем клиентам через соответствующие серверы.</span><span class="sxs-lookup"><span data-stu-id="44076-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="44076-160">Этот процесс показан на следующей схеме:</span><span class="sxs-lookup"><span data-stu-id="44076-160">This process is illustrated in the following diagram:</span></span>

![Redis Объединительная часть, сообщение, отправленное с одного сервера на все клиенты](scale/_static/redis-backplane.png)

<span data-ttu-id="44076-162">Объединительная плата Redis — это рекомендуемый подход к горизонтальному масштабированию для приложений, размещенных в собственной инфраструктуре.</span><span class="sxs-lookup"><span data-stu-id="44076-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="44076-163">Если между центром обработки данных и центром обработки данных Azure возникает значительная задержка подключения, SignalR служба Azure может оказаться непрактичной для локальных приложений с низкой задержкой или требованиями высокой пропускной способности.</span><span class="sxs-lookup"><span data-stu-id="44076-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="44076-164">SignalRПреимущества службы Azure, упомянутые ранее, являются недостатками объединительной платы Redis:</span><span class="sxs-lookup"><span data-stu-id="44076-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="44076-165">Прикрепленные сеансы, также известные как [сходство клиентов](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), являются обязательными, за исключением случаев, когда выполняются **оба** следующих условия.</span><span class="sxs-lookup"><span data-stu-id="44076-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="44076-166">Все клиенты настроены для использования **только** WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44076-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="44076-167">[Параметр скипнеготиатион](xref:signalr/configuration#configure-additional-options) включен в конфигурации клиента.</span><span class="sxs-lookup"><span data-stu-id="44076-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="44076-168">После инициации подключения на сервере соединение должно остаться на этом сервере.</span><span class="sxs-lookup"><span data-stu-id="44076-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="44076-169">SignalRПриложение должно масштабироваться на основе числа клиентов, даже если отправляется несколько сообщений.</span><span class="sxs-lookup"><span data-stu-id="44076-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="44076-170">SignalRПриложение использует значительно больше ресурсов для подключения, чем веб-приложение, без SignalR .</span><span class="sxs-lookup"><span data-stu-id="44076-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="44076-171">Ограничения IIS в клиентской ОС Windows</span><span class="sxs-lookup"><span data-stu-id="44076-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="44076-172">Windows 10 и Windows 8. x являются клиентскими операционными системами.</span><span class="sxs-lookup"><span data-stu-id="44076-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="44076-173">Службы IIS в клиентских операционных системах имеют ограничение в 10 одновременных подключений.</span><span class="sxs-lookup"><span data-stu-id="44076-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="44076-174">SignalRподключения:</span><span class="sxs-lookup"><span data-stu-id="44076-174">SignalR's connections are:</span></span>

* <span data-ttu-id="44076-175">Временное и часто переустановленное.</span><span class="sxs-lookup"><span data-stu-id="44076-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="44076-176">**Не** удаляется сразу, когда больше не используется.</span><span class="sxs-lookup"><span data-stu-id="44076-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="44076-177">Предыдущие условия, вероятно, пристигают 10 ограничений на подключение к клиентской ОС.</span><span class="sxs-lookup"><span data-stu-id="44076-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="44076-178">При использовании клиентской ОС для разработки рекомендуется:</span><span class="sxs-lookup"><span data-stu-id="44076-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="44076-179">Избегайте IIS.</span><span class="sxs-lookup"><span data-stu-id="44076-179">Avoid IIS.</span></span>
* <span data-ttu-id="44076-180">Используйте Kestrel или IIS Express в качестве целевых объектов развертывания.</span><span class="sxs-lookup"><span data-stu-id="44076-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="44076-181">Linux с Nginx</span><span class="sxs-lookup"><span data-stu-id="44076-181">Linux with Nginx</span></span>

<span data-ttu-id="44076-182">Ниже приведены минимальные необходимые параметры для включения WebSockets, Серверсентевентс и Лонгполлинг для SignalR :</span><span class="sxs-lookup"><span data-stu-id="44076-182">The following contains the minimum required settings to enable WebSockets, ServerSentEvents, and LongPolling for SignalR:</span></span>

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

<span data-ttu-id="44076-183">При использовании нескольких внутренних серверов необходимо добавить прикрепленные сеансы, чтобы предотвратить SignalR Переключение серверов при подключении.</span><span class="sxs-lookup"><span data-stu-id="44076-183">When multiple backend servers are used, sticky sessions must be added to prevent SignalR connections from switching servers when connecting.</span></span> <span data-ttu-id="44076-184">Существует несколько способов добавления прикрепленных сеансов в nginx.</span><span class="sxs-lookup"><span data-stu-id="44076-184">There are multiple ways to add sticky sessions in Nginx.</span></span> <span data-ttu-id="44076-185">Ниже показаны два подхода в зависимости от доступных возможностей.</span><span class="sxs-lookup"><span data-stu-id="44076-185">Two approaches are shown below depending on what you have available.</span></span>

<span data-ttu-id="44076-186">В дополнение к предыдущей конфигурации добавляется следующее.</span><span class="sxs-lookup"><span data-stu-id="44076-186">The following is added in addition to the previous configuration.</span></span> <span data-ttu-id="44076-187">В следующих примерах `backend` — это имя группы серверов.</span><span class="sxs-lookup"><span data-stu-id="44076-187">In the following examples, `backend` is the name of the group of servers.</span></span>

<span data-ttu-id="44076-188">С [открытым исходным кодом nginx](https://nginx.org/en/)используйте `ip_hash` для маршрутизации подключений к серверу на основе IP-адреса клиента:</span><span class="sxs-lookup"><span data-stu-id="44076-188">With [Nginx Open Source](https://nginx.org/en/), use `ip_hash` to route connections to a server based on the client's IP address:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

<span data-ttu-id="44076-189">С помощью [nginx Plus](https://www.nginx.com/products/nginx)используйте, `sticky` чтобы добавить cookie запрос в запросы и закрепить запросы пользователя на сервере:</span><span class="sxs-lookup"><span data-stu-id="44076-189">With [Nginx Plus](https://www.nginx.com/products/nginx), use `sticky` to add a cookie to requests and pin the user's requests to a server:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

<span data-ttu-id="44076-190">Наконец, измените `proxy_pass http://localhost:5000` в `server` разделе на `proxy_pass http://backend` .</span><span class="sxs-lookup"><span data-stu-id="44076-190">Finally, change `proxy_pass http://localhost:5000` in the `server` section to `proxy_pass http://backend`.</span></span>

<span data-ttu-id="44076-191">Дополнительные сведения о WebSockets через nginx см. в разделе [nginx как прокси-сервер](https://www.nginx.com/blog/websocket-nginx).</span><span class="sxs-lookup"><span data-stu-id="44076-191">For more information on WebSockets over Nginx, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx).</span></span>

<span data-ttu-id="44076-192">Дополнительные сведения о балансировке нагрузки и закрепленных сеансах см. в разделе [nginx Load балансировка нагрузки](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span><span class="sxs-lookup"><span data-stu-id="44076-192">For more information on load balancing and sticky sessions, see [NGINX load balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span></span>

<span data-ttu-id="44076-193">Дополнительные сведения о ASP.NET Core с nginx см. в следующей статье:</span><span class="sxs-lookup"><span data-stu-id="44076-193">For more information about ASP.NET Core with Nginx see the following article:</span></span>
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a><span data-ttu-id="44076-194">Сторонние SignalR поставщики объединительной платы</span><span class="sxs-lookup"><span data-stu-id="44076-194">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="44076-195">NCache</span><span class="sxs-lookup"><span data-stu-id="44076-195">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="44076-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="44076-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>
* <span data-ttu-id="44076-197">[ребус](https://github.com/rebus-org/Rebus.SignalR)</span><span class="sxs-lookup"><span data-stu-id="44076-197">[Rebus](https://github.com/rebus-org/Rebus.SignalR)</span></span>

## <a name="next-steps"></a><span data-ttu-id="44076-198">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="44076-198">Next steps</span></span>

<span data-ttu-id="44076-199">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="44076-199">For more information, see the following resources:</span></span>

* [<span data-ttu-id="44076-200">SignalRДокументация по службе Azure</span><span class="sxs-lookup"><span data-stu-id="44076-200">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="44076-201">Настройка объединительной платы Redis</span><span class="sxs-lookup"><span data-stu-id="44076-201">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
