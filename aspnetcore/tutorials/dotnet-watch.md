---
title: Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů
author: rick-anderson
description: V tomto kurzu se dozvíte, jak nainstalovat a používat nástroj sledovacího procesu souborů (dotnet Watch) .NET Core CLI v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: 053c98ba032c85b61776d5b5644c5575cd4f890c
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828994"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="c7050-103">Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů</span><span class="sxs-lookup"><span data-stu-id="c7050-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="c7050-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [vítěz Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="c7050-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="c7050-105">[dotnet Watch](https://www.nuget.org/packages/dotnet-watch) je nástroj, který spouští příkaz [.NET Core CLI](/dotnet/core/tools) , když se změní zdrojové soubory.</span><span class="sxs-lookup"><span data-stu-id="c7050-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="c7050-106">Například změna souboru může aktivovat kompilaci, spuštění testu nebo nasazení.</span><span class="sxs-lookup"><span data-stu-id="c7050-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="c7050-107">V tomto kurzu se používá existující webové rozhraní API se dvěma koncovými body: jeden, který vrací součet, a jeden, který vrací produkt.</span><span class="sxs-lookup"><span data-stu-id="c7050-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="c7050-108">Metoda produktu obsahuje chybu, která je v tomto kurzu opravena.</span><span class="sxs-lookup"><span data-stu-id="c7050-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="c7050-109">Stáhněte si [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="c7050-109">Download the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="c7050-110">Skládá se ze dvou projektů: *WebApp* (ASP.NET Core Web API) a *WebAppTests* (testování částí webového rozhraní API).</span><span class="sxs-lookup"><span data-stu-id="c7050-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="c7050-111">V příkazovém prostředí přejděte do složky *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="c7050-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="c7050-112">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c7050-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="c7050-113">Pomocí `dotnet run --project <PROJECT>` můžete určit projekt, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="c7050-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="c7050-114">Například spuštění `dotnet run --project WebApp` z kořenového adresáře ukázkové aplikace spustí také projekt *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="c7050-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="c7050-115">Výstup konzoly zobrazuje zprávy podobné následujícímu (což značí, že aplikace běží a čekají na žádosti):</span><span class="sxs-lookup"><span data-stu-id="c7050-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="c7050-116">Ve webovém prohlížeči přejděte na adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="c7050-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="c7050-117">Měl by se zobrazit výsledek `9`.</span><span class="sxs-lookup"><span data-stu-id="c7050-117">You should see the result of `9`.</span></span>

<span data-ttu-id="c7050-118">Přejděte na rozhraní API produktu (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="c7050-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="c7050-119">Vrátí `9`, ne `20` podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="c7050-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="c7050-120">Tento problém je opravený později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c7050-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="c7050-121">Přidání `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="c7050-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="c7050-122">Nástroj `dotnet watch` sledovacího procesu souborů je součástí verze 2.1.300 .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="c7050-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="c7050-123">Při použití starší verze .NET Core SDK jsou vyžadovány následující kroky.</span><span class="sxs-lookup"><span data-stu-id="c7050-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="c7050-124">Přidat `Microsoft.DotNet.Watcher.Tools` odkaz na balíček do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="c7050-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="c7050-125">Nainstalujte balíček `Microsoft.DotNet.Watcher.Tools` spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="c7050-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="c7050-126">Spouštění příkazů .NET Core CLI pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="c7050-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="c7050-127">Libovolný [.NET Core CLI příkaz](/dotnet/core/tools#cli-commands) lze spustit s `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="c7050-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="c7050-128">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c7050-128">For example:</span></span>

| <span data-ttu-id="c7050-129">Příkaz</span><span class="sxs-lookup"><span data-stu-id="c7050-129">Command</span></span> | <span data-ttu-id="c7050-130">Příkaz se sledováním</span><span class="sxs-lookup"><span data-stu-id="c7050-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="c7050-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="c7050-131">dotnet run</span></span> | <span data-ttu-id="c7050-132">dotnet – běh kukátka</span><span class="sxs-lookup"><span data-stu-id="c7050-132">dotnet watch run</span></span> |
| <span data-ttu-id="c7050-133">příkaz dotnet Run-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="c7050-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="c7050-134">dotnet Watch Run-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="c7050-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="c7050-135">dotnet Run-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="c7050-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="c7050-136">dotnet Watch Run-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="c7050-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="c7050-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="c7050-137">dotnet test</span></span> | <span data-ttu-id="c7050-138">test výrazu dotnet</span><span class="sxs-lookup"><span data-stu-id="c7050-138">dotnet watch test</span></span> |

<span data-ttu-id="c7050-139">Spusťte `dotnet watch run` ve složce *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="c7050-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="c7050-140">Výstup konzoly indikuje, `watch` bylo zahájeno.</span><span class="sxs-lookup"><span data-stu-id="c7050-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="c7050-141">Pomocí `dotnet watch --project <PROJECT>` můžete určit projekt, který chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="c7050-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="c7050-142">Například spuštění `dotnet watch --project WebApp run` z kořenového adresáře ukázkové aplikace bude také spuštěno a sledovat projekt *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="c7050-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="c7050-143">Provedení změn pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="c7050-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="c7050-144">Ujistěte se, že je spuštěný `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="c7050-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="c7050-145">Opravte chybu v metodě `Product` *MathController.cs* , takže vrátí produkt, a ne součet:</span><span class="sxs-lookup"><span data-stu-id="c7050-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="c7050-146">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="c7050-146">Save the file.</span></span> <span data-ttu-id="c7050-147">Výstup konzoly indikuje, že `dotnet watch` zjistil změnu souboru a restartuje aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c7050-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="c7050-148">Ověřte, `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.</span><span class="sxs-lookup"><span data-stu-id="c7050-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="c7050-149">Spouštění testů pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="c7050-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="c7050-150">Změňte metodu `Product` *MathController.cs* zpět na vrácení součtu.</span><span class="sxs-lookup"><span data-stu-id="c7050-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="c7050-151">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="c7050-151">Save the file.</span></span>
1. <span data-ttu-id="c7050-152">V příkazovém prostředí přejděte do složky *WebAppTests* .</span><span class="sxs-lookup"><span data-stu-id="c7050-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="c7050-153">Spusťte [dotnet Restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="c7050-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="c7050-154">Spusťte `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="c7050-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="c7050-155">Jeho výstup označuje, že se test nezdařil a že sledovací proces čeká na změny souborů:</span><span class="sxs-lookup"><span data-stu-id="c7050-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="c7050-156">Opravte kód metody `Product` tak, aby vrátil produkt.</span><span class="sxs-lookup"><span data-stu-id="c7050-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="c7050-157">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="c7050-157">Save the file.</span></span>

<span data-ttu-id="c7050-158">`dotnet watch` detekuje změnu souboru a znovu spustí testy.</span><span class="sxs-lookup"><span data-stu-id="c7050-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="c7050-159">Výstup konzoly indikuje úspěšné testy.</span><span class="sxs-lookup"><span data-stu-id="c7050-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="c7050-160">Přizpůsobení seznamu souborů pro sledování</span><span class="sxs-lookup"><span data-stu-id="c7050-160">Customize files list to watch</span></span>

<span data-ttu-id="c7050-161">Ve výchozím nastavení `dotnet-watch` sleduje všechny soubory, které odpovídají následujícím vzorům glob:</span><span class="sxs-lookup"><span data-stu-id="c7050-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="c7050-162">Další položky lze přidat do seznamu sledování úpravou souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="c7050-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="c7050-163">Položky lze zadat jednotlivě nebo pomocí glob vzorů.</span><span class="sxs-lookup"><span data-stu-id="c7050-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="c7050-164">Výslovný souhlas se soubory, které se mají sledovat</span><span class="sxs-lookup"><span data-stu-id="c7050-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="c7050-165">`dotnet-watch` lze nakonfigurovat tak, aby ignorovala výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="c7050-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="c7050-166">Chcete-li ignorovat konkrétní soubory, přidejte atribut `Watch="false"` do definice položky v souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="c7050-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

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

## <a name="custom-watch-projects"></a><span data-ttu-id="c7050-167">Vlastní projekty kukátka</span><span class="sxs-lookup"><span data-stu-id="c7050-167">Custom watch projects</span></span>

<span data-ttu-id="c7050-168">`dotnet-watch` nejsou omezeny C# na projekty.</span><span class="sxs-lookup"><span data-stu-id="c7050-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="c7050-169">Je možné vytvořit vlastní projekty kukátka pro zpracování různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="c7050-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="c7050-170">Vezměte v úvahu následující rozložení projektu:</span><span class="sxs-lookup"><span data-stu-id="c7050-170">Consider the following project layout:</span></span>

* <span data-ttu-id="c7050-171">**napaden**</span><span class="sxs-lookup"><span data-stu-id="c7050-171">**test/**</span></span>
  * <span data-ttu-id="c7050-172">*UnitTests/UnitTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="c7050-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="c7050-173">*IntegrationTests/IntegrationTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="c7050-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="c7050-174">Pokud je cílem sledovat oba projekty, vytvořte vlastní soubor projektu nakonfigurovaný pro sledování obou projektů:</span><span class="sxs-lookup"><span data-stu-id="c7050-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

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

<span data-ttu-id="c7050-175">Chcete-li spustit sledování souborů na obou projektech, přejděte do složky *test* .</span><span class="sxs-lookup"><span data-stu-id="c7050-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="c7050-176">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c7050-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="c7050-177">VSTest se spustí při každé změně souboru v jednom testovacím projektu.</span><span class="sxs-lookup"><span data-stu-id="c7050-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="c7050-178">`dotnet-watch` v GitHubu</span><span class="sxs-lookup"><span data-stu-id="c7050-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="c7050-179">`dotnet-watch` je součástí [úložiště dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)v GitHubu.</span><span class="sxs-lookup"><span data-stu-id="c7050-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
