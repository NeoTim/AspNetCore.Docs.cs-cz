---
title: Použití ASP.NET Core SignalR s TypeScript a webpackem
author: ssougnez
description: V tomto kurzu nakonfigurujete sadu Webpack tak, aby se vytvořila sada ASP.NET Core SignalR webovou aplikaci, jejíž klient je napsaný v TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 9094a1d391c087a6f58aa9dd66e3697a79f4af86
ms.sourcegitcommit: ef1720cb733908f36a54825d84c3461c5280bdbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737513"
---
# <a name="use-aspnet-core-opno-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="24d5a-103">Použití ASP.NET Core SignalR s TypeScript a webpackem</span><span class="sxs-lookup"><span data-stu-id="24d5a-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="24d5a-104">Od [Sébastien Sougnez](https://twitter.com/ssougnez) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="24d5a-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="24d5a-105">[Webpack](https://webpack.js.org/) umožňuje vývojářům seskupit a sestavit prostředky webové aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="24d5a-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="24d5a-106">V tomto kurzu se dozvíte, jak používat Webpack ve ASP.NET Core SignalR webové aplikaci, jejíž klient je napsaný v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="24d5a-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="24d5a-107">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="24d5a-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="24d5a-108">Generování uživatelského rozhraní aplikace SignalR Starter ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24d5a-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="24d5a-109">Konfigurace SignalR klienta TypeScript</span><span class="sxs-lookup"><span data-stu-id="24d5a-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="24d5a-110">Konfigurace kanálu sestavení pomocí webpacku</span><span class="sxs-lookup"><span data-stu-id="24d5a-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="24d5a-111">Konfigurace serveru SignalR</span><span class="sxs-lookup"><span data-stu-id="24d5a-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="24d5a-112">Povolit komunikaci mezi klientem a serverem</span><span class="sxs-lookup"><span data-stu-id="24d5a-112">Enable communication between client and server</span></span>

<span data-ttu-id="24d5a-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="24d5a-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="24d5a-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="24d5a-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="24d5a-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="24d5a-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="24d5a-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="24d5a-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="24d5a-117">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="24d5a-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="24d5a-118">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="24d5a-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="24d5a-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="24d5a-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="24d5a-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="24d5a-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="24d5a-121">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="24d5a-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="24d5a-122">C#pro Visual Studio Code verze 1.17.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="24d5a-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="24d5a-123">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="24d5a-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="24d5a-124">Vytvoření webové aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24d5a-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="24d5a-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="24d5a-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="24d5a-126">Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="24d5a-127">Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři.</span><span class="sxs-lookup"><span data-stu-id="24d5a-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="24d5a-128">Postupujte podle těchto pokynů v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="24d5a-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="24d5a-129">Přejděte na **nástroje** > **Možnosti** > **projekty a řešení** > **Web Správa balíčků** > **externích webových nástrojů**.</span><span class="sxs-lookup"><span data-stu-id="24d5a-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="24d5a-130">Ze seznamu vyberte položku *$ (cesta)* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="24d5a-131">Kliknutím na šipku nahoru tuto položku přesunete do druhé pozice v seznamu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="24d5a-133">Konfigurace sady Visual Studio byla dokončena.</span><span class="sxs-lookup"><span data-stu-id="24d5a-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="24d5a-134">Je čas vytvořit projekt.</span><span class="sxs-lookup"><span data-stu-id="24d5a-134">It's time to create the project.</span></span>

1. <span data-ttu-id="24d5a-135">Použijte možnost **soubor** > **Nový** > možnosti nabídky **projekt** a vyberte šablonu **ASP.NET Core webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="24d5a-136">Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="24d5a-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="24d5a-137">V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 3,0* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="24d5a-138">Vyberte **prázdnou** šablonu a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="24d5a-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="24d5a-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="24d5a-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="24d5a-140">V **integrovaném terminálu**spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="24d5a-140">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="24d5a-141">Prázdná webová aplikace ASP.NET Core, která cílí na .NET Core, se vytvoří v adresáři *SignalRWebPack* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="24d5a-142">Konfigurace sady Webpack a TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="24d5a-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="24d5a-143">Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="24d5a-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="24d5a-144">Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte soubor *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="24d5a-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="24d5a-145">Přidejte zvýrazněnou vlastnost do souboru *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="24d5a-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="24d5a-146">Nastavením vlastnosti `private` na `true` zabráníte zobrazování upozornění při instalaci balíčku v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="24d5a-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="24d5a-147">Nainstalujte požadované balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="24d5a-147">Install the required npm packages.</span></span> <span data-ttu-id="24d5a-148">Z kořenového adresáře projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="24d5a-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="24d5a-149">Některé podrobnosti příkazu si všimněte:</span><span class="sxs-lookup"><span data-stu-id="24d5a-149">Some command details to note:</span></span>

    * <span data-ttu-id="24d5a-150">Číslo verze následuje po znaménku `@` pro každý název balíčku.</span><span class="sxs-lookup"><span data-stu-id="24d5a-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="24d5a-151">NPM nainstaluje tyto konkrétní verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="24d5a-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="24d5a-152">Možnost `-E` zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *Package. JSON*.</span><span class="sxs-lookup"><span data-stu-id="24d5a-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="24d5a-153">Místo `"webpack": "^4.29.3"`se například používá `"webpack": "4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="24d5a-154">Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="24d5a-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="24d5a-155">Další podrobnosti najdete v oficiální dokumentaci k [instalaci npm](https://docs.npmjs.com/cli/install) .</span><span class="sxs-lookup"><span data-stu-id="24d5a-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="24d5a-156">Nahraďte vlastnost `scripts` souboru *Package. JSON* následujícím fragmentem kódu:</span><span class="sxs-lookup"><span data-stu-id="24d5a-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="24d5a-157">Některé vysvětlení skriptů:</span><span class="sxs-lookup"><span data-stu-id="24d5a-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="24d5a-158">`build`: rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="24d5a-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="24d5a-159">Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="24d5a-160">Možnost `mode` zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace.</span><span class="sxs-lookup"><span data-stu-id="24d5a-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="24d5a-161">Používejte `build` jenom při vývoji.</span><span class="sxs-lookup"><span data-stu-id="24d5a-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="24d5a-162">`release`: rozbalí prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="24d5a-163">`publish`: spustí skript `release`, který vyplní prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="24d5a-164">Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="24d5a-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="24d5a-165">V kořenovém adresáři projektu vytvořte soubor s názvem *Webpack. config. js*s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="24d5a-166">Předchozí soubor nakonfiguruje kompilaci sady Webpack.</span><span class="sxs-lookup"><span data-stu-id="24d5a-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="24d5a-167">Některé podrobnosti konfigurace k poznámení:</span><span class="sxs-lookup"><span data-stu-id="24d5a-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="24d5a-168">Vlastnost `output` přepisuje výchozí hodnotu *DIST*.</span><span class="sxs-lookup"><span data-stu-id="24d5a-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="24d5a-169">Místo toho se svazek vydává v adresáři *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="24d5a-170">`resolve.extensions` pole obsahuje *. js* pro import SignalR klientského JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="24d5a-171">V kořenovém adresáři projektu vytvořte nový *zdrojový* adresář.</span><span class="sxs-lookup"><span data-stu-id="24d5a-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="24d5a-172">Jeho účelem je uložit prostředky na straně klienta v projektu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="24d5a-173">Vytvořte *Src/index.html* s následujícím obsahem.</span><span class="sxs-lookup"><span data-stu-id="24d5a-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="24d5a-174">Předchozí kód HTML definuje standardní kód domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="24d5a-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="24d5a-175">Vytvořte nový adresář *Src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="24d5a-176">Jeho účelem je uložit soubory *. CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="24d5a-177">Vytvořte *Src/CSS/Main. CSS* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="24d5a-178">Předchozí *hlavní soubor. CSS* styly aplikace.</span><span class="sxs-lookup"><span data-stu-id="24d5a-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="24d5a-179">Vytvořte *Src/tsconfig. JSON* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="24d5a-180">Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="24d5a-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="24d5a-181">Vytvořte *Src/index. TS* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="24d5a-182">Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="24d5a-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="24d5a-183">`keyup`: Tato událost se aktivuje, když uživatel zadá něco do textového pole identifikovaného jako `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="24d5a-184">Funkce `send` se volá, když uživatel stiskne klávesu **ENTER** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="24d5a-185">`click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="24d5a-186">Je volána funkce `send`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="24d5a-187">Konfigurace aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24d5a-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="24d5a-188">Do metody `Startup.Configure` přidejte volání [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="24d5a-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="24d5a-189">Předchozí kód umožňuje serveru vyhledat a zpracovat soubor *index. html* bez ohledu na to, zda uživatel zadal svou úplnou adresu URL nebo kořenovou adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="24d5a-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="24d5a-190">Na konci metody `Startup.Configure` namapujte */hub* trasu na centrum `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="24d5a-191">Nahraďte kód, který se zobrazí *Hello World!*</span><span class="sxs-lookup"><span data-stu-id="24d5a-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="24d5a-192">s následujícím řádkem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="24d5a-193">V metodě `Startup.ConfigureServices` volejte [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="24d5a-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="24d5a-194">Přidá služby SignalR do projektu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="24d5a-195">V kořenovém adresáři projektu vytvořte nový adresář s názvem *centra*.</span><span class="sxs-lookup"><span data-stu-id="24d5a-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="24d5a-196">Jejím účelem je uložit centrum SignalR, které se vytvoří v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="24d5a-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="24d5a-197">Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="24d5a-198">Do horní části souboru *Startup.cs* přidejte následující kód, který vyřeší odkaz na `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="24d5a-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="24d5a-199">Povolit komunikaci klienta a serveru</span><span class="sxs-lookup"><span data-stu-id="24d5a-199">Enable client and server communication</span></span>

<span data-ttu-id="24d5a-200">Aplikace aktuálně zobrazuje jednoduchý formulář pro posílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="24d5a-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="24d5a-201">Při pokusu o provedení akce nedojde k žádné akci.</span><span class="sxs-lookup"><span data-stu-id="24d5a-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="24d5a-202">Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.</span><span class="sxs-lookup"><span data-stu-id="24d5a-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="24d5a-203">V kořenu projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="24d5a-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="24d5a-204">Předchozí příkaz nainstaluje [klienta TypeScriptSignalR](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="24d5a-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="24d5a-205">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="24d5a-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="24d5a-206">Předchozí kód podporuje příjem zpráv ze serveru.</span><span class="sxs-lookup"><span data-stu-id="24d5a-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="24d5a-207">Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="24d5a-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="24d5a-208">Funkce `withUrl` nakonfiguruje adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="24d5a-208">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="24d5a-209"> umožňuje výměnu zpráv mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="24d5a-209"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="24d5a-210">Každá zpráva má konkrétní název.</span><span class="sxs-lookup"><span data-stu-id="24d5a-210">Each message has a specific name.</span></span> <span data-ttu-id="24d5a-211">Můžete například mít zprávy s názvem `messageReceived`, které spouštějí logiku odpovědnou za zobrazení nové zprávy v zóně zprávy.</span><span class="sxs-lookup"><span data-stu-id="24d5a-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="24d5a-212">Naslouchat konkrétní zprávě se dá provést pomocí funkce `on`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="24d5a-213">Můžete naslouchat libovolnému počtu názvů zpráv.</span><span class="sxs-lookup"><span data-stu-id="24d5a-213">You can listen to any number of message names.</span></span> <span data-ttu-id="24d5a-214">Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy.</span><span class="sxs-lookup"><span data-stu-id="24d5a-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="24d5a-215">Jakmile klient obdrží zprávu, vytvoří se nový prvek `div` s názvem autora a obsahem zprávy ve svém atributu `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="24d5a-216">Je přidáno do hlavního `div` elementu, který zobrazuje zprávy.</span><span class="sxs-lookup"><span data-stu-id="24d5a-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="24d5a-217">Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="24d5a-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="24d5a-218">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="24d5a-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="24d5a-219">Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání metody `send`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="24d5a-220">První parametr metody je název zprávy.</span><span class="sxs-lookup"><span data-stu-id="24d5a-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="24d5a-221">Data zprávy jsou v ostatních parametrech nezvyklá.</span><span class="sxs-lookup"><span data-stu-id="24d5a-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="24d5a-222">V tomto příkladu se do serveru pošle zpráva identifikovaná jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="24d5a-223">Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole.</span><span class="sxs-lookup"><span data-stu-id="24d5a-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="24d5a-224">Pokud funkce Send funguje, hodnota textového pole se nevymaže.</span><span class="sxs-lookup"><span data-stu-id="24d5a-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="24d5a-225">Přidejte zvýrazněnou metodu do třídy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="24d5a-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="24d5a-226">Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží.</span><span class="sxs-lookup"><span data-stu-id="24d5a-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="24d5a-227">Není nutné mít obecnou `on` metodu pro příjem všech zpráv.</span><span class="sxs-lookup"><span data-stu-id="24d5a-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="24d5a-228">Metoda pojmenovaná po názvu zprávy postačuje.</span><span class="sxs-lookup"><span data-stu-id="24d5a-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="24d5a-229">V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="24d5a-230">Metoda C# `NewMessage` očekává data odesílaná klientem.</span><span class="sxs-lookup"><span data-stu-id="24d5a-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="24d5a-231">Na klientech se provede volání metody [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) [. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="24d5a-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="24d5a-232">Přijaté zprávy se odesílají všem klientům připojeným k centru.</span><span class="sxs-lookup"><span data-stu-id="24d5a-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="24d5a-233">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="24d5a-233">Test the app</span></span>

<span data-ttu-id="24d5a-234">Ověřte, že aplikace funguje s následujícím postupem.</span><span class="sxs-lookup"><span data-stu-id="24d5a-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="24d5a-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="24d5a-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="24d5a-236">Spusťte příkaz Webpack v režimu *vydání* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="24d5a-237">Pomocí okna **konzoly Správce balíčků** spusťte v kořenovém adresáři projektu následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="24d5a-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="24d5a-238">Pokud nejste v kořenovém adresáři projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="24d5a-239">Vyberte **ladit** > **Spustit bez ladění** a spusťte aplikaci v prohlížeči bez připojení ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="24d5a-240">Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="24d5a-241">Pokud se zobrazí chyby kompilace, zkuste řešení zavřít a znovu otevřít.</span><span class="sxs-lookup"><span data-stu-id="24d5a-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="24d5a-242">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="24d5a-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="24d5a-243">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="24d5a-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="24d5a-244">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="24d5a-245">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="24d5a-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="24d5a-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="24d5a-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="24d5a-247">Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="24d5a-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="24d5a-248">Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="24d5a-248">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="24d5a-249">Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.</span><span class="sxs-lookup"><span data-stu-id="24d5a-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="24d5a-250">Otevřete prohlížeč a `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="24d5a-251">Soubor *wwwroot/index.html* se obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="24d5a-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="24d5a-252">Zkopírujte adresu URL z panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="24d5a-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="24d5a-253">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="24d5a-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="24d5a-254">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="24d5a-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="24d5a-255">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="24d5a-256">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="24d5a-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="24d5a-258">Požadavky</span><span class="sxs-lookup"><span data-stu-id="24d5a-258">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="24d5a-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="24d5a-259">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="24d5a-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="24d5a-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="24d5a-261">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="24d5a-261">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="24d5a-262">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="24d5a-262">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="24d5a-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="24d5a-263">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="24d5a-264">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="24d5a-264">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="24d5a-265">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="24d5a-265">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="24d5a-266">C#pro Visual Studio Code verze 1.17.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="24d5a-266">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="24d5a-267">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="24d5a-267">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="24d5a-268">Vytvoření webové aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24d5a-268">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="24d5a-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="24d5a-269">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="24d5a-270">Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-270">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="24d5a-271">Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři.</span><span class="sxs-lookup"><span data-stu-id="24d5a-271">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="24d5a-272">Postupujte podle těchto pokynů v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="24d5a-272">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="24d5a-273">Přejděte na **nástroje** > **Možnosti** > **projekty a řešení** > **Web Správa balíčků** > **externích webových nástrojů**.</span><span class="sxs-lookup"><span data-stu-id="24d5a-273">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="24d5a-274">Ze seznamu vyberte položku *$ (cesta)* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-274">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="24d5a-275">Kliknutím na šipku nahoru tuto položku přesunete do druhé pozice v seznamu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-275">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="24d5a-277">Konfigurace sady Visual Studio byla dokončena.</span><span class="sxs-lookup"><span data-stu-id="24d5a-277">Visual Studio configuration is completed.</span></span> <span data-ttu-id="24d5a-278">Je čas vytvořit projekt.</span><span class="sxs-lookup"><span data-stu-id="24d5a-278">It's time to create the project.</span></span>

1. <span data-ttu-id="24d5a-279">Použijte možnost **soubor** > **Nový** > možnosti nabídky **projekt** a vyberte šablonu **ASP.NET Core webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-279">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="24d5a-280">Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="24d5a-280">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="24d5a-281">V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 2,2* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-281">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="24d5a-282">Vyberte **prázdnou** šablonu a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="24d5a-282">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="24d5a-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="24d5a-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="24d5a-284">V **integrovaném terminálu**spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="24d5a-284">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="24d5a-285">Prázdná webová aplikace ASP.NET Core, která cílí na .NET Core, se vytvoří v adresáři *SignalRWebPack* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-285">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="24d5a-286">Konfigurace sady Webpack a TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="24d5a-286">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="24d5a-287">Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="24d5a-287">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="24d5a-288">Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte soubor *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="24d5a-288">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="24d5a-289">Přidejte zvýrazněnou vlastnost do souboru *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="24d5a-289">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="24d5a-290">Nastavením vlastnosti `private` na `true` zabráníte zobrazování upozornění při instalaci balíčku v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="24d5a-290">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="24d5a-291">Nainstalujte požadované balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="24d5a-291">Install the required npm packages.</span></span> <span data-ttu-id="24d5a-292">Z kořenového adresáře projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="24d5a-292">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="24d5a-293">Některé podrobnosti příkazu si všimněte:</span><span class="sxs-lookup"><span data-stu-id="24d5a-293">Some command details to note:</span></span>

    * <span data-ttu-id="24d5a-294">Číslo verze následuje po znaménku `@` pro každý název balíčku.</span><span class="sxs-lookup"><span data-stu-id="24d5a-294">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="24d5a-295">NPM nainstaluje tyto konkrétní verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="24d5a-295">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="24d5a-296">Možnost `-E` zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *Package. JSON*.</span><span class="sxs-lookup"><span data-stu-id="24d5a-296">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="24d5a-297">Místo `"webpack": "^4.29.3"`se například používá `"webpack": "4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-297">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="24d5a-298">Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="24d5a-298">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="24d5a-299">Další podrobnosti najdete v oficiální dokumentaci k [instalaci npm](https://docs.npmjs.com/cli/install) .</span><span class="sxs-lookup"><span data-stu-id="24d5a-299">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="24d5a-300">Nahraďte vlastnost `scripts` souboru *Package. JSON* následujícím fragmentem kódu:</span><span class="sxs-lookup"><span data-stu-id="24d5a-300">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="24d5a-301">Některé vysvětlení skriptů:</span><span class="sxs-lookup"><span data-stu-id="24d5a-301">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="24d5a-302">`build`: rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="24d5a-302">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="24d5a-303">Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-303">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="24d5a-304">Možnost `mode` zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace.</span><span class="sxs-lookup"><span data-stu-id="24d5a-304">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="24d5a-305">Používejte `build` jenom při vývoji.</span><span class="sxs-lookup"><span data-stu-id="24d5a-305">Only use `build` in development.</span></span>
    * <span data-ttu-id="24d5a-306">`release`: rozbalí prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-306">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="24d5a-307">`publish`: spustí skript `release`, který vyplní prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-307">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="24d5a-308">Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="24d5a-308">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="24d5a-309">V kořenovém adresáři projektu vytvořte soubor s názvem *Webpack. config. js*s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-309">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="24d5a-310">Předchozí soubor nakonfiguruje kompilaci sady Webpack.</span><span class="sxs-lookup"><span data-stu-id="24d5a-310">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="24d5a-311">Některé podrobnosti konfigurace k poznámení:</span><span class="sxs-lookup"><span data-stu-id="24d5a-311">Some configuration details to note:</span></span>

    * <span data-ttu-id="24d5a-312">Vlastnost `output` přepisuje výchozí hodnotu *DIST*.</span><span class="sxs-lookup"><span data-stu-id="24d5a-312">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="24d5a-313">Místo toho se svazek vydává v adresáři *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-313">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="24d5a-314">`resolve.extensions` pole obsahuje *. js* pro import SignalR klientského JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-314">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="24d5a-315">V kořenovém adresáři projektu vytvořte nový *zdrojový* adresář.</span><span class="sxs-lookup"><span data-stu-id="24d5a-315">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="24d5a-316">Jeho účelem je uložit prostředky na straně klienta v projektu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-316">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="24d5a-317">Vytvořte *Src/index.html* s následujícím obsahem.</span><span class="sxs-lookup"><span data-stu-id="24d5a-317">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="24d5a-318">Předchozí kód HTML definuje standardní kód domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="24d5a-318">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="24d5a-319">Vytvořte nový adresář *Src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-319">Create a new *src/css* directory.</span></span> <span data-ttu-id="24d5a-320">Jeho účelem je uložit soubory *. CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-320">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="24d5a-321">Vytvořte *Src/CSS/Main. CSS* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-321">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="24d5a-322">Předchozí *hlavní soubor. CSS* styly aplikace.</span><span class="sxs-lookup"><span data-stu-id="24d5a-322">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="24d5a-323">Vytvořte *Src/tsconfig. JSON* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-323">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="24d5a-324">Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="24d5a-324">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="24d5a-325">Vytvořte *Src/index. TS* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-325">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="24d5a-326">Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="24d5a-326">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="24d5a-327">`keyup`: Tato událost se aktivuje, když uživatel zadá něco do textového pole identifikovaného jako `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-327">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="24d5a-328">Funkce `send` se volá, když uživatel stiskne klávesu **ENTER** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-328">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="24d5a-329">`click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-329">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="24d5a-330">Je volána funkce `send`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-330">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="24d5a-331">Konfigurace aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24d5a-331">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="24d5a-332">Kód, který je uveden v metodě `Startup.Configure`, zobrazí *Hello World!* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-332">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="24d5a-333">Nahraďte volání metody `app.Run` voláními [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="24d5a-333">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="24d5a-334">Předchozí kód umožňuje serveru vyhledat a zpracovat soubor *index. html* bez ohledu na to, zda uživatel zadal svou úplnou adresu URL nebo kořenovou adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="24d5a-334">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="24d5a-335">Zavolejte [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) v metodě `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-335">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="24d5a-336">Přidá služby SignalR do projektu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-336">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="24d5a-337">Namapujte */hub* trasu na centrum `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-337">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="24d5a-338">Na konec `Startup.Configure` metody přidejte následující řádky:</span><span class="sxs-lookup"><span data-stu-id="24d5a-338">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="24d5a-339">V kořenovém adresáři projektu vytvořte nový adresář s názvem *centra*.</span><span class="sxs-lookup"><span data-stu-id="24d5a-339">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="24d5a-340">Jejím účelem je uložit centrum SignalR, které se vytvoří v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="24d5a-340">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="24d5a-341">Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="24d5a-341">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="24d5a-342">Do horní části souboru *Startup.cs* přidejte následující kód, který vyřeší odkaz na `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="24d5a-342">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="24d5a-343">Povolit komunikaci klienta a serveru</span><span class="sxs-lookup"><span data-stu-id="24d5a-343">Enable client and server communication</span></span>

<span data-ttu-id="24d5a-344">Aplikace aktuálně zobrazuje jednoduchý formulář pro posílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="24d5a-344">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="24d5a-345">Při pokusu o provedení akce nedojde k žádné akci.</span><span class="sxs-lookup"><span data-stu-id="24d5a-345">Nothing happens when you try to do so.</span></span> <span data-ttu-id="24d5a-346">Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.</span><span class="sxs-lookup"><span data-stu-id="24d5a-346">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="24d5a-347">V kořenu projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="24d5a-347">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="24d5a-348">Předchozí příkaz nainstaluje [klienta TypeScriptSignalR](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="24d5a-348">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="24d5a-349">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="24d5a-349">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="24d5a-350">Předchozí kód podporuje příjem zpráv ze serveru.</span><span class="sxs-lookup"><span data-stu-id="24d5a-350">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="24d5a-351">Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="24d5a-351">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="24d5a-352">Funkce `withUrl` nakonfiguruje adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="24d5a-352">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="24d5a-353"> umožňuje výměnu zpráv mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="24d5a-353"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="24d5a-354">Každá zpráva má konkrétní název.</span><span class="sxs-lookup"><span data-stu-id="24d5a-354">Each message has a specific name.</span></span> <span data-ttu-id="24d5a-355">Můžete například mít zprávy s názvem `messageReceived`, které spouštějí logiku odpovědnou za zobrazení nové zprávy v zóně zprávy.</span><span class="sxs-lookup"><span data-stu-id="24d5a-355">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="24d5a-356">Naslouchat konkrétní zprávě se dá provést pomocí funkce `on`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-356">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="24d5a-357">Můžete naslouchat libovolnému počtu názvů zpráv.</span><span class="sxs-lookup"><span data-stu-id="24d5a-357">You can listen to any number of message names.</span></span> <span data-ttu-id="24d5a-358">Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy.</span><span class="sxs-lookup"><span data-stu-id="24d5a-358">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="24d5a-359">Jakmile klient obdrží zprávu, vytvoří se nový prvek `div` s názvem autora a obsahem zprávy ve svém atributu `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-359">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="24d5a-360">Je přidáno do hlavního `div` elementu, který zobrazuje zprávy.</span><span class="sxs-lookup"><span data-stu-id="24d5a-360">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="24d5a-361">Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="24d5a-361">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="24d5a-362">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="24d5a-362">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="24d5a-363">Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání metody `send`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-363">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="24d5a-364">První parametr metody je název zprávy.</span><span class="sxs-lookup"><span data-stu-id="24d5a-364">The method's first parameter is the message name.</span></span> <span data-ttu-id="24d5a-365">Data zprávy jsou v ostatních parametrech nezvyklá.</span><span class="sxs-lookup"><span data-stu-id="24d5a-365">The message data inhabits the other parameters.</span></span> <span data-ttu-id="24d5a-366">V tomto příkladu se do serveru pošle zpráva identifikovaná jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-366">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="24d5a-367">Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole.</span><span class="sxs-lookup"><span data-stu-id="24d5a-367">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="24d5a-368">Pokud funkce Send funguje, hodnota textového pole se nevymaže.</span><span class="sxs-lookup"><span data-stu-id="24d5a-368">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="24d5a-369">Přidejte zvýrazněnou metodu do třídy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="24d5a-369">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="24d5a-370">Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží.</span><span class="sxs-lookup"><span data-stu-id="24d5a-370">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="24d5a-371">Není nutné mít obecnou `on` metodu pro příjem všech zpráv.</span><span class="sxs-lookup"><span data-stu-id="24d5a-371">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="24d5a-372">Metoda pojmenovaná po názvu zprávy postačuje.</span><span class="sxs-lookup"><span data-stu-id="24d5a-372">A method named after the message name suffices.</span></span>

    <span data-ttu-id="24d5a-373">V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-373">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="24d5a-374">Metoda C# `NewMessage` očekává data odesílaná klientem.</span><span class="sxs-lookup"><span data-stu-id="24d5a-374">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="24d5a-375">Na klientech se provede volání metody [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) [. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="24d5a-375">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="24d5a-376">Přijaté zprávy se odesílají všem klientům připojeným k centru.</span><span class="sxs-lookup"><span data-stu-id="24d5a-376">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="24d5a-377">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="24d5a-377">Test the app</span></span>

<span data-ttu-id="24d5a-378">Ověřte, že aplikace funguje s následujícím postupem.</span><span class="sxs-lookup"><span data-stu-id="24d5a-378">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="24d5a-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="24d5a-379">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="24d5a-380">Spusťte příkaz Webpack v režimu *vydání* .</span><span class="sxs-lookup"><span data-stu-id="24d5a-380">Run Webpack in *release* mode.</span></span> <span data-ttu-id="24d5a-381">Pomocí okna **konzoly Správce balíčků** spusťte v kořenovém adresáři projektu následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="24d5a-381">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="24d5a-382">Pokud nejste v kořenovém adresáři projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-382">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="24d5a-383">Vyberte **ladit** > **Spustit bez ladění** a spusťte aplikaci v prohlížeči bez připojení ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="24d5a-383">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="24d5a-384">Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-384">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="24d5a-385">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="24d5a-385">Open another browser instance (any browser).</span></span> <span data-ttu-id="24d5a-386">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="24d5a-386">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="24d5a-387">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-387">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="24d5a-388">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="24d5a-388">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="24d5a-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="24d5a-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="24d5a-390">Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="24d5a-390">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="24d5a-391">Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="24d5a-391">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="24d5a-392">Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.</span><span class="sxs-lookup"><span data-stu-id="24d5a-392">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="24d5a-393">Otevřete prohlížeč a `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="24d5a-393">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="24d5a-394">Soubor *wwwroot/index.html* se obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="24d5a-394">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="24d5a-395">Zkopírujte adresu URL z panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="24d5a-395">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="24d5a-396">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="24d5a-396">Open another browser instance (any browser).</span></span> <span data-ttu-id="24d5a-397">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="24d5a-397">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="24d5a-398">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="24d5a-398">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="24d5a-399">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="24d5a-399">The unique user name and message are displayed on both pages instantly.</span></span>

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="24d5a-401">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="24d5a-401">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
