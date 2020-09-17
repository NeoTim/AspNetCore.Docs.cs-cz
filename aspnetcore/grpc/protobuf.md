---
title: Vytváření zpráv Protobuf pro aplikace .NET
author: jamesnk
description: Naučte se vytvářet Protobuf zprávy pro aplikace .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
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
ms.openlocfilehash: ea46e04bc4aa6269efbf8917d5f32194402a66ef
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722693"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="cb3fb-103">Vytváření zpráv Protobuf pro aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="cb3fb-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="cb3fb-104">Autor [: James Newton – král](https://twitter.com/jamesnk) a [označení Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="cb3fb-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="cb3fb-105">gRPC používá [Protobuf](https://developers.google.com/protocol-buffers) jako svůj jazyk IDL (Interface Definition Language).</span><span class="sxs-lookup"><span data-stu-id="cb3fb-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="cb3fb-106">Protobuf IDL je jazykově neutrální formát pro určení zpráv odesílaných a přijímaných službou gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="cb3fb-107">Zprávy Protobuf jsou definovány v `.proto` souborech.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="cb3fb-108">Tento dokument vysvětluje, jak se Protobuf koncepty mapují na rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="cb3fb-109">Zprávy ve formátu protobuf</span><span class="sxs-lookup"><span data-stu-id="cb3fb-109">Protobuf messages</span></span>

<span data-ttu-id="cb3fb-110">Zprávy jsou hlavní objekt pro přenos dat v Protobuf.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="cb3fb-111">Jsou koncepčně podobné třídám .NET.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="cb3fb-112">Předchozí definice zprávy určuje tři pole jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="cb3fb-113">Podobně jako vlastnosti na typech .NET má každé pole název a typ.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="cb3fb-114">Typ pole může být [Protobuf skalární typ hodnoty](#scalar-value-types), například `int32` nebo jiná zpráva.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="cb3fb-115">Kromě názvu má každé pole v definici zprávy jedinečné číslo.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="cb3fb-116">Čísla polí slouží k identifikaci polí při serializaci zprávy do Protobuf.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="cb3fb-117">Serializace malého čísla je rychlejší než serializace celého názvu pole.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="cb3fb-118">Vzhledem k tomu, že čísla polí identifikují pole, je důležité při jejich změně provádět pozornost.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="cb3fb-119">Další informace o změně zpráv Protobuf naleznete v tématu <xref:grpc/versioning> .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="cb3fb-120">Když je aplikace sestavená pomocí nástroje Protobuf, generuje typy .NET ze `.proto` souborů.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-120">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="cb3fb-121">`Person`Zpráva generuje třídu .NET:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="cb3fb-122">Další informace o zprávách Protobuf najdete v tématu [Průvodce jazyky Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="cb3fb-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="cb3fb-123">Skalární typy hodnot</span><span class="sxs-lookup"><span data-stu-id="cb3fb-123">Scalar Value Types</span></span>

<span data-ttu-id="cb3fb-124">Protobuf podporuje rozsah nativních typů skalárních hodnot.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="cb3fb-125">V následující tabulce jsou uvedeny všechny s ekvivalentním typem C#:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="cb3fb-126">Typ Protobuf</span><span class="sxs-lookup"><span data-stu-id="cb3fb-126">Protobuf type</span></span> | <span data-ttu-id="cb3fb-127">Typ C#</span><span class="sxs-lookup"><span data-stu-id="cb3fb-127">C# type</span></span>      |
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

<span data-ttu-id="cb3fb-128">Skalární hodnoty mají vždy výchozí hodnotu a nelze je nastavit na `null` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-128">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="cb3fb-129">Toto omezení zahrnuje `string` a, `ByteString` což jsou třídy jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-129">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="cb3fb-130">`string` ve výchozím nastavení je prázdná hodnota řetězce a `ByteString` Výchozí hodnota je prázdná hodnota v bajtech.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-130">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="cb3fb-131">Při pokusu o nastavení `null` vyvolá chybu.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-131">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="cb3fb-132">K podpoře hodnot null lze použít [typy obálky s možnou hodnotou](#nullable-types) null.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-132">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="cb3fb-133">Data a časy</span><span class="sxs-lookup"><span data-stu-id="cb3fb-133">Dates and times</span></span>

<span data-ttu-id="cb3fb-134">Nativní skalární typy neposkytují hodnoty data a času, které jsou ekvivalentní. NET ' <xref:System.DateTimeOffset> , <xref:System.DateTime> a <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-134">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="cb3fb-135">Tyto typy lze zadat pomocí některých z rozšíření *známých typů* Protobuf.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-135">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="cb3fb-136">Tato rozšíření poskytují podporu generování kódu a běhové prostředí pro komplexní typy polí napříč podporovanými platformami.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-136">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="cb3fb-137">V následující tabulce jsou uvedeny typy data a času:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-137">The following table shows the date and time types:</span></span>

| <span data-ttu-id="cb3fb-138">Typ .NET</span><span class="sxs-lookup"><span data-stu-id="cb3fb-138">.NET type</span></span>        | <span data-ttu-id="cb3fb-139">Protobuf dobře známý typ</span><span class="sxs-lookup"><span data-stu-id="cb3fb-139">Protobuf Well-Known Type</span></span>    |
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

<span data-ttu-id="cb3fb-140">Vygenerované vlastnosti ve třídě C# nejsou typy data a času .NET.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-140">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="cb3fb-141">Vlastnosti používají `Timestamp` `Duration` třídy a v `Google.Protobuf.WellKnownTypes` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-141">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="cb3fb-142">Tyto třídy poskytují metody pro převod na a z `DateTimeOffset` , `DateTime` a `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-142">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

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
> <span data-ttu-id="cb3fb-143">`Timestamp`Typ pracuje s časy UTC.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-143">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="cb3fb-144">`DateTimeOffset` hodnoty vždy mají posun nula a `DateTime.Kind` vlastnost je vždy `DateTimeKind.Utc` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-144">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="cb3fb-145">Typy nullable</span><span class="sxs-lookup"><span data-stu-id="cb3fb-145">Nullable types</span></span>

<span data-ttu-id="cb3fb-146">Generování kódu Protobuf pro jazyk C# používá nativní typy, jako například `int` pro `int32` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-146">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="cb3fb-147">Takže hodnoty jsou vždycky zahrnuté a nemůžou být `null` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-147">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="cb3fb-148">Pro hodnoty, které vyžadují explicitní `null` , jako je například použití `int?` v kódu jazyka C#, jsou známé typy Protobuf zahrnuty do typů, které jsou kompilovány na typy s možnou hodnotou null C#.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-148">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="cb3fb-149">Pokud je chcete použít, importujte je `wrappers.proto` do `.proto` souboru, jako je například následující kód:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-149">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="cb3fb-150">`wrappers.proto` typy nejsou vystavené v generovaných vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-150">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="cb3fb-151">Protobuf je automaticky mapuje na odpovídající typy s možnou hodnotou null v C# zprávy.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-151">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="cb3fb-152">Například `google.protobuf.Int32Value` pole vygeneruje `int?` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-152">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="cb3fb-153">Vlastnosti referenčního typu `string` , jako jsou a, `ByteString` se nemění, s výjimkou toho, že je `null` lze přiřadit bez chyby</span><span class="sxs-lookup"><span data-stu-id="cb3fb-153">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="cb3fb-154">V následující tabulce je uveden úplný seznam typů obálek s ekvivalentním typem jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-154">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="cb3fb-155">Typ C#</span><span class="sxs-lookup"><span data-stu-id="cb3fb-155">C# type</span></span>      | <span data-ttu-id="cb3fb-156">Obálka dobře známého typu</span><span class="sxs-lookup"><span data-stu-id="cb3fb-156">Well-Known Type wrapper</span></span>       |
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

### <a name="bytes"></a><span data-ttu-id="cb3fb-157">Bajty</span><span class="sxs-lookup"><span data-stu-id="cb3fb-157">Bytes</span></span>

<span data-ttu-id="cb3fb-158">Binární datové části jsou podporovány v Protobuf s `bytes` typem skalární hodnoty.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-158">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="cb3fb-159">Vygenerovaná vlastnost v jazyce C# používá `ByteString` jako typ vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-159">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="cb3fb-160">Použijte `ByteString.CopyFrom(byte[] data)` k vytvoření nové instance z bajtového pole:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-160">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="cb3fb-161">`ByteString` k datům se dostanete přímo pomocí `ByteString.Span` nebo `ByteString.Memory` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-161">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="cb3fb-162">Nebo zavolejte `ByteString.ToByteArray()` na převést instanci zpátky do pole bajtů:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-162">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="cb3fb-163">Desetinná místa</span><span class="sxs-lookup"><span data-stu-id="cb3fb-163">Decimals</span></span>

<span data-ttu-id="cb3fb-164">Protobuf netivně podporuje `decimal` typ .NET, a to jenom `double` a `float` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-164">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="cb3fb-165">V projektu Protobuf je průběžná diskuze o tom, jak přidat standardní typ desetinného čísla do známých typů s podporou platforem pro jazyky a rozhraní, které ho podporují.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-165">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="cb3fb-166">Ještě nic není implementované.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-166">Nothing has been implemented yet.</span></span>

<span data-ttu-id="cb3fb-167">Je možné vytvořit definici zprávy `decimal` , která představuje typ, který funguje pro bezpečnou serializaci mezi klienty a servery rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-167">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="cb3fb-168">Ale vývojáři na jiných platformách by museli porozumět použitému formátu a implementovat jeho vlastní zpracování.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-168">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="cb3fb-169">Vytvoření vlastního typu desetinného čísla pro Protobuf</span><span class="sxs-lookup"><span data-stu-id="cb3fb-169">Creating a custom decimal type for Protobuf</span></span>

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

<span data-ttu-id="cb3fb-170">`nanos`Pole představuje hodnoty z `0.999_999_999` do `-0.999_999_999` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-170">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="cb3fb-171">`decimal`Hodnota `1.5m` by mohla být například reprezentovaná jako `{ units = 1, nanos = 500_000_000 }` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-171">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="cb3fb-172">To je důvod `nanos` , proč pole v tomto příkladu používá `sfixed32` typ, který kóduje efektivněji než `int32` pro větší hodnoty.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-172">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="cb3fb-173">Pokud `units` je pole záporné, `nanos` mělo by být pole také záporné.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-173">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="cb3fb-174">K dispozici je několik dalších algoritmů pro kódování `decimal` hodnot jako bajtů, ale tuto zprávu je snazší pochopit.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-174">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="cb3fb-175">Hodnoty nejsou ovlivněny big-endian nebo little endian na různých platformách.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-175">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="cb3fb-176">Konverze mezi tímto typem a typem BCL `decimal` může být implementována v jazyce C# takto:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-176">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

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

## <a name="collections"></a><span data-ttu-id="cb3fb-177">Kolekce</span><span class="sxs-lookup"><span data-stu-id="cb3fb-177">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="cb3fb-178">Seznamy</span><span class="sxs-lookup"><span data-stu-id="cb3fb-178">Lists</span></span>

<span data-ttu-id="cb3fb-179">Seznamy v Protobuf jsou určeny pomocí `repeated` klíčového slova prefix v poli.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-179">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="cb3fb-180">Následující příklad ukazuje, jak vytvořit seznam:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-180">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="cb3fb-181">Ve vygenerovaném kódu `repeated` jsou pole reprezentována `Google.Protobuf.Collections.RepeatedField<T>` obecným typem.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-181">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="cb3fb-182">`RepeatedField<T>` implementuje <xref:System.Collections.Generic.IList%601> .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-182">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="cb3fb-183">Takže můžete použít dotazy LINQ nebo je převést na pole nebo seznam.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-183">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="cb3fb-184">`RepeatedField<T>` vlastnosti nemají veřejnou metodu setter.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-184">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="cb3fb-185">Položky by měly být přidány do existující kolekce.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-185">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="cb3fb-186">Slovníky</span><span class="sxs-lookup"><span data-stu-id="cb3fb-186">Dictionaries</span></span>

<span data-ttu-id="cb3fb-187">Typ .NET <xref:System.Collections.Generic.IDictionary%602> je reprezentován v Protobuf pomocí `map<key_type, value_type>` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-187">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="cb3fb-188">V generovaném kódu .NET `map` jsou pole reprezentována `Google.Protobuf.Collections.MapField<TKey, TValue>` obecným typem.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-188">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="cb3fb-189">`MapField<TKey, TValue>` implementuje <xref:System.Collections.Generic.IDictionary%602> .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-189">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="cb3fb-190">Vlastnosti, jako jsou vlastnosti `repeated` , nemají `map` veřejnou metodu setter.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-190">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="cb3fb-191">Položky by měly být přidány do existující kolekce.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-191">Items should be added to the existing collection.</span></span>

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

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="cb3fb-192">Nestrukturované a podmíněné zprávy</span><span class="sxs-lookup"><span data-stu-id="cb3fb-192">Unstructured and conditional messages</span></span>

<span data-ttu-id="cb3fb-193">Protobuf je formát zasílání zpráv prvního kontraktu.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-193">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="cb3fb-194">Zprávy aplikace, včetně polí a typů, musí být `.proto` při sestavení aplikace zadány v souborech.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-194">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="cb3fb-195">Návrh na první smlouvu Protobuf je skvělý při vynucování obsahu zpráv, ale může omezit scénáře, u kterých není nutná striktní smlouva:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-195">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="cb3fb-196">Zprávy s neznámými datovými částmi.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-196">Messages with unknown payloads.</span></span> <span data-ttu-id="cb3fb-197">Například zpráva s polem, které může obsahovat libovolnou zprávu.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-197">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="cb3fb-198">Podmíněné zprávy.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-198">Conditional messages.</span></span> <span data-ttu-id="cb3fb-199">Například zpráva vrácená ze služby gRPC může být výsledkem úspěchu nebo výsledkem chyby.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-199">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="cb3fb-200">Dynamické hodnoty.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-200">Dynamic values.</span></span> <span data-ttu-id="cb3fb-201">Například zpráva s polem, které obsahuje nestrukturované kolekce hodnot, podobně jako JSON.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-201">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="cb3fb-202">Protobuf nabízí jazykové funkce a typy pro podporu těchto scénářů.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-202">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="cb3fb-203">Všechny</span><span class="sxs-lookup"><span data-stu-id="cb3fb-203">Any</span></span>

<span data-ttu-id="cb3fb-204">`Any`Typ umožňuje používat zprávy jako vložené typy bez nutnosti jejich `.proto` definice.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-204">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="cb3fb-205">Chcete-li použít `Any` typ, importujte `any.proto` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-205">To use the `Any` type, import `any.proto`.</span></span>

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

### <a name="oneof"></a><span data-ttu-id="cb3fb-206">Oneof</span><span class="sxs-lookup"><span data-stu-id="cb3fb-206">Oneof</span></span>

<span data-ttu-id="cb3fb-207">`oneof` pole jsou funkcí jazyka.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-207">`oneof` fields are a language feature.</span></span> <span data-ttu-id="cb3fb-208">Kompilátor zpracovává `oneof` klíčové slovo, když generuje třídu zprávy.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-208">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="cb3fb-209">Pomocí `oneof` můžete zadat zprávu odpovědi, která by mohla vracet, `Person` nebo `Error` může vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-209">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

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

<span data-ttu-id="cb3fb-210">Pole v `oneof` sadě musí mít v celkové deklaraci zprávy jedinečné číselné pole.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-210">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="cb3fb-211">Při použití `oneof` , vygenerovaný kód jazyka C# obsahuje výčet, který určuje, která z polí byla nastavena.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-211">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="cb3fb-212">Chcete-li zjistit, které pole je nastaveno, můžete otestovat výčet.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-212">You can test the enum to find which field is set.</span></span> <span data-ttu-id="cb3fb-213">Pole, která nejsou nastavena jako návratová `null` hodnota nebo výchozí hodnotu, namísto vyvolání výjimky.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-213">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

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

### <a name="value"></a><span data-ttu-id="cb3fb-214">Hodnota</span><span class="sxs-lookup"><span data-stu-id="cb3fb-214">Value</span></span>

<span data-ttu-id="cb3fb-215">`Value`Typ představuje dynamicky typované hodnoty.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-215">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="cb3fb-216">Může to být buď `null` číslo, řetězec, logická hodnota, slovník hodnot ( `Struct` ) nebo seznam hodnot ( `ValueList` ).</span><span class="sxs-lookup"><span data-stu-id="cb3fb-216">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="cb3fb-217">`Value` je Protobuf dobře známý typ, který používá dříve popisovanou `oneof` funkci.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-217">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="cb3fb-218">Chcete-li použít `Value` typ, importujte `struct.proto` .</span><span class="sxs-lookup"><span data-stu-id="cb3fb-218">To use the `Value` type, import `struct.proto`.</span></span>

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

<span data-ttu-id="cb3fb-219">`Value`Přímé použití může být podrobné.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-219">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="cb3fb-220">Alternativním způsobem, jak použít, `Value` je integrovanou podporu Protobuf pro mapování zpráv do formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-220">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="cb3fb-221">Protobuf `JsonFormatter` a `JsonWriter` typy lze použít s libovolnou Protobuf zprávou.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-221">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="cb3fb-222">`Value` je vhodný zejména pro převod na JSON a z něj.</span><span class="sxs-lookup"><span data-stu-id="cb3fb-222">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="cb3fb-223">Toto je ekvivalent JSON předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="cb3fb-223">This is the JSON equivalent of the previous code:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="cb3fb-224">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cb3fb-224">Additional resources</span></span>

* [<span data-ttu-id="cb3fb-225">Průvodce jazyky Protobuf</span><span class="sxs-lookup"><span data-stu-id="cb3fb-225">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
