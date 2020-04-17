---
title: Ladění ASP.NET Blazor základní webové sestavy
author: guardrex
description: Přečtěte si, Blazor jak ladit aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 8b63444ba5c8cd45e64e722c8978ba4e6d90af36
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488745"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="f1962-103">Ladění ASP.NET Blazor základní webové sestavy</span><span class="sxs-lookup"><span data-stu-id="f1962-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="f1962-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="f1962-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="f1962-105">Aplikace WebAssembly lze ladit pomocí nástrojů pro vývoj prohlížeče v prohlížečích založených na chromu (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="f1962-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="f1962-106">Případně můžete ladit aplikaci pomocí Visual Studia nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f1962-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="f1962-107">Mezi dostupné scénáře patří:</span><span class="sxs-lookup"><span data-stu-id="f1962-107">Available scenarios include:</span></span>

* <span data-ttu-id="f1962-108">Nastavte a odeberte zarážky.</span><span class="sxs-lookup"><span data-stu-id="f1962-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="f1962-109">Spusťte aplikaci s podporou ladění v sadě Visual Studio a Visual Studio Code (podpora<kbd>F5).</kbd></span><span class="sxs-lookup"><span data-stu-id="f1962-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="f1962-110">Jeden krok (<kbd>F10</kbd>) prostřednictvím kódu.</span><span class="sxs-lookup"><span data-stu-id="f1962-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="f1962-111">Pokračovat spuštění kódu s <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v sadě Visual Studio nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f1962-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="f1962-112">V *locals* zobrazení, dodržovat hodnoty místních proměnných.</span><span class="sxs-lookup"><span data-stu-id="f1962-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="f1962-113">Podívejte se na zásobník volání, včetně řetězců volání, které přejdou z JavaScriptu do rozhraní .NET a z rozhraní .NET do Jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f1962-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="f1962-114">Pro tuto chvíli, *nemůžete:*</span><span class="sxs-lookup"><span data-stu-id="f1962-114">For now, you *can't*:</span></span>

* <span data-ttu-id="f1962-115">Zkontrolujte pole.</span><span class="sxs-lookup"><span data-stu-id="f1962-115">Inspect arrays.</span></span>
* <span data-ttu-id="f1962-116">Najeďte na členy.</span><span class="sxs-lookup"><span data-stu-id="f1962-116">Hover to inspect members.</span></span>
* <span data-ttu-id="f1962-117">Krok ladění do nebo ze spravovaného kódu.</span><span class="sxs-lookup"><span data-stu-id="f1962-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="f1962-118">Mít plnou podporu pro kontrolu typů hodnot.</span><span class="sxs-lookup"><span data-stu-id="f1962-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="f1962-119">Přerušit na neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="f1962-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="f1962-120">Při spuštění aplikace byly zarážky přístupů.</span><span class="sxs-lookup"><span data-stu-id="f1962-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="f1962-121">Ladění aplikace s pracovníkem služby.</span><span class="sxs-lookup"><span data-stu-id="f1962-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="f1962-122">Budeme i nadále zlepšovat ladění v nadcházejících verzích.</span><span class="sxs-lookup"><span data-stu-id="f1962-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1962-123">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f1962-123">Prerequisites</span></span>

<span data-ttu-id="f1962-124">Ladění vyžaduje některý z následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="f1962-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="f1962-125">Microsoft Edge (verze 80 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="f1962-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="f1962-126">Google Chrome (verze 70 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="f1962-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="f1962-127">Povolení ladění pro visual studio a kód sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1962-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="f1962-128">Ladění je povoleno automaticky pro nové projekty, které jsou vytvořeny pomocí Blazor ASP.NET Core 3.2 Preview 3 nebo novější šablona projektu WebAssembly[(aktuální verze je 3.2 Náhled 4](xref:blazor/get-started)).</span><span class="sxs-lookup"><span data-stu-id="f1962-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template ([current release is 3.2 Preview 4](xref:blazor/get-started)).</span></span>

<span data-ttu-id="f1962-129">Chcete-li povolit ladění Blazor pro existující aplikaci WebAssembly, aktualizujte soubor *launchSettings.json* v projektu spuštění tak, aby do každého profilu spuštění zahrnul následující `inspectUri` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f1962-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="f1962-130">Po aktualizaci by měl soubor *launchSettings.json* vypadat podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f1962-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="f1962-131">Nemovitost: `inspectUri`</span><span class="sxs-lookup"><span data-stu-id="f1962-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="f1962-132">Umožňuje ide zjistit, že aplikace Blazor je aplikace WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="f1962-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="f1962-133">Instruuje infrastrukturu ladění skriptů, aby Blazorse připojila k prohlížeči prostřednictvím proxy serveru pro ladění programu .</span><span class="sxs-lookup"><span data-stu-id="f1962-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="f1962-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1962-134">Visual Studio</span></span>

<span data-ttu-id="f1962-135">Ladění aplikace Blazor WebAssembly v Sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f1962-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="f1962-136">Ujistěte se, že jste [nainstalovali nejnovější verzi Preview Visual Studia 2019 16.6](https://visualstudio.com/preview) (náhled 2 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="f1962-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="f1962-137">Vytvořte novou aplikaci Blazor ASP.NET hostovaho webu Core.</span><span class="sxs-lookup"><span data-stu-id="f1962-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="f1962-138">Stisknutím <kbd>klávesy F5</kbd> spusťte aplikaci v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="f1962-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="f1962-139">Nastavte zarážku v *Counter.razor* v metodě. `IncrementCount`</span><span class="sxs-lookup"><span data-stu-id="f1962-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="f1962-140">Přejděte na kartu **Čítadlo** a vyberte tlačítko pro stisknutí zarážky:</span><span class="sxs-lookup"><span data-stu-id="f1962-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="f1962-142">Podívejte se na `currentCount` hodnotu pole v místním okně:</span><span class="sxs-lookup"><span data-stu-id="f1962-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Zobrazit místní obyvatele](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="f1962-144">Chcete-li pokračovat v provádění, stiskněte klávesu <kbd>F5.</kbd></span><span class="sxs-lookup"><span data-stu-id="f1962-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="f1962-145">Při ladění aplikace Blazor WebAssembly můžete také ladit kód serveru:</span><span class="sxs-lookup"><span data-stu-id="f1962-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="f1962-146">Nastavte zarážku na stránce *FetchData.razor* v . `OnInitializedAsync`</span><span class="sxs-lookup"><span data-stu-id="f1962-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="f1962-147">Nastavte zarážku `WeatherForecastController` v `Get` metodě akce.</span><span class="sxs-lookup"><span data-stu-id="f1962-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="f1962-148">Přejděte na kartu **Načíst data** a `FetchData` klikněte na první zarážku v komponentě těsně před tím, než server vydá požadavek HTTP:</span><span class="sxs-lookup"><span data-stu-id="f1962-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Ladění dat načtení](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="f1962-150">Stisknutím <kbd>klávesy F5</kbd> pokračujte v provádění a `WeatherForecastController`poté klepněte na zarážku na serveru v :</span><span class="sxs-lookup"><span data-stu-id="f1962-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Ladicí server](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="f1962-152">Dalším stisknutím <kbd>klávesy F5</kbd> umožníte pokračování provedení a zobrazí se vykreslená tabulka předpovědi počasí.</span><span class="sxs-lookup"><span data-stu-id="f1962-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="f1962-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1962-153">Visual Studio Code</span></span>

<span data-ttu-id="f1962-154">Ladění aplikace Blazor WebAssembly v kódu Sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f1962-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="f1962-155">Nainstalujte [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [debuggeru JavaScriptu (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="f1962-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Rozšíření](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Ladicí program náhledu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="f1962-158">Otevřete Blazor existující aplikaci WebAssembly s povoleným laděním.</span><span class="sxs-lookup"><span data-stu-id="f1962-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="f1962-159">Pokud se zobrazí následující oznámení, že k povolení ladění je nutné provést další nastavení, zkontrolujte, zda máte nainstalovaná správná rozšíření a je povoleno ladění náhledu JavaScriptu, a potom okno znovu načtěte:</span><span class="sxs-lookup"><span data-stu-id="f1962-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Další nastavení requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="f1962-161">Oznámení nabízí přidat požadované prostředky do aplikace pro vytváření a ladění.</span><span class="sxs-lookup"><span data-stu-id="f1962-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="f1962-162">Vyberte **Ano**:</span><span class="sxs-lookup"><span data-stu-id="f1962-162">Select **Yes**:</span></span>

     ![Přidání požadovaných datových zdrojů](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="f1962-164">Spuštění aplikace v ladicím programu je dvoustupňový proces:</span><span class="sxs-lookup"><span data-stu-id="f1962-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="f1962-165">1\.</span><span class="sxs-lookup"><span data-stu-id="f1962-165">1\.</span></span> <span data-ttu-id="f1962-166">**Nejprve**spusťte aplikaci pomocí konfigurace spuštění \*\*.NET Core Launch (Standalone).Blazor \*\*</span><span class="sxs-lookup"><span data-stu-id="f1962-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="f1962-167">2\.</span><span class="sxs-lookup"><span data-stu-id="f1962-167">2\.</span></span> <span data-ttu-id="f1962-168">**Po spuštění aplikace**spusťte prohlížeč pomocí konfigurace spuštění **.NET Core Blazor Debug Web Assembly v Chromu** (vyžaduje Chrome).</span><span class="sxs-lookup"><span data-stu-id="f1962-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="f1962-169">Chcete-li místo Chromu `type` používat Edge, změňte konfiguraci spuštění v *souboru .vscode/launch.json* z `pwa-chrome` na . `pwa-msedge`</span><span class="sxs-lookup"><span data-stu-id="f1962-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="f1962-170">Nastavte zarážku `IncrementCount` v `Counter` metodě v komponentě a pak vyberte tlačítko pro stisknutí zarážky:</span><span class="sxs-lookup"><span data-stu-id="f1962-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Čítač ladění v kódu VS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="f1962-172">Ladění v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="f1962-172">Debug in the browser</span></span>

1. <span data-ttu-id="f1962-173">Spusťte sestavení ladění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f1962-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="f1962-174">Stiskněte <kbd>klávesu Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="f1962-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="f1962-175">Prohlížeč musí být spuštěn s povoleným vzdáleným laděním.</span><span class="sxs-lookup"><span data-stu-id="f1962-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="f1962-176">Pokud je zakázáno vzdálené ladění, je generována chybová stránka nelze **najít ladnou kartu prohlížeče.**</span><span class="sxs-lookup"><span data-stu-id="f1962-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="f1962-177">Chybová stránka obsahuje pokyny pro spuštění prohlížeče s otevřeným ladicím portem, aby se Blazor ladicí proxy server mohl připojit k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f1962-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="f1962-178">*Zavřete všechny instance prohlížeče* a restartujte prohlížeč podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="f1962-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="f1962-179">Jakmile je prohlížeč spuštěn s povoleným vzdáleným laděním, ladicí klávesová zkratka otevře novou kartu ladicího programu. Po chvíli se na kartě **Zdroje** zobrazí seznam sestavení .NET v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f1962-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="f1962-180">Rozbalte každé sestavení a najděte zdrojové soubory *.cs*/*.razor,* které jsou k dispozici pro ladění.</span><span class="sxs-lookup"><span data-stu-id="f1962-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="f1962-181">Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky jsou přístupů při spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="f1962-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="f1962-182">Po zásahu zarážky, jeden krok (<kbd>F10</kbd>) prostřednictvím kódu nebo pokračovat<kbd>(F8)</kbd>spuštění kódu normálně.</span><span class="sxs-lookup"><span data-stu-id="f1962-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="f1962-183">Poskytuje ladicí proxy server, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol o soubor . NET-specifické informace.</span><span class="sxs-lookup"><span data-stu-id="f1962-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="f1962-184">Při ladění klávesové zkratky Blazor najede Chrome DevTools na proxy server.</span><span class="sxs-lookup"><span data-stu-id="f1962-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="f1962-185">Proxy se připojí k oknu prohlížeče, které se snažíte ladit (proto je třeba povolit vzdálené ladění).</span><span class="sxs-lookup"><span data-stu-id="f1962-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="f1962-186">Mapy zdroje prohlížeče</span><span class="sxs-lookup"><span data-stu-id="f1962-186">Browser source maps</span></span>

<span data-ttu-id="f1962-187">Mapy zdroje prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpět na původní zdrojové soubory a běžně se používají pro ladění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f1962-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="f1962-188">Vsoučasné Blazor době však není mapovat C# přímo do JavaScriptu / WASM.</span><span class="sxs-lookup"><span data-stu-id="f1962-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="f1962-189">Místo Blazor toho il interpretace v prohlížeči, takže zdrojové mapy nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="f1962-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f1962-190">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="f1962-190">Troubleshoot</span></span>

<span data-ttu-id="f1962-191">Pokud dochází k chybám, může vám pomoci následující tip:</span><span class="sxs-lookup"><span data-stu-id="f1962-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="f1962-192">Na kartě **Debugger** otevřete vývojářské nástroje v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="f1962-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="f1962-193">V konzole, `localStorage.clear()` spusťte odebrat všechny zarážky.</span><span class="sxs-lookup"><span data-stu-id="f1962-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
