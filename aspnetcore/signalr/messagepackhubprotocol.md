---
title: Protokol MessagePack rozbočovače signalr pro ASP.NET Core
author: bradygaster
description: Přidáte protokol MessagePack rozbočovače SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 2852ca93c62e706e9a5203625822c2fb954fd2b8
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835606"
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

### <a name="net-client"></a>.NET client

Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíčku a volání `AddMessagePackProtocol` na `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> To `AddMessagePackProtocol` trvá volání delegáta pro konfiguraci možností, stejně jako na serveru.

### <a name="javascript-client"></a>Javascriptový klient

Poskytuje MessagePack podporu pro Javascript klienta `@aspnet/signalr-protocol-msgpack` balíčku NPM.

```console
npm install @aspnet/signalr-protocol-msgpack
```

Po instalaci balíčku npm, lze použít přímo prostřednictvím zavaděč modulu JavaScript modulu nebo importovat do prohlížeče odkazováním *node_modules\\ @aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*  souboru. V prohlížeči `msgpack5` knihovny musí také odkazovat. Použití `<script>` značku k vytvoření odkazu. Knihovnu lze nalézt v *node_modules\msgpack5\dist\msgpack5.js*.

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

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)
