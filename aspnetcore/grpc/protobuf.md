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
# <a name="create-protobuf-messages-for-net-apps"></a>Создание сообщений protobuf для приложений .NET

Авторы: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King) и [Марк Рендл](https://twitter.com/markrendle) (Mark Rendle)

gRPC использует [protobuf](https://developers.google.com/protocol-buffers) в качестве языка определения интерфейса (IDL). Protobuf IDL — это не зависящий от языка формат для указания сообщений, отправляемых и получаемых службами gRPC. Сообщения protobuf определяются в файлах `.proto`. В этом документе объясняется, как концепции protobuf соотносятся с .NET.

## <a name="protobuf-messages"></a>Сообщения Protobuf

Сообщения — это основной объект для обмена данными в protobuf. Они концептуально схожи с классами .NET.

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

В предыдущем определении сообщения указывается три поля в качестве пар "имя — значение". Как и свойства типов .NET, каждое поле имеет имя и тип. Типом поля может быть [скалярный тип значения protobuf](#scalar-value-types), например `int32`, или другое сообщение.

В [инструкции по стилю protobuf`underscore_separated_names` рекомендуется использовать ](https://developers.google.com/protocol-buffers/docs/style) для имен полей. Новые сообщения protobuf, созданные для приложений .NET, должны следовать рекомендациям по стилю protobuf. Инструментарий .NET автоматически создает типы .NET, использующие стандарты именования .NET. Например, поле protobuf `first_name` формирует свойство .NET `FirstName`.

Помимо имени, каждое поле в определении сообщения имеет уникальный номер. Номера полей используются для задания полей при сериализации сообщения в protobuf. Сериализация небольшого числа выполняется быстрее, чем сериализация всего имени поля. Поскольку номера полей указывают на поле, важно соблюдать осторожность при их изменении. Дополнительные сведения об изменении сообщений protobuf см. в разделе <xref:grpc/versioning>.

При построении приложения средства protobuf создают типы .NET из файлов `.proto`. Сообщение `Person` создает класс .NET:

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Дополнительные сведения о сообщениях protobuf см. в разделе [Руководство по языку protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).

## <a name="scalar-value-types"></a>Скалярные типы значений

Protobuf поддерживает ряд собственных скалярных типов значений. В следующей таблице перечислены все типы с эквивалентными им типами в C#.

| Тип protobuf | Тип C#      |
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

Скалярные значения всегда имеют значение по умолчанию. Им невозможно задать значение `null`. Это ограничение действует для `string` и `ByteString`, которые являются классами C#. Значением по умолчанию `string` является пустое строковое значение, а для `ByteString` по умолчанию используется пустое байтовое значение. При попытке задать для них значение `null` возникает ошибка.

[Типы оболочек, допускающие значение NULL](#nullable-types), могут использоваться для поддержки значений NULL.

### <a name="dates-and-times"></a>Даты и время

Собственные скалярные типы не предоставляют значения даты и времени, что эквивалентно <xref:System.DateTimeOffset>, <xref:System.DateTime> и <xref:System.TimeSpan> в .NET. Эти типы можно задавать с помощью некоторых из расширений *хорошо известных типов* Protobuf. Эти расширения обеспечивают поддержку создания кода и среды выполнения для сложных типов полей в поддерживаемых платформах.

В следующей таблице показаны типы даты и времени.

| Тип .NET        | Хорошо известный тип Protobuf    |
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

Созданные свойства в классе C# не являются типами даты и времени .NET. Свойства используют классы `Timestamp` и `Duration` в пространстве имен `Google.Protobuf.WellKnownTypes`. Эти классы предоставляют методы для преобразования в `DateTimeOffset`, `DateTime` и `TimeSpan`.

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
> Тип `Timestamp` работает с временем в формате UTC. Значения `DateTimeOffset` всегда имеют нулевое смещение, а свойство `DateTime.Kind` всегда имеет значение `DateTimeKind.Utc`.

### <a name="nullable-types"></a>Типы, допускающие значение NULL

При создании кода protobuf для C# используются собственные типы, например `int` для `int32`. Поэтому значения всегда включаются и не могут быть `null`.

Для значений, требующих явного задания `null`, например при использовании `int?` в коде C#, хорошо известные типы Protobuf включают оболочки, которые компилируются в типы C#, допускающие значение NULL. Чтобы использовать их, импортируйте `wrappers.proto` в файл `.proto`, как в следующем коде.

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

Типы `wrappers.proto` не представлены в созданных свойствах. Protobuf автоматически сопоставляет их с соответствующими типами .NET, допускающими значение NULL, в сообщениях C#. Например, поле `google.protobuf.Int32Value` создает свойство `int?`. Свойства ссылочного типа, такие как `string` и `ByteString`, не изменяются, за исключением того, что значение `null` может быть назначено им без ошибок.

В следующей таблице приведен полный список типов оболочек с эквивалентным им типом C#.

| Тип C#      | Оболочка хорошо известного типа       |
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

### <a name="bytes"></a>Байты

Двоичные полезные данные поддерживаются в Protobuf со скалярным типом значений `bytes`. Созданное свойство в C# использует `ByteString` как тип свойства.

Используйте `ByteString.CopyFrom(byte[] data)` для создания экземпляра из массива байтов:

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

Доступ к данным `ByteString` осуществляется напрямую с помощью `ByteString.Span` или `ByteString.Memory`. Или вызовите `ByteString.ToByteArray()`, чтобы преобразовать экземпляр обратно в массив байтов:

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a>Десятичные знаки

Protobuf изначально не поддерживает тип .NET `decimal`, просто `double` и `float`. В проекте Protobuf ведется обсуждение возможности добавления к хорошо известным типам стандартного десятичного типа с платформенной поддержкой языков и инфраструктур, поддерживающих эту возможность. Однако к настоящему моменту еще ничего не реализовано.

Можно создать определение сообщения, представляющее тип `decimal`, которое подходит для безопасной сериализации между клиентами и серверами .NET. Однако разработчикам на других платформах следует понимать, какой формат используется, и реализовать свою собственную обработку.

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Создание настраиваемого десятичного типа для protobuf

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

Поле `nanos` представляет значения из `0.999_999_999` в `-0.999_999_999`. Например, значение `decimal` `1.5m` будет представлено как `{ units = 1, nanos = 500_000_000 }`. Именно поэтому в поле `nanos` в этом примере используется тип `sfixed32`, который более эффективно кодируется, чем `int32` для больших значений. Если поле `units` имеет отрицательное значение, поле `nanos` также должно быть отрицательным.

> [!NOTE]
> Существует несколько других алгоритмов кодирования значений `decimal` в виде строк байтов, но это сообщение проще понять, чем любое из них. На значения не влияет обратный порядок байтов или прямой порядок байтов в разных платформах.

Преобразование между этим типом и типом BCL `decimal` может быть реализовано в C# следующим образом:

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

## <a name="collections"></a>Коллекции

### <a name="lists"></a>Списки

Списки в protobuf указываются с помощью ключевого слова префикса `repeated` в поле. В следующем примере показано, как создать список.

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

В созданном коде поля `repeated` представлены универсальным типом `Google.Protobuf.Collections.RepeatedField<T>`.

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

Объект `RepeatedField<T>` реализует интерфейс <xref:System.Collections.Generic.IList%601>. Поэтому можно использовать запросы LINQ или преобразовать их в массив или список. У свойств `RepeatedField<T>` нет открытого метода задания. Элементы должны быть добавлены в существующую коллекцию.

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>Словари

Тип .NET <xref:System.Collections.Generic.IDictionary%602> представлен в protobuf с помощью `map<key_type, value_type>`.

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

В созданном коде .NET поля `map` представлены универсальным типом `Google.Protobuf.Collections.MapField<TKey, TValue>`. Объект `MapField<TKey, TValue>` реализует интерфейс <xref:System.Collections.Generic.IDictionary%602>. Как и свойства `repeated`, свойства `map` не имеют открытого метода задания. Элементы должны быть добавлены в существующую коллекцию.

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

## <a name="unstructured-and-conditional-messages"></a>Неструктурированные и условные сообщения

Protobuf — это формат обмена сообщениями на основе контракта. Сообщения приложения, включая поля и типы, должны быть указаны в файлах `.proto` при сборке приложения. Проектирование на основе контракта Protobuf отлично подходит для обеспечения содержимого сообщения, но может ограничивать сценарии, в которых не требуется строгий контракт:

* Сообщения с неизвестными полезными данными. Например, сообщение с полем, которое может содержать любое сообщение.
* Условные сообщения. Например, служба gRPC может возвращать сообщение об успехе или ошибке.
* Динамические значения. Например, сообщение с полем, которое содержит неструктурированную коллекцию значений, аналогично JSON.

Для поддержки этих сценариев Protobuf предлагает языковые функции и типы.

### <a name="any"></a>Любой

Тип `Any` позволяет использовать сообщения как внедренные типы без определения `.proto`. Чтобы использовать тип `Any`, импортируйте `any.proto`.

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

### <a name="oneof"></a>Oneof

Поля `oneof` — это функция языка. Компилятор обрабатывает ключевое слово `oneof` при формировании класса сообщения. Использование `oneof` для указания ответного сообщения, которое может возвращать `Person` или `Error`, может выглядеть следующим образом.

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

Поля в наборе `oneof` должны иметь уникальные номера полей в общем объявлении сообщения.

При использовании `oneof` созданный код C# включает перечисление, указывающее, какое из полей было задано. Можно проверить перечисление, чтобы узнать, какое поле задается. Поля, которые не заданы, возвращают `null` или значение по умолчанию вместо создания исключения.

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

### <a name="value"></a>Значение

Тип `Value` представляет динамически типизированное значение. Это может быть либо `null`, число, строка, логическое значение, словарь значений (`Struct`), либо список значений (`ValueList`). `Value` — это хорошо известный тип Protobuf, использующий описанную выше функцию `oneof`. Чтобы использовать тип `Value`, импортируйте `struct.proto`.

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

Использование `Value` напрямую может быть подробным. Альтернативный способ использования `Value` — встроенная поддержка protobuf для сопоставления сообщений с JSON. Типы `JsonFormatter` и `JsonWriter` protobuf можно использовать с любым сообщением protobuf. `Value` особенно хорошо подходит для преобразования в JSON и обратно.

Это эквивалент JSON предыдущего кода.

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

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Руководство по языку protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
