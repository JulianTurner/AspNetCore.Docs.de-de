---
title: Erstellen von Protobuf-Nachrichten für .NET-Apps
author: jamesnk
description: In diesem Artikel erfahren Sie, wie Sie Protobuf-Nachrichten für .NET-Apps erstellen.
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
ms.openlocfilehash: adb048c9b20e172ef8a7bc13f9b56928b1d4bf77
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280286"
---
# <a name="create-protobuf-messages-for-net-apps"></a>Erstellen von Protobuf-Nachrichten für .NET-Apps

Von [James Newton-King](https://twitter.com/jamesnk) und [Mark Rendle](https://twitter.com/markrendle)

gRPC verwendet [Protobuf](https://developers.google.com/protocol-buffers) als Interface Definition Language (IDL). Protobuf-IDL ist ein sprachunabhängiges Format zum Angeben der Nachrichten, die von gRPC-Diensten gesendet und empfangen werden. Protobuf-Nachrichten werden in `.proto`-Dateien definiert. In dieser Dokumentation wird erläutert, wie Protobuf-Konzepte auf .NET übertragen werden können.

## <a name="protobuf-messages"></a>Protobuf-Nachrichten

Nachrichten sind das primäre Datenübertragungsobjekt in Protobuf. Sie sind konzeptionell mit .NET-Klassen vergleichbar.

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

In der obigen Nachrichtendefinition werden drei Felder als Name/Wert-Paare angegeben. Wie Eigenschaften von .NET-Typen verfügt jedes Feld über einen Namen und einen Typ. Beim Feldtyp kann es sich um einen [Protobuf-Skalarwerttyp](#scalar-value-types) handeln, z. B. `int32`, oder eine andere Nachricht.

Im [Protobuf-Styleguide](https://developers.google.com/protocol-buffers/docs/style) wird `underscore_separated_names` für die Feldnamen empfohlen. Die neuen Protobuf-Nachrichten, die für .NET-Apps erstellt wurden, sollten den Protobuf-Stilrichtlinien entsprechen. .NET-Tools generieren automatisch .NET-Typen, die .NET-Benennungsstandards verwenden. Beispielsweise generiert ein Protobuf-Feld `first_name` die .NET-Eigenschaft `FirstName`.

Zusätzlich zu einem Namen verfügt jedes Feld in der Nachrichtendefinition über eine eindeutige Zahl. Feldzahlen werden dazu verwendet, Felder zu identifizieren, wenn die Nachricht an Protobuf serialisiert wird. Die Serialisierung einer kleinen Zahl ist schneller als die Serialisierung des gesamten Feldnamens. Da Feldzahlen zur Identifikation eines Felds dienen, ist es wichtig, dass Änderung mit Bedacht vorgenommen werden. Weitere Informationen zum Ändern von Protobuf-Nachrichten finden Sie unter <xref:grpc/versioning>.

Wenn eine App erstellt wird, generieren die Protobuf-Tools .NET-Typen aus `.proto`-Dateien. Die `Person`-Nachricht generiert eine .NET-Klasse:

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Weitere Informationen über Protobuf-Nachrichten finden Sie im [Leitfaden zur Protobuf-Sprache](https://developers.google.com/protocol-buffers/docs/proto3#simple).

## <a name="scalar-value-types"></a>Skalarwerttypen

Protobuf unterstützt eine Reihe nativer Skalarwerttypen. In der folgenden Tabelle werden alle dieser Typen mit dem entsprechenden C#-Typ aufgeführt:

| Protobuf-Typ | C#-Typ      |
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

Skalarwerte weisen immer einen Standardwert auf und können nicht auf `null` festgelegt werden. Diese Einschränkung umfasst `string` und `ByteString`, die C#-Klassen sind. `string` ist standardmäßig ein leerer Zeichenfolgenwert, `ByteString` ist standardmäßig ein leerer Bytewert. Wenn Sie versuchen, diese Angaben auf `null` festzulegen, wird ein Fehler ausgelöst.

[Nullable-Wrappertypen](#nullable-types) können zur Unterstützung von NULL-Werten verwendet werden.

### <a name="dates-and-times"></a>Datums- und Zeitangaben

Die nativen Skalartypen stellen wie die .NET-Typen <xref:System.DateTimeOffset>, <xref:System.DateTime> und <xref:System.TimeSpan> keine Datums- und Zeitwerte bereit. Diese Typen können mithilfe der Protobuf-Erweiterung *Well Known Types* angegeben werden. Diese Erweiterungen bieten Codegenerierung und Runtime-Unterstützung für komplexe Feldtypen für die unterstützten Plattformen.

In der folgenden Tabelle werden die Datums- und Zeittypen aufgeführt:

| .NET-Typ        | Bekannter Protobuf-Typ    |
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

Die in der C#-Klasse generierten Eigenschaften sind nicht die Datums- und Zeittypen von .NET. Die Eigenschaften nutzen die Klassen `Timestamp` und `Duration` im Namespace `Google.Protobuf.WellKnownTypes`. Diese Klassen stellen Methoden zum Konvertieren in und aus `DateTimeOffset`, `DateTime` und `TimeSpan` bereit.

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
> Der `Timestamp`-Typ kann mit UTC-Zeiten verwendet werden. `DateTimeOffset`-Werte weisen immer ein Offset von 0 (null) auf, und die `DateTime.Kind`-Eigenschaft ist immer `DateTimeKind.Utc`.

### <a name="nullable-types"></a>Nullable-Typen

Bei der Protobuf-Codegenerierung für C# werden die nativen Typen verwendet, z. B. `int` für `int32`. Daher können die Werte, die immer enthalten sind, nicht `null` sein.

Für Werte, die einen expliziten `null`-Wert erfordern, z. B. `int?` in C#-Code, umfasst die Protobuf-Erweiterung „Well Known Types“ Wrapper, die in C#-Nullable-Typen kompiliert werden. Importieren Sie `wrappers.proto` in Ihre `.proto`-Datei wie im folgenden Code, um sie zu verwenden:

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

`wrappers.proto`-Typen werden in den generierten Eigenschaften nicht verfügbar gemacht. Protobuf ordnet diese automatisch entsprechenden .NET-Nullable-Typen in C#-Nachrichten zu. Beispielsweise generiert ein `google.protobuf.Int32Value`-Feld eine `int?`-Eigenschaft. Verweistypeigenschaften wie `string` und `ByteString` bleiben unverändert, außer dass `null` ihnen ohne Fehler zugewiesen werden kann.

In der folgenden Tabelle finden Sie eine vollständige Liste der Wrappertypen mit dem entsprechenden C#-Typ:

| C#-Typ      | Wrapper für bekannte Typen       |
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

### <a name="bytes"></a>Byte

Binäre Nutzlasten werden in Protobuf mit dem `bytes`-Skalarenwerttyp unterstützt. Eine generierte Eigenschaft in C# verwendet `ByteString` als Eigenschaftstyp.

Verwenden Sie `ByteString.CopyFrom(byte[] data)`, um eine neue Instanz aus einem Bytearray zu erstellen:

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

Der Zugriff auf `ByteString`-Daten erfolgt direkt über `ByteString.Span` oder `ByteString.Memory`. Oder rufen Sie `ByteString.ToByteArray()` auf, um eine Instanz zurück in ein Bytearray zu konvertieren:

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a>Dezimalstellen

Protobuf unterstützt den .NET-Typ `decimal` nicht nativ, nur `double` und `float`. Im Protobuf-Projekt gibt es eine fortlaufende Diskussion darüber, einen Standarddezimaltyp mit Plattformunterstützung für Sprachen und Frameworks, die den Typ unterstützen, zur Erweiterung „Well-Known Types“ hinzuzufügen. Bisher wurde keine Implementierung vorgenommen.

Eine Nachrichtendefinition zum Darstellen des `decimal`-Typs kann erstellt werden, die für die sichere Serialisierung zwischen .NET-Clients und -Servern verwendet werden kann. Entwickler anderer Plattformen müssten jedoch das verwendete Format verstehen und ihr eigenes Format implementieren, um dieses zu verarbeiten.

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Erstellen eines benutzerdefinierten Dezimaltyps für Protobuf

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

Das `nanos`-Feld stellt Werte von `0.999_999_999` bis `-0.999_999_999` dar. Beispielsweise würde der `decimal`-Wert `1.5m` als `{ units = 1, nanos = 500_000_000 }` dargestellt werden. Aus diesem Grund wird in diesem Beispiel für das `nanos`-Feld der `sfixed32`-Typ verwendet, der bei größeren Werten effizienter als `int32` codiert wird. Wenn das `units`-Feld negativ ist, sollte auch das `nanos`-Feld negativ sein.

> [!NOTE]
> Es gibt mehrere andere Algorithmen zum Codieren von `decimal`-Werten als Bytezeichenfolgen, jedoch ist diese Nachricht leichter zu verstehen, als alle dieser Algorithmen. Die Werte werden von „big-endian“ und „little-endian“ auf verschiedenen Plattformen nicht beeinflusst.

Konvertierungen zwischen diesem Typ und dem BCL-Typ `decimal` können wie folgt in C# implementiert werden:

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

## <a name="collections"></a>Auflistungen

### <a name="lists"></a>Listen

Listen werden in Protobuf mithilfe des Präfixschlüsselworts `repeated` in einem Feld angegeben. Im folgenden Beispiel wird das Erstellen einer Liste veranschaulicht:

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

Im generierten Code werden `repeated`-Felder vom generischen Typ `Google.Protobuf.Collections.RepeatedField<T>` dargestellt.

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

`RepeatedField<T>` implementiert <xref:System.Collections.Generic.IList%601>. Sie können also LINQ-Abfragen verwenden oder in ein Array oder eine Liste konvertieren. `RepeatedField<T>`-Eigenschaften verfügen über keinen öffentlichen Setter. Zur vorhandenen Sammlung sollten Elemente hinzugefügt werden.

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>Wörterbücher

Der .NET-Typ <xref:System.Collections.Generic.IDictionary%602> wird in Protobuf mit `map<key_type, value_type>` dargestellt.

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

Im generierten .NET-Code werden `map`-Felder vom generischen Typ `Google.Protobuf.Collections.MapField<TKey, TValue>` dargestellt. `MapField<TKey, TValue>` implementiert <xref:System.Collections.Generic.IDictionary%602>. Wie `repeated`-Eigenschaften verfügen auch `map`-Eigenschaften über keinen öffentlichen Setter. Zur vorhandenen Sammlung sollten Elemente hinzugefügt werden.

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

## <a name="unstructured-and-conditional-messages"></a>Nicht strukturiere und bedingte Nachrichten

Protobuf ist ein Contract-First-Messagingformat. Die Nachrichten einer App, einschließlich der Felder und Typen, müssen beim Kompilieren der App in `.proto`-Dateien angegeben werden. Das Contract-First-Design von Protobuf ist gut geeignet, um Nachrichteninhalte zu erzwingen, kann jedoch zu Einschränkungen führen, falls kein strenger Vertrag erforderlich ist:

* Nachrichten mit unbekannten Nutzdaten, z. B. eine Nachricht mit einem Feld, das eine beliebige Nachricht enthalten könnte
* Bedingte Nachrichten, z. B. eine von einem gRPC-Dienst zurückgegebene Nachricht, ob ein Vorgang erfolgreich war oder ein Fehler aufgetreten ist
* Dynamische Werte, z. B. eine Nachricht mit einem Feld, das eine ungeordnete Wertesammlung (ähnlich wie JSON) enthält

Protobuf bietet Sprachfeatures und Typen, um diese Szenarios zu unterstützen.

### <a name="any"></a>Any

Mithilfe des `Any`-Typs können Sie Nachrichten als eingebettete Typen ohne `.proto`-Definition verwenden. Importieren Sie `any.proto`, um den `Any`-Typ zu verwenden.

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

`oneof`-Felder sind ein Sprachfeature. Der Compiler verarbeitet das `oneof`-Schlüsselwert, wenn die Message-Klasse generiert wird. Die Verwendung von `oneof` zum Angeben einer Antwortnachricht, die entweder `Person` oder `Error` zurückgibt, könnte wie folgt aussehen:

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

Felder in der `oneof`-Gruppe müssen über eindeutige Feldnummern in der gesamten Nachrichtendeklaration verfügen.

Bei Verwendung von `oneof` enthält der generierte C#-Code eine Enumeration, die angibt, welche Felder festgelegt wurden. Sie können die Enumeration testen, um herauszufinden, welches Feld festgelegt wurde. Für nicht festgelegte Felder wird `null` oder der Standardwert zurückgegeben, anstatt dass eine Ausnahme ausgelöst wird.

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

### <a name="value"></a>Wert

Der `Value`-Typ stellt einen dynamisch typisierten Wert dar. Dabei kann es sich um `null`, eine Zahl, eine Zeichenfolge, einen booleschen Wert, ein Wörterbuch mit Werten (`Struct`) oder eine Liste von Werten (`ValueList`) handeln. `Value` ist ein bekannter Protobuf-Typ, der das zuvor beschriebene `oneof`-Feature nutzt. Importieren Sie `struct.proto`, um den `Value`-Typ zu verwenden.

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

Die direkte Verwendung von `Value` kann kompliziert sein. Eine alternative Möglichkeit zum Verwenden von `Value` besteht darin, die integrierte Unterstützung von Protobuf zum Zuordnen von Nachrichten zu JSON zu verwenden. Die Typen `JsonFormatter` und `JsonWriter` von Protobuf können mit allen Protobuf-Nachrichten verwendet werden. `Value` eignet sich insbesondere für die Konvertierung in und aus JSON.

Hier sehen Sie das JSON-Äquivalent des vorherigen Codes:

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Leitfaden zur Protobuf-Sprache](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
