---
title: Vyvíjejte aplikace ASP.NET Core s využitím sledovací proces souborů
author: rick-anderson
description: Tento kurz ukazuje, jak nainstalovat a používat nástroje pro .NET Core CLI souborů sledovacích procesů (dotnet watch) v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: 40ecca1c6f9d519b24649d0c28946d95b820c07c
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068193"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Vyvíjejte aplikace ASP.NET Core s využitím sledovací proces souborů

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` je nástroj, který běží [rozhraní příkazového řádku .NET Core](/dotnet/core/tools) příkaz při zdrojové soubory změnit. Například změna souboru můžete aktivovat sestavování, spouštění testů nebo nasazení.

Tento kurz používá existující webové rozhraní API s dva koncové body: jeden, který vrací součet a ten, který vrátí produktu. Metoda produkt obsahuje chybu, která je stanovena v tomto kurzu.

Stáhněte si [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Skládá se ze dvou projektů: *WebApp* (ASP.NET Core webového rozhraní API) a *WebAppTests* (testů jednotek pro webové rozhraní API).

V příkazovém řádku přejděte *WebApp* složky. Spusťte následující příkaz:

```console
dotnet run
```

> [!NOTE]
> Můžete použít `dotnet run --project <PROJECT>` k určení projektu pro spuštění. Například systém `dotnet run --project WebApp` z kořenového adresáře vzorku aplikace spustí se taky *WebApp* projektu.

Výstup konzoly zobrazí podobná následující zprávy (což znamená, že je aplikace spuštěná a čeká na požadavky):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Ve webovém prohlížeči přejděte na `http://localhost:<port number>/api/math/sum?a=4&b=5`. Zobrazí se výsledek `9`.

Přejděte do produktu API (`http://localhost:<port number>/api/math/product?a=4&b=5`). Vrátí `9`, nikoli `20` dle očekávání. Tento problém je vyřešen v pozdější části kurzu.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Přidat `dotnet watch` do projektu

`dotnet watch` Nástroj sledovací proces souborů je součástí 2.1.300 verzi .NET Core SDK. Následující kroky jsou povinné, jestli používáte starší verzi .NET Core SDK.

1. Přidat `Microsoft.DotNet.Watcher.Tools` odkaz na balíček *.csproj* souboru:

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Nainstalujte `Microsoft.DotNet.Watcher.Tools` balíčku spuštěním následujícího příkazu:

    ```console
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Spuštění pomocí příkazů rozhraní příkazového řádku .NET Core `dotnet watch`

Žádné [rozhraní příkazového řádku .NET Core](/dotnet/core/tools#cli-commands) můžete spustit s `dotnet watch`. Příklad:

| Příkaz | Příkaz s hodinkami |
| ---- | ----- |
| Spusťte příkaz DotNet | Spustit sledování DotNet |
| DotNet spustit netcoreapp2.0 -f | sledování DotNet spustit netcoreapp2.0 -f |
| Spustit netcoreapp2.0 -f - DotNet-arg1 | sledování DotNet spustit netcoreapp2.0 -f –--arg1 |
| DotNet test | DotNet watch test |

Spustit `dotnet watch run` v *WebApp* složky. Určuje výstup konzoly `watch` byla spuštěna.

> [!NOTE]
> Můžete použít `dotnet watch --project <PROJECT>` zadat projekt, který chcete sledovat. Například systém `dotnet watch --project WebApp run` z kořenového adresáře ukázky aplikací také spustí a podívejte se *WebApp* projektu.

## <a name="make-changes-with-dotnet-watch"></a>Změny pomocí `dotnet watch`

Ujistěte se, že `dotnet watch` běží.

Oprava chyby v `Product` metoda *MathController.cs* tak, aby vracel produktu a nikoli součet:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Uložte soubor. Výstup konzoly znamená, že `dotnet watch` byla zjištěna změna souboru a restartovat aplikaci.

Ověřte `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.

## <a name="run-tests-using-dotnet-watch"></a>Spustit testy pomocí `dotnet watch`

1. Změnit `Product` metoda *MathController.cs* zpět do vrací součet. Uložte soubor.
1. V příkazovém řádku přejděte *WebAppTests* složky.
1. Spustit [dotnet restore](/dotnet/core/tools/dotnet-restore).
1. Spusťte `dotnet watch test`. Výstup udává, že se nezdařil test a že sledovací proces čeká na změny v souboru:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Oprava `Product` metoda kódu tak, aby vracel produktu. Uložte soubor.

`dotnet watch` zjistí změnu souboru a znovu spustí testy. Výstup konzoly označuje, testy proběhly úspěšně.

## <a name="customize-files-list-to-watch"></a>Upravit seznam souborů ke sledování

Ve výchozím nastavení `dotnet-watch` sleduje všechny soubory, které odpovídají následujícím vzorům glob:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Další položky lze přidat do seznamu sledovaných úpravou *.csproj* souboru. Položky lze jednotlivě nebo s použitím glob vzory.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Odhlásit souborů, které mají být sledovány.

`dotnet-watch` je možné nakonfigurovat ignorovat výchozí nastavení. Chcete-li ignorovat konkrétní soubory, přidejte `Watch="false"` atributu na definici položky v *.csproj* souboru:

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

## <a name="custom-watch-projects"></a>Vlastní sledování projektů

`dotnet-watch` není omezena na projekty jazyka C#. Vlastní sledování projekty mohou být vytvořeny pro zpracování různých scénářů. Vezměte v úvahu následující rozložení projektu:

* **test/**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

Pokud je cílem jak oba projekty, vytvořte vlastní projekt soubor nakonfigurovaný tak, aby podívejte se na oba projekty:

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

Spusťte soubor sledování na obou projektů změňte na *testování* složky. Spusťte následující příkaz:

```console
dotnet watch msbuild /t:Test
```

VSTest provede při změn v souboru v obou testovacího projektu.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch` v Githubu

`dotnet-watch` je součástí Githubu [aspnet/AspNetCore úložiště](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).
