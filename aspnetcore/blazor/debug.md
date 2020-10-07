---
title: ASP.NET Core ladění Blazor WebAssembly
author: guardrex
description: Naučte se ladit Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: d4fd0d501ff14e37bb55b78bb6493ad43f9e5a87
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805567"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="1b32a-103">ASP.NET Core ladění Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1b32a-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="1b32a-104">Daniel Skořepa</span><span class="sxs-lookup"><span data-stu-id="1b32a-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="1b32a-105">Blazor WebAssembly aplikace se dají ladit pomocí vývojářských nástrojů pro vývoj v prohlížečích založených na chromu (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="1b32a-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="1b32a-106">Aplikaci můžete také ladit pomocí následujících integrovaných vývojových prostředí (IDEs):</span><span class="sxs-lookup"><span data-stu-id="1b32a-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="1b32a-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b32a-107">Visual Studio</span></span>
* <span data-ttu-id="1b32a-108">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b32a-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="1b32a-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b32a-109">Visual Studio Code</span></span>

<span data-ttu-id="1b32a-110">K dispozici jsou tyto scénáře:</span><span class="sxs-lookup"><span data-stu-id="1b32a-110">Available scenarios include:</span></span>

* <span data-ttu-id="1b32a-111">Nastavení a odebrání zarážek.</span><span class="sxs-lookup"><span data-stu-id="1b32a-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="1b32a-112">Spusťte aplikaci s podporou ladění v prostředích IDEs.</span><span class="sxs-lookup"><span data-stu-id="1b32a-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="1b32a-113">Jednoduchý krok kódu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-113">Single-step through the code.</span></span>
* <span data-ttu-id="1b32a-114">Pokračuje v provádění kódu pomocí klávesové zkratky v prostředích IDEs.</span><span class="sxs-lookup"><span data-stu-id="1b32a-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="1b32a-115">V okně *místní* hodnoty Sledujte hodnoty místních proměnných.</span><span class="sxs-lookup"><span data-stu-id="1b32a-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="1b32a-116">Podívejte se do zásobníku volání, včetně řetězců volání mezi JavaScript a .NET.</span><span class="sxs-lookup"><span data-stu-id="1b32a-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="1b32a-117">Teď *nemůžete*:</span><span class="sxs-lookup"><span data-stu-id="1b32a-117">For now, you *can't*:</span></span>

* <span data-ttu-id="1b32a-118">Přerušit při neošetřených výjimkách.</span><span class="sxs-lookup"><span data-stu-id="1b32a-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="1b32a-119">Zarážky volání během spouštění aplikace před spuštěním ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="1b32a-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="1b32a-120">To zahrnuje zarážky v `Program.Main` ( `Program.cs` ) a zarážky v [ `OnInitialized{Async}` metodách](xref:blazor/components/lifecycle#component-initialization-methods) komponent, které jsou načteny první stránkou požadovanou z aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1b32a-121">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="1b32a-121">Prerequisites</span></span>

<span data-ttu-id="1b32a-122">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="1b32a-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="1b32a-123">Google Chrome (verze 70 nebo novější) (výchozí)</span><span class="sxs-lookup"><span data-stu-id="1b32a-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="1b32a-124">Microsoft Edge (verze 80 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="1b32a-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="1b32a-125">Visual Studio pro Mac vyžaduje verzi 8,8 (Build 1532) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="1b32a-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="1b32a-126">Nejnovější verzi Visual Studio pro Mac nainstalujete tak, že vyberete tlačítko **stáhnout Visual Studio pro Mac** v [Microsoft: Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="1b32a-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="1b32a-127">V sadě Visual Studio vyberte kanál *verze Preview* .</span><span class="sxs-lookup"><span data-stu-id="1b32a-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="1b32a-128">Další informace najdete v tématu [instalace verze preview Visual Studio pro Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="1b32a-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="1b32a-129">Apple Safari na macOS se v tuto chvíli nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="1b32a-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="1b32a-130">Povolení ladění</span><span class="sxs-lookup"><span data-stu-id="1b32a-130">Enable debugging</span></span>

<span data-ttu-id="1b32a-131">Chcete-li povolit ladění pro existující Blazor WebAssembly aplikaci, aktualizujte `launchSettings.json` soubor v spouštěném projektu tak, aby do `inspectUri` každého spouštěcího profilu zahrnoval následující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="1b32a-131">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="1b32a-132">Po aktualizaci `launchSettings.json` by měl soubor vypadat podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1b32a-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="1b32a-133">`inspectUri`Vlastnost:</span><span class="sxs-lookup"><span data-stu-id="1b32a-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="1b32a-134">Umožňuje rozhraní IDE zjistit, že aplikace je Blazor WebAssembly aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-134">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="1b32a-135">Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím Blazor ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="1b32a-135">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="1b32a-136">Zástupný text pro protokol WebSockets ( `wsProtocol` ), hostitele ( `url.hostname` ), port ( `url.port` ) a identifikátor URI pro inspektor v spuštěném prohlížeči ( `browserInspectUri` ) jsou poskytovány rozhraním.</span><span class="sxs-lookup"><span data-stu-id="1b32a-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b32a-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b32a-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1b32a-138">Ladění Blazor WebAssembly aplikace v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="1b32a-138">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="1b32a-139">Vytvořte novou ASP.NET Core hostovanou Blazor WebAssembly aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1b32a-139">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="1b32a-140">Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="1b32a-141">**Spuštění bez ladění** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) není podporováno.</span><span class="sxs-lookup"><span data-stu-id="1b32a-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="1b32a-142">Když je aplikace spuštěna v konfiguraci ladění, režie ladění vždy vede k omezení malého výkonu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="1b32a-143">V *klientské* aplikaci nastavte zarážku na `currentCount++;` řádku v `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-143">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="1b32a-144">V prohlížeči přejděte na `Counter` stránku a kliknutím na tlačítko **klikněte na tlačítko Zobrazit** zarážku.</span><span class="sxs-lookup"><span data-stu-id="1b32a-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="1b32a-145">V aplikaci Visual Studio zkontrolujte hodnotu `currentCount` pole v okně **místní** hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1b32a-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="1b32a-146">Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="1b32a-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="1b32a-147">Při ladění Blazor WebAssembly aplikace můžete také ladit kód serveru:</span><span class="sxs-lookup"><span data-stu-id="1b32a-147">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="1b32a-148">Nastavte zarážku na `Pages/FetchData.razor` stránce v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="1b32a-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="1b32a-149">Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.</span><span class="sxs-lookup"><span data-stu-id="1b32a-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="1b32a-150">Přejděte na `Fetch Data` stránku a vyberte první zarážku v `FetchData` součásti těsně předtím, než VYDÁ požadavek HTTP na server.</span><span class="sxs-lookup"><span data-stu-id="1b32a-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="1b32a-151">Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a potom stiskněte zarážku na serveru v `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="1b32a-152">Opakovaným stisknutím klávesy <kbd>F5</kbd> můžete pokračovat v provádění a zobrazit tabulku předpovědi počasí vygenerovanou v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1b32a-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="1b32a-153">Zarážky se během spouštění aplikace **neprojeví** před spuštěním ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="1b32a-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="1b32a-154">To zahrnuje zarážky v `Program.Main` ( `Program.cs` ) a zarážky v [ `OnInitialized{Async}` metodách](xref:blazor/components/lifecycle#component-initialization-methods) komponent, které jsou načteny první stránkou požadovanou z aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="1b32a-155">Pokud je aplikace hostovaná v jiné [základní cestě aplikace](xref:blazor/host-and-deploy/index#app-base-path) než `/` , aktualizujte následující vlastnosti v nástroji `Properties/launchSettings.json` tak, aby odrážely základní cestu aplikace:</span><span class="sxs-lookup"><span data-stu-id="1b32a-155">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="1b32a-156">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="1b32a-156">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="1b32a-157">`inspectUri` každý profil:</span><span class="sxs-lookup"><span data-stu-id="1b32a-157">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="1b32a-158">Zástupné symboly v předchozích nastaveních:</span><span class="sxs-lookup"><span data-stu-id="1b32a-158">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="1b32a-159">`{INSECURE PORT}`: Nezabezpečený port.</span><span class="sxs-lookup"><span data-stu-id="1b32a-159">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="1b32a-160">Ve výchozím nastavení je k dispozici náhodná hodnota, ale je povolený vlastní port.</span><span class="sxs-lookup"><span data-stu-id="1b32a-160">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="1b32a-161">`{APP BASE PATH}`: Základní cesta aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-161">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="1b32a-162">`{SECURE PORT}`: Zabezpečený port.</span><span class="sxs-lookup"><span data-stu-id="1b32a-162">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="1b32a-163">Ve výchozím nastavení je k dispozici náhodná hodnota, ale je povolený vlastní port.</span><span class="sxs-lookup"><span data-stu-id="1b32a-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="1b32a-164">`{PROFILE 1, 2, ... N}`: Profily nastavení spuštění.</span><span class="sxs-lookup"><span data-stu-id="1b32a-164">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="1b32a-165">Aplikace obvykle ve výchozím nastavení určuje více než jeden profil (například profil pro IIS Express a profil projektu, který je používán serverem Kestrel).</span><span class="sxs-lookup"><span data-stu-id="1b32a-165">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="1b32a-166">V následujících příkladech je aplikace hostovaná na základě `/OAT` základní cesty aplikace nakonfigurované v `wwwroot/index.html` podobě `<base href="/OAT/">` :</span><span class="sxs-lookup"><span data-stu-id="1b32a-166">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="1b32a-167">Informace o tom, jak používat vlastní základní cestu aplikace pro Blazor WebAssembly aplikace, najdete v tématu <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="1b32a-167">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b32a-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b32a-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="1b32a-169">Samostatně ladit Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1b32a-169">Debug standalone Blazor WebAssembly</span></span></h2>

1. <span data-ttu-id="1b32a-170">Otevřete samostatnou Blazor WebAssembly aplikaci v vs Code.</span><span class="sxs-lookup"><span data-stu-id="1b32a-170">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="1b32a-171">Může se zobrazit oznámení, že pro povolení ladění je potřeba další nastavení:</span><span class="sxs-lookup"><span data-stu-id="1b32a-171">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="1b32a-172">Pro ladění aplikací je potřeba další nastavení Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="1b32a-172">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="1b32a-173">Pokud obdržíte oznámení:</span><span class="sxs-lookup"><span data-stu-id="1b32a-173">If you receive the notification:</span></span>

   * <span data-ttu-id="1b32a-174">Potvrďte, že je nainstalovaná nejnovější [rozšíření C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .</span><span class="sxs-lookup"><span data-stu-id="1b32a-174">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="1b32a-175">Pokud chcete zkontrolovat nainstalovaná rozšíření, **View**otevřete  >  **rozšíření** zobrazení z řádku nabídek nebo vyberte ikonu **rozšíření** na postranním panelu **aktivita** .</span><span class="sxs-lookup"><span data-stu-id="1b32a-175">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="1b32a-176">Potvrďte, že je povolené ladění v jazyce JavaScript verze Preview.</span><span class="sxs-lookup"><span data-stu-id="1b32a-176">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="1b32a-177">Otevřete nastavení z řádku nabídek (**File**  >  **Preferences**  >  **Nastavení**předvoleb souborů).</span><span class="sxs-lookup"><span data-stu-id="1b32a-177">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="1b32a-178">Hledání pomocí klíčových slov `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-178">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="1b32a-179">Ve výsledcích hledání potvrďte, že je zaškrtnuto políčko pro **ladění > JavaScript: použít verzi Preview** .</span><span class="sxs-lookup"><span data-stu-id="1b32a-179">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="1b32a-180">Pokud není k dispozici možnost Povolit ladění ve verzi Preview, upgradujte na nejnovější verzi VS Code nebo nainstalujte [rozšíření ladicího programu jazyka JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code verze 1,46 nebo starší).</span><span class="sxs-lookup"><span data-stu-id="1b32a-180">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="1b32a-181">Znovu načtěte okno.</span><span class="sxs-lookup"><span data-stu-id="1b32a-181">Reload the window.</span></span>

1. <span data-ttu-id="1b32a-182">Spusťte ladění pomocí klávesových zkratek <kbd>F5</kbd> nebo položky nabídky.</span><span class="sxs-lookup"><span data-stu-id="1b32a-182">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="1b32a-183">**Spuštění bez ladění** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) není podporováno.</span><span class="sxs-lookup"><span data-stu-id="1b32a-183">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="1b32a-184">Když je aplikace spuštěna v konfiguraci ladění, režie ladění vždy vede k omezení malého výkonu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-184">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="1b32a-185">Po zobrazení výzvy vyberte možnost \*\* Blazor WebAssembly ladění\*\* pro spuštění ladění.</span><span class="sxs-lookup"><span data-stu-id="1b32a-185">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="1b32a-186">Spustí se samostatná aplikace a otevře se prohlížeč ladění.</span><span class="sxs-lookup"><span data-stu-id="1b32a-186">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="1b32a-187">V *klientské* aplikaci nastavte zarážku na `currentCount++;` řádku v `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-187">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="1b32a-188">V prohlížeči přejděte na `Counter` stránku a kliknutím na tlačítko **klikněte na tlačítko Zobrazit** zarážku.</span><span class="sxs-lookup"><span data-stu-id="1b32a-188">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="1b32a-189">Zarážky se během spouštění aplikace **neprojeví** před spuštěním ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="1b32a-189">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="1b32a-190">To zahrnuje zarážky v `Program.Main` ( `Program.cs` ) a zarážky v [ `OnInitialized{Async}` metodách](xref:blazor/components/lifecycle#component-initialization-methods) komponent, které jsou načteny první stránkou požadovanou z aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-190">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="1b32a-191">Ladění je hostované Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1b32a-191">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="1b32a-192">Otevřete Blazor WebAssembly složku řešení hostované aplikace v vs Code.</span><span class="sxs-lookup"><span data-stu-id="1b32a-192">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="1b32a-193">Pokud pro projekt neexistuje konfigurační sada pro spuštění, zobrazí se následující oznámení.</span><span class="sxs-lookup"><span data-stu-id="1b32a-193">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="1b32a-194">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="1b32a-194">Select **Yes**.</span></span>

   > <span data-ttu-id="1b32a-195">Požadované prostředky pro sestavení a ladění chybí v {APPLICATION NAME}.</span><span class="sxs-lookup"><span data-stu-id="1b32a-195">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="1b32a-196">Přidat je?</span><span class="sxs-lookup"><span data-stu-id="1b32a-196">Add them?</span></span>

1. <span data-ttu-id="1b32a-197">V paletě příkazů v horní části okna vyberte *serverový* projekt v hostovaném řešení.</span><span class="sxs-lookup"><span data-stu-id="1b32a-197">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="1b32a-198">`launch.json`Soubor se vygeneruje s konfigurací spuštění pro spuštění ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-198">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="1b32a-199">Připojit k existující relaci ladění</span><span class="sxs-lookup"><span data-stu-id="1b32a-199">Attach to an existing debugging session</span></span>

<span data-ttu-id="1b32a-200">Pokud se chcete připojit ke spuštěné Blazor aplikaci, vytvořte `launch.json` soubor s následující konfigurací:</span><span class="sxs-lookup"><span data-stu-id="1b32a-200">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="1b32a-201">Připojení k relaci ladění se podporuje jenom pro samostatné aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-201">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="1b32a-202">Chcete-li použít ladění v plném zásobníku, musíte aplikaci spustit z VS Code.</span><span class="sxs-lookup"><span data-stu-id="1b32a-202">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="1b32a-203">Spustit možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="1b32a-203">Launch configuration options</span></span>

<span data-ttu-id="1b32a-204">Pro `blazorwasm` typ ladění () jsou podporovány následující možnosti konfigurace spuštění `.vscode/launch.json` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-204">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="1b32a-205">Možnost</span><span class="sxs-lookup"><span data-stu-id="1b32a-205">Option</span></span>    | <span data-ttu-id="1b32a-206">Popis</span><span class="sxs-lookup"><span data-stu-id="1b32a-206">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="1b32a-207">Pomocí `launch` můžete spustit a připojit relaci ladění k Blazor WebAssembly aplikaci nebo `attach` připojit relaci ladění k již spuštěné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1b32a-207">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="1b32a-208">Adresa URL, která se má otevřít v prohlížeči při ladění.</span><span class="sxs-lookup"><span data-stu-id="1b32a-208">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="1b32a-209">Výchozí hodnota je `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-209">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="1b32a-210">Prohlížeč, který se má spustit pro relaci ladění.</span><span class="sxs-lookup"><span data-stu-id="1b32a-210">The browser to launch for the debugging session.</span></span> <span data-ttu-id="1b32a-211">Nastavte na `edge` nebo `chrome`.</span><span class="sxs-lookup"><span data-stu-id="1b32a-211">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="1b32a-212">Výchozí hodnota je `chrome` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-212">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="1b32a-213">Slouží ke generování protokolů z ladicího programu JS.</span><span class="sxs-lookup"><span data-stu-id="1b32a-213">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="1b32a-214">Nastavte na `true` Generovat protokoly.</span><span class="sxs-lookup"><span data-stu-id="1b32a-214">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="1b32a-215">Musí být nastavené na hodnotu `true` při spuštění a ladění hostované Blazor WebAssembly aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-215">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="1b32a-216">Určuje absolutní cestu webového serveru.</span><span class="sxs-lookup"><span data-stu-id="1b32a-216">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="1b32a-217">By měla být nastavena, pokud je aplikace obsluhována z dílčího směrování.</span><span class="sxs-lookup"><span data-stu-id="1b32a-217">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="1b32a-218">Počet milisekund, po které se má čekat na připojení relace ladění</span><span class="sxs-lookup"><span data-stu-id="1b32a-218">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="1b32a-219">Výchozí hodnota je 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="1b32a-219">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="1b32a-220">Odkaz na spustitelný soubor pro spuštění serveru hostované aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-220">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="1b32a-221">Musí být nastaven `hosted` , pokud je `true` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-221">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="1b32a-222">Pracovní adresář, ve kterém se má spustit aplikace</span><span class="sxs-lookup"><span data-stu-id="1b32a-222">The working directory to launch the app under.</span></span> <span data-ttu-id="1b32a-223">Musí být nastaven `hosted` , pokud je `true` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-223">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="1b32a-224">Proměnné prostředí, které se mají poskytnout spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-224">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="1b32a-225">Platí pouze v případě `hosted` , že je nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-225">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="1b32a-226">Příklad konfigurací spuštění</span><span class="sxs-lookup"><span data-stu-id="1b32a-226">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="1b32a-227">Spuštění a ladění samostatné Blazor WebAssembly aplikace</span><span class="sxs-lookup"><span data-stu-id="1b32a-227">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="1b32a-228">Připojit se k běžící aplikaci v zadané adrese URL</span><span class="sxs-lookup"><span data-stu-id="1b32a-228">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="1b32a-229">Spuštění a ladění hostované Blazor WebAssembly aplikace pomocí Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1b32a-229">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="1b32a-230">Konfigurace prohlížeče je ve výchozím nastavení Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="1b32a-230">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="1b32a-231">Při použití Microsoft Edge pro ladění nastavte `browser` na `edge` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-231">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="1b32a-232">Pokud chcete používat Google Chrome, buď nenastavujte `browser` možnost, nebo nastavte hodnotu možnosti na `chrome` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-232">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="1b32a-233">V předchozím příkladu `MyHostedApp.Server.dll` je sestavení *serverové* aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-233">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="1b32a-234">`.vscode`Složka se nachází ve složce řešení vedle `Client` `Server` složek, a `Shared` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-234">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b32a-235">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b32a-235">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1b32a-236">Ladění Blazor WebAssembly aplikace v Visual Studio pro Mac:</span><span class="sxs-lookup"><span data-stu-id="1b32a-236">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="1b32a-237">Vytvořte novou ASP.NET Core hostovanou Blazor WebAssembly aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1b32a-237">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="1b32a-238">Stisknutím <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> spusťte aplikaci v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="1b32a-239">**Spuštění bez ladění** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) není podporováno.</span><span class="sxs-lookup"><span data-stu-id="1b32a-239">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="1b32a-240">Když je aplikace spuštěna v konfiguraci ladění, režie ladění vždy vede k omezení malého výkonu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-240">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="1b32a-241">Google Chrome nebo Microsoft Edge musí být vybraným prohlížečem pro relaci ladění.</span><span class="sxs-lookup"><span data-stu-id="1b32a-241">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="1b32a-242">V *klientské* aplikaci nastavte zarážku na `currentCount++;` řádku v `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-242">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="1b32a-243">V prohlížeči přejděte na `Counter` stránku a kliknutím na tlačítko **kliknutím na tlačítko** spustit zarážku stiskněte klávesu:</span><span class="sxs-lookup"><span data-stu-id="1b32a-243">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="1b32a-244">V aplikaci Visual Studio zkontrolujte hodnotu `currentCount` pole v okně **místní** hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1b32a-244">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="1b32a-245">Pokračujte v provádění stisknutím <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> .</span><span class="sxs-lookup"><span data-stu-id="1b32a-245">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="1b32a-246">Při ladění Blazor WebAssembly aplikace můžete také ladit kód serveru:</span><span class="sxs-lookup"><span data-stu-id="1b32a-246">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="1b32a-247">Nastavte zarážku na `Pages/FetchData.razor` stránce v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="1b32a-247">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="1b32a-248">Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.</span><span class="sxs-lookup"><span data-stu-id="1b32a-248">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="1b32a-249">Přejděte na `Fetch Data` stránku a vyberte první zarážku v `FetchData` součásti těsně předtím, než VYDÁ požadavek HTTP na server.</span><span class="sxs-lookup"><span data-stu-id="1b32a-249">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="1b32a-250">Pokračujte v provádění stisknutím <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> a potom stiskněte zarážku na serveru v `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-250">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="1b32a-251">Znovu stiskněte <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na tabulku předpovědi počasí vykreslenou v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1b32a-251">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="1b32a-252">Zarážky se během spouštění aplikace **neprojeví** před spuštěním ladicího proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="1b32a-252">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="1b32a-253">To zahrnuje zarážky v `Program.Main` ( `Program.cs` ) a zarážky v [ `OnInitialized{Async}` metodách](xref:blazor/components/lifecycle#component-initialization-methods) komponent, které jsou načteny první stránkou požadovanou z aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-253">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="1b32a-254">Další informace naleznete v tématu [ladění pomocí Visual Studio pro Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="1b32a-254">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="1b32a-255">Ladit v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="1b32a-255">Debug in the browser</span></span>

<span data-ttu-id="1b32a-256">*Pokyny v této části se vztahují na Google Chrome a Microsoft Edge běžící ve Windows.*</span><span class="sxs-lookup"><span data-stu-id="1b32a-256">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="1b32a-257">Spusťte ladicí sestavení aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="1b32a-257">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="1b32a-258">Spusťte prohlížeč a přejděte na adresu URL aplikace (například `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="1b32a-258">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="1b32a-259">V prohlížeči se pokuste zahájit vzdálené ladění stisknutím klávesy <kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>d</kbd>.</span><span class="sxs-lookup"><span data-stu-id="1b32a-259">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="1b32a-260">Prohlížeč musí běžet se zapnutým vzdáleným laděním, což není výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="1b32a-260">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="1b32a-261">Pokud je vzdálené ladění zakázané, zobrazí se stránka s informací o tom, že se pro spuštění prohlížeče s otevřeným portem ladění **nedaří najít** chybovou stránku karty prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="1b32a-261">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="1b32a-262">Postupujte podle pokynů pro prohlížeč a otevřete nové okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="1b32a-262">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="1b32a-263">Zavřete předchozí okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="1b32a-263">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="1b32a-264">Po spuštění prohlížeče se zapnutým vzdáleným laděním otevře klávesovou zkratku pro ladění v předchozím kroku novou kartu ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-264">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="1b32a-265">Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET aplikace v rámci `file://` uzlu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-265">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="1b32a-266">V kódu komponenty ( `.razor` soubory) a souborech kódu jazyka C# ( `.cs` ) jsou zarážky, které jste nastavili, zasaženy při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-266">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="1b32a-267">Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.</span><span class="sxs-lookup"><span data-stu-id="1b32a-267">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="1b32a-268">Blazor poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť.</span><span class="sxs-lookup"><span data-stu-id="1b32a-268">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="1b32a-269">Když se stiskne klávesová zkratka ladění, navede Blazor devtools Chrome na proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="1b32a-269">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="1b32a-270">Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="1b32a-270">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="1b32a-271">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="1b32a-271">Browser source maps</span></span>

<span data-ttu-id="1b32a-272">Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="1b32a-272">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="1b32a-273">V Blazor současné době ale nemapuje C# přímo na JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="1b32a-273">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="1b32a-274">Místo toho Blazor provede výklad il v prohlížeči, takže zdrojové mapy nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="1b32a-274">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1b32a-275">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="1b32a-275">Troubleshoot</span></span>

<span data-ttu-id="1b32a-276">Pokud dochází k chybám, může vám pomáhat následující tipy:</span><span class="sxs-lookup"><span data-stu-id="1b32a-276">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="1b32a-277">Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1b32a-277">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="1b32a-278">V konzole nástroje spusťte příkaz `localStorage.clear()` k odebrání všech zarážek.</span><span class="sxs-lookup"><span data-stu-id="1b32a-278">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="1b32a-279">Potvrďte, že jste nainstalovali a důvěřujete ASP.NET Core certifikát pro vývoj HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1b32a-279">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="1b32a-280">Další informace naleznete v tématu <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="1b32a-280">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="1b32a-281">Visual Studio vyžaduje možnost **Povolit ladění JavaScriptu pro ASP.NET (Chrome, Edge a IE)** v možnostech **nástrojů**  >  **Options**  >  **Debugging**  >  **Obecné**ladění.</span><span class="sxs-lookup"><span data-stu-id="1b32a-281">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="1b32a-282">Toto je výchozí nastavení pro Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1b32a-282">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="1b32a-283">Pokud ladění nefunguje, potvrďte, že je vybraná možnost.</span><span class="sxs-lookup"><span data-stu-id="1b32a-283">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="1b32a-284">Pokud vaše prostředí používá proxy server HTTP, ujistěte se, že `localhost` je součástí nastavení pro obcházení proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="1b32a-284">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="1b32a-285">To lze provést nastavením `NO_PROXY` proměnné prostředí v jednom z těchto parametrů:</span><span class="sxs-lookup"><span data-stu-id="1b32a-285">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="1b32a-286">`launchSettings.json`Soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="1b32a-286">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="1b32a-287">Na úrovni uživatelem nebo systémových proměnných prostředí, které se mají použít pro všechny aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-287">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="1b32a-288">Pokud používáte proměnnou prostředí, restartujte Visual Studio, aby se změna projevila.</span><span class="sxs-lookup"><span data-stu-id="1b32a-288">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="1b32a-289">Zarážky `OnInitialized{Async}` nejsou v neúspěšných.</span><span class="sxs-lookup"><span data-stu-id="1b32a-289">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="1b32a-290">BlazorSpuštění ladicího proxy serveru pro rozhraní trvá krátkou dobu, takže zarážky v [ `OnInitialized{Async}` metodě životního cyklu](xref:blazor/components/lifecycle#component-initialization-methods) nemusí být k dispozice.</span><span class="sxs-lookup"><span data-stu-id="1b32a-290">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="1b32a-291">Doporučujeme přidat zpoždění na začátek těla metody a umožnit tak spuštění ladicího proxy serveru nějakou dobu, než se zarážka zasáhne.</span><span class="sxs-lookup"><span data-stu-id="1b32a-291">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="1b32a-292">Můžete zahrnout zpoždění na základě [ `if` direktivy kompilátoru](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) , aby se zajistilo, že zpoždění není k dispozici pro sestavení pro vydání aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b32a-292">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="1b32a-293"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="1b32a-293"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="1b32a-294"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="1b32a-294"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="1b32a-295">Časový limit sady Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="1b32a-295">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="1b32a-296">Pokud Visual Studio vyvolá výjimku, že se ladicímu adaptéru nepovedlo spustit zmínku o tom, že byl dosažen časový limit, můžete nastavit časový limit pomocí nastavení registru:</span><span class="sxs-lookup"><span data-stu-id="1b32a-296">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="1b32a-297">`{TIMEOUT}`Zástupný symbol v předchozím příkazu je v milisekundách.</span><span class="sxs-lookup"><span data-stu-id="1b32a-297">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="1b32a-298">Například jedna minuta je přiřazena jako `60000` .</span><span class="sxs-lookup"><span data-stu-id="1b32a-298">For example, one minute is assigned as `60000`.</span></span>
