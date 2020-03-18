---
title: Použití ASP.NET Core SignalR s TypeScript a webpackem
author: ssougnez
description: V tomto kurzu nakonfigurujete sadu Webpack tak, aby se vytvořila sada ASP.NET Core SignalR webovou aplikaci, jejíž klient je napsaný v TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511337"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="c1199-103">Použití signalizace ASP.NET Core s TypeScript a webpackem</span><span class="sxs-lookup"><span data-stu-id="c1199-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="c1199-104">Od [Sébastien Sougnez](https://twitter.com/ssougnez) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="c1199-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="c1199-105">[Webpack](https://webpack.js.org/) umožňuje vývojářům seskupit a sestavit prostředky webové aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c1199-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="c1199-106">V tomto kurzu se dozvíte, jak používat Webpack ve webové aplikaci ASP.NET Coreového signálu, jejíž klient je napsaný v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="c1199-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="c1199-107">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="c1199-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c1199-108">Generování uživatelského rozhraní pro aplikaci signalizace úvodní ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1199-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="c1199-109">Konfigurace klienta TypeScript nástroje Signal</span><span class="sxs-lookup"><span data-stu-id="c1199-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="c1199-110">Konfigurace kanálu sestavení pomocí webpacku</span><span class="sxs-lookup"><span data-stu-id="c1199-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="c1199-111">Konfigurace serveru signálu</span><span class="sxs-lookup"><span data-stu-id="c1199-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="c1199-112">Povolit komunikaci mezi klientem a serverem</span><span class="sxs-lookup"><span data-stu-id="c1199-112">Enable communication between client and server</span></span>

<span data-ttu-id="c1199-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1199-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="c1199-114">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="c1199-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1199-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1199-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1199-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="c1199-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="c1199-117">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c1199-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="c1199-118">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c1199-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1199-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1199-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="c1199-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1199-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="c1199-121">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c1199-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c1199-122">C#pro Visual Studio Code verze 1.17.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c1199-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="c1199-123">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c1199-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="c1199-124">Vytvoření webové aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1199-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1199-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1199-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1199-126">Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* .</span><span class="sxs-lookup"><span data-stu-id="c1199-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="c1199-127">Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři.</span><span class="sxs-lookup"><span data-stu-id="c1199-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="c1199-128">Postupujte podle těchto pokynů v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c1199-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="c1199-129">Spusťte sadu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c1199-129">Launch Visual Studio.</span></span> <span data-ttu-id="c1199-130">V okně Start vyberte **pokračovat bez kódu**.</span><span class="sxs-lookup"><span data-stu-id="c1199-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="c1199-131">Přejděte na **nástroje** > **Možnosti** > **projekty a řešení** > **Web Správa balíčků** > **externích webových nástrojů**.</span><span class="sxs-lookup"><span data-stu-id="c1199-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="c1199-132">Ze seznamu vyberte položku *$ (cesta)* .</span><span class="sxs-lookup"><span data-stu-id="c1199-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="c1199-133">Kliknutím na šipku nahoru přesuňte položku do druhé pozice v seznamu a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="c1199-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="c1199-135">Konfigurace sady Visual Studio byla dokončena.</span><span class="sxs-lookup"><span data-stu-id="c1199-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="c1199-136">Použijte možnost **soubor** > **Nový** > možnosti nabídky **projekt** a vyberte šablonu **ASP.NET Core webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="c1199-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="c1199-137">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="c1199-137">Select **Next**.</span></span>
1. <span data-ttu-id="c1199-138">Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c1199-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="c1199-139">V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 3,1* .</span><span class="sxs-lookup"><span data-stu-id="c1199-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="c1199-140">Vyberte **prázdnou** šablonu a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c1199-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="c1199-141">Přidejte do projektu balíček `Microsoft.TypeScript.MSBuild`:</span><span class="sxs-lookup"><span data-stu-id="c1199-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="c1199-142">V **Průzkumník řešení** (pravé podokno) klikněte pravým tlačítkem myši na uzel projektu a vyberte možnost **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="c1199-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="c1199-143">Na kartě **Procházet** vyhledejte `Microsoft.TypeScript.MSBuild`a kliknutím na **nainstalovat** napravo nainstalujte balíček.</span><span class="sxs-lookup"><span data-stu-id="c1199-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="c1199-144">Sada Visual Studio přidá balíček NuGet pod uzel **závislosti** v **Průzkumník řešení**a povolí v projektu kompilaci TypeScript.</span><span class="sxs-lookup"><span data-stu-id="c1199-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1199-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1199-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1199-146">V **integrovaném terminálu**spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1199-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="c1199-147">Příkaz `dotnet new` vytvoří prázdnou ASP.NET Core webovou aplikaci v adresáři *SignalRWebPack* .</span><span class="sxs-lookup"><span data-stu-id="c1199-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="c1199-148">Příkaz `code` otevře složku *SignalRWebPack* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c1199-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="c1199-149">V **integrovaném terminálu**spusťte následující příkaz .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="c1199-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="c1199-150">Předchozí příkaz přidá balíček [Microsoft. TypeScript. MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) , který umožňuje kompilaci TypeScript v projektu.</span><span class="sxs-lookup"><span data-stu-id="c1199-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="c1199-151">Konfigurace sady Webpack a TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="c1199-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="c1199-152">Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c1199-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="c1199-153">Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte soubor *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="c1199-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="c1199-154">Přidejte zvýrazněnou vlastnost do souboru *Package. JSON* a uložte změny souborů:</span><span class="sxs-lookup"><span data-stu-id="c1199-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="c1199-155">Nastavením vlastnosti `private` na `true` zabráníte zobrazování upozornění při instalaci balíčku v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="c1199-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="c1199-156">Nainstalujte požadované balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="c1199-156">Install the required npm packages.</span></span> <span data-ttu-id="c1199-157">Z kořenového adresáře projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1199-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="c1199-158">Některé podrobnosti příkazu si všimněte:</span><span class="sxs-lookup"><span data-stu-id="c1199-158">Some command details to note:</span></span>

    * <span data-ttu-id="c1199-159">Číslo verze následuje po znaménku `@` pro každý název balíčku.</span><span class="sxs-lookup"><span data-stu-id="c1199-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="c1199-160">NPM nainstaluje tyto konkrétní verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="c1199-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="c1199-161">Možnost `-E` zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *Package. JSON*.</span><span class="sxs-lookup"><span data-stu-id="c1199-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="c1199-162">Místo `"webpack": "^4.41.5"`se například používá `"webpack": "4.41.5"`.</span><span class="sxs-lookup"><span data-stu-id="c1199-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="c1199-163">Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="c1199-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="c1199-164">Další podrobnosti najdete v tématu [npm-Install](https://docs.npmjs.com/cli/install) docs.</span><span class="sxs-lookup"><span data-stu-id="c1199-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="c1199-165">Vlastnost `scripts` souboru *Package. JSON* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1199-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="c1199-166">Některé vysvětlení skriptů:</span><span class="sxs-lookup"><span data-stu-id="c1199-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="c1199-167">`build`: rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="c1199-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="c1199-168">Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="c1199-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="c1199-169">Možnost `mode` zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace.</span><span class="sxs-lookup"><span data-stu-id="c1199-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="c1199-170">Používejte `build` jenom při vývoji.</span><span class="sxs-lookup"><span data-stu-id="c1199-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="c1199-171">`release`: rozbalí prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="c1199-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="c1199-172">`publish`: spustí skript `release`, který vyplní prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="c1199-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="c1199-173">Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1199-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="c1199-174">V kořenovém adresáři projektu vytvořte soubor s názvem *Webpack. config. js*s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1199-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="c1199-175">Předchozí soubor nakonfiguruje kompilaci sady Webpack.</span><span class="sxs-lookup"><span data-stu-id="c1199-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="c1199-176">Některé podrobnosti konfigurace k poznámení:</span><span class="sxs-lookup"><span data-stu-id="c1199-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="c1199-177">Vlastnost `output` přepisuje výchozí hodnotu *DIST*.</span><span class="sxs-lookup"><span data-stu-id="c1199-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="c1199-178">Místo toho se svazek vydává v adresáři *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="c1199-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="c1199-179">`resolve.extensions` pole obsahuje *. js* pro Import klientského JavaScriptu pro signalizaci.</span><span class="sxs-lookup"><span data-stu-id="c1199-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="c1199-180">Vytvořte nový *zdrojový* adresář v kořenovém adresáři projektu pro uložení prostředků na straně klienta v projektu.</span><span class="sxs-lookup"><span data-stu-id="c1199-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="c1199-181">Vytvořte *Src/index.html* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="c1199-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="c1199-182">Předchozí kód HTML definuje standardní kód domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="c1199-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="c1199-183">Vytvořte nový adresář *Src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="c1199-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="c1199-184">Jeho účelem je uložit soubory *. CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="c1199-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="c1199-185">Vytvořte *Src/CSS/Main. CSS* s následujícími šablonami stylů CSS:</span><span class="sxs-lookup"><span data-stu-id="c1199-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="c1199-186">Předchozí *hlavní soubor. CSS* styly aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1199-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="c1199-187">Vytvořte *Src/tsconfig. JSON* s následujícím JSON:</span><span class="sxs-lookup"><span data-stu-id="c1199-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="c1199-188">Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="c1199-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="c1199-189">Vytvořte *Src/index. TS* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1199-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="c1199-190">Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="c1199-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="c1199-191">`keyup`: Tato událost se aktivuje, když uživatel zadá do textového pole `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="c1199-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="c1199-192">Funkce `send` se volá, když uživatel stiskne klávesu **ENTER** .</span><span class="sxs-lookup"><span data-stu-id="c1199-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="c1199-193">`click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="c1199-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="c1199-194">Je volána funkce `send`.</span><span class="sxs-lookup"><span data-stu-id="c1199-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="c1199-195">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="c1199-195">Configure the app</span></span>

1. <span data-ttu-id="c1199-196">V `Startup.Configure`přidejte volání [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="c1199-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="c1199-197">Předchozí kód umožňuje serveru vyhledat a zpracovat soubor *index. html* .</span><span class="sxs-lookup"><span data-stu-id="c1199-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="c1199-198">Soubor se obsluhuje, jestli uživatel zadá svoji úplnou adresu URL nebo kořenovou adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1199-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="c1199-199">Na konci `Startup.Configure`namapujte */hub* trasu na centrum `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="c1199-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="c1199-200">Nahraďte kód, který se zobrazí *Hello World!*</span><span class="sxs-lookup"><span data-stu-id="c1199-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="c1199-201">s následujícím řádkem:</span><span class="sxs-lookup"><span data-stu-id="c1199-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="c1199-202">V `Startup.ConfigureServices`volejte [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="c1199-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="c1199-203">Vytvořte nový adresář *s názvem hub* v kořenovém adresáři projektu *SignalRWebPack/* pro uložení centra signalizace.</span><span class="sxs-lookup"><span data-stu-id="c1199-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="c1199-204">Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1199-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="c1199-205">Do horní části souboru *Startup.cs* přidejte následující příkaz `using`, který vyřeší `ChatHub` odkaz:</span><span class="sxs-lookup"><span data-stu-id="c1199-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="c1199-206">Povolit komunikaci klienta a serveru</span><span class="sxs-lookup"><span data-stu-id="c1199-206">Enable client and server communication</span></span>

<span data-ttu-id="c1199-207">Aplikace aktuálně zobrazuje základní formulář pro posílání zpráv, ale ještě není funkční.</span><span class="sxs-lookup"><span data-stu-id="c1199-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="c1199-208">Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.</span><span class="sxs-lookup"><span data-stu-id="c1199-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="c1199-209">Spusťte následující příkaz v kořenu projektu:</span><span class="sxs-lookup"><span data-stu-id="c1199-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="c1199-210">Předchozí příkaz nainstaluje:</span><span class="sxs-lookup"><span data-stu-id="c1199-210">The preceding command installs:</span></span>

     * <span data-ttu-id="c1199-211">[Klient TypeScript Signal](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="c1199-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="c1199-212">Definice typu TypeScript pro Node. js, které umožňují kontrolu typů Node. js v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="c1199-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="c1199-213">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="c1199-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="c1199-214">Předchozí kód podporuje příjem zpráv ze serveru.</span><span class="sxs-lookup"><span data-stu-id="c1199-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="c1199-215">Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="c1199-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="c1199-216">Funkce `withUrl` nakonfiguruje adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="c1199-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="c1199-217">Signalizace umožňuje výměnu zpráv mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="c1199-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="c1199-218">Každá zpráva má konkrétní název.</span><span class="sxs-lookup"><span data-stu-id="c1199-218">Each message has a specific name.</span></span> <span data-ttu-id="c1199-219">Například zprávy s názvem `messageReceived` mohou spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy.</span><span class="sxs-lookup"><span data-stu-id="c1199-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="c1199-220">Naslouchat konkrétní zprávě se dá provést pomocí funkce `on`.</span><span class="sxs-lookup"><span data-stu-id="c1199-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="c1199-221">Na je možné naslouchat libovolný počet názvů zpráv.</span><span class="sxs-lookup"><span data-stu-id="c1199-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="c1199-222">Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy.</span><span class="sxs-lookup"><span data-stu-id="c1199-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="c1199-223">Jakmile klient obdrží zprávu, vytvoří se nový prvek `div` s názvem autora a obsahem zprávy ve svém atributu `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="c1199-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="c1199-224">Je přidáno do hlavního `div` elementu, který zobrazuje zprávy.</span><span class="sxs-lookup"><span data-stu-id="c1199-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="c1199-225">Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="c1199-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="c1199-226">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="c1199-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="c1199-227">Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání metody `send`.</span><span class="sxs-lookup"><span data-stu-id="c1199-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="c1199-228">První parametr metody je název zprávy.</span><span class="sxs-lookup"><span data-stu-id="c1199-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="c1199-229">Data zprávy jsou v ostatních parametrech nezvyklá.</span><span class="sxs-lookup"><span data-stu-id="c1199-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="c1199-230">V tomto příkladu se do serveru pošle zpráva identifikovaná jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="c1199-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="c1199-231">Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole.</span><span class="sxs-lookup"><span data-stu-id="c1199-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="c1199-232">Pokud funkce Send funguje, hodnota textového pole se nevymaže.</span><span class="sxs-lookup"><span data-stu-id="c1199-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="c1199-233">Do třídy `NewMessage` přidejte metodu `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="c1199-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="c1199-234">Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží.</span><span class="sxs-lookup"><span data-stu-id="c1199-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="c1199-235">Není nutné mít obecnou `on` metodu pro příjem všech zpráv.</span><span class="sxs-lookup"><span data-stu-id="c1199-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="c1199-236">Metoda pojmenovaná po názvu zprávy postačuje.</span><span class="sxs-lookup"><span data-stu-id="c1199-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="c1199-237">V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="c1199-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="c1199-238">Metoda C# `NewMessage` očekává data odesílaná klientem.</span><span class="sxs-lookup"><span data-stu-id="c1199-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="c1199-239">Bylo provedeno volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientech. vše](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="c1199-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="c1199-240">Přijaté zprávy se odesílají všem klientům připojeným k centru.</span><span class="sxs-lookup"><span data-stu-id="c1199-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="c1199-241">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="c1199-241">Test the app</span></span>

<span data-ttu-id="c1199-242">Ověřte, že aplikace funguje s následujícím postupem.</span><span class="sxs-lookup"><span data-stu-id="c1199-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1199-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1199-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c1199-244">Spusťte příkaz Webpack v režimu *vydání* .</span><span class="sxs-lookup"><span data-stu-id="c1199-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="c1199-245">Pomocí okna **konzoly Správce balíčků** spusťte následující příkaz v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="c1199-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="c1199-246">Pokud nejste v kořenovém adresáři projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="c1199-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c1199-247">Vyberte **ladit** > **Spustit bez ladění** a spusťte aplikaci v prohlížeči bez připojení ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="c1199-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="c1199-248">Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="c1199-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="c1199-249">Pokud se zobrazí chyby kompilace, zkuste řešení zavřít a znovu otevřít.</span><span class="sxs-lookup"><span data-stu-id="c1199-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="c1199-250">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="c1199-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="c1199-251">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c1199-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c1199-252">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="c1199-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c1199-253">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="c1199-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1199-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1199-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c1199-255">Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="c1199-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c1199-256">Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="c1199-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="c1199-257">Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.</span><span class="sxs-lookup"><span data-stu-id="c1199-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="c1199-258">Otevřete prohlížeč a `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="c1199-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="c1199-259">Soubor *wwwroot/index.html* se obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="c1199-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="c1199-260">Zkopírujte adresu URL z panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c1199-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="c1199-261">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="c1199-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="c1199-262">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c1199-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c1199-263">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="c1199-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c1199-264">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="c1199-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="c1199-266">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="c1199-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1199-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1199-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1199-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="c1199-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="c1199-269">.NET Core SDK 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c1199-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="c1199-270">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c1199-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1199-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1199-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="c1199-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1199-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="c1199-273">.NET Core SDK 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c1199-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c1199-274">C#pro Visual Studio Code verze 1.17.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c1199-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="c1199-275">[Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c1199-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="c1199-276">Vytvoření webové aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1199-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1199-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1199-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1199-278">Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* .</span><span class="sxs-lookup"><span data-stu-id="c1199-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="c1199-279">Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři.</span><span class="sxs-lookup"><span data-stu-id="c1199-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="c1199-280">Postupujte podle těchto pokynů v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c1199-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="c1199-281">Přejděte na **nástroje** > **Možnosti** > **projekty a řešení** > **Web Správa balíčků** > **externích webových nástrojů**.</span><span class="sxs-lookup"><span data-stu-id="c1199-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="c1199-282">Ze seznamu vyberte položku *$ (cesta)* .</span><span class="sxs-lookup"><span data-stu-id="c1199-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="c1199-283">Kliknutím na šipku nahoru tuto položku přesunete do druhé pozice v seznamu.</span><span class="sxs-lookup"><span data-stu-id="c1199-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="c1199-285">Konfigurace sady Visual Studio byla dokončena.</span><span class="sxs-lookup"><span data-stu-id="c1199-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="c1199-286">Je čas vytvořit projekt.</span><span class="sxs-lookup"><span data-stu-id="c1199-286">It's time to create the project.</span></span>

1. <span data-ttu-id="c1199-287">Použijte možnost **soubor** > **Nový** > možnosti nabídky **projekt** a vyberte šablonu **ASP.NET Core webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="c1199-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="c1199-288">Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c1199-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="c1199-289">V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 2,2* .</span><span class="sxs-lookup"><span data-stu-id="c1199-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="c1199-290">Vyberte **prázdnou** šablonu a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c1199-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1199-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1199-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1199-292">V **integrovaném terminálu**spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1199-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="c1199-293">Prázdná webová aplikace ASP.NET Core, která cílí na .NET Core, se vytvoří v adresáři *SignalRWebPack* .</span><span class="sxs-lookup"><span data-stu-id="c1199-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="c1199-294">Konfigurace sady Webpack a TypeScriptu</span><span class="sxs-lookup"><span data-stu-id="c1199-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="c1199-295">Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c1199-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="c1199-296">Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte soubor *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="c1199-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="c1199-297">Přidejte zvýrazněnou vlastnost do souboru *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="c1199-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="c1199-298">Nastavením vlastnosti `private` na `true` zabráníte zobrazování upozornění při instalaci balíčku v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="c1199-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="c1199-299">Nainstalujte požadované balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="c1199-299">Install the required npm packages.</span></span> <span data-ttu-id="c1199-300">Z kořenového adresáře projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1199-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="c1199-301">Některé podrobnosti příkazu si všimněte:</span><span class="sxs-lookup"><span data-stu-id="c1199-301">Some command details to note:</span></span>

    * <span data-ttu-id="c1199-302">Číslo verze následuje po znaménku `@` pro každý název balíčku.</span><span class="sxs-lookup"><span data-stu-id="c1199-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="c1199-303">NPM nainstaluje tyto konkrétní verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="c1199-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="c1199-304">Možnost `-E` zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *Package. JSON*.</span><span class="sxs-lookup"><span data-stu-id="c1199-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="c1199-305">Místo `"webpack": "^4.29.3"`se například používá `"webpack": "4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="c1199-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="c1199-306">Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="c1199-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="c1199-307">Další podrobnosti najdete v tématu [npm-Install](https://docs.npmjs.com/cli/install) docs.</span><span class="sxs-lookup"><span data-stu-id="c1199-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="c1199-308">Vlastnost `scripts` souboru *Package. JSON* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1199-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="c1199-309">Některé vysvětlení skriptů:</span><span class="sxs-lookup"><span data-stu-id="c1199-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="c1199-310">`build`: rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="c1199-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="c1199-311">Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="c1199-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="c1199-312">Možnost `mode` zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace.</span><span class="sxs-lookup"><span data-stu-id="c1199-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="c1199-313">Používejte `build` jenom při vývoji.</span><span class="sxs-lookup"><span data-stu-id="c1199-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="c1199-314">`release`: rozbalí prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="c1199-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="c1199-315">`publish`: spustí skript `release`, který vyplní prostředky na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="c1199-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="c1199-316">Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1199-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="c1199-317">V kořenovém adresáři projektu vytvořte soubor s názvem *Webpack. config. js* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1199-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="c1199-318">Předchozí soubor nakonfiguruje kompilaci sady Webpack.</span><span class="sxs-lookup"><span data-stu-id="c1199-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="c1199-319">Některé podrobnosti konfigurace k poznámení:</span><span class="sxs-lookup"><span data-stu-id="c1199-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="c1199-320">Vlastnost `output` přepisuje výchozí hodnotu *DIST*.</span><span class="sxs-lookup"><span data-stu-id="c1199-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="c1199-321">Místo toho se svazek vydává v adresáři *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="c1199-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="c1199-322">`resolve.extensions` pole obsahuje *. js* pro Import klientského JavaScriptu pro signalizaci.</span><span class="sxs-lookup"><span data-stu-id="c1199-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="c1199-323">Vytvořte nový *zdrojový* adresář v kořenovém adresáři projektu pro uložení prostředků na straně klienta v projektu.</span><span class="sxs-lookup"><span data-stu-id="c1199-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="c1199-324">Vytvořte *Src/index.html* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="c1199-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="c1199-325">Předchozí kód HTML definuje standardní kód domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="c1199-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="c1199-326">Vytvořte nový adresář *Src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="c1199-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="c1199-327">Jeho účelem je uložit soubory *. CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="c1199-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="c1199-328">Vytvořte *Src/CSS/Main. CSS* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1199-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="c1199-329">Předchozí *hlavní soubor. CSS* styly aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1199-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="c1199-330">Vytvořte *Src/tsconfig. JSON* s následujícím JSON:</span><span class="sxs-lookup"><span data-stu-id="c1199-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="c1199-331">Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="c1199-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="c1199-332">Vytvořte *Src/index. TS* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1199-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="c1199-333">Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="c1199-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="c1199-334">`keyup`: Tato událost se aktivuje, když uživatel zadá do textového pole `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="c1199-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="c1199-335">Funkce `send` se volá, když uživatel stiskne klávesu **ENTER** .</span><span class="sxs-lookup"><span data-stu-id="c1199-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="c1199-336">`click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="c1199-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="c1199-337">Je volána funkce `send`.</span><span class="sxs-lookup"><span data-stu-id="c1199-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="c1199-338">Konfigurace aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1199-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="c1199-339">Kód, který je uveden v metodě `Startup.Configure`, zobrazí *Hello World!* .</span><span class="sxs-lookup"><span data-stu-id="c1199-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="c1199-340">Nahraďte volání metody `app.Run` voláními [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="c1199-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="c1199-341">Předchozí kód umožňuje serveru vyhledat a zpracovat soubor *index. html* bez ohledu na to, zda uživatel zadal svou úplnou adresu URL nebo kořenovou adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1199-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="c1199-342">Volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c1199-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c1199-343">Přidá do projektu služby signalizace.</span><span class="sxs-lookup"><span data-stu-id="c1199-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="c1199-344">Namapujte */hub* trasu na centrum `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="c1199-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="c1199-345">Na konec `Startup.Configure`přidejte následující řádky:</span><span class="sxs-lookup"><span data-stu-id="c1199-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="c1199-346">V kořenovém adresáři projektu vytvořte nový adresář s názvem *centra*.</span><span class="sxs-lookup"><span data-stu-id="c1199-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="c1199-347">Jejím účelem je uložit centrum signalizace, které se vytvoří v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="c1199-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="c1199-348">Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1199-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="c1199-349">Do horní části souboru *Startup.cs* přidejte následující kód, který vyřeší odkaz na `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="c1199-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="c1199-350">Povolit komunikaci klienta a serveru</span><span class="sxs-lookup"><span data-stu-id="c1199-350">Enable client and server communication</span></span>

<span data-ttu-id="c1199-351">Aplikace aktuálně zobrazuje jednoduchý formulář pro posílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="c1199-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="c1199-352">Při pokusu o provedení akce nedojde k žádné akci.</span><span class="sxs-lookup"><span data-stu-id="c1199-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="c1199-353">Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.</span><span class="sxs-lookup"><span data-stu-id="c1199-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="c1199-354">Spusťte následující příkaz v kořenu projektu:</span><span class="sxs-lookup"><span data-stu-id="c1199-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="c1199-355">Předchozí příkaz nainstaluje [klienta TypeScript Signal](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="c1199-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="c1199-356">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="c1199-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="c1199-357">Předchozí kód podporuje příjem zpráv ze serveru.</span><span class="sxs-lookup"><span data-stu-id="c1199-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="c1199-358">Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="c1199-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="c1199-359">Funkce `withUrl` nakonfiguruje adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="c1199-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="c1199-360">Signalizace umožňuje výměnu zpráv mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="c1199-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="c1199-361">Každá zpráva má konkrétní název.</span><span class="sxs-lookup"><span data-stu-id="c1199-361">Each message has a specific name.</span></span> <span data-ttu-id="c1199-362">Například zprávy s názvem `messageReceived` mohou spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy.</span><span class="sxs-lookup"><span data-stu-id="c1199-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="c1199-363">Naslouchat konkrétní zprávě se dá provést pomocí funkce `on`.</span><span class="sxs-lookup"><span data-stu-id="c1199-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="c1199-364">Můžete naslouchat libovolnému počtu názvů zpráv.</span><span class="sxs-lookup"><span data-stu-id="c1199-364">You can listen to any number of message names.</span></span> <span data-ttu-id="c1199-365">Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy.</span><span class="sxs-lookup"><span data-stu-id="c1199-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="c1199-366">Jakmile klient obdrží zprávu, vytvoří se nový prvek `div` s názvem autora a obsahem zprávy ve svém atributu `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="c1199-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="c1199-367">Nová zpráva je přidána do hlavního `div` elementu, který zobrazuje zprávy.</span><span class="sxs-lookup"><span data-stu-id="c1199-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="c1199-368">Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="c1199-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="c1199-369">Přidejte zvýrazněný kód do souboru *Src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="c1199-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="c1199-370">Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání metody `send`.</span><span class="sxs-lookup"><span data-stu-id="c1199-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="c1199-371">První parametr metody je název zprávy.</span><span class="sxs-lookup"><span data-stu-id="c1199-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="c1199-372">Data zprávy jsou v ostatních parametrech nezvyklá.</span><span class="sxs-lookup"><span data-stu-id="c1199-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="c1199-373">V tomto příkladu se do serveru pošle zpráva identifikovaná jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="c1199-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="c1199-374">Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole.</span><span class="sxs-lookup"><span data-stu-id="c1199-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="c1199-375">Pokud funkce Send funguje, hodnota textového pole se nevymaže.</span><span class="sxs-lookup"><span data-stu-id="c1199-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="c1199-376">Do třídy `NewMessage` přidejte metodu `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="c1199-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="c1199-377">Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží.</span><span class="sxs-lookup"><span data-stu-id="c1199-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="c1199-378">Není nutné mít obecnou `on` metodu pro příjem všech zpráv.</span><span class="sxs-lookup"><span data-stu-id="c1199-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="c1199-379">Metoda pojmenovaná po názvu zprávy postačuje.</span><span class="sxs-lookup"><span data-stu-id="c1199-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="c1199-380">V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="c1199-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="c1199-381">Metoda C# `NewMessage` očekává data odesílaná klientem.</span><span class="sxs-lookup"><span data-stu-id="c1199-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="c1199-382">Bylo provedeno volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientech. vše](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="c1199-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="c1199-383">Přijaté zprávy se odesílají všem klientům připojeným k centru.</span><span class="sxs-lookup"><span data-stu-id="c1199-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="c1199-384">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="c1199-384">Test the app</span></span>

<span data-ttu-id="c1199-385">Ověřte, že aplikace funguje s následujícím postupem.</span><span class="sxs-lookup"><span data-stu-id="c1199-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1199-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1199-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c1199-387">Spusťte příkaz Webpack v režimu *vydání* .</span><span class="sxs-lookup"><span data-stu-id="c1199-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="c1199-388">Pomocí okna **konzoly Správce balíčků** spusťte následující příkaz v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="c1199-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="c1199-389">Pokud nejste v kořenovém adresáři projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="c1199-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c1199-390">Vyberte **ladit** > **Spustit bez ladění** a spusťte aplikaci v prohlížeči bez připojení ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="c1199-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="c1199-391">Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="c1199-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="c1199-392">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="c1199-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="c1199-393">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c1199-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c1199-394">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="c1199-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c1199-395">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="c1199-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1199-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1199-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c1199-397">Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="c1199-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c1199-398">Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="c1199-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="c1199-399">Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.</span><span class="sxs-lookup"><span data-stu-id="c1199-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="c1199-400">Otevřete prohlížeč a `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="c1199-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="c1199-401">Soubor *wwwroot/index.html* se obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="c1199-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="c1199-402">Zkopírujte adresu URL z panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c1199-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="c1199-403">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="c1199-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="c1199-404">Vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c1199-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c1199-405">Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="c1199-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c1199-406">Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="c1199-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c1199-408">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c1199-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
