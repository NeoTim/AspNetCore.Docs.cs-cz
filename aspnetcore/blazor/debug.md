---
title: ASP.NET Core ladění Blazor WebAssembly
author: guardrex
description: Naučte se ladit Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 7b5dac5c634ae3eef180ef2c7c7287da94342169
ms.sourcegitcommit: 4df445e7d49a99f81625430f728c28e5d6bf2107
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2020
ms.locfileid: "88253574"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="9bc7c-103">ASP.NET Core ladění Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9bc7c-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="9bc7c-104">Daniel Skořepa</span><span class="sxs-lookup"><span data-stu-id="9bc7c-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="9bc7c-105">Blazor WebAssembly aplikace se dají ladit pomocí vývojářských nástrojů pro vývoj v prohlížečích založených na chromu (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="9bc7c-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="9bc7c-106">Alternativně můžete aplikaci ladit pomocí sady Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="9bc7c-107">K dispozici jsou tyto scénáře:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-107">Available scenarios include:</span></span>

* <span data-ttu-id="9bc7c-108">Nastavení a odebrání zarážek.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="9bc7c-109">Spusťte aplikaci s podporou ladění v aplikaci Visual Studio a Visual Studio Code (podpora<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="9bc7c-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="9bc7c-110">Jednoduchý krok (<kbd>F10</kbd>) prostřednictvím kódu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="9bc7c-111">Pokračuje v provádění kódu pomocí <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v aplikaci Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="9bc7c-112">V zobrazení *místních* hodnot Sledujte hodnoty místních proměnných.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="9bc7c-113">Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="9bc7c-114">Teď *nemůžete*:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-114">For now, you *can't*:</span></span>

* <span data-ttu-id="9bc7c-115">Přerušit při neošetřených výjimkách.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="9bc7c-116">Při spuštění aplikace se zarážky volání.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="9bc7c-117">V nadcházejících vydáních budeme dál zlepšovat možnosti ladění.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9bc7c-118">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9bc7c-118">Prerequisites</span></span>

<span data-ttu-id="9bc7c-119">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="9bc7c-120">Google Chrome (verze 70 nebo novější) (výchozí)</span><span class="sxs-lookup"><span data-stu-id="9bc7c-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="9bc7c-121">Microsoft Edge (verze 80 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="9bc7c-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="9bc7c-122">Povolit ladění pro Visual Studio a Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bc7c-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="9bc7c-123">Chcete-li povolit ladění pro existující Blazor WebAssembly aplikaci, aktualizujte `launchSettings.json` soubor v spouštěném projektu tak, aby do `inspectUri` každého spouštěcího profilu zahrnoval následující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="9bc7c-124">Po aktualizaci `launchSettings.json` by měl soubor vypadat podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="9bc7c-125">`inspectUri`Vlastnost:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="9bc7c-126">Umožňuje rozhraní IDE zjistit, že aplikace je Blazor WebAssembly aplikace.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="9bc7c-127">Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím Blazor ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="9bc7c-128">Zástupný text pro protokol WebSockets ( `wsProtocol` ), hostitele ( `url.hostname` ), port ( `url.port` ) a identifikátor URI pro inspektor v spuštěném prohlížeči ( `browserInspectUri` ) jsou poskytovány rozhraním.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="9bc7c-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bc7c-129">Visual Studio</span></span>

<span data-ttu-id="9bc7c-130">Ladění Blazor WebAssembly aplikace v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="9bc7c-131">Vytvořte novou ASP.NET Core hostovanou Blazor WebAssembly aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="9bc7c-132">Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9bc7c-133">**Spuštění bez ladění** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) není podporováno.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-133">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="9bc7c-134">Když je aplikace spuštěna v konfiguraci ladění, režie ladění vždy vede k omezení malého výkonu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-134">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="9bc7c-135">Nastavte zarážku v `Pages/Counter.razor` `IncrementCount` metodě.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-135">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="9bc7c-136">Přejděte na **`Counter`** kartu a vyberte tlačítko, kde se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-136">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="9bc7c-138">Podívejte se na hodnotu `currentCount` pole v okně místní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-138">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Zobrazit místní hodnoty](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="9bc7c-140">Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-140">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="9bc7c-141">Při ladění Blazor WebAssembly aplikace můžete také ladit kód serveru:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-141">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="9bc7c-142">Nastavte zarážku na `Pages/FetchData.razor` stránce v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-142">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="9bc7c-143">Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-143">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="9bc7c-144">Přejděte na **`Fetch Data`** kartu a vyberte první zarážku v `FetchData` součásti těsně předtím, než VYDÁ požadavek HTTP na server:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-144">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Ladit načtení dat](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="9bc7c-146">Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a potom stiskněte zarážku na serveru v `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="9bc7c-146">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="9bc7c-148">Stiskněte znovu <kbd>F5</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na vykreslenou tabulku předpovědi počasí.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-148">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="9bc7c-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bc7c-149">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="9bc7c-150">Samostatně ladit Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9bc7c-150">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="9bc7c-151">Otevřete samostatnou Blazor WebAssembly aplikaci v vs Code.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-151">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="9bc7c-152">Může se zobrazit následující oznámení, že pro povolení ladění je potřeba další nastavení:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-152">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Vyžaduje se další instalace.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="9bc7c-154">Pokud obdržíte oznámení:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-154">If you receive the notification:</span></span>

   * <span data-ttu-id="9bc7c-155">Potvrďte, že je nainstalovaná nejnovější [rozšíření C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-155">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="9bc7c-156">Pokud chcete zkontrolovat nainstalovaná rozšíření, **View**otevřete  >  **rozšíření** zobrazení z řádku nabídek nebo vyberte ikonu **rozšíření** na postranním panelu **aktivita** .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-156">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="9bc7c-157">Potvrďte, že je povolené ladění v jazyce JavaScript verze Preview.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-157">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="9bc7c-158">Otevřete nastavení z řádku nabídek (**File**  >  **Preferences**  >  **Nastavení**předvoleb souborů).</span><span class="sxs-lookup"><span data-stu-id="9bc7c-158">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="9bc7c-159">Hledání pomocí klíčových slov `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-159">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="9bc7c-160">Ve výsledcích hledání potvrďte, že je zaškrtnuto políčko pro **ladění > JavaScript: použít verzi Preview** .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-160">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="9bc7c-161">Pokud není k dispozici možnost Povolit ladění ve verzi Preview, upgradujte na nejnovější verzi VS Code nebo nainstalujte [rozšíření ladicího programu jazyka JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code verze 1,46 nebo starší).</span><span class="sxs-lookup"><span data-stu-id="9bc7c-161">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="9bc7c-162">Znovu načtěte okno.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-162">Reload the window.</span></span>

1. <span data-ttu-id="9bc7c-163">Spusťte ladění pomocí klávesových zkratek <kbd>F5</kbd> nebo položky nabídky.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-163">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9bc7c-164">**Spuštění bez ladění** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) není podporováno.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-164">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="9bc7c-165">Když je aplikace spuštěna v konfiguraci ladění, režie ladění vždy vede k omezení malého výkonu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-165">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="9bc7c-166">Po zobrazení výzvy vyberte možnost \*\* Blazor WebAssembly ladění\*\* pro spuštění ladění.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-166">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Seznam dostupných možností ladění](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="9bc7c-168">Spustí se samostatná aplikace a otevře se prohlížeč ladění.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-168">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="9bc7c-169">Nastavte zarážku v `IncrementCount` metodě v `Counter` součásti a potom vyberte tlačítko, kterým se má zarážka opakovat:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-169">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Čítač ladění v VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="9bc7c-171">Ladění je hostované Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9bc7c-171">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="9bc7c-172">Otevřete Blazor WebAssembly složku řešení hostované aplikace v vs Code.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-172">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="9bc7c-173">Pokud pro projekt neexistuje konfigurační sada pro spuštění, zobrazí se následující oznámení.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-173">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="9bc7c-174">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-174">Select **Yes**.</span></span>

   ![Přidat požadované prostředky](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="9bc7c-176">V paletě příkazů v horní části okna vyberte *serverový* projekt v hostovaném řešení.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-176">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="9bc7c-177">`launch.json`Soubor se vygeneruje s konfigurací spuštění pro spuštění ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-177">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="9bc7c-178">Připojit k existující relaci ladění</span><span class="sxs-lookup"><span data-stu-id="9bc7c-178">Attach to an existing debugging session</span></span>

<span data-ttu-id="9bc7c-179">Pokud se chcete připojit ke spuštěné Blazor aplikaci, vytvořte `launch.json` soubor s následující konfigurací:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-179">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="9bc7c-180">Připojení k relaci ladění se podporuje jenom pro samostatné aplikace.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-180">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="9bc7c-181">Chcete-li použít ladění v plném zásobníku, musíte aplikaci spustit z VS Code.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-181">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="9bc7c-182">Spustit možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="9bc7c-182">Launch configuration options</span></span>

<span data-ttu-id="9bc7c-183">Pro `blazorwasm` typ ladění () jsou podporovány následující možnosti konfigurace spuštění `.vscode/launch.json` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-183">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="9bc7c-184">Možnost</span><span class="sxs-lookup"><span data-stu-id="9bc7c-184">Option</span></span>    | <span data-ttu-id="9bc7c-185">Popis</span><span class="sxs-lookup"><span data-stu-id="9bc7c-185">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="9bc7c-186">Pomocí `launch` můžete spustit a připojit relaci ladění k Blazor WebAssembly aplikaci nebo `attach` připojit relaci ladění k již spuštěné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-186">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="9bc7c-187">Adresa URL, která se má otevřít v prohlížeči při ladění.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-187">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="9bc7c-188">Výchozí hodnota je `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-188">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="9bc7c-189">Prohlížeč, který se má spustit pro relaci ladění.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-189">The browser to launch for the debugging session.</span></span> <span data-ttu-id="9bc7c-190">Nastavte na `edge` nebo `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-190">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="9bc7c-191">Výchozí hodnota je `chrome` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-191">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="9bc7c-192">Slouží ke generování protokolů z ladicího programu JS.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-192">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="9bc7c-193">Nastavte na `true` Generovat protokoly.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-193">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="9bc7c-194">Musí být nastavené na hodnotu `true` při spuštění a ladění hostované Blazor WebAssembly aplikace.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-194">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="9bc7c-195">Určuje absolutní cestu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-195">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="9bc7c-196">By měla být nastavena, pokud je aplikace obsluhována z dílčího směrování.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-196">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="9bc7c-197">Počet milisekund, po které se má čekat na připojení relace ladění</span><span class="sxs-lookup"><span data-stu-id="9bc7c-197">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="9bc7c-198">Výchozí hodnota je 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="9bc7c-198">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="9bc7c-199">Odkaz na spustitelný soubor pro spuštění serveru hostované aplikace.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-199">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="9bc7c-200">Musí být nastaven `hosted` , pokud je `true` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-200">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="9bc7c-201">Pracovní adresář, ve kterém se má spustit aplikace</span><span class="sxs-lookup"><span data-stu-id="9bc7c-201">The working directory to launch the app under.</span></span> <span data-ttu-id="9bc7c-202">Musí být nastaven `hosted` , pokud je `true` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-202">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="9bc7c-203">Proměnné prostředí, které se mají poskytnout spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-203">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="9bc7c-204">Platí pouze v případě `hosted` , že je nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-204">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="9bc7c-205">Příklad konfigurací spuštění</span><span class="sxs-lookup"><span data-stu-id="9bc7c-205">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="9bc7c-206">Spuštění a ladění samostatné Blazor WebAssembly aplikace</span><span class="sxs-lookup"><span data-stu-id="9bc7c-206">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="9bc7c-207">Připojit se k běžící aplikaci v zadané adrese URL</span><span class="sxs-lookup"><span data-stu-id="9bc7c-207">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="9bc7c-208">Spuštění a ladění hostované Blazor WebAssembly aplikace pomocí Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="9bc7c-208">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="9bc7c-209">Konfigurace prohlížeče je ve výchozím nastavení Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-209">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="9bc7c-210">Při použití Microsoft Edge pro ladění nastavte `browser` na `edge` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-210">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="9bc7c-211">Pokud chcete používat Google Chrome, buď nenastavujte `browser` možnost, nebo nastavte hodnotu možnosti na `chrome` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-211">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="9bc7c-212">V předchozím příkladu `MyHostedApp.Server.dll` je sestavení *serverové* aplikace.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-212">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="9bc7c-213">`.vscode`Složka se nachází ve složce řešení vedle `Client` `Server` složek, a `Shared` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-213">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="9bc7c-214">Ladit v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="9bc7c-214">Debug in the browser</span></span>

1. <span data-ttu-id="9bc7c-215">Spusťte ladicí sestavení aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-215">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="9bc7c-216">Spusťte prohlížeč a přejděte na adresu URL aplikace (například `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="9bc7c-216">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="9bc7c-217">V prohlížeči se pokuste zahájit vzdálené ladění stisknutím klávesy <kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-217">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="9bc7c-218">Prohlížeč musí běžet se zapnutým vzdáleným laděním, což není výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-218">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="9bc7c-219">Pokud je vzdálené ladění zakázané, zobrazí se stránka s informací o tom, že se pro spuštění prohlížeče s otevřeným portem ladění **nedaří najít** chybovou stránku karty prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-219">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="9bc7c-220">Postupujte podle pokynů pro prohlížeč a otevřete nové okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-220">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="9bc7c-221">Zavřete předchozí okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-221">Close the previous browser window.</span></span>

1. <span data-ttu-id="9bc7c-222">Po spuštění prohlížeče se zapnutým vzdáleným laděním se otevře klávesová zkratka (<kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) s novou kartou ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-222">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="9bc7c-223">Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET aplikace v rámci `file://` uzlu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-223">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="9bc7c-224">V kódu komponenty ( `.razor` soubory) a souborech kódu jazyka C# ( `.cs` ) jsou zarážky, které jste nastavili, zasaženy při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-224">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="9bc7c-225">Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-225">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="9bc7c-226">Blazor poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-226">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="9bc7c-227">Když se stiskne klávesová zkratka ladění, navede Blazor devtools Chrome na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-227">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="9bc7c-228">Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="9bc7c-228">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="9bc7c-229">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="9bc7c-229">Browser source maps</span></span>

<span data-ttu-id="9bc7c-230">Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-230">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="9bc7c-231">V Blazor současné době ale nemapuje C# přímo na JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-231">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="9bc7c-232">Místo toho Blazor provede výklad il v prohlížeči, takže zdrojové mapy nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-232">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9bc7c-233">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9bc7c-233">Troubleshoot</span></span>

<span data-ttu-id="9bc7c-234">Pokud dochází k chybám, může vám pomáhat následující tipy:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-234">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="9bc7c-235">Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-235">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="9bc7c-236">V konzole nástroje spusťte příkaz `localStorage.clear()` k odebrání všech zarážek.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-236">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="9bc7c-237">Potvrďte, že jste nainstalovali a důvěřujete ASP.NET Core certifikát pro vývoj HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-237">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="9bc7c-238">Další informace naleznete v tématu <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-238">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="9bc7c-239">Visual Studio vyžaduje možnost **Povolit ladění JavaScriptu pro ASP.NET (Chrome, Edge a IE)** v možnostech **nástrojů**  >  **Options**  >  **Debugging**  >  **Obecné**ladění.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-239">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="9bc7c-240">Toto je výchozí nastavení pro Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-240">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="9bc7c-241">Pokud ladění nefunguje, potvrďte, že je vybraná možnost.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-241">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="9bc7c-242">Zarážky `OnInitialized{Async}` nejsou v neúspěšných.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-242">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="9bc7c-243">BlazorSpuštění ladicího proxy serveru pro rozhraní trvá krátkou dobu, takže zarážky v [ `OnInitialized{Async}` metodě životního cyklu](xref:blazor/components/lifecycle#component-initialization-methods) nemusí být k dispozice.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-243">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="9bc7c-244">Doporučujeme přidat zpoždění na začátek těla metody a umožnit tak spuštění ladicího proxy serveru nějakou dobu, než se zarážka zasáhne.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-244">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="9bc7c-245">Můžete zahrnout zpoždění na základě [ `if` direktivy kompilátoru](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) , aby se zajistilo, že zpoždění není k dispozici pro sestavení pro vydání aplikace.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-245">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="9bc7c-246"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-246"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="9bc7c-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-timeout"></a><span data-ttu-id="9bc7c-248">Časový limit sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bc7c-248">Visual Studio timeout</span></span>

<span data-ttu-id="9bc7c-249">Pokud Visual Studio vyvolá výjimku, že se ladicímu adaptéru nepovedlo spustit zmínku o tom, že byl dosažen časový limit, můžete nastavit časový limit pomocí nastavení registru:</span><span class="sxs-lookup"><span data-stu-id="9bc7c-249">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="9bc7c-250">`{TIMEOUT}`Zástupný symbol v předchozím příkazu je v milisekundách.</span><span class="sxs-lookup"><span data-stu-id="9bc7c-250">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="9bc7c-251">Například jedna minuta je přiřazena jako `60000` .</span><span class="sxs-lookup"><span data-stu-id="9bc7c-251">For example, one minute is assigned as `60000`.</span></span>
