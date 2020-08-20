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
ms.openlocfilehash: cc152c2ca553b00619ddbf829f6044867c53bb98
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635135"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="87419-103">Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů</span><span class="sxs-lookup"><span data-stu-id="87419-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="87419-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [vítěz Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="87419-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="87419-105">`dotnet watch` je nástroj, který spouští příkaz [.NET Core CLI](/dotnet/core/tools) , když se změní zdrojové soubory.</span><span class="sxs-lookup"><span data-stu-id="87419-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="87419-106">Například změna souboru může aktivovat kompilaci, spuštění testu nebo nasazení.</span><span class="sxs-lookup"><span data-stu-id="87419-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="87419-107">V tomto kurzu se používá existující webové rozhraní API se dvěma koncovými body: jeden, který vrací součet, a jeden, který vrací produkt.</span><span class="sxs-lookup"><span data-stu-id="87419-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="87419-108">Metoda produktu obsahuje chybu, která je v tomto kurzu opravena.</span><span class="sxs-lookup"><span data-stu-id="87419-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="87419-109">Stáhněte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="87419-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="87419-110">Skládá se ze dvou projektů: *WebApp* (ASP.NET Core Web API) a *WebAppTests* (testování částí webového rozhraní API).</span><span class="sxs-lookup"><span data-stu-id="87419-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="87419-111">V příkazovém prostředí přejděte do složky *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="87419-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="87419-112">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="87419-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="87419-113">Můžete použít `dotnet run --project <PROJECT>` k určení projektu, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="87419-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="87419-114">Například spuštění `dotnet run --project WebApp` z kořenového adresáře ukázkové aplikace spustí také projekt *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="87419-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="87419-115">Výstup konzoly zobrazuje zprávy podobné následujícímu (což značí, že aplikace běží a čekají na žádosti):</span><span class="sxs-lookup"><span data-stu-id="87419-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="87419-116">Ve webovém prohlížeči přejděte na adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="87419-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="87419-117">Měl by se zobrazit výsledek `9` .</span><span class="sxs-lookup"><span data-stu-id="87419-117">You should see the result of `9`.</span></span>

<span data-ttu-id="87419-118">Přejděte na rozhraní API produktu ( `http://localhost:<port number>/api/math/product?a=4&b=5` ).</span><span class="sxs-lookup"><span data-stu-id="87419-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="87419-119">Vrátí `9` , ne `20` jako byste očekávali.</span><span class="sxs-lookup"><span data-stu-id="87419-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="87419-120">Tento problém je opravený později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="87419-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="87419-121">Přidat `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="87419-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="87419-122">`dotnet watch`Nástroj sledovacího procesu souborů je součástí 2.1.300 verze .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="87419-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="87419-123">Při použití starší verze .NET Core SDK jsou vyžadovány následující kroky.</span><span class="sxs-lookup"><span data-stu-id="87419-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="87419-124">Přidejte `Microsoft.DotNet.Watcher.Tools` odkaz na balíček do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="87419-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="87419-125">Nainstalujte `Microsoft.DotNet.Watcher.Tools` balíček spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="87419-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="87419-126">Spouštění příkazů .NET Core CLI pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="87419-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="87419-127">Libovolný [.NET Core CLI příkaz](/dotnet/core/tools#cli-commands) lze spustit pomocí příkazu `dotnet watch` .</span><span class="sxs-lookup"><span data-stu-id="87419-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="87419-128">Příklad:</span><span class="sxs-lookup"><span data-stu-id="87419-128">For example:</span></span>

| <span data-ttu-id="87419-129">Příkaz</span><span class="sxs-lookup"><span data-stu-id="87419-129">Command</span></span> | <span data-ttu-id="87419-130">Příkaz se sledováním</span><span class="sxs-lookup"><span data-stu-id="87419-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="87419-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="87419-131">dotnet run</span></span> | <span data-ttu-id="87419-132">dotnet – běh kukátka</span><span class="sxs-lookup"><span data-stu-id="87419-132">dotnet watch run</span></span> |
| <span data-ttu-id="87419-133">příkaz dotnet Run-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="87419-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="87419-134">dotnet Watch Run-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="87419-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="87419-135">dotnet Run-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="87419-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="87419-136">dotnet Watch Run-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="87419-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="87419-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="87419-137">dotnet test</span></span> | <span data-ttu-id="87419-138">test výrazu dotnet</span><span class="sxs-lookup"><span data-stu-id="87419-138">dotnet watch test</span></span> |

<span data-ttu-id="87419-139">Spusťte `dotnet watch run` ve složce *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="87419-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="87419-140">Výstup konzoly indikuje, že `watch` byl spuštěn.</span><span class="sxs-lookup"><span data-stu-id="87419-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="87419-141">Můžete použít `dotnet watch --project <PROJECT>` k určení projektu, který se má sledovat.</span><span class="sxs-lookup"><span data-stu-id="87419-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="87419-142">Například spuštění `dotnet watch --project WebApp run` z kořenového adresáře ukázkové aplikace bude také spuštěno a sledovat projekt *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="87419-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="87419-143">Provést změny pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="87419-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="87419-144">Ujistěte `dotnet watch` se, že je spuštěný.</span><span class="sxs-lookup"><span data-stu-id="87419-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="87419-145">Opravte chybu v `Product` metodě *MathController.cs* tak, aby vrátila produkt, a ne součet:</span><span class="sxs-lookup"><span data-stu-id="87419-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="87419-146">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="87419-146">Save the file.</span></span> <span data-ttu-id="87419-147">Výstup konzoly indikuje, že `dotnet watch` zjistil změnu souboru a restartuje aplikaci.</span><span class="sxs-lookup"><span data-stu-id="87419-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="87419-148">Ověřování `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.</span><span class="sxs-lookup"><span data-stu-id="87419-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="87419-149">Spustit testy pomocí `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="87419-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="87419-150">Změňte `Product` metodu *MathController.cs* zpět na vrácení součtu.</span><span class="sxs-lookup"><span data-stu-id="87419-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="87419-151">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="87419-151">Save the file.</span></span>
1. <span data-ttu-id="87419-152">V příkazovém prostředí přejděte do složky *WebAppTests* .</span><span class="sxs-lookup"><span data-stu-id="87419-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="87419-153">Spusťte [dotnet Restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="87419-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="87419-154">Spusťte `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="87419-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="87419-155">Jeho výstup označuje, že se test nezdařil a že sledovací proces čeká na změny souborů:</span><span class="sxs-lookup"><span data-stu-id="87419-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="87419-156">Opravte `Product` kód metody tak, aby vrátil produkt.</span><span class="sxs-lookup"><span data-stu-id="87419-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="87419-157">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="87419-157">Save the file.</span></span>

<span data-ttu-id="87419-158">`dotnet watch` zjistí změnu souboru a znovu spustí testy.</span><span class="sxs-lookup"><span data-stu-id="87419-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="87419-159">Výstup konzoly indikuje úspěšné testy.</span><span class="sxs-lookup"><span data-stu-id="87419-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="87419-160">Přizpůsobení seznamu souborů pro sledování</span><span class="sxs-lookup"><span data-stu-id="87419-160">Customize files list to watch</span></span>

<span data-ttu-id="87419-161">Ve výchozím nastavení `dotnet-watch` sleduje všechny soubory, které odpovídají následujícím vzorům glob:</span><span class="sxs-lookup"><span data-stu-id="87419-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="87419-162">Další položky lze přidat do seznamu sledování úpravou souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="87419-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="87419-163">Položky lze zadat jednotlivě nebo pomocí glob vzorů.</span><span class="sxs-lookup"><span data-stu-id="87419-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="87419-164">Výslovný souhlas se soubory, které se mají sledovat</span><span class="sxs-lookup"><span data-stu-id="87419-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="87419-165">`dotnet-watch` dá se nakonfigurovat tak, aby ignoroval výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="87419-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="87419-166">Chcete-li ignorovat konkrétní soubory, přidejte `Watch="false"` atribut do definice položky v souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="87419-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

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

## <a name="custom-watch-projects"></a><span data-ttu-id="87419-167">Vlastní projekty kukátka</span><span class="sxs-lookup"><span data-stu-id="87419-167">Custom watch projects</span></span>

<span data-ttu-id="87419-168">`dotnet-watch` není omezen na projekty C#.</span><span class="sxs-lookup"><span data-stu-id="87419-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="87419-169">Je možné vytvořit vlastní projekty kukátka pro zpracování různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="87419-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="87419-170">Vezměte v úvahu následující rozložení projektu:</span><span class="sxs-lookup"><span data-stu-id="87419-170">Consider the following project layout:</span></span>

* <span data-ttu-id="87419-171">**napaden**</span><span class="sxs-lookup"><span data-stu-id="87419-171">**test/**</span></span>
  * <span data-ttu-id="87419-172">*UnitTests/UnitTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="87419-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="87419-173">*IntegrationTests/IntegrationTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="87419-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="87419-174">Pokud je cílem sledovat oba projekty, vytvořte vlastní soubor projektu nakonfigurovaný pro sledování obou projektů:</span><span class="sxs-lookup"><span data-stu-id="87419-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

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

<span data-ttu-id="87419-175">Chcete-li spustit sledování souborů na obou projektech, přejděte do složky *test* .</span><span class="sxs-lookup"><span data-stu-id="87419-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="87419-176">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="87419-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="87419-177">VSTest se spustí při každé změně souboru v jednom testovacím projektu.</span><span class="sxs-lookup"><span data-stu-id="87419-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="87419-178">`dotnet-watch` v GitHubu</span><span class="sxs-lookup"><span data-stu-id="87419-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="87419-179">`dotnet-watch` je součástí [úložiště dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)v GitHubu.</span><span class="sxs-lookup"><span data-stu-id="87419-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
