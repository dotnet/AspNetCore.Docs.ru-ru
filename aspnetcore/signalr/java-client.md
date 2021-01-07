---
title: SignalRКлиент Java ASP.NET Core
author: mikaelm12
description: Узнайте, как использовать SignalR клиент Java ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: da6876e0540579dac5fb9e92362b38a398bca4d5
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972084"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a><span data-ttu-id="b8e8a-103">SignalRКлиент Java ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8e8a-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="b8e8a-104">Авторы: [Микаэль Менгисту](https://twitter.com/MikaelM_12) (Mikael Mengistu)</span><span class="sxs-lookup"><span data-stu-id="b8e8a-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="b8e8a-105">Клиент Java позволяет подключаться к SignalR серверу ASP.NET Core из кода Java, включая приложения Android.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="b8e8a-106">Как и клиент [JavaScript](xref:signalr/javascript-client) и [клиент .NET](xref:signalr/dotnet-client), клиент Java позволяет получать и отправлять сообщения в концентратор в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="b8e8a-107">Клиент Java доступен в ASP.NET Core 2,2 и более поздних версиях.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="b8e8a-108">Пример консольного приложения Java, упоминаемого в этой статье, использует SignalR клиент Java.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="b8e8a-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8e8a-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-java-client-package"></a><span data-ttu-id="b8e8a-110">Установка SignalR клиентского пакета Java</span><span class="sxs-lookup"><span data-stu-id="b8e8a-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="b8e8a-111">JAR *-файл 1.0.0* позволяет клиентам подключаться к SignalR концентраторам.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="b8e8a-112">Чтобы найти последний номер версии JAR-файла, см. [Результаты поиска Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="b8e8a-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="b8e8a-113">При использовании Gradle добавьте следующую строку в `dependencies` раздел файла *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="b8e8a-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="b8e8a-114">При использовании Maven добавьте следующие строки в `<dependencies>` элемент файла *pom.xml* :</span><span class="sxs-lookup"><span data-stu-id="b8e8a-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="b8e8a-115">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="b8e8a-115">Connect to a hub</span></span>

<span data-ttu-id="b8e8a-116">Чтобы установить `HubConnection` , `HubConnectionBuilder` следует использовать.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="b8e8a-117">URL-адрес центра и уровень журнала можно настроить при создании соединения.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="b8e8a-118">Настройте все необходимые параметры, вызвав любой из `HubConnectionBuilder` методов, предшествующих `build` .</span><span class="sxs-lookup"><span data-stu-id="b8e8a-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="b8e8a-119">Запустите соединение с `start` .</span><span class="sxs-lookup"><span data-stu-id="b8e8a-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="b8e8a-120">Методы концентратора вызовов от клиента</span><span class="sxs-lookup"><span data-stu-id="b8e8a-120">Call hub methods from client</span></span>

<span data-ttu-id="b8e8a-121">Вызов `send` метода вызывает метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="b8e8a-122">Передайте имя метода концентратора и все аргументы, определенные в методе концентратора, в `send` .</span><span class="sxs-lookup"><span data-stu-id="b8e8a-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="b8e8a-123">Вызов методов концентратора из клиента поддерживается только при использовании SignalR службы Azure в режиме *по умолчанию* .</span><span class="sxs-lookup"><span data-stu-id="b8e8a-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="b8e8a-124">Дополнительные сведения см. в разделе [часто задаваемые вопросы (репозиторий GitHub Azure SignalR)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="b8e8a-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="b8e8a-125">Вызов методов клиента из концентратора</span><span class="sxs-lookup"><span data-stu-id="b8e8a-125">Call client methods from hub</span></span>

<span data-ttu-id="b8e8a-126">Используется `hubConnection.on` для определения методов на клиенте, которые может вызывать концентратор.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="b8e8a-127">Определите методы после сборки, но перед запуском соединения.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="b8e8a-128">Добавление ведения журнала</span><span class="sxs-lookup"><span data-stu-id="b8e8a-128">Add logging</span></span>

<span data-ttu-id="b8e8a-129">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b8e8a-130">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b8e8a-131">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b8e8a-132">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="b8e8a-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b8e8a-133">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="b8e8a-134">Заметки о разработке для Android</span><span class="sxs-lookup"><span data-stu-id="b8e8a-134">Android development notes</span></span>

<span data-ttu-id="b8e8a-135">С учетом пакет SDK для Android совместимости для SignalR клиентских функций при указании целевой версии пакет SDK для Android учитывайте следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="b8e8a-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="b8e8a-136">SignalRКлиент Java будет работать на API Android уровня 16 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="b8e8a-137">Для подключения через службу Azure потребуется SignalR уровень API Android 20 и более поздней версии, так как [ SignalR службе azure](/azure/azure-signalr/signalr-overview) требуется TLS 1,2 и не поддерживаются комплекты шифров на основе SHA-1.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="b8e8a-138">В Android [добавлена поддержка комплектов шифров SHA-256 (и более поздних версий)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) на уровне API 20.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="b8e8a-139">Настройка проверки подлинности токена носителя</span><span class="sxs-lookup"><span data-stu-id="b8e8a-139">Configure bearer token authentication</span></span>

<span data-ttu-id="b8e8a-140">В SignalR клиенте Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив "фабрику маркеров доступа" для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b8e8a-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b8e8a-141">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="b8e8a-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b8e8a-142">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="b8e8a-143">Известные ограничения</span><span class="sxs-lookup"><span data-stu-id="b8e8a-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="b8e8a-144">Поддерживается только протокол JSON.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="b8e8a-145">Транспортировка транспорта и транспорт событий отправки сервера не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="b8e8a-146">Поддерживается только протокол JSON.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="b8e8a-147">Поддерживается только транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="b8e8a-148">Потоковая передача еще не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="b8e8a-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b8e8a-149">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b8e8a-149">Additional resources</span></span>

* [<span data-ttu-id="b8e8a-150">Справочник по API Java</span><span class="sxs-lookup"><span data-stu-id="b8e8a-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="b8e8a-151">SignalRДокументация, посвященная бессерверной службе Azure</span><span class="sxs-lookup"><span data-stu-id="b8e8a-151">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
