---
title: Кэширование в памяти в ASP.NET Core
author: rick-anderson
description: Узнайте, как кэшировать данные в памяти в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
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
uid: performance/caching/memory
ms.openlocfilehash: d71678aeee9b3fca717129a2fbed1f75b593e010
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586310"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Кэширование в памяти в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Джон Луо](https://github.com/JunTaoLuo)и [Стив Смит](https://ardalis.com/)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/memory/3.0sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Основы кэширования

Кэширование может значительно повысить производительность и масштабируемость приложения, уменьшая объем работы, необходимый для создания содержимого. Кэширование лучше всего работает с данными, которые изменяются редко **и** требуют больших затрат. Кэширование создает копию данных, которая может быть возвращена гораздо быстрее, чем из источника. Приложения должны быть написаны и протестированы, чтобы **никогда не** зависеть от кэшированных данных.

ASP.NET Core поддерживает несколько разных кэшей. Простейший кэш основан на [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache). `IMemoryCache` Представляет кэш, хранящийся в памяти веб-сервера. Приложения, работающие на ферме серверов (несколько серверов), должны обеспечивать работу сеансов при использовании кэша в памяти. Закрепленные сеансы гарантируют, что последующие запросы от клиента отправляются на один и тот же сервер. Например, веб-приложения Azure используют [маршрутизацию запросов приложений](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) для маршрутизации всех последующих запросов к тому же серверу.

Для неприкрепленных сеансов в веб-ферме требуется [распределенный кэш](distributed.md) , чтобы избежать проблем с согласованностью кэша. Для некоторых приложений распределенный кэш может поддерживать более высокие возможности масштабирования, чем кэш в памяти. Использование распределенного кэша разгружает кэш-память во внешний процесс.

Кэш в памяти может хранить любой объект. Интерфейс распределенного кэша ограничен `byte[]` . Элементы кэша в памяти и распределенного кэша хранятся в виде пар "ключ-значение".

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Пакет NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) можно использовать с:

* .NET Standard 2,0 или более поздней версии.
* Любая [реализация .NET](/dotnet/standard/net-standard#net-implementation-support) , предназначенная для .NET Standard 2,0 или более поздней версии. Например, ASP.NET Core 2,0 или более поздней версии.
* .NET Framework 4.5 или более поздней версии.

[Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (описывается в этой статье) рекомендуется использовать, `System.Runtime.Caching` / `MemoryCache` так как он лучше интегрирован в ASP.NET Core. Например, `IMemoryCache` работает изначально с [внедрением зависимостей](xref:fundamentals/dependency-injection)ASP.NET Core.

Используйте `System.Runtime.Caching` / `MemoryCache` в качестве моста совместимости при переносе кода из ASP.NET 4. x в ASP.NET Core.

## <a name="cache-guidelines"></a>Рекомендации по кэшированию

* Код должен всегда иметь резервный вариант для выборки данных и **не** зависит от доступности кэшированного значения.
* Кэш использует неограниченный ресурс, память. Ограничение роста кэша:
  * **Не** используйте внешние входные данные в качестве ключей кэша.
  * Используйте срок действия, чтобы ограничить рост кэша.
  * [Используйте SetSize, Size и сизелимит для ограничения размера кэша](#use-setsize-size-and-sizelimit-to-limit-cache-size). Среда выполнения ASP.NET Core не **ограничивает размер** кэша на основе нехватки памяти. Разработчик может ограничить размер кэша.

## <a name="use-imemorycache"></a>Использование IMemoryCache

> [!WARNING]
> Использование кэша *общей* памяти из [внедрения зависимостей](xref:fundamentals/dependency-injection) и вызова `SetSize` , `Size` или `SizeLimit` для ограничения размера кэша может привести к сбою приложения. Если для кэша задано ограничение размера, все записи должны указывать размер при добавлении. Это может привести к проблемам, так как разработчики могут не иметь полного контроля над тем, что использует общий кэш. Например, Entity Framework Core использует общий кэш и не задает размер. Если приложение устанавливает ограничение размера кэша и использует EF Core, приложение создает исключение `InvalidOperationException` .
> При использовании `SetSize` , `Size` или `SizeLimit` для ограничения кэша создайте одноэлементный кэш для кэширования. Дополнительные сведения и пример см. в статьях [Использование SetSize, Size и сизелимит для ограничения размера кэша](#use-setsize-size-and-sizelimit-to-limit-cache-size).
> Общий кэш является общим для других платформ или библиотек. Например, EF Core использует общий кэш и не задает размер. 

Кэширование в памяти — это *Служба* , на которую ссылается приложение, использующее [внедрение зависимостей](xref:fundamentals/dependency-injection). Запросите `IMemoryCache` экземпляр в конструкторе:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

В следующем коде используется [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) для проверки, находится ли время в кэше. Если время не кэшировано, создается новая запись и добавляется в кэш с помощью [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_). `CacheKeys`Класс является частью примера загрузки.

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

Текущее время и кэшированное время отображаются:

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

Следующий код использует метод расширения [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_System_TimeSpan_) для кэширования данных в относительное время без создания `MemoryCacheEntryOptions` объекта.
[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_set)]

Кэшированное `DateTime` значение остается в кэше во время выполнения запросов в течение периода ожидания.

В следующем коде для кэширования данных используются [жеторкреате](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) и [жеторкреатеасинк](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) .

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Следующий код вызывает [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) для получения кэшированного времени:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

Следующий код возвращает или создает кэшированный элемент с абсолютным сроком действия:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

Набор кэшированных элементов с скользящим сроком действия может быть только устаревшим. Если доступ к нему осуществляется чаще, чем скользящий интервал срока действия, срок действия элемента никогда не истечет. Объедините скользящий срок действия с абсолютным сроком действия, чтобы гарантировать, что срок действия элемента истекает по истечении его абсолютного срока действия. Абсолютный срок действия задает верхнюю границу того, как долго элемент может быть кэширован, в то же время допуская окончания срока действия элемента, если он не запрашивался в течение скользящего интервала истечения. Если указан как абсолютный, так и скользящий срок действия, истечение срока действия логически ORed. Если либо скользящий интервал истечения срока действия, *либо* период абсолютного окончания срока действия, элемент удаляется из кэша.

Следующий код возвращает или создает кэшированный элемент со скользящим *и* абсолютным сроком действия:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

Приведенный выше код гарантирует, что данные не будут кэшироваться дольше, чем абсолютное время.

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>методы, и <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> являются методами расширения в <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> классе. Эти методы расширяют возможности <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .

## <a name="memorycacheentryoptions"></a>меморикачинтрйоптионс

Следующий пример:

* Задает скользящий срок действия. Запросы, обращающиеся к этому кэшированному элементу, будут сбрасывать скользящий срок действия.
* Задает приоритет кэша для [качеитемприорити. неверремове](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).
* Задает [постевиктионделегате](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , который будет вызываться после удаления записи из кэша. Обратный вызов выполняется в другом потоке из кода, который удаляет элемент из кэша.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Использование SetSize, Size и Сизелимит для ограничения размера кэша

`MemoryCache`Экземпляр может дополнительно задавать и применять ограничение размера. Ограничение размера кэша не имеет определенной единицы измерения, так как кэш не имеет механизма для измерения размера записей. Если установлен предел размера кэша, то для всех записей должен быть указан размер. Среда выполнения ASP.NET Core не ограничивает размер кэша на основе нехватки памяти. Разработчик может ограничить размер кэша. Указанный размер находится в единицах, которые выбирает разработчик.

Пример:

* Если веб-приложение в основном кэширует строки, каждый размер записи кэша может быть длиной строки.
* Приложение может указать размер всех записей как 1, а максимальный размер — число записей.

Если <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> значение не задано, кэш растет без привязки. Среда выполнения ASP.NET Core не обрезает кэш при нехватке системной памяти. Приложения должны быть спроектированы следующим образом:

* Ограничение роста кэша.
* Вызов <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> или <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> Если доступная память ограничена:

В следующем коде создается фиксированный размер независимого <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> от единицы, доступный при [внедрении зависимостей](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` не имеет единиц. Кэшированные записи должны указывать размер в любых единицах, которые они считаются наиболее подходящими, если установлен предел размера кэша. Все пользователи экземпляра кэша должны использовать одну и ту же систему единиц измерения. Запись не будет кэшироваться, если сумма кэшированных размеров записей превышает значение, заданное параметром `SizeLimit` . Если ограничение размера кэша не задано, размер кэша, заданный для записи, будет проигнорирован.

Следующий код регистрируется в `MyMemoryCache` контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

`MyMemoryCache` создается как независимый кэш памяти для компонентов, которые знают об этом ограниченном размере кэша и знают, как задать размер записи кэша соответствующим образом.

В следующем коде используется `MyMemoryCache` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

Размер записи кэша может быть задан <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> или <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> методами расширения:

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` пытается удалить указанный процент кэша в следующем порядке:

* Все элементы с истекшим сроком действия.
* Элементы по приоритету. Первыми удаляются элементы с наименьшим приоритетом.
* Наименее недавно использованные объекты.
* Элементы с самым ранним абсолютным сроком действия.
* Элементы с самой ранней скользящей истечением срока действия.

Закрепленные элементы с приоритетом <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> никогда не удаляются. Следующий код удаляет элемент кэша и вызывает `Compact` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Дополнительные сведения см. [в разделе Compact Source на сайте GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Зависимости кэша

В следующем примере показано, как истечет срок действия записи кэша, если истечет срок действия зависимой записи. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>Добавляется к кэшированному элементу. Если `Cancel` метод вызывается для `CancellationTokenSource` , то обе записи кэша будут вытеснены.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

Использование <xref:System.Threading.CancellationTokenSource> позволяет удалять несколько записей кэша как группу. Используя `using` шаблон в приведенном выше коде, записи кэша, созданные внутри `using` блока, будут наследовать параметры триггеров и срока действия.

## <a name="additional-notes"></a>Дополнительные сведения

* Истечение срока действия не происходит в фоновом режиме. Нет таймера, который активно проверяет кэш на наличие просроченных элементов. Любое действие в кэше ( `Get` , `Set` , `Remove` ) может активировать фоновое сканирование для элементов с истекшим сроком действия. Таймер на `CancellationTokenSource` ( <xref:System.Threading.CancellationTokenSource.CancelAfter*> ) также удаляет запись и активирует проверку для элементов с истекшим сроком действия. В следующем примере для зарегистрированного маркера используется [CancellationTokenSource (TimeSpan)](/dotnet/api/system.threading.cancellationtokensource.-ctor) . Когда этот маркер срабатывает, он немедленно удаляет запись и вызывает обратные вызовы вытеснения:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ae)]

* При использовании обратного вызова для повторного заполнения элемента кэша:

  * Несколько запросов могут найти значение кэшированного ключа пустым, так как обратный вызов не завершен.
  * Это может привести к повторному заполнению кэшированного элемента несколькими потоками.

* Если одна запись кэша используется для создания другой, дочерняя запись копирует маркеры истечения срока действия родительской записи и параметры срока действия, основанные на времени. Срок действия дочернего элемента не истек, удаление или обновление родительской записи вручную.

* Используется <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> для установки обратных вызовов, которые будут срабатывать после удаления записи кэша из кэша.
* Для большинства приложений `IMemoryCache` включен. Например, вызов метода `AddMvc` ,,, `AddControllersWithViews` `AddRazorPages` `AddMvcCore().AddRazorViewEngine` и многих других `Add{Service}` методов в `ConfigureServices` включает `IMemoryCache` . Для приложений, которые не вызывают один из вышеперечисленных `Add{Service}` методов, может потребоваться вызвать <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> в `ConfigureServices` .

## <a name="background-cache-update"></a>Фоновое обновление кэша

Используйте [фоновую службу](xref:fundamentals/host/hosted-services) , например, <xref:Microsoft.Extensions.Hosting.IHostedService> для обновления кэша. Фоновая служба может повторно вычислить записи и назначить их кэшу только в том случае, если они готовы.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Джон Луо](https://github.com/JunTaoLuo)и [Стив Смит](https://ardalis.com/)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/memory/sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Основы кэширования

Кэширование может значительно повысить производительность и масштабируемость приложения, уменьшая объем работы, необходимый для создания содержимого. Кэширование лучше всего работает с данными, которые изменяются редко. Кэширование создает копию данных, которая может быть возвращена гораздо быстрее, чем из исходного источника. Код должен быть написан и протестирован, чтобы **никогда не** зависеть от кэшированных данных.

ASP.NET Core поддерживает несколько разных кэшей. Простейший кэш основан на [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), который представляет кэш, хранящийся в памяти веб-сервера. Приложения, работающие в ферме серверов (несколько серверов), должны обеспечивать работу сеансов при использовании кэша в памяти. Закрепленные сеансы гарантируют, что последующие запросы от клиента поступают на один и тот же сервер. Например, веб-приложения Azure используют [маршрутизацию запросов приложений](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) для маршрутизации всех запросов от агента пользователя на один и тот же сервер.

Для неприкрепленных сеансов в веб-ферме требуется [распределенный кэш](distributed.md) , чтобы избежать проблем с согласованностью кэша. Для некоторых приложений распределенный кэш может поддерживать более высокие возможности масштабирования, чем кэш в памяти. Использование распределенного кэша разгружает кэш-память во внешний процесс.

Кэш в памяти может хранить любой объект. Интерфейс распределенного кэша ограничен `byte[]` . Элементы кэша в памяти и распределенного кэша хранятся в виде пар "ключ-значение".

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Пакет NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) можно использовать с:

* .NET Standard 2,0 или более поздней версии.
* Любая [реализация .NET](/dotnet/standard/net-standard#net-implementation-support) , предназначенная для .NET Standard 2,0 или более поздней версии. Например, ASP.NET Core 2,0 или более поздней версии.
* .NET Framework 4.5 или более поздней версии.

[Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (описывается в этой статье) рекомендуется использовать, `System.Runtime.Caching` / `MemoryCache` так как он лучше интегрирован в ASP.NET Core. Например, `IMemoryCache` работает изначально с [внедрением зависимостей](xref:fundamentals/dependency-injection)ASP.NET Core.

Используйте `System.Runtime.Caching` / `MemoryCache` в качестве моста совместимости при переносе кода из ASP.NET 4. x в ASP.NET Core.

## <a name="cache-guidelines"></a>Рекомендации по кэшированию

* Код должен всегда иметь резервный вариант для выборки данных и **не** зависит от доступности кэшированного значения.
* Кэш использует неограниченный ресурс, память. Ограничение роста кэша:
  * **Не** используйте внешние входные данные в качестве ключей кэша.
  * Используйте срок действия, чтобы ограничить рост кэша.
  * [Используйте SetSize, Size и сизелимит для ограничения размера кэша](#use-setsize-size-and-sizelimit-to-limit-cache-size). Среда выполнения ASP.NET Core не ограничивает размер кэша на основе нехватки памяти. Разработчик может ограничить размер кэша.

## <a name="using-imemorycache"></a>Использование IMemoryCache

> [!WARNING]
> Использование кэша *общей* памяти из [внедрения зависимостей](xref:fundamentals/dependency-injection) и вызова `SetSize` , `Size` или `SizeLimit` для ограничения размера кэша может привести к сбою приложения. Если для кэша задано ограничение размера, все записи должны указывать размер при добавлении. Это может привести к проблемам, так как разработчики могут не иметь полного контроля над тем, что использует общий кэш. Например, Entity Framework Core использует общий кэш и не задает размер. Если приложение устанавливает ограничение размера кэша и использует EF Core, приложение создает исключение `InvalidOperationException` .
> При использовании `SetSize` , `Size` или `SizeLimit` для ограничения кэша создайте одноэлементный кэш для кэширования. Дополнительные сведения и пример см. в статьях [Использование SetSize, Size и сизелимит для ограничения размера кэша](#use-setsize-size-and-sizelimit-to-limit-cache-size).

Кэширование в памяти — это *Служба* , на которую можно ссылаться из приложения с помощью [внедрения зависимостей](../../fundamentals/dependency-injection.md). Вызов `AddMemoryCache` в `ConfigureServices` :

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Запросите `IMemoryCache` экземпляр в конструкторе:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

`IMemoryCache` требуется пакет NuGet [Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), доступный в [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).

В следующем коде используется [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) для проверки, находится ли время в кэше. Если время не кэшировано, создается новая запись и добавляется в кэш с помощью [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

Текущее время и кэшированное время отображаются:

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

Кэшированное `DateTime` значение остается в кэше во время выполнения запросов в течение периода ожидания. На следующем рисунке показано текущее время и старое время, полученное из кэша:

![Представление индекса с двумя различно отображаемым временем](memory/_static/time.png)

В следующем коде для кэширования данных используются [жеторкреате](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) и [жеторкреатеасинк](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) .

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Следующий код вызывает [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) для получения кэшированного времени:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>методы, и [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) являются методами расширения в классе [качикстенсионс](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) , который расширяет возможности <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . Описание других методов кэширования см. в разделе [методы IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) и [методы качикстенсионс](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) .

## <a name="memorycacheentryoptions"></a>меморикачинтрйоптионс

Следующий пример:

* Задает скользящий срок действия. Запросы, обращающиеся к этому кэшированному элементу, будут сбрасывать скользящий срок действия.
* Устанавливает для приоритета кэша значение `CacheItemPriority.NeverRemove` .
* Задает [постевиктионделегате](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , который будет вызываться после удаления записи из кэша. Обратный вызов выполняется в другом потоке из кода, который удаляет элемент из кэша.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Использование SetSize, Size и Сизелимит для ограничения размера кэша

`MemoryCache`Экземпляр может дополнительно задавать и применять ограничение размера. Ограничение размера кэша не имеет определенной единицы измерения, так как кэш не имеет механизма для измерения размера записей. Если установлен предел размера кэша, то для всех записей должен быть указан размер. Среда выполнения ASP.NET Core не ограничивает размер кэша на основе нехватки памяти. Разработчик может ограничить размер кэша. Указанный размер находится в единицах, которые выбирает разработчик.

Пример:

* Если веб-приложение в основном кэширует строки, каждый размер записи кэша может быть длиной строки.
* Приложение может указать размер всех записей как 1, а максимальный размер — число записей.

Если <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> параметр не установлен, кэш растет без привязки. При нехватке системной памяти среда выполнения ASP.NET Core не усекает кэш. Разработка приложений в значительной степени составляет:

* Ограничение роста кэша.
* Вызов <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> или <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> Если доступная память ограничена:

В следующем коде создается фиксированный размер независимого <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> от единицы, доступный при [внедрении зависимостей](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` не имеет единиц. Кэшированные записи должны указывать размер в любых единицах, которые они считаются наиболее подходящими, если установлен предел размера кэша. Все пользователи экземпляра кэша должны использовать одну и ту же систему единиц измерения. Запись не будет кэшироваться, если сумма кэшированных размеров записей превышает значение, заданное параметром `SizeLimit` . Если ограничение размера кэша не задано, размер кэша, заданный для записи, будет проигнорирован.

Следующий код регистрируется в `MyMemoryCache` контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` создается как независимый кэш памяти для компонентов, которые знают об этом ограниченном размере кэша и знают, как задать размер записи кэша соответствующим образом.

В следующем коде используется `MyMemoryCache` :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

Размер записи кэша можно задать по [размеру](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) или методу расширения [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` пытается удалить указанный процент кэша в следующем порядке:

* Все элементы с истекшим сроком действия.
* Элементы по приоритету. Первыми удаляются элементы с наименьшим приоритетом.
* Наименее недавно использованные объекты.
* Элементы с самым ранним абсолютным сроком действия.
* Элементы с самой ранней скользящей истечением срока действия.

Закрепленные элементы с приоритетом <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> никогда не удаляются.

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Дополнительные сведения см. [в разделе Compact Source на сайте GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Зависимости кэша

В следующем примере показано, как истечет срок действия записи кэша, если истечет срок действия зависимой записи. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>Добавляется к кэшированному элементу. Если `Cancel` метод вызывается для `CancellationTokenSource` , то обе записи кэша будут вытеснены.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

Использование `CancellationTokenSource` позволяет удалять несколько записей кэша как группу. Используя `using` шаблон в приведенном выше коде, записи кэша, созданные внутри `using` блока, будут наследовать параметры триггеров и срока действия.

## <a name="additional-notes"></a>Дополнительные сведения

* При использовании обратного вызова для повторного заполнения элемента кэша:

  * Несколько запросов могут найти значение кэшированного ключа пустым, так как обратный вызов не завершен.
  * Это может привести к повторному заполнению кэшированного элемента несколькими потоками.

* Если одна запись кэша используется для создания другой, дочерняя запись копирует маркеры истечения срока действия родительской записи и параметры срока действия, основанные на времени. Срок действия дочернего элемента не истек, удаление или обновление родительской записи вручную.

* Используйте [постевиктионкаллбаккс](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) , чтобы задать обратные вызовы, которые будут срабатывать после удаления записи кэша из кэша.

## <a name="background-cache-update"></a>Фоновое обновление кэша

Используйте [фоновую службу](xref:fundamentals/host/hosted-services) , например, <xref:Microsoft.Extensions.Hosting.IHostedService> для обновления кэша. Фоновая служба может повторно вычислить записи и назначить их кэшу только в том случае, если они готовы.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
