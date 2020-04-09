---
title: Použití šablony projektu Angular s jádrem ASP.NET
author: SteveSandersonMS
description: Přečtěte si, jak začít s šablonou projektu ASP.NET základní jednostránkové aplikace (SPA) pro úhlové a úhlové cli.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
uid: spa/angular
ms.openlocfilehash: fee872ff237e14cbe491efed9b320809df4c5654
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657631"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="e16a7-103">Použití šablony projektu Angular s jádrem ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e16a7-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="e16a7-104">Aktualizovaná šablona projektu Angular poskytuje vhodný výchozí bod pro ASP.NET základní aplikace pomocí úhlového a úhlového rozhraní CLI k implementaci bohatého uživatelského rozhraní na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e16a7-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="e16a7-105">Šablona je ekvivalentní k vytvoření ASP.NET core projektu fungovat jako back-end rozhraní API a úhlové cli projekt fungovat jako uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e16a7-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="e16a7-106">Šablona nabízí pohodlí hostování obou typů projektů v jednom projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="e16a7-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="e16a7-107">V důsledku toho může být projekt aplikace sestaven a publikován jako jedna jednotka.</span><span class="sxs-lookup"><span data-stu-id="e16a7-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="e16a7-108">Vytvoření nové aplikace</span><span class="sxs-lookup"><span data-stu-id="e16a7-108">Create a new app</span></span>

<span data-ttu-id="e16a7-109">Pokud máte nainstalovanou ASP.NET Core 2.1, není nutné instalovat šablonu projektu Angular.</span><span class="sxs-lookup"><span data-stu-id="e16a7-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="e16a7-110">Vytvořte nový projekt z příkazového `dotnet new angular` řádku pomocí příkazu v prázdném adresáři.</span><span class="sxs-lookup"><span data-stu-id="e16a7-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="e16a7-111">Například následující příkazy vytvoří aplikaci v adresáři *moje nová aplikace* a přejdou do tohoto adresáře:</span><span class="sxs-lookup"><span data-stu-id="e16a7-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="e16a7-112">Spusťte aplikaci z Visual Studia nebo rozhraní .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="e16a7-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e16a7-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e16a7-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="e16a7-114">Otevřete vygenerovaný soubor *.csproj* a spusťte aplikaci jako obvykle.</span><span class="sxs-lookup"><span data-stu-id="e16a7-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="e16a7-115">Proces sestavení obnoví npm závislosti na první spuštění, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="e16a7-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="e16a7-116">Následná sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="e16a7-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e16a7-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="e16a7-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="e16a7-118">Ujistěte se, že `ASPNETCORE_Environment` máte proměnnou prostředí volanou s hodnotou `Development`.</span><span class="sxs-lookup"><span data-stu-id="e16a7-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="e16a7-119">Ve Windows (v výzvách, které `SET ASPNETCORE_Environment=Development`nejsou powershelly), spusťte .</span><span class="sxs-lookup"><span data-stu-id="e16a7-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="e16a7-120">Na Linuxu nebo macOS spusťte `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="e16a7-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="e16a7-121">Spusťte [dotnet sestavení](/dotnet/core/tools/dotnet-build) ověřit sestavení aplikace správně.</span><span class="sxs-lookup"><span data-stu-id="e16a7-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="e16a7-122">Při prvním spuštění proces sestavení obnoví npm závislosti, což může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="e16a7-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="e16a7-123">Následná sestavení jsou mnohem rychlejší.</span><span class="sxs-lookup"><span data-stu-id="e16a7-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="e16a7-124">Spuštění [montovny dotnet](/dotnet/core/tools/dotnet-run) spusťte.</span><span class="sxs-lookup"><span data-stu-id="e16a7-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="e16a7-125">Je zaznamenána zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="e16a7-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="e16a7-126">Přejděte na tuto adresu URL v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e16a7-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="e16a7-127">Aplikace spustí instanci úhlového cli serveru na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e16a7-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="e16a7-128">Zpráva podobná následující je zaznamenána: *NG Live Development Server&lt;poslouchá&gt;na localhost: http://localhost:&ltotherport&gt;, otevřete prohlížeč ;otherport*.</span><span class="sxs-lookup"><span data-stu-id="e16a7-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="e16a7-129">Ignorovat tuto&mdash;zprávu **není** adresa URL pro kombinované ASP.NET core a úhlové CLI aplikace.</span><span class="sxs-lookup"><span data-stu-id="e16a7-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="e16a7-130">Šablona projektu vytvoří aplikaci ASP.NET Core a aplikaci Angular.</span><span class="sxs-lookup"><span data-stu-id="e16a7-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="e16a7-131">Aplikace ASP.NET Core je určena pro přístup k datům, autorizaci a další problémy na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="e16a7-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="e16a7-132">Angular aplikace, s bydlištěm v podadresáři *ClientApp,* je určen pro všechny problémy s rozhraním.</span><span class="sxs-lookup"><span data-stu-id="e16a7-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="e16a7-133">Přidejte stránky, obrázky, styly, moduly atd.</span><span class="sxs-lookup"><span data-stu-id="e16a7-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="e16a7-134">Adresář *ClientApp* obsahuje standardní aplikaci Úhlové cli.</span><span class="sxs-lookup"><span data-stu-id="e16a7-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="e16a7-135">Další informace naleznete v oficiální [dokumentaci angular.](https://angular.io)</span><span class="sxs-lookup"><span data-stu-id="e16a7-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="e16a7-136">Existují mírné rozdíly mezi aplikací Angular vytvořenou touto šablonou a aplikací vytvořenou samotným úhlovým CLI (přes); `ng new` možnosti aplikace se však nemění.</span><span class="sxs-lookup"><span data-stu-id="e16a7-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="e16a7-137">Aplikace vytvořená šablonou obsahuje rozložení založené na [Bootstrapu](https://getbootstrap.com/)a základní příklad směrování.</span><span class="sxs-lookup"><span data-stu-id="e16a7-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="e16a7-138">Spuštění příkazů ng</span><span class="sxs-lookup"><span data-stu-id="e16a7-138">Run ng commands</span></span>

<span data-ttu-id="e16a7-139">V příkazovém řádku přepněte do podadresáře *ClientApp:*</span><span class="sxs-lookup"><span data-stu-id="e16a7-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="e16a7-140">Pokud máte `ng` nástroj nainstalován globálně, můžete spustit některý z jeho příkazů.</span><span class="sxs-lookup"><span data-stu-id="e16a7-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="e16a7-141">Můžete například spustit `ng lint` `ng test`, nebo některý z dalších [příkazů úhlového příkazu cli](https://angular.io/cli).</span><span class="sxs-lookup"><span data-stu-id="e16a7-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="e16a7-142">Není však třeba `ng serve` spouštět, protože vaše aplikace ASP.NET Core se zabývá zobrazováním částí aplikace na straně serveru i na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e16a7-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="e16a7-143">Interně se `ng serve` používá ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="e16a7-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="e16a7-144">Pokud nástroj nemáte nainstalovaný, `ng` spusťte `npm run ng` jej.</span><span class="sxs-lookup"><span data-stu-id="e16a7-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="e16a7-145">Můžete například spustit `npm run ng lint` `npm run ng test`nebo .</span><span class="sxs-lookup"><span data-stu-id="e16a7-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="e16a7-146">Instalace balíčků npm</span><span class="sxs-lookup"><span data-stu-id="e16a7-146">Install npm packages</span></span>

<span data-ttu-id="e16a7-147">Chcete-li nainstalovat balíčky npm od jiných výrobců, použijte příkazový řádek v podadresáři *ClientApp.*</span><span class="sxs-lookup"><span data-stu-id="e16a7-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="e16a7-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e16a7-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="e16a7-149">Publikování a nasazení</span><span class="sxs-lookup"><span data-stu-id="e16a7-149">Publish and deploy</span></span>

<span data-ttu-id="e16a7-150">Ve vývoji aplikace běží v režimu optimalizovaném pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="e16a7-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="e16a7-151">Například javascriptové balíčky obsahují zdrojové mapy (takže při ladění můžete vidět původní kód TypeScript).</span><span class="sxs-lookup"><span data-stu-id="e16a7-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="e16a7-152">Aplikace sleduje změny souborů TypeScript, HTML a CSS na disku a automaticky se znovu zkompiluje a znovu načte, když vidí, že se tyto soubory mění.</span><span class="sxs-lookup"><span data-stu-id="e16a7-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="e16a7-153">V produkčním prostředí můžete poskytovat verzi aplikace optimalizovanou pro výkon.</span><span class="sxs-lookup"><span data-stu-id="e16a7-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="e16a7-154">Tato možnost je nakonfigurována tak, aby se stala automaticky.</span><span class="sxs-lookup"><span data-stu-id="e16a7-154">This is configured to happen automatically.</span></span> <span data-ttu-id="e16a7-155">Při publikování konfigurace sestavení vyzařuje minified, ahead-of-time (AoT) zkompilované sestavení kódu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e16a7-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="e16a7-156">Na rozdíl od vývojového sestavení nevyžaduje produkční sestavení instalaci node.js na server (pokud jste nepovolili vykreslování na straně serveru (SSR)).</span><span class="sxs-lookup"><span data-stu-id="e16a7-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="e16a7-157">Můžete použít standardní [ASP.NET core hosting a metody nasazení](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="e16a7-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="e16a7-158">Spustit "ng sloužit" nezávisle</span><span class="sxs-lookup"><span data-stu-id="e16a7-158">Run "ng serve" independently</span></span>

<span data-ttu-id="e16a7-159">Projekt je nakonfigurován tak, aby spustil vlastní instanci serveru Úhlové cli na pozadí při spuštění aplikace ASP.NET Core v režimu vývoje.</span><span class="sxs-lookup"><span data-stu-id="e16a7-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="e16a7-160">To je výhodné, protože není nutné spustit samostatný server ručně.</span><span class="sxs-lookup"><span data-stu-id="e16a7-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="e16a7-161">Toto výchozí nastavení má nevýhodu.</span><span class="sxs-lookup"><span data-stu-id="e16a7-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="e16a7-162">Pokaždé, když upravíte kód C# a aplikace ASP.NET Core musí restartovat, restartuje se server Úhlové cli.</span><span class="sxs-lookup"><span data-stu-id="e16a7-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="e16a7-163">Pro spuštění zálohy je zapotřebí přibližně 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="e16a7-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="e16a7-164">Pokud provádíte časté úpravy kódu jazyka C# a nechcete čekat na restartování úhlového cli, spusťte server angular CLI externě, nezávisle na procesu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e16a7-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="e16a7-165">Postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="e16a7-165">To do so:</span></span>

1. <span data-ttu-id="e16a7-166">V příkazovém řádku přepněte do podadresáře *ClientApp* a spusťte vývojový server Úhlové rozhraní příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="e16a7-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="e16a7-167">Slouží `npm start` ke spuštění úhlového cli `ng serve`vývojový server, nikoli , tak, aby konfigurace v *package.json* je respektována.</span><span class="sxs-lookup"><span data-stu-id="e16a7-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="e16a7-168">Chcete-li předat další parametry serveru úhlového rozhraní `scripts` CLI, přidejte je do příslušného řádku v souboru *package.json.*</span><span class="sxs-lookup"><span data-stu-id="e16a7-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="e16a7-169">Upravte aplikaci ASP.NET Core tak, aby místo spuštění vlastní instance úhlového cli používala externí instanci úhlového cli.</span><span class="sxs-lookup"><span data-stu-id="e16a7-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="e16a7-170">Ve třídě *Startup* nahraďte `spa.UseAngularCliServer` vyvolání následujícím:</span><span class="sxs-lookup"><span data-stu-id="e16a7-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="e16a7-171">Když spustíte aplikaci ASP.NET Core, nespustí se server úhlového cli.</span><span class="sxs-lookup"><span data-stu-id="e16a7-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="e16a7-172">Místo toho se použije instance, kterou jste spustili ručně.</span><span class="sxs-lookup"><span data-stu-id="e16a7-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="e16a7-173">To umožňuje rychlejší spuštění a restartování.</span><span class="sxs-lookup"><span data-stu-id="e16a7-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="e16a7-174">Už nečeká na úhlové cli znovu sestavit klientskou aplikaci pokaždé.</span><span class="sxs-lookup"><span data-stu-id="e16a7-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="e16a7-175">Předání dat z kódu .NET do kódu Typu Typu Script</span><span class="sxs-lookup"><span data-stu-id="e16a7-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="e16a7-176">Během SSR můžete chtít předat data na žádost z aplikace ASP.NET Core do aplikace Angular.</span><span class="sxs-lookup"><span data-stu-id="e16a7-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="e16a7-177">Můžete například předat informace o souborech cookie nebo něco přečteného z databáze.</span><span class="sxs-lookup"><span data-stu-id="e16a7-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="e16a7-178">Chcete-li to provést, upravte třídu *Po spuštění.*</span><span class="sxs-lookup"><span data-stu-id="e16a7-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="e16a7-179">V zpětném `UseSpaPrerendering`volání pro nastavte `options.SupplyData` hodnotu, například následující:</span><span class="sxs-lookup"><span data-stu-id="e16a7-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="e16a7-180">Zpětné `SupplyData` volání umožňuje předat libovolná data json-serializovatelná data (například řetězce, logické hodnoty nebo čísla).</span><span class="sxs-lookup"><span data-stu-id="e16a7-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="e16a7-181">Váš kód *main.server.ts* obdrží `params.data`toto jako .</span><span class="sxs-lookup"><span data-stu-id="e16a7-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="e16a7-182">Například předchozí ukázka kódu předá logickou hodnotu jako `params.data.isHttpsRequest` do zpětného `createServerRenderer` volání.</span><span class="sxs-lookup"><span data-stu-id="e16a7-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="e16a7-183">Můžete předat do jiných částí aplikace v libovolném způsobem podporované Angular.</span><span class="sxs-lookup"><span data-stu-id="e16a7-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="e16a7-184">Například podívejte se, jak *main.server.ts* předá hodnotu `BASE_URL` jakékoli součásti, jejíž konstruktor je deklarován k jeho přijetí.</span><span class="sxs-lookup"><span data-stu-id="e16a7-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="e16a7-185">Nevýhody SSR</span><span class="sxs-lookup"><span data-stu-id="e16a7-185">Drawbacks of SSR</span></span>

<span data-ttu-id="e16a7-186">Ne všechny aplikace mají prospěch z SSR.</span><span class="sxs-lookup"><span data-stu-id="e16a7-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="e16a7-187">Hlavním přínosem je vnímaný výkon.</span><span class="sxs-lookup"><span data-stu-id="e16a7-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="e16a7-188">Návštěvníci, kteří se k vaší aplikaci dostanou prostředpou prostředpou k pomalému síťovému připojení nebo na pomalých mobilních zařízeních, uvidí počáteční ui rychle, i když trvá nějakou dobu, než načte nebo analyzují balíčky JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e16a7-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="e16a7-189">Nicméně, mnoho SA se používá hlavně přes rychlé, interní firemní sítě na rychlých počítačích, kde se aplikace objeví téměř okamžitě.</span><span class="sxs-lookup"><span data-stu-id="e16a7-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="e16a7-190">Současně existují významné nevýhody povolení SSR.</span><span class="sxs-lookup"><span data-stu-id="e16a7-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="e16a7-191">To zvyšuje složitost procesu vývoje.</span><span class="sxs-lookup"><span data-stu-id="e16a7-191">It adds complexity to your development process.</span></span> <span data-ttu-id="e16a7-192">Váš kód musí běžet ve dvou různých prostředích: na straně klienta a na straně serveru (v prostředí Node.js vyvolaném z ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="e16a7-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="e16a7-193">Zde je několik věcí, které je třeba mít na paměti:</span><span class="sxs-lookup"><span data-stu-id="e16a7-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="e16a7-194">SSR vyžaduje instalaci Node.js na produkčních serverech.</span><span class="sxs-lookup"><span data-stu-id="e16a7-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="e16a7-195">To je automaticky případ pro některé scénáře nasazení, jako je například Azure App Services, ale ne pro jiné, jako je například Azure Service Fabric.</span><span class="sxs-lookup"><span data-stu-id="e16a7-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="e16a7-196">Povolení `BuildServerSideRenderer` příznaku sestavení způsobí publikování *node_modules* adresáře.</span><span class="sxs-lookup"><span data-stu-id="e16a7-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="e16a7-197">Tato složka obsahuje více než 20 000 souborů, což zvyšuje dobu nasazení.</span><span class="sxs-lookup"><span data-stu-id="e16a7-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="e16a7-198">Chcete-li spustit kód v prostředí Node.js, nemůže se spoléhat na existenci javascriptových api specifických pro prohlížeč, jako `window` jsou nebo `localStorage`.</span><span class="sxs-lookup"><span data-stu-id="e16a7-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="e16a7-199">Pokud váš kód (nebo některé knihovny třetích stran, na které odkazujete) se pokusí použít tato rozhraní API, zobrazí se chyba během SSR.</span><span class="sxs-lookup"><span data-stu-id="e16a7-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="e16a7-200">Například nepoužívejte jQuery, protože odkazuje na prohlížeče specifické api na mnoha místech.</span><span class="sxs-lookup"><span data-stu-id="e16a7-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="e16a7-201">Chcete-li zabránit chybám, musíte se buď vyhnout SSR nebo se vyhnout prohlížeče specifické pro api nebo knihovny.</span><span class="sxs-lookup"><span data-stu-id="e16a7-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="e16a7-202">Můžete zabalit všechna volání těchto api v kontrolách zajistit, že nejsou vyvolány během SSR.</span><span class="sxs-lookup"><span data-stu-id="e16a7-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="e16a7-203">V kódu Jazyka JavaScript nebo TypeScript použijte například následující:</span><span class="sxs-lookup"><span data-stu-id="e16a7-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="e16a7-204">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e16a7-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
