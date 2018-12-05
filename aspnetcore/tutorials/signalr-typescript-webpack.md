---
title: Použití funkce SignalR technologie ASP.NET Core s TypeScript a Webpacku
author: ssougnez
description: V tomto kurzu nakonfigurujete Webpacku k vytvoření balíčku a sestavit webovou aplikaci funkce SignalR technologie ASP.NET Core, jejichž klienta je napsána v TypeScript.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: b2d59dfc449953cc2d747b507295c00ac0f652dd
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862249"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="a0f5a-103">Použití funkce SignalR technologie ASP.NET Core s TypeScript a Webpacku</span><span class="sxs-lookup"><span data-stu-id="a0f5a-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="a0f5a-104">Podle [Sébastien Sougnez](https://twitter.com/ssougnez) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a0f5a-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a0f5a-105">[Webpacku](https://webpack.js.org/) vývojářům umožňuje vytvořit balíček a vytvářet prostředky webové aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="a0f5a-106">Tento kurz ukazuje použití Webpacku ve webové aplikaci funkce SignalR technologie ASP.NET Core, jejichž klienta je napsána v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="a0f5a-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="a0f5a-107">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a0f5a-108">Generování uživatelského rozhraní si první aplikaci funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0f5a-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="a0f5a-109">Konfigurace klienta SignalR TypeScript</span><span class="sxs-lookup"><span data-stu-id="a0f5a-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="a0f5a-110">Nakonfigurujte kanál sestavení pomocí Webpacku</span><span class="sxs-lookup"><span data-stu-id="a0f5a-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="a0f5a-111">Konfigurace serveru SignalR</span><span class="sxs-lookup"><span data-stu-id="a0f5a-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="a0f5a-112">Povolení komunikace mezi klientem a serverem</span><span class="sxs-lookup"><span data-stu-id="a0f5a-112">Enable communication between client and server</span></span>

<span data-ttu-id="a0f5a-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a0f5a-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE [Prerequisites](~/includes/net-core-prereqs-vs-vsc-2.2.md)]

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="a0f5a-114">Vytvoření webové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0f5a-114">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a0f5a-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0f5a-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a0f5a-116">Konfigurace sady Visual Studio k vyhledání npm v *cesta* proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-116">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="a0f5a-117">Ve výchozím nastavení používá verzi npm nalezen v adresáři instalace sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-117">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="a0f5a-118">Postupujte podle těchto pokynů v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-118">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="a0f5a-119">Přejděte do **nástroje** > **možnosti** > **projekty a řešení** > **webové správy balíčků**  >  **Externí webové nástroje**.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-119">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="a0f5a-120">Vyberte *$(PATH)* položku seznamu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-120">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="a0f5a-121">Klikněte na šipku nahoru přesunout položku na druhém pozici v seznamu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-121">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="a0f5a-123">Konfigurace Visual Studio je dokončen.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-123">Visual Studio configuration is completed.</span></span> <span data-ttu-id="a0f5a-124">Je čas vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-124">It's time to create the project.</span></span>

1. <span data-ttu-id="a0f5a-125">Použití **souboru** > **nový** > **projektu** nabídku a vyberte **webové aplikace ASP.NET Core** Šablona.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-125">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="a0f5a-126">Pojmenujte projekt *SignalRWebPack*a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-126">Name the project *SignalRWebPack*, and select **OK**.</span></span>
1. <span data-ttu-id="a0f5a-127">Vyberte *.NET Core* cílovém rozhraní rozevíracího seznamu a vyberte *2.2 technologie ASP.NET Core* z rozevírací seznam pro výběr framework.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-127">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="a0f5a-128">Vyberte **prázdný** šablony a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-128">Select the **Empty** template, and select **OK**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a0f5a-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0f5a-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a0f5a-130">Spuštěním následujícího příkazu **integrovaný terminál**:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-130">Run the following command in the **Integrated Terminal**:</span></span>

```console
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="a0f5a-131">Prázdné webové aplikace ASP.NET Core, jejichž cílem je .NET Core, je vytvořen v *SignalRWebPack* adresáře.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-131">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="a0f5a-132">Konfigurace Webpacku a TypeScript</span><span class="sxs-lookup"><span data-stu-id="a0f5a-132">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="a0f5a-133">Následující kroky konfigurace převod TypeScript pro JavaScript a vytváření prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-133">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="a0f5a-134">Spuštěním následujícího příkazu v kořenovém adresáři projektu vytvořte *package.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-134">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="a0f5a-135">Přidat vlastnost zvýrazněný na *package.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-135">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/snippets/package1.json?highlight=4)]

    <span data-ttu-id="a0f5a-136">Nastavení `private` vlastnost `true` brání upozornění instalace balíčku v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-136">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="a0f5a-137">Instalace balíčků npm vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-137">Install the required npm packages.</span></span> <span data-ttu-id="a0f5a-138">Spusťte následující příkaz z kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-138">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@0.1.19 css-loader@0.28.11 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.4.0 ts-loader@4.4.1 typescript@2.9.2 webpack@4.12.0 webpack-cli@3.0.6
    ```

    <span data-ttu-id="a0f5a-139">Některé podrobnosti příkazu mějte na paměti:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-139">Some command details to note:</span></span>

    * <span data-ttu-id="a0f5a-140">Následuje číslo verze `@` přihlášení pro každý název balíčku.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-140">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="a0f5a-141">npm instalaci těchto verzí určitého balíčku.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-141">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="a0f5a-142">`-E` Možnost zakáže npm, a výchozí chování psaní [sémantické správy verzí](https://semver.org/) rozsahu operátorům *package.json*.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-142">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="a0f5a-143">Například `"webpack": "4.12.0"` se použije namísto `"webpack": "^4.12.0"`.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-143">For example, `"webpack": "4.12.0"` is used instead of `"webpack": "^4.12.0"`.</span></span> <span data-ttu-id="a0f5a-144">Tato možnost zabraňuje neúmyslnému upgrade na novější verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-144">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="a0f5a-145">Najdete v oficiální [npm install](https://docs.npmjs.com/cli/install) dokumentace pro více podrobností.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-145">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="a0f5a-146">Nahradit `scripts` vlastnost *package.json* souboru následujícím fragmentem kódu:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-146">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="a0f5a-147">Vysvětlení skriptů:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-147">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="a0f5a-148">`build`: Obsahuje ureitou vašich prostředků na straně klienta v režimu pro vývoj a sleduje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-148">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="a0f5a-149">Sledovací proces souborů způsobí, že sada, která má znovu pokaždé, když změny souborů projektu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-149">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="a0f5a-150">`mode` Možnost zakáže optimalizace produkčního prostředí, jako je například strom, přičemž a připravenost k minifikaci.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-150">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="a0f5a-151">Používejte pouze `build` ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-151">Only use `build` in development.</span></span>
    * <span data-ttu-id="a0f5a-152">`release`: Obsahuje ureitou vašich prostředků na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-152">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="a0f5a-153">`publish`: Spustí `release` skript k vytvoření balíčku sady prostředků na straně klienta v provozním režimu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-153">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="a0f5a-154">Volá v .NET Core CLI [publikovat](/dotnet/core/tools/dotnet-publish) příkaz pro publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-154">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="a0f5a-155">Vytvořte soubor s názvem *webpack.config.js*, v kořenové složce projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-155">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/webpack.config.js)]

    <span data-ttu-id="a0f5a-156">Předchozí soubor nastaví Webpacku kompilace.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-156">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="a0f5a-157">Některé podrobnosti konfigurace mějte na paměti:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-157">Some configuration details to note:</span></span>

    * <span data-ttu-id="a0f5a-158">`output` Vlastnost přepíše výchozí hodnoty *dist*.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-158">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="a0f5a-159">Do sady prostředků místo toho je vygenerován v *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-159">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="a0f5a-160">`resolve.extensions` Pole zahrnuje *js* k importu jazyka JavaScript klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-160">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="a0f5a-161">Vytvořte nový *src* adresáře v kořenové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-161">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="a0f5a-162">Jeho účelem je ukládat prostředky projektu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-162">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="a0f5a-163">Vytvoření *src/index.html* s následujícím obsahem.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-163">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/src/index.html)]

    <span data-ttu-id="a0f5a-164">Předchozí kód HTML definuje často používaný kód na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-164">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="a0f5a-165">Vytvořte nový *src/css* adresáře.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-165">Create a new *src/css* directory.</span></span> <span data-ttu-id="a0f5a-166">Jeho účelem je pro uložení projektu *.css* soubory.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-166">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="a0f5a-167">Vytvoření *src/css/main.css* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-167">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/src/css/main.css)]

    <span data-ttu-id="a0f5a-168">Předchozí *main.css* styly souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-168">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="a0f5a-169">Vytvoření *src/tsconfig.json* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-169">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/src/tsconfig.json)]

    <span data-ttu-id="a0f5a-170">Předchozí kód konfiguruje kompilátor TypeScript vytvoří [ECMAScript](https://wikipedia.org/wiki/ECMAScript) JavaScript 5 kompatibilní.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-170">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="a0f5a-171">Vytvoření *src/index.ts* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-171">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="a0f5a-172">Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny události:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-172">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="a0f5a-173">`keyup`: Tato událost je vyvoláno, když uživatel zadá něco do textového pole označeno `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-173">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="a0f5a-174">`send` Funkce se volá, když uživatel stiskne klávesu **Enter** klíč.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-174">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="a0f5a-175">`click`: Tato událost se aktivuje, když uživatel klikne **odeslat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-175">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="a0f5a-176">`send` Funkce je volána.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-176">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="a0f5a-177">Konfigurace aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0f5a-177">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="a0f5a-178">V kódu `Startup.Configure` metoda zobrazí *Hello World!*.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-178">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="a0f5a-179">Nahradit `app.Run` volání metody s voláními [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="a0f5a-179">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="a0f5a-180">Předchozí kód umožňuje serveru k vyhledání a poskytovat *index.html* souborů, zda uživatel zadá jeho úplnou adresu URL nebo kořenovou adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-180">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="a0f5a-181">Volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) v `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-181">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a0f5a-182">Přidá do projektu služby SignalR.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-182">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="a0f5a-183">Mapování */hub* směrovat `ChatHub` rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-183">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="a0f5a-184">Přidejte následující řádky na konci `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-184">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="a0f5a-185">Vytvořte nový adresář, s názvem *rozbočovače*, v kořenové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-185">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="a0f5a-186">Jeho účelem je ukládat rozbočovače SignalR, která je vytvořena v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-186">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="a0f5a-187">Vytvoření centra *Hubs/ChatHub.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-187">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="a0f5a-188">Přidejte následující kód v horní části *Startup.cs* soubor řešení `ChatHub` odkaz:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-188">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="a0f5a-189">Povolení komunikace klienta a serveru</span><span class="sxs-lookup"><span data-stu-id="a0f5a-189">Enable client and server communication</span></span>

<span data-ttu-id="a0f5a-190">Aplikace nyní zobrazí jednoduchý formulář pro odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-190">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="a0f5a-191">Nic se nestane při pokusu provést.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-191">Nothing happens when you try to do so.</span></span> <span data-ttu-id="a0f5a-192">Server naslouchá na konkrétní postup, ale neprovede žádnou akci s odeslané zprávy.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-192">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="a0f5a-193">Spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-193">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="a0f5a-194">Předchozí příkaz nainstaluje [klienta SignalR TypeScript](https://www.npmjs.com/package/@aspnet/signalr), což umožňuje klientovi umožní odeslat zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-194">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@aspnet/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="a0f5a-195">Přidejte zvýrazněný kód, který *src/index.ts* souboru:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-195">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="a0f5a-196">Předchozí kód podporuje příjem zpráv ze serveru.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-196">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="a0f5a-197">`HubConnectionBuilder` Třída vytvoří nového tvůrce pro konfiguraci připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-197">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="a0f5a-198">`withUrl` Funkce nakonfiguruje adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-198">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="a0f5a-199">Funkce SignalR umožňuje výměnu zpráv mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-199">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="a0f5a-200">Každá zpráva má specifický název.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-200">Each message has a specific name.</span></span> <span data-ttu-id="a0f5a-201">Například můžete mít zprávy s názvem `messageReceived` spouštěných logiku za zobrazení novou zprávu v zóně zprávy.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-201">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="a0f5a-202">Naslouchání na konkrétní zprávou, můžete to udělat pomocí `on` funkce.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-202">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="a0f5a-203">Může naslouchat na libovolný počet názvů zpráv.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-203">You can listen to any number of message names.</span></span> <span data-ttu-id="a0f5a-204">Je také možné předat parametry zpráv, jako je například jméno autora nebo obsah ve zprávě přijaté.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-204">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="a0f5a-205">Jakmile klient obdrží zprávu, nový `div` element je vytvořena s jméno autora a zpráva obsahu v jeho `innerHTML` atribut.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-205">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="a0f5a-206">Přidá se do hlavní `div` element zobrazení zprávy.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-206">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="a0f5a-207">Teď, když klient může obdržet zprávu, nakonfigurujte ji k odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-207">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="a0f5a-208">Přidejte zvýrazněný kód, který *src/index.ts* souboru:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-208">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/src/index.ts?highlight=34-35)]

    <span data-ttu-id="a0f5a-209">Odeslání zprávy přes WebSockets připojení vyžaduje volání `send` metody.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-209">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="a0f5a-210">První parametr metody je název zprávy.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-210">The method's first parameter is the message name.</span></span> <span data-ttu-id="a0f5a-211">Data zprávy se vyskytuje v podoblastech ostatní parametry.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-211">The message data inhabits the other parameters.</span></span> <span data-ttu-id="a0f5a-212">V tomto příkladu zprávu identifikována jako `newMessage` je odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-212">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="a0f5a-213">Zpráva se skládá z uživatelského jména a uživatelský vstup z textového pole.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-213">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="a0f5a-214">Pokud odesílání funguje, se vymaže hodnotu textového pole.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-214">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="a0f5a-215">Přidat metodu zvýrazněný `ChatHub` třídy:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-215">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="a0f5a-216">Předchozí kód vysílá přijaté zprávy na všechny připojené uživatele po server je obdrží.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-216">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="a0f5a-217">Už není potřeba mít obecný `on` metoda přijímat všechny zprávy.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-217">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="a0f5a-218">Metodu s názvem po přípony název zprávy.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-218">A method named after the message name suffices.</span></span>

    <span data-ttu-id="a0f5a-219">V tomto příkladu TypeScript klient odešle zprávu identifikována jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-219">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="a0f5a-220">C# `NewMessage` metoda očekává, že data odeslaná klientem.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-220">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="a0f5a-221">Je provedeno volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metoda [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="a0f5a-221">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="a0f5a-222">Přijaté zprávy jsou odeslány na všechny klienty připojené k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-222">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="a0f5a-223">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="a0f5a-223">Test the app</span></span>

<span data-ttu-id="a0f5a-224">Potvrďte, že aplikace funguje pomocí následujících kroků.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-224">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a0f5a-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0f5a-225">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a0f5a-226">Spustit Webpacku *release* režimu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-226">Run Webpack in *release* mode.</span></span> <span data-ttu-id="a0f5a-227">Použití **Konzola správce balíčků** okna, spusťte následující příkaz v kořenové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-227">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="a0f5a-228">Pokud nejste v kořenové složce projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-228">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="a0f5a-229">Vyberte **ladění** > **spustit bez ladění** pro spuštění aplikace v prohlížeči bez připojení ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-229">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="a0f5a-230">*Wwwroot/index.html* se použijí pro soubor `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-230">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="a0f5a-231">Otevřete jiná instance prohlížeče (libovolného prohlížeče).</span><span class="sxs-lookup"><span data-stu-id="a0f5a-231">Open another browser instance (any browser).</span></span> <span data-ttu-id="a0f5a-232">Vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-232">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a0f5a-233">Zvolte buď prohlížeče, zadejte v něco **zpráva** textového pole a klikněte na tlačítko **odeslat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-233">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="a0f5a-234">Jedinečné uživatelské jméno a zpráva se zobrazí na obě stránky okamžitě.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-234">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a0f5a-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0f5a-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a0f5a-236">Spustit Webpacku *release* režimu spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-236">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="a0f5a-237">Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="a0f5a-237">Build and run the app by executing the following command in the project root:</span></span>

    ```console
    dotnet run
    ```

    <span data-ttu-id="a0f5a-238">Webový server spustí aplikaci a zpřístupňuje je v místním hostiteli.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-238">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="a0f5a-239">Otevřete prohlížeč na `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-239">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="a0f5a-240">*Wwwroot/index.html* soubor je předán.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-240">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="a0f5a-241">Zkopírujte adresu URL z panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-241">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="a0f5a-242">Otevřete jiná instance prohlížeče (libovolného prohlížeče).</span><span class="sxs-lookup"><span data-stu-id="a0f5a-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="a0f5a-243">Vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a0f5a-244">Zvolte buď prohlížeče, zadejte v něco **zpráva** textového pole a klikněte na tlačítko **odeslat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="a0f5a-245">Jedinečné uživatelské jméno a zpráva se zobrazí na obě stránky okamžitě.</span><span class="sxs-lookup"><span data-stu-id="a0f5a-245">The unique user name and message are displayed on both pages instantly.</span></span>

---

![zpráva zobrazená v prohlížeči windows](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

## <a name="additional-resources"></a><span data-ttu-id="a0f5a-247">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a0f5a-247">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
