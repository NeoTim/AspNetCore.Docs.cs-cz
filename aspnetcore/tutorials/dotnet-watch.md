---
title: Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů
author: rick-anderson
description: V tomto kurzu se dozvíte, jak nainstalovat a používat nástroj sledovacího procesu souborů (dotnet Watch) .NET Core CLI v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/dotnet-watch
ms.openlocfilehash: 3569e9440b8e431ec0e5357e548af2e3783481ac
ms.sourcegitcommit: 422e02bad384775bfe19a90910737340ad106c5b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90083450"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="713e2-103">Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů</span><span class="sxs-lookup"><span data-stu-id="713e2-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="713e2-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [vítěz Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="713e2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="713e2-105">`dotnet watch` je nástroj, který spouští příkaz [.NET Core CLI](/dotnet/core/tools) , když se změní zdrojové soubory.</span><span class="sxs-lookup"><span data-stu-id="713e2-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="713e2-106">Například změna souboru může aktivovat kompilaci, spuštění testu nebo nasazení.</span><span class="sxs-lookup"><span data-stu-id="713e2-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="713e2-107">V tomto kurzu se používá existující webové rozhraní API se dvěma koncovými body: jeden, který vrací součet, a jeden, který vrací produkt.</span><span class="sxs-lookup"><span data-stu-id="713e2-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="713e2-108">Metoda produktu obsahuje chybu, která je v tomto kurzu opravena.</span><span class="sxs-lookup"><span data-stu-id="713e2-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="713e2-109">Stáhněte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="713e2-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="713e2-110">Skládá se ze dvou projektů: *WebApp* (ASP.NET Core Web API) a *WebAppTests* (testování částí webového rozhraní API).</span><span class="sxs-lookup"><span data-stu-id="713e2-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="713e2-111">V příkazovém prostředí přejděte do složky *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="713e2-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="713e2-112">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="713e2-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="713e2-113">Můžete použít `dotnet run --project <PROJECT>` k určení projektu, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="713e2-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="713e2-114">Například spuštění `dotnet run --project WebApp` z kořenového adresáře ukázkové aplikace spustí také projekt *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="713e2-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="713e2-115">Výstup konzoly zobrazuje zprávy podobné následujícímu (což značí, že aplikace běží a čekají na žádosti):</span><span class="sxs-lookup"><span data-stu-id="713e2-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="713e2-116">Ve webovém prohlížeči přejděte na adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="713e2-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="713e2-117">Měl by se zobrazit výsledek `9` .</span><span class="sxs-lookup"><span data-stu-id="713e2-117">You should see the result of `9`.</span></span>

<span data-ttu-id="713e2-118">Přejděte na rozhraní API produktu ( `http://localhost:<port number>/api/math/product?a=4&b=5` ).</span><span class="sxs-lookup"><span data-stu-id="713e2-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="713e2-119">Vrátí `9` , ne `20` jako byste očekávali.</span><span class="sxs-lookup"><span data-stu-id="713e2-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="713e2-120">Tento problém je opravený později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="713e2-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="713e2-121">Přidat `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="713e2-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="713e2-122">`dotnet watch`Nástroj sledovacího procesu souborů je součástí 2.1.300 verze .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="713e2-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="713e2-123">Při použití starší verze .NET Core SDK jsou vyžadovány následující kroky.</span><span class="sxs-lookup"><span data-stu-id="713e2-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="713e2-124">Přidejte `Microsoft.DotNet.Watcher.Tools` odkaz na balíček do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="713e2-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="713e2-125">Nainstalujte `Microsoft.DotNet.Watcher.Tools` balíček spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="713e2-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="713e2-126">Spouštění příkazů .NET Core CLI pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="713e2-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="713e2-127">Libovolný [.NET Core CLI příkaz](/dotnet/core/tools#cli-commands) lze spustit pomocí příkazu `dotnet watch` .</span><span class="sxs-lookup"><span data-stu-id="713e2-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="713e2-128">Příklad:</span><span class="sxs-lookup"><span data-stu-id="713e2-128">For example:</span></span>

| <span data-ttu-id="713e2-129">Příkaz</span><span class="sxs-lookup"><span data-stu-id="713e2-129">Command</span></span> | <span data-ttu-id="713e2-130">Příkaz se sledováním</span><span class="sxs-lookup"><span data-stu-id="713e2-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="713e2-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="713e2-131">dotnet run</span></span> | <span data-ttu-id="713e2-132">dotnet – běh kukátka</span><span class="sxs-lookup"><span data-stu-id="713e2-132">dotnet watch run</span></span> |
| <span data-ttu-id="713e2-133">spuštění dotnet – f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="713e2-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="713e2-134">dotnet – běh kukátka – f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="713e2-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="713e2-135">příkaz dotnet Run-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="713e2-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="713e2-136">dotnet Watch Run-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="713e2-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="713e2-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="713e2-137">dotnet test</span></span> | <span data-ttu-id="713e2-138">test výrazu dotnet</span><span class="sxs-lookup"><span data-stu-id="713e2-138">dotnet watch test</span></span> |

<span data-ttu-id="713e2-139">Spusťte `dotnet watch run` ve složce *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="713e2-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="713e2-140">Výstup konzoly indikuje, že `watch` byl spuštěn.</span><span class="sxs-lookup"><span data-stu-id="713e2-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="713e2-141">`dotnet watch run`Po spuštění na webové aplikaci se spustí prohlížeč, který po dokončení přejde na adresu URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="713e2-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="713e2-142">`dotnet watch` Přečtěte si výstup z konzoly aplikace a počkejte, než se zobrazí zpráva připravena <xref:Microsoft.AspNetCore.WebHost> .</span><span class="sxs-lookup"><span data-stu-id="713e2-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="713e2-143">`dotnet watch` aktualizuje prohlížeč, když detekuje změny sledovaných souborů.</span><span class="sxs-lookup"><span data-stu-id="713e2-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="713e2-144">Pokud to chcete provést, příkaz Watch vloží do aplikace middleware, který upraví odpovědi HTML vytvořené aplikací.</span><span class="sxs-lookup"><span data-stu-id="713e2-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="713e2-145">Middleware přidá do stránky blok skriptu JavaScriptu, který umožňuje `dotnet watch` dát pokyn prohlížeči k aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="713e2-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="713e2-146">V současné době změny všech sledovaných souborů, včetně statického obsahu, jako jsou soubory *. html* a *. CSS* , způsobí, že se aplikace znovu sestaví.</span><span class="sxs-lookup"><span data-stu-id="713e2-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="713e2-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="713e2-147">`dotnet watch`:</span></span>

* <span data-ttu-id="713e2-148">Sleduje pouze soubory, které mají vliv na sestavení ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="713e2-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="713e2-149">U všech sledovaných souborů (prostřednictvím konfigurace) stále dochází k sestavení.</span><span class="sxs-lookup"><span data-stu-id="713e2-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="713e2-150">Další informace o konfiguraci najdete v tématu [dotnet – sledování konfigurace](#dotnet-watch-configuration) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="713e2-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="713e2-151">Můžete použít `dotnet watch --project <PROJECT>` k určení projektu, který se má sledovat.</span><span class="sxs-lookup"><span data-stu-id="713e2-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="713e2-152">Například spuštění `dotnet watch --project WebApp run` z kořenového adresáře ukázkové aplikace bude také spuštěno a sledovat projekt *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="713e2-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="713e2-153">Provést změny pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="713e2-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="713e2-154">Ujistěte `dotnet watch` se, že je spuštěný.</span><span class="sxs-lookup"><span data-stu-id="713e2-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="713e2-155">Opravte chybu v `Product` metodě *MathController.cs* tak, aby vrátila produkt, a ne součet:</span><span class="sxs-lookup"><span data-stu-id="713e2-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="713e2-156">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="713e2-156">Save the file.</span></span> <span data-ttu-id="713e2-157">Výstup konzoly indikuje, že `dotnet watch` zjistil změnu souboru a restartuje aplikaci.</span><span class="sxs-lookup"><span data-stu-id="713e2-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="713e2-158">Ověřování `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.</span><span class="sxs-lookup"><span data-stu-id="713e2-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="713e2-159">Spustit testy pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="713e2-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="713e2-160">Změňte `Product` metodu *MathController.cs* zpět na vrácení součtu.</span><span class="sxs-lookup"><span data-stu-id="713e2-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="713e2-161">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="713e2-161">Save the file.</span></span>
1. <span data-ttu-id="713e2-162">V příkazovém prostředí přejděte do složky *WebAppTests* .</span><span class="sxs-lookup"><span data-stu-id="713e2-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="713e2-163">Spusťte [dotnet Restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="713e2-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="713e2-164">Spusťte `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="713e2-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="713e2-165">Jeho výstup označuje, že se test nezdařil a že sledovací proces čeká na změny souborů:</span><span class="sxs-lookup"><span data-stu-id="713e2-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="713e2-166">Opravte `Product` kód metody tak, aby vrátil produkt.</span><span class="sxs-lookup"><span data-stu-id="713e2-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="713e2-167">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="713e2-167">Save the file.</span></span>

<span data-ttu-id="713e2-168">`dotnet watch` zjistí změnu souboru a znovu spustí testy.</span><span class="sxs-lookup"><span data-stu-id="713e2-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="713e2-169">Výstup konzoly indikuje úspěšné testy.</span><span class="sxs-lookup"><span data-stu-id="713e2-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="713e2-170">Přizpůsobení seznamu souborů pro sledování</span><span class="sxs-lookup"><span data-stu-id="713e2-170">Customize files list to watch</span></span>

<span data-ttu-id="713e2-171">Ve výchozím nastavení `dotnet-watch` sleduje všechny soubory, které odpovídají následujícím vzorům glob:</span><span class="sxs-lookup"><span data-stu-id="713e2-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="713e2-172">Další položky lze přidat do seznamu sledování úpravou souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="713e2-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="713e2-173">Položky lze zadat jednotlivě nebo pomocí glob vzorů.</span><span class="sxs-lookup"><span data-stu-id="713e2-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="713e2-174">Výslovný souhlas se soubory, které se mají sledovat</span><span class="sxs-lookup"><span data-stu-id="713e2-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="713e2-175">`dotnet-watch` dá se nakonfigurovat tak, aby ignoroval výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="713e2-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="713e2-176">Chcete-li ignorovat konkrétní soubory, přidejte `Watch="false"` atribut do definice položky v souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="713e2-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="713e2-177">Vlastní projekty kukátka</span><span class="sxs-lookup"><span data-stu-id="713e2-177">Custom watch projects</span></span>

<span data-ttu-id="713e2-178">`dotnet-watch` není omezen na projekty C#.</span><span class="sxs-lookup"><span data-stu-id="713e2-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="713e2-179">Je možné vytvořit vlastní projekty kukátka pro zpracování různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="713e2-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="713e2-180">Vezměte v úvahu následující rozložení projektu:</span><span class="sxs-lookup"><span data-stu-id="713e2-180">Consider the following project layout:</span></span>

* <span data-ttu-id="713e2-181">**napaden**</span><span class="sxs-lookup"><span data-stu-id="713e2-181">**test/**</span></span>
  * <span data-ttu-id="713e2-182">*UnitTests/UnitTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="713e2-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="713e2-183">*IntegrationTests/IntegrationTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="713e2-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="713e2-184">Pokud je cílem sledovat oba projekty, vytvořte vlastní soubor projektu nakonfigurovaný pro sledování obou projektů:</span><span class="sxs-lookup"><span data-stu-id="713e2-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="713e2-185">Chcete-li spustit sledování souborů na obou projektech, přejděte do složky *test* .</span><span class="sxs-lookup"><span data-stu-id="713e2-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="713e2-186">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="713e2-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="713e2-187">VSTest se spustí při každé změně souboru v jednom testovacím projektu.</span><span class="sxs-lookup"><span data-stu-id="713e2-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="713e2-188">dotnet – sledování konfigurace</span><span class="sxs-lookup"><span data-stu-id="713e2-188">dotnet-watch configuration</span></span>

<span data-ttu-id="713e2-189">Některé možnosti konfigurace lze předat do `dotnet watch` proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="713e2-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="713e2-190">Dostupné proměnné jsou:</span><span class="sxs-lookup"><span data-stu-id="713e2-190">The available variables are:</span></span>

| <span data-ttu-id="713e2-191">Nastavení</span><span class="sxs-lookup"><span data-stu-id="713e2-191">Setting</span></span>  | <span data-ttu-id="713e2-192">Popis</span><span class="sxs-lookup"><span data-stu-id="713e2-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="713e2-193">Pokud je nastavená na "1" nebo "true", `dotnet watch` používá sledovací proces pro cyklické soubory místo CoreFx `FileSystemWatcher` .</span><span class="sxs-lookup"><span data-stu-id="713e2-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="713e2-194">Používá se při sledování souborů v síťových sdílených složkách nebo připojených svazcích Docker.</span><span class="sxs-lookup"><span data-stu-id="713e2-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="713e2-195">Ve výchozím nastavení `dotnet watch` optimalizuje sestavení tím, že vyloučí určité operace, jako je spuštění obnovení nebo opětovné vyhodnocení sady sledovaných souborů při každé změně souboru.</span><span class="sxs-lookup"><span data-stu-id="713e2-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="713e2-196">Je-li nastaveno na hodnotu "1" nebo "true", jsou tyto optimalizace zakázány.</span><span class="sxs-lookup"><span data-stu-id="713e2-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="713e2-197">`dotnet watch run` pokusí se spustit prohlížeče pro webové aplikace, které jsou `launchBrowser` nakonfigurované v *launchSettings.js*.</span><span class="sxs-lookup"><span data-stu-id="713e2-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json*.</span></span> <span data-ttu-id="713e2-198">Pokud je nastaveno na hodnotu "1" nebo "true", toto chování je potlačeno.</span><span class="sxs-lookup"><span data-stu-id="713e2-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="713e2-199">`dotnet watch run` pokusí se aktualizovat prohlížeče, když detekuje změny souborů.</span><span class="sxs-lookup"><span data-stu-id="713e2-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="713e2-200">Pokud je nastaveno na hodnotu "1" nebo "true", toto chování je potlačeno.</span><span class="sxs-lookup"><span data-stu-id="713e2-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="713e2-201">Toto chování je také potlačeno `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` , pokud je nastaveno.</span><span class="sxs-lookup"><span data-stu-id="713e2-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="713e2-202">`dotnet-watch` v GitHubu</span><span class="sxs-lookup"><span data-stu-id="713e2-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="713e2-203">`dotnet-watch` je součástí [úložiště dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)v GitHubu.</span><span class="sxs-lookup"><span data-stu-id="713e2-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
