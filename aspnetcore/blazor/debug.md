---
title: Ladění ASP.NET Core Blazor
author: guardrex
description: Naučte se ladit aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/debug
ms.openlocfilehash: 3519479d8058f013de23cc9cfa0f5574cd158053
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207207"
---
# <a name="debug-aspnet-core-blazor"></a>Ladění ASP.NET Core Blazor

[Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Pro ladění Blazorch aplikací WebAssembly běžících na WebAssembly v Chrome existuje *brzké* podpora.

Možnosti ladicího programu jsou omezené. K dispozici jsou tyto scénáře:

* Nastavení a odebrání zarážek.
* Jednoduchý krok (`F10`) prostřednictvím kódu nebo obnovení kódu (`F8`).
* V zobrazení *místních* hodnot Sledujte hodnoty jakýchkoli místních proměnných typu `int`, `string`a `bool`.
* Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.

*Nemůžete*:

* Sledujte hodnoty všech národních prostředí, která nejsou `int`, `string`nebo `bool`.
* Sledujte hodnoty jakýchkoli vlastností nebo polí třídy.
* Pokud chcete zobrazit jejich hodnoty, najeďte myší na proměnné.
* Vyhodnoťte výrazy v konzole.
* Krok v rámci asynchronních volání.
* Proveďte většinu dalších běžných scénářů ladění.

Vývoj dalších scénářů ladění je průběžným soustředěním na technický tým.

## <a name="prerequisites"></a>Požadavky

Ladění vyžaduje některý z následujících prohlížečů:

* Google Chrome (verze 70 nebo novější)
* Microsoft Edge Preview ([kanál pro vývoj](https://www.microsoftedgeinsider.com)na webu Edge)

## <a name="procedure"></a>Postup

1. Spusťte aplikaci Blazor WebAssembly v `Debug` konfiguraci. Předat možnost příkazu [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`. `--configuration Debug`
1. Přihlaste se k aplikaci v prohlížeči.
1. Umístěte fokus klávesnice na aplikaci, ne na panel nástroje pro vývojáře. Panel nástrojů pro vývojáře lze zavřít při zahájení ladění.
1. Vyberte následující klávesovou zkratku specifickou pro Blazor:
   * `Shift+Alt+D`v systému Windows/Linux
   * `Shift+Cmd+D`v macOS
1. Postupujte podle kroků uvedených na obrazovce a restartujte prohlížeč se zapnutým vzdáleným laděním.
1. Znovu vyberte následující klávesovou zkratku Blazor, abyste mohli spustit ladicí relaci:
   * `Shift+Alt+D`v systému Windows/Linux
   * `Shift+Cmd+D`v macOS

## <a name="enable-remote-debugging"></a>Povolit vzdálené ladění

Pokud je vzdálené ladění zakázané, **nemůžete najít** chybovou stránku karty prohlížeče, která je vygenerovaná v Chrome. Chybová stránka obsahuje pokyny pro spuštění aplikace Chrome s otevřeným portem pro ladění, aby se proxy server Blazor pro ladění mohl připojit k aplikaci. *Zavřete všechny instance Chrome* a restartujte Chrome podle pokynů.

## <a name="debug-the-app"></a>Ladění aplikace

Po spuštění aplikace Chrome se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci. Rozbalte jednotlivá sestavení a vyhledejte zdrojové soubory *. cs*/ *. Razor* k dispozici pro ladění. Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu. Po stisknutí zarážky krok za běhu (`F10`) prostřednictvím kódu nebo obnovení kódu (`F8`) normálně.

Blazor poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť. Při stisknutí klávesových zkratek ladění Blazor odkazuje na rozhraní Chrome DevTools na proxy serveru. Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).

## <a name="browser-source-maps"></a>Mapy zdroje prohlížeče

Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta. Blazor se ale v tuto chvíli C# nemapuje přímo na JavaScript/WASM. Místo toho Blazor provede interpretaci IL v prohlížeči, takže zdrojová mapování nejsou relevantní.

## <a name="troubleshooting-tip"></a>Tip Poradce při potížích

Pokud pracujete s chybami, může vám následující Tip:

Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči. V konzole nástroje spusťte `localStorage.clear()` příkaz k odebrání všech zarážek.
