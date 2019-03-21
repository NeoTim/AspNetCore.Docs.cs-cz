---
title: Zrušení ochrany datových částí, které byly odvolány klíči v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak zrušit ochranu dat, které jsou chráněné pomocí klíče, které od té doby byly odvolány, v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208028"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Zrušení ochrany datových částí, které byly odvolány klíči v ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

Ochranu dat ASP.NET Core API nejsou určené především pro neomezenou trvalost důvěrné datových částí. Jiné technologie, jako je [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) a [Azure Rights Management](/rights-management/) jsou vhodnější pro scénář neomezené úložiště, a mají možnosti odpovídajícím způsobem silné správu klíčů. Ale nutné dodat, není nic zakazují vývojář pro dlouhodobou ochranu důvěrných dat pomocí data protection API ASP.NET Core. Odeberou se klíče nikdy z okruhu klíč, tedy `IDataProtector.Unprotect` zotaví vždy stávajících datových částí klíče jsou k dispozici a je platný.

Však problém nastane, když se pokusí zrušit ochranu dat, která byla nastavená ochrana pomocí odvolané klíče, jako vývojář `IDataProtector.Unprotect` v tomto případě vyvolá výjimku. To může být u datových částí krátkodobé a jednorázové nebo přechodné (např. ověřovacích tokenů), a tyto druhy datových částí lze snadno znovu vytvořit v systému, v nejhorším případě návštěvník může být nutné se přihlásit znovu. Ale pro trvalých datových částí s `Unprotect` vyvolání výjimky může způsobit ztrátu dat nemůže být přijata.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Aby podporoval scénář umožnit datové části má být zrušena ochrana dokonce i v případě odvolané klíče, obsahuje systém ochrany dat `IPersistedDataProtector` typu. Chcete-li získat instanci `IPersistedDataProtector`, stačí získat instanci `IDataProtector` v normálním způsobem a zkuste přetypování `IDataProtector` k `IPersistedDataProtector`.

> [!NOTE]
> Ne všechny `IDataProtector` instance může být převeden na `IPersistedDataProtector`. Vývojáři měli použít C# jako operátor nebo podobné výjimky modulu CLR, aby způsobené neplatné přetypování a musí být připraveni odpovídajícím způsobem zpracovat případ selhání.

`IPersistedDataProtector` poskytuje následující plochy rozhraní API:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Toto rozhraní API chráněné datové části (jako pole bajtů) přijímá a vrací nechráněné datové části. Neexistuje žádné přetížení založené na řetězci. Dvě výstupní parametry jsou následující.

* `requiresMigration`: se nastavenou na hodnotu true, pokud klíč používaný k ochraně tato datová část už není aktivní výchozí klíč, například je starý klíč používaný k ochraně tato datová část a operace se zajištěním provozu klíči ještě zbývá od provedou místo. Volající může chtít vezměte v úvahu znovu se zapíná ochrana datové části v závislosti na svých obchodních potřeb.

* `wasRevoked`: bude nastavena na hodnotu true, pokud klíč používaný k ochraně tato datová část byl odvolán.

>[!WARNING]
> Výkon extrémně opatrní při předávání `ignoreRevocationErrors: true` k `DangerousUnprotect` metody. Pokud po volání této metody `wasRevoked` je hodnota true, pak klíč používaný k ochraně tato datová část byla odvolána a pravosti datové části mají být považována za podezřelé. V takovém případě pouze pokračujte provozní nechráněné datovou část, pokud máte některé samostatné zárukou, že je platná, například, že se chystá v zabezpečené databázi spíše než odesíláno nedůvěryhodné webového klienta.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
