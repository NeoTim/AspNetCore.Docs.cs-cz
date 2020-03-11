---
title: Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů
author: rick-anderson
description: V tomto kurzu se dozvíte, jak nainstalovat a používat nástroj sledovacího procesu souborů (dotnet Watch) .NET Core CLI v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: bedb3e6a65839db915ca7bc821a267a14d34bf30
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667410"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="05858-103">Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů</span><span class="sxs-lookup"><span data-stu-id="05858-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="05858-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [vítěz Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="05858-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="05858-105">[dotnet Watch](https://www.nuget.org/packages/dotnet-watch) je nástroj, který spouští příkaz [.NET Core CLI](/dotnet/core/tools) , když se změní zdrojové soubory.</span><span class="sxs-lookup"><span data-stu-id="05858-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="05858-106">Například změna souboru může aktivovat kompilaci, spuštění testu nebo nasazení.</span><span class="sxs-lookup"><span data-stu-id="05858-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="05858-107">V tomto kurzu se používá existující webové rozhraní API se dvěma koncovými body: jeden, který vrací součet, a jeden, který vrací produkt.</span><span class="sxs-lookup"><span data-stu-id="05858-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="05858-108">Metoda produktu obsahuje chybu, která je v tomto kurzu opravena.</span><span class="sxs-lookup"><span data-stu-id="05858-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="05858-109">Stáhněte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="05858-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="05858-110">Skládá se ze dvou projektů: *WebApp* (ASP.NET Core Web API) a *WebAppTests* (testování částí webového rozhraní API).</span><span class="sxs-lookup"><span data-stu-id="05858-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="05858-111">V příkazovém prostředí přejděte do složky *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="05858-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="05858-112">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="05858-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="05858-113">Pomocí `dotnet run --project <PROJECT>` můžete určit projekt, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="05858-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="05858-114">Například spuštění `dotnet run --project WebApp` z kořenového adresáře ukázkové aplikace spustí také projekt *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="05858-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="05858-115">Výstup konzoly zobrazuje zprávy podobné následujícímu (což značí, že aplikace běží a čekají na žádosti):</span><span class="sxs-lookup"><span data-stu-id="05858-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="05858-116">Ve webovém prohlížeči přejděte na `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="05858-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="05858-117">Měl by se zobrazit výsledek `9`.</span><span class="sxs-lookup"><span data-stu-id="05858-117">You should see the result of `9`.</span></span>

<span data-ttu-id="05858-118">Přejděte na rozhraní API produktu (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="05858-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="05858-119">Vrátí `9`, ne `20` podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="05858-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="05858-120">Tento problém je opravený později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="05858-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="05858-121">Přidání `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="05858-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="05858-122">Nástroj `dotnet watch` sledovacího procesu souborů je součástí verze 2.1.300 .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="05858-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="05858-123">Při použití starší verze .NET Core SDK jsou vyžadovány následující kroky.</span><span class="sxs-lookup"><span data-stu-id="05858-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="05858-124">Přidat `Microsoft.DotNet.Watcher.Tools` odkaz na balíček do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="05858-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="05858-125">Nainstalujte balíček `Microsoft.DotNet.Watcher.Tools` spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="05858-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="05858-126">Spouštění příkazů .NET Core CLI pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="05858-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="05858-127">Libovolný [.NET Core CLI příkaz](/dotnet/core/tools#cli-commands) lze spustit s `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="05858-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="05858-128">Příklad:</span><span class="sxs-lookup"><span data-stu-id="05858-128">For example:</span></span>

| <span data-ttu-id="05858-129">Příkaz</span><span class="sxs-lookup"><span data-stu-id="05858-129">Command</span></span> | <span data-ttu-id="05858-130">Příkaz se sledováním</span><span class="sxs-lookup"><span data-stu-id="05858-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="05858-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="05858-131">dotnet run</span></span> | <span data-ttu-id="05858-132">dotnet – běh kukátka</span><span class="sxs-lookup"><span data-stu-id="05858-132">dotnet watch run</span></span> |
| <span data-ttu-id="05858-133">příkaz dotnet Run-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="05858-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="05858-134">dotnet Watch Run-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="05858-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="05858-135">dotnet Run-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="05858-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="05858-136">dotnet Watch Run-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="05858-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="05858-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="05858-137">dotnet test</span></span> | <span data-ttu-id="05858-138">test výrazu dotnet</span><span class="sxs-lookup"><span data-stu-id="05858-138">dotnet watch test</span></span> |

<span data-ttu-id="05858-139">Spusťte `dotnet watch run` ve složce *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="05858-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="05858-140">Výstup konzoly indikuje, `watch` bylo zahájeno.</span><span class="sxs-lookup"><span data-stu-id="05858-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="05858-141">Pomocí `dotnet watch --project <PROJECT>` můžete určit projekt, který chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="05858-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="05858-142">Například spuštění `dotnet watch --project WebApp run` z kořenového adresáře ukázkové aplikace bude také spuštěno a sledovat projekt *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="05858-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="05858-143">Provedení změn pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="05858-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="05858-144">Ujistěte se, že je spuštěný `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="05858-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="05858-145">Opravte chybu v metodě `Product` *MathController.cs* , takže vrátí produkt, a ne součet:</span><span class="sxs-lookup"><span data-stu-id="05858-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="05858-146">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="05858-146">Save the file.</span></span> <span data-ttu-id="05858-147">Výstup konzoly indikuje, že `dotnet watch` zjistil změnu souboru a restartuje aplikaci.</span><span class="sxs-lookup"><span data-stu-id="05858-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="05858-148">Ověřte, `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.</span><span class="sxs-lookup"><span data-stu-id="05858-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="05858-149">Spouštění testů pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="05858-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="05858-150">Změňte metodu `Product` *MathController.cs* zpět na vrácení součtu.</span><span class="sxs-lookup"><span data-stu-id="05858-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="05858-151">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="05858-151">Save the file.</span></span>
1. <span data-ttu-id="05858-152">V příkazovém prostředí přejděte do složky *WebAppTests* .</span><span class="sxs-lookup"><span data-stu-id="05858-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="05858-153">Spusťte [dotnet Restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="05858-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="05858-154">Spusťte `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="05858-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="05858-155">Jeho výstup označuje, že se test nezdařil a že sledovací proces čeká na změny souborů:</span><span class="sxs-lookup"><span data-stu-id="05858-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="05858-156">Opravte kód metody `Product` tak, aby vrátil produkt.</span><span class="sxs-lookup"><span data-stu-id="05858-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="05858-157">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="05858-157">Save the file.</span></span>

<span data-ttu-id="05858-158">`dotnet watch` detekuje změnu souboru a znovu spustí testy.</span><span class="sxs-lookup"><span data-stu-id="05858-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="05858-159">Výstup konzoly indikuje úspěšné testy.</span><span class="sxs-lookup"><span data-stu-id="05858-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="05858-160">Přizpůsobení seznamu souborů pro sledování</span><span class="sxs-lookup"><span data-stu-id="05858-160">Customize files list to watch</span></span>

<span data-ttu-id="05858-161">Ve výchozím nastavení `dotnet-watch` sleduje všechny soubory, které odpovídají následujícím vzorům glob:</span><span class="sxs-lookup"><span data-stu-id="05858-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="05858-162">Další položky lze přidat do seznamu sledování úpravou souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="05858-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="05858-163">Položky lze zadat jednotlivě nebo pomocí glob vzorů.</span><span class="sxs-lookup"><span data-stu-id="05858-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="05858-164">Výslovný souhlas se soubory, které se mají sledovat</span><span class="sxs-lookup"><span data-stu-id="05858-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="05858-165">`dotnet-watch` lze nakonfigurovat tak, aby ignorovala výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="05858-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="05858-166">Chcete-li ignorovat konkrétní soubory, přidejte atribut `Watch="false"` do definice položky v souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="05858-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

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

## <a name="custom-watch-projects"></a><span data-ttu-id="05858-167">Vlastní projekty kukátka</span><span class="sxs-lookup"><span data-stu-id="05858-167">Custom watch projects</span></span>

<span data-ttu-id="05858-168">`dotnet-watch` nejsou omezeny C# na projekty.</span><span class="sxs-lookup"><span data-stu-id="05858-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="05858-169">Je možné vytvořit vlastní projekty kukátka pro zpracování různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="05858-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="05858-170">Vezměte v úvahu následující rozložení projektu:</span><span class="sxs-lookup"><span data-stu-id="05858-170">Consider the following project layout:</span></span>

* <span data-ttu-id="05858-171">**napaden**</span><span class="sxs-lookup"><span data-stu-id="05858-171">**test/**</span></span>
  * <span data-ttu-id="05858-172">*UnitTests/UnitTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="05858-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="05858-173">*IntegrationTests/IntegrationTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="05858-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="05858-174">Pokud je cílem sledovat oba projekty, vytvořte vlastní soubor projektu nakonfigurovaný pro sledování obou projektů:</span><span class="sxs-lookup"><span data-stu-id="05858-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

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

<span data-ttu-id="05858-175">Chcete-li spustit sledování souborů na obou projektech, přejděte do složky *test* .</span><span class="sxs-lookup"><span data-stu-id="05858-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="05858-176">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="05858-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="05858-177">VSTest se spustí při každé změně souboru v jednom testovacím projektu.</span><span class="sxs-lookup"><span data-stu-id="05858-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="05858-178">`dotnet-watch` v GitHubu</span><span class="sxs-lookup"><span data-stu-id="05858-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="05858-179">`dotnet-watch` je součástí [úložiště dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)v GitHubu.</span><span class="sxs-lookup"><span data-stu-id="05858-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
