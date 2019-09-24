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
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="010e9-103">Ladění ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="010e9-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="010e9-104">Daniel Skořepa</span><span class="sxs-lookup"><span data-stu-id="010e9-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="010e9-105">Pro ladění Blazorch aplikací WebAssembly běžících na WebAssembly v Chrome existuje *brzké* podpora.</span><span class="sxs-lookup"><span data-stu-id="010e9-105">*Early* support exists for debugging Blazor WebAssembly apps running on WebAssembly in Chrome.</span></span>

<span data-ttu-id="010e9-106">Možnosti ladicího programu jsou omezené.</span><span class="sxs-lookup"><span data-stu-id="010e9-106">Debugger capabilities are limited.</span></span> <span data-ttu-id="010e9-107">K dispozici jsou tyto scénáře:</span><span class="sxs-lookup"><span data-stu-id="010e9-107">Available scenarios include:</span></span>

* <span data-ttu-id="010e9-108">Nastavení a odebrání zarážek.</span><span class="sxs-lookup"><span data-stu-id="010e9-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="010e9-109">Jednoduchý krok (`F10`) prostřednictvím kódu nebo obnovení kódu (`F8`).</span><span class="sxs-lookup"><span data-stu-id="010e9-109">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="010e9-110">V zobrazení *místních* hodnot Sledujte hodnoty jakýchkoli místních proměnných typu `int`, `string`a `bool`.</span><span class="sxs-lookup"><span data-stu-id="010e9-110">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="010e9-111">Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="010e9-111">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="010e9-112">*Nemůžete*:</span><span class="sxs-lookup"><span data-stu-id="010e9-112">You *can't*:</span></span>

* <span data-ttu-id="010e9-113">Sledujte hodnoty všech národních prostředí, která nejsou `int`, `string`nebo `bool`.</span><span class="sxs-lookup"><span data-stu-id="010e9-113">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="010e9-114">Sledujte hodnoty jakýchkoli vlastností nebo polí třídy.</span><span class="sxs-lookup"><span data-stu-id="010e9-114">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="010e9-115">Pokud chcete zobrazit jejich hodnoty, najeďte myší na proměnné.</span><span class="sxs-lookup"><span data-stu-id="010e9-115">Hover over variables to see their values.</span></span>
* <span data-ttu-id="010e9-116">Vyhodnoťte výrazy v konzole.</span><span class="sxs-lookup"><span data-stu-id="010e9-116">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="010e9-117">Krok v rámci asynchronních volání.</span><span class="sxs-lookup"><span data-stu-id="010e9-117">Step across async calls.</span></span>
* <span data-ttu-id="010e9-118">Proveďte většinu dalších běžných scénářů ladění.</span><span class="sxs-lookup"><span data-stu-id="010e9-118">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="010e9-119">Vývoj dalších scénářů ladění je průběžným soustředěním na technický tým.</span><span class="sxs-lookup"><span data-stu-id="010e9-119">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="010e9-120">Požadavky</span><span class="sxs-lookup"><span data-stu-id="010e9-120">Prerequisites</span></span>

<span data-ttu-id="010e9-121">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="010e9-121">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="010e9-122">Google Chrome (verze 70 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="010e9-122">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="010e9-123">Microsoft Edge Preview ([kanál pro vývoj](https://www.microsoftedgeinsider.com)na webu Edge)</span><span class="sxs-lookup"><span data-stu-id="010e9-123">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="010e9-124">Postup</span><span class="sxs-lookup"><span data-stu-id="010e9-124">Procedure</span></span>

1. <span data-ttu-id="010e9-125">Spusťte aplikaci Blazor WebAssembly v `Debug` konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="010e9-125">Run a Blazor WebAssembly app in `Debug` configuration.</span></span> <span data-ttu-id="010e9-126">Předat možnost příkazu [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`. `--configuration Debug`</span><span class="sxs-lookup"><span data-stu-id="010e9-126">Pass the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="010e9-127">Přihlaste se k aplikaci v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="010e9-127">Access the app in the browser.</span></span>
1. <span data-ttu-id="010e9-128">Umístěte fokus klávesnice na aplikaci, ne na panel nástroje pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="010e9-128">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="010e9-129">Panel nástrojů pro vývojáře lze zavřít při zahájení ladění.</span><span class="sxs-lookup"><span data-stu-id="010e9-129">The developer tools panel can be closed when debugging is initiated.</span></span>
1. <span data-ttu-id="010e9-130">Vyberte následující klávesovou zkratku specifickou pro Blazor:</span><span class="sxs-lookup"><span data-stu-id="010e9-130">Select the following Blazor-specific keyboard shortcut:</span></span>
   * <span data-ttu-id="010e9-131">`Shift+Alt+D`v systému Windows/Linux</span><span class="sxs-lookup"><span data-stu-id="010e9-131">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="010e9-132">`Shift+Cmd+D`v macOS</span><span class="sxs-lookup"><span data-stu-id="010e9-132">`Shift+Cmd+D` on macOS</span></span>
1. <span data-ttu-id="010e9-133">Postupujte podle kroků uvedených na obrazovce a restartujte prohlížeč se zapnutým vzdáleným laděním.</span><span class="sxs-lookup"><span data-stu-id="010e9-133">Follow the steps listed on the screen to restart the browser with remote debugging enabled.</span></span>
1. <span data-ttu-id="010e9-134">Znovu vyberte následující klávesovou zkratku Blazor, abyste mohli spustit ladicí relaci:</span><span class="sxs-lookup"><span data-stu-id="010e9-134">Select the following Blazor-specific keyboard shortcut once again to start the debug session:</span></span>
   * <span data-ttu-id="010e9-135">`Shift+Alt+D`v systému Windows/Linux</span><span class="sxs-lookup"><span data-stu-id="010e9-135">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="010e9-136">`Shift+Cmd+D`v macOS</span><span class="sxs-lookup"><span data-stu-id="010e9-136">`Shift+Cmd+D` on macOS</span></span>

## <a name="enable-remote-debugging"></a><span data-ttu-id="010e9-137">Povolit vzdálené ladění</span><span class="sxs-lookup"><span data-stu-id="010e9-137">Enable remote debugging</span></span>

<span data-ttu-id="010e9-138">Pokud je vzdálené ladění zakázané, **nemůžete najít** chybovou stránku karty prohlížeče, která je vygenerovaná v Chrome.</span><span class="sxs-lookup"><span data-stu-id="010e9-138">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="010e9-139">Chybová stránka obsahuje pokyny pro spuštění aplikace Chrome s otevřeným portem pro ladění, aby se proxy server Blazor pro ladění mohl připojit k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="010e9-139">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="010e9-140">*Zavřete všechny instance Chrome* a restartujte Chrome podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="010e9-140">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="010e9-141">Ladění aplikace</span><span class="sxs-lookup"><span data-stu-id="010e9-141">Debug the app</span></span>

<span data-ttu-id="010e9-142">Po spuštění aplikace Chrome se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="010e9-142">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="010e9-143">Rozbalte jednotlivá sestavení a vyhledejte zdrojové soubory *. cs*/ *. Razor* k dispozici pro ladění.</span><span class="sxs-lookup"><span data-stu-id="010e9-143">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="010e9-144">Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="010e9-144">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="010e9-145">Po stisknutí zarážky krok za běhu (`F10`) prostřednictvím kódu nebo obnovení kódu (`F8`) normálně.</span><span class="sxs-lookup"><span data-stu-id="010e9-145">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

<span data-ttu-id="010e9-146">Blazor poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť.</span><span class="sxs-lookup"><span data-stu-id="010e9-146">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="010e9-147">Při stisknutí klávesových zkratek ladění Blazor odkazuje na rozhraní Chrome DevTools na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="010e9-147">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="010e9-148">Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="010e9-148">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="010e9-149">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="010e9-149">Browser source maps</span></span>

<span data-ttu-id="010e9-150">Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="010e9-150">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="010e9-151">Blazor se ale v tuto chvíli C# nemapuje přímo na JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="010e9-151">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="010e9-152">Místo toho Blazor provede interpretaci IL v prohlížeči, takže zdrojová mapování nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="010e9-152">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshooting-tip"></a><span data-ttu-id="010e9-153">Tip Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="010e9-153">Troubleshooting tip</span></span>

<span data-ttu-id="010e9-154">Pokud pracujete s chybami, může vám následující Tip:</span><span class="sxs-lookup"><span data-stu-id="010e9-154">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="010e9-155">Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="010e9-155">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="010e9-156">V konzole nástroje spusťte `localStorage.clear()` příkaz k odebrání všech zarážek.</span><span class="sxs-lookup"><span data-stu-id="010e9-156">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
