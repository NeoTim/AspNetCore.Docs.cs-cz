---
title: Použijte šablonu projektu reakce s ASP.NET Core
author: SteveSandersonMS
description: Naučte se, jak začít pracovat s ASP.NET Core šablonou projektu s jednou stránkou (SPA) pro reakci a vytváření reakce na aplikaci.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/react
ms.openlocfilehash: d5185a80895423bdd60309509ab4d1dad4dad12f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401724"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="befb2-103">Použijte šablonu projektu reakce s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="befb2-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="befb2-104">Aktualizovaná šablona projektu reakce poskytuje pohodlný výchozí bod pro ASP.NET Core aplikace pomocí konvencí reakce a [vytváření reakce aplikace](https://github.com/facebookincubator/create-react-app) (CRA) pro implementaci bohatě náročného uživatelského rozhraní (UI) na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="befb2-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="befb2-105">Šablona je ekvivalentní k vytváření ASP.NET Core projektu, který se má chovat jako back-end rozhraní API, a standardní projekt CRA reaguje na fungování jako uživatelské rozhraní, ale s pohodlíou hostování v jediném projektu aplikace, který je možné sestavit a publikovat jako jeden celek.</span><span class="sxs-lookup"><span data-stu-id="befb2-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="befb2-106">Šablona projektu reakce není určena pro vykreslování na straně serveru (SSR).</span><span class="sxs-lookup"><span data-stu-id="befb2-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="befb2-107">Pro SSR, který reaguje a Node.js, zvažte [Next.js](https://github.com/zeit/next.js/) nebo [Razzle](https://github.com/jaredpalmer/razzle).</span><span class="sxs-lookup"><span data-stu-id="befb2-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="befb2-108">Vytvoření nové aplikace</span><span class="sxs-lookup"><span data-stu-id="befb2-108">Create a new app</span></span>

<span data-ttu-id="befb2-109">Pokud máte nainstalovanou ASP.NET Core 2,1, není nutné instalovat šablonu projektu reakce.</span><span class="sxs-lookup"><span data-stu-id="befb2-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="befb2-110">Vytvoří nový projekt z příkazového řádku pomocí příkazu `dotnet new react` v prázdném adresáři.</span><span class="sxs-lookup"><span data-stu-id="befb2-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="befb2-111">Například následující příkazy vytvoří aplikaci v adresáři *My-New-App* a přepne do tohoto adresáře:</span><span class="sxs-lookup"><span data-stu-id="befb2-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="befb2-112">Spusťte aplikaci buď ze sady Visual Studio, nebo z .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="befb2-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="befb2-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="befb2-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="befb2-114">Otevřete vygenerovaný soubor *. csproj* a z něj spusťte aplikaci jako normální.</span><span class="sxs-lookup"><span data-stu-id="befb2-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="befb2-115">Proces sestavení obnoví závislosti npm při prvním spuštění, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="befb2-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="befb2-116">Následná sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="befb2-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="befb2-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="befb2-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="befb2-118">Ujistěte se, že máte proměnnou prostředí `ASPNETCORE_Environment` s názvem s hodnotou `Development` .</span><span class="sxs-lookup"><span data-stu-id="befb2-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="befb2-119">V systému Windows (v případě výzev mimo PowerShell) spusťte příkaz `SET ASPNETCORE_Environment=Development` .</span><span class="sxs-lookup"><span data-stu-id="befb2-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="befb2-120">V systému Linux nebo macOS spusťte `export ASPNETCORE_Environment=Development` .</span><span class="sxs-lookup"><span data-stu-id="befb2-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="befb2-121">Spusťte [sestavení dotnet](/dotnet/core/tools/dotnet-build) pro správné ověření sestavení vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="befb2-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="befb2-122">Při prvním spuštění proces sestavení obnoví závislosti NPM, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="befb2-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="befb2-123">Následná sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="befb2-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="befb2-124">Spuštěním příkazu [dotnet](/dotnet/core/tools/dotnet-run) spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="befb2-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="befb2-125">Šablona projektu vytvoří aplikaci ASP.NET Core a aplikaci, která reaguje.</span><span class="sxs-lookup"><span data-stu-id="befb2-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="befb2-126">Aplikace ASP.NET Core je určena k použití pro přístup k datům, autorizaci a další aspekty na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="befb2-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="befb2-127">Aplikace reaguje, která je umístěná v podadresáři *clientapp* , je určena k použití pro všechny uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="befb2-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="befb2-128">Přidat stránky, obrázky, styly, moduly atd.</span><span class="sxs-lookup"><span data-stu-id="befb2-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="befb2-129">Adresář *clientapp* je standardní aplikace reagující na CRA.</span><span class="sxs-lookup"><span data-stu-id="befb2-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="befb2-130">Další informace najdete v oficiální [dokumentaci k CRA](https://create-react-app.dev/docs/getting-started/) .</span><span class="sxs-lookup"><span data-stu-id="befb2-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="befb2-131">Existují mírné rozdíly mezi aplikací s reakci, kterou vytvořila Tato šablona, a ta, kterou vytvořila sama CRA; Možnosti aplikace se ale nezměnily.</span><span class="sxs-lookup"><span data-stu-id="befb2-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="befb2-132">Aplikace vytvořená šablonou obsahuje rozložení založené na [bootstrap](https://getbootstrap.com/)a základní příklad směrování.</span><span class="sxs-lookup"><span data-stu-id="befb2-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="befb2-133">Instalace balíčků npm</span><span class="sxs-lookup"><span data-stu-id="befb2-133">Install npm packages</span></span>

<span data-ttu-id="befb2-134">K instalaci balíčků npm třetích stran použijte příkazový řádek v podadresáři *clientapp* .</span><span class="sxs-lookup"><span data-stu-id="befb2-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="befb2-135">Například:</span><span class="sxs-lookup"><span data-stu-id="befb2-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="befb2-136">Publikování a nasazení</span><span class="sxs-lookup"><span data-stu-id="befb2-136">Publish and deploy</span></span>

<span data-ttu-id="befb2-137">Ve vývoji se aplikace spouští v režimu optimalizovaném pro usnadnění vývoje.</span><span class="sxs-lookup"><span data-stu-id="befb2-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="befb2-138">Například sady prostředků JavaScriptu obsahují zdrojové mapy (takže při ladění uvidíte původní zdrojový kód).</span><span class="sxs-lookup"><span data-stu-id="befb2-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="befb2-139">Aplikace sleduje změny souborů JavaScriptu, HTML a CSS na disku a automaticky překompiluje a znovu načte, když se tyto soubory uvidí.</span><span class="sxs-lookup"><span data-stu-id="befb2-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="befb2-140">V produkčním prostředí poskytuje verzi vaší aplikace optimalizované pro výkon.</span><span class="sxs-lookup"><span data-stu-id="befb2-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="befb2-141">To se nakonfiguruje tak, aby se automaticky stalo.</span><span class="sxs-lookup"><span data-stu-id="befb2-141">This is configured to happen automatically.</span></span> <span data-ttu-id="befb2-142">Při publikování generuje konfigurace sestavení minifikovaného, převedený Build kódu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="befb2-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="befb2-143">Na rozdíl od sestavení pro vývoj není provozní sestavení vyžadovat instalaci Node.js na server.</span><span class="sxs-lookup"><span data-stu-id="befb2-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="befb2-144">Můžete použít standardní [ASP.NET Core hostování a metody nasazení](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="befb2-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="befb2-145">Spustit CRA Server nezávisle</span><span class="sxs-lookup"><span data-stu-id="befb2-145">Run the CRA server independently</span></span>

<span data-ttu-id="befb2-146">Projekt je nakonfigurován tak, aby spouštěl svou vlastní instanci CRA vývojového serveru na pozadí, když se aplikace ASP.NET Core spustí v režimu vývoje.</span><span class="sxs-lookup"><span data-stu-id="befb2-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="befb2-147">To je užitečné, protože to znamená, že nemusíte spouštět samostatný server ručně.</span><span class="sxs-lookup"><span data-stu-id="befb2-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="befb2-148">Tato výchozí instalace je nevýhodná.</span><span class="sxs-lookup"><span data-stu-id="befb2-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="befb2-149">Pokaždé, když upravíte kód v C# a vaše aplikace ASP.NET Core se musí restartovat, server CRA se restartuje.</span><span class="sxs-lookup"><span data-stu-id="befb2-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="befb2-150">Spuštění zálohování vyžaduje několik sekund.</span><span class="sxs-lookup"><span data-stu-id="befb2-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="befb2-151">Pokud provádíte časté úpravy kódu v jazyce C# a nechcete čekat na restartování serveru CRA, spusťte server CRA externě, nezávisle na procesu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="befb2-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="befb2-152">Postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="befb2-152">To do so:</span></span>

1. <span data-ttu-id="befb2-153">Do podadresáře *clientapp* přidejte soubor *. env* s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="befb2-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="befb2-154">Tím zabráníte otevření webového prohlížeče při externém spuštění serveru CRA.</span><span class="sxs-lookup"><span data-stu-id="befb2-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="befb2-155">V příkazovém řádku přejděte do podadresáře *clientapp* a spusťte vývojový server CRA:</span><span class="sxs-lookup"><span data-stu-id="befb2-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="befb2-156">Upravte aplikaci ASP.NET Core tak, aby používala externí instanci serveru CRA místo spuštění vlastní.</span><span class="sxs-lookup"><span data-stu-id="befb2-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="befb2-157">Ve vaší *spouštěcí* třídě nahraďte `spa.UseReactDevelopmentServer` vyvolání následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="befb2-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="befb2-158">Když spustíte aplikaci ASP.NET Core, nespustí se server CRA.</span><span class="sxs-lookup"><span data-stu-id="befb2-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="befb2-159">Místo toho se použije instance, kterou jste spustili ručně.</span><span class="sxs-lookup"><span data-stu-id="befb2-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="befb2-160">To umožňuje, aby se rychleji spouštěla a restartovala.</span><span class="sxs-lookup"><span data-stu-id="befb2-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="befb2-161">Už nečeká na opětovné sestavení vaší aplikace v reakci.</span><span class="sxs-lookup"><span data-stu-id="befb2-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="befb2-162">"Vykreslování na straně serveru" není podporovanou funkcí této šablony.</span><span class="sxs-lookup"><span data-stu-id="befb2-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="befb2-163">Naším cílem této šablony je vyhovět paritě pomocí příkazu "vytvořit reakci-aplikace".</span><span class="sxs-lookup"><span data-stu-id="befb2-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="befb2-164">Například scénáře a funkce, které nejsou součástí projektu "vytvoření reakce na aplikaci" (například SSR), nejsou podporovány a jsou pro uživatele ponechány jako cvičení.</span><span class="sxs-lookup"><span data-stu-id="befb2-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="befb2-165">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="befb2-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
