---
title: Erstellen von Protobuf-Nachrichten für .NET-Apps
author: jamesnk
description: In diesem Artikel erfahren Sie, wie Sie Protobuf-Nachrichten für .NET-Apps erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
ms.openlocfilehash: b70a5ee00405eecfce900b86dc631a54682dce1a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058895"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="8a6c4-103">Erstellen von Protobuf-Nachrichten für .NET-Apps</span><span class="sxs-lookup"><span data-stu-id="8a6c4-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="8a6c4-104">Von [James Newton-King](https://twitter.com/jamesnk) und [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="8a6c4-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="8a6c4-105">gRPC verwendet [Protobuf](https://developers.google.com/protocol-buffers) als Interface Definition Language (IDL).</span><span class="sxs-lookup"><span data-stu-id="8a6c4-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="8a6c4-106">Protobuf-IDL ist ein sprachunabhängiges Format zum Angeben der Nachrichten, die von gRPC-Diensten gesendet und empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="8a6c4-107">Protobuf-Nachrichten werden in `.proto`-Dateien definiert.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="8a6c4-108">In dieser Dokumentation wird erläutert, wie Protobuf-Konzepte auf .NET übertragen werden können.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="8a6c4-109">Protobuf-Nachrichten</span><span class="sxs-lookup"><span data-stu-id="8a6c4-109">Protobuf messages</span></span>

<span data-ttu-id="8a6c4-110">Nachrichten sind das primäre Datenübertragungsobjekt in Protobuf.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="8a6c4-111">Sie sind konzeptionell mit .NET-Klassen vergleichbar.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="8a6c4-112">In der obigen Nachrichtendefinition werden drei Felder als Name/Wert-Paare angegeben.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="8a6c4-113">Wie Eigenschaften von .NET-Typen verfügt jedes Feld über einen Namen und einen Typ.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="8a6c4-114">Beim Feldtyp kann es sich um einen [Protobuf-Skalarwerttyp](#scalar-value-types) handeln, z. B. `int32`, oder eine andere Nachricht.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="8a6c4-115">Zusätzlich zu einem Namen verfügt jedes Feld in der Nachrichtendefinition über eine eindeutige Zahl.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="8a6c4-116">Feldzahlen werden dazu verwendet, Felder zu identifizieren, wenn die Nachricht an Protobuf serialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="8a6c4-117">Die Serialisierung einer kleinen Zahl ist schneller als die Serialisierung des gesamten Feldnamens.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="8a6c4-118">Da Feldzahlen zur Identifikation eines Felds dienen, ist es wichtig, dass Änderung mit Bedacht vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="8a6c4-119">Weitere Informationen zum Ändern von Protobuf-Nachrichten finden Sie unter <xref:grpc/versioning>.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="8a6c4-120">Wenn eine App erstellt wird, generieren die Protobuf-Tools .NET-Typen aus `.proto`-Dateien.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-120">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="8a6c4-121">Die `Person`-Nachricht generiert eine .NET-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="8a6c4-122">Weitere Informationen über Protobuf-Nachrichten finden Sie im [Leitfaden zur Protobuf-Sprache](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="8a6c4-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="8a6c4-123">Skalarwerttypen</span><span class="sxs-lookup"><span data-stu-id="8a6c4-123">Scalar Value Types</span></span>

<span data-ttu-id="8a6c4-124">Protobuf unterstützt eine Reihe nativer Skalarwerttypen.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="8a6c4-125">In der folgenden Tabelle werden alle dieser Typen mit dem entsprechenden C#-Typ aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="8a6c4-126">Protobuf-Typ</span><span class="sxs-lookup"><span data-stu-id="8a6c4-126">Protobuf type</span></span> | <span data-ttu-id="8a6c4-127">C#-Typ</span><span class="sxs-lookup"><span data-stu-id="8a6c4-127">C# type</span></span>      |
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

<span data-ttu-id="8a6c4-128">Skalarwerte weisen immer einen Standardwert auf und können nicht auf `null` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-128">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="8a6c4-129">Diese Einschränkung umfasst `string` und `ByteString`, die C#-Klassen sind.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-129">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="8a6c4-130">`string` ist standardmäßig ein leerer Zeichenfolgenwert, `ByteString` ist standardmäßig ein leerer Bytewert.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-130">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="8a6c4-131">Wenn Sie versuchen, diese Angaben auf `null` festzulegen, wird ein Fehler ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-131">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="8a6c4-132">[Nullable-Wrappertypen](#nullable-types) können zur Unterstützung von NULL-Werten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-132">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="8a6c4-133">Datums- und Zeitangaben</span><span class="sxs-lookup"><span data-stu-id="8a6c4-133">Dates and times</span></span>

<span data-ttu-id="8a6c4-134">Die nativen Skalartypen stellen wie die .NET-Typen <xref:System.DateTimeOffset>, <xref:System.DateTime> und <xref:System.TimeSpan> keine Datums- und Zeitwerte bereit.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-134">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="8a6c4-135">Diese Typen können mithilfe der Protobuf-Erweiterung *Well Known Types* angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-135">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="8a6c4-136">Diese Erweiterungen bieten Codegenerierung und Runtime-Unterstützung für komplexe Feldtypen für die unterstützten Plattformen.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-136">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="8a6c4-137">In der folgenden Tabelle werden die Datums- und Zeittypen aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-137">The following table shows the date and time types:</span></span>

| <span data-ttu-id="8a6c4-138">.NET-Typ</span><span class="sxs-lookup"><span data-stu-id="8a6c4-138">.NET type</span></span>        | <span data-ttu-id="8a6c4-139">Bekannter Protobuf-Typ</span><span class="sxs-lookup"><span data-stu-id="8a6c4-139">Protobuf Well-Known Type</span></span>    |
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

<span data-ttu-id="8a6c4-140">Die in der C#-Klasse generierten Eigenschaften sind nicht die Datums- und Zeittypen von .NET.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-140">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="8a6c4-141">Die Eigenschaften nutzen die Klassen `Timestamp` und `Duration` im Namespace `Google.Protobuf.WellKnownTypes`.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-141">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="8a6c4-142">Diese Klassen stellen Methoden zum Konvertieren in und aus `DateTimeOffset`, `DateTime` und `TimeSpan` bereit.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-142">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

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
> <span data-ttu-id="8a6c4-143">Der `Timestamp`-Typ kann mit UTC-Zeiten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-143">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="8a6c4-144">`DateTimeOffset`-Werte weisen immer ein Offset von 0 (null) auf, und die `DateTime.Kind`-Eigenschaft ist immer `DateTimeKind.Utc`.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-144">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="8a6c4-145">Nullable-Typen</span><span class="sxs-lookup"><span data-stu-id="8a6c4-145">Nullable types</span></span>

<span data-ttu-id="8a6c4-146">Bei der Protobuf-Codegenerierung für C# werden die nativen Typen verwendet, z. B. `int` für `int32`.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-146">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="8a6c4-147">Daher können die Werte, die immer enthalten sind, nicht `null` sein.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-147">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="8a6c4-148">Für Werte, die einen expliziten `null`-Wert erfordern, z. B. `int?` in C#-Code, umfasst die Protobuf-Erweiterung „Well Known Types“ Wrapper, die in C#-Nullable-Typen kompiliert werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-148">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="8a6c4-149">Importieren Sie `wrappers.proto` in Ihre `.proto`-Datei wie im folgenden Code, um sie zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-149">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="8a6c4-150">`wrappers.proto`-Typen werden in den generierten Eigenschaften nicht verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-150">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="8a6c4-151">Protobuf ordnet diese automatisch entsprechenden .NET-Nullable-Typen in C#-Nachrichten zu.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-151">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="8a6c4-152">Beispielsweise generiert ein `google.protobuf.Int32Value`-Feld eine `int?`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-152">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="8a6c4-153">Verweistypeigenschaften wie `string` und `ByteString` bleiben unverändert, außer dass `null` ihnen ohne Fehler zugewiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-153">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="8a6c4-154">In der folgenden Tabelle finden Sie eine vollständige Liste der Wrappertypen mit dem entsprechenden C#-Typ:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-154">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="8a6c4-155">C#-Typ</span><span class="sxs-lookup"><span data-stu-id="8a6c4-155">C# type</span></span>      | <span data-ttu-id="8a6c4-156">Wrapper für bekannte Typen</span><span class="sxs-lookup"><span data-stu-id="8a6c4-156">Well-Known Type wrapper</span></span>       |
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

### <a name="bytes"></a><span data-ttu-id="8a6c4-157">Byte</span><span class="sxs-lookup"><span data-stu-id="8a6c4-157">Bytes</span></span>

<span data-ttu-id="8a6c4-158">Binäre Nutzlasten werden in Protobuf mit dem `bytes`-Skalarenwerttyp unterstützt.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-158">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="8a6c4-159">Eine generierte Eigenschaft in C# verwendet `ByteString` als Eigenschaftstyp.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-159">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="8a6c4-160">Verwenden Sie `ByteString.CopyFrom(byte[] data)`, um eine neue Instanz aus einem Bytearray zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-160">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="8a6c4-161">Der Zugriff auf `ByteString`-Daten erfolgt direkt über `ByteString.Span` oder `ByteString.Memory`.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-161">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="8a6c4-162">Oder rufen Sie `ByteString.ToByteArray()` auf, um eine Instanz zurück in ein Bytearray zu konvertieren:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-162">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="8a6c4-163">Dezimalstellen</span><span class="sxs-lookup"><span data-stu-id="8a6c4-163">Decimals</span></span>

<span data-ttu-id="8a6c4-164">Protobuf unterstützt den .NET-Typ `decimal` nicht nativ, nur `double` und `float`.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-164">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="8a6c4-165">Im Protobuf-Projekt gibt es eine fortlaufende Diskussion darüber, einen Standarddezimaltyp mit Plattformunterstützung für Sprachen und Frameworks, die den Typ unterstützen, zur Erweiterung „Well-Known Types“ hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-165">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="8a6c4-166">Bisher wurde keine Implementierung vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-166">Nothing has been implemented yet.</span></span>

<span data-ttu-id="8a6c4-167">Eine Nachrichtendefinition zum Darstellen des `decimal`-Typs kann erstellt werden, die für die sichere Serialisierung zwischen .NET-Clients und -Servern verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-167">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="8a6c4-168">Entwickler anderer Plattformen müssten jedoch das verwendete Format verstehen und ihr eigenes Format implementieren, um dieses zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-168">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="8a6c4-169">Erstellen eines benutzerdefinierten Dezimaltyps für Protobuf</span><span class="sxs-lookup"><span data-stu-id="8a6c4-169">Creating a custom decimal type for Protobuf</span></span>

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

<span data-ttu-id="8a6c4-170">Das `nanos`-Feld stellt Werte von `0.999_999_999` bis `-0.999_999_999` dar.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-170">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="8a6c4-171">Beispielsweise würde der `decimal`-Wert `1.5m` als `{ units = 1, nanos = 500_000_000 }` dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-171">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="8a6c4-172">Aus diesem Grund wird in diesem Beispiel für das `nanos`-Feld der `sfixed32`-Typ verwendet, der bei größeren Werten effizienter als `int32` codiert wird.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-172">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="8a6c4-173">Wenn das `units`-Feld negativ ist, sollte auch das `nanos`-Feld negativ sein.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-173">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="8a6c4-174">Es gibt mehrere andere Algorithmen zum Codieren von `decimal`-Werten als Bytezeichenfolgen, jedoch ist diese Nachricht leichter zu verstehen, als alle dieser Algorithmen.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-174">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="8a6c4-175">Die Werte werden von „big-endian“ und „little-endian“ auf verschiedenen Plattformen nicht beeinflusst.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-175">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="8a6c4-176">Konvertierungen zwischen diesem Typ und dem BCL-Typ `decimal` können wie folgt in C# implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-176">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

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

## <a name="collections"></a><span data-ttu-id="8a6c4-177">Auflistungen</span><span class="sxs-lookup"><span data-stu-id="8a6c4-177">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="8a6c4-178">Listen</span><span class="sxs-lookup"><span data-stu-id="8a6c4-178">Lists</span></span>

<span data-ttu-id="8a6c4-179">Listen werden in Protobuf mithilfe des Präfixschlüsselworts `repeated` in einem Feld angegeben.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-179">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="8a6c4-180">Im folgenden Beispiel wird das Erstellen einer Liste veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-180">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="8a6c4-181">Im generierten Code werden `repeated`-Felder vom generischen Typ `Google.Protobuf.Collections.RepeatedField<T>` dargestellt.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-181">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="8a6c4-182">`RepeatedField<T>` implementiert <xref:System.Collections.Generic.IList%601>.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-182">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="8a6c4-183">Sie können also LINQ-Abfragen verwenden oder in ein Array oder eine Liste konvertieren.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-183">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="8a6c4-184">`RepeatedField<T>`-Eigenschaften verfügen über keinen öffentlichen Setter.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-184">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="8a6c4-185">Zur vorhandenen Sammlung sollten Elemente hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-185">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="8a6c4-186">Wörterbücher</span><span class="sxs-lookup"><span data-stu-id="8a6c4-186">Dictionaries</span></span>

<span data-ttu-id="8a6c4-187">Der .NET-Typ <xref:System.Collections.Generic.IDictionary%602> wird in Protobuf mit `map<key_type, value_type>` dargestellt.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-187">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="8a6c4-188">Im generierten .NET-Code werden `map`-Felder vom generischen Typ `Google.Protobuf.Collections.MapField<TKey, TValue>` dargestellt.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-188">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="8a6c4-189">`MapField<TKey, TValue>` implementiert <xref:System.Collections.Generic.IDictionary%602>.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-189">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="8a6c4-190">Wie `repeated`-Eigenschaften verfügen auch `map`-Eigenschaften über keinen öffentlichen Setter.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-190">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="8a6c4-191">Zur vorhandenen Sammlung sollten Elemente hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-191">Items should be added to the existing collection.</span></span>

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

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="8a6c4-192">Nicht strukturiere und bedingte Nachrichten</span><span class="sxs-lookup"><span data-stu-id="8a6c4-192">Unstructured and conditional messages</span></span>

<span data-ttu-id="8a6c4-193">Protobuf ist ein Contract-First-Messagingformat.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-193">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="8a6c4-194">Die Nachrichten einer App, einschließlich der Felder und Typen, müssen beim Kompilieren der App in `.proto`-Dateien angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-194">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="8a6c4-195">Das Contract-First-Design von Protobuf ist gut geeignet, um Nachrichteninhalte zu erzwingen, kann jedoch zu Einschränkungen führen, falls kein strenger Vertrag erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-195">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="8a6c4-196">Nachrichten mit unbekannten Nutzdaten,</span><span class="sxs-lookup"><span data-stu-id="8a6c4-196">Messages with unknown payloads.</span></span> <span data-ttu-id="8a6c4-197">z. B. eine Nachricht mit einem Feld, das eine beliebige Nachricht enthalten könnte</span><span class="sxs-lookup"><span data-stu-id="8a6c4-197">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="8a6c4-198">Bedingte Nachrichten,</span><span class="sxs-lookup"><span data-stu-id="8a6c4-198">Conditional messages.</span></span> <span data-ttu-id="8a6c4-199">z. B. eine von einem gRPC-Dienst zurückgegebene Nachricht, ob ein Vorgang erfolgreich war oder ein Fehler aufgetreten ist</span><span class="sxs-lookup"><span data-stu-id="8a6c4-199">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="8a6c4-200">Dynamische Werte,</span><span class="sxs-lookup"><span data-stu-id="8a6c4-200">Dynamic values.</span></span> <span data-ttu-id="8a6c4-201">z. B. eine Nachricht mit einem Feld, das eine ungeordnete Wertesammlung (ähnlich wie JSON) enthält</span><span class="sxs-lookup"><span data-stu-id="8a6c4-201">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="8a6c4-202">Protobuf bietet Sprachfeatures und Typen, um diese Szenarios zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-202">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="8a6c4-203">Any</span><span class="sxs-lookup"><span data-stu-id="8a6c4-203">Any</span></span>

<span data-ttu-id="8a6c4-204">Mithilfe des `Any`-Typs können Sie Nachrichten als eingebettete Typen ohne `.proto`-Definition verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-204">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="8a6c4-205">Importieren Sie `any.proto`, um den `Any`-Typ zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-205">To use the `Any` type, import `any.proto`.</span></span>

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

### <a name="oneof"></a><span data-ttu-id="8a6c4-206">Oneof</span><span class="sxs-lookup"><span data-stu-id="8a6c4-206">Oneof</span></span>

<span data-ttu-id="8a6c4-207">`oneof`-Felder sind ein Sprachfeature.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-207">`oneof` fields are a language feature.</span></span> <span data-ttu-id="8a6c4-208">Der Compiler verarbeitet das `oneof`-Schlüsselwert, wenn die Message-Klasse generiert wird.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-208">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="8a6c4-209">Die Verwendung von `oneof` zum Angeben einer Antwortnachricht, die entweder `Person` oder `Error` zurückgibt, könnte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-209">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

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

<span data-ttu-id="8a6c4-210">Felder in der `oneof`-Gruppe müssen über eindeutige Feldnummern in der gesamten Nachrichtendeklaration verfügen.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-210">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="8a6c4-211">Bei Verwendung von `oneof` enthält der generierte C#-Code eine Enumeration, die angibt, welche Felder festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-211">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="8a6c4-212">Sie können die Enumeration testen, um herauszufinden, welches Feld festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-212">You can test the enum to find which field is set.</span></span> <span data-ttu-id="8a6c4-213">Für nicht festgelegte Felder wird `null` oder der Standardwert zurückgegeben, anstatt dass eine Ausnahme ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-213">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

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

### <a name="value"></a><span data-ttu-id="8a6c4-214">Wert</span><span class="sxs-lookup"><span data-stu-id="8a6c4-214">Value</span></span>

<span data-ttu-id="8a6c4-215">Der `Value`-Typ stellt einen dynamisch typisierten Wert dar.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-215">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="8a6c4-216">Dabei kann es sich um `null`, eine Zahl, eine Zeichenfolge, einen booleschen Wert, ein Wörterbuch mit Werten (`Struct`) oder eine Liste von Werten (`ValueList`) handeln.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-216">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="8a6c4-217">`Value` ist ein bekannter Protobuf-Typ, der das zuvor beschriebene `oneof`-Feature nutzt.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-217">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="8a6c4-218">Importieren Sie `struct.proto`, um den `Value`-Typ zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-218">To use the `Value` type, import `struct.proto`.</span></span>

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

<span data-ttu-id="8a6c4-219">Die direkte Verwendung von `Value` kann kompliziert sein.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-219">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="8a6c4-220">Eine alternative Möglichkeit zum Verwenden von `Value` besteht darin, die integrierte Unterstützung von Protobuf zum Zuordnen von Nachrichten zu JSON zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-220">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="8a6c4-221">Die Typen `JsonFormatter` und `JsonWriter` von Protobuf können mit allen Protobuf-Nachrichten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-221">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="8a6c4-222">`Value` eignet sich insbesondere für die Konvertierung in und aus JSON.</span><span class="sxs-lookup"><span data-stu-id="8a6c4-222">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="8a6c4-223">Hier sehen Sie das JSON-Äquivalent des vorherigen Codes:</span><span class="sxs-lookup"><span data-stu-id="8a6c4-223">This is the JSON equivalent of the previous code:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="8a6c4-224">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8a6c4-224">Additional resources</span></span>

* [<span data-ttu-id="8a6c4-225">Leitfaden zur Protobuf-Sprache</span><span class="sxs-lookup"><span data-stu-id="8a6c4-225">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
