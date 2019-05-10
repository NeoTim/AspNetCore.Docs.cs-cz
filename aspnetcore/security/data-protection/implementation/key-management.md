---
title: Správa klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti implementace správy klíčů na ochranu dat ASP.NET Core API.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 431bdf2d3076c83279b78f327ddb647f69e6e584
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898770"
---
# <a name="key-management-in-aspnet-core"></a>Správa klíčů v ASP.NET Core

<a name="data-protection-implementation-key-management"></a>

Systém ochrany dat automaticky spravuje životnost hlavního klíče používané k ochraně a zrušení ochrany datových částí. Každý klíč může existovat v jednom ze čtyř fází:

* Vytvořená: klíč existuje v aktualizační kanál, který klíč, ale ještě nebyl aktivován. Klíč nesmí použít pro nové operace ochrany až do uplynutí dostatečné doby, že klíč má využili příležitost dobře se rozšíří do všech počítačů, které spotřebovávají kanál klíč.

* Aktivní - klíč existuje v aktualizační kanál, který klíč a slouží pro všechny nové operace ochrany.

* Vypršela platnost – klíč proběhla přirozené dobu života a již nelze použít pro nové operace ochrany.

* Odvolat – klíč dojde k ohrožení a nesmí se používat pro nové operace ochrany.

Všechny vytvořené, aktivní a vypršela platnost klíče může používají k zrušení ochrany datových částí pro APN příchozí. Odvolané klíče ve výchozím nastavení se nedají použít k zrušení ochrany datových částí, ale může vývojář aplikace [toto chování přepsat](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) v případě potřeby.

>[!WARNING]
> Vývojáři mohou mít tendenci získat odstranění klíče z klíče okruhu (například tak, že odstraníte odpovídající soubor ze systému souborů). V tomto okamžiku je trvale undecipherable všechna data chráněná pomocí klíče a neexistuje žádná nouzový přepsání, jako je s odvolanými klíči. Odstraňuje se klíč je skutečně destruktivní chování a v důsledku toho systém pro ochranu dat poskytuje žádné prvotřídní rozhraní API k provedení této operace.

## <a name="default-key-selection"></a>Výchozí výběr klíče

Když systém ochrany dat čte aktualizační kanál, který klíč ze záložního úložiště, pokusí se najít klíč "Výchozí" z aktualizační kanál, který klíč. Výchozí klíč se používá pro nové operace ochrany.

Obecné heuristiky je, že systém pro ochranu dat stiskne klávesu s poslední datum aktivace jako výchozí klíč. (Není faktor malé hry fudge umožňující hodiny na serveru zkosení.) Pokud klíč vyprší nebo odvolán, a pokud se aplikace nezakázala automatické generování klíče, pak se vygeneruje nový klíč s okamžitou aktivaci za [klíčů vypršení platnosti a vrácení](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) zásad níže.

Z důvodu ochrany systému dat vygeneruje nový klíč okamžitě místo považován za jiný klíč je, že vygenerování nového klíče mají být považována za implicitní vypršení platnosti všechny klíče, které byly aktivovány před novým klíčem. Obecnou myšlenku je, že nové klíče může být nakonfigurován se různé algoritmy nebo mechanismů šifrování v klidovém stavu než staré klíče a systému měli dát přednost aktuální konfiguraci přes návrat.

Dojde k výjimce. Pokud má vývojář aplikace [zakázané automatické generování klíčů](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), zvolte systém ochrany dat musí něco jako výchozí klíč. V tomto scénáři záložní systém zvolí klíč – zrušeno s poslední datum aktivace přednost ke klíčům, které jste využili čas potřebný k šíření do dalších počítačů v clusteru se. Záložní systém uvíznout v důsledku výběru vypršela platnost výchozí klíče. Záložní systém zvolí nikdy odvolané klíč jako výchozí klíč, a pokud aktualizační kanál, který klíč je prázdný nebo každý klíč byl odvolán. potom systému dojde k chybě při inicializaci.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Vypršení platnosti klíče a se zajištěním provozu

Při vytvoření klíče je automaticky přiřazen k aktivaci {now + 2 dny} a datum vypršení platnosti {now + 90 dní}. Zpoždění 2 dny před aktivací poskytuje klíče čas potřebný k šíření prostřednictvím systému. To znamená umožňuje sledovat klíč v jejich dalšího období automatické aktualizace, tím taky maximalizujete pravděpodobnost, že když klíč vyzvánět fakturuje se stane aktivní, které se rozšíří na všechny aplikace, které může být nutné použít jiné aplikace odkazuje na záložní úložiště.

Pokud výchozí klíč vyprší během dvou dnů a aktualizační kanál, který klíč ještě nemá klíč, který bude aktivováno po vypršení platnosti klíče výchozí, pak systém ochrany dat se automaticky zachová nový klíč na klíč kanál. Tento nový klíč má datum aktivace {datum vypršení platnosti výchozí klíč} a datum vypršení platnosti {now + 90 dní}. To umožňuje systému automatickou změnu klíče v pravidelných intervalech bez přerušení služby.

Můžou nastat okolnosti kde klíče se vytvoří s okamžitou aktivaci. Jedním z příkladů se při nebyla na dobu spuštění aplikace a všechny klíče v aktualizační kanál, který klíč buď vypršela platnost. Pokud k tomu dojde, dostane klíč datum aktivace {nyní} bez zpoždění normální 2denním aktivace.

Výchozí doba platnosti klíče je 90 dnů, ačkoli se dají konfigurovat jako v následujícím příkladu.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Správce může také změnit výchozí systémová, i když explicitní volání konstruktoru `SetDefaultKeyLifetime` přepíše všechny zásady v rámci systému. Výchozí doba platnosti klíče nemůže být kratší než 7 dní.

## <a name="automatic-key-ring-refresh"></a>Aktualizace automatického aktualizačního kanálu klíč

Když inicializuje systém ochrany dat, čte aktualizační kanál, který klíč ze základního úložiště a ukládá do mezipaměti v paměti. Tato mezipaměť umožňuje chránit a Unprotect operace pokračovat bez dosažení záložního úložiště. Systém automaticky zkontroluje záložní úložiště pro změny přibližně každých 24 hodin nebo když vyprší platnost aktuálního klíče výchozí, podle toho, co nastane dřív.

>[!WARNING]
> Vývojáři by měl velmi zřídka (Pokud někdy) potřeba použít správu klíčů rozhraní API přímo. Systém ochrany dat provede automatické správy klíčů, jak je popsáno výše.

Systém pro ochranu dat poskytuje rozhraní `IKeyManager` , který můžete použít ke kontrole a provést změny aktualizační kanál, který klíč. DI systém, který poskytuje instance `IDataProtectionProvider` můžete také zadat instanci `IKeyManager` pro spotřebu. Alternativně si můžete vyžádat `IKeyManager` přímo z `IServiceProvider` stejně jako v následujícím příkladu.

Všechny operace, která upravuje klíč prstenec (explicitně vytváření nového klíče nebo při provádění odvolání) zruší platnost mezipaměti v paměti. Další volání `Protect` nebo `Unprotect` způsobí, že systém ochrany dat znovu načíst klíč okruh a znovu vytvořit mezipaměť.

Následující příklad ukazuje použití `IKeyManager` rozhraní ke kontrole a manipulaci s klíč okruh, včetně odvoláním existující klíče a generuje se nový klíč ručně.

[!code-csharp[](key-management/samples/key-management.cs)]

## <a name="key-storage"></a>Úložiště klíčů

Systém ochrany dat má heuristické metody, které se pokusí odvodit příslušné úložiště klíčů umístění a mechanismus šifrování v klidovém stavu automaticky. Trvalost klíče mechanismus je také možné konfigurovat vývojář aplikace. Tyto dokumenty popisují implementace v poli z těchto mechanismů:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
