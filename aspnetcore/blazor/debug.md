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
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159986"
---
# <a name="debug-aspnet-core-opno-locblazor"></a><span data-ttu-id="6e6fa-103">Blazor ASP.NET Core ladění</span><span class="sxs-lookup"><span data-stu-id="6e6fa-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="6e6fa-104">Daniel Skořepa</span><span class="sxs-lookup"><span data-stu-id="6e6fa-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6e6fa-105">Pro ladění Blazor webové sestavení pomocí nástrojů pro vývoj v Chromu v prohlížečích založených na chromu (Chrome/Edge) existuje *brzké* podpora.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-105">*Early* support exists for debugging Blazor WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="6e6fa-106">Práce probíhá:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-106">Work is in progress to:</span></span>

* <span data-ttu-id="6e6fa-107">Zcela povolit ladění v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="6e6fa-108">Povolit ladění v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="6e6fa-109">Možnosti ladicího programu jsou omezené.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="6e6fa-110">K dispozici jsou tyto scénáře:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-110">Available scenarios include:</span></span>

* <span data-ttu-id="6e6fa-111">Nastavení a odebrání zarážek.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="6e6fa-112">Jeden krok (`F10`) prostřednictvím kódu nebo obnovení (`F8`) provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="6e6fa-113">V zobrazení *místních* hodnot Sledujte hodnoty jakýchkoli místních proměnných typu `int`, `string`a `bool`.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="6e6fa-114">Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="6e6fa-115">*Nemůžete*:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-115">You *can't*:</span></span>

* <span data-ttu-id="6e6fa-116">Sledujte hodnoty všech národních prostředí, která nejsou `int`, `string`nebo `bool`.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="6e6fa-117">Sledujte hodnoty jakýchkoli vlastností nebo polí třídy.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="6e6fa-118">Pokud chcete zobrazit jejich hodnoty, najeďte myší na proměnné.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="6e6fa-119">Vyhodnoťte výrazy v konzole.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="6e6fa-120">Krok v rámci asynchronních volání.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-120">Step across async calls.</span></span>
* <span data-ttu-id="6e6fa-121">Proveďte většinu dalších běžných scénářů ladění.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="6e6fa-122">Vývoj dalších scénářů ladění je průběžným soustředěním na technický tým.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6e6fa-123">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6e6fa-123">Prerequisites</span></span>

<span data-ttu-id="6e6fa-124">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="6e6fa-125">Google Chrome (verze 70 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="6e6fa-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="6e6fa-126">Microsoft Edge Preview ([kanál pro vývoj](https://www.microsoftedgeinsider.com)na webu Edge)</span><span class="sxs-lookup"><span data-stu-id="6e6fa-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="6e6fa-127">Postup</span><span class="sxs-lookup"><span data-stu-id="6e6fa-127">Procedure</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e6fa-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e6fa-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="6e6fa-129">Podpora ladění v aplikaci Visual Studio je v rané fázi vývoje.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="6e6fa-130">Ladění **F5** není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="6e6fa-131">Spusťte aplikaci Blazor WebAssembly v konfiguraci `Debug` bez ladění (**Ctrl**+**F5** místo **F5**).</span><span class="sxs-lookup"><span data-stu-id="6e6fa-131">Run a Blazor WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="6e6fa-132">Otevřete vlastnosti ladění aplikace (poslední položka v nabídce **ladění** ) a zkopírujte **adresu URL aplikace**http.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="6e6fa-133">Přejděte k adrese HTTP (nikoli k adrese HTTPS) aplikace pomocí prohlížeče založeného na chromu (Edge beta nebo Chrome).</span><span class="sxs-lookup"><span data-stu-id="6e6fa-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="6e6fa-134">Umístěte fokus klávesnice do aplikace v okně prohlížeče, nikoli na panelu nástroje pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="6e6fa-135">Pro tento postup je nejlepší, aby byl panel nástrojů pro vývojáře uzavřený.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="6e6fa-136">Po spuštění ladění můžete panel nástroje pro vývojáře znovu otevřít.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="6e6fa-137">Vyberte následující klávesovou zkratku určenou pro Blazor:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-137">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="6e6fa-138">`Shift+Alt+D` ve Windows</span><span class="sxs-lookup"><span data-stu-id="6e6fa-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="6e6fa-139">`Shift+Cmd+D` na macOS</span><span class="sxs-lookup"><span data-stu-id="6e6fa-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="6e6fa-140">Pokud se zobrazí **karta nelze najít laditelné prohlížeče**, přečtěte si téma [Povolení vzdáleného ladění](#enable-remote-debugging).</span><span class="sxs-lookup"><span data-stu-id="6e6fa-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="6e6fa-141">Po povolení vzdáleného ladění:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="6e6fa-142">1\.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-142">1\.</span></span> <span data-ttu-id="6e6fa-143">Otevře se nové okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-143">A new browser window opens.</span></span> <span data-ttu-id="6e6fa-144">Zavřete předchozí okno.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-144">Close the prior window.</span></span>

   <span data-ttu-id="6e6fa-145">2\.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-145">2\.</span></span> <span data-ttu-id="6e6fa-146">Umístěte fokus klávesnice do aplikace v okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="6e6fa-147">3\.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-147">3\.</span></span> <span data-ttu-id="6e6fa-148">V novém okně prohlížeče vyberte klávesovou zkratku Blazor: `Shift+Alt+D` ve Windows nebo `Shift+Cmd+D` na macOS.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-148">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="6e6fa-149">4\.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-149">4\.</span></span> <span data-ttu-id="6e6fa-150">V prohlížeči se otevře karta **devtools** .</span><span class="sxs-lookup"><span data-stu-id="6e6fa-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="6e6fa-151">**V okně prohlížeče znovu vyberte kartu aplikace.**</span><span class="sxs-lookup"><span data-stu-id="6e6fa-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="6e6fa-152">Chcete-li připojit aplikaci k aplikaci Visual Studio, přečtěte si část [připojit k procesu v aplikaci Visual Studio](#attach-to-process-in-visual-studio) .</span><span class="sxs-lookup"><span data-stu-id="6e6fa-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6e6fa-153">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="6e6fa-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="6e6fa-154">Spusťte aplikaci Blazor WebAssembly v konfiguraci `Debug` předáním možnosti `--configuration Debug` příkazu [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-154">Run a Blazor WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="6e6fa-155">Přejděte do aplikace na adrese URL protokolu HTTP zobrazeném v okně prostředí.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="6e6fa-156">Umístěte fokus klávesnice na aplikaci, ne na panel nástroje pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="6e6fa-157">Pro tento postup je nejlepší, aby byl panel nástrojů pro vývojáře uzavřený.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="6e6fa-158">Po spuštění ladění můžete panel nástroje pro vývojáře znovu otevřít.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="6e6fa-159">Vyberte následující klávesovou zkratku určenou pro Blazor:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-159">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="6e6fa-160">`Shift+Alt+D` ve Windows</span><span class="sxs-lookup"><span data-stu-id="6e6fa-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="6e6fa-161">`Shift+Cmd+D` na macOS</span><span class="sxs-lookup"><span data-stu-id="6e6fa-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="6e6fa-162">Pokud se zobrazí **karta nelze najít laditelné prohlížeče**, přečtěte si téma [Povolení vzdáleného ladění](#enable-remote-debugging).</span><span class="sxs-lookup"><span data-stu-id="6e6fa-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="6e6fa-163">Po povolení vzdáleného ladění:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="6e6fa-164">1\.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-164">1\.</span></span> <span data-ttu-id="6e6fa-165">Otevře se nové okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-165">A new browser window opens.</span></span> <span data-ttu-id="6e6fa-166">Zavřete předchozí okno.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-166">Close the prior window.</span></span>

   <span data-ttu-id="6e6fa-167">2\.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-167">2\.</span></span> <span data-ttu-id="6e6fa-168">Umístěte fokus klávesnice do aplikace v okně prohlížeče, nikoli na panelu nástroje pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="6e6fa-169">3\.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-169">3\.</span></span> <span data-ttu-id="6e6fa-170">V novém okně prohlížeče vyberte klávesovou zkratku Blazor: `Shift+Alt+D` ve Windows nebo `Shift+Cmd+D` na macOS.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-170">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="6e6fa-171">Povolit vzdálené ladění</span><span class="sxs-lookup"><span data-stu-id="6e6fa-171">Enable remote debugging</span></span>

<span data-ttu-id="6e6fa-172">Pokud je vzdálené ladění zakázané, **nemůžete najít** chybovou stránku karty prohlížeče, která je vygenerovaná v Chrome.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="6e6fa-173">Chybová stránka obsahuje pokyny pro spuštění Chrome s otevřeným portem pro ladění, aby se proxy Blazor ladicího programu mohl připojit k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="6e6fa-174">*Zavřete všechny instance Chrome* a restartujte Chrome podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="6e6fa-175">Ladění aplikace</span><span class="sxs-lookup"><span data-stu-id="6e6fa-175">Debug the app</span></span>

<span data-ttu-id="6e6fa-176">Po spuštění aplikace Chrome se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="6e6fa-177">Rozbalte jednotlivá sestavení a vyhledejte soubory *. cs*/ *. Razor* , které jsou k dispozici pro ladění.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="6e6fa-178">Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="6e6fa-179">Po zablokování je jeden krok (`F10`) prostřednictvím kódu nebo obnovení kódu (`F8`) normálního spuštění.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="6e6fa-180"> poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="6e6fa-181">Při Blazor stisknutí klávesových zkratek ladění DevTools odkazuje na rozhraní Chrome na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="6e6fa-182">Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="6e6fa-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="6e6fa-183">Připojení k procesu v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e6fa-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="6e6fa-184">Připojení k procesu aplikace v aplikaci Visual Studio je *dočasný* scénář ladění pro Blazor WebAssembly, zatímco ladění **F5** je ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="6e6fa-185">Připojení procesu běžící aplikace k aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="6e6fa-186">V aplikaci Visual Studio vyberte možnost **ladění** > **připojit k procesu**.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="6e6fa-187">Jako **Typ připojení**vyberte **Chrome DevTools Protocol WebSocket (bez ověřování)** .</span><span class="sxs-lookup"><span data-stu-id="6e6fa-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="6e6fa-188">Pro **cíl připojení**vložte adresu http (nikoli adresu https) aplikace.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="6e6fa-189">Vyberte **aktualizovat** a aktualizujte položky v části **procesy k dispozici**.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="6e6fa-190">Vyberte proces prohlížeče, který chcete ladit, a vyberte **připojit**.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="6e6fa-191">V dialogovém okně **Vybrat typ kódu** vyberte typ kódu pro konkrétní prohlížeč, ke kterému se připojujete (Edge nebo Chrome), a pak vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="6e6fa-192">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="6e6fa-192">Browser source maps</span></span>

<span data-ttu-id="6e6fa-193">Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="6e6fa-194">Blazor ale v tuto chvíli není C# přímo namapována na JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="6e6fa-195">Místo toho Blazor provede interpretaci IL v prohlížeči, takže zdrojová mapování nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6e6fa-196">Řešení problémů</span><span class="sxs-lookup"><span data-stu-id="6e6fa-196">Troubleshoot</span></span>

<span data-ttu-id="6e6fa-197">Pokud pracujete s chybami, může vám následující Tip:</span><span class="sxs-lookup"><span data-stu-id="6e6fa-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="6e6fa-198">Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="6e6fa-199">V konzole spusťte `localStorage.clear()` a odeberte všechny zarážky.</span><span class="sxs-lookup"><span data-stu-id="6e6fa-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
