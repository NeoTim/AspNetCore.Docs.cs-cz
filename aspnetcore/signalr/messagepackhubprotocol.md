---
title: Použijte protokol MessagePack hub v nástroji Signal pro ASP.NET Core
author: bradygaster
description: Přidejte protokol MessagePack hub pro ASP.NET Core Signal.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/08/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: fe09b646eba5ae15cbd9e568b276aaf7763e4b1b
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165396"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>Použijte protokol MessagePack hub v nástroji Signal pro ASP.NET Core

Od [Brennan Conroy](https://github.com/BrennanConroy)

Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co je MessagePack?

[MessagePack](https://msgpack.org/index.html) je binární Serializační formát, který je rychlý a kompaktní. To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/). Vzhledem k tomu, že se jedná o binární formát, zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru. Signalizace má vestavěnou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.

## <a name="configure-messagepack-on-the-server"></a>Konfigurace MessagePack na serveru

Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte do své aplikace balíček `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`. V souboru Startup.cs přidejte do volání `AddSignalR` `AddMessagePackProtocol`, aby se povolila podpora MessagePack na serveru.

> [!NOTE]
> Ve výchozím nastavení je povolený formát JSON. Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pokud chcete přizpůsobit, jak bude MessagePack formátovat vaše data, `AddMessagePackProtocol` převezme delegáta pro konfiguraci možností. V tomto delegátu lze použít vlastnost `FormatterResolvers` ke konfiguraci možností serializace MessagePack. Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.

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

## <a name="configure-messagepack-on-the-client"></a>Konfigurace MessagePack na klientovi

> [!NOTE]
> Formát JSON je ve výchozím nastavení povolen pro podporované klienty. Klienti můžou podporovat jenom jeden protokol. Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.

### <a name="net-client"></a>Klient .NET

Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte balíček `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Toto volání `AddMessagePackProtocol` přebírá delegáta pro konfiguraci možností, jako je server.

### <a name="javascript-client"></a>Klient JavaScriptu

::: moniker range=">= aspnetcore-3.0"

Podpora MessagePack pro klienta JavaScriptu je poskytována balíčkem npm `@microsoft/signalr-protocol-msgpack`. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Podpora MessagePack pro klienta JavaScriptu je poskytována balíčkem npm `@aspnet/signalr-protocol-msgpack`. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:

::: moniker range=">= aspnetcore-3.0"

*node_modules @ no__t-1 @ no__t-2* 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*node_modules @ no__t-1 @ no__t-2* 

::: moniker-end

V prohlížeči musí být také odkazována knihovna `msgpack5`. Chcete-li vytvořit odkaz, použijte značku `<script>`. Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Při použití prvku `<script>` je pořadí důležité. Pokud se na *SignalR-Protocol-msgpack. js* odkazuje před *msgpack5. js*, při pokusu o připojení pomocí MessagePack dojde k chybě. *návěstí. js* je také vyžadováno před *SignalR-Protocol-msgpack. js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` nakonfiguruje klienta k používání protokolu MessagePack při připojování k serveru.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.

## <a name="messagepack-quirks"></a>MessagePack adaptivní

Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.

### <a name="messagepack-is-case-sensitive"></a>MessagePack rozlišuje velká a malá písmena.

Protokol MessagePack rozlišuje velká a malá písmena. Zvažte například následující C# třídu:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Při odesílání z klienta jazyka JavaScript je nutné použít názvy vlastností `PascalCased`, protože velikost písmen se musí přesně shodovat C# s třídou. Příklad:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Použití názvů `camelCased` se nebude správně navazovat C# na třídu. Můžete to obejít tak, že pomocí atributu `Key` zadáte jiný název vlastnosti MessagePack. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind se nezachová při serializaci nebo deserializaci.

Protokol MessagePack neposkytuje způsob, jak zakódovat hodnotu `Kind` `DateTime`. Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC. Pokud pracujete s hodnotami `DateTime` v místním čase, doporučujeme před odesláním převést na čas UTC. Převeďte je ze standardu UTC na místní čas, když je přijmete.

Další informace o tomto omezení najdete v tématu problém GitHub [ASPNET/Signal # 2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue není podporován MessagePack v JavaScriptu.

Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem jazyka JavaScript nástroje Signal nepodporuje typ `timestamp96` v MessagePack. Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti). Hodnota `DateTime.MinValue` je `January 1, 0001`, která musí být kódována v hodnotě `timestamp96`. Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje. Když klient jazyka JavaScript obdrží `DateTime.MinValue`, je vyvolána následující chyba:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

K zakódování hodnoty "chybí" nebo `null` se obvykle používá `DateTime.MinValue`. Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte hodnotu Nullable @no__t 0 (`DateTime?`) nebo zakódovat samostatnou hodnotu `bool`, která označuje, jestli je k dispozici datum.

Další informace o tomto omezení najdete v tématu problém GitHub [ASPNET/Signal # 2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Podpora MessagePack v prostředí kompilace "před a za běhu"

Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace. V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity). V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports). Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného do `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Kontroly typů jsou v MessagePack přísnější.

Protokol centra JSON provede převody typů během deserializace. Například Pokud příchozí objekt má hodnotu vlastnosti, která je číslo (`{ foo: 42 }`), ale vlastnost třídy .NET je typu `string`, hodnota bude převedena. MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Další informace o tomto omezení najdete v tématu problém GitHub [ASPNET/Signal # 2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)
