---
<span data-ttu-id="a31eb-101">title: ' Debug ASP.NET Core Blazor WebAssembly ' Author: Description: ' Naučte se ladit Blazor aplikace. '</span><span class="sxs-lookup"><span data-stu-id="a31eb-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="a31eb-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a31eb-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a31eb-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a31eb-103">'Blazor'</span></span>
- <span data-ttu-id="a31eb-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a31eb-104">'Identity'</span></span>
- <span data-ttu-id="a31eb-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a31eb-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="a31eb-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a31eb-106">'Razor'</span></span>
- <span data-ttu-id="a31eb-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="a31eb-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="a31eb-108">Ladění ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a31eb-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="a31eb-109">Daniel Skořepa</span><span class="sxs-lookup"><span data-stu-id="a31eb-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="a31eb-110">Aplikace WebAssembly se dají ladit pomocí nástrojů pro vývoj v prohlížeči v prohlížečích založených na chromu (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="a31eb-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="a31eb-111">Alternativně můžete aplikaci ladit pomocí sady Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a31eb-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="a31eb-112">K dispozici jsou tyto scénáře:</span><span class="sxs-lookup"><span data-stu-id="a31eb-112">Available scenarios include:</span></span>

* <span data-ttu-id="a31eb-113">Nastavení a odebrání zarážek.</span><span class="sxs-lookup"><span data-stu-id="a31eb-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="a31eb-114">Spusťte aplikaci s podporou ladění v aplikaci Visual Studio a Visual Studio Code (podpora<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="a31eb-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="a31eb-115">Jednoduchý krok (<kbd>F10</kbd>) prostřednictvím kódu.</span><span class="sxs-lookup"><span data-stu-id="a31eb-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="a31eb-116">Pokračuje v provádění kódu pomocí <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v aplikaci Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a31eb-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="a31eb-117">V zobrazení *místních* hodnot Sledujte hodnoty místních proměnných.</span><span class="sxs-lookup"><span data-stu-id="a31eb-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="a31eb-118">Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="a31eb-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="a31eb-119">Teď *nemůžete*:</span><span class="sxs-lookup"><span data-stu-id="a31eb-119">For now, you *can't*:</span></span>

* <span data-ttu-id="a31eb-120">Zkontrolujte pole.</span><span class="sxs-lookup"><span data-stu-id="a31eb-120">Inspect arrays.</span></span>
* <span data-ttu-id="a31eb-121">Najeďte myší na kontrolu členů.</span><span class="sxs-lookup"><span data-stu-id="a31eb-121">Hover to inspect members.</span></span>
* <span data-ttu-id="a31eb-122">Krok ladění do spravovaného kódu nebo z něj.</span><span class="sxs-lookup"><span data-stu-id="a31eb-122">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="a31eb-123">Má plnou podporu pro kontrolu hodnotových typů.</span><span class="sxs-lookup"><span data-stu-id="a31eb-123">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="a31eb-124">Přerušit při neošetřených výjimkách.</span><span class="sxs-lookup"><span data-stu-id="a31eb-124">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="a31eb-125">Při spuštění aplikace se zarážky volání.</span><span class="sxs-lookup"><span data-stu-id="a31eb-125">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="a31eb-126">Ladění aplikace pomocí pracovního procesu služby.</span><span class="sxs-lookup"><span data-stu-id="a31eb-126">Debug an app with a service worker.</span></span>

<span data-ttu-id="a31eb-127">V nadcházejících vydáních budeme dál zlepšovat možnosti ladění.</span><span class="sxs-lookup"><span data-stu-id="a31eb-127">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a31eb-128">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a31eb-128">Prerequisites</span></span>

<span data-ttu-id="a31eb-129">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="a31eb-129">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="a31eb-130">Microsoft Edge (verze 80 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a31eb-130">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="a31eb-131">Google Chrome (verze 70 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="a31eb-131">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="a31eb-132">Povolit ladění pro Visual Studio a Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a31eb-132">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="a31eb-133">Chcete-li povolit ladění pro existující Blazor aplikaci WebAssembly, aktualizujte soubor *launchSettings. JSON* ve složce spouštěný projekt tak, aby do `inspectUri` každého spouštěcího profilu zahrnoval následující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="a31eb-133">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="a31eb-134">Po aktualizaci by soubor *launchSettings. JSON* měl vypadat podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a31eb-134">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="a31eb-135">`inspectUri`Vlastnost:</span><span class="sxs-lookup"><span data-stu-id="a31eb-135">The `inspectUri` property:</span></span>

* <span data-ttu-id="a31eb-136">Umožňuje rozhraní IDE zjistit, že aplikace je Blazor aplikace WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="a31eb-136">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="a31eb-137">Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím Blazor ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="a31eb-137">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="a31eb-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a31eb-138">Visual Studio</span></span>

<span data-ttu-id="a31eb-139">Ladění Blazor aplikace WebAssembly v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a31eb-139">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="a31eb-140">Vytvoří novou ASP.NET Core hostovanou Blazor aplikaci WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="a31eb-140">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="a31eb-141">Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="a31eb-141">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="a31eb-142">V metodě nastavte zarážku v *čítači. Razor* `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="a31eb-142">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="a31eb-143">Přejděte na kartu **čítač** a vyberte tlačítko, kde se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="a31eb-143">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="a31eb-145">Podívejte se na hodnotu `currentCount` pole v okně místní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a31eb-145">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Zobrazit místní hodnoty](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="a31eb-147">Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="a31eb-147">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="a31eb-148">Při ladění Blazor aplikace pro WebAssembly můžete také ladit kód serveru:</span><span class="sxs-lookup"><span data-stu-id="a31eb-148">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="a31eb-149">Nastavte zarážku na stránce *FetchData. Razor* v `OnInitializedAsync` .</span><span class="sxs-lookup"><span data-stu-id="a31eb-149">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="a31eb-150">Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.</span><span class="sxs-lookup"><span data-stu-id="a31eb-150">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="a31eb-151">Přejděte na kartu **načíst data** , abyste narazili na první zarážku v `FetchData` součásti těsně předtím, než vydá požadavek HTTP na server:</span><span class="sxs-lookup"><span data-stu-id="a31eb-151">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Ladit načtení dat](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="a31eb-153">Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a potom stiskněte zarážku na serveru v `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="a31eb-153">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="a31eb-155">Stiskněte znovu <kbd>F5</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na vykreslenou tabulku předpovědi počasí.</span><span class="sxs-lookup"><span data-stu-id="a31eb-155">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="a31eb-156">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a31eb-156">Visual Studio Code</span></span>

<span data-ttu-id="a31eb-157">Ladění Blazor aplikace WebAssembly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="a31eb-157">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="a31eb-158">Nainstalujte [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [ladicí program JavaScriptu (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true` .</span><span class="sxs-lookup"><span data-stu-id="a31eb-158">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Rozšíření](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS Preview ladicího programu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="a31eb-161">Otevře existující Blazor aplikaci WebAssembly s povoleným laděním.</span><span class="sxs-lookup"><span data-stu-id="a31eb-161">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="a31eb-162">Pokud obdržíte následující oznámení, že pro povolení ladění je potřeba další nastavení, zkontrolujte, že máte nainstalované správné rozšíření a že je povolené ladění JavaScriptu ve verzi Preview, a pak znovu načtěte okno:</span><span class="sxs-lookup"><span data-stu-id="a31eb-162">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Vyžaduje se další instalace.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="a31eb-164">Oznámení nabízí přidání požadovaných prostředků do aplikace pro sestavování a ladění.</span><span class="sxs-lookup"><span data-stu-id="a31eb-164">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="a31eb-165">Vyberte **Ano**:</span><span class="sxs-lookup"><span data-stu-id="a31eb-165">Select **Yes**:</span></span>

     ![Přidat požadované prostředky](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="a31eb-167">Spuštění aplikace v ladicím programu je proces se dvěma kroky:</span><span class="sxs-lookup"><span data-stu-id="a31eb-167">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="a31eb-168">1 \.</span><span class="sxs-lookup"><span data-stu-id="a31eb-168">1\.</span></span> <span data-ttu-id="a31eb-169">**Nejdřív**spusťte aplikaci pomocí konfigurace spuštění **.NET Core Launch ( Blazor Standalone)** .</span><span class="sxs-lookup"><span data-stu-id="a31eb-169">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="a31eb-170">2 \.</span><span class="sxs-lookup"><span data-stu-id="a31eb-170">2\.</span></span> <span data-ttu-id="a31eb-171">**Po spuštění aplikace**spusťte prohlížeč pomocí **ladicího Blazor webového sestavení .NET Core v** konfiguraci spuštění Chrome (vyžaduje Chrome).</span><span class="sxs-lookup"><span data-stu-id="a31eb-171">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="a31eb-172">Pokud chcete místo Chromu použít Edge, změňte `type` konfiguraci spuštění v *. VSCode/Launch. JSON* z `pwa-chrome` na `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="a31eb-172">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="a31eb-173">Nastavte zarážku v `IncrementCount` metodě v `Counter` součásti a potom vyberte tlačítko, kterým se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="a31eb-173">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Čítač ladění v VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="a31eb-175">Ladit v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="a31eb-175">Debug in the browser</span></span>

1. <span data-ttu-id="a31eb-176">Spusťte ladicí sestavení aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a31eb-176">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="a31eb-177">Stiskněte <kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="a31eb-177">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="a31eb-178">Prohlížeč musí být spuštěn s povoleným vzdáleným laděním.</span><span class="sxs-lookup"><span data-stu-id="a31eb-178">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="a31eb-179">Pokud je vzdálené ladění zakázané, vygeneruje se chybová stránka karty prohlížeče, která se **nedá najít** .</span><span class="sxs-lookup"><span data-stu-id="a31eb-179">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="a31eb-180">Chybová stránka obsahuje pokyny pro spuštění prohlížeče s otevřeným portem pro ladění, aby se Blazor ladicí proxy server mohl připojit k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a31eb-180">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="a31eb-181">*Zavřete všechny instance prohlížeče* a restartujte prohlížeč podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="a31eb-181">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="a31eb-182">Po spuštění prohlížeče se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a31eb-182">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="a31eb-183">Rozbalte jednotlivá sestavení a vyhledejte zdrojové soubory *. cs* / *. Razor* k dispozici pro ladění.</span><span class="sxs-lookup"><span data-stu-id="a31eb-183">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="a31eb-184">Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="a31eb-184">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="a31eb-185">Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.</span><span class="sxs-lookup"><span data-stu-id="a31eb-185">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="a31eb-186">poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť.</span><span class="sxs-lookup"><span data-stu-id="a31eb-186"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="a31eb-187">Když se stiskne klávesová zkratka ladění, navede Blazor devtools Chrome na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="a31eb-187">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="a31eb-188">Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="a31eb-188">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="a31eb-189">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="a31eb-189">Browser source maps</span></span>

<span data-ttu-id="a31eb-190">Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a31eb-190">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="a31eb-191">V Blazor současné době ale nemapuje C# přímo na JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="a31eb-191">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="a31eb-192">Místo toho Blazor provede výklad il v prohlížeči, takže zdrojové mapy nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="a31eb-192">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a31eb-193">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="a31eb-193">Troubleshoot</span></span>

<span data-ttu-id="a31eb-194">Pokud pracujete s chybami, může vám následující Tip:</span><span class="sxs-lookup"><span data-stu-id="a31eb-194">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="a31eb-195">Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a31eb-195">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="a31eb-196">V konzole nástroje spusťte příkaz `localStorage.clear()` k odebrání všech zarážek.</span><span class="sxs-lookup"><span data-stu-id="a31eb-196">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
