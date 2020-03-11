---
title: Omezte dobu života chráněných datových částí v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak omezit životnost chráněné datové části pomocí ASP.NET Core rozhraní API ochrany dat.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656056"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Omezte dobu života chráněných datových částí v ASP.NET Core

V některých scénářích chce vývojář aplikace vytvořit chráněnou datovou část, jejíž platnost vyprší po nastaveném časovém intervalu. Chráněná datová část například může představovat token pro resetování hesla, který by měl platit jenom pro jednu hodinu. Je možné, že vývojář vytvoří vlastní formát datové části, který obsahuje vložené datum vypršení platnosti, a zkušení vývojáři ho můžou chtít udělat, ale většina vývojářů, kteří spravují tyto vypršení platnosti, může růst zdlouhavě.

Aby bylo snazší pro naši cílovou skupinu pro vývojáře, balíček [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) obsahuje rozhraní API pro vytváření datových částí, jejichž platnost automaticky vyprší po nastaveném časovém intervalu. Tato rozhraní API se odblokují od `ITimeLimitedDataProtector`ho typu.

## <a name="api-usage"></a>Použití rozhraní API

Rozhraní `ITimeLimitedDataProtector` je základní rozhraní pro ochranu a odemknutí datových částí s časovým limitem, které jsou časově omezená a jejichž platnost vypršela. Chcete-li vytvořit instanci `ITimeLimitedDataProtector`, budete nejprve potřebovat instanci regulárního [IDataProtector](xref:security/data-protection/consumer-apis/overview) vytvořenou s konkrétním účelem. Jakmile je instance `IDataProtector` k dispozici, zavolejte metodu rozšíření `IDataProtector.ToTimeLimitedDataProtector` a vraťte ochranu pomocí integrovaných možností vypršení platnosti.

`ITimeLimitedDataProtector` zpřístupňuje následující plochy rozhraní API a metody rozšíření:

* CreateProtector (řetězec pro účely): ITimeLimitedDataProtector – toto rozhraní API je podobné existujícímu `IDataProtectionProvider.CreateProtector` v tom, že se dá použít k vytvoření [řetězů účelu](xref:security/data-protection/consumer-apis/purpose-strings) z kořenového ochrany s časovým omezením.

* Chránit (Byte [] prostý text, DateTimeOffset vypršení): Byte []

* Ochrana (Byte [] prostého textu, životnost TimeSpan): Byte []

* Chránit (Byte [] ve formátu prostého textu): Byte []

* Chránit (řetězec ve formátu prostého textu, DateTimeOffset vypršení): řetězec

* Chránit (textový řetězec ve formátu prostého textu, životnost TimeSpan): řetězec

* Chránit (řetězec ve formátu prostého textu): řetězec

Kromě základních `Protect` metod, které přebírají pouze prostý text, existují nová přetížení, která umožňují zadat datum vypršení platnosti datové části. Datum vypršení platnosti lze zadat jako absolutní datum (prostřednictvím `DateTimeOffset`) nebo jako relativní čas (od aktuálního systémového času prostřednictvím `TimeSpan`). Pokud se zavolá přetížení, které nepřijímá vypršení platnosti, předpokládá se, že datová část nebude nikdy vypršet.

* Odemknout (Byte [] protectedData, konec platnosti datového typu DateTimeOffset): Byte []

* Zrušit ochranu (Byte [] protectedData): Byte []

* Zrušit ochranu (řetězcová protectedData, odchozí datový čas DateTimeOffset): řetězec

* Unprotect (String protectedData): řetězec

Metody `Unprotect` vrátí původní nechráněná data. Pokud v datové části ještě nevypršela platnost, vrátí se absolutní doba vypršení platnosti jako volitelný parametr out spolu s původní nechráněnými daty. Pokud vypršela platnost datové části, všechna přetížení metody Unprotect vyvolá CryptographicException –.

>[!WARNING]
> Nedoporučuje se používat tato rozhraní API k ochraně datových částí, které vyžadují dlouhodobou nebo neomezenou perzistenci. "Můžu si dovolit, aby se chráněná datová část trvale po měsíci neobnovila?" může sloužit jako dobré pravidlo pro palec. Pokud odpověď není, vývojáři by si měli vzít v úvahu alternativní rozhraní API.

Následující ukázka používá [nedi cestu kódu](xref:security/data-protection/configuration/non-di-scenarios) pro vytvoření instance systému ochrany dat. Chcete-li spustit tuto ukázku, ujistěte se, že jste nejprve přidali odkaz na balíček Microsoft. AspNetCore. DataProtection. Extensions.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
