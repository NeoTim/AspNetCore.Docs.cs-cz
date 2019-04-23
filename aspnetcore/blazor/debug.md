---
title: Ladění Blazor
author: guardrex
description: Zjistěte, jak ladit Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: blazor/debug
ms.openlocfilehash: 40457b942061fb910a6311af78ff29ac3a1699de
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982792"
---
# <a name="debug-blazor"></a><span data-ttu-id="eaaf6-103">Ladění Blazor</span><span class="sxs-lookup"><span data-stu-id="eaaf6-103">Debug Blazor</span></span>

[<span data-ttu-id="eaaf6-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="eaaf6-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="eaaf6-105">*Časná* podpora je dostupná pro ladění na straně klienta Blazor aplikace běžící na WebAssembly v prohlížeči Chrome.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-105">*Early* support exists for debugging client-side Blazor apps running on WebAssembly in Chrome.</span></span>

<span data-ttu-id="eaaf6-106">Možnosti ladicího programu jsou omezené.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-106">Debugger capabilities are limited.</span></span> <span data-ttu-id="eaaf6-107">K dispozici scénáře patří:</span><span class="sxs-lookup"><span data-stu-id="eaaf6-107">Available scenarios include:</span></span>

* <span data-ttu-id="eaaf6-108">Nastavení a odebrat zarážky.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="eaaf6-109">Krokování (`F10`) prostřednictvím kódu nebo pokračovat (`F8`) spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-109">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="eaaf6-110">V *lokální* zobrazovat, sledovat všechny místní proměnné typu hodnoty `int`, `string`, a `bool`.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-110">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="eaaf6-111">Zobrazit zásobník volání, včetně volání řetězce, které přejít z jazyka JavaScript do technologie .NET a .NET pro jazyk JavaScript.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-111">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="eaaf6-112">Můžete *nelze*:</span><span class="sxs-lookup"><span data-stu-id="eaaf6-112">You *can't*:</span></span>

* <span data-ttu-id="eaaf6-113">Sledovat hodnoty všech místních hodnot, které nejsou `int`, `string`, nebo `bool`.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-113">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="eaaf6-114">Sledujte hodnoty pole nebo vlastnosti třídy.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-114">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="eaaf6-115">Najeďte myší proměnné zobrazíte jejich hodnoty.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-115">Hover over variables to see their values.</span></span>
* <span data-ttu-id="eaaf6-116">Vyhodnocení výrazů v konzole.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-116">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="eaaf6-117">Krok přes asynchronní volání.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-117">Step across async calls.</span></span>
* <span data-ttu-id="eaaf6-118">Proveďte většina jiných běžné scénáře ladění.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-118">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="eaaf6-119">Vývoj pro další ladění scénářů je probíhající těžiště technickému týmu.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-119">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="procedure"></a><span data-ttu-id="eaaf6-120">Postup</span><span class="sxs-lookup"><span data-stu-id="eaaf6-120">Procedure</span></span>

<span data-ttu-id="eaaf6-121">Ladění aplikací na straně klienta Blazor v prohlížeči Chrome:</span><span class="sxs-lookup"><span data-stu-id="eaaf6-121">To debug a client-side Blazor app in Chrome:</span></span>

* <span data-ttu-id="eaaf6-122">Vytvoření aplikace Blazor během `Debug` konfigurace (výchozí pro nepublikované aplikace).</span><span class="sxs-lookup"><span data-stu-id="eaaf6-122">Build a Blazor app in `Debug` configuration (the default for unpublished apps).</span></span>
* <span data-ttu-id="eaaf6-123">Blazor aplikaci spusťte v prohlížeči Chrome (verze 70 nebo vyšší).</span><span class="sxs-lookup"><span data-stu-id="eaaf6-123">Run the Blazor app in Chrome (version 70 or later).</span></span>
* <span data-ttu-id="eaaf6-124">Fokus klávesnice na aplikace (ne v panelu nástrojů pro vývojáře, který by měl pravděpodobně zavřete pro méně matoucí možnosti ladění) vyberte následující klávesové zkratky specifické pro Blazor:</span><span class="sxs-lookup"><span data-stu-id="eaaf6-124">With the keyboard focus on the app (not in the developer tools panel, which you should probably close for a less confusing debugging experience), select the following Blazor-specific keyboard shortcut:</span></span>
  * <span data-ttu-id="eaaf6-125">`Shift+Alt+D` na Windows/Linux</span><span class="sxs-lookup"><span data-stu-id="eaaf6-125">`Shift+Alt+D` on Windows/Linux</span></span>
  * <span data-ttu-id="eaaf6-126">`Shift+Cmd+D` V systému macOS</span><span class="sxs-lookup"><span data-stu-id="eaaf6-126">`Shift+Cmd+D` on macOS</span></span>

## <a name="enable-remote-debugging"></a><span data-ttu-id="eaaf6-127">Povolení vzdáleného ladění</span><span class="sxs-lookup"><span data-stu-id="eaaf6-127">Enable remote debugging</span></span>

<span data-ttu-id="eaaf6-128">Pokud je vzdálené ladění je zakázané, **nelze nalézt kartu prohlížeče laditelné** Chrome vygeneruje chybovou stránku.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-128">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="eaaf6-129">Chybová stránka obsahuje pokyny ke spouštění Chrome pomocí portu ladění otevřete tak, aby Blazor ladicí proxy server může připojit k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-129">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="eaaf6-130">*Zavřete všechny instance chromu* a znovu spusťte Chrome podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-130">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="eaaf6-131">Ladění aplikace</span><span class="sxs-lookup"><span data-stu-id="eaaf6-131">Debug the app</span></span>

<span data-ttu-id="eaaf6-132">Po spuštění Chrome s povoleným laděním vzdáleného ladění stiskněte kombinaci kláves otevře novou kartu ladicího programu. Za okamžik **zdroje** karta zobrazuje seznam sestavení .NET v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-132">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="eaaf6-133">Rozbalte každého sestavení a vyhledejte *.cs*/*.razor* zdrojové soubory pro ladění k dispozici.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-133">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="eaaf6-134">Nastavit zarážky, přejděte zpět na kartě aplikace a zarážky jsou přístupů, když kód spustí.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-134">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="eaaf6-135">Po zarážku přístupů, krokování (`F10`) prostřednictvím kódu nebo pokračovat (`F8`) obvykle spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-135">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

<span data-ttu-id="eaaf6-136">Blazor poskytuje ladicí proxy server tohoto implementuje [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) a argumentech protokolu. Informace specifické pro sítě.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-136">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="eaaf6-137">Při stisknutí klávesové zkratky ladění ukazuje Blazor Chrome DevTools na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-137">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="eaaf6-138">Proxy server připojí do okna prohlížeče se snaží ladění (tedy potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="eaaf6-138">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="eaaf6-139">Prohlížeč zdrojového mapování</span><span class="sxs-lookup"><span data-stu-id="eaaf6-139">Browser source maps</span></span>

<span data-ttu-id="eaaf6-140">Prohlížeč zdrojových mapování povolit prohlížeči mapování zkompilované soubory zpátky na jejich původní zdrojové soubory a běžně se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-140">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="eaaf6-141">Ale není aktuálně namapován Blazor C# přímo do jazyka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-141">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="eaaf6-142">Místo toho Blazor nemá překladu IL v prohlížeči, takže zdrojových mapování nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-142">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshooting-tip"></a><span data-ttu-id="eaaf6-143">Tip Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="eaaf6-143">Troubleshooting tip</span></span>

<span data-ttu-id="eaaf6-144">Pokud používáte k chybám, mohou pomoci následující tip:</span><span class="sxs-lookup"><span data-stu-id="eaaf6-144">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="eaaf6-145">V **ladicí program** kartě, otevřete vývojářské nástroje v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-145">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="eaaf6-146">V konzole, spusťte `localStorage.clear()` odebrat všechny zarážky.</span><span class="sxs-lookup"><span data-stu-id="eaaf6-146">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
