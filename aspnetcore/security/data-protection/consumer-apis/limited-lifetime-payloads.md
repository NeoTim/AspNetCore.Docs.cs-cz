---
title: Omezte dobu života chráněných datových částí v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak omezit životnost chráněné datové části pomocí ASP.NET Core rozhraní API ochrany dat.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: bc1597f75d8c5f786d46e59ac027d01ffca077c0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768608"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Omezte dobu života chráněných datových částí v ASP.NET Core

V některých scénářích chce vývojář aplikace vytvořit chráněnou datovou část, jejíž platnost vyprší po nastaveném časovém intervalu. Chráněná datová část například může představovat token pro resetování hesla, který by měl platit jenom pro jednu hodinu. Je možné, že vývojář vytvoří vlastní formát datové části, který obsahuje vložené datum vypršení platnosti, a zkušení vývojáři ho můžou chtít udělat, ale většina vývojářů, kteří spravují tyto vypršení platnosti, může růst zdlouhavě.

Aby bylo snazší pro naši cílovou skupinu pro vývojáře, balíček [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) obsahuje rozhraní API pro vytváření datových částí, jejichž platnost automaticky vyprší po nastaveném časovém intervalu. Tato rozhraní API zablokují `ITimeLimitedDataProtector` typ.

## <a name="api-usage"></a>Použití rozhraní API

`ITimeLimitedDataProtector` Rozhraní je základní rozhraní pro ochranu a odemknutí datové části s časovým limitem pro časově omezená data. Chcete-li vytvořit instanci `ITimeLimitedDataProtector`, budete nejprve potřebovat instanci regulárního [IDataProtector](xref:security/data-protection/consumer-apis/overview) vytvořenou s konkrétním účelem. Jakmile je `IDataProtector` instance k dispozici, zavolejte `IDataProtector.ToTimeLimitedDataProtector` metodu rozšíření a vraťte ochranu s integrovanými možnostmi vypršení platnosti.

`ITimeLimitedDataProtector`zpřístupňuje následující plochu rozhraní API a metody rozšíření:

* CreateProtector (řetězec pro účely): ITimeLimitedDataProtector – toto rozhraní API je podobné jako `IDataProtectionProvider.CreateProtector` stávající v tom, že je možné ho použít k vytvoření [řetězů účelu](xref:security/data-protection/consumer-apis/purpose-strings) z kořenového ochrany s časovým omezením.

* Chránit (Byte [] prostý text, DateTimeOffset vypršení): Byte []

* Ochrana (Byte [] prostého textu, životnost TimeSpan): Byte []

* Chránit (Byte [] ve formátu prostého textu): Byte []

* Chránit (řetězec ve formátu prostého textu, DateTimeOffset vypršení): řetězec

* Chránit (textový řetězec ve formátu prostého textu, životnost TimeSpan): řetězec

* Chránit (řetězec ve formátu prostého textu): řetězec

Kromě základních `Protect` metod, které přebírají pouze prostý text, existují nová přetížení, která umožňují zadat datum vypršení platnosti datové části. Datum vypršení platnosti lze zadat jako absolutní datum (prostřednictvím `DateTimeOffset`) nebo jako relativní čas (od aktuálního systémového času, přes a `TimeSpan`). Pokud se zavolá přetížení, které nepřijímá vypršení platnosti, předpokládá se, že datová část nebude nikdy vypršet.

* Odemknout (Byte [] protectedData, konec platnosti datového typu DateTimeOffset): Byte []

* Zrušit ochranu (Byte [] protectedData): Byte []

* Zrušit ochranu (řetězcová protectedData, odchozí datový čas DateTimeOffset): řetězec

* Unprotect (String protectedData): řetězec

`Unprotect` Metody vrátí původní nechráněná data. Pokud v datové části ještě nevypršela platnost, vrátí se absolutní doba vypršení platnosti jako volitelný parametr out spolu s původní nechráněnými daty. Pokud vypršela platnost datové části, všechna přetížení metody Unprotect vyvolá CryptographicException –.

>[!WARNING]
> Nedoporučuje se používat tato rozhraní API k ochraně datových částí, které vyžadují dlouhodobou nebo neomezenou perzistenci. "Můžu si dovolit, aby se chráněná datová část trvale po měsíci neobnovila?" může sloužit jako dobré pravidlo pro palec. Pokud odpověď není, vývojáři by si měli vzít v úvahu alternativní rozhraní API.

Následující ukázka používá [nedi cestu kódu](xref:security/data-protection/configuration/non-di-scenarios) pro vytvoření instance systému ochrany dat. Chcete-li spustit tuto ukázku, ujistěte se, že jste nejprve přidali odkaz na balíček Microsoft. AspNetCore. DataProtection. Extensions.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
