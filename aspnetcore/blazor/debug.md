---
title: ASP.NET Core laděníBlazor WebAssembly
author: guardrex
description: Naučte se ladit Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 14943b9f7847ac9144addfdf16a003f6fc8c340c
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159700"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="7f848-103">ASP.NET Core laděníBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7f848-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="7f848-104">Daniel Skořepa</span><span class="sxs-lookup"><span data-stu-id="7f848-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="7f848-105">Blazor WebAssemblyaplikace se dají ladit pomocí vývojářských nástrojů pro vývoj v prohlížečích založených na chromu (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="7f848-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="7f848-106">Alternativně můžete aplikaci ladit pomocí sady Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7f848-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="7f848-107">K dispozici jsou tyto scénáře:</span><span class="sxs-lookup"><span data-stu-id="7f848-107">Available scenarios include:</span></span>

* <span data-ttu-id="7f848-108">Nastavení a odebrání zarážek.</span><span class="sxs-lookup"><span data-stu-id="7f848-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="7f848-109">Spusťte aplikaci s podporou ladění v aplikaci Visual Studio a Visual Studio Code (podpora<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="7f848-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="7f848-110">Jednoduchý krok (<kbd>F10</kbd>) prostřednictvím kódu.</span><span class="sxs-lookup"><span data-stu-id="7f848-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="7f848-111">Pokračuje v provádění kódu pomocí <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v aplikaci Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7f848-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="7f848-112">V zobrazení *místních* hodnot Sledujte hodnoty místních proměnných.</span><span class="sxs-lookup"><span data-stu-id="7f848-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="7f848-113">Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="7f848-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="7f848-114">Teď *nemůžete*:</span><span class="sxs-lookup"><span data-stu-id="7f848-114">For now, you *can't*:</span></span>

* <span data-ttu-id="7f848-115">Přerušit při neošetřených výjimkách.</span><span class="sxs-lookup"><span data-stu-id="7f848-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="7f848-116">Při spuštění aplikace se zarážky volání.</span><span class="sxs-lookup"><span data-stu-id="7f848-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="7f848-117">V nadcházejících vydáních budeme dál zlepšovat možnosti ladění.</span><span class="sxs-lookup"><span data-stu-id="7f848-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7f848-118">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="7f848-118">Prerequisites</span></span>

<span data-ttu-id="7f848-119">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="7f848-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="7f848-120">Google Chrome (verze 70 nebo novější) (výchozí)</span><span class="sxs-lookup"><span data-stu-id="7f848-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="7f848-121">Microsoft Edge (verze 80 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="7f848-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="7f848-122">Povolit ladění pro Visual Studio a Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f848-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="7f848-123">Chcete-li povolit ladění pro existující Blazor WebAssembly aplikaci, aktualizujte `launchSettings.json` soubor v spouštěném projektu tak, aby do `inspectUri` každého spouštěcího profilu zahrnoval následující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="7f848-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="7f848-124">Po aktualizaci `launchSettings.json` by měl soubor vypadat podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="7f848-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="7f848-125">`inspectUri`Vlastnost:</span><span class="sxs-lookup"><span data-stu-id="7f848-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="7f848-126">Umožňuje rozhraní IDE zjistit, že aplikace je Blazor WebAssembly aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f848-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="7f848-127">Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím Blazor ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="7f848-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="7f848-128">Zástupný text pro protokol WebSockets ( `wsProtocol` ), hostitele ( `url.hostname` ), port ( `url.port` ) a identifikátor URI pro inspektor v spuštěném prohlížeči ( `browserInspectUri` ) jsou poskytovány rozhraním.</span><span class="sxs-lookup"><span data-stu-id="7f848-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="7f848-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f848-129">Visual Studio</span></span>

<span data-ttu-id="7f848-130">Ladění Blazor WebAssembly aplikace v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="7f848-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="7f848-131">Vytvořte novou ASP.NET Core hostovanou Blazor WebAssembly aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7f848-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7f848-132">Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="7f848-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="7f848-133">Nastavte zarážku v `Pages/Counter.razor` `IncrementCount` metodě.</span><span class="sxs-lookup"><span data-stu-id="7f848-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="7f848-134">Přejděte na **`Counter`** kartu a vyberte tlačítko, kde se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="7f848-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="7f848-136">Podívejte se na hodnotu `currentCount` pole v okně místní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="7f848-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Zobrazit místní hodnoty](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="7f848-138">Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="7f848-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="7f848-139">Při ladění Blazor WebAssembly aplikace můžete také ladit kód serveru:</span><span class="sxs-lookup"><span data-stu-id="7f848-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="7f848-140">Nastavte zarážku na `Pages/FetchData.razor` stránce v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7f848-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="7f848-141">Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.</span><span class="sxs-lookup"><span data-stu-id="7f848-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="7f848-142">Přejděte na **`Fetch Data`** kartu a vyberte první zarážku v `FetchData` součásti těsně předtím, než VYDÁ požadavek HTTP na server:</span><span class="sxs-lookup"><span data-stu-id="7f848-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Ladit načtení dat](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="7f848-144">Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a potom stiskněte zarážku na serveru v `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="7f848-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="7f848-146">Stiskněte znovu <kbd>F5</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na vykreslenou tabulku předpovědi počasí.</span><span class="sxs-lookup"><span data-stu-id="7f848-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="7f848-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f848-147">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="7f848-148">Samostatně laditBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7f848-148">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="7f848-149">Otevřete samostatnou Blazor WebAssembly aplikaci v vs Code.</span><span class="sxs-lookup"><span data-stu-id="7f848-149">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="7f848-150">Pokud se zobrazí následující oznámení, že pro povolení ladění je potřeba další nastavení:</span><span class="sxs-lookup"><span data-stu-id="7f848-150">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="7f848-151">Potvrďte, že máte nainstalované správné rozšíření.</span><span class="sxs-lookup"><span data-stu-id="7f848-151">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="7f848-152">Potvrďte, že je povolené ladění v jazyce JavaScript verze Preview.</span><span class="sxs-lookup"><span data-stu-id="7f848-152">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="7f848-153">Znovu načtěte okno.</span><span class="sxs-lookup"><span data-stu-id="7f848-153">Reload the window.</span></span>

   ![Vyžaduje se další instalace.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="7f848-155">Spusťte ladění pomocí klávesových zkratek <kbd>F5</kbd> nebo položky nabídky.</span><span class="sxs-lookup"><span data-stu-id="7f848-155">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="7f848-156">Po zobrazení výzvy vyberte možnost \*\* Blazor WebAssembly ladění\*\* pro spuštění ladění.</span><span class="sxs-lookup"><span data-stu-id="7f848-156">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Seznam dostupných možností ladění](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="7f848-158">Spustí se samostatná aplikace a otevře se prohlížeč ladění.</span><span class="sxs-lookup"><span data-stu-id="7f848-158">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="7f848-159">Nastavte zarážku v `IncrementCount` metodě v `Counter` součásti a potom vyberte tlačítko, kterým se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="7f848-159">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Čítač ladění v VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="7f848-161">Ladění je hostovanéBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7f848-161">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="7f848-162">Otevřete Blazor WebAssembly složku řešení hostované aplikace v vs Code.</span><span class="sxs-lookup"><span data-stu-id="7f848-162">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="7f848-163">Pokud pro projekt neexistuje konfigurační sada pro spuštění, zobrazí se následující oznámení.</span><span class="sxs-lookup"><span data-stu-id="7f848-163">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="7f848-164">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="7f848-164">Select **Yes**.</span></span>

   ![Přidat požadované prostředky](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="7f848-166">V paletě příkazů v horní části okna vyberte *serverový* projekt v hostovaném řešení.</span><span class="sxs-lookup"><span data-stu-id="7f848-166">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="7f848-167">`launch.json`Soubor se vygeneruje s konfigurací spuštění pro spuštění ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="7f848-167">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="7f848-168">Připojit k existující relaci ladění</span><span class="sxs-lookup"><span data-stu-id="7f848-168">Attach to an existing debugging session</span></span>

<span data-ttu-id="7f848-169">Pokud se chcete připojit ke spuštěné Blazor aplikaci, vytvořte `launch.json` soubor s následující konfigurací:</span><span class="sxs-lookup"><span data-stu-id="7f848-169">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="7f848-170">Připojení k relaci ladění se podporuje jenom pro samostatné aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f848-170">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="7f848-171">Chcete-li použít ladění v plném zásobníku, musíte aplikaci spustit z VS Code.</span><span class="sxs-lookup"><span data-stu-id="7f848-171">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="7f848-172">Spustit možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="7f848-172">Launch configuration options</span></span>

<span data-ttu-id="7f848-173">Pro `blazorwasm` typ ladění () jsou podporovány následující možnosti konfigurace spuštění `.vscode/launch.json` .</span><span class="sxs-lookup"><span data-stu-id="7f848-173">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="7f848-174">Možnost</span><span class="sxs-lookup"><span data-stu-id="7f848-174">Option</span></span>    | <span data-ttu-id="7f848-175">Popis</span><span class="sxs-lookup"><span data-stu-id="7f848-175">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="7f848-176">Pomocí `launch` můžete spustit a připojit relaci ladění k Blazor WebAssembly aplikaci nebo `attach` připojit relaci ladění k již spuštěné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7f848-176">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="7f848-177">Adresa URL, která se má otevřít v prohlížeči při ladění.</span><span class="sxs-lookup"><span data-stu-id="7f848-177">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="7f848-178">Výchozí hodnota je `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="7f848-178">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="7f848-179">Prohlížeč, který se má spustit pro relaci ladění.</span><span class="sxs-lookup"><span data-stu-id="7f848-179">The browser to launch for the debugging session.</span></span> <span data-ttu-id="7f848-180">Nastavte na `edge` nebo `chrome`.</span><span class="sxs-lookup"><span data-stu-id="7f848-180">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="7f848-181">Výchozí hodnota je `chrome` .</span><span class="sxs-lookup"><span data-stu-id="7f848-181">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="7f848-182">Slouží ke generování protokolů z ladicího programu JS.</span><span class="sxs-lookup"><span data-stu-id="7f848-182">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="7f848-183">Nastavte na `true` Generovat protokoly.</span><span class="sxs-lookup"><span data-stu-id="7f848-183">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="7f848-184">Musí být nastavené na hodnotu `true` při spuštění a ladění hostované Blazor WebAssembly aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f848-184">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="7f848-185">Určuje absolutní cestu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="7f848-185">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="7f848-186">By měla být nastavena, pokud je aplikace obsluhována z dílčího směrování.</span><span class="sxs-lookup"><span data-stu-id="7f848-186">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="7f848-187">Počet milisekund, po které se má čekat na připojení relace ladění</span><span class="sxs-lookup"><span data-stu-id="7f848-187">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="7f848-188">Výchozí hodnota je 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="7f848-188">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="7f848-189">Odkaz na spustitelný soubor pro spuštění serveru hostované aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f848-189">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="7f848-190">Musí být nastaven `hosted` , pokud je `true` .</span><span class="sxs-lookup"><span data-stu-id="7f848-190">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="7f848-191">Pracovní adresář, ve kterém se má spustit aplikace</span><span class="sxs-lookup"><span data-stu-id="7f848-191">The working directory to launch the app under.</span></span> <span data-ttu-id="7f848-192">Musí být nastaven `hosted` , pokud je `true` .</span><span class="sxs-lookup"><span data-stu-id="7f848-192">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="7f848-193">Proměnné prostředí, které se mají poskytnout spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="7f848-193">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="7f848-194">Platí pouze v případě `hosted` , že je nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="7f848-194">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="7f848-195">Příklad konfigurací spuštění</span><span class="sxs-lookup"><span data-stu-id="7f848-195">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="7f848-196">Spuštění a ladění samostatné Blazor WebAssembly aplikace</span><span class="sxs-lookup"><span data-stu-id="7f848-196">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="7f848-197">Připojit se k běžící aplikaci v zadané adrese URL</span><span class="sxs-lookup"><span data-stu-id="7f848-197">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="7f848-198">Spuštění a ladění hostované Blazor WebAssembly aplikace pomocí Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="7f848-198">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="7f848-199">Konfigurace prohlížeče je ve výchozím nastavení Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="7f848-199">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="7f848-200">Při použití Microsoft Edge pro ladění nastavte `browser` na `edge` .</span><span class="sxs-lookup"><span data-stu-id="7f848-200">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="7f848-201">Pokud chcete používat Google Chrome, buď nenastavujte `browser` možnost, nebo nastavte hodnotu možnosti na `chrome` .</span><span class="sxs-lookup"><span data-stu-id="7f848-201">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="7f848-202">V předchozím příkladu `MyHostedApp.Server.dll` je sestavení *serverové* aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f848-202">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="7f848-203">`.vscode`Složka se nachází ve složce řešení vedle `Client` `Server` složek, a `Shared` .</span><span class="sxs-lookup"><span data-stu-id="7f848-203">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="7f848-204">Ladit v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="7f848-204">Debug in the browser</span></span>

1. <span data-ttu-id="7f848-205">Spusťte ladicí sestavení aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="7f848-205">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="7f848-206">Spusťte prohlížeč a přejděte na adresu URL aplikace (například `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="7f848-206">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="7f848-207">V prohlížeči se pokuste zahájit vzdálené ladění stisknutím klávesy <kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="7f848-207">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="7f848-208">Prohlížeč musí běžet se zapnutým vzdáleným laděním, což není výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="7f848-208">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="7f848-209">Pokud je vzdálené ladění zakázané, zobrazí se stránka s informací o tom, že se pro spuštění prohlížeče s otevřeným portem ladění **nedaří najít** chybovou stránku karty prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7f848-209">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="7f848-210">Postupujte podle pokynů pro prohlížeč a otevřete nové okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7f848-210">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="7f848-211">Zavřete předchozí okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7f848-211">Close the previous browser window.</span></span>

1. <span data-ttu-id="7f848-212">Po spuštění prohlížeče se zapnutým vzdáleným laděním se otevře klávesová zkratka (<kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) s novou kartou ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="7f848-212">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="7f848-213">Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET aplikace v rámci `file://` uzlu.</span><span class="sxs-lookup"><span data-stu-id="7f848-213">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="7f848-214">V kódu komponenty ( `.razor` soubory) a souborech kódu jazyka C# ( `.cs` ) jsou zarážky, které jste nastavili, zasaženy při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="7f848-214">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="7f848-215">Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.</span><span class="sxs-lookup"><span data-stu-id="7f848-215">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="7f848-216">Blazorposkytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť.</span><span class="sxs-lookup"><span data-stu-id="7f848-216">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="7f848-217">Když se stiskne klávesová zkratka ladění, navede Blazor devtools Chrome na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="7f848-217">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="7f848-218">Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="7f848-218">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="7f848-219">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="7f848-219">Browser source maps</span></span>

<span data-ttu-id="7f848-220">Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="7f848-220">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="7f848-221">V Blazor současné době ale nemapuje C# přímo na JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="7f848-221">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="7f848-222">Místo toho Blazor provede výklad il v prohlížeči, takže zdrojové mapy nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="7f848-222">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7f848-223">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="7f848-223">Troubleshoot</span></span>

<span data-ttu-id="7f848-224">Pokud dochází k chybám, může vám pomáhat následující tipy:</span><span class="sxs-lookup"><span data-stu-id="7f848-224">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="7f848-225">Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7f848-225">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="7f848-226">V konzole nástroje spusťte příkaz `localStorage.clear()` k odebrání všech zarážek.</span><span class="sxs-lookup"><span data-stu-id="7f848-226">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="7f848-227">Potvrďte, že jste nainstalovali a důvěřujete ASP.NET Core certifikát pro vývoj HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7f848-227">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="7f848-228">Další informace naleznete v tématu <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="7f848-228">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="7f848-229">Visual Studio vyžaduje možnost **Povolit ladění JavaScriptu pro ASP.NET (Chrome, Edge a IE)** v možnostech **nástrojů**  >  **Options**  >  **Debugging**  >  **Obecné**ladění.</span><span class="sxs-lookup"><span data-stu-id="7f848-229">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="7f848-230">Toto je výchozí nastavení pro Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7f848-230">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="7f848-231">Pokud ladění nefunguje, potvrďte, že je vybraná možnost.</span><span class="sxs-lookup"><span data-stu-id="7f848-231">If debugging isn't working, confirm that the option is selected.</span></span>
