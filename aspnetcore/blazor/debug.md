---
title: Blazor ASP.NET Core ladění
author: guardrex
description: Naučte se ladit aplikace Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661705"
---
# <a name="debug-aspnet-core-blazor"></a>Ladění ASP.NET Core Blazor

[Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Pro ladění Blazor WebAssembly pomocí nástrojů pro vývoj v Chromu v prohlížečích založených na chromu (Chrome/Edge) existuje *brzké* podpora. Práce probíhá:

* Zcela povolit ladění v aplikaci Visual Studio.
* Povolit ladění v Visual Studio Code.

Možnosti ladicího programu jsou omezené. K dispozici jsou tyto scénáře:

* Nastavení a odebrání zarážek.
* Jeden krok (`F10`) prostřednictvím kódu nebo obnovení (`F8`) provádění kódu.
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

## <a name="prerequisites"></a>Předpoklady

Ladění vyžaduje některý z následujících prohlížečů:

* Google Chrome (verze 70 nebo novější)
* Microsoft Edge Preview ([kanál pro vývoj](https://www.microsoftedgeinsider.com)na webu Edge)

## <a name="procedure"></a>Postup

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> Podpora ladění v aplikaci Visual Studio je v rané fázi vývoje. Ladění **F5** není aktuálně podporováno.

1. Spusťte aplikaci Blazor WebAssembly v konfiguraci `Debug` bez ladění (**Ctrl**+**F5** místo **F5**).
1. Otevřete vlastnosti ladění aplikace (poslední položka v nabídce **ladění** ) a zkopírujte **adresu URL aplikace**http. Přejděte k adrese HTTP (nikoli k adrese HTTPS) aplikace pomocí prohlížeče založeného na chromu (Edge beta nebo Chrome).
1. Umístěte fokus klávesnice do aplikace v okně prohlížeče, nikoli na panelu nástroje pro vývojáře. Pro tento postup je nejlepší, aby byl panel nástrojů pro vývojáře uzavřený. Po spuštění ladění můžete panel nástroje pro vývojáře znovu otevřít.
1. Vyberte následující klávesovou zkratku specifickou pro Blazor:

   * `Shift+Alt+D` ve Windows
   * `Shift+Cmd+D` na macOS

   Pokud se zobrazí **karta nelze najít laditelné prohlížeče**, přečtěte si téma [Povolení vzdáleného ladění](#enable-remote-debugging).
   
   Po povolení vzdáleného ladění:
   
   1\. Otevře se nové okno prohlížeče. Zavřete předchozí okno.

   2\. Umístěte fokus klávesnice do aplikace v okně prohlížeče.

   3\. V novém okně prohlížeče vyberte klávesovou zkratku specifickou pro Blazor: `Shift+Alt+D` ve Windows nebo `Shift+Cmd+D` na macOS.

   4\. V prohlížeči se otevře karta **devtools** . **V okně prohlížeče znovu vyberte kartu aplikace.**

   Chcete-li připojit aplikaci k aplikaci Visual Studio, přečtěte si část [připojit k procesu v aplikaci Visual Studio](#attach-to-process-in-visual-studio) .

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. Spusťte Blazor aplikaci WebAssembly v konfiguraci `Debug` předáním možnosti `--configuration Debug` do příkazu [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.
1. Přejděte do aplikace na adrese URL protokolu HTTP zobrazeném v okně prostředí.
1. Umístěte fokus klávesnice na aplikaci, ne na panel nástroje pro vývojáře. Pro tento postup je nejlepší, aby byl panel nástrojů pro vývojáře uzavřený. Po spuštění ladění můžete panel nástroje pro vývojáře znovu otevřít.
1. Vyberte následující klávesovou zkratku specifickou pro Blazor:

   * `Shift+Alt+D` ve Windows
   * `Shift+Cmd+D` na macOS

   Pokud se zobrazí **karta nelze najít laditelné prohlížeče**, přečtěte si téma [Povolení vzdáleného ladění](#enable-remote-debugging).
   
   Po povolení vzdáleného ladění:
   
   1\. Otevře se nové okno prohlížeče. Zavřete předchozí okno.

   2\. Umístěte fokus klávesnice do aplikace v okně prohlížeče, nikoli na panelu nástroje pro vývojáře.

   3\. V novém okně prohlížeče vyberte klávesovou zkratku specifickou pro Blazor: `Shift+Alt+D` ve Windows nebo `Shift+Cmd+D` na macOS.

---

## <a name="enable-remote-debugging"></a>Povolit vzdálené ladění

Pokud je vzdálené ladění zakázané, **nemůžete najít** chybovou stránku karty prohlížeče, která je vygenerovaná v Chrome. Chybová stránka obsahuje pokyny pro spuštění Chrome s otevřeným portem pro ladění, aby se proxy Blazor ladicího programu mohl připojit k aplikaci. *Zavřete všechny instance Chrome* a restartujte Chrome podle pokynů.

## <a name="debug-the-app"></a>Ladění aplikace

Po spuštění aplikace Chrome se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci. Rozbalte jednotlivá sestavení a vyhledejte soubory *. cs*/ *. Razor* , které jsou k dispozici pro ladění. Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu. Po zablokování je jeden krok (`F10`) prostřednictvím kódu nebo obnovení kódu (`F8`) normálního spuštění.

Blazor poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť. Při Blazor stisknutí klávesových zkratek ladění DevTools odkazuje na rozhraní Chrome na proxy serveru. Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).

## <a name="attach-to-process-in-visual-studio"></a>Připojení k procesu v aplikaci Visual Studio

Připojení k procesu aplikace v aplikaci Visual Studio je *dočasný* scénář ladění pro Blazor WebAssembly, zatímco ladění **F5** je ve vývoji.

Připojení procesu běžící aplikace k aplikaci Visual Studio:

1. V aplikaci Visual Studio vyberte možnost **ladění** > **připojit k procesu**.
1. Jako **Typ připojení**vyberte **Chrome DevTools Protocol WebSocket (bez ověřování)** .
1. Pro **cíl připojení**vložte adresu http (nikoli adresu https) aplikace.
1. Vyberte **aktualizovat** a aktualizujte položky v části **procesy k dispozici**.
1. Vyberte proces prohlížeče, který chcete ladit, a vyberte **připojit**.
1. V dialogovém okně **Vybrat typ kódu** vyberte typ kódu pro konkrétní prohlížeč, ke kterému se připojujete (Edge nebo Chrome), a pak vyberte **OK**.

## <a name="browser-source-maps"></a>Mapy zdroje prohlížeče

Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta. Blazor ale v tuto chvíli není C# přímo namapována na JavaScript/WASM. Místo toho Blazor provede interpretaci IL v prohlížeči, takže zdrojová mapování nejsou relevantní.

## <a name="troubleshoot"></a>Řešení potíží

Pokud pracujete s chybami, může vám následující Tip:

Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči. V konzole spusťte `localStorage.clear()` a odeberte všechny zarážky.
