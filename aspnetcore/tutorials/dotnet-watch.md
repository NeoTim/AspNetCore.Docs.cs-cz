---
title: Vyvíjejte aplikace ASP.NET Core s využitím sledovací proces souborů
author: rick-anderson
description: Tento kurz ukazuje, jak nainstalovat a používat nástroje pro .NET Core CLI souborů sledovacích procesů (dotnet watch) v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: f1e0d91b27df4af7cbfb6f2547c94c0370c65d0d
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207499"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="4ac93-103">Vyvíjejte aplikace ASP.NET Core s využitím sledovací proces souborů</span><span class="sxs-lookup"><span data-stu-id="4ac93-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="4ac93-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="4ac93-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="4ac93-105">`dotnet watch` je nástroj, který běží [rozhraní příkazového řádku .NET Core](/dotnet/core/tools) příkaz při zdrojové soubory změnit.</span><span class="sxs-lookup"><span data-stu-id="4ac93-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="4ac93-106">Například změna souboru můžete aktivovat sestavování, spouštění testů nebo nasazení.</span><span class="sxs-lookup"><span data-stu-id="4ac93-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="4ac93-107">Tento kurz používá existující webové rozhraní API s dva koncové body: jeden, který vrací součet a ten, který vrátí produktu.</span><span class="sxs-lookup"><span data-stu-id="4ac93-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="4ac93-108">Metoda produkt obsahuje chybu, která je stanovena v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4ac93-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="4ac93-109">Stáhněte si [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="4ac93-109">Download the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="4ac93-110">Skládá se ze dvou projektů: *WebApp* (ASP.NET Core webového rozhraní API) a *WebAppTests* (testů jednotek pro webové rozhraní API).</span><span class="sxs-lookup"><span data-stu-id="4ac93-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="4ac93-111">V příkazovém řádku přejděte *WebApp* složky.</span><span class="sxs-lookup"><span data-stu-id="4ac93-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="4ac93-112">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4ac93-112">Run the following command:</span></span>

```console
dotnet run
```

<span data-ttu-id="4ac93-113">Výstup konzoly zobrazí podobná následující zprávy (což znamená, že je aplikace spuštěná a čeká na požadavky):</span><span class="sxs-lookup"><span data-stu-id="4ac93-113">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="4ac93-114">Ve webovém prohlížeči přejděte na `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="4ac93-114">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="4ac93-115">Zobrazí se výsledek `9`.</span><span class="sxs-lookup"><span data-stu-id="4ac93-115">You should see the result of `9`.</span></span>

<span data-ttu-id="4ac93-116">Přejděte do produktu API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="4ac93-116">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="4ac93-117">Vrátí `9`, nikoli `20` dle očekávání.</span><span class="sxs-lookup"><span data-stu-id="4ac93-117">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="4ac93-118">Tento problém je vyřešen v pozdější části kurzu.</span><span class="sxs-lookup"><span data-stu-id="4ac93-118">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="4ac93-119">Přidat `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="4ac93-119">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="4ac93-120">`dotnet watch` Nástroj sledovací proces souborů je součástí 2.1.300 verzi .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="4ac93-120">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="4ac93-121">Následující kroky jsou povinné, jestli používáte starší verzi .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="4ac93-121">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="4ac93-122">Přidat `Microsoft.DotNet.Watcher.Tools` odkaz na balíček *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="4ac93-122">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="4ac93-123">Nainstalujte `Microsoft.DotNet.Watcher.Tools` balíčku spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="4ac93-123">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```console
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="4ac93-124">Spuštění pomocí příkazů rozhraní příkazového řádku .NET Core `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="4ac93-124">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="4ac93-125">Žádné [rozhraní příkazového řádku .NET Core](/dotnet/core/tools#cli-commands) můžete spustit s `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="4ac93-125">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="4ac93-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4ac93-126">For example:</span></span>

| <span data-ttu-id="4ac93-127">Příkaz</span><span class="sxs-lookup"><span data-stu-id="4ac93-127">Command</span></span> | <span data-ttu-id="4ac93-128">Příkaz s hodinkami</span><span class="sxs-lookup"><span data-stu-id="4ac93-128">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="4ac93-129">Spusťte příkaz DotNet</span><span class="sxs-lookup"><span data-stu-id="4ac93-129">dotnet run</span></span> | <span data-ttu-id="4ac93-130">Spustit sledování DotNet</span><span class="sxs-lookup"><span data-stu-id="4ac93-130">dotnet watch run</span></span> |
| <span data-ttu-id="4ac93-131">DotNet spustit netcoreapp2.0 -f</span><span class="sxs-lookup"><span data-stu-id="4ac93-131">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="4ac93-132">sledování DotNet spustit netcoreapp2.0 -f</span><span class="sxs-lookup"><span data-stu-id="4ac93-132">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="4ac93-133">Spustit netcoreapp2.0 -f - DotNet-arg1</span><span class="sxs-lookup"><span data-stu-id="4ac93-133">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="4ac93-134">sledování DotNet spustit netcoreapp2.0 -f –--arg1</span><span class="sxs-lookup"><span data-stu-id="4ac93-134">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="4ac93-135">DotNet test</span><span class="sxs-lookup"><span data-stu-id="4ac93-135">dotnet test</span></span> | <span data-ttu-id="4ac93-136">DotNet watch test</span><span class="sxs-lookup"><span data-stu-id="4ac93-136">dotnet watch test</span></span> |

<span data-ttu-id="4ac93-137">Spustit `dotnet watch run` v *WebApp* složky.</span><span class="sxs-lookup"><span data-stu-id="4ac93-137">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="4ac93-138">Určuje výstup konzoly `watch` byla spuštěna.</span><span class="sxs-lookup"><span data-stu-id="4ac93-138">The console output indicates `watch` has started.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="4ac93-139">Změny pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="4ac93-139">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="4ac93-140">Ujistěte se, že `dotnet watch` běží.</span><span class="sxs-lookup"><span data-stu-id="4ac93-140">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="4ac93-141">Oprava chyby v `Product` metoda *MathController.cs* tak, aby vracel produktu a nikoli součet:</span><span class="sxs-lookup"><span data-stu-id="4ac93-141">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
  return a * b;
}
```

<span data-ttu-id="4ac93-142">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="4ac93-142">Save the file.</span></span> <span data-ttu-id="4ac93-143">Výstup konzoly znamená, že `dotnet watch` byla zjištěna změna souboru a restartovat aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ac93-143">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="4ac93-144">Ověřte `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.</span><span class="sxs-lookup"><span data-stu-id="4ac93-144">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="4ac93-145">Spustit testy pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="4ac93-145">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="4ac93-146">Změnit `Product` metoda *MathController.cs* zpět do vrací součet.</span><span class="sxs-lookup"><span data-stu-id="4ac93-146">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="4ac93-147">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="4ac93-147">Save the file.</span></span>
1. <span data-ttu-id="4ac93-148">V příkazovém řádku přejděte *WebAppTests* složky.</span><span class="sxs-lookup"><span data-stu-id="4ac93-148">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="4ac93-149">Spustit [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="4ac93-149">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="4ac93-150">Spusťte `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="4ac93-150">Run `dotnet watch test`.</span></span> <span data-ttu-id="4ac93-151">Výstup udává, že se nezdařil test a že sledovací proces čeká na změny v souboru:</span><span class="sxs-lookup"><span data-stu-id="4ac93-151">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="4ac93-152">Oprava `Product` metoda kódu tak, aby vracel produktu.</span><span class="sxs-lookup"><span data-stu-id="4ac93-152">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="4ac93-153">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="4ac93-153">Save the file.</span></span>

<span data-ttu-id="4ac93-154">`dotnet watch` zjistí změnu souboru a znovu spustí testy.</span><span class="sxs-lookup"><span data-stu-id="4ac93-154">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="4ac93-155">Výstup konzoly označuje, testy proběhly úspěšně.</span><span class="sxs-lookup"><span data-stu-id="4ac93-155">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="4ac93-156">Upravit seznam souborů ke sledování</span><span class="sxs-lookup"><span data-stu-id="4ac93-156">Customize files list to watch</span></span>

<span data-ttu-id="4ac93-157">Ve výchozím nastavení `dotnet-watch` sleduje všechny soubory, které odpovídají následujícím vzorům glob:</span><span class="sxs-lookup"><span data-stu-id="4ac93-157">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="4ac93-158">Další položky lze přidat do seznamu sledovaných úpravou *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="4ac93-158">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="4ac93-159">Položky lze jednotlivě nebo s použitím glob vzory.</span><span class="sxs-lookup"><span data-stu-id="4ac93-159">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="4ac93-160">Odhlásit souborů, které mají být sledovány.</span><span class="sxs-lookup"><span data-stu-id="4ac93-160">Opt-out of files to be watched</span></span>

<span data-ttu-id="4ac93-161">`dotnet-watch` je možné nakonfigurovat ignorovat výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="4ac93-161">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="4ac93-162">Chcete-li ignorovat konkrétní soubory, přidejte `Watch="false"` atributu na definici položky v *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="4ac93-162">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

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

## <a name="custom-watch-projects"></a><span data-ttu-id="4ac93-163">Vlastní sledování projektů</span><span class="sxs-lookup"><span data-stu-id="4ac93-163">Custom watch projects</span></span>

<span data-ttu-id="4ac93-164">`dotnet-watch` není omezena na projekty jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="4ac93-164">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="4ac93-165">Vlastní sledování projekty mohou být vytvořeny pro zpracování různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="4ac93-165">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="4ac93-166">Vezměte v úvahu následující rozložení projektu:</span><span class="sxs-lookup"><span data-stu-id="4ac93-166">Consider the following project layout:</span></span>

* <span data-ttu-id="4ac93-167">**Test /**</span><span class="sxs-lookup"><span data-stu-id="4ac93-167">**test/**</span></span>
  * <span data-ttu-id="4ac93-168">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="4ac93-168">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="4ac93-169">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="4ac93-169">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="4ac93-170">Pokud je cílem jak oba projekty, vytvořte vlastní projekt soubor nakonfigurovaný tak, aby podívejte se na oba projekty:</span><span class="sxs-lookup"><span data-stu-id="4ac93-170">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

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

<span data-ttu-id="4ac93-171">Spusťte soubor sledování na obou projektů změňte na *testování* složky.</span><span class="sxs-lookup"><span data-stu-id="4ac93-171">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="4ac93-172">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4ac93-172">Execute the following command:</span></span>

```console
dotnet watch msbuild /t:Test
```

<span data-ttu-id="4ac93-173">VSTest provede při změn v souboru v obou testovacího projektu.</span><span class="sxs-lookup"><span data-stu-id="4ac93-173">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="4ac93-174">`dotnet-watch` v Githubu</span><span class="sxs-lookup"><span data-stu-id="4ac93-174">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="4ac93-175">`dotnet-watch` je součástí Githubu [aspnet/AspNetCore úložiště](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="4ac93-175">`dotnet-watch` is part of the GitHub [aspnet/AspNetCore repository](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
