---
title: Správa klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci rozhraní API pro správu klíčů ASP.NET Core Data Protection.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: c81e328d8774bfbd1309f854715fcda2152f9eeb
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021208"
---
# <a name="key-management-in-aspnet-core"></a>Správa klíčů v ASP.NET Core

<a name="data-protection-implementation-key-management"></a>

Systém ochrany dat automaticky spravuje dobu života hlavních klíčů, které slouží k ochraně a odemknutí datových částí. Každý klíč může existovat v jednom ze čtyř fází:

* Vytvořeno – klíč existuje ve službě Key Ring, ale ještě není aktivovaný. Klíč by se neměl používat pro nové operace ochrany, dokud neuplyne dostatek času na to, že by měl mít možnost rozšířit na všechny počítače, které tento klíčový kanál využívají.

* Aktivní – klíč existuje ve službě Key Ring a měl by se používat pro všechny nové operace ochrany.

* Vypršela platnost – klíč spustil svou přirozenou životnost a neměl by se už používat pro nové operace ochrany.

* Odvoláno – klíč je napadený a nesmí se používat pro nové operace ochrany.

Klíče pro vytváření, aktivní a vypršení platnosti se můžou používat k odemknutí příchozích datových částí. Odvolané klíče ve výchozím nastavení se nedají použít k zrušení ochrany datových částí, ale vývojář aplikace může v případě potřeby [Toto chování přepsat](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) .

>[!WARNING]
> Vývojář se může rozhodnout odstranit klíč z klíčového prstence (například odstraněním odpovídajícího souboru ze systému souborů). V tomto okamžiku se všechna data chráněná klíčem trvale nešifrují a neexistuje žádné nouzové přepsání, podobně jako u odvolaných klíčů. Odstranění klíče je skutečně destruktivní chování, a proto systém ochrany dat zpřístupňuje žádné rozhraní API první třídy, které by tuto operaci provádělo.

## <a name="default-key-selection"></a>Výchozí výběr klíče

Když systém ochrany dat přečte z záložního úložiště klíč, pokusí se najít "výchozí" klíč ze služby Key Ring. Výchozí klíč se používá pro nové operace ochrany.

Obecná heuristika znamená, že systém ochrany dat zvolí klíč s nejnovějším datem aktivace jako výchozí klíč. (K dispozici je malý Fudge faktor, který umožňuje vyhodnotit časové zkosení mezi servery.) Pokud vypršela platnost klíče nebo odvolala, a pokud aplikace nevypne automatické generování klíčů, vygeneruje se nový klíč s okamžitou aktivací na základě [vypršení platnosti klíče a postupná](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) zásada.

Důvodem je, že systém ochrany dat vygeneruje nový klíč hned a nevrátí se zpět do jiného klíče, že by se nová generace klíčů měla považovat za implicitní vypršení všech klíčů, které byly aktivovány před novým klíčem. Obecným nápadem je, že nové klíče byly nakonfigurované s různými algoritmy nebo mechanizmy při šifrování v klidovém režimu než staré klíče a systém by měl preferovat aktuální konfiguraci v případě vrácení zpět.

Došlo k výjimce. Pokud vývojář aplikace [zakázal automatické generování klíčů](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), musí systém ochrany dat zvolit něco jako výchozí klíč. V tomto záložním scénáři systém zvolí neodvolán klíč s nejaktuálnějším datem aktivace s upřednostněním klíčů, které měly čas rozšířit na jiné počítače v clusteru. Záložní systém může v důsledku toho ukončit výběr výchozího klíče s vypršenou platností. Záložní systém nikdy nezvolí odvolaný klíč jako výchozí klíč, a pokud je klíčového prstence prázdný nebo byl odvolán každý klíč, dojde při inicializaci k chybě systému.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Vypršení platnosti klíče a vracení

Když se vytvoří klíč, automaticky se mu zobrazí datum aktivace {Now + 2 dny} a datum vypršení platnosti {Now + 90 dní}. Před dvěma dny před aktivací je klíčovým časem, který se má rozšířit přes systém. To znamená, že umožňuje jiným aplikacím, které odkazují na záložní úložiště, sledovat klíč při příští automatické aktualizaci, což maximalizuje riziko, že když se klíčový kanál stane aktivní, rozšíří se na všechny aplikace, které ho můžou potřebovat použít.

Pokud vyprší platnost výchozího klíče do 2 dnů a pokud klíčového prstence ještě nemá klíč, který bude aktivní po vypršení platnosti výchozího klíče, pak systém ochrany dat automaticky zachová nový klíč do služby Key Ring. Tento nový klíč má datum aktivace {výchozí datum vypršení platnosti klíče} a datum vypršení platnosti {Now + 90 dní}. Díky tomu může systém pravidelně automaticky pravidelně vytvářet klíče bez přerušení služby.

Můžou nastat situace, kdy se klíč vytvoří s okamžitou aktivací. Příkladem může být, že aplikace neběžela po dobu a platnost všech klíčů ve službě Key Ring vypršela. V takovém případě se klíč udělí datum aktivace {Now} bez normálního prodlevy Aktivace 2 den.

Výchozí životnost klíče je 90 dní, ale je možné ji nakonfigurovat jako v následujícím příkladu.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Správce může změnit také výchozí systém, přestože explicitní volání přepíše `SetDefaultKeyLifetime` jakékoli zásady pro celé systémy. Výchozí životnost klíče nemůže být kratší než 7 dní.

## <a name="automatic-key-ring-refresh"></a>Automatická aktualizace klíčového kruhu

Po inicializaci systému ochrany dat přečte klíčovou Ring z podkladového úložiště a uloží jej do paměti. Tato mezipaměť umožňuje chránit a zrušit ochranu operací, aniž byste se museli zaběhnout do záložního úložiště. Systém automaticky zkontroluje záložní úložiště, aby se změny projevily přibližně každých 24 hodin, nebo když vyprší aktuální výchozí klíč, podle toho, co nastane dřív.

>[!WARNING]
> Vývojáři by měli velmi zřídka používat rozhraní API pro správu klíčů přímo. Systém ochrany dat provede automatickou správu klíčů, jak je popsáno výše.

Systém ochrany dat zpřístupňuje rozhraní `IKeyManager` , které lze použít ke kontrole a provádění změn ve službě Key Ring. Systém DI, který poskytl instanci, `IDataProtectionProvider` může také poskytnout instanci `IKeyManager` pro vaši spotřebu. Alternativně můžete načíst `IKeyManager` přímo z, jak je `IServiceProvider` uvedeno v následujícím příkladu.

Jakákoli operace, která upravuje klíč Ring (explicitní vytvoření nového klíče nebo zrušení odvolání), zruší platnost mezipaměti v paměti. Další volání `Protect` nebo `Unprotect` způsobí, že systém ochrany dat znovu přečte klíč Ring a znovu vytvoří mezipaměť.

Následující ukázka ukazuje použití `IKeyManager` rozhraní pro kontrolu a manipulaci s kanálem, včetně odvolání existujících klíčů a vygenerování nového klíče ručně.

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>Úložiště klíčů

Systém ochrany dat má heuristickou metodu, která se pokusí odvodit příslušné klíčové umístění úložiště klíčů a automatické šifrování. Mechanismus trvalosti klíčů je také možné nakonfigurovat vývojářem aplikace. Následující dokumenty popisují implementace těchto mechanismů v krabicích:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
