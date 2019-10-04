---
title: Použití signalizace ASP.NET Core s TypeScript a webpackem
author: ssougnez
description: V tomto kurzu nakonfigurujete sadu Webpack tak, aby se vytvořila sada a vytvořila se webová aplikace ASP.NET Core Signaler, jejíž klient je napsaný v TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 10/04/2019
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 630e8cb5efe9c313479960626d3d864c4923cbd1
ms.sourcegitcommit: 3ffcd8cbff8b49128733842f72270bc58279de70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955935"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="a5067-103">Použití signalizace ASP.NET Core s TypeScript a webpackem</span><span class="sxs-lookup"><span data-stu-id="a5067-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="a5067-104">Od [Sébastien Sougnez](https://twitter.com/ssougnez) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a5067-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a5067-105">[Webpack](https://webpack.js.org/) umožňuje vývojářům seskupit a sestavit prostředky webové aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a5067-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="a5067-106">V tomto kurzu se dozvíte, jak používat Webpack ve webové aplikaci ASP.NET Coreového signálu, jejíž klient je napsaný v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="a5067-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="a5067-107">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="a5067-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a5067-108">Generování uživatelského rozhraní pro aplikaci signalizace úvodní ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5067-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="a5067-109">Konfigurace klienta TypeScript nástroje Signal</span><span class="sxs-lookup"><span data-stu-id="a5067-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="a5067-110">Konfigurace kanálu sestavení pomocí webpacku</span><span class="sxs-lookup"><span data-stu-id="a5067-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="a5067-111">Konfigurace serveru signálu</span><span class="sxs-lookup"><span data-stu-id="a5067-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="a5067-112">Povolit komunikaci mezi klientem a serverem</span><span class="sxs-lookup"><span data-stu-id="a5067-112">Enable communication between client and server</span></span>

<span data-ttu-id="a5067-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a5067-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="a5067-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a5067-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5067-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5067-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5067-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="a5067-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="a5067-117">.NET Core SDK 3,0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a5067-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="a5067-118">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="a5067-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5067-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5067-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="a5067-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5067-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="a5067-121">.NET Core SDK 3,0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a5067-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="a5067-122">C#pro Visual Studio Code verze 1.17.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a5067-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="a5067-123">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="a5067-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="a5067-124">Vytvoření webové aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5067-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5067-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5067-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a5067-126">Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* .</span><span class="sxs-lookup"><span data-stu-id="a5067-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="a5067-127">Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři.</span><span class="sxs-lookup"><span data-stu-id="a5067-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="a5067-128">Postupujte podle těchto pokynů v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a5067-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="a5067-129">Přejděte k **nástrojům** @no__t **možnosti** -1 > **projekty a řešení** > **externí webové nástroje**pro **Web Správa balíčků** >.</span><span class="sxs-lookup"><span data-stu-id="a5067-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="a5067-130">Ze seznamu vyberte položku *$ (cesta)* .</span><span class="sxs-lookup"><span data-stu-id="a5067-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="a5067-131">Kliknutím na šipku nahoru tuto položku přesunete do druhé pozice v seznamu.</span><span class="sxs-lookup"><span data-stu-id="a5067-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="a5067-133">Konfigurace sady Visual Studio byla dokončena.</span><span class="sxs-lookup"><span data-stu-id="a5067-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="a5067-134">Je čas vytvořit projekt.</span><span class="sxs-lookup"><span data-stu-id="a5067-134">It's time to create the project.</span></span>

1. <span data-ttu-id="a5067-135">Použijte možnost **soubor** > **Nový** @no__t nabídky **projekt** -3 a vyberte šablonu **ASP.NET Core webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="a5067-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="a5067-136">Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a5067-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="a5067-137">V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 3,0* .</span><span class="sxs-lookup"><span data-stu-id="a5067-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="a5067-138">Vyberte **prázdnou** šablonu a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a5067-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5067-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5067-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a5067-140">V **integrovaném terminálu**spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a5067-140">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="a5067-141">Prázdná webová aplikace ASP.NET Core, která cílí na .NET Core, se vytvoří v adresáři *SignalRWebPack* .</span><span class="sxs-lookup"><span data-stu-id="a5067-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="a5067-142">Konfigurace sady Webpack a TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="a5067-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="a5067-143">Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a5067-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="a5067-144">Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte soubor *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a5067-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="a5067-145">Přidejte zvýrazněnou vlastnost do souboru *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a5067-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="a5067-146">Nastavení vlastnosti `private` na hodnotu `true` brání v dalším kroku upozornění na instalaci balíčku.</span><span class="sxs-lookup"><span data-stu-id="a5067-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="a5067-147">Nainstalujte požadované balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="a5067-147">Install the required npm packages.</span></span> <span data-ttu-id="a5067-148">Z kořenového adresáře projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a5067-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="a5067-149">Některé podrobnosti příkazu si všimněte:</span><span class="sxs-lookup"><span data-stu-id="a5067-149">Some command details to note:</span></span>

    * <span data-ttu-id="a5067-150">Číslo verze se řídí znakem `@` pro každý název balíčku.</span><span class="sxs-lookup"><span data-stu-id="a5067-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="a5067-151">NPM nainstaluje tyto konkrétní verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="a5067-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="a5067-152">Možnost `-E` zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *Package. JSON*.</span><span class="sxs-lookup"><span data-stu-id="a5067-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="a5067-153">Například `"webpack": "4.29.3"` se použije místo `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="a5067-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="a5067-154">Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="a5067-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="a5067-155">Další podrobnosti najdete v oficiální dokumentaci k [instalaci npm](https://docs.npmjs.com/cli/install) .</span><span class="sxs-lookup"><span data-stu-id="a5067-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="a5067-156">Nahraďte vlastnost `scripts` souboru *Package. JSON* následujícím fragmentem kódu:</span><span class="sxs-lookup"><span data-stu-id="a5067-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="a5067-157">Některé vysvětlení skriptů:</span><span class="sxs-lookup"><span data-stu-id="a5067-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="a5067-158">`build`: Rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="a5067-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="a5067-159">Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="a5067-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="a5067-160">Možnost `mode` zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace.</span><span class="sxs-lookup"><span data-stu-id="a5067-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="a5067-161">Používejte pouze `build` ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="a5067-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="a5067-162">`release`: Rozbalí prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="a5067-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="a5067-163">`publish`: Spustí skript `release` pro vytvoření svazku prostředků na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="a5067-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="a5067-164">Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5067-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="a5067-165">V kořenovém adresáři projektu vytvořte soubor s názvem *Webpack. config. js*s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a5067-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="a5067-166">Předchozí soubor nakonfiguruje kompilaci sady Webpack.</span><span class="sxs-lookup"><span data-stu-id="a5067-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="a5067-167">Některé podrobnosti konfigurace k poznámení:</span><span class="sxs-lookup"><span data-stu-id="a5067-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="a5067-168">Vlastnost `output` přepisuje výchozí hodnotu *DIST*.</span><span class="sxs-lookup"><span data-stu-id="a5067-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="a5067-169">Místo toho se svazek vydává v adresáři *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="a5067-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="a5067-170">Pole `resolve.extensions` zahrnuje *. js* pro Import klientského JavaScriptu pro signalizaci.</span><span class="sxs-lookup"><span data-stu-id="a5067-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="a5067-171">V kořenovém adresáři projektu vytvořte nový *zdrojový* adresář.</span><span class="sxs-lookup"><span data-stu-id="a5067-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="a5067-172">Jeho účelem je uložit prostředky na straně klienta v projektu.</span><span class="sxs-lookup"><span data-stu-id="a5067-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="a5067-173">Vytvořte *Src/index.html* s následujícím obsahem.</span><span class="sxs-lookup"><span data-stu-id="a5067-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="a5067-174">Předchozí kód HTML definuje standardní kód domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="a5067-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="a5067-175">Vytvořte nový adresář *Src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="a5067-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="a5067-176">Jeho účelem je uložit soubory *. CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="a5067-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="a5067-177">Vytvořte *Src/CSS/Main. CSS* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a5067-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="a5067-178">Předchozí *hlavní soubor. CSS* styly aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5067-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="a5067-179">Vytvořte *Src/tsconfig. JSON* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a5067-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="a5067-180">Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="a5067-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="a5067-181">Vytvořte *Src/index. TS* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a5067-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="a5067-182">Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="a5067-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="a5067-183">`keyup`: Tato událost se aktivuje, když uživatel zadá něco do textového pole identifikovaného jako `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="a5067-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="a5067-184">Funkce `send` se volá, když uživatel stiskne klávesu **ENTER** .</span><span class="sxs-lookup"><span data-stu-id="a5067-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="a5067-185">`click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="a5067-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="a5067-186">Je volána funkce `send`.</span><span class="sxs-lookup"><span data-stu-id="a5067-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="a5067-187">Konfigurace aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5067-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="a5067-188">V metodě `Startup.Configure` přidejte volání do [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="a5067-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="a5067-189">Předchozí kód umožňuje serveru vyhledat a zpracovat soubor *index. html* bez ohledu na to, zda uživatel zadal svou úplnou adresu URL nebo kořenovou adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5067-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="a5067-190">Na konci metody `Startup.Configure` namapujte cestu */hub* k rozbočovači `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="a5067-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="a5067-191">Nahraďte kód, který se zobrazí *Hello World!*</span><span class="sxs-lookup"><span data-stu-id="a5067-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="a5067-192">s následujícím řádkem:</span><span class="sxs-lookup"><span data-stu-id="a5067-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="a5067-193">V metodě `Startup.ConfigureServices` volejte [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="a5067-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="a5067-194">Přidá do projektu služby signalizace.</span><span class="sxs-lookup"><span data-stu-id="a5067-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="a5067-195">V kořenovém adresáři projektu vytvořte nový adresář s názvem *centra*.</span><span class="sxs-lookup"><span data-stu-id="a5067-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="a5067-196">Jejím účelem je uložit centrum signalizace, které se vytvoří v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="a5067-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="a5067-197">Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a5067-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="a5067-198">Přidejte následující kód na začátek souboru *Startup.cs* pro vyřešení odkazu `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="a5067-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="a5067-199">Povolit komunikaci klienta a serveru</span><span class="sxs-lookup"><span data-stu-id="a5067-199">Enable client and server communication</span></span>

<span data-ttu-id="a5067-200">Aplikace aktuálně zobrazuje jednoduchý formulář pro posílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="a5067-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="a5067-201">Při pokusu o provedení akce nedojde k žádné akci.</span><span class="sxs-lookup"><span data-stu-id="a5067-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="a5067-202">Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.</span><span class="sxs-lookup"><span data-stu-id="a5067-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="a5067-203">V kořenu projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a5067-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="a5067-204">Předchozí příkaz nainstaluje [klienta TypeScript Signal](https://www.npmjs.com/package/@aspnet/signalr), který umožňuje klientovi odesílat zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="a5067-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@aspnet/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="a5067-205">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="a5067-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="a5067-206">Předchozí kód podporuje příjem zpráv ze serveru.</span><span class="sxs-lookup"><span data-stu-id="a5067-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="a5067-207">Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="a5067-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="a5067-208">Funkce `withUrl` nakonfiguruje adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="a5067-208">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="a5067-209">Signalizace umožňuje výměnu zpráv mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="a5067-209">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="a5067-210">Každá zpráva má konkrétní název.</span><span class="sxs-lookup"><span data-stu-id="a5067-210">Each message has a specific name.</span></span> <span data-ttu-id="a5067-211">Můžete například mít zprávy s názvem `messageReceived`, které spouštějí logiku odpovědnou za zobrazení nové zprávy v zóně zprávy.</span><span class="sxs-lookup"><span data-stu-id="a5067-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="a5067-212">Naslouchat konkrétní zprávě se dá provést pomocí funkce `on`.</span><span class="sxs-lookup"><span data-stu-id="a5067-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="a5067-213">Můžete naslouchat libovolnému počtu názvů zpráv.</span><span class="sxs-lookup"><span data-stu-id="a5067-213">You can listen to any number of message names.</span></span> <span data-ttu-id="a5067-214">Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy.</span><span class="sxs-lookup"><span data-stu-id="a5067-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="a5067-215">Jakmile klient obdrží zprávu, vytvoří se nový prvek `div` s názvem autora a obsahem zprávy ve svém atributu `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="a5067-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="a5067-216">Je přidáno do hlavního prvku `div` zobrazujícím zprávy.</span><span class="sxs-lookup"><span data-stu-id="a5067-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="a5067-217">Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="a5067-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="a5067-218">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="a5067-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="a5067-219">Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání metody `send`.</span><span class="sxs-lookup"><span data-stu-id="a5067-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="a5067-220">První parametr metody je název zprávy.</span><span class="sxs-lookup"><span data-stu-id="a5067-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="a5067-221">Data zprávy jsou v ostatních parametrech nezvyklá.</span><span class="sxs-lookup"><span data-stu-id="a5067-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="a5067-222">V tomto příkladu se na server pošle zpráva identifikovaná jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="a5067-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="a5067-223">Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole.</span><span class="sxs-lookup"><span data-stu-id="a5067-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="a5067-224">Pokud funkce Send funguje, hodnota textového pole se nevymaže.</span><span class="sxs-lookup"><span data-stu-id="a5067-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="a5067-225">Přidejte zvýrazněnou metodu do třídy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="a5067-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="a5067-226">Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží.</span><span class="sxs-lookup"><span data-stu-id="a5067-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="a5067-227">Pro příjem všech zpráv není nutné mít obecnou metodu `on`.</span><span class="sxs-lookup"><span data-stu-id="a5067-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="a5067-228">Metoda pojmenovaná po názvu zprávy postačuje.</span><span class="sxs-lookup"><span data-stu-id="a5067-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="a5067-229">V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="a5067-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="a5067-230">Metoda C# `NewMessage` očekává data odesílaná klientem.</span><span class="sxs-lookup"><span data-stu-id="a5067-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="a5067-231">Na klientech se provede volání metody [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) [. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="a5067-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="a5067-232">Přijaté zprávy se odesílají všem klientům připojeným k centru.</span><span class="sxs-lookup"><span data-stu-id="a5067-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="a5067-233">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="a5067-233">Test the app</span></span>

<span data-ttu-id="a5067-234">Ověřte, že aplikace funguje s následujícím postupem.</span><span class="sxs-lookup"><span data-stu-id="a5067-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5067-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5067-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a5067-236">Spusťte příkaz Webpack v režimu *vydání* .</span><span class="sxs-lookup"><span data-stu-id="a5067-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="a5067-237">Pomocí okna **konzoly Správce balíčků** spusťte v kořenovém adresáři projektu následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="a5067-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="a5067-238">Pokud nejste v kořenovém adresáři projektu, před zadáním příkazu zadejte `cd SignalRWebPack`.</span><span class="sxs-lookup"><span data-stu-id="a5067-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="a5067-239">Vyberte **ladit** > **Spustit bez ladění** a spusťte aplikaci v prohlížeči bez připojení ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="a5067-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="a5067-240">Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="a5067-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="a5067-241">Pokud se zobrazí chyby kompilace, zkuste řešení zavřít a znovu otevřít.</span><span class="sxs-lookup"><span data-stu-id="a5067-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="a5067-242">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="a5067-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="a5067-243">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="a5067-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a5067-244">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="a5067-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="a5067-245">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="a5067-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5067-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5067-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a5067-247">Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="a5067-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="a5067-248">Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="a5067-248">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="a5067-249">Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.</span><span class="sxs-lookup"><span data-stu-id="a5067-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="a5067-250">Otevřete prohlížeč `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="a5067-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="a5067-251">Soubor *wwwroot/index.html* se obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="a5067-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="a5067-252">Zkopírujte adresu URL z panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="a5067-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="a5067-253">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="a5067-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="a5067-254">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="a5067-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a5067-255">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="a5067-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="a5067-256">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="a5067-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5067-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5067-258">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5067-259">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="a5067-259">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="a5067-260">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="a5067-260">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="a5067-261">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="a5067-261">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5067-262">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5067-262">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="a5067-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5067-263">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="a5067-264">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="a5067-264">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="a5067-265">C#pro Visual Studio Code verze 1.17.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="a5067-265">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="a5067-266">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="a5067-266">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="a5067-267">Vytvoření webové aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5067-267">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5067-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5067-268">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a5067-269">Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* .</span><span class="sxs-lookup"><span data-stu-id="a5067-269">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="a5067-270">Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři.</span><span class="sxs-lookup"><span data-stu-id="a5067-270">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="a5067-271">Postupujte podle těchto pokynů v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a5067-271">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="a5067-272">Přejděte k **nástrojům** @no__t **možnosti** -1 > **projekty a řešení** > **externí webové nástroje**pro **Web Správa balíčků** >.</span><span class="sxs-lookup"><span data-stu-id="a5067-272">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="a5067-273">Ze seznamu vyberte položku *$ (cesta)* .</span><span class="sxs-lookup"><span data-stu-id="a5067-273">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="a5067-274">Kliknutím na šipku nahoru tuto položku přesunete do druhé pozice v seznamu.</span><span class="sxs-lookup"><span data-stu-id="a5067-274">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="a5067-276">Konfigurace sady Visual Studio byla dokončena.</span><span class="sxs-lookup"><span data-stu-id="a5067-276">Visual Studio configuration is completed.</span></span> <span data-ttu-id="a5067-277">Je čas vytvořit projekt.</span><span class="sxs-lookup"><span data-stu-id="a5067-277">It's time to create the project.</span></span>

1. <span data-ttu-id="a5067-278">Použijte možnost **soubor** > **Nový** @no__t nabídky **projekt** -3 a vyberte šablonu **ASP.NET Core webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="a5067-278">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="a5067-279">Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a5067-279">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="a5067-280">V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 2,2* .</span><span class="sxs-lookup"><span data-stu-id="a5067-280">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="a5067-281">Vyberte **prázdnou** šablonu a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a5067-281">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5067-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5067-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a5067-283">V **integrovaném terminálu**spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a5067-283">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="a5067-284">Prázdná webová aplikace ASP.NET Core, která cílí na .NET Core, se vytvoří v adresáři *SignalRWebPack* .</span><span class="sxs-lookup"><span data-stu-id="a5067-284">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="a5067-285">Konfigurace sady Webpack a TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="a5067-285">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="a5067-286">Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a5067-286">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="a5067-287">Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte soubor *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a5067-287">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="a5067-288">Přidejte zvýrazněnou vlastnost do souboru *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a5067-288">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="a5067-289">Nastavení vlastnosti `private` na hodnotu `true` brání v dalším kroku upozornění na instalaci balíčku.</span><span class="sxs-lookup"><span data-stu-id="a5067-289">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="a5067-290">Nainstalujte požadované balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="a5067-290">Install the required npm packages.</span></span> <span data-ttu-id="a5067-291">Z kořenového adresáře projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a5067-291">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="a5067-292">Některé podrobnosti příkazu si všimněte:</span><span class="sxs-lookup"><span data-stu-id="a5067-292">Some command details to note:</span></span>

    * <span data-ttu-id="a5067-293">Číslo verze se řídí znakem `@` pro každý název balíčku.</span><span class="sxs-lookup"><span data-stu-id="a5067-293">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="a5067-294">NPM nainstaluje tyto konkrétní verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="a5067-294">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="a5067-295">Možnost `-E` zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *Package. JSON*.</span><span class="sxs-lookup"><span data-stu-id="a5067-295">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="a5067-296">Například `"webpack": "4.29.3"` se použije místo `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="a5067-296">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="a5067-297">Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="a5067-297">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="a5067-298">Další podrobnosti najdete v oficiální dokumentaci k [instalaci npm](https://docs.npmjs.com/cli/install) .</span><span class="sxs-lookup"><span data-stu-id="a5067-298">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="a5067-299">Nahraďte vlastnost `scripts` souboru *Package. JSON* následujícím fragmentem kódu:</span><span class="sxs-lookup"><span data-stu-id="a5067-299">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="a5067-300">Některé vysvětlení skriptů:</span><span class="sxs-lookup"><span data-stu-id="a5067-300">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="a5067-301">`build`: Rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="a5067-301">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="a5067-302">Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="a5067-302">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="a5067-303">Možnost `mode` zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace.</span><span class="sxs-lookup"><span data-stu-id="a5067-303">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="a5067-304">Používejte pouze `build` ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="a5067-304">Only use `build` in development.</span></span>
    * <span data-ttu-id="a5067-305">`release`: Rozbalí prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="a5067-305">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="a5067-306">`publish`: Spustí skript `release` pro vytvoření svazku prostředků na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="a5067-306">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="a5067-307">Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5067-307">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="a5067-308">V kořenovém adresáři projektu vytvořte soubor s názvem *Webpack. config. js*s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a5067-308">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="a5067-309">Předchozí soubor nakonfiguruje kompilaci sady Webpack.</span><span class="sxs-lookup"><span data-stu-id="a5067-309">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="a5067-310">Některé podrobnosti konfigurace k poznámení:</span><span class="sxs-lookup"><span data-stu-id="a5067-310">Some configuration details to note:</span></span>

    * <span data-ttu-id="a5067-311">Vlastnost `output` přepisuje výchozí hodnotu *DIST*.</span><span class="sxs-lookup"><span data-stu-id="a5067-311">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="a5067-312">Místo toho se svazek vydává v adresáři *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="a5067-312">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="a5067-313">Pole `resolve.extensions` zahrnuje *. js* pro Import klientského JavaScriptu pro signalizaci.</span><span class="sxs-lookup"><span data-stu-id="a5067-313">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="a5067-314">V kořenovém adresáři projektu vytvořte nový *zdrojový* adresář.</span><span class="sxs-lookup"><span data-stu-id="a5067-314">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="a5067-315">Jeho účelem je uložit prostředky na straně klienta v projektu.</span><span class="sxs-lookup"><span data-stu-id="a5067-315">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="a5067-316">Vytvořte *Src/index.html* s následujícím obsahem.</span><span class="sxs-lookup"><span data-stu-id="a5067-316">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="a5067-317">Předchozí kód HTML definuje standardní kód domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="a5067-317">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="a5067-318">Vytvořte nový adresář *Src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="a5067-318">Create a new *src/css* directory.</span></span> <span data-ttu-id="a5067-319">Jeho účelem je uložit soubory *. CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="a5067-319">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="a5067-320">Vytvořte *Src/CSS/Main. CSS* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a5067-320">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="a5067-321">Předchozí *hlavní soubor. CSS* styly aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5067-321">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="a5067-322">Vytvořte *Src/tsconfig. JSON* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a5067-322">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="a5067-323">Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="a5067-323">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="a5067-324">Vytvořte *Src/index. TS* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a5067-324">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="a5067-325">Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="a5067-325">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="a5067-326">`keyup`: Tato událost se aktivuje, když uživatel zadá něco do textového pole identifikovaného jako `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="a5067-326">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="a5067-327">Funkce `send` se volá, když uživatel stiskne klávesu **ENTER** .</span><span class="sxs-lookup"><span data-stu-id="a5067-327">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="a5067-328">`click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="a5067-328">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="a5067-329">Je volána funkce `send`.</span><span class="sxs-lookup"><span data-stu-id="a5067-329">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="a5067-330">Konfigurace aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5067-330">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="a5067-331">Kód uvedený v metodě `Startup.Configure` zobrazuje *Hello World!* .</span><span class="sxs-lookup"><span data-stu-id="a5067-331">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="a5067-332">Nahraďte volání metody `app.Run` voláními do [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="a5067-332">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="a5067-333">Předchozí kód umožňuje serveru vyhledat a zpracovat soubor *index. html* bez ohledu na to, zda uživatel zadal svou úplnou adresu URL nebo kořenovou adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5067-333">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="a5067-334">Zavolejte [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) v metodě `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a5067-334">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a5067-335">Přidá do projektu služby signalizace.</span><span class="sxs-lookup"><span data-stu-id="a5067-335">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="a5067-336">Namapujte */hubou* trasu na rozbočovač `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="a5067-336">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="a5067-337">Na konec metody `Startup.Configure` přidejte následující řádky:</span><span class="sxs-lookup"><span data-stu-id="a5067-337">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="a5067-338">V kořenovém adresáři projektu vytvořte nový adresář s názvem *centra*.</span><span class="sxs-lookup"><span data-stu-id="a5067-338">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="a5067-339">Jejím účelem je uložit centrum signalizace, které se vytvoří v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="a5067-339">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="a5067-340">Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a5067-340">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="a5067-341">Přidejte následující kód na začátek souboru *Startup.cs* pro vyřešení odkazu `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="a5067-341">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="a5067-342">Povolit komunikaci klienta a serveru</span><span class="sxs-lookup"><span data-stu-id="a5067-342">Enable client and server communication</span></span>

<span data-ttu-id="a5067-343">Aplikace aktuálně zobrazuje jednoduchý formulář pro posílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="a5067-343">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="a5067-344">Při pokusu o provedení akce nedojde k žádné akci.</span><span class="sxs-lookup"><span data-stu-id="a5067-344">Nothing happens when you try to do so.</span></span> <span data-ttu-id="a5067-345">Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.</span><span class="sxs-lookup"><span data-stu-id="a5067-345">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="a5067-346">V kořenu projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a5067-346">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="a5067-347">Předchozí příkaz nainstaluje [klienta TypeScript Signal](https://www.npmjs.com/package/@aspnet/signalr), který umožňuje klientovi odesílat zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="a5067-347">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@aspnet/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="a5067-348">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="a5067-348">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="a5067-349">Předchozí kód podporuje příjem zpráv ze serveru.</span><span class="sxs-lookup"><span data-stu-id="a5067-349">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="a5067-350">Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="a5067-350">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="a5067-351">Funkce `withUrl` nakonfiguruje adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="a5067-351">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="a5067-352">Signalizace umožňuje výměnu zpráv mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="a5067-352">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="a5067-353">Každá zpráva má konkrétní název.</span><span class="sxs-lookup"><span data-stu-id="a5067-353">Each message has a specific name.</span></span> <span data-ttu-id="a5067-354">Můžete například mít zprávy s názvem `messageReceived`, které spouštějí logiku odpovědnou za zobrazení nové zprávy v zóně zprávy.</span><span class="sxs-lookup"><span data-stu-id="a5067-354">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="a5067-355">Naslouchat konkrétní zprávě se dá provést pomocí funkce `on`.</span><span class="sxs-lookup"><span data-stu-id="a5067-355">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="a5067-356">Můžete naslouchat libovolnému počtu názvů zpráv.</span><span class="sxs-lookup"><span data-stu-id="a5067-356">You can listen to any number of message names.</span></span> <span data-ttu-id="a5067-357">Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy.</span><span class="sxs-lookup"><span data-stu-id="a5067-357">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="a5067-358">Jakmile klient obdrží zprávu, vytvoří se nový prvek `div` s názvem autora a obsahem zprávy ve svém atributu `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="a5067-358">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="a5067-359">Je přidáno do hlavního prvku `div` zobrazujícím zprávy.</span><span class="sxs-lookup"><span data-stu-id="a5067-359">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="a5067-360">Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="a5067-360">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="a5067-361">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="a5067-361">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="a5067-362">Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání metody `send`.</span><span class="sxs-lookup"><span data-stu-id="a5067-362">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="a5067-363">První parametr metody je název zprávy.</span><span class="sxs-lookup"><span data-stu-id="a5067-363">The method's first parameter is the message name.</span></span> <span data-ttu-id="a5067-364">Data zprávy jsou v ostatních parametrech nezvyklá.</span><span class="sxs-lookup"><span data-stu-id="a5067-364">The message data inhabits the other parameters.</span></span> <span data-ttu-id="a5067-365">V tomto příkladu se na server pošle zpráva identifikovaná jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="a5067-365">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="a5067-366">Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole.</span><span class="sxs-lookup"><span data-stu-id="a5067-366">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="a5067-367">Pokud funkce Send funguje, hodnota textového pole se nevymaže.</span><span class="sxs-lookup"><span data-stu-id="a5067-367">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="a5067-368">Přidejte zvýrazněnou metodu do třídy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="a5067-368">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="a5067-369">Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží.</span><span class="sxs-lookup"><span data-stu-id="a5067-369">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="a5067-370">Pro příjem všech zpráv není nutné mít obecnou metodu `on`.</span><span class="sxs-lookup"><span data-stu-id="a5067-370">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="a5067-371">Metoda pojmenovaná po názvu zprávy postačuje.</span><span class="sxs-lookup"><span data-stu-id="a5067-371">A method named after the message name suffices.</span></span>

    <span data-ttu-id="a5067-372">V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="a5067-372">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="a5067-373">Metoda C# `NewMessage` očekává data odesílaná klientem.</span><span class="sxs-lookup"><span data-stu-id="a5067-373">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="a5067-374">Na klientech se provede volání metody [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) [. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="a5067-374">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="a5067-375">Přijaté zprávy se odesílají všem klientům připojeným k centru.</span><span class="sxs-lookup"><span data-stu-id="a5067-375">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="a5067-376">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="a5067-376">Test the app</span></span>

<span data-ttu-id="a5067-377">Ověřte, že aplikace funguje s následujícím postupem.</span><span class="sxs-lookup"><span data-stu-id="a5067-377">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5067-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5067-378">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a5067-379">Spusťte příkaz Webpack v režimu *vydání* .</span><span class="sxs-lookup"><span data-stu-id="a5067-379">Run Webpack in *release* mode.</span></span> <span data-ttu-id="a5067-380">Pomocí okna **konzoly Správce balíčků** spusťte v kořenovém adresáři projektu následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="a5067-380">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="a5067-381">Pokud nejste v kořenovém adresáři projektu, před zadáním příkazu zadejte `cd SignalRWebPack`.</span><span class="sxs-lookup"><span data-stu-id="a5067-381">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="a5067-382">Vyberte **ladit** > **Spustit bez ladění** a spusťte aplikaci v prohlížeči bez připojení ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="a5067-382">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="a5067-383">Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="a5067-383">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="a5067-384">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="a5067-384">Open another browser instance (any browser).</span></span> <span data-ttu-id="a5067-385">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="a5067-385">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a5067-386">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="a5067-386">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="a5067-387">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="a5067-387">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5067-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5067-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a5067-389">Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="a5067-389">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="a5067-390">Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="a5067-390">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="a5067-391">Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.</span><span class="sxs-lookup"><span data-stu-id="a5067-391">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="a5067-392">Otevřete prohlížeč `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="a5067-392">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="a5067-393">Soubor *wwwroot/index.html* se obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="a5067-393">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="a5067-394">Zkopírujte adresu URL z panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="a5067-394">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="a5067-395">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="a5067-395">Open another browser instance (any browser).</span></span> <span data-ttu-id="a5067-396">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="a5067-396">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a5067-397">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="a5067-397">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="a5067-398">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="a5067-398">The unique user name and message are displayed on both pages instantly.</span></span>

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a5067-400">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a5067-400">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
