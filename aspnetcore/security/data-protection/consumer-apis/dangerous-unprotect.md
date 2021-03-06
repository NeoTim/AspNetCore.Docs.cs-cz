---
title: Zrušení ochrany datových částí, jejichž klíče byly odvolány v ASP.NET Core
author: rick-anderson
description: Naučte se, jak zrušit ochranu dat chráněných pomocí klíčů, které od nich byly odvolány, v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: a6ced7341967362ace9537ad0124ccca6fbe79b0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626698"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Zrušení ochrany datových částí, jejichž klíče byly odvolány v ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

Rozhraní API pro ochranu ASP.NET Core dat nejsou primárně určena pro neomezenou perzistenci důvěrných datových částí. Další technologie, jako je [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) a [Azure Rights Management](/rights-management/) , jsou vhodnější pro scénář neurčitého úložiště a mají odpovídající možnosti silné správy klíčů. V takovém případě nebrání vývojářům v používání rozhraní API ochrany ASP.NET Core dat pro dlouhodobou ochranu důvěrných dat. Klíče se nikdy neodebraly z klíčového prstence, takže `IDataProtector.Unprotect` může vždycky obnovovat stávající datové části, pokud jsou klíče k dispozici a jsou platné.

Pokud se ale vývojář pokusí zrušit ochranu dat chráněných pomocí odvolaného klíče, dojde k problému, protože `IDataProtector.Unprotect` v tomto případě vyvolá výjimku. To může být jemné pro krátkodobé nebo přechodné datové části (jako jsou ověřovací tokeny), protože tyto typy datových částí lze snadno znovu vytvořit systémem a v nejhorším případě může být vyžadováno přihlášení návštěvníka webu. Ale u trvalých datových částí `Unprotect` může vyvolání throw způsobit nepřijatelnou ztrátu dat.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Aby bylo možné podporovat scénář povolení ochrany datových částí i na přední stěně odvolaných klíčů, systém ochrany dat obsahuje `IPersistedDataProtector` typ. Chcete-li získat instanci `IPersistedDataProtector` , jednoduše Získejte instanci `IDataProtector` v normálním způsobu a zkuste přetypování na `IDataProtector` `IPersistedDataProtector` .

> [!NOTE]
> Ne všechny `IDataProtector` instance lze přetypovat na `IPersistedDataProtector` . Vývojáři by měli použít operátor jazyka C# nebo podobným způsobem, aby nedocházelo k výjimkám modulu runtime způsobených neplatnými přetypováními a měli by být připraveni zpracovat případ selhání odpovídajícím způsobem.

`IPersistedDataProtector` zpřístupňuje následující plochu rozhraní API:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Toto rozhraní API přebírá chráněnou datovou část (jako bajtové pole) a vrací nechráněnou datovou část. Neexistuje žádné přetížení založené na řetězci. Tyto dva výstupní parametry jsou následující.

* `requiresMigration`: bude nastaveno na hodnotu true, pokud klíč, který je použit k ochraně této datové části, již není aktivním výchozím klíčem, např. klíč použitý k ochraně této datové části je starý a od chvíle, kdy byla provedena operace s klíčovou operací. Volající může chtít zvážit přeochranu datové části v závislosti na jejich obchodních potřebách.

* `wasRevoked`: bude nastaveno na hodnotu true, pokud byl klíč použitý k ochraně této datové části odvolán.

>[!WARNING]
> Při předávání `ignoreRevocationErrors: true` do metody vyzkoušejte mimořádně opatrní `DangerousUnprotect` . Pokud po volání této metody `wasRevoked` je hodnota true, klíč použitý k ochraně této datové části byl odvolán a pravost datové části by měla být považována za podezřelou. V takovém případě můžete i nadále používat nechráněnou datovou část, pokud máte určitá samostatná ujištění, že je ze zabezpečené databáze místo odesílání nedůvěryhodným webovým klientem.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
