---
title: Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů
author: rick-anderson
description: V tomto kurzu se dozvíte, jak nainstalovat a používat nástroj sledovacího procesu souborů (dotnet Watch) .NET Core CLI v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: 5462f89a3b5a257ed0a6a8439efb077653fb14f6
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082251"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [vítěz Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch`je nástroj, který spouští příkaz [.NET Core CLI](/dotnet/core/tools) , když se změní zdrojové soubory. Například změna souboru může aktivovat kompilaci, spuštění testu nebo nasazení.

V tomto kurzu se používá existující webové rozhraní API se dvěma koncovými body: jeden, který vrací součet, a jeden, který vrací produkt. Metoda produktu obsahuje chybu, která je v tomto kurzu opravena.

Stáhněte si [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Skládá se ze dvou projektů: *WebApp* (ASP.NET Core webové rozhraní API) a *WebAppTests* (testování částí webového rozhraní API).

V příkazovém prostředí přejděte do složky *WebApp* . Spusťte následující příkaz:

```dotnetcli
dotnet run
```

> [!NOTE]
> Můžete použít `dotnet run --project <PROJECT>` k určení projektu, který se má spustit. Například `dotnet run --project WebApp` spuštění z kořenového adresáře ukázkové aplikace spustí také projekt *WebApp* .

Výstup konzoly zobrazuje zprávy podobné následujícímu (což značí, že aplikace běží a čekají na žádosti):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Ve webovém prohlížeči přejděte na `http://localhost:<port number>/api/math/sum?a=4&b=5`. Měl by se zobrazit výsledek `9`.

Přejděte na rozhraní API produktu (`http://localhost:<port number>/api/math/product?a=4&b=5`). Vrátí `9`, ne `20` jako byste očekávali. Tento problém je opravený později v tomto kurzu.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Přidat `dotnet watch` do projektu

Nástroj sledovacího procesu souborů je součástí 2.1.300 verze .NET Core SDK. `dotnet watch` Při použití starší verze .NET Core SDK jsou vyžadovány následující kroky.

1. Přidejte odkaz na balíčekdosouboru.`Microsoft.DotNet.Watcher.Tools` csproj:

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. `Microsoft.DotNet.Watcher.Tools` Nainstalujte balíček spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Spouštění příkazů .NET Core CLI pomocí`dotnet watch`

Libovolný [.NET Core CLI příkaz](/dotnet/core/tools#cli-commands) lze spustit pomocí `dotnet watch`příkazu. Příklad:

| Příkaz | Příkaz se sledováním |
| ---- | ----- |
| dotnet run | dotnet – běh kukátka |
| příkaz dotnet Run-f netcoreapp 2.0 | dotnet Watch Run-f netcoreapp 2.0 |
| dotnet Run-f netcoreapp 2.0----arg1 | dotnet Watch Run-f netcoreapp 2.0----arg1 |
| dotnet test | test výrazu dotnet |

Spusťte `dotnet watch run` ve složce *WebApp* . Výstup konzoly indikuje `watch` , že byl spuštěn.

> [!NOTE]
> Můžete použít `dotnet watch --project <PROJECT>` k určení projektu, který se má sledovat. Například `dotnet watch --project WebApp run` spuštění z kořenového adresáře ukázkové aplikace bude také spuštěno a sledovat projekt *WebApp* .

## <a name="make-changes-with-dotnet-watch"></a>Provést změny pomocí`dotnet watch`

Ujistěte se `dotnet watch` , že je spuštěný.

Opravte chybu v `Product` metodě *MathController.cs* tak, aby vrátila produkt, a ne součet:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Uložte soubor. Výstup konzoly indikuje, že `dotnet watch` zjistil změnu souboru a restartuje aplikaci.

Ověřování `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.

## <a name="run-tests-using-dotnet-watch"></a>Spustit testy pomocí`dotnet watch`

1. Změňte metodu MathController.cs zpět na vrácení součtu. `Product` Uložte soubor.
1. V příkazovém prostředí přejděte do složky *WebAppTests* .
1. Spusťte [dotnet Restore](/dotnet/core/tools/dotnet-restore).
1. Spusťte `dotnet watch test`. Jeho výstup označuje, že se test nezdařil a že sledovací proces čeká na změny souborů:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Opravte `Product` kód metody tak, aby vrátil produkt. Uložte soubor.

`dotnet watch`zjistí změnu souboru a znovu spustí testy. Výstup konzoly indikuje úspěšné testy.

## <a name="customize-files-list-to-watch"></a>Přizpůsobení seznamu souborů pro sledování

Ve výchozím nastavení `dotnet-watch` sleduje všechny soubory, které odpovídají následujícím vzorům glob:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Další položky lze přidat do seznamu sledování úpravou souboru *. csproj* . Položky lze zadat jednotlivě nebo pomocí glob vzorů.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Výslovný souhlas se soubory, které se mají sledovat

`dotnet-watch`dá se nakonfigurovat tak, aby ignoroval výchozí nastavení. Chcete-li ignorovat konkrétní soubory, `Watch="false"` přidejte atribut do definice položky v souboru *. csproj* :

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

## <a name="custom-watch-projects"></a>Vlastní projekty kukátka

`dotnet-watch`není omezen na C# projekty. Je možné vytvořit vlastní projekty kukátka pro zpracování různých scénářů. Vezměte v úvahu následující rozložení projektu:

* **napaden**
  * *UnitTests/UnitTests. csproj*
  * *IntegrationTests/IntegrationTests. csproj*

Pokud je cílem sledovat oba projekty, vytvořte vlastní soubor projektu nakonfigurovaný pro sledování obou projektů:

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

Chcete-li spustit sledování souborů na obou projektech, přejděte do složky *test* . Spusťte následující příkaz:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest se spustí při každé změně souboru v jednom testovacím projektu.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch`v GitHubu

`dotnet-watch`je součástí [úložiště GitHub ASPNET/AspNetCore](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).
