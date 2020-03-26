---
title: Ladění ASP.NET Core Blazor sestavení
author: guardrex
description: Naučte se ladit aplikace Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 5dbc900ab68682068a7f9e3ffdaabef89a0c7798
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306474"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="62215-103">Ladění ASP.NET Core Blazor sestavení</span><span class="sxs-lookup"><span data-stu-id="62215-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="62215-104">Daniel Skořepa</span><span class="sxs-lookup"><span data-stu-id="62215-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="62215-105">aplikace Blazor WebAssembly je možné ladit pomocí nástrojů pro vývoj v prohlížeči v prohlížečích založených na chromu (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="62215-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="62215-106">Alternativně můžete aplikaci ladit pomocí sady Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="62215-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="62215-107">K dispozici jsou tyto scénáře:</span><span class="sxs-lookup"><span data-stu-id="62215-107">Available scenarios include:</span></span>

* <span data-ttu-id="62215-108">Nastavení a odebrání zarážek.</span><span class="sxs-lookup"><span data-stu-id="62215-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="62215-109">Spusťte aplikaci s podporou ladění v aplikaci Visual Studio a Visual Studio Code (podpora<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="62215-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="62215-110">Jednoduchý krok (<kbd>F10</kbd>) prostřednictvím kódu.</span><span class="sxs-lookup"><span data-stu-id="62215-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="62215-111">Pokračuje v provádění kódu pomocí <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v aplikaci Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="62215-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="62215-112">V zobrazení *místních* hodnot Sledujte hodnoty místních proměnných.</span><span class="sxs-lookup"><span data-stu-id="62215-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="62215-113">Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="62215-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="62215-114">Teď *nemůžete*:</span><span class="sxs-lookup"><span data-stu-id="62215-114">For now, you *can't*:</span></span>

* <span data-ttu-id="62215-115">Zkontrolujte pole.</span><span class="sxs-lookup"><span data-stu-id="62215-115">Inspect arrays.</span></span>
* <span data-ttu-id="62215-116">Najeďte myší na kontrolu členů.</span><span class="sxs-lookup"><span data-stu-id="62215-116">Hover to inspect members.</span></span>
* <span data-ttu-id="62215-117">Krok ladění do spravovaného kódu nebo z něj.</span><span class="sxs-lookup"><span data-stu-id="62215-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="62215-118">Má plnou podporu pro kontrolu hodnotových typů.</span><span class="sxs-lookup"><span data-stu-id="62215-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="62215-119">Přerušit při neošetřených výjimkách.</span><span class="sxs-lookup"><span data-stu-id="62215-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="62215-120">Při spuštění aplikace se zarážky volání.</span><span class="sxs-lookup"><span data-stu-id="62215-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="62215-121">Ladění aplikace pomocí pracovního procesu služby.</span><span class="sxs-lookup"><span data-stu-id="62215-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="62215-122">V nadcházejících vydáních budeme dál zlepšovat možnosti ladění.</span><span class="sxs-lookup"><span data-stu-id="62215-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="62215-123">Požadavky</span><span class="sxs-lookup"><span data-stu-id="62215-123">Prerequisites</span></span>

<span data-ttu-id="62215-124">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="62215-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="62215-125">Microsoft Edge (verze 80 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="62215-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="62215-126">Google Chrome (verze 70 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="62215-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="62215-127">Povolit ladění pro Visual Studio a Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62215-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="62215-128">Ladění je povoleno automaticky pro nové projekty, které jsou vytvořeny pomocí šablony projektu ASP.NET Core 3,2 Preview 3 nebo vyšší Blazor.</span><span class="sxs-lookup"><span data-stu-id="62215-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template.</span></span>

<span data-ttu-id="62215-129">Chcete-li povolit ladění pro existující aplikaci Blazor WebAssembly, aktualizujte soubor *launchSettings. JSON* ve složce spouštěný projekt tak, aby do každého spouštěcího profilu zahrnoval následující vlastnost `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="62215-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="62215-130">Po aktualizaci by soubor *launchSettings. JSON* měl vypadat podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="62215-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="62215-131">Vlastnost `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="62215-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="62215-132">Umožňuje rozhraní IDE zjistit, že aplikace je Blazor aplikace WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="62215-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="62215-133">Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím proxy serveru pro ladění Blazor.</span><span class="sxs-lookup"><span data-stu-id="62215-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="62215-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62215-134">Visual Studio</span></span>

<span data-ttu-id="62215-135">Ladění aplikace Blazor WebAssembly v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="62215-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="62215-136">Ujistěte se, že máte [nainstalovanou nejnovější verzi Preview sady Visual Studio 2019 16,6](https://visualstudio.com/preview) (Preview 2 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="62215-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="62215-137">Vytvořte novou ASP.NET Core hostovanou Blazor aplikaci WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="62215-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="62215-138">Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="62215-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="62215-139">V metodě `IncrementCount` nastavte zarážku v *čítači. Razor* .</span><span class="sxs-lookup"><span data-stu-id="62215-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="62215-140">Přejděte na kartu **čítač** a vyberte tlačítko, kde se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="62215-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="62215-142">Podívejte se na hodnotu pole `currentCount` v okně místní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="62215-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Zobrazit místní hodnoty](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="62215-144">Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="62215-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="62215-145">Při ladění aplikace Blazor WebAssembly můžete také ladit kód serveru:</span><span class="sxs-lookup"><span data-stu-id="62215-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="62215-146">Nastavte zarážku na stránce *FetchData. Razor* v `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="62215-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="62215-147">Nastavte zarážku v `WeatherForecastController` v metodě `Get` Action.</span><span class="sxs-lookup"><span data-stu-id="62215-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="62215-148">Přejděte na kartu **načíst data** , abyste narazili na první zarážku v součásti `FetchData` těsně předtím, než VYDÁ požadavek HTTP na server:</span><span class="sxs-lookup"><span data-stu-id="62215-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Ladit načtení dat](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="62215-150">Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a pak na serveru stiskněte zarážku na `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="62215-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="62215-152">Stiskněte znovu <kbd>F5</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na vykreslenou tabulku předpovědi počasí.</span><span class="sxs-lookup"><span data-stu-id="62215-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="62215-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62215-153">Visual Studio Code</span></span>

<span data-ttu-id="62215-154">Ladění aplikace Blazor WebAssembly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="62215-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="62215-155">Nainstalujte rozšíření [a rozšíření ladicí program JavaScriptu (v noci) s `debug.javascript.usePreview` nastavenou na `true`. C# ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)</span><span class="sxs-lookup"><span data-stu-id="62215-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Rozšíření](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS Preview ladicího programu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="62215-158">Otevře existující aplikaci Blazor WebAssembly s povoleným laděním.</span><span class="sxs-lookup"><span data-stu-id="62215-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="62215-159">Pokud obdržíte následující oznámení, že pro povolení ladění je potřeba další nastavení, zkontrolujte, že máte nainstalované správné rozšíření a že je povolené ladění JavaScriptu ve verzi Preview, a pak znovu načtěte okno:</span><span class="sxs-lookup"><span data-stu-id="62215-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Další požadovaným klíčovým slovům nastavení](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="62215-161">Oznámení nabízí přidání požadovaných prostředků do aplikace pro sestavování a ladění.</span><span class="sxs-lookup"><span data-stu-id="62215-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="62215-162">Vyberte **Ano**:</span><span class="sxs-lookup"><span data-stu-id="62215-162">Select **Yes**:</span></span>

     ![Přidat požadované prostředky](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="62215-164">Spuštění aplikace v ladicím programu je proces se dvěma kroky:</span><span class="sxs-lookup"><span data-stu-id="62215-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="62215-165">1\.</span><span class="sxs-lookup"><span data-stu-id="62215-165">1\.</span></span> <span data-ttu-id="62215-166">**Nejdřív**spusťte aplikaci pomocí konfigurace spuštění **.net Core (Blazor Standalone)** .</span><span class="sxs-lookup"><span data-stu-id="62215-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="62215-167">2\.</span><span class="sxs-lookup"><span data-stu-id="62215-167">2\.</span></span> <span data-ttu-id="62215-168">**Po spuštění aplikace**spusťte prohlížeč pomocí **ladění .NET Core Blazor webové sestavení v** konfiguraci spouštění Chrome (vyžaduje Chrome).</span><span class="sxs-lookup"><span data-stu-id="62215-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="62215-169">Pokud chcete místo Chromu použít Edge, změňte `type` konfigurace spuštění v *. VSCode/Launch. JSON* z `pwa-chrome` na `pwa-edge`.</span><span class="sxs-lookup"><span data-stu-id="62215-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-edge`.</span></span>

1. <span data-ttu-id="62215-170">Nastavte zarážku v metodě `IncrementCount` v součásti `Counter` a potom vyberte tlačítko, kterým se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="62215-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Čítač ladění v VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="62215-172">Ladit v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="62215-172">Debug in the browser</span></span>

1. <span data-ttu-id="62215-173">Spusťte ladicí sestavení aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="62215-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="62215-174">Stiskněte <kbd>Shift</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="62215-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="62215-175">Prohlížeč musí být spuštěn s povoleným vzdáleným laděním.</span><span class="sxs-lookup"><span data-stu-id="62215-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="62215-176">Pokud je vzdálené ladění zakázané, vygeneruje se chybová stránka karty prohlížeče, která se **nedá najít** .</span><span class="sxs-lookup"><span data-stu-id="62215-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="62215-177">Chybová stránka obsahuje pokyny pro spuštění prohlížeče s otevřeným portem pro ladění, aby se proxy Blazor ladicího programu mohl připojit k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="62215-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="62215-178">*Zavřete všechny instance prohlížeče* a restartujte prohlížeč podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="62215-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="62215-179">Po spuštění prohlížeče se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="62215-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="62215-180">Rozbalte jednotlivá sestavení a vyhledejte soubory *. cs*/ *. Razor* , které jsou k dispozici pro ladění.</span><span class="sxs-lookup"><span data-stu-id="62215-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="62215-181">Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="62215-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="62215-182">Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.</span><span class="sxs-lookup"><span data-stu-id="62215-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="62215-183"> poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť.</span><span class="sxs-lookup"><span data-stu-id="62215-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="62215-184">Při Blazor stisknutí klávesových zkratek ladění DevTools odkazuje na rozhraní Chrome na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="62215-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="62215-185">Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="62215-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="62215-186">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="62215-186">Browser source maps</span></span>

<span data-ttu-id="62215-187">Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="62215-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="62215-188">Blazor ale v tuto chvíli není C# přímo namapována na JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="62215-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="62215-189">Místo toho Blazor provede interpretaci IL v prohlížeči, takže zdrojová mapování nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="62215-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="62215-190">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="62215-190">Troubleshoot</span></span>

<span data-ttu-id="62215-191">Pokud pracujete s chybami, může vám následující Tip:</span><span class="sxs-lookup"><span data-stu-id="62215-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="62215-192">Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="62215-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="62215-193">V konzole spusťte `localStorage.clear()` a odeberte všechny zarážky.</span><span class="sxs-lookup"><span data-stu-id="62215-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
