---
title: Použití šablony úhlového projektu s ASP.NET Core
author: SteveSandersonMS
description: Naučte se, jak začít pracovat s ASP.NET Core šablonou projektu s jednou stránkou (SPA) pro úhlové a úhlové rozhraní příkazového řádku.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/angular
ms.openlocfilehash: 150b2176eac2e68c1ef9ec6deabb087836ff84ce
ms.sourcegitcommit: cb6015f737b6a93127016ab0f21b58e34b624ff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "77004263"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="b44ad-103">Použití šablony úhlového projektu s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b44ad-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="b44ad-104">Aktualizovaná šablona úhlového projektu poskytuje pohodlný výchozí bod pro ASP.NET Core aplikace pomocí úhlů a úhlů CLI pro implementaci bohatě funkčního uživatelského rozhraní (UI) na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="b44ad-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="b44ad-105">Šablona je ekvivalentní k vytvoření ASP.NET Core projektu, který se má chovat jako back-end rozhraní API, a s úhlovým projektem CLI, který bude fungovat jako uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b44ad-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="b44ad-106">Šablona nabízí pohodlí hostování obou typů projektů v jednom projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b44ad-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="b44ad-107">V důsledku toho může být projekt aplikace sestaven a publikován jako jediná jednotka.</span><span class="sxs-lookup"><span data-stu-id="b44ad-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="b44ad-108">Vytvoření nové aplikace</span><span class="sxs-lookup"><span data-stu-id="b44ad-108">Create a new app</span></span>

<span data-ttu-id="b44ad-109">Pokud máte nainstalovanou ASP.NET Core 2,1, není potřeba instalovat úhlovou šablonu projektu.</span><span class="sxs-lookup"><span data-stu-id="b44ad-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="b44ad-110">Vytvoření nového projektu z příkazového řádku pomocí příkazu `dotnet new angular` v prázdném adresáři.</span><span class="sxs-lookup"><span data-stu-id="b44ad-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="b44ad-111">Například následující příkazy vytvoří aplikaci v adresáři *My-New-App* a přepne do tohoto adresáře:</span><span class="sxs-lookup"><span data-stu-id="b44ad-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="b44ad-112">Spusťte aplikaci buď ze sady Visual Studio, nebo z .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="b44ad-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b44ad-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b44ad-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="b44ad-114">Otevřete vygenerovaný soubor *. csproj* a z něj spusťte aplikaci jako normální.</span><span class="sxs-lookup"><span data-stu-id="b44ad-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="b44ad-115">Proces sestavení obnoví závislosti npm při prvním spuštění, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="b44ad-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="b44ad-116">Následná sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="b44ad-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b44ad-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b44ad-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b44ad-118">Ujistěte se, že máte proměnnou prostředí nazvanou `ASPNETCORE_Environment` s hodnotou `Development`.</span><span class="sxs-lookup"><span data-stu-id="b44ad-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="b44ad-119">V systému Windows (v případě výzev mimo PowerShell) spusťte `SET ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="b44ad-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="b44ad-120">V systému Linux nebo macOS spusťte `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="b44ad-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="b44ad-121">Spusťte [sestavení dotnet](/dotnet/core/tools/dotnet-build) a ověřte správné sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b44ad-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="b44ad-122">Při prvním spuštění proces sestavení obnoví závislosti NPM, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="b44ad-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="b44ad-123">Následná sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="b44ad-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="b44ad-124">Spuštěním příkazu [dotnet](/dotnet/core/tools/dotnet-run) spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b44ad-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="b44ad-125">Protokoluje se zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="b44ad-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="b44ad-126">V prohlížeči přejděte na tuto adresu URL.</span><span class="sxs-lookup"><span data-stu-id="b44ad-126">Navigate to this URL in a browser.</span></span>

<span data-ttu-id="b44ad-127">Aplikace spustí instanci úhlového CLI serveru na pozadí.</span><span class="sxs-lookup"><span data-stu-id="b44ad-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="b44ad-128">Protokoluje se zpráva podobná následující: *NG živý vývojový server naslouchá na localhost:&lt;otherport&gt;otevřete v prohlížeči http://localhost:&lt otherport&gt;/* .</span><span class="sxs-lookup"><span data-stu-id="b44ad-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open your browser on http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="b44ad-129">Tuto zprávu ignorujte&mdash;nejedná **se o** adresu URL pro kombinované ASP.NET Core a úhlové aplikace CLI.</span><span class="sxs-lookup"><span data-stu-id="b44ad-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="b44ad-130">Šablona projektu vytvoří aplikaci ASP.NET Core a úhlovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b44ad-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="b44ad-131">Aplikace ASP.NET Core je určena k použití pro přístup k datům, autorizaci a další aspekty na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="b44ad-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="b44ad-132">Úhlová aplikace, která je umístěná v podadresáři *clientapp* , má být použita pro všechny aspekty uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b44ad-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="b44ad-133">Přidat stránky, obrázky, styly, moduly atd.</span><span class="sxs-lookup"><span data-stu-id="b44ad-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="b44ad-134">Adresář *clientapp* obsahuje standardní aplikaci s úhlovými CLI.</span><span class="sxs-lookup"><span data-stu-id="b44ad-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="b44ad-135">Další informace najdete v oficiálních [úhlových dokumentaci](https://https://angular.io) .</span><span class="sxs-lookup"><span data-stu-id="b44ad-135">See the official [Angular documentation](https://https://angular.io) for more information.</span></span>

<span data-ttu-id="b44ad-136">Existují mírné rozdíly mezi úhlovými aplikacemi vytvořenými touto šablonou a šablonou vytvořenou pomocí úhlů CLI (prostřednictvím `ng new`); Možnosti aplikace se ale nezměnily.</span><span class="sxs-lookup"><span data-stu-id="b44ad-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="b44ad-137">Aplikace vytvořená šablonou obsahuje rozložení založené na [bootstrap](https://getbootstrap.com/)a základní příklad směrování.</span><span class="sxs-lookup"><span data-stu-id="b44ad-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="b44ad-138">Spustit příkazy NG</span><span class="sxs-lookup"><span data-stu-id="b44ad-138">Run ng commands</span></span>

<span data-ttu-id="b44ad-139">V příkazovém řádku přejděte do podadresáře *clientapp* :</span><span class="sxs-lookup"><span data-stu-id="b44ad-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="b44ad-140">Pokud máte nástroj `ng` nainstalovaný globálně, můžete spustit libovolný z jeho příkazů.</span><span class="sxs-lookup"><span data-stu-id="b44ad-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="b44ad-141">Můžete například spustit `ng lint`, `ng test`nebo kterýkoli z dalších [úhlů příkazů CLI](https://angular.io/cli).</span><span class="sxs-lookup"><span data-stu-id="b44ad-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="b44ad-142">Nemusíte spouštět `ng serve` ale, protože vaše aplikace ASP.NET Core se zabývá poskytováním součástí aplikace na straně serveru i na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="b44ad-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="b44ad-143">Interně používá `ng serve` při vývoji.</span><span class="sxs-lookup"><span data-stu-id="b44ad-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="b44ad-144">Pokud nemáte nainstalovaný nástroj `ng`, spusťte místo toho `npm run ng`.</span><span class="sxs-lookup"><span data-stu-id="b44ad-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="b44ad-145">Můžete například spustit `npm run ng lint` nebo `npm run ng test`.</span><span class="sxs-lookup"><span data-stu-id="b44ad-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="b44ad-146">Instalace balíčků npm</span><span class="sxs-lookup"><span data-stu-id="b44ad-146">Install npm packages</span></span>

<span data-ttu-id="b44ad-147">K instalaci balíčků npm třetích stran použijte příkazový řádek v podadresáři *clientapp* .</span><span class="sxs-lookup"><span data-stu-id="b44ad-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="b44ad-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b44ad-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="b44ad-149">Publikování a nasazení</span><span class="sxs-lookup"><span data-stu-id="b44ad-149">Publish and deploy</span></span>

<span data-ttu-id="b44ad-150">Ve vývoji se aplikace spouští v režimu optimalizovaném pro usnadnění vývoje.</span><span class="sxs-lookup"><span data-stu-id="b44ad-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="b44ad-151">Například sady prostředků JavaScriptu obsahují zdrojové mapy (takže při ladění uvidíte původní kód TypeScriptu).</span><span class="sxs-lookup"><span data-stu-id="b44ad-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="b44ad-152">Aplikace sleduje změny souborů TypeScriptu, HTML a CSS na disku a automaticky znovu zkompiluje a znovu načte, když se tyto soubory uvidí.</span><span class="sxs-lookup"><span data-stu-id="b44ad-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="b44ad-153">V produkčním prostředí poskytuje verzi vaší aplikace optimalizované pro výkon.</span><span class="sxs-lookup"><span data-stu-id="b44ad-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="b44ad-154">To se nakonfiguruje tak, aby se automaticky stalo.</span><span class="sxs-lookup"><span data-stu-id="b44ad-154">This is configured to happen automatically.</span></span> <span data-ttu-id="b44ad-155">Když publikujete, konfigurace sestavení generuje minifikovaného sestavení zkompilovaného kódu na straně klienta (AoT).</span><span class="sxs-lookup"><span data-stu-id="b44ad-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="b44ad-156">Na rozdíl od sestavení pro vývoj není v produkčním sestavení nutné nainstalovat Node. js na server (Pokud jste nepovolili vykreslování na straně serveru (SSR)).</span><span class="sxs-lookup"><span data-stu-id="b44ad-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="b44ad-157">Můžete použít standardní [ASP.NET Core hostování a metody nasazení](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="b44ad-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="b44ad-158">Spustit "NG obsluhu" nezávisle</span><span class="sxs-lookup"><span data-stu-id="b44ad-158">Run "ng serve" independently</span></span>

<span data-ttu-id="b44ad-159">Projekt je nakonfigurován tak, aby spouštěl svou vlastní instanci serveru úhlů CLI na pozadí při spuštění aplikace ASP.NET Core v režimu vývoje.</span><span class="sxs-lookup"><span data-stu-id="b44ad-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="b44ad-160">To je vhodné, protože nemusíte spouštět samostatný server ručně.</span><span class="sxs-lookup"><span data-stu-id="b44ad-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="b44ad-161">Tato výchozí instalace je nevýhodná.</span><span class="sxs-lookup"><span data-stu-id="b44ad-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="b44ad-162">Pokaždé, když upravíte C# kód a ASP.NET Core aplikace se musí restartovat, dojde k restartování serveru s úhlovým CLI.</span><span class="sxs-lookup"><span data-stu-id="b44ad-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="b44ad-163">Pro spuštění zálohování je nutné asi 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="b44ad-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="b44ad-164">Pokud provádíte časté C# úpravy kódu a nechcete čekat na restartování úhlového CLI, spusťte server úhlového CLI externě, nezávisle na procesu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b44ad-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="b44ad-165">Postup:</span><span class="sxs-lookup"><span data-stu-id="b44ad-165">To do so:</span></span>

1. <span data-ttu-id="b44ad-166">V příkazovém řádku přejděte do podadresáře *clientapp* a spusťte vývojový server s úhlovými CLI:</span><span class="sxs-lookup"><span data-stu-id="b44ad-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="b44ad-167">Pomocí `npm start` můžete spustit vývojový server úhlového CLI, který není `ng serve`, aby byla dodržena konfigurace v *Package. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b44ad-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="b44ad-168">K předání dalších parametrů serveru úhlového CLI je přidejte do příslušného `scripts` řádku v souboru *Package. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b44ad-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="b44ad-169">Upravte aplikaci ASP.NET Core tak, aby používala vnější instanci úhlů CLI místo spuštění vlastní.</span><span class="sxs-lookup"><span data-stu-id="b44ad-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="b44ad-170">Ve vaší *spouštěcí* třídě nahraďte `spa.UseAngularCliServer` vyvolání následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="b44ad-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="b44ad-171">Když spustíte aplikaci ASP.NET Core, nespustí se server úhlů CLI.</span><span class="sxs-lookup"><span data-stu-id="b44ad-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="b44ad-172">Místo toho se použije instance, kterou jste spustili ručně.</span><span class="sxs-lookup"><span data-stu-id="b44ad-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="b44ad-173">To umožňuje, aby se rychleji spouštěla a restartovala.</span><span class="sxs-lookup"><span data-stu-id="b44ad-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="b44ad-174">Nečeká se na to, že rozhraní příkazového řádku pro každou chvíli znovu sestaví klientskou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b44ad-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="b44ad-175">Předání dat z kódu .NET do kódu TypeScript</span><span class="sxs-lookup"><span data-stu-id="b44ad-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="b44ad-176">Během služby SSR budete možná chtít předat data z vaší aplikace ASP.NET Core do své aplikace v úhlovém požadavku.</span><span class="sxs-lookup"><span data-stu-id="b44ad-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="b44ad-177">Můžete třeba předat informace cookie nebo něco přečíst z databáze.</span><span class="sxs-lookup"><span data-stu-id="b44ad-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="b44ad-178">Provedete to tak, že upravíte třídu *Startup* .</span><span class="sxs-lookup"><span data-stu-id="b44ad-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="b44ad-179">Ve zpětném volání pro `UseSpaPrerendering`nastavte hodnotu pro `options.SupplyData`, například následující:</span><span class="sxs-lookup"><span data-stu-id="b44ad-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="b44ad-180">`SupplyData` zpětné volání umožňuje předat libovolná data, která jsou serializovatelný s hodnotou JSON (například řetězce, logické hodnoty nebo čísla).</span><span class="sxs-lookup"><span data-stu-id="b44ad-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="b44ad-181">Váš *hlavní kód. Server. TS* obdrží tuto `params.data`.</span><span class="sxs-lookup"><span data-stu-id="b44ad-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="b44ad-182">Například předchozí ukázka kódu předává logickou hodnotu jako `params.data.isHttpsRequest` do zpětného volání `createServerRenderer`.</span><span class="sxs-lookup"><span data-stu-id="b44ad-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="b44ad-183">Tuto možnost můžete předat ostatním částem aplikace jakýmkoli způsobem podporovaným úhlovým.</span><span class="sxs-lookup"><span data-stu-id="b44ad-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="b44ad-184">Například můžete zjistit, jak *hlavní. Server. TS* předá hodnotu `BASE_URL` do jakékoli komponenty, jejíž konstruktor je deklarován pro příjem.</span><span class="sxs-lookup"><span data-stu-id="b44ad-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="b44ad-185">Nevýhody SSR</span><span class="sxs-lookup"><span data-stu-id="b44ad-185">Drawbacks of SSR</span></span>

<span data-ttu-id="b44ad-186">Ne všechny aplikace využívají SSR.</span><span class="sxs-lookup"><span data-stu-id="b44ad-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="b44ad-187">Hlavní výhodou je vnímaný výkon.</span><span class="sxs-lookup"><span data-stu-id="b44ad-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="b44ad-188">Návštěvníci, kteří dosáhnou vaší aplikace přes pomalé připojení k síti nebo pomalá mobilní zařízení, uvidí počáteční uživatelské rozhraní rychle, a to i v případě, že nějakou dobu trvá a načítají nebo analyzují sady JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b44ad-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="b44ad-189">Mnohé jednostránkové se ale používají hlavně v rychlé interní síti společnosti na rychlých počítačích, kde se aplikace zobrazuje skoro okamžitě.</span><span class="sxs-lookup"><span data-stu-id="b44ad-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="b44ad-190">Ve stejnou chvíli existují významné nevýhody povolování SSR.</span><span class="sxs-lookup"><span data-stu-id="b44ad-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="b44ad-191">Přináší složitost vašemu procesu vývoje.</span><span class="sxs-lookup"><span data-stu-id="b44ad-191">It adds complexity to your development process.</span></span> <span data-ttu-id="b44ad-192">Váš kód musí běžet ve dvou různých prostředích: na straně klienta a na straně serveru (v prostředí Node. js vyvolaném z ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="b44ad-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="b44ad-193">Tady je několik věcí, které je potřeba mít na paměti:</span><span class="sxs-lookup"><span data-stu-id="b44ad-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="b44ad-194">SSR vyžaduje instalaci Node. js na produkčních serverech.</span><span class="sxs-lookup"><span data-stu-id="b44ad-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="b44ad-195">Toto je automaticky pro některé scénáře nasazení, jako je například Azure App Services, ale ne pro jiné, jako je například Azure Service Fabric.</span><span class="sxs-lookup"><span data-stu-id="b44ad-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="b44ad-196">Povolení příznaku `BuildServerSideRenderer` sestavení způsobí, že se váš adresář *node_modules* publikovat.</span><span class="sxs-lookup"><span data-stu-id="b44ad-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="b44ad-197">Tato složka obsahuje 20 000 souborů, což zvyšuje dobu nasazení.</span><span class="sxs-lookup"><span data-stu-id="b44ad-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="b44ad-198">Chcete-li spustit kód v prostředí Node. js, nemůže spoléhat na existenci rozhraní JavaScript API specifických pro prohlížeč, například `window` nebo `localStorage`.</span><span class="sxs-lookup"><span data-stu-id="b44ad-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="b44ad-199">Pokud váš kód (nebo některá z knihoven třetích stran, na které odkazujete) se pokusí použít tato rozhraní API, během průběhu SSR se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="b44ad-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="b44ad-200">Nepoužívejte například jQuery, protože odkazuje na rozhraní API specifická pro prohlížeč na mnoha místech.</span><span class="sxs-lookup"><span data-stu-id="b44ad-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="b44ad-201">Aby nedocházelo k chybám, musíte buď zabránit SSR nebo nejenom k rozhraní API nebo knihovnám specifickým pro prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="b44ad-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="b44ad-202">V rámci kontrol můžete zabalit jakákoli volání těchto rozhraní API, aby se zajistilo, že se nebudou vyvolávat během SSR.</span><span class="sxs-lookup"><span data-stu-id="b44ad-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="b44ad-203">Použijte například následující příkaz v kódu JavaScript nebo TypeScript:</span><span class="sxs-lookup"><span data-stu-id="b44ad-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="b44ad-204">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b44ad-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
