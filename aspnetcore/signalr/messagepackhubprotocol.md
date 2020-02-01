---
title: Použijte protokol MessagePack hub v SignalR pro ASP.NET Core
author: bradygaster
description: Přidejte protokol MessagePack hub pro ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 3c2a4285945d3fdc6bba195e3160da8b9dcbba44
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928178"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>Použijte protokol MessagePack hub v SignalR pro ASP.NET Core

Od [Brennan Conroy](https://github.com/BrennanConroy)

Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co je MessagePack?

[MessagePack](https://msgpack.org/index.html) je binární Serializační formát, který je rychlý a kompaktní. To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/). Vzhledem k tomu, že se jedná o binární formát, zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru. SignalR má integrovanou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.

## <a name="configure-messagepack-on-the-server"></a>Konfigurace MessagePack na serveru

Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte do své aplikace balíček `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`. Do souboru Startup.cs přidejte `AddMessagePackProtocol` volání `AddSignalR` a povolte podporu MessagePack na serveru.

> [!NOTE]
> Ve výchozím nastavení je povolený formát JSON. Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pokud chcete přizpůsobit způsob, jakým aplikace MessagePack naformátuje data, `AddMessagePackProtocol` převezme delegáta pro konfiguraci možností. V tomto delegátu lze vlastnost `FormatterResolvers` použít ke konfiguraci možností serializace MessagePack. Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.

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
> Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy. Například nastavení vlastnosti `MessagePackSecurity.Active` static na `MessagePackSecurity.UntrustedData`. Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [MessagePack verze 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3). Instalace `MessagePack` 1.9. x upgraduje SignalR používá verzi. Pokud `MessagePackSecurity.Active` není nastavená na `MessagePackSecurity.UntrustedData`, může škodlivý klient způsobit odepření služby. Nastavte `MessagePackSecurity.Active` v `Program.Main`, jak je znázorněno v následujícím kódu:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
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
> Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.

### <a name="javascript-client"></a>Klient JavaScriptu

::: moniker range=">= aspnetcore-3.0"

Podpora MessagePack pro klienta JavaScriptu je poskytována balíčkem `@microsoft/signalr-protocol-msgpack` npm. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Podpora MessagePack pro klienta JavaScriptu je poskytována balíčkem `@aspnet/signalr-protocol-msgpack` npm. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:

::: moniker range=">= aspnetcore-3.0"

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

::: moniker-end

V prohlížeči musí být také odkazována na knihovnu `msgpack5`. Použijte značku `<script>` k vytvoření odkazu. Knihovnu najdete na adrese *node_modules \msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Při použití prvku `<script>` je pořadí důležité. Pokud se na *SignalR-Protocol-msgpack. js* odkazuje před *msgpack5. js*, při pokusu o připojení pomocí MessagePack dojde k chybě. *návěstí. js* je také vyžadováno před *SignalR-Protocol-msgpack. js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` nakonfiguruje klienta tak, aby používal protokol MessagePack při připojování k serveru.

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

Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvů vlastností, protože velikost písmen se musí přesně shodovat C# s třídou. Příklad:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Použití názvů `camelCased` se nebude správně navazovat C# na třídu. Můžete to obejít pomocí atributu `Key` a zadat jiný název pro vlastnost MessagePack. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind se nezachová při serializaci nebo deserializaci.

Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime`. Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC. Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před odesláním převést na čas UTC. Převeďte je ze standardu UTC na místní čas, když je přijmete.

Další informace o tomto omezení najdete v tématu věnovaném problému GitHubu [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue není podporován MessagePack v JavaScriptu.

Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem SignalR JavaScript nepodporuje typ `timestamp96` v MessagePack. Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti). Hodnota `DateTime.MinValue` je `January 1, 0001`, která musí být kódována v `timestamp96` hodnotě. Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje. Když klient jazyka JavaScript obdrží `DateTime.MinValue`, je vyvolána následující chyba:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

K zakódování chybějící nebo `null`é hodnoty se obvykle používá `DateTime.MinValue`. Pokud potřebujete tuto hodnotu v MessagePack kódovat, použijte `DateTime` hodnotu s možnou hodnotou null (`DateTime?`) nebo zakódováním samostatné `bool` hodnoty, která označuje, jestli je k dispozici datum.

Další informace o tomto omezení najdete v tématu věnovaném problému GitHubu [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Podpora MessagePack v prostředí kompilace "před a za běhu"

Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace. V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity). V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports). Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol`:

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

Protokol centra JSON provede převody typů během deserializace. Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo (`{ foo: 42 }`), ale vlastnost třídy .NET je typu `string`, hodnota bude převedena. MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Další informace o tomto omezení najdete v tématu věnovaném problému GitHubu [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)
