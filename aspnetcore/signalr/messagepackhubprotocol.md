---
title: Použití protokolu Centra SignalR MessagePack pro ASP.NET core
author: bradygaster
description: Přidejte protokol MessagePack SignalRHub do ASP.NET core .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277233"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>Použití protokolu Centra SignalR MessagePack pro ASP.NET core

::: moniker range=">= aspnetcore-5.0"

Tento článek předpokládá, že čtenář je obeznámen s tématy, na které se vztahuje [v Začínáme](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co je MessagePack?

[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární serializace formátu. Je to užitečné, když výkon a šířka pásma jsou problém, protože vytváří menší zprávy ve srovnání s [JSON](https://www.json.org/). Binární zprávy jsou nečitelné při pohledu na trasování sítě a protokoly, pokud bajty jsou předány prostřednictvím analyzátormessagepack. SignalRMá integrovanou podporu formátu MessagePack a poskytuje api pro klienta a server k použití.

## <a name="configure-messagepack-on-the-server"></a>Konfigurace programu MessagePack na serveru

Chcete-li povolit protokol MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` na serveru, nainstalujte balíček do aplikace. V `Startup.ConfigureServices` metodě `AddMessagePackProtocol` přidejte `AddSignalR` do volání povolit messagepack podporu na serveru.

> [!NOTE]
> JSON je ve výchozím nastavení povolen. Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Chcete-li přizpůsobit, jak MessagePack bude formátovat data, `AddMessagePackProtocol` trvá delegát pro konfiguraci možností. V tomto delegáta `SerializerOptions` vlastnost lze použít ke konfiguraci MessagePack možnosti serializace. Další informace o tom, jak překladače fungují, naleznete v knihovně MessagePack na [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributy lze použít na objekty, které chcete serializovat definovat, jak by měly být zpracovány.

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
> Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít doporučené opravy. Například volání `.WithSecurity(MessagePackSecurity.UntrustedData)` při výměně `SerializerOptions`.

## <a name="configure-messagepack-on-the-client"></a>Konfigurace balíčku MessagePack v klientovi

> [!NOTE]
> JSON je ve výchozím nastavení povolen pro podporované klienty. Klienti mohou podporovat pouze jeden protokol. Přidání podpory messagepack nahradí všechny dříve nakonfigurované protokoly.

### <a name="net-client"></a>Klient .NET

Chcete-li povolit messagepack v `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` klientovi `AddMessagePackProtocol` .NET, nainstalujte balíček a volejte na `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Toto `AddMessagePackProtocol` volání trvá delegátpro konfiguraci možností, stejně jako server.

### <a name="javascript-client"></a>Klient JavaScriptu

Podpora messagepacku pro klienta JavaScriptu je poskytována balíčkem [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Po instalaci balíčku npm lze modul použít přímo přes javascriptový zavaděč modulu nebo importovat do prohlížeče odkazem na následující soubor:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

V prohlížeči `msgpack5` musí být také odkazováno na knihovnu. K `<script>` vytvoření odkazu použijte značku. Knihovnu naleznete na *adrese node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Při použití `<script>` prvku je důležité pořadí. Pokud *signalr-protocol-msgpack.js* odkazuje před *msgpack5.js*, dojde k chybě při pokusu o připojení s MessagePack. *signalr.js* je také vyžadován před *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` programu nakonfiguruje klienta tak, aby při připojování k serveru používal protokol MessagePack.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> V současné době neexistují žádné možnosti konfigurace pro messagepack protokol na klienta JavaScript.

## <a name="messagepack-quirks"></a>MessagePack vtípky

Existuje několik problémů, které je třeba vědět při použití messagepack hub protokolu.

### <a name="messagepack-is-case-sensitive"></a>MessagePack rozlišuje malá a velká písmena

MessagePack protokol je malá a velká písmena. Zvažte například následující třídu C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Při odesílání z klienta JavaScript, musíte použít `PascalCased` názvy vlastností, protože kryt musí přesně odpovídat c# třídy. Příklad:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Pomocí `camelCased` názvů nebude správně svázat na C# třídy. Můžete tento problém obejít pomocí atributu `Key` k určení jiného názvu vlastnosti MessagePack. Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind není zachována při serializaci/deserializaci

MessagePack protokol neposkytuje způsob, jak kódovat `Kind` hodnotu . `DateTime` V důsledku toho při rekonstrukci data messagepack hub protokol převede do formátu `DateTime.Kind` `DateTimeKind.Local` UTC, pokud je jinak nebude dotýkat času a předat tak, jak je. Pokud pracujete s `DateTime` hodnotami, doporučujeme před jejich odesláním převést na čas UTC. Převeďte je z Času UTC na místní čas, když je obdržíte.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue není podporovánmessagepack v Jazyce JavaScript

Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem SignalR Jazyka JavaScript nepodporuje typ v messagepacku. `timestamp96` Tento typ se používá ke kódování velmi velké hodnoty kalendářních dat (buď velmi brzy v minulosti nebo velmi daleko v budoucnosti). Hodnota `DateTime.MinValue` je `January 1, 0001`, která musí být zakódována v hodnotě. `timestamp96` Z tohoto důvodu `DateTime.MinValue` není podporováno odesílání do klienta JavaScriptu. Když `DateTime.MinValue` je přijat klientem JavaScript, je vyvolána následující chyba:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo `null` hodnota. Pokud potřebujete kódovat tuto hodnotu v MessagePack,`DateTime?`použijte hodnotu s `bool` hodnotou `DateTime` null ( ) nebo zakódujte samostatnou hodnotu označující, zda je datum přítomno.

Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Podpora messagepacku v prostředí kompilace "ahead-of-time"

Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) používaná klientem a serverem .NET používá generování kódu k optimalizaci serializace. V důsledku toho není ve výchozím nastavení podporována v prostředích, která používají kompilaci "ahead-of-time" (například Xamarin iOS nebo Unity). Je možné použít MessagePack v těchto prostředích "pre-generating" serializátor/deserializer kód. Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Jakmile jste předem vygenerovali serializátory, můžete je `AddMessagePackProtocol`zaregistrovat pomocí delegáta konfigurace předaný :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Typ kontroly jsou přísnější v MessagePack

Protokol JSON Hub bude během deserializace provádět převody typů. Například pokud příchozí objekt má hodnotu vlastnosti,`{ foo: 42 }`která je číslo ( ), ale `string`vlastnost na .NET třída je typu , hodnota bude převedena. MessagePack však neprovede tento převod a vyvolá výjimku, která může být vidět v protokolech na straně serveru (a v konzoli):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient rozhraní .NET](xref:signalr/dotnet-client)
* [Javascriptový klient](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Tento článek předpokládá, že čtenář je obeznámen s tématy, na které se vztahuje [v Začínáme](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co je MessagePack?

[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární serializace formátu. Je to užitečné, když výkon a šířka pásma jsou problém, protože vytváří menší zprávy ve srovnání s [JSON](https://www.json.org/). Binární zprávy jsou nečitelné při pohledu na trasování sítě a protokoly, pokud bajty jsou předány prostřednictvím analyzátormessagepack. SignalRMá integrovanou podporu formátu MessagePack a poskytuje api pro klienta a server k použití.

## <a name="configure-messagepack-on-the-server"></a>Konfigurace programu MessagePack na serveru

Chcete-li povolit protokol MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` na serveru, nainstalujte balíček do aplikace. V `Startup.ConfigureServices` metodě `AddMessagePackProtocol` přidejte `AddSignalR` do volání povolit messagepack podporu na serveru.

> [!NOTE]
> JSON je ve výchozím nastavení povolen. Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Chcete-li přizpůsobit, jak MessagePack bude formátovat data, `AddMessagePackProtocol` trvá delegát pro konfiguraci možností. V tomto delegáta `FormatterResolvers` vlastnost lze použít ke konfiguraci MessagePack možnosti serializace. Další informace o tom, jak překladače fungují, naleznete v knihovně MessagePack na [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributy lze použít na objekty, které chcete serializovat definovat, jak by měly být zpracovány.

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
> Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít doporučené opravy. Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData`. Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [1.9.x verze MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Instalace `MessagePack` verze 1.9.x SignalR upgraduje verzi. Není-li `MessagePackSecurity.Active` nastavena možnost `MessagePackSecurity.UntrustedData`, může klient se zlými úmysly způsobit odmítnutí služby. Set `MessagePackSecurity.Active` `Program.Main`in , jak je znázorněno v následujícím kódu:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurace balíčku MessagePack v klientovi

> [!NOTE]
> JSON je ve výchozím nastavení povolen pro podporované klienty. Klienti mohou podporovat pouze jeden protokol. Přidání podpory messagepack nahradí všechny dříve nakonfigurované protokoly.

### <a name="net-client"></a>Klient .NET

Chcete-li povolit messagepack v `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` klientovi `AddMessagePackProtocol` .NET, nainstalujte balíček a volejte na `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Toto `AddMessagePackProtocol` volání trvá delegátpro konfiguraci možností, stejně jako server.

### <a name="javascript-client"></a>Klient JavaScriptu

Podpora messagepacku pro klienta JavaScriptu je poskytována balíčkem [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Po instalaci balíčku npm lze modul použít přímo přes javascriptový zavaděč modulu nebo importovat do prohlížeče odkazem na následující soubor:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

V prohlížeči `msgpack5` musí být také odkazováno na knihovnu. K `<script>` vytvoření odkazu použijte značku. Knihovnu naleznete na *adrese node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Při použití `<script>` prvku je důležité pořadí. Pokud *signalr-protocol-msgpack.js* odkazuje před *msgpack5.js*, dojde k chybě při pokusu o připojení s MessagePack. *signalr.js* je také vyžadován před *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` programu nakonfiguruje klienta tak, aby při připojování k serveru používal protokol MessagePack.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> V současné době neexistují žádné možnosti konfigurace pro messagepack protokol na klienta JavaScript.

## <a name="messagepack-quirks"></a>MessagePack vtípky

Existuje několik problémů, které je třeba vědět při použití messagepack hub protokolu.

### <a name="messagepack-is-case-sensitive"></a>MessagePack rozlišuje malá a velká písmena

MessagePack protokol je malá a velká písmena. Zvažte například následující třídu C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Při odesílání z klienta JavaScript, musíte použít `PascalCased` názvy vlastností, protože kryt musí přesně odpovídat c# třídy. Příklad:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Pomocí `camelCased` názvů nebude správně svázat na C# třídy. Můžete tento problém obejít pomocí atributu `Key` k určení jiného názvu vlastnosti MessagePack. Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind není zachována při serializaci/deserializaci

MessagePack protokol neposkytuje způsob, jak kódovat `Kind` hodnotu . `DateTime` V důsledku toho při rekonstrukci data protokol MessagePack Hub protokol předpokládá, že příchozí datum je ve formátu UTC. Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před jejich odesláním převést na čas UTC. Převeďte je z Času UTC na místní čas, když je obdržíte.

Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue není podporovánmessagepack v Jazyce JavaScript

Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem SignalR Jazyka JavaScript nepodporuje typ v messagepacku. `timestamp96` Tento typ se používá ke kódování velmi velké hodnoty kalendářních dat (buď velmi brzy v minulosti nebo velmi daleko v budoucnosti). Hodnota `DateTime.MinValue` je `January 1, 0001`, která musí být zakódována v hodnotě. `timestamp96` Z tohoto důvodu `DateTime.MinValue` není podporováno odesílání do klienta JavaScriptu. Když `DateTime.MinValue` je přijat klientem JavaScript, je vyvolána následující chyba:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo `null` hodnota. Pokud potřebujete kódovat tuto hodnotu v MessagePack,`DateTime?`použijte hodnotu s `bool` hodnotou `DateTime` null ( ) nebo zakódujte samostatnou hodnotu označující, zda je datum přítomno.

Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Podpora messagepacku v prostředí kompilace "ahead-of-time"

Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) používaná klientem a serverem .NET používá generování kódu k optimalizaci serializace. V důsledku toho není ve výchozím nastavení podporována v prostředích, která používají kompilaci "ahead-of-time" (například Xamarin iOS nebo Unity). Je možné použít MessagePack v těchto prostředích "pre-generating" serializátor/deserializer kód. Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Jakmile jste předem vygenerovali serializátory, můžete je `AddMessagePackProtocol`zaregistrovat pomocí delegáta konfigurace předaný :

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

Protokol JSON Hub bude během deserializace provádět převody typů. Například pokud příchozí objekt má hodnotu vlastnosti,`{ foo: 42 }`která je číslo ( ), ale `string`vlastnost na .NET třída je typu , hodnota bude převedena. MessagePack však neprovede tento převod a vyvolá výjimku, která může být vidět v protokolech na straně serveru (a v konzoli):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient rozhraní .NET](xref:signalr/dotnet-client)
* [Javascriptový klient](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek předpokládá, že čtenář je obeznámen s tématy, na které se vztahuje [v Začínáme](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co je MessagePack?

[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární serializace formátu. Je to užitečné, když výkon a šířka pásma jsou problém, protože vytváří menší zprávy ve srovnání s [JSON](https://www.json.org/). Binární zprávy jsou nečitelné při pohledu na trasování sítě a protokoly, pokud bajty jsou předány prostřednictvím analyzátormessagepack. SignalRMá integrovanou podporu formátu MessagePack a poskytuje api pro klienta a server k použití.

## <a name="configure-messagepack-on-the-server"></a>Konfigurace programu MessagePack na serveru

Chcete-li povolit protokol MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` na serveru, nainstalujte balíček do aplikace. V `Startup.ConfigureServices` metodě `AddMessagePackProtocol` přidejte `AddSignalR` do volání povolit messagepack podporu na serveru.

> [!NOTE]
> JSON je ve výchozím nastavení povolen. Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Chcete-li přizpůsobit, jak MessagePack bude formátovat data, `AddMessagePackProtocol` trvá delegát pro konfiguraci možností. V tomto delegáta `FormatterResolvers` vlastnost lze použít ke konfiguraci MessagePack možnosti serializace. Další informace o tom, jak překladače fungují, naleznete v knihovně MessagePack na [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributy lze použít na objekty, které chcete serializovat definovat, jak by měly být zpracovány.

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
> Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít doporučené opravy. Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData`. Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [1.9.x verze MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Instalace `MessagePack` verze 1.9.x SignalR upgraduje verzi. Není-li `MessagePackSecurity.Active` nastavena možnost `MessagePackSecurity.UntrustedData`, může klient se zlými úmysly způsobit odmítnutí služby. Set `MessagePackSecurity.Active` `Program.Main`in , jak je znázorněno v následujícím kódu:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurace balíčku MessagePack v klientovi

> [!NOTE]
> JSON je ve výchozím nastavení povolen pro podporované klienty. Klienti mohou podporovat pouze jeden protokol. Přidání podpory messagepack nahradí všechny dříve nakonfigurované protokoly.

### <a name="net-client"></a>Klient .NET

Chcete-li povolit messagepack v `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` klientovi `AddMessagePackProtocol` .NET, nainstalujte balíček a volejte na `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Toto `AddMessagePackProtocol` volání trvá delegátpro konfiguraci možností, stejně jako server.

### <a name="javascript-client"></a>Klient JavaScriptu

Podpora messagepacku pro klienta JavaScriptu je poskytována balíčkem [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm. Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Po instalaci balíčku npm lze modul použít přímo přes javascriptový zavaděč modulu nebo importovat do prohlížeče odkazem na následující soubor:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

V prohlížeči `msgpack5` musí být také odkazováno na knihovnu. K `<script>` vytvoření odkazu použijte značku. Knihovnu naleznete na *adrese node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Při použití `<script>` prvku je důležité pořadí. Pokud *signalr-protocol-msgpack.js* odkazuje před *msgpack5.js*, dojde k chybě při pokusu o připojení s MessagePack. *signalr.js* je také vyžadován před *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` programu nakonfiguruje klienta tak, aby při připojování k serveru používal protokol MessagePack.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> V současné době neexistují žádné možnosti konfigurace pro messagepack protokol na klienta JavaScript.

## <a name="messagepack-quirks"></a>MessagePack vtípky

Existuje několik problémů, které je třeba vědět při použití messagepack hub protokolu.

### <a name="messagepack-is-case-sensitive"></a>MessagePack rozlišuje malá a velká písmena

MessagePack protokol je malá a velká písmena. Zvažte například následující třídu C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Při odesílání z klienta JavaScript, musíte použít `PascalCased` názvy vlastností, protože kryt musí přesně odpovídat c# třídy. Příklad:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Pomocí `camelCased` názvů nebude správně svázat na C# třídy. Můžete tento problém obejít pomocí atributu `Key` k určení jiného názvu vlastnosti MessagePack. Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind není zachována při serializaci/deserializaci

MessagePack protokol neposkytuje způsob, jak kódovat `Kind` hodnotu . `DateTime` V důsledku toho při rekonstrukci data protokol MessagePack Hub protokol předpokládá, že příchozí datum je ve formátu UTC. Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před jejich odesláním převést na čas UTC. Převeďte je z Času UTC na místní čas, když je obdržíte.

Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue není podporovánmessagepack v Jazyce JavaScript

Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem SignalR Jazyka JavaScript nepodporuje typ v messagepacku. `timestamp96` Tento typ se používá ke kódování velmi velké hodnoty kalendářních dat (buď velmi brzy v minulosti nebo velmi daleko v budoucnosti). Hodnota `DateTime.MinValue` is, `January 1, 0001` která musí být zakódována v hodnotě. `timestamp96` Z tohoto důvodu `DateTime.MinValue` není podporováno odesílání do klienta JavaScriptu. Když `DateTime.MinValue` je přijat klientem JavaScript, je vyvolána následující chyba:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo `null` hodnota. Pokud potřebujete kódovat tuto hodnotu v MessagePack,`DateTime?`použijte hodnotu s `bool` hodnotou `DateTime` null ( ) nebo zakódujte samostatnou hodnotu označující, zda je datum přítomno.

Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Podpora messagepacku v prostředí kompilace "ahead-of-time"

Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) používaná klientem a serverem .NET používá generování kódu k optimalizaci serializace. V důsledku toho není ve výchozím nastavení podporována v prostředích, která používají kompilaci "ahead-of-time" (například Xamarin iOS nebo Unity). Je možné použít MessagePack v těchto prostředích "pre-generating" serializátor/deserializer kód. Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Jakmile jste předem vygenerovali serializátory, můžete je `AddMessagePackProtocol`zaregistrovat pomocí delegáta konfigurace předaný :

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

Protokol JSON Hub bude během deserializace provádět převody typů. Například pokud příchozí objekt má hodnotu vlastnosti,`{ foo: 42 }`která je číslo ( ), ale `string`vlastnost na .NET třída je typu , hodnota bude převedena. MessagePack však neprovede tento převod a vyvolá výjimku, která může být vidět v protokolech na straně serveru (a v konzoli):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient rozhraní .NET](xref:signalr/dotnet-client)
* [Javascriptový klient](xref:signalr/javascript-client)

::: moniker-end
