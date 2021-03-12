---
title: Создание сообщений protobuf для приложений .NET
author: jamesnk
description: Узнайте, как создавать сообщения protobuf для приложений .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/12/2021
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
uid: grpc/protobuf
ms.openlocfilehash: 65a84c34e182b7a330d14e1f7ace17790ee4ed47
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110174"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="22ee4-103">Создание сообщений protobuf для приложений .NET</span><span class="sxs-lookup"><span data-stu-id="22ee4-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="22ee4-104">Авторы: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King) и [Марк Рендл](https://twitter.com/markrendle) (Mark Rendle)</span><span class="sxs-lookup"><span data-stu-id="22ee4-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="22ee4-105">gRPC использует [protobuf](https://developers.google.com/protocol-buffers) в качестве языка определения интерфейса (IDL).</span><span class="sxs-lookup"><span data-stu-id="22ee4-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="22ee4-106">Protobuf IDL — это не зависящий от языка формат для указания сообщений, отправляемых и получаемых службами gRPC.</span><span class="sxs-lookup"><span data-stu-id="22ee4-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="22ee4-107">Сообщения protobuf определяются в файлах `.proto`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="22ee4-108">В этом документе объясняется, как концепции protobuf соотносятся с .NET.</span><span class="sxs-lookup"><span data-stu-id="22ee4-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="22ee4-109">Сообщения Protobuf</span><span class="sxs-lookup"><span data-stu-id="22ee4-109">Protobuf messages</span></span>

<span data-ttu-id="22ee4-110">Сообщения — это основной объект для обмена данными в protobuf.</span><span class="sxs-lookup"><span data-stu-id="22ee4-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="22ee4-111">Они концептуально схожи с классами .NET.</span><span class="sxs-lookup"><span data-stu-id="22ee4-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="22ee4-112">В предыдущем определении сообщения указывается три поля в качестве пар "имя — значение".</span><span class="sxs-lookup"><span data-stu-id="22ee4-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="22ee4-113">Как и свойства типов .NET, каждое поле имеет имя и тип.</span><span class="sxs-lookup"><span data-stu-id="22ee4-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="22ee4-114">Типом поля может быть [скалярный тип значения protobuf](#scalar-value-types), например `int32`, или другое сообщение.</span><span class="sxs-lookup"><span data-stu-id="22ee4-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="22ee4-115">В [инструкции по стилю protobuf`underscore_separated_names` рекомендуется использовать ](https://developers.google.com/protocol-buffers/docs/style) для имен полей.</span><span class="sxs-lookup"><span data-stu-id="22ee4-115">The [Protobuf style guide](https://developers.google.com/protocol-buffers/docs/style) recommends using `underscore_separated_names` for field names.</span></span> <span data-ttu-id="22ee4-116">Новые сообщения protobuf, созданные для приложений .NET, должны следовать рекомендациям по стилю protobuf.</span><span class="sxs-lookup"><span data-stu-id="22ee4-116">New Protobuf messages created for .NET apps should follow the Protobuf style guidelines.</span></span> <span data-ttu-id="22ee4-117">Инструментарий .NET автоматически создает типы .NET, использующие стандарты именования .NET.</span><span class="sxs-lookup"><span data-stu-id="22ee4-117">.NET tooling automatically generates .NET types that use .NET naming standards.</span></span> <span data-ttu-id="22ee4-118">Например, поле protobuf `first_name` формирует свойство .NET `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-118">For example, a `first_name` Protobuf field generates a `FirstName` .NET property.</span></span>

<span data-ttu-id="22ee4-119">Помимо имени, каждое поле в определении сообщения имеет уникальный номер.</span><span class="sxs-lookup"><span data-stu-id="22ee4-119">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="22ee4-120">Номера полей используются для задания полей при сериализации сообщения в protobuf.</span><span class="sxs-lookup"><span data-stu-id="22ee4-120">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="22ee4-121">Сериализация небольшого числа выполняется быстрее, чем сериализация всего имени поля.</span><span class="sxs-lookup"><span data-stu-id="22ee4-121">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="22ee4-122">Поскольку номера полей указывают на поле, важно соблюдать осторожность при их изменении.</span><span class="sxs-lookup"><span data-stu-id="22ee4-122">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="22ee4-123">Дополнительные сведения об изменении сообщений protobuf см. в разделе <xref:grpc/versioning>.</span><span class="sxs-lookup"><span data-stu-id="22ee4-123">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="22ee4-124">При построении приложения средства protobuf создают типы .NET из файлов `.proto`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-124">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="22ee4-125">Сообщение `Person` создает класс .NET:</span><span class="sxs-lookup"><span data-stu-id="22ee4-125">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="22ee4-126">Дополнительные сведения о сообщениях protobuf см. в разделе [Руководство по языку protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="22ee4-126">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="22ee4-127">Скалярные типы значений</span><span class="sxs-lookup"><span data-stu-id="22ee4-127">Scalar Value Types</span></span>

<span data-ttu-id="22ee4-128">Protobuf поддерживает ряд собственных скалярных типов значений.</span><span class="sxs-lookup"><span data-stu-id="22ee4-128">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="22ee4-129">В следующей таблице перечислены все типы с эквивалентными им типами в C#.</span><span class="sxs-lookup"><span data-stu-id="22ee4-129">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="22ee4-130">Тип protobuf</span><span class="sxs-lookup"><span data-stu-id="22ee4-130">Protobuf type</span></span> | <span data-ttu-id="22ee4-131">Тип C#</span><span class="sxs-lookup"><span data-stu-id="22ee4-131">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

<span data-ttu-id="22ee4-132">Скалярные значения всегда имеют значение по умолчанию. Им невозможно задать значение `null`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-132">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="22ee4-133">Это ограничение действует для `string` и `ByteString`, которые являются классами C#.</span><span class="sxs-lookup"><span data-stu-id="22ee4-133">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="22ee4-134">Значением по умолчанию `string` является пустое строковое значение, а для `ByteString` по умолчанию используется пустое байтовое значение.</span><span class="sxs-lookup"><span data-stu-id="22ee4-134">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="22ee4-135">При попытке задать для них значение `null` возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="22ee4-135">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="22ee4-136">[Типы оболочек, допускающие значение NULL](#nullable-types), могут использоваться для поддержки значений NULL.</span><span class="sxs-lookup"><span data-stu-id="22ee4-136">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="22ee4-137">Даты и время</span><span class="sxs-lookup"><span data-stu-id="22ee4-137">Dates and times</span></span>

<span data-ttu-id="22ee4-138">Собственные скалярные типы не предоставляют значения даты и времени, что эквивалентно <xref:System.DateTimeOffset>, <xref:System.DateTime> и <xref:System.TimeSpan> в .NET.</span><span class="sxs-lookup"><span data-stu-id="22ee4-138">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="22ee4-139">Эти типы можно задавать с помощью некоторых из расширений *хорошо известных типов* Protobuf.</span><span class="sxs-lookup"><span data-stu-id="22ee4-139">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="22ee4-140">Эти расширения обеспечивают поддержку создания кода и среды выполнения для сложных типов полей в поддерживаемых платформах.</span><span class="sxs-lookup"><span data-stu-id="22ee4-140">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="22ee4-141">В следующей таблице показаны типы даты и времени.</span><span class="sxs-lookup"><span data-stu-id="22ee4-141">The following table shows the date and time types:</span></span>

| <span data-ttu-id="22ee4-142">Тип .NET</span><span class="sxs-lookup"><span data-stu-id="22ee4-142">.NET type</span></span>        | <span data-ttu-id="22ee4-143">Хорошо известный тип Protobuf</span><span class="sxs-lookup"><span data-stu-id="22ee4-143">Protobuf Well-Known Type</span></span>    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="22ee4-144">Созданные свойства в классе C# не являются типами даты и времени .NET.</span><span class="sxs-lookup"><span data-stu-id="22ee4-144">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="22ee4-145">Свойства используют классы `Timestamp` и `Duration` в пространстве имен `Google.Protobuf.WellKnownTypes`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-145">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="22ee4-146">Эти классы предоставляют методы для преобразования в `DateTimeOffset`, `DateTime` и `TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-146">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="22ee4-147">Тип `Timestamp` работает с временем в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="22ee4-147">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="22ee4-148">Значения `DateTimeOffset` всегда имеют нулевое смещение, а свойство `DateTime.Kind` всегда имеет значение `DateTimeKind.Utc`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-148">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="22ee4-149">Типы, допускающие значение NULL</span><span class="sxs-lookup"><span data-stu-id="22ee4-149">Nullable types</span></span>

<span data-ttu-id="22ee4-150">При создании кода protobuf для C# используются собственные типы, например `int` для `int32`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-150">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="22ee4-151">Поэтому значения всегда включаются и не могут быть `null`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-151">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="22ee4-152">Для значений, требующих явного задания `null`, например при использовании `int?` в коде C#, хорошо известные типы Protobuf включают оболочки, которые компилируются в типы C#, допускающие значение NULL.</span><span class="sxs-lookup"><span data-stu-id="22ee4-152">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="22ee4-153">Чтобы использовать их, импортируйте `wrappers.proto` в файл `.proto`, как в следующем коде.</span><span class="sxs-lookup"><span data-stu-id="22ee4-153">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="22ee4-154">Типы `wrappers.proto` не представлены в созданных свойствах.</span><span class="sxs-lookup"><span data-stu-id="22ee4-154">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="22ee4-155">Protobuf автоматически сопоставляет их с соответствующими типами .NET, допускающими значение NULL, в сообщениях C#.</span><span class="sxs-lookup"><span data-stu-id="22ee4-155">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="22ee4-156">Например, поле `google.protobuf.Int32Value` создает свойство `int?`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-156">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="22ee4-157">Свойства ссылочного типа, такие как `string` и `ByteString`, не изменяются, за исключением того, что значение `null` может быть назначено им без ошибок.</span><span class="sxs-lookup"><span data-stu-id="22ee4-157">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="22ee4-158">В следующей таблице приведен полный список типов оболочек с эквивалентным им типом C#.</span><span class="sxs-lookup"><span data-stu-id="22ee4-158">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="22ee4-159">Тип C#</span><span class="sxs-lookup"><span data-stu-id="22ee4-159">C# type</span></span>      | <span data-ttu-id="22ee4-160">Оболочка хорошо известного типа</span><span class="sxs-lookup"><span data-stu-id="22ee4-160">Well-Known Type wrapper</span></span>       |
| ------------ | ----------------------------- |
| `bool?`      | `google.protobuf.BoolValue`   |
| `double?`    | `google.protobuf.DoubleValue` |
| `float?`     | `google.protobuf.FloatValue`  |
| `int?`       | `google.protobuf.Int32Value`  |
| `long?`      | `google.protobuf.Int64Value`  |
| `uint?`      | `google.protobuf.UInt32Value` |
| `ulong?`     | `google.protobuf.UInt64Value` |
| `string`     | `google.protobuf.StringValue` |
| `ByteString` | `google.protobuf.BytesValue`  |

### <a name="bytes"></a><span data-ttu-id="22ee4-161">Байты</span><span class="sxs-lookup"><span data-stu-id="22ee4-161">Bytes</span></span>

<span data-ttu-id="22ee4-162">Двоичные полезные данные поддерживаются в Protobuf со скалярным типом значений `bytes`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-162">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="22ee4-163">Созданное свойство в C# использует `ByteString` как тип свойства.</span><span class="sxs-lookup"><span data-stu-id="22ee4-163">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="22ee4-164">Используйте `ByteString.CopyFrom(byte[] data)` для создания экземпляра из массива байтов:</span><span class="sxs-lookup"><span data-stu-id="22ee4-164">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="22ee4-165">Доступ к данным `ByteString` осуществляется напрямую с помощью `ByteString.Span` или `ByteString.Memory`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-165">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="22ee4-166">Или вызовите `ByteString.ToByteArray()`, чтобы преобразовать экземпляр обратно в массив байтов:</span><span class="sxs-lookup"><span data-stu-id="22ee4-166">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="22ee4-167">Десятичные знаки</span><span class="sxs-lookup"><span data-stu-id="22ee4-167">Decimals</span></span>

<span data-ttu-id="22ee4-168">Protobuf изначально не поддерживает тип .NET `decimal`, просто `double` и `float`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-168">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="22ee4-169">В проекте Protobuf ведется обсуждение возможности добавления к хорошо известным типам стандартного десятичного типа с платформенной поддержкой языков и инфраструктур, поддерживающих эту возможность.</span><span class="sxs-lookup"><span data-stu-id="22ee4-169">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="22ee4-170">Однако к настоящему моменту еще ничего не реализовано.</span><span class="sxs-lookup"><span data-stu-id="22ee4-170">Nothing has been implemented yet.</span></span>

<span data-ttu-id="22ee4-171">Можно создать определение сообщения, представляющее тип `decimal`, которое подходит для безопасной сериализации между клиентами и серверами .NET.</span><span class="sxs-lookup"><span data-stu-id="22ee4-171">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="22ee4-172">Однако разработчикам на других платформах следует понимать, какой формат используется, и реализовать свою собственную обработку.</span><span class="sxs-lookup"><span data-stu-id="22ee4-172">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="22ee4-173">Создание настраиваемого десятичного типа для protobuf</span><span class="sxs-lookup"><span data-stu-id="22ee4-173">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="22ee4-174">Поле `nanos` представляет значения из `0.999_999_999` в `-0.999_999_999`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-174">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="22ee4-175">Например, значение `decimal` `1.5m` будет представлено как `{ units = 1, nanos = 500_000_000 }`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-175">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="22ee4-176">Именно поэтому в поле `nanos` в этом примере используется тип `sfixed32`, который более эффективно кодируется, чем `int32` для больших значений.</span><span class="sxs-lookup"><span data-stu-id="22ee4-176">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="22ee4-177">Если поле `units` имеет отрицательное значение, поле `nanos` также должно быть отрицательным.</span><span class="sxs-lookup"><span data-stu-id="22ee4-177">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="22ee4-178">Существует несколько других алгоритмов кодирования значений `decimal` в виде строк байтов, но это сообщение проще понять, чем любое из них.</span><span class="sxs-lookup"><span data-stu-id="22ee4-178">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="22ee4-179">На значения не влияет обратный порядок байтов или прямой порядок байтов в разных платформах.</span><span class="sxs-lookup"><span data-stu-id="22ee4-179">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="22ee4-180">Преобразование между этим типом и типом BCL `decimal` может быть реализовано в C# следующим образом:</span><span class="sxs-lookup"><span data-stu-id="22ee4-180">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="22ee4-181">Коллекции</span><span class="sxs-lookup"><span data-stu-id="22ee4-181">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="22ee4-182">Списки</span><span class="sxs-lookup"><span data-stu-id="22ee4-182">Lists</span></span>

<span data-ttu-id="22ee4-183">Списки в protobuf указываются с помощью ключевого слова префикса `repeated` в поле.</span><span class="sxs-lookup"><span data-stu-id="22ee4-183">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="22ee4-184">В следующем примере показано, как создать список.</span><span class="sxs-lookup"><span data-stu-id="22ee4-184">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="22ee4-185">В созданном коде поля `repeated` представлены универсальным типом `Google.Protobuf.Collections.RepeatedField<T>`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-185">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="22ee4-186">Объект `RepeatedField<T>` реализует интерфейс <xref:System.Collections.Generic.IList%601>.</span><span class="sxs-lookup"><span data-stu-id="22ee4-186">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="22ee4-187">Поэтому можно использовать запросы LINQ или преобразовать их в массив или список.</span><span class="sxs-lookup"><span data-stu-id="22ee4-187">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="22ee4-188">У свойств `RepeatedField<T>` нет открытого метода задания.</span><span class="sxs-lookup"><span data-stu-id="22ee4-188">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="22ee4-189">Элементы должны быть добавлены в существующую коллекцию.</span><span class="sxs-lookup"><span data-stu-id="22ee4-189">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="22ee4-190">Словари</span><span class="sxs-lookup"><span data-stu-id="22ee4-190">Dictionaries</span></span>

<span data-ttu-id="22ee4-191">Тип .NET <xref:System.Collections.Generic.IDictionary%602> представлен в protobuf с помощью `map<key_type, value_type>`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-191">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="22ee4-192">В созданном коде .NET поля `map` представлены универсальным типом `Google.Protobuf.Collections.MapField<TKey, TValue>`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-192">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="22ee4-193">Объект `MapField<TKey, TValue>` реализует интерфейс <xref:System.Collections.Generic.IDictionary%602>.</span><span class="sxs-lookup"><span data-stu-id="22ee4-193">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="22ee4-194">Как и свойства `repeated`, свойства `map` не имеют открытого метода задания.</span><span class="sxs-lookup"><span data-stu-id="22ee4-194">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="22ee4-195">Элементы должны быть добавлены в существующую коллекцию.</span><span class="sxs-lookup"><span data-stu-id="22ee4-195">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="22ee4-196">Неструктурированные и условные сообщения</span><span class="sxs-lookup"><span data-stu-id="22ee4-196">Unstructured and conditional messages</span></span>

<span data-ttu-id="22ee4-197">Protobuf — это формат обмена сообщениями на основе контракта.</span><span class="sxs-lookup"><span data-stu-id="22ee4-197">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="22ee4-198">Сообщения приложения, включая поля и типы, должны быть указаны в файлах `.proto` при сборке приложения.</span><span class="sxs-lookup"><span data-stu-id="22ee4-198">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="22ee4-199">Проектирование на основе контракта Protobuf отлично подходит для обеспечения содержимого сообщения, но может ограничивать сценарии, в которых не требуется строгий контракт:</span><span class="sxs-lookup"><span data-stu-id="22ee4-199">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="22ee4-200">Сообщения с неизвестными полезными данными.</span><span class="sxs-lookup"><span data-stu-id="22ee4-200">Messages with unknown payloads.</span></span> <span data-ttu-id="22ee4-201">Например, сообщение с полем, которое может содержать любое сообщение.</span><span class="sxs-lookup"><span data-stu-id="22ee4-201">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="22ee4-202">Условные сообщения.</span><span class="sxs-lookup"><span data-stu-id="22ee4-202">Conditional messages.</span></span> <span data-ttu-id="22ee4-203">Например, служба gRPC может возвращать сообщение об успехе или ошибке.</span><span class="sxs-lookup"><span data-stu-id="22ee4-203">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="22ee4-204">Динамические значения.</span><span class="sxs-lookup"><span data-stu-id="22ee4-204">Dynamic values.</span></span> <span data-ttu-id="22ee4-205">Например, сообщение с полем, которое содержит неструктурированную коллекцию значений, аналогично JSON.</span><span class="sxs-lookup"><span data-stu-id="22ee4-205">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="22ee4-206">Для поддержки этих сценариев Protobuf предлагает языковые функции и типы.</span><span class="sxs-lookup"><span data-stu-id="22ee4-206">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="22ee4-207">Любой</span><span class="sxs-lookup"><span data-stu-id="22ee4-207">Any</span></span>

<span data-ttu-id="22ee4-208">Тип `Any` позволяет использовать сообщения как внедренные типы без определения `.proto`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-208">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="22ee4-209">Чтобы использовать тип `Any`, импортируйте `any.proto`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-209">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="22ee4-210">Oneof</span><span class="sxs-lookup"><span data-stu-id="22ee4-210">Oneof</span></span>

<span data-ttu-id="22ee4-211">Поля `oneof` — это функция языка.</span><span class="sxs-lookup"><span data-stu-id="22ee4-211">`oneof` fields are a language feature.</span></span> <span data-ttu-id="22ee4-212">Компилятор обрабатывает ключевое слово `oneof` при формировании класса сообщения.</span><span class="sxs-lookup"><span data-stu-id="22ee4-212">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="22ee4-213">Использование `oneof` для указания ответного сообщения, которое может возвращать `Person` или `Error`, может выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="22ee4-213">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="22ee4-214">Поля в наборе `oneof` должны иметь уникальные номера полей в общем объявлении сообщения.</span><span class="sxs-lookup"><span data-stu-id="22ee4-214">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="22ee4-215">При использовании `oneof` созданный код C# включает перечисление, указывающее, какое из полей было задано.</span><span class="sxs-lookup"><span data-stu-id="22ee4-215">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="22ee4-216">Можно проверить перечисление, чтобы узнать, какое поле задается.</span><span class="sxs-lookup"><span data-stu-id="22ee4-216">You can test the enum to find which field is set.</span></span> <span data-ttu-id="22ee4-217">Поля, которые не заданы, возвращают `null` или значение по умолчанию вместо создания исключения.</span><span class="sxs-lookup"><span data-stu-id="22ee4-217">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="22ee4-218">Значение</span><span class="sxs-lookup"><span data-stu-id="22ee4-218">Value</span></span>

<span data-ttu-id="22ee4-219">Тип `Value` представляет динамически типизированное значение.</span><span class="sxs-lookup"><span data-stu-id="22ee4-219">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="22ee4-220">Это может быть либо `null`, число, строка, логическое значение, словарь значений (`Struct`), либо список значений (`ValueList`).</span><span class="sxs-lookup"><span data-stu-id="22ee4-220">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="22ee4-221">`Value` — это хорошо известный тип Protobuf, использующий описанную выше функцию `oneof`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-221">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="22ee4-222">Чтобы использовать тип `Value`, импортируйте `struct.proto`.</span><span class="sxs-lookup"><span data-stu-id="22ee4-222">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="22ee4-223">Использование `Value` напрямую может быть подробным.</span><span class="sxs-lookup"><span data-stu-id="22ee4-223">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="22ee4-224">Альтернативный способ использования `Value` — встроенная поддержка protobuf для сопоставления сообщений с JSON.</span><span class="sxs-lookup"><span data-stu-id="22ee4-224">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="22ee4-225">Типы `JsonFormatter` и `JsonWriter` protobuf можно использовать с любым сообщением protobuf.</span><span class="sxs-lookup"><span data-stu-id="22ee4-225">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="22ee4-226">`Value` особенно хорошо подходит для преобразования в JSON и обратно.</span><span class="sxs-lookup"><span data-stu-id="22ee4-226">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="22ee4-227">Это эквивалент JSON предыдущего кода.</span><span class="sxs-lookup"><span data-stu-id="22ee4-227">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Data);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="22ee4-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="22ee4-228">Additional resources</span></span>

* [<span data-ttu-id="22ee4-229">Руководство по языку protobuf</span><span class="sxs-lookup"><span data-stu-id="22ee4-229">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
