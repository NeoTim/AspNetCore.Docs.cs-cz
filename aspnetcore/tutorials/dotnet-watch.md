---
title: Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů
author: rick-anderson
description: Tento kurz ukazuje, jak nainstalovat a používat nástroj sledování souborů (dotnet watch) rozhraní .NET Core CLI v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: bedb3e6a65839db915ca7bc821a267a14d34bf30
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667410"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="518fe-103">Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů</span><span class="sxs-lookup"><span data-stu-id="518fe-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="518fe-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="518fe-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="518fe-105">[Dotnet watch](https://www.nuget.org/packages/dotnet-watch) je nástroj, který při změně zdrojových souborů spouští příkaz [.NET Core CLI.](/dotnet/core/tools)</span><span class="sxs-lookup"><span data-stu-id="518fe-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="518fe-106">Změna souboru může například aktivovat kompilaci, spuštění testu nebo nasazení.</span><span class="sxs-lookup"><span data-stu-id="518fe-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="518fe-107">Tento kurz používá existující webové rozhraní API se dvěma koncovými body: jeden, který vrací součet a jeden, který vrací produkt.</span><span class="sxs-lookup"><span data-stu-id="518fe-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="518fe-108">Metoda produktu má chybu, která je opravena v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="518fe-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="518fe-109">Stáhněte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="518fe-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="518fe-110">Skládá se ze dvou projektů: *WebApp* (ASP.NET základní webové rozhraní API) a *WebAppTests* (testování částí pro webové rozhraní API).</span><span class="sxs-lookup"><span data-stu-id="518fe-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="518fe-111">V příkazovém prostředí přejděte do složky *WebApp.*</span><span class="sxs-lookup"><span data-stu-id="518fe-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="518fe-112">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="518fe-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="518fe-113">Můžete použít `dotnet run --project <PROJECT>` k určení projektu ke spuštění.</span><span class="sxs-lookup"><span data-stu-id="518fe-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="518fe-114">Například spuštění `dotnet run --project WebApp` z kořenového adresáře ukázkové aplikace také spustí projekt *WebApp.*</span><span class="sxs-lookup"><span data-stu-id="518fe-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="518fe-115">Výstup konzoly zobrazuje zprávy podobné následujícímu (označující, že aplikace je spuštěná a čeká na požadavky):</span><span class="sxs-lookup"><span data-stu-id="518fe-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="518fe-116">Ve webovém prohlížeči přejděte na adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="518fe-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="518fe-117">Měli byste vidět `9`výsledek .</span><span class="sxs-lookup"><span data-stu-id="518fe-117">You should see the result of `9`.</span></span>

<span data-ttu-id="518fe-118">Přejděte do rozhraní`http://localhost:<port number>/api/math/product?a=4&b=5`API produktu ( ).</span><span class="sxs-lookup"><span data-stu-id="518fe-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="518fe-119">Vrací `9`se `20` , ne tak, jak byste očekávali.</span><span class="sxs-lookup"><span data-stu-id="518fe-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="518fe-120">Tento problém je vyřešen později v tutoriálu.</span><span class="sxs-lookup"><span data-stu-id="518fe-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="518fe-121">Přidat `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="518fe-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="518fe-122">Nástroj `dotnet watch` pro sledování souborů je součástí verze 2.1.300 sady .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="518fe-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="518fe-123">Při použití starší verze sady .NET Core SDK jsou vyžadovány následující kroky.</span><span class="sxs-lookup"><span data-stu-id="518fe-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="518fe-124">Přidejte `Microsoft.DotNet.Watcher.Tools` odkaz na balíček do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="518fe-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="518fe-125">Nainstalujte `Microsoft.DotNet.Watcher.Tools` balíček spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="518fe-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="518fe-126">Spuštění příkazů rozhraní PŘÍKAZOVÉHO PŘÍKAZU .NET Core CLI pomocí`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="518fe-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="518fe-127">Libovolný [příkaz CLI jádra .NET](/dotnet/core/tools#cli-commands) lze spustit pomocí příkazu `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="518fe-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="518fe-128">Příklad:</span><span class="sxs-lookup"><span data-stu-id="518fe-128">For example:</span></span>

| <span data-ttu-id="518fe-129">Příkaz</span><span class="sxs-lookup"><span data-stu-id="518fe-129">Command</span></span> | <span data-ttu-id="518fe-130">Příkaz s hodinkami</span><span class="sxs-lookup"><span data-stu-id="518fe-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="518fe-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="518fe-131">dotnet run</span></span> | <span data-ttu-id="518fe-132">dotnet sledovat spustit</span><span class="sxs-lookup"><span data-stu-id="518fe-132">dotnet watch run</span></span> |
| <span data-ttu-id="518fe-133">dotnet běh -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="518fe-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="518fe-134">dotnet hodinky spustit -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="518fe-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="518fe-135">dotnet běh -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="518fe-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="518fe-136">dotnet hodinky spustit -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="518fe-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="518fe-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="518fe-137">dotnet test</span></span> | <span data-ttu-id="518fe-138">dotnet hodinky test</span><span class="sxs-lookup"><span data-stu-id="518fe-138">dotnet watch test</span></span> |

<span data-ttu-id="518fe-139">Spustit `dotnet watch run` ve složce *WebApp.*</span><span class="sxs-lookup"><span data-stu-id="518fe-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="518fe-140">Výstup konzoly `watch` indikuje, že byl spuštěn.</span><span class="sxs-lookup"><span data-stu-id="518fe-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="518fe-141">Můžete použít `dotnet watch --project <PROJECT>` k určení projektu ke sledování.</span><span class="sxs-lookup"><span data-stu-id="518fe-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="518fe-142">Například spuštění `dotnet watch --project WebApp run` z kořenového adresáře ukázkové aplikace se také spustí a bude sledovat projekt *WebApp.*</span><span class="sxs-lookup"><span data-stu-id="518fe-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="518fe-143">Provádění změn pomocí`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="518fe-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="518fe-144">Ujistěte `dotnet watch` se, že běží.</span><span class="sxs-lookup"><span data-stu-id="518fe-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="518fe-145">Opravte chybu `Product` v metodě *MathController.cs* tak, aby se vrátil produkt a ne součet:</span><span class="sxs-lookup"><span data-stu-id="518fe-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="518fe-146">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="518fe-146">Save the file.</span></span> <span data-ttu-id="518fe-147">Výstup konzoly `dotnet watch` indikuje, že zjistil změnu souboru a restartoval aplikaci.</span><span class="sxs-lookup"><span data-stu-id="518fe-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="518fe-148">Ověřit `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.</span><span class="sxs-lookup"><span data-stu-id="518fe-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="518fe-149">Spuštění testů pomocí`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="518fe-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="518fe-150">Změňte `Product` metodu *MathController.cs* zpět na vrácení součtu.</span><span class="sxs-lookup"><span data-stu-id="518fe-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="518fe-151">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="518fe-151">Save the file.</span></span>
1. <span data-ttu-id="518fe-152">V příkazovém prostředí přejděte do složky *WebAppTests.*</span><span class="sxs-lookup"><span data-stu-id="518fe-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="518fe-153">Spusťte [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="518fe-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="518fe-154">Spusťte `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="518fe-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="518fe-155">Jeho výstup označuje, že test se nezdařil a že sledovací proces čeká na změny souboru:</span><span class="sxs-lookup"><span data-stu-id="518fe-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="518fe-156">Opravte `Product` kód metody tak, aby vrátil produkt.</span><span class="sxs-lookup"><span data-stu-id="518fe-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="518fe-157">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="518fe-157">Save the file.</span></span>

<span data-ttu-id="518fe-158">`dotnet watch`detekuje změnu souboru a znovu spustí testy.</span><span class="sxs-lookup"><span data-stu-id="518fe-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="518fe-159">Výstup konzoly označuje, že testy byly předány.</span><span class="sxs-lookup"><span data-stu-id="518fe-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="518fe-160">Přizpůsobení seznamu souborů ke sledování</span><span class="sxs-lookup"><span data-stu-id="518fe-160">Customize files list to watch</span></span>

<span data-ttu-id="518fe-161">Ve výchozím `dotnet-watch` nastavení sleduje všechny soubory odpovídající následujícím vzorům globu:</span><span class="sxs-lookup"><span data-stu-id="518fe-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="518fe-162">Další položky lze přidat do seznamu sledovaných položek úpravou souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="518fe-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="518fe-163">Položky lze zadat jednotlivě nebo pomocí glob vzory.</span><span class="sxs-lookup"><span data-stu-id="518fe-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="518fe-164">Odhlášení ze sledovaných souborů</span><span class="sxs-lookup"><span data-stu-id="518fe-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="518fe-165">`dotnet-watch`lze nakonfigurovat tak, aby ignorovala jeho výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="518fe-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="518fe-166">Chcete-li ignorovat určité `Watch="false"` soubory, přidejte atribut do definice položky v souboru *CSProJ:*</span><span class="sxs-lookup"><span data-stu-id="518fe-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

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

## <a name="custom-watch-projects"></a><span data-ttu-id="518fe-167">Vlastní projekty sledování</span><span class="sxs-lookup"><span data-stu-id="518fe-167">Custom watch projects</span></span>

<span data-ttu-id="518fe-168">`dotnet-watch`není omezena na projekty Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="518fe-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="518fe-169">Vlastní sledovat projekty lze vytvořit pro zpracování různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="518fe-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="518fe-170">Zvažte následující rozložení projektu:</span><span class="sxs-lookup"><span data-stu-id="518fe-170">Consider the following project layout:</span></span>

* <span data-ttu-id="518fe-171">**test/**</span><span class="sxs-lookup"><span data-stu-id="518fe-171">**test/**</span></span>
  * <span data-ttu-id="518fe-172">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="518fe-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="518fe-173">*IntegraceTesty/IntegraceTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="518fe-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="518fe-174">Pokud je cílem sledovat oba projekty, vytvořte vlastní soubor projektu nakonfigurovaný tak, aby sledoval oba projekty:</span><span class="sxs-lookup"><span data-stu-id="518fe-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

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

<span data-ttu-id="518fe-175">Chcete-li spustit sledování souborů v obou projektech, změňte na *testovací* složku.</span><span class="sxs-lookup"><span data-stu-id="518fe-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="518fe-176">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="518fe-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="518fe-177">VSTest se spustí, když se všechny změny souboru v obou testovacích projektu.</span><span class="sxs-lookup"><span data-stu-id="518fe-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="518fe-178">`dotnet-watch`v GitHubu</span><span class="sxs-lookup"><span data-stu-id="518fe-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="518fe-179">`dotnet-watch`je součástí úložiště GitHub [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="518fe-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
