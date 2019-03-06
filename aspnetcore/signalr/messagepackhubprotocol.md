---
title: Protokol MessagePack rozbočovače signalr pro ASP.NET Core
author: bradygaster
description: Přidáte protokol MessagePack rozbočovače SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/27/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7742f6f8bb53fb3c299ff98ae52a0da519ff396c
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400668"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>Protokol MessagePack rozbočovače signalr pro ASP.NET Core

Podle [Brennan Conroy](https://github.com/BrennanConroy)

Tento článek předpokládá čtecí modul se seznámit s tématy v [Začínáme](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co je MessagePack?

[MessagePack](https://msgpack.org/index.html) je binární serializační formát, který je rychlý a compact. To je užitečné, když výkonu a šířky pásma jsou důležité, protože vytváří menší zprávy ve srovnání s [JSON](https://www.json.org/). Vzhledem k tomu, že je binární formát, jsou zprávy nejde přečíst, při prohlížení protokoly a trasování sítě, pokud počet bajtů se předaly MessagePack analyzátor. Funkce SignalR obsahuje integrovanou podporu pro formát MessagePack a poskytuje rozhraní API pro klienty a server používat.

## <a name="configure-messagepack-on-the-server"></a>Konfigurace MessagePack na serveru

Pokud chcete povolit protokol MessagePack rozbočovače na serveru, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček ve vaší aplikaci. V souboru Startup.cs přidat `AddMessagePackProtocol` k `AddSignalR` volání k povolení podpory MessagePack na serveru.

> [!NOTE]
> JSON je standardně povolená. Přidání MessagePack umožňuje podporu pro JSON a MessagePack klienty.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Přizpůsobit, jak bude MessagePack formátovat data, `AddMessagePackProtocol` přijímá delegát pro konfiguraci možností. V delegátu `FormatterResolvers` vlastnost lze použít ke konfiguraci možností MessagePack serializace. Další informace o fungování překladače v knihovně MessagePack na [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributy lze použít na objekty, které chcete serializovat k definování, jak by měl být zpracována.

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

## <a name="configure-messagepack-on-the-client"></a>MessagePack konfigurace na straně klienta

> [!NOTE]
> JSON je ve výchozím nastavení povolena pro Podporovaní klienti. Klienti podporují pouze jeden protokol. Přidání podpory MessagePack nahradí všechny dřív nakonfigurované protokoly.

### <a name="net-client"></a>Klient .NET

Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíčku a volání `AddMessagePackProtocol` na `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> To `AddMessagePackProtocol` trvá volání delegáta pro konfiguraci možností, stejně jako na serveru.

### <a name="javascript-client"></a>Klient JavaScriptu

Poskytuje MessagePack podporu pro JavaScript klienta `@aspnet/signalr-protocol-msgpack` balíčku npm.

```console
npm install @aspnet/signalr-protocol-msgpack
```

Po instalaci balíčku npm, lze použít přímo prostřednictvím zavaděč modulu JavaScript modulu nebo importovat do prohlížeče odkazováním *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* souboru. V prohlížeči `msgpack5` knihovny musí také odkazovat. Použití `<script>` značku k vytvoření odkazu. Knihovnu lze nalézt v *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Při použití `<script>` prvku, pořadí je důležité. Pokud *signalr-protocol-msgpack.js* odkazuje před *msgpack5.js*, dojde k chybě při pokusu o připojení s MessagePack. *signalr.js* je také nutné před *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` k `HubConnectionBuilder` nakonfiguruje klienta pro použití protokolu MessagePack při připojování k serveru.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack na klientovi JavaScript.

## <a name="messagepack-quirks"></a>Adaptivní MessagePack

Existuje několik problémů, je potřeba vědět při použití protokolu MessagePack rozbočovače.

### <a name="messagepack-is-case-sensitive"></a>MessagePack je malá a velká písmena

Protokol MessagePack rozlišuje velká a malá písmena. Představte si třeba následující C# třídy:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Při odesílání z Javascriptového klienta, je nutné použít `PascalCased` názvy vlastností, protože se musí shodovat velká a malá písmena C# přesně třídy. Příklad:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Pomocí `camelCased` názvy nebudou správně vázat na C# třídy. Můžete alternativně vyřešit to s využitím `Key` atribut zadejte jiný název pro vlastnost MessagePack. Další informace najdete v tématu [dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind se nezachová při serializaci/deserializaci

MessagePack protokol neposkytuje způsob, jak kódovat `Kind` hodnotu `DateTime`. V důsledku toho při deserializaci datum, MessagePack centra protokol se předpokládá, že příchozí datum je ve formátu UTC. Pokud pracujete s `DateTime` hodnoty v místním čase, doporučujeme převod na standard UTC před jejich odesláním. Převod je od času UTC na místní čas při jejich obdržení.

Další informace o tomto omezení, najdete na Githubu problém [aspnet/SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue nepodporuje MessagePack v jazyce JavaScript

[Msgpack5](https://github.com/mcollina/msgpack5) používaných klientem SignalR JavaScript library nepodporuje `timestamp96` zadejte MessagePack. Tento typ se používá ke kódování velmi velké datové hodnoty (buď velmi brzy v minulosti nebo velmi úplně v budoucnosti). Hodnota `DateTime.MinValue` je `January 1, 0001` musí být kódovány ve `timestamp96` hodnotu. Z důvodu této, odesílání `DateTime.MinValue` pro JavaScript klienta nepodporuje. Když `DateTime.MinValue` přijetí klientem jazyka JavaScript, je vyvolána následující chybu:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Obvykle `DateTime.MinValue` slouží ke kódování "nebyl nalezen" nebo `null` hodnotu. Pokud je potřeba tuto hodnotu v MessagePack kódování, použijte s povolenou hodnotou Null `DateTime` hodnotu (`DateTime?`) nebo kódování samostatné `bool` hodnotu, která pokud je k dispozici data.

Další informace o tomto omezení, najdete na Githubu problém [aspnet/SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Podpora MessagePack v prostředí "ahead-of-time" kompilace

[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) knihovny používá klient .NET a server používá k optimalizaci serializace generování kódu. V důsledku toho se nepodporuje ve výchozím nastavení v prostředí, které používá "ahead-of-time" kompilace (jako je Unity nebo Xamarin pro iOS). Je možné použít MessagePack vygenerováním"před" serializátor/deserializátor kód v těchto prostředích. Další informace najdete v tématu [dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports). Po vygenerování serializátory předem, můžete zaregistrovat pomocí konfigurace delegát předaný `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Typ kontroly jsou přísnější v MessagePack

JSON centra protokol provede převody typů během deserializace. Například pokud má hodnotu vlastnosti příchozí objekt, který je číslo (`{ foo: 42 }`), ale vlastnost ve třídě .NET je typu `string`, převede se hodnota. MessagePack však nebude vykoná tento převod a vyvolá výjimku, která můžete zobrazit v protokolech na straně serveru (a v konzole):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Další informace o tomto omezení, najdete na Githubu problém [aspnet/SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)
