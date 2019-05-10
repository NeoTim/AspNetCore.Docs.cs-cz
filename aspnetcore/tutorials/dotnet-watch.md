---
title: Vyvíjejte aplikace ASP.NET Core s využitím sledovací proces souborů
author: rick-anderson
description: Tento kurz ukazuje, jak nainstalovat a používat nástroje pro .NET Core CLI souborů sledovacích procesů (dotnet watch) v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: 03b4f7f4ade5268915482a659890c7edc2d9a852
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902169"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="4ffef-103">Vyvíjejte aplikace ASP.NET Core s využitím sledovací proces souborů</span><span class="sxs-lookup"><span data-stu-id="4ffef-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="4ffef-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="4ffef-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="4ffef-105">`dotnet watch` je nástroj, který běží [rozhraní příkazového řádku .NET Core](/dotnet/core/tools) příkaz při zdrojové soubory změnit.</span><span class="sxs-lookup"><span data-stu-id="4ffef-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="4ffef-106">Například změna souboru můžete aktivovat sestavování, spouštění testů nebo nasazení.</span><span class="sxs-lookup"><span data-stu-id="4ffef-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="4ffef-107">Tento kurz používá existující webové rozhraní API s dva koncové body: jeden, který vrací součet a ten, který vrátí produktu.</span><span class="sxs-lookup"><span data-stu-id="4ffef-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="4ffef-108">Metoda produkt obsahuje chybu, která je stanovena v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4ffef-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="4ffef-109">Stáhněte si [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="4ffef-109">Download the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="4ffef-110">Skládá se ze dvou projektů: *WebApp* (ASP.NET Core webového rozhraní API) a *WebAppTests* (testů jednotek pro webové rozhraní API).</span><span class="sxs-lookup"><span data-stu-id="4ffef-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="4ffef-111">V příkazovém řádku přejděte *WebApp* složky.</span><span class="sxs-lookup"><span data-stu-id="4ffef-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="4ffef-112">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4ffef-112">Run the following command:</span></span>

```console
dotnet run
```

> [!NOTE]
> <span data-ttu-id="4ffef-113">Můžete použít `dotnet run --project <PROJECT>` k určení projektu pro spuštění.</span><span class="sxs-lookup"><span data-stu-id="4ffef-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="4ffef-114">Například systém `dotnet run --project WebApp` z kořenového adresáře vzorku aplikace spustí se taky *WebApp* projektu.</span><span class="sxs-lookup"><span data-stu-id="4ffef-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="4ffef-115">Výstup konzoly zobrazí podobná následující zprávy (což znamená, že je aplikace spuštěná a čeká na požadavky):</span><span class="sxs-lookup"><span data-stu-id="4ffef-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="4ffef-116">Ve webovém prohlížeči přejděte na `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="4ffef-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="4ffef-117">Zobrazí se výsledek `9`.</span><span class="sxs-lookup"><span data-stu-id="4ffef-117">You should see the result of `9`.</span></span>

<span data-ttu-id="4ffef-118">Přejděte do produktu API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="4ffef-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="4ffef-119">Vrátí `9`, nikoli `20` dle očekávání.</span><span class="sxs-lookup"><span data-stu-id="4ffef-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="4ffef-120">Tento problém je vyřešen v pozdější části kurzu.</span><span class="sxs-lookup"><span data-stu-id="4ffef-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="4ffef-121">Přidat `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="4ffef-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="4ffef-122">`dotnet watch` Nástroj sledovací proces souborů je součástí 2.1.300 verzi .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="4ffef-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="4ffef-123">Následující kroky jsou povinné, jestli používáte starší verzi .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="4ffef-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="4ffef-124">Přidat `Microsoft.DotNet.Watcher.Tools` odkaz na balíček *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="4ffef-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="4ffef-125">Nainstalujte `Microsoft.DotNet.Watcher.Tools` balíčku spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="4ffef-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```console
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="4ffef-126">Spuštění pomocí příkazů rozhraní příkazového řádku .NET Core `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="4ffef-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="4ffef-127">Žádné [rozhraní příkazového řádku .NET Core](/dotnet/core/tools#cli-commands) můžete spustit s `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="4ffef-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="4ffef-128">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4ffef-128">For example:</span></span>

| <span data-ttu-id="4ffef-129">Příkaz</span><span class="sxs-lookup"><span data-stu-id="4ffef-129">Command</span></span> | <span data-ttu-id="4ffef-130">Příkaz s hodinkami</span><span class="sxs-lookup"><span data-stu-id="4ffef-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="4ffef-131">Spusťte příkaz DotNet</span><span class="sxs-lookup"><span data-stu-id="4ffef-131">dotnet run</span></span> | <span data-ttu-id="4ffef-132">Spustit sledování DotNet</span><span class="sxs-lookup"><span data-stu-id="4ffef-132">dotnet watch run</span></span> |
| <span data-ttu-id="4ffef-133">DotNet spustit netcoreapp2.0 -f</span><span class="sxs-lookup"><span data-stu-id="4ffef-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="4ffef-134">sledování DotNet spustit netcoreapp2.0 -f</span><span class="sxs-lookup"><span data-stu-id="4ffef-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="4ffef-135">Spustit netcoreapp2.0 -f - DotNet-arg1</span><span class="sxs-lookup"><span data-stu-id="4ffef-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="4ffef-136">sledování DotNet spustit netcoreapp2.0 -f –--arg1</span><span class="sxs-lookup"><span data-stu-id="4ffef-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="4ffef-137">DotNet test</span><span class="sxs-lookup"><span data-stu-id="4ffef-137">dotnet test</span></span> | <span data-ttu-id="4ffef-138">DotNet watch test</span><span class="sxs-lookup"><span data-stu-id="4ffef-138">dotnet watch test</span></span> |

<span data-ttu-id="4ffef-139">Spustit `dotnet watch run` v *WebApp* složky.</span><span class="sxs-lookup"><span data-stu-id="4ffef-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="4ffef-140">Určuje výstup konzoly `watch` byla spuštěna.</span><span class="sxs-lookup"><span data-stu-id="4ffef-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="4ffef-141">Můžete použít `dotnet watch --project <PROJECT>` zadat projekt, který chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="4ffef-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="4ffef-142">Například systém `dotnet watch --project WebApp run` z kořenového adresáře ukázky aplikací také spustí a podívejte se *WebApp* projektu.</span><span class="sxs-lookup"><span data-stu-id="4ffef-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="4ffef-143">Změny pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="4ffef-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="4ffef-144">Ujistěte se, že `dotnet watch` běží.</span><span class="sxs-lookup"><span data-stu-id="4ffef-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="4ffef-145">Oprava chyby v `Product` metoda *MathController.cs* tak, aby vracel produktu a nikoli součet:</span><span class="sxs-lookup"><span data-stu-id="4ffef-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="4ffef-146">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="4ffef-146">Save the file.</span></span> <span data-ttu-id="4ffef-147">Výstup konzoly znamená, že `dotnet watch` byla zjištěna změna souboru a restartovat aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ffef-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="4ffef-148">Ověřte `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.</span><span class="sxs-lookup"><span data-stu-id="4ffef-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="4ffef-149">Spustit testy pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="4ffef-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="4ffef-150">Změnit `Product` metoda *MathController.cs* zpět do vrací součet.</span><span class="sxs-lookup"><span data-stu-id="4ffef-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="4ffef-151">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="4ffef-151">Save the file.</span></span>
1. <span data-ttu-id="4ffef-152">V příkazovém řádku přejděte *WebAppTests* složky.</span><span class="sxs-lookup"><span data-stu-id="4ffef-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="4ffef-153">Spustit [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="4ffef-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="4ffef-154">Spusťte `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="4ffef-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="4ffef-155">Výstup udává, že se nezdařil test a že sledovací proces čeká na změny v souboru:</span><span class="sxs-lookup"><span data-stu-id="4ffef-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="4ffef-156">Oprava `Product` metoda kódu tak, aby vracel produktu.</span><span class="sxs-lookup"><span data-stu-id="4ffef-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="4ffef-157">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="4ffef-157">Save the file.</span></span>

<span data-ttu-id="4ffef-158">`dotnet watch` zjistí změnu souboru a znovu spustí testy.</span><span class="sxs-lookup"><span data-stu-id="4ffef-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="4ffef-159">Výstup konzoly označuje, testy proběhly úspěšně.</span><span class="sxs-lookup"><span data-stu-id="4ffef-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="4ffef-160">Upravit seznam souborů ke sledování</span><span class="sxs-lookup"><span data-stu-id="4ffef-160">Customize files list to watch</span></span>

<span data-ttu-id="4ffef-161">Ve výchozím nastavení `dotnet-watch` sleduje všechny soubory, které odpovídají následujícím vzorům glob:</span><span class="sxs-lookup"><span data-stu-id="4ffef-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="4ffef-162">Další položky lze přidat do seznamu sledovaných úpravou *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="4ffef-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="4ffef-163">Položky lze jednotlivě nebo s použitím glob vzory.</span><span class="sxs-lookup"><span data-stu-id="4ffef-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="4ffef-164">Odhlásit souborů, které mají být sledovány.</span><span class="sxs-lookup"><span data-stu-id="4ffef-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="4ffef-165">`dotnet-watch` je možné nakonfigurovat ignorovat výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="4ffef-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="4ffef-166">Chcete-li ignorovat konkrétní soubory, přidejte `Watch="false"` atributu na definici položky v *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="4ffef-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

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

## <a name="custom-watch-projects"></a><span data-ttu-id="4ffef-167">Vlastní sledování projektů</span><span class="sxs-lookup"><span data-stu-id="4ffef-167">Custom watch projects</span></span>

<span data-ttu-id="4ffef-168">`dotnet-watch` není omezena na projekty jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="4ffef-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="4ffef-169">Vlastní sledování projekty mohou být vytvořeny pro zpracování různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="4ffef-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="4ffef-170">Vezměte v úvahu následující rozložení projektu:</span><span class="sxs-lookup"><span data-stu-id="4ffef-170">Consider the following project layout:</span></span>

* <span data-ttu-id="4ffef-171">**test/**</span><span class="sxs-lookup"><span data-stu-id="4ffef-171">**test/**</span></span>
  * <span data-ttu-id="4ffef-172">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="4ffef-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="4ffef-173">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="4ffef-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="4ffef-174">Pokud je cílem jak oba projekty, vytvořte vlastní projekt soubor nakonfigurovaný tak, aby podívejte se na oba projekty:</span><span class="sxs-lookup"><span data-stu-id="4ffef-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

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

<span data-ttu-id="4ffef-175">Spusťte soubor sledování na obou projektů změňte na *testování* složky.</span><span class="sxs-lookup"><span data-stu-id="4ffef-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="4ffef-176">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4ffef-176">Execute the following command:</span></span>

```console
dotnet watch msbuild /t:Test
```

<span data-ttu-id="4ffef-177">VSTest provede při změn v souboru v obou testovacího projektu.</span><span class="sxs-lookup"><span data-stu-id="4ffef-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="4ffef-178">`dotnet-watch` v Githubu</span><span class="sxs-lookup"><span data-stu-id="4ffef-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="4ffef-179">`dotnet-watch` je součástí Githubu [aspnet/AspNetCore úložiště](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="4ffef-179">`dotnet-watch` is part of the GitHub [aspnet/AspNetCore repository](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
