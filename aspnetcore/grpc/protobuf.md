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
ms.openlocfilehash: f898907e5bae7c67cfca72c70dc8497f36de2622
ms.sourcegitcommit: 111b4e451da2e275fb074cde5d8a84b26a81937d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "89040850"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="e9aee-103">Vytváření zpráv Protobuf pro aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="e9aee-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="e9aee-104">Autor [: James Newton – král](https://twitter.com/jamesnk) a [označení Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="e9aee-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="e9aee-105">gRPC používá [Protobuf](https://developers.google.com/protocol-buffers) jako svůj jazyk IDL (Interface Definition Language).</span><span class="sxs-lookup"><span data-stu-id="e9aee-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="e9aee-106">Protobuf IDL je jazykově neutrální formát pro určení zpráv odesílaných a přijímaných službou gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="e9aee-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="e9aee-107">Zprávy Protobuf jsou definovány v souborech *.*</span><span class="sxs-lookup"><span data-stu-id="e9aee-107">Protobuf messages are defined in *.proto* files.</span></span> <span data-ttu-id="e9aee-108">Tento dokument vysvětluje, jak se Protobuf koncepty mapují na rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="e9aee-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="e9aee-109">Zprávy ve formátu protobuf</span><span class="sxs-lookup"><span data-stu-id="e9aee-109">Protobuf messages</span></span>

<span data-ttu-id="e9aee-110">Zprávy jsou hlavní objekt pro přenos dat v Protobuf.</span><span class="sxs-lookup"><span data-stu-id="e9aee-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="e9aee-111">Jsou koncepčně podobné třídám .NET.</span><span class="sxs-lookup"><span data-stu-id="e9aee-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="e9aee-112">Předchozí definice zprávy určuje tři pole jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="e9aee-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="e9aee-113">Podobně jako vlastnosti na typech .NET má každé pole název a typ.</span><span class="sxs-lookup"><span data-stu-id="e9aee-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="e9aee-114">Typ pole může být [Protobuf skalární typ hodnoty](#scalar-value-types), například `int32` nebo jiná zpráva.</span><span class="sxs-lookup"><span data-stu-id="e9aee-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="e9aee-115">Kromě názvu má každé pole v definici zprávy jedinečné číslo.</span><span class="sxs-lookup"><span data-stu-id="e9aee-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="e9aee-116">Čísla polí slouží k identifikaci polí při serializaci zprávy do Protobuf.</span><span class="sxs-lookup"><span data-stu-id="e9aee-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="e9aee-117">Serializace malého čísla je rychlejší než serializace celého názvu pole.</span><span class="sxs-lookup"><span data-stu-id="e9aee-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="e9aee-118">Vzhledem k tomu, že čísla polí identifikují pole, je důležité při jejich změně provádět pozornost.</span><span class="sxs-lookup"><span data-stu-id="e9aee-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="e9aee-119">Další informace o změně zpráv Protobuf naleznete v tématu <xref:grpc/versioning> .</span><span class="sxs-lookup"><span data-stu-id="e9aee-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="e9aee-120">Když je aplikace vytvořená, Protobuf nástroje generují typy .NET ze souborů *.* ..</span><span class="sxs-lookup"><span data-stu-id="e9aee-120">When an app is built the Protobuf tooling generates .NET types from *.proto* files.</span></span> <span data-ttu-id="e9aee-121">`Person`Zpráva generuje třídu .NET:</span><span class="sxs-lookup"><span data-stu-id="e9aee-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="e9aee-122">Další informace o zprávách Protobuf najdete v tématu [Průvodce jazyky Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="e9aee-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="e9aee-123">Skalární typy hodnot</span><span class="sxs-lookup"><span data-stu-id="e9aee-123">Scalar Value Types</span></span>

<span data-ttu-id="e9aee-124">Protobuf podporuje rozsah nativních typů skalárních hodnot.</span><span class="sxs-lookup"><span data-stu-id="e9aee-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="e9aee-125">V následující tabulce jsou uvedeny všechny s ekvivalentním typem C#:</span><span class="sxs-lookup"><span data-stu-id="e9aee-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="e9aee-126">Typ Protobuf</span><span class="sxs-lookup"><span data-stu-id="e9aee-126">Protobuf type</span></span> | <span data-ttu-id="e9aee-127">Typ C#</span><span class="sxs-lookup"><span data-stu-id="e9aee-127">C# type</span></span>      |
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

### <a name="dates-and-times"></a><span data-ttu-id="e9aee-128">Data a časy</span><span class="sxs-lookup"><span data-stu-id="e9aee-128">Dates and times</span></span>

<span data-ttu-id="e9aee-129">Nativní skalární typy neposkytují hodnoty data a času, které jsou ekvivalentní. NET ' <xref:System.DateTimeOffset> , <xref:System.DateTime> a <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="e9aee-129">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="e9aee-130">Tyto typy lze zadat pomocí některých rozšíření "dobře známých typů" Protobuf.</span><span class="sxs-lookup"><span data-stu-id="e9aee-130">These types can be specified by using some of Protobuf's "Well Known Types" extensions.</span></span> <span data-ttu-id="e9aee-131">Tato rozšíření poskytují podporu generování kódu a běhové prostředí pro komplexní typy polí napříč podporovanými platformami.</span><span class="sxs-lookup"><span data-stu-id="e9aee-131">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="e9aee-132">V následující tabulce jsou uvedeny typy data a času:</span><span class="sxs-lookup"><span data-stu-id="e9aee-132">The following table shows the date and time types:</span></span>

| <span data-ttu-id="e9aee-133">Typ .NET</span><span class="sxs-lookup"><span data-stu-id="e9aee-133">.NET type</span></span> | <span data-ttu-id="e9aee-134">Protobuf dobře známý typ</span><span class="sxs-lookup"><span data-stu-id="e9aee-134">Protobuf well-known type</span></span> |
| ------- | ------------------------ |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime` | `google.protobuf.Timestamp` |
| `TimeSpan` | `google.protobuf.Duration` |

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

<span data-ttu-id="e9aee-135">Vygenerované vlastnosti ve třídě C# nejsou typy data a času .NET.</span><span class="sxs-lookup"><span data-stu-id="e9aee-135">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="e9aee-136">Vlastnosti používají `Timestamp` `Duration` třídy a v `Google.Protobuf.WellKnownTypes` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="e9aee-136">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="e9aee-137">Tyto třídy poskytují metody pro převod na a z `DateTimeOffset` , `DateTime` a `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-137">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

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
> <span data-ttu-id="e9aee-138">`Timestamp`Typ pracuje s časy UTC.</span><span class="sxs-lookup"><span data-stu-id="e9aee-138">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="e9aee-139">`DateTimeOffset` hodnoty vždy mají posun nula a `DateTime.Kind` vlastnost je vždy `DateTimeKind.Utc` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-139">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="e9aee-140">Typy nullable</span><span class="sxs-lookup"><span data-stu-id="e9aee-140">Nullable types</span></span>

<span data-ttu-id="e9aee-141">Generování kódu Protobuf pro jazyk C# používá nativní typy, jako například `int` pro `int32` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-141">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="e9aee-142">Takže hodnoty jsou vždycky zahrnuté a nemůžou být `null` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-142">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="e9aee-143">Pro hodnoty, které vyžadují explicitní `null` , jako je například použití `int?` v kódu jazyka C#, jsou "dobře známé typy" Protobuf zahrnuty obálky, které jsou kompilovány na typy s možnou hodnotou null C#.</span><span class="sxs-lookup"><span data-stu-id="e9aee-143">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's "Well Known Types" include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="e9aee-144">Pokud je chcete použít, importujte je `wrappers.proto` do `.proto` souboru, jako je například následující kód:</span><span class="sxs-lookup"><span data-stu-id="e9aee-144">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="e9aee-145">Protobuf používá typy s možnou hodnotou null .NET, například `int?` , pro vlastnost Generated Message.</span><span class="sxs-lookup"><span data-stu-id="e9aee-145">Protobuf uses .NET nullable types, for example, `int?`, for the generated message property.</span></span>

<span data-ttu-id="e9aee-146">V následující tabulce je uveden úplný seznam typů obálek s ekvivalentním typem jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="e9aee-146">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="e9aee-147">Typ C#</span><span class="sxs-lookup"><span data-stu-id="e9aee-147">C# type</span></span>   | <span data-ttu-id="e9aee-148">Obálka dobře známého typu</span><span class="sxs-lookup"><span data-stu-id="e9aee-148">Well Known Type wrapper</span></span>       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a><span data-ttu-id="e9aee-149">Desetinná místa</span><span class="sxs-lookup"><span data-stu-id="e9aee-149">Decimals</span></span>

<span data-ttu-id="e9aee-150">Protobuf netivně podporuje `decimal` typ .NET, a to jenom `double` a `float` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-150">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="e9aee-151">V projektu Protobuf je průběžná diskuze o tom, jak přidat standardní typ desetinného čísla do známých typů s podporou platforem pro jazyky a rozhraní, které ho podporují.</span><span class="sxs-lookup"><span data-stu-id="e9aee-151">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the well-known types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="e9aee-152">Ještě nic není implementované.</span><span class="sxs-lookup"><span data-stu-id="e9aee-152">Nothing has been implemented yet.</span></span>

<span data-ttu-id="e9aee-153">Je možné vytvořit definici zprávy `decimal` , která představuje typ, který funguje pro bezpečnou serializaci mezi klienty a servery rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="e9aee-153">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="e9aee-154">Ale vývojáři na jiných platformách by museli porozumět použitému formátu a implementovat jeho vlastní zpracování.</span><span class="sxs-lookup"><span data-stu-id="e9aee-154">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="e9aee-155">Vytvoření vlastního typu desetinného čísla pro Protobuf</span><span class="sxs-lookup"><span data-stu-id="e9aee-155">Creating a custom decimal type for Protobuf</span></span>

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

<span data-ttu-id="e9aee-156">`nanos`Pole představuje hodnoty z `0.999_999_999` do `-0.999_999_999` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-156">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="e9aee-157">`decimal`Hodnota `1.5m` by mohla být například reprezentovaná jako `{ units = 1, nanos = 500_000_000 }` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-157">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="e9aee-158">To je důvod `nanos` , proč pole v tomto příkladu používá `sfixed32` typ, který kóduje efektivněji než `int32` pro větší hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e9aee-158">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="e9aee-159">Pokud `units` je pole záporné, `nanos` mělo by být pole také záporné.</span><span class="sxs-lookup"><span data-stu-id="e9aee-159">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="e9aee-160">K dispozici je několik dalších algoritmů pro kódování `decimal` hodnot jako bajtů, ale tuto zprávu je snazší pochopit.</span><span class="sxs-lookup"><span data-stu-id="e9aee-160">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="e9aee-161">Hodnoty nejsou ovlivněny big-endian nebo little endian na různých platformách.</span><span class="sxs-lookup"><span data-stu-id="e9aee-161">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="e9aee-162">Konverze mezi tímto typem a typem BCL `decimal` může být implementována v jazyce C# takto:</span><span class="sxs-lookup"><span data-stu-id="e9aee-162">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

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

## <a name="collections"></a><span data-ttu-id="e9aee-163">Kolekce</span><span class="sxs-lookup"><span data-stu-id="e9aee-163">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="e9aee-164">Seznamy</span><span class="sxs-lookup"><span data-stu-id="e9aee-164">Lists</span></span>

<span data-ttu-id="e9aee-165">Seznamy v Protobuf jsou určeny pomocí `repeated` klíčového slova prefix v poli.</span><span class="sxs-lookup"><span data-stu-id="e9aee-165">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="e9aee-166">Následující příklad ukazuje, jak vytvořit seznam:</span><span class="sxs-lookup"><span data-stu-id="e9aee-166">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="e9aee-167">Ve vygenerovaném kódu `repeated` jsou pole reprezentována `Google.Protobuf.Collections.RepeatedField<T>` obecným typem.</span><span class="sxs-lookup"><span data-stu-id="e9aee-167">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="e9aee-168">`RepeatedField<T>` implementuje <xref:System.Collections.Generic.IList%601> .</span><span class="sxs-lookup"><span data-stu-id="e9aee-168">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="e9aee-169">Takže můžete použít dotazy LINQ nebo je převést na pole nebo seznam.</span><span class="sxs-lookup"><span data-stu-id="e9aee-169">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="e9aee-170">`RepeatedField<T>` vlastnosti nemají veřejnou metodu setter.</span><span class="sxs-lookup"><span data-stu-id="e9aee-170">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="e9aee-171">Položky by měly být přidány do existující kolekce.</span><span class="sxs-lookup"><span data-stu-id="e9aee-171">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="e9aee-172">Slovníky</span><span class="sxs-lookup"><span data-stu-id="e9aee-172">Dictionaries</span></span>

<span data-ttu-id="e9aee-173">Typ .NET <xref:System.Collections.Generic.IDictionary%602> je reprezentován v Protobuf pomocí `map<key_type, value_type>` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-173">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="e9aee-174">V generovaném kódu .NET `map` jsou pole reprezentována `Google.Protobuf.Collections.MapField<TKey, TValue>` obecným typem.</span><span class="sxs-lookup"><span data-stu-id="e9aee-174">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="e9aee-175">`MapField<TKey, TValue>` implementuje <xref:System.Collections.Generic.IDictionary%602> .</span><span class="sxs-lookup"><span data-stu-id="e9aee-175">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="e9aee-176">Vlastnosti, jako jsou vlastnosti `repeated` , nemají `map` veřejnou metodu setter.</span><span class="sxs-lookup"><span data-stu-id="e9aee-176">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="e9aee-177">Položky by měly být přidány do existující kolekce.</span><span class="sxs-lookup"><span data-stu-id="e9aee-177">Items should be added to the existing collection.</span></span>

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

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="e9aee-178">Nestrukturované a podmíněné zprávy</span><span class="sxs-lookup"><span data-stu-id="e9aee-178">Unstructured and conditional messages</span></span>

<span data-ttu-id="e9aee-179">Protobuf je formát zasílání zpráv prvního kontraktu a zprávy aplikací je potřeba zadat v souboru *. proto* soubory, které jsou sestavené v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e9aee-179">Protobuf is a contract-first messaging format, and an apps messages need to be specified in *.proto* files when the app is built.</span></span> <span data-ttu-id="e9aee-180">V případě pokročilých scénářů nabízí Protobuf jazykové funkce a známé typy pro podporu podmíněných a neznámých zpráv.</span><span class="sxs-lookup"><span data-stu-id="e9aee-180">For advanced scenarios, Protobuf offers language features and well known types to support conditional and unknown messages.</span></span>

### <a name="any"></a><span data-ttu-id="e9aee-181">Všechny</span><span class="sxs-lookup"><span data-stu-id="e9aee-181">Any</span></span>

<span data-ttu-id="e9aee-182">`Any`Typ umožňuje používat zprávy jako vložené typy bez definice *.*</span><span class="sxs-lookup"><span data-stu-id="e9aee-182">The `Any` type lets you use messages as embedded types without having their *.proto* definition.</span></span> <span data-ttu-id="e9aee-183">Chcete-li použít `Any` typ, importujte `any.proto` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-183">To use the `Any` type, import `any.proto`.</span></span>

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

### <a name="oneof"></a><span data-ttu-id="e9aee-184">Oneof</span><span class="sxs-lookup"><span data-stu-id="e9aee-184">Oneof</span></span>

<span data-ttu-id="e9aee-185">`oneof` pole jsou funkcí jazyka.</span><span class="sxs-lookup"><span data-stu-id="e9aee-185">`oneof` fields are a language feature.</span></span> <span data-ttu-id="e9aee-186">Kompilátor zpracovává `oneof` klíčové slovo, když generuje třídu zprávy.</span><span class="sxs-lookup"><span data-stu-id="e9aee-186">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="e9aee-187">Pomocí `oneof` můžete zadat zprávu odpovědi, která by mohla vracet, `Person` nebo `Error` může vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="e9aee-187">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

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

<span data-ttu-id="e9aee-188">Pole v `oneof` sadě musí mít v celkové deklaraci zprávy jedinečné číselné pole.</span><span class="sxs-lookup"><span data-stu-id="e9aee-188">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="e9aee-189">Při použití `oneof` , vygenerovaný kód jazyka C# obsahuje výčet, který určuje, která z polí byla nastavena.</span><span class="sxs-lookup"><span data-stu-id="e9aee-189">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="e9aee-190">Chcete-li zjistit, které pole je nastaveno, můžete otestovat výčet.</span><span class="sxs-lookup"><span data-stu-id="e9aee-190">You can test the enum to find which field is set.</span></span> <span data-ttu-id="e9aee-191">Pole, která nejsou nastavena jako návratová `null` hodnota nebo výchozí hodnotu, namísto vyvolání výjimky.</span><span class="sxs-lookup"><span data-stu-id="e9aee-191">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

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

### <a name="value"></a><span data-ttu-id="e9aee-192">Hodnota</span><span class="sxs-lookup"><span data-stu-id="e9aee-192">Value</span></span>

<span data-ttu-id="e9aee-193">`Value`Typ představuje dynamicky typované hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e9aee-193">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="e9aee-194">Může to být buď `null` číslo, řetězec, logická hodnota, slovník hodnot ( `Struct` ) nebo seznam hodnot ( `ValueList` ).</span><span class="sxs-lookup"><span data-stu-id="e9aee-194">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="e9aee-195">`Value` je dobře známý typ, který používá dříve popisovanou `oneof` funkci.</span><span class="sxs-lookup"><span data-stu-id="e9aee-195">`Value` is a well known type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="e9aee-196">Chcete-li použít `Value` typ, importujte `struct.proto` .</span><span class="sxs-lookup"><span data-stu-id="e9aee-196">To use the `Value` type, import `struct.proto`.</span></span>

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

<span data-ttu-id="e9aee-197">`Value`Přímé použití může být podrobné.</span><span class="sxs-lookup"><span data-stu-id="e9aee-197">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="e9aee-198">Alternativním způsobem, jak použít, `Value` je integrovanou podporu Protobuf pro mapování zpráv do formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="e9aee-198">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="e9aee-199">Protobuf `JsonFormatter` a `JsonWriter` typy lze použít s libovolnou Protobuf zprávou.</span><span class="sxs-lookup"><span data-stu-id="e9aee-199">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="e9aee-200">`Value` je vhodný zejména pro převod na JSON a z něj.</span><span class="sxs-lookup"><span data-stu-id="e9aee-200">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="e9aee-201">Toto je ekvivalent JSON předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="e9aee-201">This is the JSON equivalent of the previous code:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e9aee-202">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e9aee-202">Additional resources</span></span>

* [<span data-ttu-id="e9aee-203">Průvodce jazyky Protobuf</span><span class="sxs-lookup"><span data-stu-id="e9aee-203">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
