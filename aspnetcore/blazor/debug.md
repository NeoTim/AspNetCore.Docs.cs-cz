---
<span data-ttu-id="e5a3c-101">title: ' ladění ASP.NET Core Blazor WebAssembly ' Autor: guardrex Description: ' Naučte se ladit Blazor aplikace. '</span><span class="sxs-lookup"><span data-stu-id="e5a3c-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="e5a3c-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/31/2020 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/31/2020 no-loc:</span></span>
- <span data-ttu-id="e5a3c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5a3c-103">'Blazor'</span></span>
- <span data-ttu-id="e5a3c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5a3c-104">'Identity'</span></span>
- <span data-ttu-id="e5a3c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5a3c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5a3c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5a3c-106">'Razor'</span></span>
- <span data-ttu-id="e5a3c-107">SignalRUID: blazor/Debug</span><span class="sxs-lookup"><span data-stu-id="e5a3c-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="e5a3c-108">Ladění ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e5a3c-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="e5a3c-109">Daniel Skořepa</span><span class="sxs-lookup"><span data-stu-id="e5a3c-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="e5a3c-110">Aplikace WebAssembly se dají ladit pomocí nástrojů pro vývoj v prohlížeči v prohlížečích založených na chromu (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="e5a3c-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="e5a3c-111">Alternativně můžete aplikaci ladit pomocí sady Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="e5a3c-112">K dispozici jsou tyto scénáře:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-112">Available scenarios include:</span></span>

* <span data-ttu-id="e5a3c-113">Nastavení a odebrání zarážek.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="e5a3c-114">Spusťte aplikaci s podporou ladění v aplikaci Visual Studio a Visual Studio Code (podpora<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="e5a3c-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="e5a3c-115">Jednoduchý krok (<kbd>F10</kbd>) prostřednictvím kódu.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="e5a3c-116">Pokračuje v provádění kódu pomocí <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v aplikaci Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="e5a3c-117">V zobrazení *místních* hodnot Sledujte hodnoty místních proměnných.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="e5a3c-118">Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="e5a3c-119">Teď *nemůžete*:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-119">For now, you *can't*:</span></span>

* <span data-ttu-id="e5a3c-120">Přerušit při neošetřených výjimkách.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="e5a3c-121">Při spuštění aplikace se zarážky volání.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="e5a3c-122">V nadcházejících vydáních budeme dál zlepšovat možnosti ladění.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e5a3c-123">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e5a3c-123">Prerequisites</span></span>

<span data-ttu-id="e5a3c-124">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="e5a3c-125">Microsoft Edge (verze 80 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="e5a3c-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="e5a3c-126">Google Chrome (verze 70 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="e5a3c-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="e5a3c-127">Povolit ladění pro Visual Studio a Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5a3c-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="e5a3c-128">Chcete-li povolit ladění pro existující Blazor aplikaci WebAssembly, aktualizujte soubor *launchSettings. JSON* ve složce spouštěný projekt tak, aby do `inspectUri` každého spouštěcího profilu zahrnoval následující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="e5a3c-129">Po aktualizaci by soubor *launchSettings. JSON* měl vypadat podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="e5a3c-130">`inspectUri`Vlastnost:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="e5a3c-131">Umožňuje rozhraní IDE zjistit, že aplikace je Blazor aplikace WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="e5a3c-132">Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím Blazor ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="e5a3c-133">Zástupný text pro protokol WebSockets ( `wsProtocol` ), hostitele ( `url.hostname` ), port ( `url.port` ) a identifikátor URI pro inspektor v spuštěném prohlížeči ( `browserInspectUri` ) jsou poskytovány rozhraním.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="e5a3c-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5a3c-134">Visual Studio</span></span>

<span data-ttu-id="e5a3c-135">Ladění Blazor aplikace WebAssembly v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="e5a3c-136">Vytvoří novou ASP.NET Core hostovanou Blazor aplikaci WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="e5a3c-137">Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="e5a3c-138">V metodě nastavte zarážku v *čítači. Razor* `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="e5a3c-139">Přejděte na kartu **čítač** a vyberte tlačítko, kde se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="e5a3c-141">Podívejte se na hodnotu `currentCount` pole v okně místní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Zobrazit místní hodnoty](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="e5a3c-143">Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="e5a3c-144">Při ladění Blazor aplikace pro WebAssembly můžete také ladit kód serveru:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="e5a3c-145">Nastavte zarážku na stránce *FetchData. Razor* v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="e5a3c-146">Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="e5a3c-147">Přejděte na kartu **načíst data** , abyste narazili na první zarážku v `FetchData` součásti těsně předtím, než vydá požadavek HTTP na server:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Ladit načtení dat](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="e5a3c-149">Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a potom stiskněte zarážku na serveru v `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="e5a3c-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="e5a3c-151">Stiskněte znovu <kbd>F5</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na vykreslenou tabulku předpovědi počasí.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="e5a3c-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5a3c-152">Visual Studio Code</span></span>

<span data-ttu-id="e5a3c-153">Ladění Blazor aplikace WebAssembly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="e5a3c-154">Nainstalujte [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [ladicí program JavaScriptu (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true` .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Rozšíření](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS Preview ladicího programu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="e5a3c-157">Ladit samostatné Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e5a3c-157">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="e5a3c-158">Otevřete samostatnou Blazor aplikaci WebAssembly v vs Code.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-158">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="e5a3c-159">Pokud se zobrazí následující oznámení, že pro povolení ladění je potřeba další nastavení:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-159">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="e5a3c-160">Potvrďte, že máte nainstalované správné rozšíření.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-160">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="e5a3c-161">Potvrďte, že je povolené ladění v jazyce JavaScript verze Preview.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-161">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="e5a3c-162">Znovu načtěte okno.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-162">Reload the window.</span></span>

   ![Vyžaduje se další instalace.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="e5a3c-164">Spusťte ladění pomocí klávesových zkratek <kbd>F5</kbd> nebo položky nabídky.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-164">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="e5a3c-165">Po zobrazení výzvy vyberte možnost \*\* Blazor ladění WebAssembly\*\* a spusťte ladění.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-165">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Seznam dostupných možností ladění](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="e5a3c-167">Spustí se samostatná aplikace a otevře se prohlížeč ladění.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-167">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="e5a3c-168">Nastavte zarážku v `IncrementCount` metodě v `Counter` součásti a potom vyberte tlačítko, kterým se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Čítač ladění v VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="e5a3c-170">Ladit hostované Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e5a3c-170">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="e5a3c-171">Otevřete hostovanou Blazor aplikaci WebAssembly v vs Code.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-171">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="e5a3c-172">Pokud pro projekt neexistuje konfigurační sada pro spuštění, zobrazí se následující oznámení.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-172">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="e5a3c-173">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-173">Select **Yes**.</span></span>

   ![Přidat požadované prostředky](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="e5a3c-175">V okně Výběr vyberte *serverový* projekt v hostovaném řešení.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-175">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="e5a3c-176">Soubor *Launch. JSON* se vygeneruje s konfigurací spuštění pro spuštění ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-176">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="e5a3c-177">Připojit k existující relaci ladění</span><span class="sxs-lookup"><span data-stu-id="e5a3c-177">Attach to an existing debugging session</span></span>

<span data-ttu-id="e5a3c-178">Pokud se chcete připojit ke spuštěné Blazor aplikaci, vytvořte soubor *Launch. JSON* s následující konfigurací:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-178">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="e5a3c-179">Připojení k relaci ladění se podporuje jenom pro samostatné aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-179">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="e5a3c-180">Chcete-li použít ladění v plném zásobníku, musíte aplikaci spustit z VS Code.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-180">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="e5a3c-181">Spustit možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="e5a3c-181">Launch configuration options</span></span>

<span data-ttu-id="e5a3c-182">Pro typ ladění jsou podporovány následující možnosti konfigurace spuštění `blazorwasm` .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-182">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="e5a3c-183">Možnost</span><span class="sxs-lookup"><span data-stu-id="e5a3c-183">Option</span></span>    | <span data-ttu-id="e5a3c-184">Popis</span><span class="sxs-lookup"><span data-stu-id="e5a3c-184">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="e5a3c-185">Použijte `launch` ke spuštění a připojení relace ladění k Blazor aplikaci WebAssembly nebo `attach` k připojení relace ladění k již spuštěné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-185">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="e5a3c-186">Adresa URL, která se má otevřít v prohlížeči při ladění.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-186">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="e5a3c-187">Výchozí hodnota je `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-187">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="e5a3c-188">Prohlížeč, který se má spustit pro relaci ladění.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-188">The browser to launch for the debugging session.</span></span> <span data-ttu-id="e5a3c-189">Nastavte na `edge` nebo `chrome`.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-189">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="e5a3c-190">Výchozí hodnota je `chrome` .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-190">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="e5a3c-191">Slouží ke generování protokolů z ladicího programu JS.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-191">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="e5a3c-192">Nastavte na `true` Generovat protokoly.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-192">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="e5a3c-193">Musí být nastaven na hodnotu `true` při spuštění a ladění hostované Blazor aplikace WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-193">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="e5a3c-194">Určuje absolutní cestu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-194">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="e5a3c-195">By měla být nastavena, pokud je aplikace obsluhována z dílčího směrování.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-195">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="e5a3c-196">Počet milisekund, po které se má čekat na připojení relace ladění</span><span class="sxs-lookup"><span data-stu-id="e5a3c-196">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="e5a3c-197">Výchozí hodnota je 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="e5a3c-197">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="e5a3c-198">Odkaz na spustitelný soubor pro spuštění serveru hostované aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-198">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="e5a3c-199">Musí být nastaven `hosted` , pokud je `true` .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-199">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="e5a3c-200">Pracovní adresář, ve kterém se má spustit aplikace</span><span class="sxs-lookup"><span data-stu-id="e5a3c-200">The working directory to launch the app under.</span></span> <span data-ttu-id="e5a3c-201">Musí být nastaven `hosted` , pokud je `true` .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-201">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="e5a3c-202">Proměnné prostředí, které se mají poskytnout spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-202">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="e5a3c-203">Platí pouze v případě `hosted` , že je nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-203">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="e5a3c-204">Příklad konfigurací spuštění</span><span class="sxs-lookup"><span data-stu-id="e5a3c-204">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="e5a3c-205">Spuštění a ladění samostatné Blazor aplikace WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e5a3c-205">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="e5a3c-206">Připojit se k běžící aplikaci v zadané adrese URL</span><span class="sxs-lookup"><span data-stu-id="e5a3c-206">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="e5a3c-207">Spuštění a ladění hostované Blazor aplikace WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e5a3c-207">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="e5a3c-208">Ladit v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="e5a3c-208">Debug in the browser</span></span>

1. <span data-ttu-id="e5a3c-209">Spusťte ladicí sestavení aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-209">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="e5a3c-210">Stiskněte <kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-210">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="e5a3c-211">Prohlížeč musí být spuštěn s povoleným vzdáleným laděním.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-211">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="e5a3c-212">Pokud je vzdálené ladění zakázané, vygeneruje se chybová stránka karty prohlížeče, která se **nedá najít** .</span><span class="sxs-lookup"><span data-stu-id="e5a3c-212">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="e5a3c-213">Chybová stránka obsahuje pokyny pro spuštění prohlížeče s otevřeným portem pro ladění, aby se Blazor ladicí proxy server mohl připojit k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-213">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="e5a3c-214">*Zavřete všechny instance prohlížeče* a restartujte prohlížeč podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-214">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="e5a3c-215">Po spuštění prohlížeče se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-215">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="e5a3c-216">Rozbalte jednotlivá sestavení a vyhledejte zdrojové soubory *. cs* / *. Razor* k dispozici pro ladění.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-216">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="e5a3c-217">Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-217">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="e5a3c-218">Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-218">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="e5a3c-219">poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-219"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="e5a3c-220">Když se stiskne klávesová zkratka ladění, navede Blazor devtools Chrome na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-220">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="e5a3c-221">Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="e5a3c-221">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="e5a3c-222">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="e5a3c-222">Browser source maps</span></span>

<span data-ttu-id="e5a3c-223">Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-223">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="e5a3c-224">V Blazor současné době ale nemapuje C# přímo na JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-224">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="e5a3c-225">Místo toho Blazor provede výklad il v prohlížeči, takže zdrojové mapy nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-225">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e5a3c-226">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="e5a3c-226">Troubleshoot</span></span>

<span data-ttu-id="e5a3c-227">Pokud dochází k chybám, může vám pomáhat následující tipy:</span><span class="sxs-lookup"><span data-stu-id="e5a3c-227">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="e5a3c-228">Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-228">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="e5a3c-229">V konzole nástroje spusťte příkaz `localStorage.clear()` k odebrání všech zarážek.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-229">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="e5a3c-230">Potvrďte, že jste nainstalovali a důvěřujete ASP.NET Core certifikát pro vývoj HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-230">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="e5a3c-231">Další informace naleznete v tématu <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="e5a3c-231">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
