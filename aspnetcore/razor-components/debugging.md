---
title: Ladění součásti syntaxe Razor
author: guardrex
description: Zjistěte, jak ladit Blazor a Razor součásti aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/debug
ms.openlocfilehash: fb7ddcf3ae40ec28a372adf724a293b375be28a1
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668097"
---
# <a name="debug-razor-components"></a>Ladění součásti syntaxe Razor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Některé má Razor součástí *velmi brzy* podporu pro ladění na straně klienta Blazor aplikace běžící na WebAssembly v prohlížeči Chrome. Tento počáteční podpory ladění je velmi omezená a unpolished, ukazuje základní infrastrukturu ladění setkávají.

Ladění aplikací na straně klienta Blazor v prohlížeči Chrome:

* Vytvoření aplikace Blazor během `Debug` konfigurace (výchozí pro nepublikované aplikace).
* Blazor aplikaci spusťte v prohlížeči Chrome (verze 70 nebo vyšší).
* Fokus klávesnice v aplikaci (ne v panelu nástrojů dev, který by měl pravděpodobně zavřete pro méně matoucí možnosti ladění) vyberte následující klávesové zkratky specifické pro Blazor:
  * `Shift+Alt+D` na Windows/Linux
  * `Shift+Cmd+D` V systému macOS

Spusťte Chrome se vzdáleným laděním povoleno ladění Blazor aplikací. Pokud je vzdálené ladění je zakázané, Chrome vygeneruje chybovou stránku. Chybová stránka obsahuje pokyny ke spouštění Chrome pomocí portu ladění otevřete tak, aby Blazor ladicí proxy server může připojit k aplikaci. *Zavřete všechny instance chromu* a znovu spusťte Chrome podle pokynů.

![Blazor ladění chybovou stránku](https://user-images.githubusercontent.com/1874516/43123091-01ec0796-8ed8-11e8-844c-23b4e6e9d069.png)

Po spuštění Chrome s povoleným laděním vzdáleného ladění stiskněte kombinaci kláves otevře novou kartu ladicího programu. Za okamžik *zdroje* karta zobrazuje seznam sestavení .NET v aplikaci. Rozbalte každého sestavení a vyhledejte *.cs*/*.cshtml* zdrojové soubory pro ladění k dispozici. Nastavit zarážky, přejděte zpět na kartě aplikace a zarážky jsou přístupů. Po zarážku přístupů, krokování (`F10`) nebo obnovení (`F8`) normálně.

![Blazor ladění](https://user-images.githubusercontent.com/1874516/43123060-efb0b3b0-8ed7-11e8-9ea5-97aa34247a0b.png)

Blazor poskytuje ladicí proxy server tohoto implementuje [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) a argumentech protokolu. Informace specifické pro sítě. Při stisknutí klávesové zkratky ladění ukazuje Blazor Chrome DevTools na proxy serveru. Proxy server připojí do okna prohlížeče se snaží ladění (tedy potřeba povolit vzdálené ladění).

Možná se ptáte, proč jsme právě nepoužívejte prohlížeče zdrojových mapování. Zdrojových mapování povolit prohlížeči mapování zkompilované soubory zpátky na jejich původní zdrojové soubory. Ale Blazor nemapuje C# přímo do JS/WASM (nejméně ještě není). Místo toho Blazor nemá překladu IL v prohlížeči, takže zdrojových mapování nejsou relevantní.

Všimněte si, že jsou možnosti ladicího programu **velmi omezená**. Je možné aktuálně pouze:

* Nastavení a odebrat zarážky.
* Krokování kódu nebo pokračovat (`F8`).
* V *lokální* zobrazovat, sledovat všechny místní proměnné typu hodnoty `int`, `string`, a `bool`.
* Zobrazit zásobník volání, včetně volání řetězce, které přejít z jazyka JavaScript do technologie .NET a .NET pro jazyk JavaScript.

Můžete *nelze*:

* Sledovat hodnoty všech místních hodnot, které nejsou `int`, `string`, nebo `bool`.
* Sledujte hodnoty pole nebo vlastnosti třídy.
* Najeďte myší zobrazíte jejich hodnoty proměnné
* Vyhodnocení výrazů v konzole.
* Krok přes asynchronní volání.
* Proveďte většina jiných běžné scénáře ladění.

Vývoj pro další ladění scénářů je probíhající těžiště technickému týmu.

## <a name="troubleshooting-tip"></a>Tip Poradce při potížích

Pokud používáte k chybám, mohou pomoci následující tip:

V **ladicí program** kartě, otevřete vývojářské nástroje v prohlížeči. V konzole, spusťte `localStorage.clear()` odebrat všechny zarážky.
