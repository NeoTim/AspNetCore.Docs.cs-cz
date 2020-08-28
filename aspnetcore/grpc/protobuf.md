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
# <a name="create-protobuf-messages-for-net-apps"></a>Vytváření zpráv Protobuf pro aplikace .NET

Autor [: James Newton – král](https://twitter.com/jamesnk) a [označení Rendle](https://twitter.com/markrendle)

gRPC používá [Protobuf](https://developers.google.com/protocol-buffers) jako svůj jazyk IDL (Interface Definition Language). Protobuf IDL je jazykově neutrální formát pro určení zpráv odesílaných a přijímaných službou gRPC Services. Zprávy Protobuf jsou definovány v souborech *.* Tento dokument vysvětluje, jak se Protobuf koncepty mapují na rozhraní .NET.

## <a name="protobuf-messages"></a>Zprávy ve formátu protobuf

Zprávy jsou hlavní objekt pro přenos dat v Protobuf. Jsou koncepčně podobné třídám .NET.

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

Předchozí definice zprávy určuje tři pole jako páry název-hodnota. Podobně jako vlastnosti na typech .NET má každé pole název a typ. Typ pole může být [Protobuf skalární typ hodnoty](#scalar-value-types), například `int32` nebo jiná zpráva.

Kromě názvu má každé pole v definici zprávy jedinečné číslo. Čísla polí slouží k identifikaci polí při serializaci zprávy do Protobuf. Serializace malého čísla je rychlejší než serializace celého názvu pole. Vzhledem k tomu, že čísla polí identifikují pole, je důležité při jejich změně provádět pozornost. Další informace o změně zpráv Protobuf naleznete v tématu <xref:grpc/versioning> .

Když je aplikace vytvořená, Protobuf nástroje generují typy .NET ze souborů *.* .. `Person`Zpráva generuje třídu .NET:

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Další informace o zprávách Protobuf najdete v tématu [Průvodce jazyky Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).

## <a name="scalar-value-types"></a>Skalární typy hodnot

Protobuf podporuje rozsah nativních typů skalárních hodnot. V následující tabulce jsou uvedeny všechny s ekvivalentním typem C#:

| Typ Protobuf | Typ C#      |
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

### <a name="dates-and-times"></a>Data a časy

Nativní skalární typy neposkytují hodnoty data a času, které jsou ekvivalentní. NET ' <xref:System.DateTimeOffset> , <xref:System.DateTime> a <xref:System.TimeSpan> . Tyto typy lze zadat pomocí některých rozšíření "dobře známých typů" Protobuf. Tato rozšíření poskytují podporu generování kódu a běhové prostředí pro komplexní typy polí napříč podporovanými platformami.

V následující tabulce jsou uvedeny typy data a času:

| Typ .NET | Protobuf dobře známý typ |
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

Vygenerované vlastnosti ve třídě C# nejsou typy data a času .NET. Vlastnosti používají `Timestamp` `Duration` třídy a v `Google.Protobuf.WellKnownTypes` oboru názvů. Tyto třídy poskytují metody pro převod na a z `DateTimeOffset` , `DateTime` a `TimeSpan` .

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
> `Timestamp`Typ pracuje s časy UTC. `DateTimeOffset` hodnoty vždy mají posun nula a `DateTime.Kind` vlastnost je vždy `DateTimeKind.Utc` .

### <a name="nullable-types"></a>Typy nullable

Generování kódu Protobuf pro jazyk C# používá nativní typy, jako například `int` pro `int32` . Takže hodnoty jsou vždycky zahrnuté a nemůžou být `null` .

Pro hodnoty, které vyžadují explicitní `null` , jako je například použití `int?` v kódu jazyka C#, jsou "dobře známé typy" Protobuf zahrnuty obálky, které jsou kompilovány na typy s možnou hodnotou null C#. Pokud je chcete použít, importujte je `wrappers.proto` do `.proto` souboru, jako je například následující kód:

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

Protobuf používá typy s možnou hodnotou null .NET, například `int?` , pro vlastnost Generated Message.

V následující tabulce je uveden úplný seznam typů obálek s ekvivalentním typem jazyka C#:

| Typ C#   | Obálka dobře známého typu       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a>Desetinná místa

Protobuf netivně podporuje `decimal` typ .NET, a to jenom `double` a `float` . V projektu Protobuf je průběžná diskuze o tom, jak přidat standardní typ desetinného čísla do známých typů s podporou platforem pro jazyky a rozhraní, které ho podporují. Ještě nic není implementované.

Je možné vytvořit definici zprávy `decimal` , která představuje typ, který funguje pro bezpečnou serializaci mezi klienty a servery rozhraní .NET. Ale vývojáři na jiných platformách by museli porozumět použitému formátu a implementovat jeho vlastní zpracování.

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Vytvoření vlastního typu desetinného čísla pro Protobuf

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

`nanos`Pole představuje hodnoty z `0.999_999_999` do `-0.999_999_999` . `decimal`Hodnota `1.5m` by mohla být například reprezentovaná jako `{ units = 1, nanos = 500_000_000 }` . To je důvod `nanos` , proč pole v tomto příkladu používá `sfixed32` typ, který kóduje efektivněji než `int32` pro větší hodnoty. Pokud `units` je pole záporné, `nanos` mělo by být pole také záporné.

> [!NOTE]
> K dispozici je několik dalších algoritmů pro kódování `decimal` hodnot jako bajtů, ale tuto zprávu je snazší pochopit. Hodnoty nejsou ovlivněny big-endian nebo little endian na různých platformách.

Konverze mezi tímto typem a typem BCL `decimal` může být implementována v jazyce C# takto:

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

## <a name="collections"></a>Kolekce

### <a name="lists"></a>Seznamy

Seznamy v Protobuf jsou určeny pomocí `repeated` klíčového slova prefix v poli. Následující příklad ukazuje, jak vytvořit seznam:

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

Ve vygenerovaném kódu `repeated` jsou pole reprezentována `Google.Protobuf.Collections.RepeatedField<T>` obecným typem.

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

`RepeatedField<T>` implementuje <xref:System.Collections.Generic.IList%601> . Takže můžete použít dotazy LINQ nebo je převést na pole nebo seznam. `RepeatedField<T>` vlastnosti nemají veřejnou metodu setter. Položky by měly být přidány do existující kolekce.

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>Slovníky

Typ .NET <xref:System.Collections.Generic.IDictionary%602> je reprezentován v Protobuf pomocí `map<key_type, value_type>` .

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

V generovaném kódu .NET `map` jsou pole reprezentována `Google.Protobuf.Collections.MapField<TKey, TValue>` obecným typem. `MapField<TKey, TValue>` implementuje <xref:System.Collections.Generic.IDictionary%602> . Vlastnosti, jako jsou vlastnosti `repeated` , nemají `map` veřejnou metodu setter. Položky by měly být přidány do existující kolekce.

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

## <a name="unstructured-and-conditional-messages"></a>Nestrukturované a podmíněné zprávy

Protobuf je formát zasílání zpráv prvního kontraktu a zprávy aplikací je potřeba zadat v souboru *. proto* soubory, které jsou sestavené v aplikaci. V případě pokročilých scénářů nabízí Protobuf jazykové funkce a známé typy pro podporu podmíněných a neznámých zpráv.

### <a name="any"></a>Všechny

`Any`Typ umožňuje používat zprávy jako vložené typy bez definice *.* Chcete-li použít `Any` typ, importujte `any.proto` .

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

`oneof` pole jsou funkcí jazyka. Kompilátor zpracovává `oneof` klíčové slovo, když generuje třídu zprávy. Pomocí `oneof` můžete zadat zprávu odpovědi, která by mohla vracet, `Person` nebo `Error` může vypadat takto:

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

Pole v `oneof` sadě musí mít v celkové deklaraci zprávy jedinečné číselné pole.

Při použití `oneof` , vygenerovaný kód jazyka C# obsahuje výčet, který určuje, která z polí byla nastavena. Chcete-li zjistit, které pole je nastaveno, můžete otestovat výčet. Pole, která nejsou nastavena jako návratová `null` hodnota nebo výchozí hodnotu, namísto vyvolání výjimky.

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

### <a name="value"></a>Hodnota

`Value`Typ představuje dynamicky typované hodnoty. Může to být buď `null` číslo, řetězec, logická hodnota, slovník hodnot ( `Struct` ) nebo seznam hodnot ( `ValueList` ). `Value` je dobře známý typ, který používá dříve popisovanou `oneof` funkci. Chcete-li použít `Value` typ, importujte `struct.proto` .

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

`Value`Přímé použití může být podrobné. Alternativním způsobem, jak použít, `Value` je integrovanou podporu Protobuf pro mapování zpráv do formátu JSON. Protobuf `JsonFormatter` a `JsonWriter` typy lze použít s libovolnou Protobuf zprávou. `Value` je vhodný zejména pro převod na JSON a z něj.

Toto je ekvivalent JSON předchozího kódu:

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

## <a name="additional-resources"></a>Další zdroje

* [Průvodce jazyky Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
