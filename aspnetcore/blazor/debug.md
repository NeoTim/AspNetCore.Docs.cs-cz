---
title: Blazor ASP.NET Core ladění
author: guardrex
description: Naučte se ladit aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/debug
ms.openlocfilehash: 3096ad9b3a6904804f239d61f374adcd4d851978
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963138"
---
# <a name="debug-aspnet-core-opno-locblazor"></a>Blazor ASP.NET Core ladění

[Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Pro ladění Blazorch aplikací WebAssembly běžících na WebAssembly v Chrome existuje *brzké* podpora.

Možnosti ladicího programu jsou omezené. K dispozici jsou tyto scénáře:

* Nastavení a odebrání zarážek.
* Jeden krok (`F10`) prostřednictvím kódu nebo obnovení (`F8`) provádění kódu.
* V zobrazení *místních* hodnot Sledujte hodnoty všech místních proměnných typu `int`, `string` a `bool`.
* Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.

*Nemůžete*:

* Sledujte hodnoty všech národních prostředí, která nejsou `int`, `string` nebo `bool`.
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

1. Spusťte aplikaci Blazor WebAssembly v konfiguraci `Debug`. Předejte možnost `--configuration Debug` do příkazu [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.
1. Přihlaste se k aplikaci v prohlížeči.
1. Umístěte fokus klávesnice na aplikaci, ne na panel nástroje pro vývojáře. Panel nástrojů pro vývojáře lze zavřít při zahájení ladění.
1. Vyberte následující klávesovou zkratku určenou pro Blazor:
   * `Shift+Alt+D` v systému Windows/Linux
   * `Shift+Cmd+D` v macOS
1. Postupujte podle kroků uvedených na obrazovce a restartujte prohlížeč se zapnutým vzdáleným laděním.
1. Po opětovném spuštění relace ladění vyberte následující klávesovou zkratku Blazor:
   * `Shift+Alt+D` v systému Windows/Linux
   * `Shift+Cmd+D` v macOS

## <a name="enable-remote-debugging"></a>Povolit vzdálené ladění

Pokud je vzdálené ladění zakázané, **nemůžete najít** chybovou stránku karty prohlížeče, která je vygenerovaná v Chrome. Chybová stránka obsahuje pokyny pro spuštění Chrome s otevřeným portem pro ladění, aby se proxy Blazor ladicího programu mohl připojit k aplikaci. *Zavřete všechny instance Chrome* a restartujte Chrome podle pokynů.

## <a name="debug-the-app"></a>Ladění aplikace

Po spuštění aplikace Chrome se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci. Rozbalte jednotlivá sestavení a vyhledejte soubory *. cs*/ *. Razor* , které jsou k dispozici pro ladění. Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu. Po dosažení zarážky, krok za běhu (`F10`) prostřednictvím kódu nebo obnovení kódu (`F8`) normálního spuštění kódu.

Blazor poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť. Při Blazor stisknutí klávesových zkratek ladění DevTools odkazuje na rozhraní Chrome na proxy serveru. Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).

## <a name="browser-source-maps"></a>Mapy zdroje prohlížeče

Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta. Blazor ale v tuto chvíli není C# přímo namapována na JavaScript/WASM. Místo toho Blazor provede interpretaci IL v prohlížeči, takže zdrojová mapování nejsou relevantní.

## <a name="troubleshooting-tip"></a>Tip Poradce při potížích

Pokud pracujete s chybami, může vám následující Tip:

Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči. V konzole spusťte `localStorage.clear()` a odeberte všechny zarážky.
