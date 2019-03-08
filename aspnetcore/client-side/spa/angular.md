---
title: Šablona projektu Angular s ASP.NET Core
author: SteveSandersonMS
description: Zjistěte, jak začít pracovat se šablonou projektu ASP.NET Core jedné stránky aplikace (SPA) zaujetí pro Angular a Angular CLI.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/angular
ms.openlocfilehash: 6d0107ef52d63a0f6f5713c518ddc54ac4230d53
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665597"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="530ea-103">Šablona projektu Angular s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="530ea-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="530ea-104">Šablona projektu aktualizované Angular poskytuje příhodný výchozí bod pro ASP.NET Core pomocí Angular a Angular CLI k implementaci bohatě vybaveným a na straně klienta uživatelské rozhraní (UI) aplikace.</span><span class="sxs-lookup"><span data-stu-id="530ea-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="530ea-105">Šablona je ekvivalentní k vytváření projektu aplikace ASP.NET Core tak, aby fungoval jako back-endu rozhraní API a projekt Angular CLI tak, aby fungoval jako uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="530ea-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="530ea-106">Šablona nabízí praktické hostování oba typy projektů v projektu aplikace s jedním.</span><span class="sxs-lookup"><span data-stu-id="530ea-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="530ea-107">V důsledku toho projekt aplikace dají vytvořit a publikovat jako jeden celek.</span><span class="sxs-lookup"><span data-stu-id="530ea-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="530ea-108">Vytvoření nové aplikace</span><span class="sxs-lookup"><span data-stu-id="530ea-108">Create a new app</span></span>

<span data-ttu-id="530ea-109">Pokud máte ASP.NET Core 2.1 nainstalovaný, není nutné k instalaci šablona projektu Angular.</span><span class="sxs-lookup"><span data-stu-id="530ea-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="530ea-110">Vytvoření nového projektu z příkazového řádku pomocí příkazu `dotnet new angular` v prázdném adresáři.</span><span class="sxs-lookup"><span data-stu-id="530ea-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="530ea-111">Například následující příkazy vytvoří aplikaci *my nové app* adresáře a přepnete se do tohoto adresáře:</span><span class="sxs-lookup"><span data-stu-id="530ea-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```console
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="530ea-112">Spuštění aplikace Visual Studio nebo .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="530ea-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="530ea-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="530ea-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="530ea-114">Otevřete vygenerovaný *.csproj* souboru a spuštění aplikace jako za normálních okolností z něj.</span><span class="sxs-lookup"><span data-stu-id="530ea-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="530ea-115">Proces sestavení obnoví závislosti npm při prvním spuštění, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="530ea-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="530ea-116">Následující sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="530ea-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="530ea-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="530ea-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="530ea-118">Ujistěte se, máte proměnnou prostředí volá `ASPNETCORE_Environment` s hodnotou `Development`.</span><span class="sxs-lookup"><span data-stu-id="530ea-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="530ea-119">Na Windows (v výzev – prostředí PowerShell), spusťte `SET ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="530ea-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="530ea-120">V systému macOS nebo Linux spusťte `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="530ea-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="530ea-121">Spustit [dotnet sestavení](/dotnet/core/tools/dotnet-build) správně k ověření aplikace sestavena.</span><span class="sxs-lookup"><span data-stu-id="530ea-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="530ea-122">Při prvním spuštění procesu sestavení obnoví závislosti na npm, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="530ea-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="530ea-123">Následující sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="530ea-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="530ea-124">Spustit [dotnet spustit](/dotnet/core/tools/dotnet-run) a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="530ea-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="530ea-125">Bude zaznamenána zpráva podobná této:</span><span class="sxs-lookup"><span data-stu-id="530ea-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="530ea-126">Přejděte na tuto adresu URL v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="530ea-126">Navigate to this URL in a browser.</span></span>

<span data-ttu-id="530ea-127">Spuštění aplikace instance serveru Angular CLI na pozadí.</span><span class="sxs-lookup"><span data-stu-id="530ea-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="530ea-128">Bude zaznamenána zpráva podobná této: *NG Live vývojový Server naslouchá na localhost:&lt;otherport&gt;, otevřete prohlížeč na http://localhost:&lt; otherport&gt;/*.</span><span class="sxs-lookup"><span data-stu-id="530ea-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open your browser on http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="530ea-129">Tuto zprávu ignorovat&mdash;má **není** adresu URL pro kombinované aplikace ASP.NET Core a Angular CLI.</span><span class="sxs-lookup"><span data-stu-id="530ea-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="530ea-130">Šablona projektu vytvoří aplikace ASP.NET Core a aplikaci Angular.</span><span class="sxs-lookup"><span data-stu-id="530ea-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="530ea-131">Aplikace ASP.NET Core je určena pro použití pro přístup k datům, autorizaci a další aspekty na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="530ea-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="530ea-132">Aplikaci Angular, které se nacházejí v *ClientApp* podadresář, je určena pro použití pro všechny aspekty uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="530ea-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="530ea-133">Přidání stránek, obrázků, styly, moduly, atd.</span><span class="sxs-lookup"><span data-stu-id="530ea-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="530ea-134">*ClientApp* adresář obsahuje standardní aplikaci Angular CLI.</span><span class="sxs-lookup"><span data-stu-id="530ea-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="530ea-135">Najdete v oficiální [Angular dokumentaci](https://github.com/angular/angular-cli/wiki) Další informace.</span><span class="sxs-lookup"><span data-stu-id="530ea-135">See the official [Angular documentation](https://github.com/angular/angular-cli/wiki) for more information.</span></span>

<span data-ttu-id="530ea-136">Existují mírné rozdíly mezi aplikaci Angular, které jsou vytvořené pomocí této šablony a vytvořeny pomocí Angular CLI (prostřednictvím `ng new`); nicméně jsou funkcí aplikace beze změny.</span><span class="sxs-lookup"><span data-stu-id="530ea-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="530ea-137">Obsahuje aplikaci vytvořenou pomocí šablony [Bootstrap](https://getbootstrap.com/)– na základě rozložení a základní příklad směrování.</span><span class="sxs-lookup"><span data-stu-id="530ea-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="530ea-138">Spusťte příkazy ng</span><span class="sxs-lookup"><span data-stu-id="530ea-138">Run ng commands</span></span>

<span data-ttu-id="530ea-139">V příkazovém řádku přejděte *ClientApp* podadresáři:</span><span class="sxs-lookup"><span data-stu-id="530ea-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="530ea-140">Pokud máte `ng` globálně nainstalovaný nástroj, můžete spustit některý z jeho příkazů.</span><span class="sxs-lookup"><span data-stu-id="530ea-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="530ea-141">Například můžete spustit `ng lint`, `ng test`, ani žádný z nich [Angular CLI příkazy](https://github.com/angular/angular-cli/wiki#additional-commands).</span><span class="sxs-lookup"><span data-stu-id="530ea-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://github.com/angular/angular-cli/wiki#additional-commands).</span></span> <span data-ttu-id="530ea-142">Není nutné ke spuštění `ng serve` , protože vaše aplikace ASP.NET Core se zabývá obsluhující serverové a klientské součásti vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="530ea-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="530ea-143">Interně používá `ng serve` ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="530ea-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="530ea-144">Pokud nemáte k dispozici `ng` nástroj nainstalovali, spusťte `npm run ng` místo.</span><span class="sxs-lookup"><span data-stu-id="530ea-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="530ea-145">Například můžete spustit `npm run ng lint` nebo `npm run ng test`.</span><span class="sxs-lookup"><span data-stu-id="530ea-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="530ea-146">Instalace balíčků npm</span><span class="sxs-lookup"><span data-stu-id="530ea-146">Install npm packages</span></span>

<span data-ttu-id="530ea-147">Pokud chcete nainstalovat balíčky npm třetích stran, použijte příkazový řádek v *ClientApp* podadresáře.</span><span class="sxs-lookup"><span data-stu-id="530ea-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="530ea-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="530ea-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="530ea-149">Publikování a nasazení</span><span class="sxs-lookup"><span data-stu-id="530ea-149">Publish and deploy</span></span>

<span data-ttu-id="530ea-150">Při vývoji se aplikace běží v režimu optimalizované pro usnadnění práce vývojářů.</span><span class="sxs-lookup"><span data-stu-id="530ea-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="530ea-151">Například jazyka JavaScript sady obsahují zdrojových mapování (tak, aby při ladění, zobrazí se váš původním kód TypeScript).</span><span class="sxs-lookup"><span data-stu-id="530ea-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="530ea-152">Aplikace sleduje TypeScript, HTML a CSS změny souborů na disku a automaticky se znovu zkompiluje a znovu načte, když vidí tyto soubory změnit.</span><span class="sxs-lookup"><span data-stu-id="530ea-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="530ea-153">V produkčním prostředí sloužit verzi vaší aplikace, které je optimalizované pro výkon.</span><span class="sxs-lookup"><span data-stu-id="530ea-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="530ea-154">Ta se nakonfiguruje, která se provede automaticky.</span><span class="sxs-lookup"><span data-stu-id="530ea-154">This is configured to happen automatically.</span></span> <span data-ttu-id="530ea-155">Když publikujete, konfigurace sestavení generuje minifikovaný, ahead-of-time (AoT) zkompilován sestavení kódu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="530ea-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="530ea-156">Na rozdíl od sestavení vývoj nevyžaduje produkční build Node.js k instalaci na serveru (Pokud jste povolili vykreslování na straně serveru (SSR)).</span><span class="sxs-lookup"><span data-stu-id="530ea-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="530ea-157">Můžete použít standardní [metody hostování a nasazení ASP.NET Core](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="530ea-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="530ea-158">Spuštění "ng slouží" nezávisle na sobě</span><span class="sxs-lookup"><span data-stu-id="530ea-158">Run "ng serve" independently</span></span>

<span data-ttu-id="530ea-159">Projekt je nakonfigurován ke spuštění svoji vlastní instanci serveru Angular CLI na pozadí při spuštění v režimu pro vývoj aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="530ea-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="530ea-160">To je vhodné, protože není nutné ručně spustit na samostatný server.</span><span class="sxs-lookup"><span data-stu-id="530ea-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="530ea-161">Nevýhodou této výchozí nastavení není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="530ea-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="530ea-162">Pokaždé, když změníte kód jazyka C# a vaše ASP.NET Core, které aplikace potřebuje k restartování, Angular CLI server se restartuje.</span><span class="sxs-lookup"><span data-stu-id="530ea-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="530ea-163">Přibližně 10 sekund je potřebná ke spuštění zálohování.</span><span class="sxs-lookup"><span data-stu-id="530ea-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="530ea-164">Pokud vytváříte časté úpravy kódu jazyka C# a nechcete čekat na restartování, Angular CLI spustit server Angular CLI externě, nezávisle na procesu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="530ea-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="530ea-165">Postup:</span><span class="sxs-lookup"><span data-stu-id="530ea-165">To do so:</span></span>

1. <span data-ttu-id="530ea-166">V příkazovém řádku přejděte *ClientApp* podadresáře a spusťte vývojový server sady Angular CLI:</span><span class="sxs-lookup"><span data-stu-id="530ea-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="530ea-167">Použití `npm start` nelze spustit vývojový server Angular CLI, `ng serve`tak, aby konfigurace v *package.json* je dodržena.</span><span class="sxs-lookup"><span data-stu-id="530ea-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="530ea-168">Chcete-li předat další parametry serveru Angular CLI, přidejte ho do příslušné `scripts` řádku v vaše *package.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="530ea-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="530ea-169">Upravte aplikace ASP.NET Core pro použití namísto spuštění jednu vlastní instance externího Angular CLI.</span><span class="sxs-lookup"><span data-stu-id="530ea-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="530ea-170">Ve vaší *spuštění* třídy, nahraďte `spa.UseAngularCliServer` vyvolání následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="530ea-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="530ea-171">Při spuštění aplikace ASP.NET Core se nespustí serveru Angular CLI.</span><span class="sxs-lookup"><span data-stu-id="530ea-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="530ea-172">Místo toho se používá instanci, kterou jste spustili ručně.</span><span class="sxs-lookup"><span data-stu-id="530ea-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="530ea-173">To umožňuje spuštění a restartování rychleji.</span><span class="sxs-lookup"><span data-stu-id="530ea-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="530ea-174">Se už čeká Angular CLI sestavení pokaždé, když klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="530ea-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="530ea-175">Předání dat z kódu .NET do kódu TypeScript</span><span class="sxs-lookup"><span data-stu-id="530ea-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="530ea-176">Během SSR může být vhodné k předávání dat na žádost z vaší aplikace ASP.NET Core do aplikace pro Angular.</span><span class="sxs-lookup"><span data-stu-id="530ea-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="530ea-177">Například může předat informace o souboru cookie nebo něco čtení z databáze.</span><span class="sxs-lookup"><span data-stu-id="530ea-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="530ea-178">Chcete-li to provést, upravte vaše *spuštění* třídy.</span><span class="sxs-lookup"><span data-stu-id="530ea-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="530ea-179">Při zpětném volání pro `UseSpaPrerendering`, nastavte hodnotu pro `options.SupplyData` jako je následující:</span><span class="sxs-lookup"><span data-stu-id="530ea-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="530ea-180">`SupplyData` Zpětného volání vám umožní předat libovolný jednotlivých žádostí, serializovat JSON data (například řetězce, logické hodnoty nebo číslice).</span><span class="sxs-lookup"><span data-stu-id="530ea-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="530ea-181">Vaše *main.server.ts* kód přijímá jako `params.data`.</span><span class="sxs-lookup"><span data-stu-id="530ea-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="530ea-182">Například předchozí příklad kódu předá hodnotu typu boolean jako `params.data.isHttpsRequest` do `createServerRenderer` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="530ea-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="530ea-183">To můžete předat do jiné části aplikace žádným způsobem podporuje Angular.</span><span class="sxs-lookup"><span data-stu-id="530ea-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="530ea-184">Třeba zjistit, jak *main.server.ts* předává `BASE_URL` hodnotu pro všechny součásti, jejíž konstruktor je deklarované ho přijímat pomocí.</span><span class="sxs-lookup"><span data-stu-id="530ea-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="530ea-185">Nevýhody SSR</span><span class="sxs-lookup"><span data-stu-id="530ea-185">Drawbacks of SSR</span></span>

<span data-ttu-id="530ea-186">Ne všechny aplikace s výhodou SSR.</span><span class="sxs-lookup"><span data-stu-id="530ea-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="530ea-187">Primární výhoda je vnímaný výkon.</span><span class="sxs-lookup"><span data-stu-id="530ea-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="530ea-188">Návštěvníci dosáhnout aplikace přes pomalé připojení k síti nebo na mobilních zařízeních pomalé počáteční uživatelského rozhraní zobrazovat rychle, i v případě, že trvá při načtení nebo parsovat sady JavaScript.</span><span class="sxs-lookup"><span data-stu-id="530ea-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="530ea-189">Ale mnoho SPA slouží především přes rychlé, interní firemní sítě na počítačích rychlé kde se zobrazí aplikace, téměř okamžitě.</span><span class="sxs-lookup"><span data-stu-id="530ea-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="530ea-190">Ve stejnou dobu jsou významné nevýhod povolení SSR.</span><span class="sxs-lookup"><span data-stu-id="530ea-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="530ea-191">To zvyšuje složitost vašeho vývojového procesu.</span><span class="sxs-lookup"><span data-stu-id="530ea-191">It adds complexity to your development process.</span></span> <span data-ttu-id="530ea-192">Váš kód musí běžet ve dvou různých prostředích: na straně klienta i stranu serveru (v prostředí Node.js vyvolat pomocí ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="530ea-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="530ea-193">Tady jsou některé věci k berte v úvahu:</span><span class="sxs-lookup"><span data-stu-id="530ea-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="530ea-194">SSR vyžaduje instalaci Node.js na provozních serverech.</span><span class="sxs-lookup"><span data-stu-id="530ea-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="530ea-195">Toto je automaticky případ pro některé scénáře nasazení, jako je například Azure App Services, ale ne pro jiné, jako je Azure Service Fabric.</span><span class="sxs-lookup"><span data-stu-id="530ea-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="530ea-196">Povolení `BuildServerSideRenderer` sestavení příznak způsobí, že vaše *node_modules* directory k publikování.</span><span class="sxs-lookup"><span data-stu-id="530ea-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="530ea-197">Tato složka obsahuje 20 000 + soubory, které zvýší dobu nasazení.</span><span class="sxs-lookup"><span data-stu-id="530ea-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="530ea-198">Ke spuštění kódu v prostředí Node.js, se nelze spoléhat na existenci specifické pro prohlížeč rozhraní API jazyka JavaScript, jako `window` nebo `localStorage`.</span><span class="sxs-lookup"><span data-stu-id="530ea-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="530ea-199">Pokud váš kód (nebo některé knihovny třetí strany, který odkazujete) pokusí o pomocí těchto rozhraní API, budete při SSR dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="530ea-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="530ea-200">Například nepoužívejte jQuery protože odkazuje na rozhraní API pro konkrétní prohlížeče na mnoha místech.</span><span class="sxs-lookup"><span data-stu-id="530ea-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="530ea-201">Chcete-li zabránit chybám, musíte vyhnout SSR nebo Vyhněte se rozhraní API nebo knihovny specifické pro prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="530ea-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="530ea-202">Všechna volání do těchto rozhraní API můžete zabalit do kontroly pro zajištění, že nejsou vyvolány během SSR.</span><span class="sxs-lookup"><span data-stu-id="530ea-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="530ea-203">Například v kódu jazyka JavaScript nebo TypeScript použijte kontrolu, jako je následující:</span><span class="sxs-lookup"><span data-stu-id="530ea-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="530ea-204">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="530ea-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
