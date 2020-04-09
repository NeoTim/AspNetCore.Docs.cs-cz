---
title: Použití ASP.NET SignalR jádra s TypeScriptem a webpackem
author: ssougnez
description: V tomto kurzu nakonfigurujete webový SignalR balíček tak, aby sdružoval a vytvářel ASP.NET webovou aplikaci Core, jejíž klient je napsán v typescriptu.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511337"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="d48ee-103">Použití ASP.NET core signalr s TypeScriptem a webpackem</span><span class="sxs-lookup"><span data-stu-id="d48ee-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="d48ee-104">[Sébastien Sougnez](https://twitter.com/ssougnez) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d48ee-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d48ee-105">[Webpack](https://webpack.js.org/) umožňuje vývojářům sdružovat a vytvářet prostředky webové aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d48ee-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="d48ee-106">Tento kurz ukazuje použití webového balíčku v ASP.NET webové aplikace Core SignalR, jejíž klient je napsán v [typescriptu](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="d48ee-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="d48ee-107">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="d48ee-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d48ee-108">Lešení startér ASP.NET aplikace Core SignalR</span><span class="sxs-lookup"><span data-stu-id="d48ee-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="d48ee-109">Konfigurace klienta TypeScript SignalR</span><span class="sxs-lookup"><span data-stu-id="d48ee-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="d48ee-110">Konfigurace kanálu sestavení pomocí webového balíčku</span><span class="sxs-lookup"><span data-stu-id="d48ee-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="d48ee-111">Konfigurace serveru SignalR</span><span class="sxs-lookup"><span data-stu-id="d48ee-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="d48ee-112">Povolení komunikace mezi klientem a serverem</span><span class="sxs-lookup"><span data-stu-id="d48ee-112">Enable communication between client and server</span></span>

<span data-ttu-id="d48ee-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d48ee-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="d48ee-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d48ee-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d48ee-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d48ee-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d48ee-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**</span><span class="sxs-lookup"><span data-stu-id="d48ee-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="d48ee-117">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d48ee-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="d48ee-118">[Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="d48ee-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d48ee-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d48ee-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="d48ee-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d48ee-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="d48ee-121">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d48ee-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d48ee-122">C# pro kód visual studia verze 1.17.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d48ee-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="d48ee-123">[Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="d48ee-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="d48ee-124">Vytvoření webové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d48ee-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d48ee-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d48ee-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d48ee-126">Nakonfigurujte Visual Studio tak, aby v proměnné prostředí *PATH* hledalo npm.</span><span class="sxs-lookup"><span data-stu-id="d48ee-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="d48ee-127">Ve výchozím nastavení používá visual studio verzi npm, která se nachází v instalačním adresáři.</span><span class="sxs-lookup"><span data-stu-id="d48ee-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="d48ee-128">Postupujte podle následujících pokynů v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d48ee-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="d48ee-129">Spusťte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d48ee-129">Launch Visual Studio.</span></span> <span data-ttu-id="d48ee-130">V počátečním okně vyberte **pokračovat bez kódu**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="d48ee-131">Přejděte na **nástroje** > **možnosti** > **projekty a řešení** > **webová správa** > balíčků externí webové **nástroje**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="d48ee-132">Ze seznamu vyberte položku *$(PATH).*</span><span class="sxs-lookup"><span data-stu-id="d48ee-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="d48ee-133">Kliknutím na šipku nahoru přesuňte položku na druhé místo v seznamu a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="d48ee-135">Konfigurace sady Visual Studio je dokončena.</span><span class="sxs-lookup"><span data-stu-id="d48ee-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="d48ee-136">Použijte možnost nabídky**New** > **Nový** **projekt souboru** > a zvolte šablonu **ASP.NET základní webové aplikace.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="d48ee-137">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-137">Select **Next**.</span></span>
1. <span data-ttu-id="d48ee-138">Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="d48ee-139">V rozevíracím souboru *Rozhraní .NET Core* vyberte z rozevíracího souboru cílové ho rozhraní a z rozevíracího souboru selektoru rozhraní vyberte *ASP.NET jádra 3.1.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="d48ee-140">Vyberte **prázdnou** šablonu a vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="d48ee-141">Přidejte `Microsoft.TypeScript.MSBuild` balíček do projektu:</span><span class="sxs-lookup"><span data-stu-id="d48ee-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="d48ee-142">V **Průzkumníku řešení** (pravé podokno) klikněte pravým tlačítkem myši na uzel projektu a vyberte **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="d48ee-143">Na kartě **Procházet** vyhledejte `Microsoft.TypeScript.MSBuild`položku a potom klikněte na **nainstalovat** vpravo a nainstalujte balíček.</span><span class="sxs-lookup"><span data-stu-id="d48ee-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="d48ee-144">Visual Studio přidá balíček NuGet pod uzel **Závislosti** v **Průzkumníkovi řešení**, povolení kompilace TypeScript v projektu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d48ee-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d48ee-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d48ee-146">V **integrovaném terminálu**spusťte následující příkaz :</span><span class="sxs-lookup"><span data-stu-id="d48ee-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="d48ee-147">Příkaz `dotnet new` vytvoří prázdnou ASP.NET webovou aplikaci Core v adresáři *SignalRWebPack.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="d48ee-148">Příkaz `code` otevře složku *SignalRWebPack* v aktuální instanci kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d48ee-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="d48ee-149">Spusťte následující příkaz cli jádra .NET v **integrovaném terminálu**:</span><span class="sxs-lookup"><span data-stu-id="d48ee-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="d48ee-150">Předchozí příkaz přidá balíček [Microsoft.TypeScript.MSBuild,](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) který umožňuje kompilaci typescriptu v projektu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="d48ee-151">Konfigurace webového balíčku a jazyka TypeScript</span><span class="sxs-lookup"><span data-stu-id="d48ee-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="d48ee-152">Následující kroky nakonfigurují převod jazyka TypeScript na JavaScript a sdružování prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d48ee-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="d48ee-153">Chcete-li vytvořit soubor *package.json,* spusťte v kořenovém adresáři projektu následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d48ee-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="d48ee-154">Přidejte zvýrazněnou vlastnost do souboru *package.json* a uložte změny souboru:</span><span class="sxs-lookup"><span data-stu-id="d48ee-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="d48ee-155">Nastavení `private` vlastnosti `true` zabránit upozornění instalace balíčku v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="d48ee-156">Nainstalujte požadované balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="d48ee-156">Install the required npm packages.</span></span> <span data-ttu-id="d48ee-157">V kořenovém adresáři projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d48ee-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="d48ee-158">Některé podrobnosti příkazu na vědomí:</span><span class="sxs-lookup"><span data-stu-id="d48ee-158">Some command details to note:</span></span>

    * <span data-ttu-id="d48ee-159">Číslo verze následuje `@` za znaménkem pro každý název balíčku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="d48ee-160">npm nainstaluje tyto konkrétní verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="d48ee-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="d48ee-161">Tato `-E` možnost zakáže výchozí chování npm psaní [sémantické versioning](https://semver.org/) operátory rozsahu *package.json*.</span><span class="sxs-lookup"><span data-stu-id="d48ee-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="d48ee-162">Například `"webpack": "4.41.5"` se používá `"webpack": "^4.41.5"`místo .</span><span class="sxs-lookup"><span data-stu-id="d48ee-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="d48ee-163">Tato možnost zabraňuje nechtěnému upgradu na novější verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="d48ee-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="d48ee-164">Viz [npm-install](https://docs.npmjs.com/cli/install) docs pro více podrobností.</span><span class="sxs-lookup"><span data-stu-id="d48ee-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="d48ee-165">Nahraďte `scripts` vlastnost souboru *package.json* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d48ee-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="d48ee-166">Některé vysvětlení skriptů:</span><span class="sxs-lookup"><span data-stu-id="d48ee-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="d48ee-167">`build`: Sdružuje prostředky na straně klienta v režimu vývoje a sleduje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="d48ee-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="d48ee-168">Sledovací proces souboru způsobí, že sada regenerovat pokaždé, když se změní soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="d48ee-169">Tato `mode` možnost zakáže optimalizace výroby, jako je například třepání stromů a minifikace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="d48ee-170">Používejte `build` pouze ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="d48ee-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="d48ee-171">`release`: Sdružuje prostředky na straně klienta v produkčním režimu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="d48ee-172">`publish`: Spustí `release` skript pro sbalení prostředků na straně klienta v produkčním režimu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="d48ee-173">Volá příkaz publikování rozhraní [PŘÍKAZU](/dotnet/core/tools/dotnet-publish) .NET Core pro publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="d48ee-174">Vytvořte soubor s názvem *webpack.config.js*v kořenovém adresáři projektu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d48ee-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="d48ee-175">Předchozí soubor konfiguruje kompilaci webpacku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="d48ee-176">Některé podrobnosti o konfiguraci na vědomí:</span><span class="sxs-lookup"><span data-stu-id="d48ee-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="d48ee-177">Vlastnost `output` přepíše výchozí hodnotu *dist*.</span><span class="sxs-lookup"><span data-stu-id="d48ee-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="d48ee-178">Balíček je místo toho vyzařován v adresáři *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="d48ee-179">Pole `resolve.extensions` obsahuje *.js* pro import klienta SignalR JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d48ee-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="d48ee-180">Vytvořte nový adresář *src* v kořenovém adresáři projektu pro uložení prostředků projektu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d48ee-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="d48ee-181">Vytvořte *src/index.html* pomocí následujících značek.</span><span class="sxs-lookup"><span data-stu-id="d48ee-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="d48ee-182">Předchozí kód HTML definuje standardní značky domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="d48ee-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="d48ee-183">Vytvořte nový adresář *src/css.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="d48ee-184">Jeho účelem je uložit soubory *css* projektu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="d48ee-185">Vytvořte *src/css/main.css* s následujícími CSS:</span><span class="sxs-lookup"><span data-stu-id="d48ee-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="d48ee-186">Předchozí soubor *main.css* styly aplikace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="d48ee-187">Vytvořte *src/tsconfig.json* s následujícím JSON:</span><span class="sxs-lookup"><span data-stu-id="d48ee-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="d48ee-188">Předchozí kód konfiguruje kompilátor TypeScript tak, aby vytvářel JavaScript kompatibilní s [ECMAScriptem](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="d48ee-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="d48ee-189">Vytvořte *soubor src/index.ts* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d48ee-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="d48ee-190">Předchozí typescript načte odkazy na prvky DOM a připojí dvě obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="d48ee-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="d48ee-191">`keyup`: Tato událost se aktivuje, `tbMessage`když uživatel zadá do textového pole.</span><span class="sxs-lookup"><span data-stu-id="d48ee-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="d48ee-192">Funkce `send` je volána, když uživatel stiskne klávesu **Enter.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="d48ee-193">`click`: Tato událost se aktivuje, když uživatel klepne na tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="d48ee-194">Volá se funkce `send`.</span><span class="sxs-lookup"><span data-stu-id="d48ee-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="d48ee-195">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="d48ee-195">Configure the app</span></span>

1. <span data-ttu-id="d48ee-196">V `Startup.Configure`aplikace přidejte volání do [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="d48ee-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="d48ee-197">Předchozí kód umožňuje serveru vyhledat a obsluhovat soubor *index.html.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="d48ee-198">Soubor se zobrazuje bez ohledu na to, zda uživatel zadá úplnou adresu URL nebo kořenovou adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="d48ee-199">Na konci `Startup.Configure`namapujte trasu */hub* do rozbočovače. `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="d48ee-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="d48ee-200">Nahradit kód, který zobrazuje *Hello World!*</span><span class="sxs-lookup"><span data-stu-id="d48ee-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="d48ee-201">s následujícím řádkem:</span><span class="sxs-lookup"><span data-stu-id="d48ee-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="d48ee-202">V `Startup.ConfigureServices`, volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="d48ee-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="d48ee-203">Vytvořte nový adresář s názvem *Rozbočovače* v kořenovém projektu *SignalRWebPack/* pro uložení rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="d48ee-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="d48ee-204">Vytvořte *hubhubs/ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d48ee-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="d48ee-205">Přidejte `using` do horní části *souboru Startup.cs* `ChatHub` následující příkaz, který chcete odkaz přeložit:</span><span class="sxs-lookup"><span data-stu-id="d48ee-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="d48ee-206">Povolení komunikace klienta a serveru</span><span class="sxs-lookup"><span data-stu-id="d48ee-206">Enable client and server communication</span></span>

<span data-ttu-id="d48ee-207">Aplikace v současné době zobrazuje základní formulář pro odesílání zpráv, ale ještě není funkční.</span><span class="sxs-lookup"><span data-stu-id="d48ee-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="d48ee-208">Server naslouchá určité trase, ale s odeslanými zprávami neprovede žádné informace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="d48ee-209">Spusťte následující příkaz v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="d48ee-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="d48ee-210">Předchozí příkaz nainstaluje:</span><span class="sxs-lookup"><span data-stu-id="d48ee-210">The preceding command installs:</span></span>

     * <span data-ttu-id="d48ee-211">[Klient TypeScript SignalR](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="d48ee-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="d48ee-212">Definice typu TypeScript pro soubor Node.js, který umožňuje kontrolu typů Node.js v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="d48ee-213">Přidejte zvýrazněný kód do souboru *src/index.ts:*</span><span class="sxs-lookup"><span data-stu-id="d48ee-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="d48ee-214">Předchozí kód podporuje příjem zpráv ze serveru.</span><span class="sxs-lookup"><span data-stu-id="d48ee-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="d48ee-215">Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="d48ee-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="d48ee-216">Funkce `withUrl` konfiguruje adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="d48ee-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="d48ee-217">SignalR umožňuje výměnu zpráv mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="d48ee-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="d48ee-218">Každá zpráva má konkrétní název.</span><span class="sxs-lookup"><span data-stu-id="d48ee-218">Each message has a specific name.</span></span> <span data-ttu-id="d48ee-219">Například zprávy s `messageReceived` názvem můžete spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy.</span><span class="sxs-lookup"><span data-stu-id="d48ee-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="d48ee-220">Poslech konkrétní zprávy lze provést `on` prostřednictvím funkce.</span><span class="sxs-lookup"><span data-stu-id="d48ee-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="d48ee-221">Libovolný počet názvů zpráv lze poslouchat.</span><span class="sxs-lookup"><span data-stu-id="d48ee-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="d48ee-222">Je také možné předat parametry zprávy, jako je jméno autora a obsah přijaté zprávy.</span><span class="sxs-lookup"><span data-stu-id="d48ee-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="d48ee-223">Jakmile klient obdrží zprávu, vytvoří `div` se nový prvek se jménem autora `innerHTML` a obsahem zprávy v jeho atributu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="d48ee-224">Je přidán do hlavního `div` prvku zobrazujícího zprávy.</span><span class="sxs-lookup"><span data-stu-id="d48ee-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="d48ee-225">Nyní, když klient může přijímat zprávy, nakonfigurovat pro odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="d48ee-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="d48ee-226">Přidejte zvýrazněný kód do souboru *src/index.ts:*</span><span class="sxs-lookup"><span data-stu-id="d48ee-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="d48ee-227">Odeslání zprávy prostřednictvím připojení WebSockets `send` vyžaduje volání metody.</span><span class="sxs-lookup"><span data-stu-id="d48ee-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="d48ee-228">První parametr metody je název zprávy.</span><span class="sxs-lookup"><span data-stu-id="d48ee-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="d48ee-229">Data zprávy obývají další parametry.</span><span class="sxs-lookup"><span data-stu-id="d48ee-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="d48ee-230">V tomto příkladu je `newMessage` zpráva označená jako odeslaná na server.</span><span class="sxs-lookup"><span data-stu-id="d48ee-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="d48ee-231">Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole.</span><span class="sxs-lookup"><span data-stu-id="d48ee-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="d48ee-232">Pokud odeslání funguje, hodnota textového pole je vymazána.</span><span class="sxs-lookup"><span data-stu-id="d48ee-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="d48ee-233">Do třídy `ChatHub` přidejte metodu `NewMessage`:</span><span class="sxs-lookup"><span data-stu-id="d48ee-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="d48ee-234">Předchozí kód vysílá přijaté zprávy všem připojeným uživatelům, jakmile je server přijme.</span><span class="sxs-lookup"><span data-stu-id="d48ee-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="d48ee-235">Není nutné mít obecnou `on` metodu pro příjem všech zpráv.</span><span class="sxs-lookup"><span data-stu-id="d48ee-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="d48ee-236">Metoda pojmenovaná za názvem zprávy stačí.</span><span class="sxs-lookup"><span data-stu-id="d48ee-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="d48ee-237">V tomto příkladu klient TypeScript odešle zprávu označenou jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="d48ee-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="d48ee-238">Metoda Jazyka `NewMessage` C# očekává data odeslaná klientem.</span><span class="sxs-lookup"><span data-stu-id="d48ee-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="d48ee-239">Volání [sendasync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="d48ee-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="d48ee-240">Přijaté zprávy jsou odesílány všem klientům připojeným k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="d48ee-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="d48ee-241">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="d48ee-241">Test the app</span></span>

<span data-ttu-id="d48ee-242">Zkontrolujte, zda aplikace pracuje s následujícími kroky.</span><span class="sxs-lookup"><span data-stu-id="d48ee-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d48ee-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d48ee-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d48ee-244">Spusťte webpack v režimu *vydání.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="d48ee-245">Pomocí okna **Konzola správce balíčků** spusťte v kořenovém adresáři projektu následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="d48ee-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="d48ee-246">Pokud nejste v kořenovém `cd SignalRWebPack` adresáři projektu, zadejte před zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="d48ee-247">Vyberte **Ladění** > **start bez ladění** spustit aplikaci v prohlížeči bez připojení ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="d48ee-248">Soubor *wwwroot/index.html* je `http://localhost:<port_number>`doručen na adrese .</span><span class="sxs-lookup"><span data-stu-id="d48ee-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="d48ee-249">Pokud se zvásňují chyby kompilace, zkuste řešení zavřít a znovu otevřít.</span><span class="sxs-lookup"><span data-stu-id="d48ee-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="d48ee-250">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="d48ee-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="d48ee-251">Vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d48ee-252">Zvolte některý z prohlížečů, něco zadejte do textového pole **Zpráva** a klikněte na tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="d48ee-253">Jedinečné uživatelské jméno a zpráva se zobrazí na obou stránkách okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d48ee-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d48ee-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d48ee-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d48ee-255">Spustit webpack v *režimu vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="d48ee-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="d48ee-256">Vytvořte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="d48ee-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="d48ee-257">Webový server spustí aplikaci a zpřístupní ji na localhost.</span><span class="sxs-lookup"><span data-stu-id="d48ee-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="d48ee-258">Otevřete prohlížeč `http://localhost:<port_number>`aplikace .</span><span class="sxs-lookup"><span data-stu-id="d48ee-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="d48ee-259">Soubor *wwwroot/index.html* je doručena.</span><span class="sxs-lookup"><span data-stu-id="d48ee-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="d48ee-260">Zkopírujte adresu URL z panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="d48ee-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="d48ee-261">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="d48ee-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="d48ee-262">Vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d48ee-263">Zvolte některý z prohlížečů, něco zadejte do textového pole **Zpráva** a klikněte na tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="d48ee-264">Jedinečné uživatelské jméno a zpráva se zobrazí na obou stránkách okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d48ee-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![zpráva zobrazená v obou oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="d48ee-266">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d48ee-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d48ee-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d48ee-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d48ee-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**</span><span class="sxs-lookup"><span data-stu-id="d48ee-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="d48ee-269">Sada .NET Core SDK 2.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d48ee-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="d48ee-270">[Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="d48ee-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d48ee-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d48ee-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="d48ee-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d48ee-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="d48ee-273">Sada .NET Core SDK 2.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d48ee-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d48ee-274">C# pro kód visual studia verze 1.17.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d48ee-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="d48ee-275">[Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="d48ee-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="d48ee-276">Vytvoření webové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d48ee-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d48ee-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d48ee-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d48ee-278">Nakonfigurujte Visual Studio tak, aby v proměnné prostředí *PATH* hledalo npm.</span><span class="sxs-lookup"><span data-stu-id="d48ee-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="d48ee-279">Ve výchozím nastavení používá visual studio verzi npm, která se nachází v instalačním adresáři.</span><span class="sxs-lookup"><span data-stu-id="d48ee-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="d48ee-280">Postupujte podle následujících pokynů v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d48ee-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="d48ee-281">Přejděte na **nástroje** > **možnosti** > **projekty a řešení** > **webová správa** > balíčků externí webové **nástroje**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="d48ee-282">Ze seznamu vyberte položku *$(PATH).*</span><span class="sxs-lookup"><span data-stu-id="d48ee-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="d48ee-283">Kliknutím na šipku nahoru přesuňte položku na druhé místo v seznamu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="d48ee-285">Konfigurace sady Visual Studio je dokončena.</span><span class="sxs-lookup"><span data-stu-id="d48ee-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="d48ee-286">Je čas vytvořit projekt.</span><span class="sxs-lookup"><span data-stu-id="d48ee-286">It's time to create the project.</span></span>

1. <span data-ttu-id="d48ee-287">Použijte možnost nabídky **New** > **Nový** **projekt souboru** > a zvolte šablonu **ASP.NET základní webové aplikace.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="d48ee-288">Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="d48ee-289">V rozevíracím souboru *Rozhraní .NET Core* vyberte z rozevíracího souboru cílového rozhraní a z rozevíracího souboru selektoru rozhraní vyberte *ASP.NET jádra 2.2.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="d48ee-290">Vyberte **prázdnou** šablonu a vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d48ee-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d48ee-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d48ee-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d48ee-292">V **integrovaném terminálu**spusťte následující příkaz :</span><span class="sxs-lookup"><span data-stu-id="d48ee-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="d48ee-293">V adresáři *SignalRWebPack* je vytvořena prázdná ASP.NET webová aplikace Core, která cílí na rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d48ee-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="d48ee-294">Konfigurace webového balíčku a jazyka TypeScript</span><span class="sxs-lookup"><span data-stu-id="d48ee-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="d48ee-295">Následující kroky nakonfigurují převod jazyka TypeScript na JavaScript a sdružování prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d48ee-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="d48ee-296">Chcete-li vytvořit soubor *package.json,* spusťte v kořenovém adresáři projektu následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d48ee-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="d48ee-297">Přidejte zvýrazněnou vlastnost do souboru *package.json:*</span><span class="sxs-lookup"><span data-stu-id="d48ee-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="d48ee-298">Nastavení `private` vlastnosti `true` zabránit upozornění instalace balíčku v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="d48ee-299">Nainstalujte požadované balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="d48ee-299">Install the required npm packages.</span></span> <span data-ttu-id="d48ee-300">V kořenovém adresáři projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d48ee-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="d48ee-301">Některé podrobnosti příkazu na vědomí:</span><span class="sxs-lookup"><span data-stu-id="d48ee-301">Some command details to note:</span></span>

    * <span data-ttu-id="d48ee-302">Číslo verze následuje `@` za znaménkem pro každý název balíčku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="d48ee-303">npm nainstaluje tyto konkrétní verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="d48ee-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="d48ee-304">Tato `-E` možnost zakáže výchozí chování npm psaní [sémantické versioning](https://semver.org/) operátory rozsahu *package.json*.</span><span class="sxs-lookup"><span data-stu-id="d48ee-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="d48ee-305">Například `"webpack": "4.29.3"` se používá `"webpack": "^4.29.3"`místo .</span><span class="sxs-lookup"><span data-stu-id="d48ee-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="d48ee-306">Tato možnost zabraňuje nechtěnému upgradu na novější verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="d48ee-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="d48ee-307">Viz [npm-install](https://docs.npmjs.com/cli/install) docs pro více podrobností.</span><span class="sxs-lookup"><span data-stu-id="d48ee-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="d48ee-308">Nahraďte `scripts` vlastnost souboru *package.json* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d48ee-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="d48ee-309">Některé vysvětlení skriptů:</span><span class="sxs-lookup"><span data-stu-id="d48ee-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="d48ee-310">`build`: Sdružuje prostředky na straně klienta v režimu vývoje a sleduje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="d48ee-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="d48ee-311">Sledovací proces souboru způsobí, že sada regenerovat pokaždé, když se změní soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="d48ee-312">Tato `mode` možnost zakáže optimalizace výroby, jako je například třepání stromů a minifikace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="d48ee-313">Používejte `build` pouze ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="d48ee-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="d48ee-314">`release`: Sdružuje prostředky na straně klienta v produkčním režimu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="d48ee-315">`publish`: Spustí `release` skript pro sbalení prostředků na straně klienta v produkčním režimu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="d48ee-316">Volá příkaz publikování rozhraní [PŘÍKAZU](/dotnet/core/tools/dotnet-publish) .NET Core pro publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="d48ee-317">Vytvořte soubor s názvem *webpack.config.js* v kořenovém adresáři projektu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d48ee-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="d48ee-318">Předchozí soubor konfiguruje kompilaci webpacku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="d48ee-319">Některé podrobnosti o konfiguraci na vědomí:</span><span class="sxs-lookup"><span data-stu-id="d48ee-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="d48ee-320">Vlastnost `output` přepíše výchozí hodnotu *dist*.</span><span class="sxs-lookup"><span data-stu-id="d48ee-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="d48ee-321">Balíček je místo toho vyzařován v adresáři *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="d48ee-322">Pole `resolve.extensions` obsahuje *.js* pro import klienta SignalR JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d48ee-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="d48ee-323">Vytvořte nový adresář *src* v kořenovém adresáři projektu pro uložení prostředků projektu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d48ee-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="d48ee-324">Vytvořte *src/index.html* pomocí následujících značek.</span><span class="sxs-lookup"><span data-stu-id="d48ee-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="d48ee-325">Předchozí kód HTML definuje standardní značky domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="d48ee-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="d48ee-326">Vytvořte nový adresář *src/css.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="d48ee-327">Jeho účelem je uložit soubory *css* projektu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="d48ee-328">Vytvořte *src/css/main.css* s následujícími značkami:</span><span class="sxs-lookup"><span data-stu-id="d48ee-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="d48ee-329">Předchozí soubor *main.css* styly aplikace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="d48ee-330">Vytvořte *src/tsconfig.json* s následujícím JSON:</span><span class="sxs-lookup"><span data-stu-id="d48ee-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="d48ee-331">Předchozí kód konfiguruje kompilátor TypeScript tak, aby vytvářel JavaScript kompatibilní s [ECMAScriptem](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="d48ee-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="d48ee-332">Vytvořte *soubor src/index.ts* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d48ee-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="d48ee-333">Předchozí typescript načte odkazy na prvky DOM a připojí dvě obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="d48ee-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="d48ee-334">`keyup`: Tato událost se aktivuje, `tbMessage` když uživatel zadá do textového pole.</span><span class="sxs-lookup"><span data-stu-id="d48ee-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="d48ee-335">Funkce `send` je volána, když uživatel stiskne klávesu **Enter.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="d48ee-336">`click`: Tato událost se aktivuje, když uživatel klepne na tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="d48ee-337">Volá se funkce `send`.</span><span class="sxs-lookup"><span data-stu-id="d48ee-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="d48ee-338">Konfigurace aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d48ee-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="d48ee-339">Kód uvedený v `Startup.Configure` metodě zobrazuje *Hello World!*.</span><span class="sxs-lookup"><span data-stu-id="d48ee-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="d48ee-340">Nahraďte volání `app.Run` metody voláními [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="d48ee-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="d48ee-341">Předchozí kód umožňuje serveru vyhledat a obsluhovat soubor *index.html* bez ohledu na to, zda uživatel zadá úplnou adresu URL nebo kořenovou adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="d48ee-342">Volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d48ee-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d48ee-343">Přidá služby SignalR do projektu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="d48ee-344">Namapujte trasu `ChatHub` */hub* do rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="d48ee-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="d48ee-345">Na konec přidejte následující `Startup.Configure`řádky:</span><span class="sxs-lookup"><span data-stu-id="d48ee-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="d48ee-346">Vytvořte nový adresář s názvem *Rozbočovače*v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="d48ee-347">Jeho účelem je uložit rozbočovač SignalR, který je vytvořen v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="d48ee-348">Vytvořte *hubhubs/ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d48ee-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="d48ee-349">Chcete-li `ChatHub` přeložit odkaz, přidejte v horní části souboru *Startup.cs* následující kód:</span><span class="sxs-lookup"><span data-stu-id="d48ee-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="d48ee-350">Povolení komunikace klienta a serveru</span><span class="sxs-lookup"><span data-stu-id="d48ee-350">Enable client and server communication</span></span>

<span data-ttu-id="d48ee-351">Aplikace v současné době zobrazuje jednoduchý formulář pro odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="d48ee-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="d48ee-352">Nic se nestane, když se o to pokusíte.</span><span class="sxs-lookup"><span data-stu-id="d48ee-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="d48ee-353">Server naslouchá určité trase, ale s odeslanými zprávami neprovede žádné informace.</span><span class="sxs-lookup"><span data-stu-id="d48ee-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="d48ee-354">Spusťte následující příkaz v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="d48ee-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="d48ee-355">Předchozí příkaz nainstaluje [klienta SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="d48ee-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="d48ee-356">Přidejte zvýrazněný kód do souboru *src/index.ts:*</span><span class="sxs-lookup"><span data-stu-id="d48ee-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="d48ee-357">Předchozí kód podporuje příjem zpráv ze serveru.</span><span class="sxs-lookup"><span data-stu-id="d48ee-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="d48ee-358">Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="d48ee-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="d48ee-359">Funkce `withUrl` konfiguruje adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="d48ee-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="d48ee-360">SignalR umožňuje výměnu zpráv mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="d48ee-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="d48ee-361">Každá zpráva má konkrétní název.</span><span class="sxs-lookup"><span data-stu-id="d48ee-361">Each message has a specific name.</span></span> <span data-ttu-id="d48ee-362">Například zprávy s `messageReceived` názvem můžete spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy.</span><span class="sxs-lookup"><span data-stu-id="d48ee-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="d48ee-363">Poslech konkrétní zprávy lze provést `on` prostřednictvím funkce.</span><span class="sxs-lookup"><span data-stu-id="d48ee-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="d48ee-364">Můžete poslouchat libovolný počet názvů zpráv.</span><span class="sxs-lookup"><span data-stu-id="d48ee-364">You can listen to any number of message names.</span></span> <span data-ttu-id="d48ee-365">Je také možné předat parametry zprávy, jako je jméno autora a obsah přijaté zprávy.</span><span class="sxs-lookup"><span data-stu-id="d48ee-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="d48ee-366">Jakmile klient obdrží zprávu, vytvoří `div` se nový prvek se jménem autora `innerHTML` a obsahem zprávy v jeho atributu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="d48ee-367">Nová zpráva je přidána `div` do hlavního prvku zobrazujícího zprávy.</span><span class="sxs-lookup"><span data-stu-id="d48ee-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="d48ee-368">Nyní, když klient může přijímat zprávy, nakonfigurovat pro odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="d48ee-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="d48ee-369">Přidejte zvýrazněný kód do souboru *src/index.ts:*</span><span class="sxs-lookup"><span data-stu-id="d48ee-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="d48ee-370">Odeslání zprávy prostřednictvím připojení WebSockets `send` vyžaduje volání metody.</span><span class="sxs-lookup"><span data-stu-id="d48ee-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="d48ee-371">První parametr metody je název zprávy.</span><span class="sxs-lookup"><span data-stu-id="d48ee-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="d48ee-372">Data zprávy obývají další parametry.</span><span class="sxs-lookup"><span data-stu-id="d48ee-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="d48ee-373">V tomto příkladu je `newMessage` zpráva označená jako odeslaná na server.</span><span class="sxs-lookup"><span data-stu-id="d48ee-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="d48ee-374">Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole.</span><span class="sxs-lookup"><span data-stu-id="d48ee-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="d48ee-375">Pokud odeslání funguje, hodnota textového pole je vymazána.</span><span class="sxs-lookup"><span data-stu-id="d48ee-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="d48ee-376">Do třídy `ChatHub` přidejte metodu `NewMessage`:</span><span class="sxs-lookup"><span data-stu-id="d48ee-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="d48ee-377">Předchozí kód vysílá přijaté zprávy všem připojeným uživatelům, jakmile je server přijme.</span><span class="sxs-lookup"><span data-stu-id="d48ee-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="d48ee-378">Není nutné mít obecnou `on` metodu pro příjem všech zpráv.</span><span class="sxs-lookup"><span data-stu-id="d48ee-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="d48ee-379">Metoda pojmenovaná za názvem zprávy stačí.</span><span class="sxs-lookup"><span data-stu-id="d48ee-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="d48ee-380">V tomto příkladu klient TypeScript odešle zprávu označenou jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="d48ee-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="d48ee-381">Metoda Jazyka `NewMessage` C# očekává data odeslaná klientem.</span><span class="sxs-lookup"><span data-stu-id="d48ee-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="d48ee-382">Volání [sendasync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="d48ee-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="d48ee-383">Přijaté zprávy jsou odesílány všem klientům připojeným k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="d48ee-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="d48ee-384">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="d48ee-384">Test the app</span></span>

<span data-ttu-id="d48ee-385">Zkontrolujte, zda aplikace pracuje s následujícími kroky.</span><span class="sxs-lookup"><span data-stu-id="d48ee-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d48ee-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d48ee-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d48ee-387">Spusťte webpack v režimu *vydání.*</span><span class="sxs-lookup"><span data-stu-id="d48ee-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="d48ee-388">Pomocí okna **Konzola správce balíčků** spusťte v kořenovém adresáři projektu následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="d48ee-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="d48ee-389">Pokud nejste v kořenovém `cd SignalRWebPack` adresáři projektu, zadejte před zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="d48ee-390">Vyberte **Ladění** > **start bez ladění** spustit aplikaci v prohlížeči bez připojení ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="d48ee-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="d48ee-391">Soubor *wwwroot/index.html* je `http://localhost:<port_number>`doručen na adrese .</span><span class="sxs-lookup"><span data-stu-id="d48ee-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="d48ee-392">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="d48ee-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="d48ee-393">Vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d48ee-394">Zvolte některý z prohlížečů, něco zadejte do textového pole **Zpráva** a klikněte na tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="d48ee-395">Jedinečné uživatelské jméno a zpráva se zobrazí na obou stránkách okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d48ee-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d48ee-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d48ee-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d48ee-397">Spustit webpack v *režimu vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="d48ee-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="d48ee-398">Vytvořte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="d48ee-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="d48ee-399">Webový server spustí aplikaci a zpřístupní ji na localhost.</span><span class="sxs-lookup"><span data-stu-id="d48ee-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="d48ee-400">Otevřete prohlížeč `http://localhost:<port_number>`aplikace .</span><span class="sxs-lookup"><span data-stu-id="d48ee-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="d48ee-401">Soubor *wwwroot/index.html* je doručena.</span><span class="sxs-lookup"><span data-stu-id="d48ee-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="d48ee-402">Zkopírujte adresu URL z panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="d48ee-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="d48ee-403">Otevřete jinou instanci prohlížeče (libovolný prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="d48ee-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="d48ee-404">Vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="d48ee-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d48ee-405">Zvolte některý z prohlížečů, něco zadejte do textového pole **Zpráva** a klikněte na tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="d48ee-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="d48ee-406">Jedinečné uživatelské jméno a zpráva se zobrazí na obou stránkách okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d48ee-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![zpráva zobrazená v obou oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d48ee-408">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d48ee-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
