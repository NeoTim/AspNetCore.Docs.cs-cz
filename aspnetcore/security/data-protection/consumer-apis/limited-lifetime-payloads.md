---
title: Omezení životnosti chráněných datových částí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak omezení životnosti chráněných pomocí rozhraní API pro ASP.NET Core Data Protection datovou část.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902910"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Omezení životnosti chráněných datových částí v ASP.NET Core

Existují scénáře, kdy vývojář aplikace chce vytvořit chráněný datovou část, jejíž platnost vyprší po nastaveném časovém období. Například chráněné datové části může představovat token pro resetování hesla, které by měl být platný pouze pro jednu hodinu. Je určitě možné pro vývojáře k vytvoření vlastní datovou část formátu, který obsahuje datum vypršení platnosti embedded, a pokročilé vývojáře staví na přesto provést, ale pro většinu vývojářů Správa těchto vypršení platnosti můžou růst únavné.

Pro lepší pochopení pro naši posluchači pro vývojáře, balíček [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) obsahuje nástroj pro rozhraní API pro vytváření datových částí, které automaticky vyprší po nastaveném časovém období. Tato rozhraní API zablokování, odhlaste `ITimeLimitedDataProtector` typu.

## <a name="api-usage"></a>Použití rozhraní API

`ITimeLimitedDataProtector` Rozhraní je základní rozhraní pro ochranu a zrušení ochrany datových částí časově omezené / samoobslužné, u nichž vyprší platnost. Chcete-li vytvořit instanci `ITimeLimitedDataProtector`, musíte nejdřív instance běžný [IDataProtector](xref:security/data-protection/consumer-apis/overview) vytvořený s konkrétním účelem. Jednou `IDataProtector` instance je k dispozici, zavolejte `IDataProtector.ToTimeLimitedDataProtector` metodu rozšíření k získání zpět ochranného zařízení s možnostmi integrované vypršení platnosti.

`ITimeLimitedDataProtector` poskytuje následující metody rozšíření a povrchu API:

* CreateProtector (účel řetězec): ITimeLimitedDataProtector - toto rozhraní API je podobná stávající `IDataProtectionProvider.CreateProtector` v tom, že je možné vytvořit [účel řetězy](xref:security/data-protection/consumer-apis/purpose-strings) z časově omezené ochranného zařízení root.

* Ochrana (byte [] ve formátu prostého textu, vypršení platnosti DateTimeOffset): byte]

* Ochrana (byte [] ve formátu prostého textu, dobu života časový interval): byte]

* Ochrana (byte [] ve formátu prostého textu): byte]

* Ochrana (řetězec ve formátu prostého textu, vypršení platnosti DateTimeOffset): řetězec

* Ochrana (řetězec ve formátu prostého textu, dobu života časový interval): řetězec

* Ochrana (řetězec jako prostý text): řetězec

Kromě základních `Protect` metody, které trvat pouze jako prostý text, existují nová přetížení, které umožňují určit datum vypršení platnosti datové části. Datum vypršení platnosti se dá nastavit jako absolutní datum (prostřednictvím `DateTimeOffset`) nebo jako relativní časové (z aktuálního systému prostřednictvím čas `TimeSpan`). Pokud přetížení, které nepřijímá vypršení platnosti je volána, datové části se předpokládá, že nikdy vypršení platnosti.

* Odemknout (byte [] protectedData, out vypršení platnosti DateTimeOffset): byte]

* Odemknout (byte [] protectedData): byte]

* Odemknout (out vypršení platnosti DateTimeOffset protectedData řetězec): řetězec

* Odemknout (protectedData řetězec): řetězec

`Unprotect` Metody vrátí původní nechráněný data. Pokud dosud nepozbylo platnosti datové části, vrátí se jako volitelný parametr spolu s daty o původní nechráněný out absolutní vypršení platnosti. Pokud se datová část vypršela, vyvolá všechna přetížení metody Unprotect cryptographicexception –.

>[!WARNING]
> Není doporučuje se použití těchto rozhraní API k ochraně datových částí, které vyžadují dlouhodobé nebo neomezené trvalosti. "Jsem dovolit pro chráněných datových částí bude trvale neopravitelné po měsíci?" může sloužit jako základním pravidlem; Pokud je odpověď žádné pak vývojáři měli zvážit alternativní rozhraní API.

Ukázkové níže využívá [cesty kódu bez DI](xref:security/data-protection/configuration/non-di-scenarios) pro vytvoření instance dat ochrany systému. Tuto ukázku spustit, ujistěte se, že jste nejdřív přidali odkaz na balíček Microsoft.AspNetCore.DataProtection.Extensions.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
