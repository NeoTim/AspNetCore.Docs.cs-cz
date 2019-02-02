---
title: Šablona projektu React pomocí ASP.NET Core
author: SteveSandersonMS
description: Zjistěte, jak začít pracovat s ASP.NET Core jedné stránky aplikace (SPA) šablona projektu pro React a vytvořit aplikaci react.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
uid: spa/react
ms.openlocfilehash: d83bff8abcd5b59d8bc4a51a101510755394f0c4
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667684"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="55334-103">Šablona projektu React pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55334-103">Use the React project template with ASP.NET Core</span></span>

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> <span data-ttu-id="55334-104">Tato dokumentace se o šablona projektu React součástí ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="55334-104">This documentation isn't about the React project template included in ASP.NET Core 2.0.</span></span> <span data-ttu-id="55334-105">Jde o šabloně novější React, do kterého můžete ručně aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="55334-105">It's about the newer React template to which you can update manually.</span></span> <span data-ttu-id="55334-106">Šablona je zahrnuta v ASP.NET Core 2.1 ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="55334-106">The template is included in ASP.NET Core 2.1 by default.</span></span>

::: moniker-end

<span data-ttu-id="55334-107">Aktualizovaná šablona projektu React poskytuje příhodný výchozí bod pro ASP.NET Core aplikace pomocí React a [vytvořit aplikaci react](https://github.com/facebookincubator/create-react-app) konvence (CRA) k implementaci bohatě vybaveným a na straně klienta uživatelské rozhraní (UI).</span><span class="sxs-lookup"><span data-stu-id="55334-107">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="55334-108">Šablona je ekvivalentní k vytváření projektu aplikace ASP.NET Core tak, aby fungoval jako back-endu rozhraní API a standardní CRA React projekt tak, aby fungoval jako uživatelské rozhraní, ale se snadným ovládáním hostování i v projektu aplikace s jedním, který se dají vytvořit a publikovat jako jeden celek.</span><span class="sxs-lookup"><span data-stu-id="55334-108">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="55334-109">Vytvoření nové aplikace</span><span class="sxs-lookup"><span data-stu-id="55334-109">Create a new app</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="55334-110">Pokud používáte ASP.NET Core 2.0, zkontrolujte, že máte [nainstalované aktualizovaná šablona projektu React](xref:spa/index#installation).</span><span class="sxs-lookup"><span data-stu-id="55334-110">If using ASP.NET Core 2.0, ensure you've [installed the updated React project template](xref:spa/index#installation).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="55334-111">Pokud máte ASP.NET Core 2.1 nainstalovaný, není nutné k instalaci šablona projektu React.</span><span class="sxs-lookup"><span data-stu-id="55334-111">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

::: moniker-end

<span data-ttu-id="55334-112">Vytvoření nového projektu z příkazového řádku pomocí příkazu `dotnet new react` v prázdném adresáři.</span><span class="sxs-lookup"><span data-stu-id="55334-112">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="55334-113">Například následující příkazy vytvoří aplikaci *my nové app* adresáře a přepnete se do tohoto adresáře:</span><span class="sxs-lookup"><span data-stu-id="55334-113">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```console
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="55334-114">Spuštění aplikace Visual Studio nebo .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="55334-114">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="55334-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55334-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="55334-116">Otevřete vygenerovaný *.csproj* souboru a spuštění aplikace jako za normálních okolností z něj.</span><span class="sxs-lookup"><span data-stu-id="55334-116">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="55334-117">Proces sestavení obnoví závislosti npm při prvním spuštění, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="55334-117">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="55334-118">Následující sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="55334-118">Subsequent builds are much faster.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="55334-119">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="55334-119">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="55334-120">Ujistěte se, máte proměnnou prostředí volá `ASPNETCORE_Environment` s hodnotou `Development`.</span><span class="sxs-lookup"><span data-stu-id="55334-120">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="55334-121">Na Windows (v výzev – prostředí PowerShell), spusťte `SET ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="55334-121">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="55334-122">V systému macOS nebo Linux spusťte `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="55334-122">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="55334-123">Spustit [dotnet sestavení](/dotnet/core/tools/dotnet-build) správně k ověření aplikace sestavena.</span><span class="sxs-lookup"><span data-stu-id="55334-123">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="55334-124">Při prvním spuštění procesu sestavení obnoví závislosti na npm, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="55334-124">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="55334-125">Následující sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="55334-125">Subsequent builds are much faster.</span></span>

<span data-ttu-id="55334-126">Spustit [dotnet spustit](/dotnet/core/tools/dotnet-run) a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="55334-126">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="55334-127">Šablona projektu vytvoří aplikace ASP.NET Core a aplikace s React.</span><span class="sxs-lookup"><span data-stu-id="55334-127">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="55334-128">Aplikace ASP.NET Core je určena pro použití pro přístup k datům, autorizaci a další aspekty na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="55334-128">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="55334-129">Aplikace React, které se nacházejí v *ClientApp* podadresář, je určena pro použití pro všechny aspekty uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="55334-129">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="55334-130">Přidání stránek, obrázků, styly, moduly, atd.</span><span class="sxs-lookup"><span data-stu-id="55334-130">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="55334-131">*ClientApp* adresář je standardní CRA reakce aplikace.</span><span class="sxs-lookup"><span data-stu-id="55334-131">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="55334-132">Najdete v oficiální [CRA dokumentaci](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) Další informace.</span><span class="sxs-lookup"><span data-stu-id="55334-132">See the official [CRA documentation](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) for more information.</span></span>

<span data-ttu-id="55334-133">Existují mírné rozdíly mezi React aplikace vytvořené pomocí této šablony a vytvořil CRA sama o sobě; Nicméně jsou schopnosti aplikace beze změny.</span><span class="sxs-lookup"><span data-stu-id="55334-133">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="55334-134">Obsahuje aplikaci vytvořenou pomocí šablony [Bootstrap](https://getbootstrap.com/)– na základě rozložení a základní příklad směrování.</span><span class="sxs-lookup"><span data-stu-id="55334-134">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="55334-135">Instalace balíčků npm</span><span class="sxs-lookup"><span data-stu-id="55334-135">Install npm packages</span></span>

<span data-ttu-id="55334-136">Pokud chcete nainstalovat balíčky npm třetích stran, použijte příkazový řádek v *ClientApp* podadresáře.</span><span class="sxs-lookup"><span data-stu-id="55334-136">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="55334-137">Příklad:</span><span class="sxs-lookup"><span data-stu-id="55334-137">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="55334-138">Publikování a nasazení</span><span class="sxs-lookup"><span data-stu-id="55334-138">Publish and deploy</span></span>

<span data-ttu-id="55334-139">Při vývoji se aplikace běží v režimu optimalizované pro usnadnění práce vývojářů.</span><span class="sxs-lookup"><span data-stu-id="55334-139">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="55334-140">Například jazyka JavaScript sady obsahují zdrojových mapování (tak, aby při ladění, se zobrazí původní zdrojový kód).</span><span class="sxs-lookup"><span data-stu-id="55334-140">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="55334-141">Aplikace sleduje jazyka JavaScript, HTML a CSS změny souborů na disku a automaticky se znovu zkompiluje a znovu načte, když vidí tyto soubory změnit.</span><span class="sxs-lookup"><span data-stu-id="55334-141">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="55334-142">V produkčním prostředí sloužit verzi vaší aplikace, které je optimalizované pro výkon.</span><span class="sxs-lookup"><span data-stu-id="55334-142">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="55334-143">Ta se nakonfiguruje, která se provede automaticky.</span><span class="sxs-lookup"><span data-stu-id="55334-143">This is configured to happen automatically.</span></span> <span data-ttu-id="55334-144">Když publikujete, konfigurace sestavení generuje minifikovaný, transpiled sestavení vašeho kódu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="55334-144">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="55334-145">Na rozdíl od sestavení vývoj nevyžaduje produkční build Node.js nainstalovaný na serveru.</span><span class="sxs-lookup"><span data-stu-id="55334-145">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="55334-146">Můžete použít standardní [metody hostování a nasazení ASP.NET Core](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="55334-146">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="55334-147">Spusťte CRA server nezávisle na sobě</span><span class="sxs-lookup"><span data-stu-id="55334-147">Run the CRA server independently</span></span>

<span data-ttu-id="55334-148">Projekt je nakonfigurován ke spuštění svoji vlastní instanci vývojový server sady CRA na pozadí při spuštění v režimu pro vývoj aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="55334-148">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="55334-149">Je to vhodné, protože to znamená, že není nutné ručně spustit na samostatný server.</span><span class="sxs-lookup"><span data-stu-id="55334-149">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="55334-150">Nevýhodou této výchozí nastavení není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="55334-150">There's a drawback to this default setup.</span></span> <span data-ttu-id="55334-151">Pokaždé, když změníte kód jazyka C# a vaše ASP.NET Core, které aplikace potřebuje k restartování, CRA server se restartuje.</span><span class="sxs-lookup"><span data-stu-id="55334-151">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="55334-152">Pár sekund jsou vyžadovány pro spuštění zálohování.</span><span class="sxs-lookup"><span data-stu-id="55334-152">A few seconds are required to start back up.</span></span> <span data-ttu-id="55334-153">Pokud vytváříte časté úpravy kódu jazyka C# a nechcete čekat, CRA server restartovat, spusťte server CRA externě, nezávisle na procesu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="55334-153">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="55334-154">Postup:</span><span class="sxs-lookup"><span data-stu-id="55334-154">To do so:</span></span>

1. <span data-ttu-id="55334-155">Přidat *.env* do souboru *ClientApp* podadresář s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="55334-155">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```
    
    <span data-ttu-id="55334-156">Tím zabráníte ve webovém prohlížeči otevřít při spuštění serveru CRA externě.</span><span class="sxs-lookup"><span data-stu-id="55334-156">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="55334-157">V příkazovém řádku přejděte *ClientApp* podadresáře a spusťte vývojový server sady CRA:</span><span class="sxs-lookup"><span data-stu-id="55334-157">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="55334-158">Upravte aplikace ASP.NET Core pro použití externího instance serveru CRA namísto jeho vlastní spuštění.</span><span class="sxs-lookup"><span data-stu-id="55334-158">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="55334-159">Ve vaší *spuštění* třídy, nahraďte `spa.UseReactDevelopmentServer` vyvolání následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="55334-159">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="55334-160">Při spuštění aplikace ASP.NET Core se nespustí CRA serveru.</span><span class="sxs-lookup"><span data-stu-id="55334-160">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="55334-161">Místo toho se používá instanci, kterou jste spustili ručně.</span><span class="sxs-lookup"><span data-stu-id="55334-161">The instance you started manually is used instead.</span></span> <span data-ttu-id="55334-162">To umožňuje spuštění a restartování rychleji.</span><span class="sxs-lookup"><span data-stu-id="55334-162">This enables it to start and restart faster.</span></span> <span data-ttu-id="55334-163">Už čeká na opětovné sestavení pokaždé, když v aplikaci React.</span><span class="sxs-lookup"><span data-stu-id="55334-163">It's no longer waiting for your React app to rebuild each time.</span></span>
