---
<span data-ttu-id="cefbd-101">title: ' ladění ASP.NET Core Blazor WebAssembly ' Autor: guardrex Description: ' Naučte se ladit Blazor aplikace. '</span><span class="sxs-lookup"><span data-stu-id="cefbd-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="cefbd-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/29/2020 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cefbd-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/29/2020 no-loc:</span></span>
- <span data-ttu-id="cefbd-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefbd-103">'Blazor'</span></span>
- <span data-ttu-id="cefbd-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefbd-104">'Identity'</span></span>
- <span data-ttu-id="cefbd-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefbd-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefbd-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefbd-106">'Razor'</span></span>
- <span data-ttu-id="cefbd-107">SignalRUID: blazor/Debug</span><span class="sxs-lookup"><span data-stu-id="cefbd-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="cefbd-108">Ladění ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cefbd-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="cefbd-109">Daniel Skořepa</span><span class="sxs-lookup"><span data-stu-id="cefbd-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="cefbd-110">Aplikace WebAssembly se dají ladit pomocí nástrojů pro vývoj v prohlížeči v prohlížečích založených na chromu (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="cefbd-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="cefbd-111">Alternativně můžete aplikaci ladit pomocí sady Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cefbd-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="cefbd-112">K dispozici jsou tyto scénáře:</span><span class="sxs-lookup"><span data-stu-id="cefbd-112">Available scenarios include:</span></span>

* <span data-ttu-id="cefbd-113">Nastavení a odebrání zarážek.</span><span class="sxs-lookup"><span data-stu-id="cefbd-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="cefbd-114">Spusťte aplikaci s podporou ladění v aplikaci Visual Studio a Visual Studio Code (podpora<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="cefbd-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="cefbd-115">Jednoduchý krok (<kbd>F10</kbd>) prostřednictvím kódu.</span><span class="sxs-lookup"><span data-stu-id="cefbd-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="cefbd-116">Pokračuje v provádění kódu pomocí <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v aplikaci Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cefbd-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="cefbd-117">V zobrazení *místních* hodnot Sledujte hodnoty místních proměnných.</span><span class="sxs-lookup"><span data-stu-id="cefbd-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="cefbd-118">Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="cefbd-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="cefbd-119">Teď *nemůžete*:</span><span class="sxs-lookup"><span data-stu-id="cefbd-119">For now, you *can't*:</span></span>

* <span data-ttu-id="cefbd-120">Přerušit při neošetřených výjimkách.</span><span class="sxs-lookup"><span data-stu-id="cefbd-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="cefbd-121">Při spuštění aplikace se zarážky volání.</span><span class="sxs-lookup"><span data-stu-id="cefbd-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="cefbd-122">V nadcházejících vydáních budeme dál zlepšovat možnosti ladění.</span><span class="sxs-lookup"><span data-stu-id="cefbd-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cefbd-123">Požadavky</span><span class="sxs-lookup"><span data-stu-id="cefbd-123">Prerequisites</span></span>

<span data-ttu-id="cefbd-124">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="cefbd-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="cefbd-125">Microsoft Edge (verze 80 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="cefbd-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="cefbd-126">Google Chrome (verze 70 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="cefbd-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="cefbd-127">Povolit ladění pro Visual Studio a Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cefbd-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="cefbd-128">Chcete-li povolit ladění pro existující Blazor aplikaci WebAssembly, aktualizujte soubor *launchSettings. JSON* ve složce spouštěný projekt tak, aby do `inspectUri` každého spouštěcího profilu zahrnoval následující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="cefbd-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="cefbd-129">Po aktualizaci by soubor *launchSettings. JSON* měl vypadat podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="cefbd-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="cefbd-130">`inspectUri`Vlastnost:</span><span class="sxs-lookup"><span data-stu-id="cefbd-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="cefbd-131">Umožňuje rozhraní IDE zjistit, že aplikace je Blazor aplikace WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="cefbd-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="cefbd-132">Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím Blazor ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="cefbd-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="cefbd-133">Zástupný text pro protokol WebSockets ( `wsProtocol` ), hostitele ( `url.hostname` ), port ( `url.port` ) a identifikátor URI pro inspektor v spuštěném prohlížeči ( `browserInspectUri` ) jsou poskytovány rozhraním.</span><span class="sxs-lookup"><span data-stu-id="cefbd-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="cefbd-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cefbd-134">Visual Studio</span></span>

<span data-ttu-id="cefbd-135">Ladění Blazor aplikace WebAssembly v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="cefbd-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="cefbd-136">Vytvoří novou ASP.NET Core hostovanou Blazor aplikaci WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="cefbd-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="cefbd-137">Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="cefbd-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="cefbd-138">V metodě nastavte zarážku v *čítači. Razor* `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="cefbd-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="cefbd-139">Přejděte na kartu **čítač** a vyberte tlačítko, kde se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="cefbd-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="cefbd-141">Podívejte se na hodnotu `currentCount` pole v okně místní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="cefbd-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Zobrazit místní hodnoty](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="cefbd-143">Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="cefbd-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="cefbd-144">Při ladění Blazor aplikace pro WebAssembly můžete také ladit kód serveru:</span><span class="sxs-lookup"><span data-stu-id="cefbd-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="cefbd-145">Nastavte zarážku na stránce *FetchData. Razor* v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="cefbd-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="cefbd-146">Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.</span><span class="sxs-lookup"><span data-stu-id="cefbd-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="cefbd-147">Přejděte na kartu **načíst data** , abyste narazili na první zarážku v `FetchData` součásti těsně předtím, než vydá požadavek HTTP na server:</span><span class="sxs-lookup"><span data-stu-id="cefbd-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Ladit načtení dat](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="cefbd-149">Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a potom stiskněte zarážku na serveru v `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="cefbd-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="cefbd-151">Stiskněte znovu <kbd>F5</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na vykreslenou tabulku předpovědi počasí.</span><span class="sxs-lookup"><span data-stu-id="cefbd-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="cefbd-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cefbd-152">Visual Studio Code</span></span>

<span data-ttu-id="cefbd-153">Ladění Blazor aplikace WebAssembly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="cefbd-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="cefbd-154">Nainstalujte [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [ladicí program JavaScriptu (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true` .</span><span class="sxs-lookup"><span data-stu-id="cefbd-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Rozšíření](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS Preview ladicího programu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="cefbd-157">Otevře existující Blazor aplikaci WebAssembly s povoleným laděním.</span><span class="sxs-lookup"><span data-stu-id="cefbd-157">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="cefbd-158">Pokud obdržíte následující oznámení, že pro povolení ladění je potřeba další nastavení, zkontrolujte, že máte nainstalované správné rozšíření a že je povolené ladění JavaScriptu ve verzi Preview, a pak znovu načtěte okno:</span><span class="sxs-lookup"><span data-stu-id="cefbd-158">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Vyžaduje se další instalace.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="cefbd-160">Oznámení nabízí přidání požadovaných prostředků do aplikace pro sestavování a ladění.</span><span class="sxs-lookup"><span data-stu-id="cefbd-160">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="cefbd-161">Vyberte **Ano**:</span><span class="sxs-lookup"><span data-stu-id="cefbd-161">Select **Yes**:</span></span>

     ![Přidat požadované prostředky](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="cefbd-163">Spuštění aplikace v ladicím programu je proces se dvěma kroky:</span><span class="sxs-lookup"><span data-stu-id="cefbd-163">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="cefbd-164">1 \.</span><span class="sxs-lookup"><span data-stu-id="cefbd-164">1\.</span></span> <span data-ttu-id="cefbd-165">**Nejdřív**spusťte aplikaci pomocí konfigurace spuštění **.NET Core Launch ( Blazor Standalone)** .</span><span class="sxs-lookup"><span data-stu-id="cefbd-165">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="cefbd-166">2 \.</span><span class="sxs-lookup"><span data-stu-id="cefbd-166">2\.</span></span> <span data-ttu-id="cefbd-167">**Po spuštění aplikace**spusťte prohlížeč pomocí **ladicího Blazor webového sestavení .NET Core v** konfiguraci spuštění Chrome (vyžaduje Chrome).</span><span class="sxs-lookup"><span data-stu-id="cefbd-167">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="cefbd-168">Pokud chcete místo Chromu použít Edge, změňte `type` konfiguraci spuštění v *. VSCode/Launch. JSON* z `pwa-chrome` na `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="cefbd-168">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="cefbd-169">Nastavte zarážku v `IncrementCount` metodě v `Counter` součásti a potom vyberte tlačítko, kterým se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="cefbd-169">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Čítač ladění v VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="cefbd-171">Ladit v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="cefbd-171">Debug in the browser</span></span>

1. <span data-ttu-id="cefbd-172">Spusťte ladicí sestavení aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="cefbd-172">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="cefbd-173">Stiskněte <kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="cefbd-173">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="cefbd-174">Prohlížeč musí být spuštěn s povoleným vzdáleným laděním.</span><span class="sxs-lookup"><span data-stu-id="cefbd-174">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="cefbd-175">Pokud je vzdálené ladění zakázané, vygeneruje se chybová stránka karty prohlížeče, která se **nedá najít** .</span><span class="sxs-lookup"><span data-stu-id="cefbd-175">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="cefbd-176">Chybová stránka obsahuje pokyny pro spuštění prohlížeče s otevřeným portem pro ladění, aby se Blazor ladicí proxy server mohl připojit k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cefbd-176">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="cefbd-177">*Zavřete všechny instance prohlížeče* a restartujte prohlížeč podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="cefbd-177">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="cefbd-178">Po spuštění prohlížeče se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cefbd-178">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="cefbd-179">Rozbalte jednotlivá sestavení a vyhledejte zdrojové soubory *. cs* / *. Razor* k dispozici pro ladění.</span><span class="sxs-lookup"><span data-stu-id="cefbd-179">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="cefbd-180">Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="cefbd-180">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="cefbd-181">Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.</span><span class="sxs-lookup"><span data-stu-id="cefbd-181">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="cefbd-182">poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť.</span><span class="sxs-lookup"><span data-stu-id="cefbd-182"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="cefbd-183">Když se stiskne klávesová zkratka ladění, navede Blazor devtools Chrome na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="cefbd-183">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="cefbd-184">Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="cefbd-184">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="cefbd-185">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="cefbd-185">Browser source maps</span></span>

<span data-ttu-id="cefbd-186">Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="cefbd-186">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="cefbd-187">V Blazor současné době ale nemapuje C# přímo na JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="cefbd-187">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="cefbd-188">Místo toho Blazor provede výklad il v prohlížeči, takže zdrojové mapy nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="cefbd-188">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cefbd-189">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="cefbd-189">Troubleshoot</span></span>

<span data-ttu-id="cefbd-190">Pokud dochází k chybám, může vám pomáhat následující tipy:</span><span class="sxs-lookup"><span data-stu-id="cefbd-190">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="cefbd-191">Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="cefbd-191">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="cefbd-192">V konzole nástroje spusťte příkaz `localStorage.clear()` k odebrání všech zarážek.</span><span class="sxs-lookup"><span data-stu-id="cefbd-192">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="cefbd-193">Potvrďte, že jste nainstalovali a důvěřujete ASP.NET Core certifikát pro vývoj HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cefbd-193">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="cefbd-194">Další informace naleznete v tématu <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="cefbd-194">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
