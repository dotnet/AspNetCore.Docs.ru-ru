---
title: Распределенное кэширование в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать распределенный кэш ASP.NET Core для повышения производительности и масштабируемости приложений, особенно в среде облака или фермы серверов.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: 6f89046f2e1805111dd81b3282253a72a7c6ea09
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281016"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="b7c84-103">Распределенное кэширование в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7c84-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="b7c84-104">[Мохсин Насир](https://github.com/mohsinnasir) и [Стив Смит](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b7c84-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7c84-105">Распределенный кэш — это кэш, совместно используемый несколькими серверами приложений, обычно поддерживаемый как внешняя служба для серверов приложений, обращающихся к ней.</span><span class="sxs-lookup"><span data-stu-id="b7c84-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="b7c84-106">Распределенный кэш может повысить производительность и масштабируемость приложения ASP.NET Core, особенно если приложение размещено в облачной службе или ферме серверов.</span><span class="sxs-lookup"><span data-stu-id="b7c84-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="b7c84-107">Распределенный кэш имеет несколько преимуществ по сравнению с другими сценариями кэширования, в которых кэшированные данные хранятся на отдельных серверах приложений.</span><span class="sxs-lookup"><span data-stu-id="b7c84-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="b7c84-108">При распределении кэшированных данных данные:</span><span class="sxs-lookup"><span data-stu-id="b7c84-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="b7c84-109">—  Согласованность между запросами к нескольким серверам.</span><span class="sxs-lookup"><span data-stu-id="b7c84-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="b7c84-110">Выдерживает перезапуски сервера и развертывание приложений.</span><span class="sxs-lookup"><span data-stu-id="b7c84-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="b7c84-111">Не использует локальную память.</span><span class="sxs-lookup"><span data-stu-id="b7c84-111">Doesn't use local memory.</span></span>

<span data-ttu-id="b7c84-112">Конфигурация распределенного кэша зависит от конкретной реализации.</span><span class="sxs-lookup"><span data-stu-id="b7c84-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="b7c84-113">В этой статье описывается настройка распределенных кэшей SQL Server и Redis.</span><span class="sxs-lookup"><span data-stu-id="b7c84-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="b7c84-114">Также доступны реализации сторонних производителей, например [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="b7c84-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="b7c84-115">Независимо от выбранной реализации приложение взаимодействует с кэшем с помощью <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b7c84-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="b7c84-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7c84-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7c84-117">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="b7c84-117">Prerequisites</span></span>

<span data-ttu-id="b7c84-118">Чтобы использовать SQL Server распределенный кэш, добавьте ссылку на пакет в пакет [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="b7c84-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b7c84-119">Чтобы использовать распределенный кэш Redis, добавьте ссылку на пакет в пакет [Microsoft. Extensions. Caching. стаккексчанжередис](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="b7c84-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="b7c84-120">Чтобы использовать распределенный кэш NCache, добавьте ссылку на пакет в пакет [NCache. Microsoft. Extensions. Caching. конвертер](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="b7c84-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="b7c84-121">Интерфейс IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="b7c84-121">IDistributedCache interface</span></span>

<span data-ttu-id="b7c84-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Интерфейс предоставляет следующие методы для управления элементами в реализации распределенного кэша:</span><span class="sxs-lookup"><span data-stu-id="b7c84-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="b7c84-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Принимает строковый ключ и получает кэшированный элемент в виде `byte[]` массива, если он найден в кэше.</span><span class="sxs-lookup"><span data-stu-id="b7c84-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="b7c84-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Добавляет элемент (в виде `byte[]` массива) в кэш с помощью строкового ключа.</span><span class="sxs-lookup"><span data-stu-id="b7c84-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="b7c84-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Обновляет элемент в кэше на основе его ключа, передавая время ожидания скользящего срока действия (если оно есть).</span><span class="sxs-lookup"><span data-stu-id="b7c84-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="b7c84-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Удаляет элемент кэша на основе ключа строки.</span><span class="sxs-lookup"><span data-stu-id="b7c84-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="b7c84-127">Установка служб распределенного кэширования</span><span class="sxs-lookup"><span data-stu-id="b7c84-127">Establish distributed caching services</span></span>

<span data-ttu-id="b7c84-128">Зарегистрируйте реализацию <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b7c84-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b7c84-129">Реализации, предоставляемые платформой, описанные в этом разделе, включают:</span><span class="sxs-lookup"><span data-stu-id="b7c84-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="b7c84-130">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="b7c84-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="b7c84-131">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="b7c84-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="b7c84-132">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="b7c84-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="b7c84-133">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="b7c84-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="b7c84-134">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="b7c84-134">Distributed Memory Cache</span></span>

<span data-ttu-id="b7c84-135">Кэш распределенной памяти ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) — это предоставляемая платформой реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , которая хранит элементы в памяти.</span><span class="sxs-lookup"><span data-stu-id="b7c84-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="b7c84-136">Кэш распределенной памяти не является фактическим распределенным кэшем.</span><span class="sxs-lookup"><span data-stu-id="b7c84-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="b7c84-137">Кэшированные элементы хранятся в экземпляре приложения на сервере, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="b7c84-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="b7c84-138">Кэш распределенной памяти — это полезная реализация:</span><span class="sxs-lookup"><span data-stu-id="b7c84-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="b7c84-139">В сценариях разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="b7c84-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="b7c84-140">Если в рабочей среде используется один сервер, а использование памяти не является проблемой.</span><span class="sxs-lookup"><span data-stu-id="b7c84-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="b7c84-141">Реализация кэша распределенной памяти абстрагирует хранилище кэшированных данных.</span><span class="sxs-lookup"><span data-stu-id="b7c84-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="b7c84-142">Она позволяет реализовать истинное решение распределенного кэширования в будущем, если потребуется несколько узлов или отказоустойчивость.</span><span class="sxs-lookup"><span data-stu-id="b7c84-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="b7c84-143">Пример приложения использует распределенный кэш памяти при запуске приложения в среде разработки в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b7c84-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="b7c84-144">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="b7c84-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="b7c84-145">Реализация кэша распределенного SQL Server ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) позволяет распределенному кэшу использовать базу данных SQL Server в качестве резервного хранилища.</span><span class="sxs-lookup"><span data-stu-id="b7c84-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="b7c84-146">Чтобы создать SQL Server таблицу кэшированных элементов в экземпляре SQL Server, можно использовать `sql-cache` средство.</span><span class="sxs-lookup"><span data-stu-id="b7c84-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="b7c84-147">Средство создает таблицу с указанными именем и схемой.</span><span class="sxs-lookup"><span data-stu-id="b7c84-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="b7c84-148">Создайте таблицу в SQL Server, выполнив `sql-cache create` команду.</span><span class="sxs-lookup"><span data-stu-id="b7c84-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="b7c84-149">Укажите SQL Server экземпляр ( `Data Source` ), базу данных ( `Initial Catalog` ), схему (например, `dbo` ) и имя таблицы (например, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="b7c84-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="b7c84-150">В журнал заносится сообщение, указывающее, что средство прошло успешно:</span><span class="sxs-lookup"><span data-stu-id="b7c84-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="b7c84-151">Таблица, созданная `sql-cache` средством, имеет следующую схему:</span><span class="sxs-lookup"><span data-stu-id="b7c84-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![Таблица кэша SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="b7c84-153">Приложение должно манипулировать значениями кэша с помощью экземпляра, а <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> не <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="b7c84-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="b7c84-154">Пример приложения реализует <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> в среде, не являющейся средой разработки, в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b7c84-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="b7c84-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(И, при необходимости, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> и <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) обычно хранятся вне системы управления версиями (например, хранится в [диспетчере секретов](xref:security/app-secrets) или в *appsettings.json* / *appSettings. { Файлы среды}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="b7c84-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="b7c84-156">Строка подключения может содержать учетные данные, которые должны содержаться в системе управления версиями.</span><span class="sxs-lookup"><span data-stu-id="b7c84-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="b7c84-157">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="b7c84-157">Distributed Redis Cache</span></span>

<span data-ttu-id="b7c84-158">[Redis](https://redis.io/) -это хранилище данных в памяти с открытым исходным кодом, которое часто используется в качестве распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="b7c84-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span>  <span data-ttu-id="b7c84-159">Вы можете настроить [кэш Redis для Azure](https://azure.microsoft.com/services/cache/) для приложения ASP.NET Core, размещенного в Azure, и использовать кэш Redis для локальной разработки.</span><span class="sxs-lookup"><span data-stu-id="b7c84-159">You can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app, and use an Azure Redis Cache for local development.</span></span>

<span data-ttu-id="b7c84-160">Приложение настраивает реализацию кэша с помощью <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> экземпляра ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ).</span><span class="sxs-lookup"><span data-stu-id="b7c84-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>).</span></span>

<span data-ttu-id="b7c84-161">См. сведения на странице с [ценами на Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).</span><span class="sxs-lookup"><span data-stu-id="b7c84-161">For more information, see [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).</span></span>

<span data-ttu-id="b7c84-162">Ознакомьтесь с [этой проблемой GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/19542) для обсуждения альтернативных подходов к локальному кэшу Redis.</span><span class="sxs-lookup"><span data-stu-id="b7c84-162">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/19542) for a discussion on alternative approaches to a local Redis cache.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="b7c84-163">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="b7c84-163">Distributed NCache Cache</span></span>

<span data-ttu-id="b7c84-164">[NCache](https://github.com/Alachisoft/NCache) — это распределенный кэш в памяти с открытым исходным кодом, разработанный изначально в .NET и .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7c84-164">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="b7c84-165">NCache работает как локально, так и настроенный как кластер распределенного кэша для ASP.NET Core приложения, работающего в Azure или на других платформах размещения.</span><span class="sxs-lookup"><span data-stu-id="b7c84-165">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="b7c84-166">Сведения об установке и настройке NCache на локальном компьютере см. в разделе [Начало работы Guide для Windows (.NET и .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="b7c84-166">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="b7c84-167">Чтобы настроить NCache, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b7c84-167">To configure NCache:</span></span>

1. <span data-ttu-id="b7c84-168">Установите [NuGet с открытым исходным кодом NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="b7c84-168">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="b7c84-169">Настройте кластер кэша в [Client. нкконф](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="b7c84-169">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="b7c84-170">Добавьте в `Startup.ConfigureServices` следующий код:</span><span class="sxs-lookup"><span data-stu-id="b7c84-170">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="b7c84-171">Использование распределенного кэша</span><span class="sxs-lookup"><span data-stu-id="b7c84-171">Use the distributed cache</span></span>

<span data-ttu-id="b7c84-172">Чтобы использовать <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейс, запросите экземпляр <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> из любого конструктора в приложении.</span><span class="sxs-lookup"><span data-stu-id="b7c84-172">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="b7c84-173">Экземпляр предоставляется путем [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b7c84-173">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b7c84-174">При запуске примера приложения <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> внедряется в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b7c84-174">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="b7c84-175">Текущее время кэшируется с помощью <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (Дополнительные сведения см. в разделе [универсальный узел: ихостаппликатионлифетиме](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="b7c84-175">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="b7c84-176">Пример приложения внедряет <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в объект `IndexModel` для использования на странице индекса.</span><span class="sxs-lookup"><span data-stu-id="b7c84-176">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="b7c84-177">При каждой загрузке страницы индекса кэш проверяется на наличие кэшированного времени в `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="b7c84-177">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="b7c84-178">Если время кэширования не истекло, отображается время.</span><span class="sxs-lookup"><span data-stu-id="b7c84-178">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="b7c84-179">Если прошло 20 секунд с момента последнего обращения к кэшированному времени (время последней загрузки этой страницы), на странице отображается *время ожидания кэширования*.</span><span class="sxs-lookup"><span data-stu-id="b7c84-179">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="b7c84-180">Немедленно обновите кэшированное время на текущее время, нажав кнопку **сбросить кэшированное** время.</span><span class="sxs-lookup"><span data-stu-id="b7c84-180">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="b7c84-181">Кнопка запускает `OnPostResetCachedTime` метод обработчика.</span><span class="sxs-lookup"><span data-stu-id="b7c84-181">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="b7c84-182">Время жизни экземпляров <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (по крайней мере, для встроенных реализаций) не обязательно должно быть ограничено одним объектом или блоком.</span><span class="sxs-lookup"><span data-stu-id="b7c84-182">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="b7c84-183">Кроме того <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , экземпляр можно создать везде, где вам может понадобиться, а не использовать di, но создание экземпляра в коде может усложнить тестирование и нарушает [принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="b7c84-183">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="b7c84-184">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="b7c84-184">Recommendations</span></span>

<span data-ttu-id="b7c84-185">При принятии решения о том, какая реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> лучше подходит для вашего приложения, учитывайте следующее.</span><span class="sxs-lookup"><span data-stu-id="b7c84-185">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="b7c84-186">Существующая инфраструктура</span><span class="sxs-lookup"><span data-stu-id="b7c84-186">Existing infrastructure</span></span>
* <span data-ttu-id="b7c84-187">Требования к производительности</span><span class="sxs-lookup"><span data-stu-id="b7c84-187">Performance requirements</span></span>
* <span data-ttu-id="b7c84-188">Стоимость</span><span class="sxs-lookup"><span data-stu-id="b7c84-188">Cost</span></span>
* <span data-ttu-id="b7c84-189">Опыт работы в группе</span><span class="sxs-lookup"><span data-stu-id="b7c84-189">Team experience</span></span>

<span data-ttu-id="b7c84-190">Для обеспечения быстрого извлечения кэшированных данных решения кэширования обычно используют хранилище в памяти, но память является ограниченным ресурсом и может быть расширена.</span><span class="sxs-lookup"><span data-stu-id="b7c84-190">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="b7c84-191">Хранение часто используемых данных в кэше.</span><span class="sxs-lookup"><span data-stu-id="b7c84-191">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="b7c84-192">Как правило, кэш Redis обеспечивает более высокую пропускную способность и меньшую задержку, чем кэш SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b7c84-192">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="b7c84-193">Однако для определения характеристик производительности стратегий кэширования обычно требуется тестирование производительности.</span><span class="sxs-lookup"><span data-stu-id="b7c84-193">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="b7c84-194">Если SQL Server используется в качестве резервного хранилища распределенного кэша, использование той же базы данных для кэша и обычного хранилища данных и извлечения приложения может негативно сказаться на производительности обоих.</span><span class="sxs-lookup"><span data-stu-id="b7c84-194">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="b7c84-195">Рекомендуется использовать выделенный экземпляр SQL Server для резервного хранилища распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="b7c84-195">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7c84-196">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b7c84-196">Additional resources</span></span>

* [<span data-ttu-id="b7c84-197">Кэш Redis в Azure</span><span class="sxs-lookup"><span data-stu-id="b7c84-197">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="b7c84-198">База данных SQL в Azure</span><span class="sxs-lookup"><span data-stu-id="b7c84-198">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="b7c84-199">[Поставщик ASP.NET Core IDistributedCache для NCache в веб-фермах](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на сайте GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="b7c84-199">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b7c84-200">Распределенный кэш — это кэш, совместно используемый несколькими серверами приложений, обычно поддерживаемый как внешняя служба для серверов приложений, обращающихся к ней.</span><span class="sxs-lookup"><span data-stu-id="b7c84-200">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="b7c84-201">Распределенный кэш может повысить производительность и масштабируемость приложения ASP.NET Core, особенно если приложение размещено в облачной службе или ферме серверов.</span><span class="sxs-lookup"><span data-stu-id="b7c84-201">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="b7c84-202">Распределенный кэш имеет несколько преимуществ по сравнению с другими сценариями кэширования, в которых кэшированные данные хранятся на отдельных серверах приложений.</span><span class="sxs-lookup"><span data-stu-id="b7c84-202">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="b7c84-203">При распределении кэшированных данных данные:</span><span class="sxs-lookup"><span data-stu-id="b7c84-203">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="b7c84-204">—  Согласованность между запросами к нескольким серверам.</span><span class="sxs-lookup"><span data-stu-id="b7c84-204">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="b7c84-205">Выдерживает перезапуски сервера и развертывание приложений.</span><span class="sxs-lookup"><span data-stu-id="b7c84-205">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="b7c84-206">Не использует локальную память.</span><span class="sxs-lookup"><span data-stu-id="b7c84-206">Doesn't use local memory.</span></span>

<span data-ttu-id="b7c84-207">Конфигурация распределенного кэша зависит от конкретной реализации.</span><span class="sxs-lookup"><span data-stu-id="b7c84-207">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="b7c84-208">В этой статье описывается настройка распределенных кэшей SQL Server и Redis.</span><span class="sxs-lookup"><span data-stu-id="b7c84-208">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="b7c84-209">Также доступны реализации сторонних производителей, например [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="b7c84-209">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="b7c84-210">Независимо от выбранной реализации приложение взаимодействует с кэшем с помощью <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b7c84-210">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="b7c84-211">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7c84-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7c84-212">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="b7c84-212">Prerequisites</span></span>

<span data-ttu-id="b7c84-213">Чтобы использовать SQL Server распределенный кэш, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="b7c84-213">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b7c84-214">Чтобы использовать распределенный кэш Redis, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) и добавьте ссылку на пакет [Microsoft. Extensions. Caching. стаккексчанжередис](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="b7c84-214">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="b7c84-215">Пакет Redis не входит в `Microsoft.AspNetCore.App` пакет, поэтому необходимо отдельно ссылаться на пакет Redis в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="b7c84-215">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="b7c84-216">Чтобы использовать распределенный кэш NCache, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) и добавьте ссылку на пакет [NCache. Microsoft. Extensions. Caching. конвертер](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="b7c84-216">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="b7c84-217">Пакет NCache не входит в `Microsoft.AspNetCore.App` пакет, поэтому необходимо отдельно ссылаться на пакет NCache в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="b7c84-217">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="b7c84-218">Интерфейс IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="b7c84-218">IDistributedCache interface</span></span>

<span data-ttu-id="b7c84-219"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Интерфейс предоставляет следующие методы для управления элементами в реализации распределенного кэша:</span><span class="sxs-lookup"><span data-stu-id="b7c84-219">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="b7c84-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Принимает строковый ключ и получает кэшированный элемент в виде `byte[]` массива, если он найден в кэше.</span><span class="sxs-lookup"><span data-stu-id="b7c84-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="b7c84-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Добавляет элемент (в виде `byte[]` массива) в кэш с помощью строкового ключа.</span><span class="sxs-lookup"><span data-stu-id="b7c84-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="b7c84-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Обновляет элемент в кэше на основе его ключа, передавая время ожидания скользящего срока действия (если оно есть).</span><span class="sxs-lookup"><span data-stu-id="b7c84-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="b7c84-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Удаляет элемент кэша на основе ключа строки.</span><span class="sxs-lookup"><span data-stu-id="b7c84-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="b7c84-224">Установка служб распределенного кэширования</span><span class="sxs-lookup"><span data-stu-id="b7c84-224">Establish distributed caching services</span></span>

<span data-ttu-id="b7c84-225">Зарегистрируйте реализацию <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b7c84-225">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b7c84-226">Реализации, предоставляемые платформой, описанные в этом разделе, включают:</span><span class="sxs-lookup"><span data-stu-id="b7c84-226">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="b7c84-227">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="b7c84-227">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="b7c84-228">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="b7c84-228">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="b7c84-229">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="b7c84-229">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="b7c84-230">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="b7c84-230">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="b7c84-231">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="b7c84-231">Distributed Memory Cache</span></span>

<span data-ttu-id="b7c84-232">Кэш распределенной памяти ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) — это предоставляемая платформой реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , которая хранит элементы в памяти.</span><span class="sxs-lookup"><span data-stu-id="b7c84-232">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="b7c84-233">Кэш распределенной памяти не является фактическим распределенным кэшем.</span><span class="sxs-lookup"><span data-stu-id="b7c84-233">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="b7c84-234">Кэшированные элементы хранятся в экземпляре приложения на сервере, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="b7c84-234">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="b7c84-235">Кэш распределенной памяти — это полезная реализация:</span><span class="sxs-lookup"><span data-stu-id="b7c84-235">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="b7c84-236">В сценариях разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="b7c84-236">In development and testing scenarios.</span></span>
* <span data-ttu-id="b7c84-237">Если в рабочей среде используется один сервер, а использование памяти не является проблемой.</span><span class="sxs-lookup"><span data-stu-id="b7c84-237">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="b7c84-238">Реализация кэша распределенной памяти абстрагирует хранилище кэшированных данных.</span><span class="sxs-lookup"><span data-stu-id="b7c84-238">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="b7c84-239">Она позволяет реализовать истинное решение распределенного кэширования в будущем, если потребуется несколько узлов или отказоустойчивость.</span><span class="sxs-lookup"><span data-stu-id="b7c84-239">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="b7c84-240">Пример приложения использует распределенный кэш памяти при запуске приложения в среде разработки в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b7c84-240">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="b7c84-241">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="b7c84-241">Distributed SQL Server Cache</span></span>

<span data-ttu-id="b7c84-242">Реализация кэша распределенного SQL Server ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) позволяет распределенному кэшу использовать базу данных SQL Server в качестве резервного хранилища.</span><span class="sxs-lookup"><span data-stu-id="b7c84-242">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="b7c84-243">Чтобы создать SQL Server таблицу кэшированных элементов в экземпляре SQL Server, можно использовать `sql-cache` средство.</span><span class="sxs-lookup"><span data-stu-id="b7c84-243">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="b7c84-244">Средство создает таблицу с указанными именем и схемой.</span><span class="sxs-lookup"><span data-stu-id="b7c84-244">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="b7c84-245">Создайте таблицу в SQL Server, выполнив `sql-cache create` команду.</span><span class="sxs-lookup"><span data-stu-id="b7c84-245">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="b7c84-246">Укажите SQL Server экземпляр ( `Data Source` ), базу данных ( `Initial Catalog` ), схему (например, `dbo` ) и имя таблицы (например, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="b7c84-246">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="b7c84-247">В журнал заносится сообщение, указывающее, что средство прошло успешно:</span><span class="sxs-lookup"><span data-stu-id="b7c84-247">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="b7c84-248">Таблица, созданная `sql-cache` средством, имеет следующую схему:</span><span class="sxs-lookup"><span data-stu-id="b7c84-248">The table created by the `sql-cache` tool has the following schema:</span></span>

![Таблица кэша SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="b7c84-250">Приложение должно манипулировать значениями кэша с помощью экземпляра, а <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> не <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="b7c84-250">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="b7c84-251">Пример приложения реализует <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> в среде, не являющейся средой разработки, в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b7c84-251">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="b7c84-252"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(И, при необходимости, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> и <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) обычно хранятся вне системы управления версиями (например, хранится в [диспетчере секретов](xref:security/app-secrets) или в *appsettings.json* / *appSettings. { Файлы среды}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="b7c84-252">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="b7c84-253">Строка подключения может содержать учетные данные, которые должны содержаться в системе управления версиями.</span><span class="sxs-lookup"><span data-stu-id="b7c84-253">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="b7c84-254">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="b7c84-254">Distributed Redis Cache</span></span>

<span data-ttu-id="b7c84-255">[Redis](https://redis.io/) -это хранилище данных в памяти с открытым исходным кодом, которое часто используется в качестве распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="b7c84-255">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="b7c84-256">Вы можете использовать Redis локально, и вы можете настроить [кэш Redis для Azure](https://azure.microsoft.com/services/cache/) для приложения ASP.NET Core, размещенного в Azure.</span><span class="sxs-lookup"><span data-stu-id="b7c84-256">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="b7c84-257">Приложение настраивает реализацию кэша с помощью <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> экземпляра ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) в среде, не являющейся средой разработки, в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b7c84-257">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="b7c84-258">Чтобы установить Redis на локальном компьютере, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b7c84-258">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="b7c84-259">Установите [пакет шоколадного Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="b7c84-259">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="b7c84-260">Запустите `redis-server` из командной строки.</span><span class="sxs-lookup"><span data-stu-id="b7c84-260">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="b7c84-261">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="b7c84-261">Distributed NCache Cache</span></span>

<span data-ttu-id="b7c84-262">[NCache](https://github.com/Alachisoft/NCache) — это распределенный кэш в памяти с открытым исходным кодом, разработанный изначально в .NET и .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7c84-262">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="b7c84-263">NCache работает как локально, так и настроенный как кластер распределенного кэша для ASP.NET Core приложения, работающего в Azure или на других платформах размещения.</span><span class="sxs-lookup"><span data-stu-id="b7c84-263">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="b7c84-264">Сведения об установке и настройке NCache на локальном компьютере см. в разделе [Начало работы Guide для Windows (.NET и .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="b7c84-264">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="b7c84-265">Чтобы настроить NCache, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b7c84-265">To configure NCache:</span></span>

1. <span data-ttu-id="b7c84-266">Установите [NuGet с открытым исходным кодом NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="b7c84-266">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="b7c84-267">Настройте кластер кэша в [Client. нкконф](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="b7c84-267">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="b7c84-268">Добавьте в `Startup.ConfigureServices` следующий код:</span><span class="sxs-lookup"><span data-stu-id="b7c84-268">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="b7c84-269">Использование распределенного кэша</span><span class="sxs-lookup"><span data-stu-id="b7c84-269">Use the distributed cache</span></span>

<span data-ttu-id="b7c84-270">Чтобы использовать <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейс, запросите экземпляр <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> из любого конструктора в приложении.</span><span class="sxs-lookup"><span data-stu-id="b7c84-270">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="b7c84-271">Экземпляр предоставляется путем [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b7c84-271">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b7c84-272">При запуске примера приложения <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> внедряется в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b7c84-272">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="b7c84-273">Текущее время кэшируется с помощью <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Дополнительные сведения см. в разделе [веб-узел: иаппликатионлифетиме Interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="b7c84-273">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="b7c84-274">Пример приложения внедряет <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в объект `IndexModel` для использования на странице индекса.</span><span class="sxs-lookup"><span data-stu-id="b7c84-274">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="b7c84-275">При каждой загрузке страницы индекса кэш проверяется на наличие кэшированного времени в `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="b7c84-275">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="b7c84-276">Если время кэширования не истекло, отображается время.</span><span class="sxs-lookup"><span data-stu-id="b7c84-276">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="b7c84-277">Если прошло 20 секунд с момента последнего обращения к кэшированному времени (время последней загрузки этой страницы), на странице отображается *время ожидания кэширования*.</span><span class="sxs-lookup"><span data-stu-id="b7c84-277">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="b7c84-278">Немедленно обновите кэшированное время на текущее время, нажав кнопку **сбросить кэшированное** время.</span><span class="sxs-lookup"><span data-stu-id="b7c84-278">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="b7c84-279">Кнопка запускает `OnPostResetCachedTime` метод обработчика.</span><span class="sxs-lookup"><span data-stu-id="b7c84-279">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="b7c84-280">Время жизни экземпляров <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (по крайней мере, для встроенных реализаций) не обязательно должно быть ограничено одним объектом или блоком.</span><span class="sxs-lookup"><span data-stu-id="b7c84-280">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="b7c84-281">Кроме того <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , экземпляр можно создать везде, где вам может понадобиться, а не использовать di, но создание экземпляра в коде может усложнить тестирование и нарушает [принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="b7c84-281">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="b7c84-282">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="b7c84-282">Recommendations</span></span>

<span data-ttu-id="b7c84-283">При принятии решения о том, какая реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> лучше подходит для вашего приложения, учитывайте следующее.</span><span class="sxs-lookup"><span data-stu-id="b7c84-283">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="b7c84-284">Существующая инфраструктура</span><span class="sxs-lookup"><span data-stu-id="b7c84-284">Existing infrastructure</span></span>
* <span data-ttu-id="b7c84-285">Требования к производительности</span><span class="sxs-lookup"><span data-stu-id="b7c84-285">Performance requirements</span></span>
* <span data-ttu-id="b7c84-286">Стоимость</span><span class="sxs-lookup"><span data-stu-id="b7c84-286">Cost</span></span>
* <span data-ttu-id="b7c84-287">Опыт работы в группе</span><span class="sxs-lookup"><span data-stu-id="b7c84-287">Team experience</span></span>

<span data-ttu-id="b7c84-288">Для обеспечения быстрого извлечения кэшированных данных решения кэширования обычно используют хранилище в памяти, но память является ограниченным ресурсом и может быть расширена.</span><span class="sxs-lookup"><span data-stu-id="b7c84-288">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="b7c84-289">Хранение часто используемых данных в кэше.</span><span class="sxs-lookup"><span data-stu-id="b7c84-289">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="b7c84-290">Как правило, кэш Redis обеспечивает более высокую пропускную способность и меньшую задержку, чем кэш SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b7c84-290">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="b7c84-291">Однако для определения характеристик производительности стратегий кэширования обычно требуется тестирование производительности.</span><span class="sxs-lookup"><span data-stu-id="b7c84-291">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="b7c84-292">Если SQL Server используется в качестве резервного хранилища распределенного кэша, использование той же базы данных для кэша и обычного хранилища данных и извлечения приложения может негативно сказаться на производительности обоих.</span><span class="sxs-lookup"><span data-stu-id="b7c84-292">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="b7c84-293">Рекомендуется использовать выделенный экземпляр SQL Server для резервного хранилища распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="b7c84-293">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7c84-294">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b7c84-294">Additional resources</span></span>

* [<span data-ttu-id="b7c84-295">Кэш Redis в Azure</span><span class="sxs-lookup"><span data-stu-id="b7c84-295">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="b7c84-296">База данных SQL в Azure</span><span class="sxs-lookup"><span data-stu-id="b7c84-296">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="b7c84-297">[Поставщик ASP.NET Core IDistributedCache для NCache в веб-фермах](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на сайте GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="b7c84-297">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b7c84-298">Распределенный кэш — это кэш, совместно используемый несколькими серверами приложений, обычно поддерживаемый как внешняя служба для серверов приложений, обращающихся к ней.</span><span class="sxs-lookup"><span data-stu-id="b7c84-298">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="b7c84-299">Распределенный кэш может повысить производительность и масштабируемость приложения ASP.NET Core, особенно если приложение размещено в облачной службе или ферме серверов.</span><span class="sxs-lookup"><span data-stu-id="b7c84-299">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="b7c84-300">Распределенный кэш имеет несколько преимуществ по сравнению с другими сценариями кэширования, в которых кэшированные данные хранятся на отдельных серверах приложений.</span><span class="sxs-lookup"><span data-stu-id="b7c84-300">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="b7c84-301">При распределении кэшированных данных данные:</span><span class="sxs-lookup"><span data-stu-id="b7c84-301">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="b7c84-302">—  Согласованность между запросами к нескольким серверам.</span><span class="sxs-lookup"><span data-stu-id="b7c84-302">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="b7c84-303">Выдерживает перезапуски сервера и развертывание приложений.</span><span class="sxs-lookup"><span data-stu-id="b7c84-303">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="b7c84-304">Не использует локальную память.</span><span class="sxs-lookup"><span data-stu-id="b7c84-304">Doesn't use local memory.</span></span>

<span data-ttu-id="b7c84-305">Конфигурация распределенного кэша зависит от конкретной реализации.</span><span class="sxs-lookup"><span data-stu-id="b7c84-305">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="b7c84-306">В этой статье описывается настройка распределенных кэшей SQL Server и Redis.</span><span class="sxs-lookup"><span data-stu-id="b7c84-306">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="b7c84-307">Также доступны реализации сторонних производителей, например [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="b7c84-307">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="b7c84-308">Независимо от выбранной реализации приложение взаимодействует с кэшем с помощью <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b7c84-308">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="b7c84-309">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7c84-309">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7c84-310">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="b7c84-310">Prerequisites</span></span>

<span data-ttu-id="b7c84-311">Чтобы использовать SQL Server распределенный кэш, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="b7c84-311">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b7c84-312">Чтобы использовать распределенный кэш Redis, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) и добавьте ссылку на пакет [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="b7c84-312">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="b7c84-313">Пакет Redis не входит в `Microsoft.AspNetCore.App` пакет, поэтому необходимо отдельно ссылаться на пакет Redis в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="b7c84-313">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="b7c84-314">Чтобы использовать распределенный кэш NCache, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) и добавьте ссылку на пакет [NCache. Microsoft. Extensions. Caching. конвертер](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="b7c84-314">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="b7c84-315">Пакет NCache не входит в `Microsoft.AspNetCore.App` пакет, поэтому необходимо отдельно ссылаться на пакет NCache в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="b7c84-315">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="b7c84-316">Интерфейс IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="b7c84-316">IDistributedCache interface</span></span>

<span data-ttu-id="b7c84-317"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Интерфейс предоставляет следующие методы для управления элементами в реализации распределенного кэша:</span><span class="sxs-lookup"><span data-stu-id="b7c84-317">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="b7c84-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Принимает строковый ключ и получает кэшированный элемент в виде `byte[]` массива, если он найден в кэше.</span><span class="sxs-lookup"><span data-stu-id="b7c84-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="b7c84-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Добавляет элемент (в виде `byte[]` массива) в кэш с помощью строкового ключа.</span><span class="sxs-lookup"><span data-stu-id="b7c84-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="b7c84-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Обновляет элемент в кэше на основе его ключа, передавая время ожидания скользящего срока действия (если оно есть).</span><span class="sxs-lookup"><span data-stu-id="b7c84-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="b7c84-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Удаляет элемент кэша на основе ключа строки.</span><span class="sxs-lookup"><span data-stu-id="b7c84-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="b7c84-322">Установка служб распределенного кэширования</span><span class="sxs-lookup"><span data-stu-id="b7c84-322">Establish distributed caching services</span></span>

<span data-ttu-id="b7c84-323">Зарегистрируйте реализацию <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b7c84-323">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b7c84-324">Реализации, предоставляемые платформой, описанные в этом разделе, включают:</span><span class="sxs-lookup"><span data-stu-id="b7c84-324">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="b7c84-325">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="b7c84-325">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="b7c84-326">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="b7c84-326">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="b7c84-327">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="b7c84-327">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="b7c84-328">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="b7c84-328">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="b7c84-329">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="b7c84-329">Distributed Memory Cache</span></span>

<span data-ttu-id="b7c84-330">Кэш распределенной памяти ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) — это предоставляемая платформой реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , которая хранит элементы в памяти.</span><span class="sxs-lookup"><span data-stu-id="b7c84-330">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="b7c84-331">Кэш распределенной памяти не является фактическим распределенным кэшем.</span><span class="sxs-lookup"><span data-stu-id="b7c84-331">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="b7c84-332">Кэшированные элементы хранятся в экземпляре приложения на сервере, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="b7c84-332">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="b7c84-333">Кэш распределенной памяти — это полезная реализация:</span><span class="sxs-lookup"><span data-stu-id="b7c84-333">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="b7c84-334">В сценариях разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="b7c84-334">In development and testing scenarios.</span></span>
* <span data-ttu-id="b7c84-335">Если в рабочей среде используется один сервер, а использование памяти не является проблемой.</span><span class="sxs-lookup"><span data-stu-id="b7c84-335">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="b7c84-336">Реализация кэша распределенной памяти абстрагирует хранилище кэшированных данных.</span><span class="sxs-lookup"><span data-stu-id="b7c84-336">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="b7c84-337">Она позволяет реализовать истинное решение распределенного кэширования в будущем, если потребуется несколько узлов или отказоустойчивость.</span><span class="sxs-lookup"><span data-stu-id="b7c84-337">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="b7c84-338">Пример приложения использует распределенный кэш памяти при запуске приложения в среде разработки в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b7c84-338">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="b7c84-339">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="b7c84-339">Distributed SQL Server Cache</span></span>

<span data-ttu-id="b7c84-340">Реализация кэша распределенного SQL Server ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) позволяет распределенному кэшу использовать базу данных SQL Server в качестве резервного хранилища.</span><span class="sxs-lookup"><span data-stu-id="b7c84-340">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="b7c84-341">Чтобы создать SQL Server таблицу кэшированных элементов в экземпляре SQL Server, можно использовать `sql-cache` средство.</span><span class="sxs-lookup"><span data-stu-id="b7c84-341">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="b7c84-342">Средство создает таблицу с указанными именем и схемой.</span><span class="sxs-lookup"><span data-stu-id="b7c84-342">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="b7c84-343">Создайте таблицу в SQL Server, выполнив `sql-cache create` команду.</span><span class="sxs-lookup"><span data-stu-id="b7c84-343">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="b7c84-344">Укажите SQL Server экземпляр ( `Data Source` ), базу данных ( `Initial Catalog` ), схему (например, `dbo` ) и имя таблицы (например, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="b7c84-344">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="b7c84-345">В журнал заносится сообщение, указывающее, что средство прошло успешно:</span><span class="sxs-lookup"><span data-stu-id="b7c84-345">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="b7c84-346">Таблица, созданная `sql-cache` средством, имеет следующую схему:</span><span class="sxs-lookup"><span data-stu-id="b7c84-346">The table created by the `sql-cache` tool has the following schema:</span></span>

![Таблица кэша SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="b7c84-348">Приложение должно манипулировать значениями кэша с помощью экземпляра, а <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> не <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="b7c84-348">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="b7c84-349">Пример приложения реализует <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> в среде, не являющейся средой разработки, в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b7c84-349">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="b7c84-350"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(И, при необходимости, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> и <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) обычно хранятся вне системы управления версиями (например, хранится в [диспетчере секретов](xref:security/app-secrets) или в *appsettings.json* / *appSettings. { Файлы среды}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="b7c84-350">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="b7c84-351">Строка подключения может содержать учетные данные, которые должны содержаться в системе управления версиями.</span><span class="sxs-lookup"><span data-stu-id="b7c84-351">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="b7c84-352">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="b7c84-352">Distributed Redis Cache</span></span>

<span data-ttu-id="b7c84-353">[Redis](https://redis.io/) -это хранилище данных в памяти с открытым исходным кодом, которое часто используется в качестве распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="b7c84-353">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="b7c84-354">Вы можете использовать Redis локально, и вы можете настроить [кэш Redis для Azure](https://azure.microsoft.com/services/cache/) для приложения ASP.NET Core, размещенного в Azure.</span><span class="sxs-lookup"><span data-stu-id="b7c84-354">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="b7c84-355">Приложение настраивает реализацию кэша с помощью <xref:Microsoft.Extensions.Caching.Redis.RedisCache> экземпляра ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ):</span><span class="sxs-lookup"><span data-stu-id="b7c84-355">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="b7c84-356">Чтобы установить Redis на локальном компьютере, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b7c84-356">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="b7c84-357">Установите [пакет шоколадного Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="b7c84-357">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="b7c84-358">Запустите `redis-server` из командной строки.</span><span class="sxs-lookup"><span data-stu-id="b7c84-358">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="b7c84-359">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="b7c84-359">Distributed NCache Cache</span></span>

<span data-ttu-id="b7c84-360">[NCache](https://github.com/Alachisoft/NCache) — это распределенный кэш в памяти с открытым исходным кодом, разработанный изначально в .NET и .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7c84-360">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="b7c84-361">NCache работает как локально, так и настроенный как кластер распределенного кэша для ASP.NET Core приложения, работающего в Azure или на других платформах размещения.</span><span class="sxs-lookup"><span data-stu-id="b7c84-361">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="b7c84-362">Сведения об установке и настройке NCache на локальном компьютере см. в разделе [Начало работы Guide для Windows (.NET и .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="b7c84-362">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="b7c84-363">Чтобы настроить NCache, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b7c84-363">To configure NCache:</span></span>

1. <span data-ttu-id="b7c84-364">Установите [NuGet с открытым исходным кодом NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="b7c84-364">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="b7c84-365">Настройте кластер кэша в [Client. нкконф](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="b7c84-365">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="b7c84-366">Добавьте в `Startup.ConfigureServices` следующий код:</span><span class="sxs-lookup"><span data-stu-id="b7c84-366">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="b7c84-367">Использование распределенного кэша</span><span class="sxs-lookup"><span data-stu-id="b7c84-367">Use the distributed cache</span></span>

<span data-ttu-id="b7c84-368">Чтобы использовать <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейс, запросите экземпляр <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> из любого конструктора в приложении.</span><span class="sxs-lookup"><span data-stu-id="b7c84-368">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="b7c84-369">Экземпляр предоставляется путем [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b7c84-369">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b7c84-370">При запуске примера приложения <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> внедряется в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b7c84-370">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="b7c84-371">Текущее время кэшируется с помощью <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Дополнительные сведения см. в разделе [веб-узел: иаппликатионлифетиме Interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="b7c84-371">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="b7c84-372">Пример приложения внедряет <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в объект `IndexModel` для использования на странице индекса.</span><span class="sxs-lookup"><span data-stu-id="b7c84-372">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="b7c84-373">При каждой загрузке страницы индекса кэш проверяется на наличие кэшированного времени в `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="b7c84-373">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="b7c84-374">Если время кэширования не истекло, отображается время.</span><span class="sxs-lookup"><span data-stu-id="b7c84-374">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="b7c84-375">Если прошло 20 секунд с момента последнего обращения к кэшированному времени (время последней загрузки этой страницы), на странице отображается *время ожидания кэширования*.</span><span class="sxs-lookup"><span data-stu-id="b7c84-375">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="b7c84-376">Немедленно обновите кэшированное время на текущее время, нажав кнопку **сбросить кэшированное** время.</span><span class="sxs-lookup"><span data-stu-id="b7c84-376">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="b7c84-377">Кнопка запускает `OnPostResetCachedTime` метод обработчика.</span><span class="sxs-lookup"><span data-stu-id="b7c84-377">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="b7c84-378">Время жизни экземпляров <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (по крайней мере, для встроенных реализаций) не обязательно должно быть ограничено одним объектом или блоком.</span><span class="sxs-lookup"><span data-stu-id="b7c84-378">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="b7c84-379">Кроме того <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , экземпляр можно создать везде, где вам может понадобиться, а не использовать di, но создание экземпляра в коде может усложнить тестирование и нарушает [принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="b7c84-379">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="b7c84-380">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="b7c84-380">Recommendations</span></span>

<span data-ttu-id="b7c84-381">При принятии решения о том, какая реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> лучше подходит для вашего приложения, учитывайте следующее.</span><span class="sxs-lookup"><span data-stu-id="b7c84-381">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="b7c84-382">Существующая инфраструктура</span><span class="sxs-lookup"><span data-stu-id="b7c84-382">Existing infrastructure</span></span>
* <span data-ttu-id="b7c84-383">Требования к производительности</span><span class="sxs-lookup"><span data-stu-id="b7c84-383">Performance requirements</span></span>
* <span data-ttu-id="b7c84-384">Стоимость</span><span class="sxs-lookup"><span data-stu-id="b7c84-384">Cost</span></span>
* <span data-ttu-id="b7c84-385">Опыт работы в группе</span><span class="sxs-lookup"><span data-stu-id="b7c84-385">Team experience</span></span>

<span data-ttu-id="b7c84-386">Для обеспечения быстрого извлечения кэшированных данных решения кэширования обычно используют хранилище в памяти, но память является ограниченным ресурсом и может быть расширена.</span><span class="sxs-lookup"><span data-stu-id="b7c84-386">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="b7c84-387">Хранение часто используемых данных в кэше.</span><span class="sxs-lookup"><span data-stu-id="b7c84-387">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="b7c84-388">Как правило, кэш Redis обеспечивает более высокую пропускную способность и меньшую задержку, чем кэш SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b7c84-388">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="b7c84-389">Однако для определения характеристик производительности стратегий кэширования обычно требуется тестирование производительности.</span><span class="sxs-lookup"><span data-stu-id="b7c84-389">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="b7c84-390">Если SQL Server используется в качестве резервного хранилища распределенного кэша, использование той же базы данных для кэша и обычного хранилища данных и извлечения приложения может негативно сказаться на производительности обоих.</span><span class="sxs-lookup"><span data-stu-id="b7c84-390">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="b7c84-391">Рекомендуется использовать выделенный экземпляр SQL Server для резервного хранилища распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="b7c84-391">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7c84-392">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b7c84-392">Additional resources</span></span>

* [<span data-ttu-id="b7c84-393">Кэш Redis в Azure</span><span class="sxs-lookup"><span data-stu-id="b7c84-393">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="b7c84-394">База данных SQL в Azure</span><span class="sxs-lookup"><span data-stu-id="b7c84-394">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="b7c84-395">[Поставщик ASP.NET Core IDistributedCache для NCache в веб-фермах](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на сайте GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="b7c84-395">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
