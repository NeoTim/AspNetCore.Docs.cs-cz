---
title: Ladění ASP.NET Core Blazor
author: guardrex
description: Zjistěte, jak ladit Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: blazor/debug
ms.openlocfilehash: 887edcd1db6942ba163857d48adfcf8efc8d7f5a
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152703"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="02e27-103">Ladění ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="02e27-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="02e27-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="02e27-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="02e27-105">*Časná* podpora je dostupná pro ladění na straně klienta Blazor aplikace běžící na WebAssembly v prohlížeči Chrome.</span><span class="sxs-lookup"><span data-stu-id="02e27-105">*Early* support exists for debugging client-side Blazor apps running on WebAssembly in Chrome.</span></span>

<span data-ttu-id="02e27-106">Možnosti ladicího programu jsou omezené.</span><span class="sxs-lookup"><span data-stu-id="02e27-106">Debugger capabilities are limited.</span></span> <span data-ttu-id="02e27-107">K dispozici scénáře patří:</span><span class="sxs-lookup"><span data-stu-id="02e27-107">Available scenarios include:</span></span>

* <span data-ttu-id="02e27-108">Nastavení a odebrat zarážky.</span><span class="sxs-lookup"><span data-stu-id="02e27-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="02e27-109">Krokování (`F10`) prostřednictvím kódu nebo pokračovat (`F8`) spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="02e27-109">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="02e27-110">V *lokální* zobrazovat, sledovat všechny místní proměnné typu hodnoty `int`, `string`, a `bool`.</span><span class="sxs-lookup"><span data-stu-id="02e27-110">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="02e27-111">Zobrazit zásobník volání, včetně volání řetězce, které přejít z jazyka JavaScript do technologie .NET a .NET pro jazyk JavaScript.</span><span class="sxs-lookup"><span data-stu-id="02e27-111">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="02e27-112">Můžete *nelze*:</span><span class="sxs-lookup"><span data-stu-id="02e27-112">You *can't*:</span></span>

* <span data-ttu-id="02e27-113">Sledovat hodnoty všech místních hodnot, které nejsou `int`, `string`, nebo `bool`.</span><span class="sxs-lookup"><span data-stu-id="02e27-113">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="02e27-114">Sledujte hodnoty pole nebo vlastnosti třídy.</span><span class="sxs-lookup"><span data-stu-id="02e27-114">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="02e27-115">Najeďte myší proměnné zobrazíte jejich hodnoty.</span><span class="sxs-lookup"><span data-stu-id="02e27-115">Hover over variables to see their values.</span></span>
* <span data-ttu-id="02e27-116">Vyhodnocení výrazů v konzole.</span><span class="sxs-lookup"><span data-stu-id="02e27-116">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="02e27-117">Krok přes asynchronní volání.</span><span class="sxs-lookup"><span data-stu-id="02e27-117">Step across async calls.</span></span>
* <span data-ttu-id="02e27-118">Proveďte většina jiných běžné scénáře ladění.</span><span class="sxs-lookup"><span data-stu-id="02e27-118">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="02e27-119">Vývoj pro další ladění scénářů je probíhající těžiště technickému týmu.</span><span class="sxs-lookup"><span data-stu-id="02e27-119">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="procedure"></a><span data-ttu-id="02e27-120">Postup</span><span class="sxs-lookup"><span data-stu-id="02e27-120">Procedure</span></span>

<span data-ttu-id="02e27-121">Ladění aplikací na straně klienta Blazor v prohlížeči Chrome:</span><span class="sxs-lookup"><span data-stu-id="02e27-121">To debug a client-side Blazor app in Chrome:</span></span>

* <span data-ttu-id="02e27-122">Vytvoření aplikace Blazor během `Debug` konfigurace (výchozí pro nepublikované aplikace).</span><span class="sxs-lookup"><span data-stu-id="02e27-122">Build a Blazor app in `Debug` configuration (the default for unpublished apps).</span></span>
* <span data-ttu-id="02e27-123">Blazor aplikaci spusťte v prohlížeči Chrome (verze 70 nebo vyšší).</span><span class="sxs-lookup"><span data-stu-id="02e27-123">Run the Blazor app in Chrome (version 70 or later).</span></span>
* <span data-ttu-id="02e27-124">Fokus klávesnice na aplikace (ne v panelu nástrojů pro vývojáře, který by měl pravděpodobně zavřete pro méně matoucí možnosti ladění) vyberte následující klávesové zkratky specifické pro Blazor:</span><span class="sxs-lookup"><span data-stu-id="02e27-124">With the keyboard focus on the app (not in the developer tools panel, which you should probably close for a less confusing debugging experience), select the following Blazor-specific keyboard shortcut:</span></span>
  * <span data-ttu-id="02e27-125">`Shift+Alt+D` na Windows/Linux</span><span class="sxs-lookup"><span data-stu-id="02e27-125">`Shift+Alt+D` on Windows/Linux</span></span>
  * <span data-ttu-id="02e27-126">`Shift+Cmd+D` V systému macOS</span><span class="sxs-lookup"><span data-stu-id="02e27-126">`Shift+Cmd+D` on macOS</span></span>

## <a name="enable-remote-debugging"></a><span data-ttu-id="02e27-127">Povolení vzdáleného ladění</span><span class="sxs-lookup"><span data-stu-id="02e27-127">Enable remote debugging</span></span>

<span data-ttu-id="02e27-128">Pokud je vzdálené ladění je zakázané, **nelze nalézt kartu prohlížeče laditelné** Chrome vygeneruje chybovou stránku.</span><span class="sxs-lookup"><span data-stu-id="02e27-128">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="02e27-129">Chybová stránka obsahuje pokyny ke spouštění Chrome pomocí portu ladění otevřete tak, aby Blazor ladicí proxy server může připojit k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02e27-129">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="02e27-130">*Zavřete všechny instance chromu* a znovu spusťte Chrome podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="02e27-130">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="02e27-131">Ladění aplikace</span><span class="sxs-lookup"><span data-stu-id="02e27-131">Debug the app</span></span>

<span data-ttu-id="02e27-132">Po spuštění Chrome s povoleným laděním vzdáleného ladění stiskněte kombinaci kláves otevře novou kartu ladicího programu. Za okamžik **zdroje** karta zobrazuje seznam sestavení .NET v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02e27-132">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="02e27-133">Rozbalte každého sestavení a vyhledejte *.cs*/ *.razor* zdrojové soubory pro ladění k dispozici.</span><span class="sxs-lookup"><span data-stu-id="02e27-133">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="02e27-134">Nastavit zarážky, přejděte zpět na kartě aplikace a zarážky jsou přístupů, když kód spustí.</span><span class="sxs-lookup"><span data-stu-id="02e27-134">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="02e27-135">Po zarážku přístupů, krokování (`F10`) prostřednictvím kódu nebo pokračovat (`F8`) obvykle spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="02e27-135">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

<span data-ttu-id="02e27-136">Blazor poskytuje ladicí proxy server tohoto implementuje [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) a argumentech protokolu. Informace specifické pro sítě.</span><span class="sxs-lookup"><span data-stu-id="02e27-136">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="02e27-137">Při stisknutí klávesové zkratky ladění ukazuje Blazor Chrome DevTools na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="02e27-137">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="02e27-138">Proxy server připojí do okna prohlížeče se snaží ladění (tedy potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="02e27-138">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="02e27-139">Prohlížeč zdrojového mapování</span><span class="sxs-lookup"><span data-stu-id="02e27-139">Browser source maps</span></span>

<span data-ttu-id="02e27-140">Prohlížeč zdrojových mapování povolit prohlížeči mapování zkompilované soubory zpátky na jejich původní zdrojové soubory a běžně se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="02e27-140">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="02e27-141">Ale není aktuálně namapován Blazor C# přímo do jazyka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="02e27-141">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="02e27-142">Místo toho Blazor nemá překladu IL v prohlížeči, takže zdrojových mapování nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="02e27-142">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshooting-tip"></a><span data-ttu-id="02e27-143">Tip Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="02e27-143">Troubleshooting tip</span></span>

<span data-ttu-id="02e27-144">Pokud používáte k chybám, mohou pomoci následující tip:</span><span class="sxs-lookup"><span data-stu-id="02e27-144">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="02e27-145">V **ladicí program** kartě, otevřete vývojářské nástroje v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="02e27-145">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="02e27-146">V konzole, spusťte `localStorage.clear()` odebrat všechny zarážky.</span><span class="sxs-lookup"><span data-stu-id="02e27-146">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
