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
ms.openlocfilehash: bdfaf50895612e739eb5ca068a76755f97cf24c2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588078"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="7547c-103">SignalRКлиент Java ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7547c-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="7547c-104">Авторы: [Микаэль Менгисту](https://twitter.com/MikaelM_12) (Mikael Mengistu)</span><span class="sxs-lookup"><span data-stu-id="7547c-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="7547c-105">Клиент Java позволяет подключаться к SignalR серверу ASP.NET Core из кода Java, включая приложения Android.</span><span class="sxs-lookup"><span data-stu-id="7547c-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="7547c-106">Как и клиент [JavaScript](xref:signalr/javascript-client) и [клиент .NET](xref:signalr/dotnet-client), клиент Java позволяет получать и отправлять сообщения в концентратор в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="7547c-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="7547c-107">Клиент Java доступен в ASP.NET Core 2,2 и более поздних версиях.</span><span class="sxs-lookup"><span data-stu-id="7547c-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="7547c-108">Пример консольного приложения Java, упоминаемого в этой статье, использует SignalR клиент Java.</span><span class="sxs-lookup"><span data-stu-id="7547c-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="7547c-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/java-client/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7547c-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="7547c-110">Установка SignalR клиентского пакета Java</span><span class="sxs-lookup"><span data-stu-id="7547c-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="7547c-111">JAR *-файл 1.0.0* позволяет клиентам подключаться к SignalR концентраторам.</span><span class="sxs-lookup"><span data-stu-id="7547c-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="7547c-112">Чтобы найти последний номер версии JAR-файла, см. [Результаты поиска Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="7547c-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="7547c-113">При использовании Gradle добавьте следующую строку в `dependencies` раздел файла *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="7547c-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="7547c-114">При использовании Maven добавьте следующие строки в `<dependencies>` элемент файла *pom.xml* :</span><span class="sxs-lookup"><span data-stu-id="7547c-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="7547c-115">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="7547c-115">Connect to a hub</span></span>

<span data-ttu-id="7547c-116">Чтобы установить `HubConnection` , `HubConnectionBuilder` следует использовать.</span><span class="sxs-lookup"><span data-stu-id="7547c-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="7547c-117">URL-адрес центра и уровень журнала можно настроить при создании соединения.</span><span class="sxs-lookup"><span data-stu-id="7547c-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="7547c-118">Настройте все необходимые параметры, вызвав любой из `HubConnectionBuilder` методов, предшествующих `build` .</span><span class="sxs-lookup"><span data-stu-id="7547c-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="7547c-119">Запустите соединение с `start` .</span><span class="sxs-lookup"><span data-stu-id="7547c-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="7547c-120">Методы концентратора вызовов от клиента</span><span class="sxs-lookup"><span data-stu-id="7547c-120">Call hub methods from client</span></span>

<span data-ttu-id="7547c-121">Вызов `send` метода вызывает метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="7547c-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="7547c-122">Передайте имя метода концентратора и все аргументы, определенные в методе концентратора, в `send` .</span><span class="sxs-lookup"><span data-stu-id="7547c-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="7547c-123">Вызов методов концентратора из клиента поддерживается только при использовании SignalR службы Azure в режиме *по умолчанию* .</span><span class="sxs-lookup"><span data-stu-id="7547c-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="7547c-124">Дополнительные сведения см. в разделе [часто задаваемые вопросы (репозиторий GitHub Azure SignalR)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="7547c-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="7547c-125">Вызов методов клиента из концентратора</span><span class="sxs-lookup"><span data-stu-id="7547c-125">Call client methods from hub</span></span>

<span data-ttu-id="7547c-126">Используется `hubConnection.on` для определения методов на клиенте, которые может вызывать концентратор.</span><span class="sxs-lookup"><span data-stu-id="7547c-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="7547c-127">Определите методы после сборки, но перед запуском соединения.</span><span class="sxs-lookup"><span data-stu-id="7547c-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="7547c-128">Добавление ведения журнала</span><span class="sxs-lookup"><span data-stu-id="7547c-128">Add logging</span></span>

<span data-ttu-id="7547c-129">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="7547c-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7547c-130">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="7547c-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7547c-131">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="7547c-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7547c-132">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="7547c-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7547c-133">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="7547c-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="7547c-134">Заметки о разработке для Android</span><span class="sxs-lookup"><span data-stu-id="7547c-134">Android development notes</span></span>

<span data-ttu-id="7547c-135">С учетом пакет SDK для Android совместимости для SignalR клиентских функций при указании целевой версии пакет SDK для Android учитывайте следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="7547c-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="7547c-136">SignalRКлиент Java будет работать на API Android уровня 16 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="7547c-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="7547c-137">Для подключения через службу Azure потребуется SignalR уровень API Android 20 и более поздней версии, так как [ SignalR службе azure](/azure/azure-signalr/signalr-overview) требуется TLS 1,2 и не поддерживаются комплекты шифров на основе SHA-1.</span><span class="sxs-lookup"><span data-stu-id="7547c-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="7547c-138">В Android [добавлена поддержка комплектов шифров SHA-256 (и более поздних версий)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) на уровне API 20.</span><span class="sxs-lookup"><span data-stu-id="7547c-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="7547c-139">Настройка проверки подлинности токена носителя</span><span class="sxs-lookup"><span data-stu-id="7547c-139">Configure bearer token authentication</span></span>

<span data-ttu-id="7547c-140">В SignalR клиенте Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив "фабрику маркеров доступа" для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="7547c-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7547c-141">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="7547c-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7547c-142">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="7547c-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a><span data-ttu-id="7547c-143">Передача сведений о классах в Java</span><span class="sxs-lookup"><span data-stu-id="7547c-143">Passing Class information in Java</span></span>

<span data-ttu-id="7547c-144">При вызове `on` `invoke` методов, или `stream` `HubConnection` в клиенте Java пользователи должны передать объект, а не `Type` `Class<?>` объект для описания любого универсального `Object` метода, переданного в метод.</span><span class="sxs-lookup"><span data-stu-id="7547c-144">When calling the `on`, `invoke`, or `stream` methods of `HubConnection` in the Java client, users should pass a `Type` object rather than a `Class<?>` object to describe any generic `Object` passed to the method.</span></span> <span data-ttu-id="7547c-145">`Type`Можно получить с помощью указанного `TypeReference` класса.</span><span class="sxs-lookup"><span data-stu-id="7547c-145">A `Type` can be acquired using the provided `TypeReference` class.</span></span> <span data-ttu-id="7547c-146">Например, с помощью пользовательского универсального класса с именем `Foo<T>` следующий код получает `Type` :</span><span class="sxs-lookup"><span data-stu-id="7547c-146">For example, using a custom generic class named `Foo<T>`, the following code gets the `Type`:</span></span>

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

<span data-ttu-id="7547c-147">Для неуниверсальных типов, таких как примитивы или другие непараметризованные типы, такие как `String` , можно просто использовать встроенный `.class` .</span><span class="sxs-lookup"><span data-stu-id="7547c-147">For non-generics, such as primitives or other non-parameterized types like `String`, you can simply use the built-in `.class`.</span></span>

<span data-ttu-id="7547c-148">При вызове одного из этих методов с одним или несколькими типами объектов используйте синтаксис универсальных шаблонов при вызове метода.</span><span class="sxs-lookup"><span data-stu-id="7547c-148">When calling one of these methods with one or more object types, use the generics syntax when invoking the method.</span></span> <span data-ttu-id="7547c-149">Например, при регистрации `on` обработчика для метода с именем `func` , который принимает в качестве аргументов строку и `Foo<String>` объект, используйте следующий код, чтобы задать действие для печати аргументов:</span><span class="sxs-lookup"><span data-stu-id="7547c-149">For example, when registering an `on` handler for a method named `func`, which takes as arguments a String and a `Foo<String>` object, use the following code to set an action to print the arguments:</span></span>

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

<span data-ttu-id="7547c-150">Это соглашение необходимо, так как мы не можем получить полные сведения о сложных типах с помощью `Object.getClass` метода из-за типа очистки в Java.</span><span class="sxs-lookup"><span data-stu-id="7547c-150">This convention is necessary because we can not retrieve complete information about complex types with the `Object.getClass` method due to type erasure in Java.</span></span> <span data-ttu-id="7547c-151">Например, вызов метода `getClass` для `ArrayList<String>` не возвращает, а `Class<ArrayList<String>>` `Class<ArrayList>` , скорее, не предоставляет Десериализатору достаточно информации для правильной десериализации входящего сообщения.</span><span class="sxs-lookup"><span data-stu-id="7547c-151">For example, calling `getClass` on an `ArrayList<String>` would not return `Class<ArrayList<String>>`, but rather `Class<ArrayList>`, which does not give the deserializer enough information to correctly deserialize an incoming message.</span></span> <span data-ttu-id="7547c-152">То же самое справедливо и для пользовательских объектов.</span><span class="sxs-lookup"><span data-stu-id="7547c-152">The same is true for custom objects.</span></span>

::: moniker-end

## <a name="known-limitations"></a><span data-ttu-id="7547c-153">Известные ограничения</span><span class="sxs-lookup"><span data-stu-id="7547c-153">Known limitations</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7547c-154">Транспортировка транспорта и транспорт событий отправки сервера не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="7547c-154">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* <span data-ttu-id="7547c-155">Транспортировка транспорта и транспорт событий отправки сервера не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="7547c-155">Transport fallback and the Server Sent Events transport aren't supported.</span></span>
* <span data-ttu-id="7547c-156">Поддерживается только протокол JSON.</span><span class="sxs-lookup"><span data-stu-id="7547c-156">Only the JSON protocol is supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="7547c-157">Поддерживается только протокол JSON.</span><span class="sxs-lookup"><span data-stu-id="7547c-157">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="7547c-158">Поддерживается только транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7547c-158">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="7547c-159">Потоковая передача еще не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="7547c-159">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7547c-160">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7547c-160">Additional resources</span></span>

* [<span data-ttu-id="7547c-161">Справочник по API Java</span><span class="sxs-lookup"><span data-stu-id="7547c-161">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="7547c-162">SignalRДокументация, посвященная бессерверной службе Azure</span><span class="sxs-lookup"><span data-stu-id="7547c-162">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
