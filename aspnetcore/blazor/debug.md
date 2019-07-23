---
title: Ladění ASP.NET Core Blazor
author: guardrex
description: Zjistěte, jak ladit Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: blazor/debug
ms.openlocfilehash: 6d71296417c57f01e675bdbb31a0d4fe2fd7db63
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500433"
---
# <a name="debug-aspnet-core-blazor"></a>Ladění ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

*Časná* podpora je dostupná pro ladění aplikace na straně klienta Blazor běžící na WebAssembly v prohlížeči Chrome.

Možnosti ladicího programu jsou omezené. K dispozici scénáře patří:

* Nastavení a odebrat zarážky.
* Krokování (`F10`) prostřednictvím kódu nebo pokračovat (`F8`) spuštění kódu.
* V *lokální* zobrazovat, sledovat všechny místní proměnné typu hodnoty `int`, `string`, a `bool`.
* Zobrazit zásobník volání, včetně volání řetězce, které přejít z jazyka JavaScript do technologie .NET a .NET pro jazyk JavaScript.

Můžete *nelze*:

* Sledovat hodnoty všech místních hodnot, které nejsou `int`, `string`, nebo `bool`.
* Sledujte hodnoty pole nebo vlastnosti třídy.
* Najeďte myší proměnné zobrazíte jejich hodnoty.
* Vyhodnocení výrazů v konzole.
* Krok přes asynchronní volání.
* Proveďte většina jiných běžné scénáře ladění.

Vývoj pro další ladění scénářů je probíhající těžiště technickému týmu.

## <a name="procedure"></a>Postup

Ladění aplikací na straně klienta Blazor v prohlížeči Chrome:

* Vytvoření aplikace Blazor během `Debug` konfigurace (výchozí pro nepublikované aplikace).
* Blazor aplikaci spusťte v prohlížeči Chrome (verze 70 nebo vyšší).
* Fokus klávesnice na aplikace (ne v panelu nástrojů pro vývojáře, který by měl pravděpodobně zavřete pro méně matoucí možnosti ladění) vyberte následující klávesové zkratky specifické pro Blazor:
  * `Shift+Alt+D` na Windows/Linux
  * `Shift+Cmd+D` V systému macOS

## <a name="enable-remote-debugging"></a>Povolení vzdáleného ladění

Pokud je vzdálené ladění je zakázané, **nelze nalézt kartu prohlížeče laditelné** Chrome vygeneruje chybovou stránku. Chybová stránka obsahuje pokyny ke spouštění Chrome pomocí portu ladění otevřete tak, aby Blazor ladicí proxy server může připojit k aplikaci. *Zavřete všechny instance chromu* a znovu spusťte Chrome podle pokynů.

## <a name="debug-the-app"></a>Ladění aplikace

Po spuštění Chrome s povoleným laděním vzdáleného ladění stiskněte kombinaci kláves otevře novou kartu ladicího programu. Za okamžik **zdroje** karta zobrazuje seznam sestavení .NET v aplikaci. Rozbalte každého sestavení a vyhledejte *.cs*/ *.razor* zdrojové soubory pro ladění k dispozici. Nastavit zarážky, přejděte zpět na kartě aplikace a zarážky jsou přístupů, když kód spustí. Po zarážku přístupů, krokování (`F10`) prostřednictvím kódu nebo pokračovat (`F8`) obvykle spuštění kódu.

Blazor poskytuje ladicí proxy server tohoto implementuje [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) a argumentech protokolu. Informace specifické pro sítě. Při stisknutí klávesové zkratky ladění ukazuje Blazor Chrome DevTools na proxy serveru. Proxy server připojí do okna prohlížeče se snaží ladění (tedy potřeba povolit vzdálené ladění).

## <a name="browser-source-maps"></a>Prohlížeč zdrojového mapování

Prohlížeč zdrojových mapování povolit prohlížeči mapování zkompilované soubory zpátky na jejich původní zdrojové soubory a běžně se používají pro ladění na straně klienta. Ale není aktuálně namapován Blazor C# přímo do jazyka JavaScript/WASM. Místo toho Blazor nemá překladu IL v prohlížeči, takže zdrojových mapování nejsou relevantní.

## <a name="troubleshooting-tip"></a>Tip Poradce při potížích

Pokud používáte k chybám, mohou pomoci následující tip:

V **ladicí program** kartě, otevřete vývojářské nástroje v prohlížeči. V konzole, spusťte `localStorage.clear()` odebrat všechny zarážky.
