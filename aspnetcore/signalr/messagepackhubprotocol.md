---
title: Použití protokolu MessagePack hub v SignalR pro ASP.NET Core
author: bradygaster
description: Přidejte k ASP.NET Core protokol MessagePack hub SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 79a8c98d276738f687ef484795818897f18ceded
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755804"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>Použití protokolu MessagePack hub v SignalR pro ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co je MessagePack?

[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární formát serializace. To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/). Binární zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru. SignalRmá integrovanou podporu formátu MessagePack a poskytuje rozhraní API pro použití klientem a serverem.

## <a name="configure-messagepack-on-the-server"></a>Konfigurace MessagePack na serveru

Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček do své aplikace. V `Startup.ConfigureServices` metodě přidejte `AddMessagePackProtocol` do `AddSignalR` volání, aby bylo možné povolit podporu MessagePack na serveru.

> [!NOTE]
> Ve výchozím nastavení je povolený formát JSON. Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pokud chcete přizpůsobit způsob, jakým MessagePack data naformátují, `AddMessagePackProtocol` převezme delegáta konfiguraci možností. V tomto delegátu `SerializerOptions` lze vlastnost použít ke konfiguraci možností serializace MessagePack. Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy. Například volání `.WithSecurity(MessagePackSecurity.UntrustedData)` při nahrazení `SerializerOptions` .

## <a name="configure-messagepack-on-the-client"></a>Konfigurace MessagePack na klientovi

> [!NOTE]
> Formát JSON je ve výchozím nastavení povolen pro podporované klienty. Klienti můžou podporovat jenom jeden protokol. Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.

### <a name="net-client"></a>Klient .NET

Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder` .

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.

### <a name="javascript-client"></a>Klient JavaScriptu

Podpora MessagePack pro klienta JavaScriptu je poskytována [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) balíčkem npm. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

V prohlížeči `msgpack5` musí být také odkazováno na knihovnu. `<script>`K vytvoření odkazu použijte značku. Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Při použití `<script>` elementu je pořadí důležité. Pokud je před *msgpack5.js*odkazováno na *signalr-protocol-msgpack.js* , dojde k chybě při pokusu o připojení k MessagePack. *signalr.js* se také vyžaduje před *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Při přidání do `HubConnectionBuilder` nakonfiguruje klient používání protokolu MessagePack při připojování k serveru.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.

## <a name="messagepack-quirks"></a>MessagePack adaptivní

Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.

### <a name="messagepack-is-case-sensitive"></a>MessagePack rozlišuje velká a malá písmena.

Protokol MessagePack rozlišuje velká a malá písmena. Zvažte například následující třídu jazyka C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvy vlastností, protože velikost písmen se musí přesně shodovat s třídou jazyka C#. Například:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Použití `camelCased` názvů nebude správně svázáno se třídou jazyka C#. Můžete to obejít tak, že pomocí `Key` atributu zadáte jiný název vlastnosti MessagePack. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind se nezachová při serializaci nebo deserializaci.

Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime` . V důsledku toho se při deserializaci data MessagePack hub Protocol převede do formátu UTC, pokud `DateTime.Kind` je v opačném případě se `DateTimeKind.Local` nedotkne času a předáte tak, jak je. Pokud pracujete s `DateTime` hodnotami, doporučujeme před odesláním převést na čas UTC. Převeďte je ze standardu UTC na místní čas, když je přijmete.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue není podporován MessagePack v JavaScriptu.

Knihovna [msgpack5](https://github.com/mcollina/msgpack5) , kterou používá SignalR klient JavaScriptu, nepodporuje `timestamp96` typ v MessagePack. Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti). Hodnota `DateTime.MinValue` je `January 1, 0001` , která musí být kódována v `timestamp96` hodnotě. Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje. Když `DateTime.MinValue` je klient jazyka JavaScript přijat, je vyvolána následující chyba:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo " `null` hodnoty". Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte `DateTime` hodnotu Nullable ( `DateTime?` ) nebo zakódovat samostatnou `bool` hodnotu označující, jestli je datum k dispozici.

Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Podpora MessagePack v prostředí kompilace "před a za běhu"

Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace. V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity). V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol` :

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>Kontroly typů jsou v MessagePack přísnější.

Protokol centra JSON provede převody typů během deserializace. Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo ( `{ foo: 42 }` ), ale vlastnost třídy .NET je typu `string` , hodnota bude převedena. MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co je MessagePack?

[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární formát serializace. To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/). Binární zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru. SignalRmá vestavěnou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.

## <a name="configure-messagepack-on-the-server"></a>Konfigurace MessagePack na serveru

Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček do své aplikace. V `Startup.ConfigureServices` metodě přidejte `AddMessagePackProtocol` do `AddSignalR` volání, aby bylo možné povolit podporu MessagePack na serveru.

> [!NOTE]
> Ve výchozím nastavení je povolený formát JSON. Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pokud chcete přizpůsobit způsob, jakým MessagePack data naformátují, `AddMessagePackProtocol` převezme delegáta konfiguraci možností. V tomto delegátu `FormatterResolvers` lze vlastnost použít ke konfiguraci možností serializace MessagePack. Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.

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
> Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy. Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData` . Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [verze 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Instalace `MessagePack` aktualizací 1.9. x používá verzi SignalR . Když není `MessagePackSecurity.Active` nastavená na `MessagePackSecurity.UntrustedData` , škodlivý klient může způsobit odepření služby. Nastavte `MessagePackSecurity.Active` v `Program.Main` , jak je znázorněno v následujícím kódu:

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

Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder` .

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.

### <a name="javascript-client"></a>Klient JavaScriptu

Podpora MessagePack pro klienta JavaScriptu je poskytována [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) balíčkem npm. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

V prohlížeči `msgpack5` musí být také odkazováno na knihovnu. `<script>`K vytvoření odkazu použijte značku. Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Při použití `<script>` elementu je pořadí důležité. Pokud je před *msgpack5.js*odkazováno na *signalr-protocol-msgpack.js* , dojde k chybě při pokusu o připojení k MessagePack. *signalr.js* se také vyžaduje před *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Při přidání do `HubConnectionBuilder` nakonfiguruje klient používání protokolu MessagePack při připojování k serveru.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.

## <a name="messagepack-quirks"></a>MessagePack adaptivní

Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.

### <a name="messagepack-is-case-sensitive"></a>MessagePack rozlišuje velká a malá písmena.

Protokol MessagePack rozlišuje velká a malá písmena. Zvažte například následující třídu jazyka C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvy vlastností, protože velikost písmen se musí přesně shodovat s třídou jazyka C#. Například:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Použití `camelCased` názvů nebude správně svázáno se třídou jazyka C#. Můžete to obejít tak, že pomocí `Key` atributu zadáte jiný název vlastnosti MessagePack. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind se nezachová při serializaci nebo deserializaci.

Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime` . Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC. Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před odesláním převést na čas UTC. Převeďte je ze standardu UTC na místní čas, když je přijmete.

Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue není podporován MessagePack v JavaScriptu.

Knihovna [msgpack5](https://github.com/mcollina/msgpack5) , kterou používá SignalR klient JavaScriptu, nepodporuje `timestamp96` typ v MessagePack. Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti). Hodnota `DateTime.MinValue` je `January 1, 0001` , která musí být kódována v `timestamp96` hodnotě. Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje. Když `DateTime.MinValue` je klient jazyka JavaScript přijat, je vyvolána následující chyba:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo " `null` hodnoty". Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte `DateTime` hodnotu Nullable ( `DateTime?` ) nebo zakódovat samostatnou `bool` hodnotu označující, jestli je datum k dispozici.

Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Podpora MessagePack v prostředí kompilace "před a za běhu"

Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace. V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity). V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol` :

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

Protokol centra JSON provede převody typů během deserializace. Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo ( `{ foo: 42 }` ), ale vlastnost třídy .NET je typu `string` , hodnota bude převedena. MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co je MessagePack?

[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární formát serializace. To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/). Binární zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru. SignalRmá vestavěnou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.

## <a name="configure-messagepack-on-the-server"></a>Konfigurace MessagePack na serveru

Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček do své aplikace. V `Startup.ConfigureServices` metodě přidejte `AddMessagePackProtocol` do `AddSignalR` volání, aby bylo možné povolit podporu MessagePack na serveru.

> [!NOTE]
> Ve výchozím nastavení je povolený formát JSON. Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pokud chcete přizpůsobit způsob, jakým MessagePack data naformátují, `AddMessagePackProtocol` převezme delegáta konfiguraci možností. V tomto delegátu `FormatterResolvers` lze vlastnost použít ke konfiguraci možností serializace MessagePack. Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.

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
> Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy. Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData` . Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [verze 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Instalace `MessagePack` aktualizací 1.9. x používá verzi SignalR . Když není `MessagePackSecurity.Active` nastavená na `MessagePackSecurity.UntrustedData` , škodlivý klient může způsobit odepření služby. Nastavte `MessagePackSecurity.Active` v `Program.Main` , jak je znázorněno v následujícím kódu:

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

Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder` .

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.

### <a name="javascript-client"></a>Klient JavaScriptu

Podpora MessagePack pro klienta JavaScriptu je poskytována [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) balíčkem npm. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

V prohlížeči `msgpack5` musí být také odkazováno na knihovnu. `<script>`K vytvoření odkazu použijte značku. Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Při použití `<script>` elementu je pořadí důležité. Pokud je před *msgpack5.js*odkazováno na *signalr-protocol-msgpack.js* , dojde k chybě při pokusu o připojení k MessagePack. *signalr.js* se také vyžaduje před *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Při přidání do `HubConnectionBuilder` nakonfiguruje klient používání protokolu MessagePack při připojování k serveru.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.

## <a name="messagepack-quirks"></a>MessagePack adaptivní

Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.

### <a name="messagepack-is-case-sensitive"></a>MessagePack rozlišuje velká a malá písmena.

Protokol MessagePack rozlišuje velká a malá písmena. Zvažte například následující třídu jazyka C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvy vlastností, protože velikost písmen se musí přesně shodovat s třídou jazyka C#. Například:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Použití `camelCased` názvů nebude správně svázáno se třídou jazyka C#. Můžete to obejít tak, že pomocí `Key` atributu zadáte jiný název vlastnosti MessagePack. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind se nezachová při serializaci nebo deserializaci.

Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime` . Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC. Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před odesláním převést na čas UTC. Převeďte je ze standardu UTC na místní čas, když je přijmete.

Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue není podporován MessagePack v JavaScriptu.

Knihovna [msgpack5](https://github.com/mcollina/msgpack5) , kterou používá SignalR klient JavaScriptu, nepodporuje `timestamp96` typ v MessagePack. Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti). Hodnota je, `DateTime.MinValue` `January 1, 0001` která musí být kódována v `timestamp96` hodnotě. Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje. Když `DateTime.MinValue` je klient jazyka JavaScript přijat, je vyvolána následující chyba:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo " `null` hodnoty". Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte `DateTime` hodnotu Nullable ( `DateTime?` ) nebo zakódovat samostatnou `bool` hodnotu označující, jestli je datum k dispozici.

Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Podpora MessagePack v prostředí kompilace "před a za běhu"

Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace. V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity). V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace. Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol` :

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

Protokol centra JSON provede převody typů během deserializace. Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo ( `{ foo: 42 }` ), ale vlastnost třídy .NET je typu `string` , hodnota bude převedena. MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)

::: moniker-end
