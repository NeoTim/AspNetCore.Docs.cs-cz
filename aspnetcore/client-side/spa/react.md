---
title: Použití šablony projektu React s ASP.NET Jádrem
author: SteveSandersonMS
description: Přečtěte si, jak začít s šablonou projektu ASP.NET základní jednostránkové aplikace (SPA) pro react a create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/react
ms.openlocfilehash: 9703a62eb7f779974382fe0fb01702d9fcd37d64
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664960"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="95bc5-103">Použití šablony projektu React s ASP.NET Jádrem</span><span class="sxs-lookup"><span data-stu-id="95bc5-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="95bc5-104">Aktualizovaná šablona projektu React poskytuje pohodlný výchozí bod pro ASP.NET aplikace Core pomocí konvencí React a [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) k implementaci bohatého uživatelského rozhraní na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="95bc5-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="95bc5-105">Šablona je ekvivalentní k vytvoření ASP.NET core projektu působit jako backend ROZHRANÍ API, a standardní CRA React projekt působit jako uživatelské rozhraní, ale s pohodlí hostování jak v jednom projektu aplikace, které mohou být vytvořeny a zveřejněny jako jedna jednotka.</span><span class="sxs-lookup"><span data-stu-id="95bc5-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="95bc5-106">Šablona projektu React není určena pro vykreslování na straně serveru (SSR).</span><span class="sxs-lookup"><span data-stu-id="95bc5-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="95bc5-107">Pro SSR s Reagovat a Node.js, zvažte [Next.js](https://github.com/zeit/next.js/) nebo [Razzle](https://github.com/jaredpalmer/razzle).</span><span class="sxs-lookup"><span data-stu-id="95bc5-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="95bc5-108">Vytvoření nové aplikace</span><span class="sxs-lookup"><span data-stu-id="95bc5-108">Create a new app</span></span>

<span data-ttu-id="95bc5-109">Pokud máte nainstalovanou ASP.NET Core 2.1, není nutné instalovat šablonu projektu React.</span><span class="sxs-lookup"><span data-stu-id="95bc5-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="95bc5-110">Vytvořte nový projekt z příkazového `dotnet new react` řádku pomocí příkazu v prázdném adresáři.</span><span class="sxs-lookup"><span data-stu-id="95bc5-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="95bc5-111">Například následující příkazy vytvoří aplikaci v adresáři *moje nová aplikace* a přejdou do tohoto adresáře:</span><span class="sxs-lookup"><span data-stu-id="95bc5-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="95bc5-112">Spusťte aplikaci z Visual Studia nebo rozhraní .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="95bc5-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95bc5-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95bc5-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95bc5-114">Otevřete vygenerovaný soubor *.csproj* a spusťte aplikaci jako obvykle.</span><span class="sxs-lookup"><span data-stu-id="95bc5-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="95bc5-115">Proces sestavení obnoví npm závislosti na první spuštění, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="95bc5-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="95bc5-116">Následná sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="95bc5-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="95bc5-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="95bc5-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="95bc5-118">Ujistěte se, že `ASPNETCORE_Environment` máte `Development`proměnnou prostředí volanou s hodnotou .</span><span class="sxs-lookup"><span data-stu-id="95bc5-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="95bc5-119">Ve Windows (v výzvách, které `SET ASPNETCORE_Environment=Development`nejsou powershelly), spusťte .</span><span class="sxs-lookup"><span data-stu-id="95bc5-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="95bc5-120">Na Linuxu nebo macOS spusťte `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="95bc5-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="95bc5-121">Spusťte [dotnet sestavení](/dotnet/core/tools/dotnet-build) ověřit vaše aplikace staví správně.</span><span class="sxs-lookup"><span data-stu-id="95bc5-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="95bc5-122">Při prvním spuštění proces sestavení obnoví npm závislosti, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="95bc5-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="95bc5-123">Následná sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="95bc5-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="95bc5-124">Spuštění [montovny dotnet](/dotnet/core/tools/dotnet-run) spusťte.</span><span class="sxs-lookup"><span data-stu-id="95bc5-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="95bc5-125">Šablona projektu vytvoří aplikaci ASP.NET Core a aplikaci React.</span><span class="sxs-lookup"><span data-stu-id="95bc5-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="95bc5-126">Aplikace ASP.NET Core je určena pro přístup k datům, autorizaci a další problémy na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="95bc5-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="95bc5-127">Aplikace React, která sídlí v podadresáři *ClientApp,* je určena pro všechny problémy s rozhraním.</span><span class="sxs-lookup"><span data-stu-id="95bc5-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="95bc5-128">Přidejte stránky, obrázky, styly, moduly atd.</span><span class="sxs-lookup"><span data-stu-id="95bc5-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="95bc5-129">Adresář *ClientApp* je standardní aplikace CRA React.</span><span class="sxs-lookup"><span data-stu-id="95bc5-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="95bc5-130">Další informace naleznete v oficiální [dokumentaci cra.](https://create-react-app.dev/docs/getting-started/)</span><span class="sxs-lookup"><span data-stu-id="95bc5-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="95bc5-131">Existují mírné rozdíly mezi aplikací React vytvořenou touto šablonou a aplikací vytvořenou samotnou CRA; možnosti aplikace se však nemění.</span><span class="sxs-lookup"><span data-stu-id="95bc5-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="95bc5-132">Aplikace vytvořená šablonou obsahuje rozložení založené na [Bootstrapu](https://getbootstrap.com/)a základní příklad směrování.</span><span class="sxs-lookup"><span data-stu-id="95bc5-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="95bc5-133">Instalace balíčků npm</span><span class="sxs-lookup"><span data-stu-id="95bc5-133">Install npm packages</span></span>

<span data-ttu-id="95bc5-134">Chcete-li nainstalovat balíčky npm od jiných výrobců, použijte příkazový řádek v podadresáři *ClientApp.*</span><span class="sxs-lookup"><span data-stu-id="95bc5-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="95bc5-135">Příklad:</span><span class="sxs-lookup"><span data-stu-id="95bc5-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="95bc5-136">Publikování a nasazení</span><span class="sxs-lookup"><span data-stu-id="95bc5-136">Publish and deploy</span></span>

<span data-ttu-id="95bc5-137">Ve vývoji aplikace běží v režimu optimalizovaném pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="95bc5-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="95bc5-138">Například balíčky JavaScriptu obsahují zdrojové mapy (takže při ladění můžete vidět původní zdrojový kód).</span><span class="sxs-lookup"><span data-stu-id="95bc5-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="95bc5-139">Aplikace sleduje změny souborů JavaScript, HTML a CSS na disku a automaticky se znovu zkompiluje a znovu načte, když vidí, že se tyto soubory mění.</span><span class="sxs-lookup"><span data-stu-id="95bc5-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="95bc5-140">V produkčním prostředí můžete poskytovat verzi aplikace optimalizovanou pro výkon.</span><span class="sxs-lookup"><span data-stu-id="95bc5-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="95bc5-141">Tato možnost je nakonfigurována tak, aby se stala automaticky.</span><span class="sxs-lookup"><span data-stu-id="95bc5-141">This is configured to happen automatically.</span></span> <span data-ttu-id="95bc5-142">Při publikování, konfigurace sestavení vyzařuje minified, transpilované sestavení kódu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="95bc5-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="95bc5-143">Na rozdíl od vývojového sestavení nevyžaduje produkční sestavení node.js, které mají být nainstalovány na serveru.</span><span class="sxs-lookup"><span data-stu-id="95bc5-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="95bc5-144">Můžete použít standardní [ASP.NET core hosting a metody nasazení](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="95bc5-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="95bc5-145">Spuštění serveru CRA nezávisle</span><span class="sxs-lookup"><span data-stu-id="95bc5-145">Run the CRA server independently</span></span>

<span data-ttu-id="95bc5-146">Projekt je nakonfigurován tak, aby spustil vlastní instanci vývojového serveru CRA na pozadí při spuštění aplikace ASP.NET Core v režimu vývoje.</span><span class="sxs-lookup"><span data-stu-id="95bc5-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="95bc5-147">To je výhodné, protože to znamená, že nemusíte spouštět samostatný server ručně.</span><span class="sxs-lookup"><span data-stu-id="95bc5-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="95bc5-148">Toto výchozí nastavení má nevýhodu.</span><span class="sxs-lookup"><span data-stu-id="95bc5-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="95bc5-149">Pokaždé, když upravíte kód C# a aplikace ASP.NET Core musí být restartována, server CRA se restartuje.</span><span class="sxs-lookup"><span data-stu-id="95bc5-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="95bc5-150">Pro spuštění zálohy je zapotřebí několik sekund.</span><span class="sxs-lookup"><span data-stu-id="95bc5-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="95bc5-151">Pokud provádíte časté úpravy kódu C# a nechcete čekat na restartování serveru CRA, spusťte server CRA externě, nezávisle na procesu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="95bc5-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="95bc5-152">Postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="95bc5-152">To do so:</span></span>

1. <span data-ttu-id="95bc5-153">Přidejte soubor *ENV* do podadresáře *ClientApp* s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="95bc5-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="95bc5-154">Tím se zabrání otevření webového prohlížeče při spuštění serveru CRA externě.</span><span class="sxs-lookup"><span data-stu-id="95bc5-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="95bc5-155">V příkazovém řádku přepněte do podadresáře *ClientApp* a spusťte vývojový server CRA:</span><span class="sxs-lookup"><span data-stu-id="95bc5-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="95bc5-156">Upravte aplikaci ASP.NET Core tak, aby místo spuštění vlastní instance externího serveru CRA používala externí instanci serveru CRA.</span><span class="sxs-lookup"><span data-stu-id="95bc5-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="95bc5-157">Ve třídě *Startup* nahraďte `spa.UseReactDevelopmentServer` vyvolání následujícím:</span><span class="sxs-lookup"><span data-stu-id="95bc5-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="95bc5-158">Když spustíte aplikaci ASP.NET Core, nespustí server CRA.</span><span class="sxs-lookup"><span data-stu-id="95bc5-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="95bc5-159">Místo toho se použije instance, kterou jste spustili ručně.</span><span class="sxs-lookup"><span data-stu-id="95bc5-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="95bc5-160">To umožňuje rychlejší spuštění a restartování.</span><span class="sxs-lookup"><span data-stu-id="95bc5-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="95bc5-161">Už nečeká, až se aplikace React pokaždé znovu vytvoří.</span><span class="sxs-lookup"><span data-stu-id="95bc5-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="95bc5-162">"Vykreslování na straně serveru" není podporovanou funkcí této šablony.</span><span class="sxs-lookup"><span data-stu-id="95bc5-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="95bc5-163">Naším cílem s touto šablonou je splnit paritu s "create-react-app".</span><span class="sxs-lookup"><span data-stu-id="95bc5-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="95bc5-164">Jako takové scénáře a funkce, které nejsou zahrnuty v projektu "vytvořit reagovat aplikace" (například SSR) nejsou podporovány a jsou ponechány jako cvičení pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="95bc5-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95bc5-165">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="95bc5-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
