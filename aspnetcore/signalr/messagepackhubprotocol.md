---
title: Использование протокола концентратора MessagePack в SignalR для ASP.NET Core
author: bradygaster
description: Добавьте протокол концентратора MessagePack в ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024695"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="b4350-103">Использование протокола концентратора MessagePack в SignalR для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4350-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b4350-104">В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="b4350-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="b4350-105">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="b4350-105">What is MessagePack?</span></span>

<span data-ttu-id="b4350-106">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="b4350-107">Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="b4350-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="b4350-108">Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="b4350-109">SignalR имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="b4350-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="b4350-110">Настройка MessagePack на сервере</span><span class="sxs-lookup"><span data-stu-id="b4350-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="b4350-111">Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="b4350-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="b4350-112">В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `AddSignalR` вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="b4350-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b4350-113">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b4350-113">JSON is enabled by default.</span></span> <span data-ttu-id="b4350-114">Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="b4350-115">Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="b4350-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="b4350-116">В этом делегате `SerializerOptions` свойство можно использовать для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="b4350-117">Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="b4350-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="b4350-118">Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="b4350-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="b4350-119">Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления.</span><span class="sxs-lookup"><span data-stu-id="b4350-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="b4350-120">Например, вызов `.WithSecurity(MessagePackSecurity.UntrustedData)` при замене `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="b4350-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="b4350-121">Настройка MessagePack на клиенте</span><span class="sxs-lookup"><span data-stu-id="b4350-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="b4350-122">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="b4350-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="b4350-123">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="b4350-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="b4350-124">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="b4350-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="b4350-125">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="b4350-125">.NET client</span></span>

<span data-ttu-id="b4350-126">Чтобы включить MessagePack в клиенте .NET, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b4350-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="b4350-127">Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.</span><span class="sxs-lookup"><span data-stu-id="b4350-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="b4350-128">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="b4350-128">JavaScript client</span></span>

<span data-ttu-id="b4350-129">Поддержка MessagePack для клиента JavaScript предоставляется [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) пакетом NPM.</span><span class="sxs-lookup"><span data-stu-id="b4350-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="b4350-130">Установите пакет, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b4350-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="b4350-131">После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:</span><span class="sxs-lookup"><span data-stu-id="b4350-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="b4350-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="b4350-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="b4350-133">В браузере `msgpack5` также должна быть ссылка на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="b4350-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="b4350-134">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="b4350-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="b4350-135">Библиотеку можно найти по адресу *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="b4350-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="b4350-136">При использовании `<script>` элемента важен порядок.</span><span class="sxs-lookup"><span data-stu-id="b4350-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="b4350-137">Если на *signalr-protocol-msgpack.js* имеется ссылка до *msgpack5.js*, то при попытке соединения с MessagePack возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="b4350-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="b4350-138">*signalr.js* также требуется перед *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="b4350-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="b4350-139">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.</span><span class="sxs-lookup"><span data-stu-id="b4350-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="b4350-140">В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="b4350-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="b4350-141">Клиент Java</span><span class="sxs-lookup"><span data-stu-id="b4350-141">Java client</span></span>

<span data-ttu-id="b4350-142">Чтобы включить MessagePack с помощью Java, установите `com.microsoft.signalr.messagepack` пакет.</span><span class="sxs-lookup"><span data-stu-id="b4350-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="b4350-143">При использовании Gradle добавьте следующую строку в `dependencies` раздел файла *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="b4350-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="b4350-144">При использовании Maven добавьте следующие строки в `<dependencies>` элемент файла *pom.xml* :</span><span class="sxs-lookup"><span data-stu-id="b4350-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="b4350-145">Вызов `withHubProtocol(new MessagePackHubProtocol())` в `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b4350-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="b4350-146">Особенности MessagePack</span><span class="sxs-lookup"><span data-stu-id="b4350-146">MessagePack quirks</span></span>

<span data-ttu-id="b4350-147">Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="b4350-148">MessagePack учитывает регистр</span><span class="sxs-lookup"><span data-stu-id="b4350-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="b4350-149">В протоколе MessagePack учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="b4350-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="b4350-150">Например, рассмотрим следующий класс C#:</span><span class="sxs-lookup"><span data-stu-id="b4350-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="b4350-151">При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#.</span><span class="sxs-lookup"><span data-stu-id="b4350-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="b4350-152">Пример:</span><span class="sxs-lookup"><span data-stu-id="b4350-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="b4350-153">Использование `camelCased` имен не будет правильно привязано к классу C#.</span><span class="sxs-lookup"><span data-stu-id="b4350-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="b4350-154">Это можно обойти, используя атрибут, `Key` чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="b4350-155">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="b4350-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="b4350-156">DateTime. Kind не сохраняется при сериализации или десериализации</span><span class="sxs-lookup"><span data-stu-id="b4350-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="b4350-157">Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="b4350-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="b4350-158">В результате при десериализации даты протокол концентратора MessagePack преобразуется в формат UTC, если в `DateTime.Kind` `DateTimeKind.Local` противном случае он не будет затрагивать время и передать его как есть.</span><span class="sxs-lookup"><span data-stu-id="b4350-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="b4350-159">При работе со `DateTime` значениями рекомендуется преобразовать в формат UTC перед отправкой.</span><span class="sxs-lookup"><span data-stu-id="b4350-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="b4350-160">Преобразуйте их из времени в формате UTC в местное время при их получении.</span><span class="sxs-lookup"><span data-stu-id="b4350-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="b4350-161">DateTime. MinValue не поддерживается в MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="b4350-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="b4350-162">Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая SignalR клиентом JavaScript, не поддерживает `timestamp96` тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="b4350-163">Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="b4350-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="b4350-164">Значение `DateTime.MinValue` равно `January 1, 0001` , которое должно быть закодировано в `timestamp96` значении.</span><span class="sxs-lookup"><span data-stu-id="b4350-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="b4350-165">По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="b4350-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="b4350-166">При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="b4350-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="b4350-167">Обычно `DateTime.MinValue` используется для кодирования «отсутствующего» или `null` значения.</span><span class="sxs-lookup"><span data-stu-id="b4350-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="b4350-168">Если необходимо закодировать это значение в MessagePack, используйте `DateTime` значение null ( `DateTime?` ) или закодировать отдельное `bool` значение, указывающее, существует ли дата.</span><span class="sxs-lookup"><span data-stu-id="b4350-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="b4350-169">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="b4350-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="b4350-170">Поддержка MessagePack в среде предварительной компиляции "до времени"</span><span class="sxs-lookup"><span data-stu-id="b4350-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="b4350-171">Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="b4350-172">Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="b4350-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="b4350-173">В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="b4350-174">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="b4350-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="b4350-175">После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="b4350-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="b4350-176">Проверки типов более строго в MessagePack</span><span class="sxs-lookup"><span data-stu-id="b4350-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="b4350-177">Протокол JSON-концентратора выполнит преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="b4350-178">Например, если входящий объект имеет значение свойства, которое является числом (), `{ foo: 42 }` но свойство класса .NET имеет тип `string` , значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="b4350-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="b4350-179">Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="b4350-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="b4350-180">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="b4350-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="b4350-181">Символы и строки в Java</span><span class="sxs-lookup"><span data-stu-id="b4350-181">Chars and Strings in Java</span></span>

<span data-ttu-id="b4350-182">В клиенте Java `char` объекты будут сериализованы как объекты одного символа `String` .</span><span class="sxs-lookup"><span data-stu-id="b4350-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="b4350-183">Это отличается от клиента C# и JavaScript, который сериализует их как `short` объекты.</span><span class="sxs-lookup"><span data-stu-id="b4350-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="b4350-184">Сама спецификация MessagePack не определяет поведение `char` объектов, поэтому разработчик библиотеки может определить способ их сериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="b4350-185">Различие в поведении между клиентами является результатом использования библиотек, которые мы использовали для наших реализаций.</span><span class="sxs-lookup"><span data-stu-id="b4350-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="b4350-186">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b4350-186">Related resources</span></span>

* [<span data-ttu-id="b4350-187">Начало работы</span><span class="sxs-lookup"><span data-stu-id="b4350-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b4350-188">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="b4350-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b4350-189">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="b4350-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b4350-190">В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="b4350-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="b4350-191">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="b4350-191">What is MessagePack?</span></span>

<span data-ttu-id="b4350-192">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="b4350-193">Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="b4350-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="b4350-194">Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="b4350-195">SignalR имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="b4350-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="b4350-196">Настройка MessagePack на сервере</span><span class="sxs-lookup"><span data-stu-id="b4350-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="b4350-197">Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="b4350-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="b4350-198">В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `AddSignalR` вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="b4350-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b4350-199">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b4350-199">JSON is enabled by default.</span></span> <span data-ttu-id="b4350-200">Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="b4350-201">Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="b4350-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="b4350-202">В этом делегате `FormatterResolvers` свойство можно использовать для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="b4350-203">Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="b4350-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="b4350-204">Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="b4350-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="b4350-205">Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления.</span><span class="sxs-lookup"><span data-stu-id="b4350-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="b4350-206">Например, присвоение `MessagePackSecurity.Active` статическому свойству значения `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="b4350-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="b4350-207">Для установки параметра `MessagePackSecurity.Active` требуется ручная установка [версии 1,9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="b4350-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="b4350-208">При установке `MessagePack` обновлений 1,9. x SignalR используется версия.</span><span class="sxs-lookup"><span data-stu-id="b4350-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="b4350-209">`MessagePack` Версия 2. x предоставила критические изменения и несовместима с SignalR версиями 3,1 и более ранних версий.</span><span class="sxs-lookup"><span data-stu-id="b4350-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="b4350-210">Если `MessagePackSecurity.Active` параметр не имеет значение `MessagePackSecurity.UntrustedData` , вредоносный клиент может вызвать отказ в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="b4350-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="b4350-211">Задайте `MessagePackSecurity.Active` в `Program.Main` , как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="b4350-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="b4350-212">Настройка MessagePack на клиенте</span><span class="sxs-lookup"><span data-stu-id="b4350-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="b4350-213">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="b4350-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="b4350-214">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="b4350-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="b4350-215">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="b4350-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="b4350-216">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="b4350-216">.NET client</span></span>

<span data-ttu-id="b4350-217">Чтобы включить MessagePack в клиенте .NET, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b4350-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="b4350-218">Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.</span><span class="sxs-lookup"><span data-stu-id="b4350-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="b4350-219">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="b4350-219">JavaScript client</span></span>

<span data-ttu-id="b4350-220">Поддержка MessagePack для клиента JavaScript предоставляется [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) пакетом NPM.</span><span class="sxs-lookup"><span data-stu-id="b4350-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="b4350-221">Установите пакет, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b4350-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="b4350-222">После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:</span><span class="sxs-lookup"><span data-stu-id="b4350-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="b4350-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="b4350-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="b4350-224">В браузере `msgpack5` также должна быть ссылка на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="b4350-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="b4350-225">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="b4350-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="b4350-226">Библиотеку можно найти по адресу *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="b4350-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="b4350-227">При использовании `<script>` элемента важен порядок.</span><span class="sxs-lookup"><span data-stu-id="b4350-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="b4350-228">Если на *signalr-protocol-msgpack.js* имеется ссылка до *msgpack5.js*, то при попытке соединения с MessagePack возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="b4350-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="b4350-229">*signalr.js* также требуется перед *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="b4350-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="b4350-230">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.</span><span class="sxs-lookup"><span data-stu-id="b4350-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="b4350-231">В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="b4350-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="b4350-232">Особенности MessagePack</span><span class="sxs-lookup"><span data-stu-id="b4350-232">MessagePack quirks</span></span>

<span data-ttu-id="b4350-233">Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="b4350-234">MessagePack учитывает регистр</span><span class="sxs-lookup"><span data-stu-id="b4350-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="b4350-235">В протоколе MessagePack учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="b4350-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="b4350-236">Например, рассмотрим следующий класс C#:</span><span class="sxs-lookup"><span data-stu-id="b4350-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="b4350-237">При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#.</span><span class="sxs-lookup"><span data-stu-id="b4350-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="b4350-238">Пример:</span><span class="sxs-lookup"><span data-stu-id="b4350-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="b4350-239">Использование `camelCased` имен не будет правильно привязано к классу C#.</span><span class="sxs-lookup"><span data-stu-id="b4350-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="b4350-240">Это можно обойти, используя атрибут, `Key` чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="b4350-241">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="b4350-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="b4350-242">DateTime. Kind не сохраняется при сериализации или десериализации</span><span class="sxs-lookup"><span data-stu-id="b4350-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="b4350-243">Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="b4350-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="b4350-244">В результате при десериализации даты протокол концентратора MessagePack предполагает, что входящая Дата находится в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="b4350-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="b4350-245">Если вы работаете со `DateTime` значениями в местном времени, перед отправкой рекомендуется преобразовать в формат UTC.</span><span class="sxs-lookup"><span data-stu-id="b4350-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="b4350-246">Преобразуйте их из времени в формате UTC в местное время при их получении.</span><span class="sxs-lookup"><span data-stu-id="b4350-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="b4350-247">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="b4350-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="b4350-248">DateTime. MinValue не поддерживается в MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="b4350-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="b4350-249">Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая SignalR клиентом JavaScript, не поддерживает `timestamp96` тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="b4350-250">Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="b4350-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="b4350-251">Значение `DateTime.MinValue` равно `January 1, 0001` , которое должно быть закодировано в `timestamp96` значении.</span><span class="sxs-lookup"><span data-stu-id="b4350-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="b4350-252">По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="b4350-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="b4350-253">При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="b4350-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="b4350-254">Обычно `DateTime.MinValue` используется для кодирования «отсутствующего» или `null` значения.</span><span class="sxs-lookup"><span data-stu-id="b4350-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="b4350-255">Если необходимо закодировать это значение в MessagePack, используйте `DateTime` значение null ( `DateTime?` ) или закодировать отдельное `bool` значение, указывающее, существует ли дата.</span><span class="sxs-lookup"><span data-stu-id="b4350-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="b4350-256">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="b4350-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="b4350-257">Поддержка MessagePack в среде предварительной компиляции "до времени"</span><span class="sxs-lookup"><span data-stu-id="b4350-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="b4350-258">Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="b4350-259">Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="b4350-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="b4350-260">В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="b4350-261">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="b4350-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="b4350-262">После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="b4350-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="b4350-263">Проверки типов более строго в MessagePack</span><span class="sxs-lookup"><span data-stu-id="b4350-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="b4350-264">Протокол JSON-концентратора выполнит преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="b4350-265">Например, если входящий объект имеет значение свойства, которое является числом (), `{ foo: 42 }` но свойство класса .NET имеет тип `string` , значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="b4350-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="b4350-266">Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="b4350-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="b4350-267">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="b4350-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="b4350-268">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b4350-268">Related resources</span></span>

* [<span data-ttu-id="b4350-269">Начало работы</span><span class="sxs-lookup"><span data-stu-id="b4350-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b4350-270">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="b4350-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b4350-271">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="b4350-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b4350-272">В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="b4350-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="b4350-273">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="b4350-273">What is MessagePack?</span></span>

<span data-ttu-id="b4350-274">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="b4350-275">Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="b4350-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="b4350-276">Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="b4350-277">SignalR имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="b4350-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="b4350-278">Настройка MessagePack на сервере</span><span class="sxs-lookup"><span data-stu-id="b4350-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="b4350-279">Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="b4350-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="b4350-280">В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `AddSignalR` вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="b4350-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b4350-281">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b4350-281">JSON is enabled by default.</span></span> <span data-ttu-id="b4350-282">Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="b4350-283">Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="b4350-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="b4350-284">В этом делегате `FormatterResolvers` свойство можно использовать для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="b4350-285">Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="b4350-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="b4350-286">Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="b4350-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="b4350-287">Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления.</span><span class="sxs-lookup"><span data-stu-id="b4350-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="b4350-288">Например, присвоение `MessagePackSecurity.Active` статическому свойству значения `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="b4350-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="b4350-289">Для установки параметра `MessagePackSecurity.Active` требуется ручная установка [версии 1,9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="b4350-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="b4350-290">При установке `MessagePack` обновлений 1,9. x SignalR используется версия.</span><span class="sxs-lookup"><span data-stu-id="b4350-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="b4350-291">Если параметр `MessagePackSecurity.Active` не имеет значение `MessagePackSecurity.UntrustedData` , вредоносный клиент может вызвать отказ в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="b4350-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="b4350-292">Задайте `MessagePackSecurity.Active` в `Program.Main` , как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="b4350-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="b4350-293">Настройка MessagePack на клиенте</span><span class="sxs-lookup"><span data-stu-id="b4350-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="b4350-294">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="b4350-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="b4350-295">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="b4350-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="b4350-296">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="b4350-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="b4350-297">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="b4350-297">.NET client</span></span>

<span data-ttu-id="b4350-298">Чтобы включить MessagePack в клиенте .NET, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b4350-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="b4350-299">Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.</span><span class="sxs-lookup"><span data-stu-id="b4350-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="b4350-300">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="b4350-300">JavaScript client</span></span>

<span data-ttu-id="b4350-301">Поддержка MessagePack для клиента JavaScript предоставляется [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) пакетом NPM.</span><span class="sxs-lookup"><span data-stu-id="b4350-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="b4350-302">Установите пакет, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b4350-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="b4350-303">После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:</span><span class="sxs-lookup"><span data-stu-id="b4350-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="b4350-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="b4350-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="b4350-305">В браузере `msgpack5` также должна быть ссылка на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="b4350-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="b4350-306">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="b4350-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="b4350-307">Библиотеку можно найти по адресу *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="b4350-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="b4350-308">При использовании `<script>` элемента важен порядок.</span><span class="sxs-lookup"><span data-stu-id="b4350-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="b4350-309">Если на *signalr-protocol-msgpack.js* имеется ссылка до *msgpack5.js*, то при попытке соединения с MessagePack возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="b4350-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="b4350-310">*signalr.js* также требуется перед *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="b4350-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="b4350-311">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.</span><span class="sxs-lookup"><span data-stu-id="b4350-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="b4350-312">В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="b4350-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="b4350-313">Особенности MessagePack</span><span class="sxs-lookup"><span data-stu-id="b4350-313">MessagePack quirks</span></span>

<span data-ttu-id="b4350-314">Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="b4350-315">MessagePack учитывает регистр</span><span class="sxs-lookup"><span data-stu-id="b4350-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="b4350-316">В протоколе MessagePack учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="b4350-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="b4350-317">Например, рассмотрим следующий класс C#:</span><span class="sxs-lookup"><span data-stu-id="b4350-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="b4350-318">При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#.</span><span class="sxs-lookup"><span data-stu-id="b4350-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="b4350-319">Пример:</span><span class="sxs-lookup"><span data-stu-id="b4350-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="b4350-320">Использование `camelCased` имен не будет правильно привязано к классу C#.</span><span class="sxs-lookup"><span data-stu-id="b4350-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="b4350-321">Это можно обойти, используя атрибут, `Key` чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="b4350-322">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="b4350-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="b4350-323">DateTime. Kind не сохраняется при сериализации или десериализации</span><span class="sxs-lookup"><span data-stu-id="b4350-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="b4350-324">Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="b4350-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="b4350-325">В результате при десериализации даты протокол концентратора MessagePack предполагает, что входящая Дата находится в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="b4350-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="b4350-326">Если вы работаете со `DateTime` значениями в местном времени, перед отправкой рекомендуется преобразовать в формат UTC.</span><span class="sxs-lookup"><span data-stu-id="b4350-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="b4350-327">Преобразуйте их из времени в формате UTC в местное время при их получении.</span><span class="sxs-lookup"><span data-stu-id="b4350-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="b4350-328">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="b4350-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="b4350-329">DateTime. MinValue не поддерживается в MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="b4350-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="b4350-330">Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая SignalR клиентом JavaScript, не поддерживает `timestamp96` тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b4350-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="b4350-331">Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="b4350-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="b4350-332">Значение, `DateTime.MinValue` `January 1, 0001` которое должно быть закодировано в `timestamp96` значении.</span><span class="sxs-lookup"><span data-stu-id="b4350-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="b4350-333">По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="b4350-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="b4350-334">При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="b4350-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="b4350-335">Обычно `DateTime.MinValue` используется для кодирования «отсутствующего» или `null` значения.</span><span class="sxs-lookup"><span data-stu-id="b4350-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="b4350-336">Если необходимо закодировать это значение в MessagePack, используйте `DateTime` значение null ( `DateTime?` ) или закодировать отдельное `bool` значение, указывающее, существует ли дата.</span><span class="sxs-lookup"><span data-stu-id="b4350-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="b4350-337">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="b4350-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="b4350-338">Поддержка MessagePack в среде предварительной компиляции "до времени"</span><span class="sxs-lookup"><span data-stu-id="b4350-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="b4350-339">Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="b4350-340">Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="b4350-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="b4350-341">В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="b4350-342">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="b4350-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="b4350-343">После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="b4350-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="b4350-344">Проверки типов более строго в MessagePack</span><span class="sxs-lookup"><span data-stu-id="b4350-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="b4350-345">Протокол JSON-концентратора выполнит преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="b4350-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="b4350-346">Например, если входящий объект имеет значение свойства, которое является числом (), `{ foo: 42 }` но свойство класса .NET имеет тип `string` , значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="b4350-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="b4350-347">Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="b4350-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="b4350-348">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="b4350-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="b4350-349">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b4350-349">Related resources</span></span>

* [<span data-ttu-id="b4350-350">Начало работы</span><span class="sxs-lookup"><span data-stu-id="b4350-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b4350-351">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="b4350-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b4350-352">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="b4350-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
