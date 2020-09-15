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
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [vítěz Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` je nástroj, který spouští příkaz [.NET Core CLI](/dotnet/core/tools) , když se změní zdrojové soubory. Například změna souboru může aktivovat kompilaci, spuštění testu nebo nasazení.

V tomto kurzu se používá existující webové rozhraní API se dvěma koncovými body: jeden, který vrací součet, a jeden, který vrací produkt. Metoda produktu obsahuje chybu, která je v tomto kurzu opravena.

Stáhněte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Skládá se ze dvou projektů: *WebApp* (ASP.NET Core Web API) a *WebAppTests* (testování částí webového rozhraní API).

V příkazovém prostředí přejděte do složky *WebApp* . Spusťte následující příkaz:

```dotnetcli
dotnet run
```

> [!NOTE]
> Můžete použít `dotnet run --project <PROJECT>` k určení projektu, který se má spustit. Například spuštění `dotnet run --project WebApp` z kořenového adresáře ukázkové aplikace spustí také projekt *WebApp* .

Výstup konzoly zobrazuje zprávy podobné následujícímu (což značí, že aplikace běží a čekají na žádosti):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Ve webovém prohlížeči přejděte na adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`. Měl by se zobrazit výsledek `9` .

Přejděte na rozhraní API produktu ( `http://localhost:<port number>/api/math/product?a=4&b=5` ). Vrátí `9` , ne `20` jako byste očekávali. Tento problém je opravený později v tomto kurzu.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Přidat `dotnet watch` do projektu

`dotnet watch`Nástroj sledovacího procesu souborů je součástí 2.1.300 verze .NET Core SDK. Při použití starší verze .NET Core SDK jsou vyžadovány následující kroky.

1. Přidejte `Microsoft.DotNet.Watcher.Tools` odkaz na balíček do souboru *. csproj* :

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Nainstalujte `Microsoft.DotNet.Watcher.Tools` balíček spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Spouštění příkazů .NET Core CLI pomocí `dotnet watch`

Libovolný [.NET Core CLI příkaz](/dotnet/core/tools#cli-commands) lze spustit pomocí příkazu `dotnet watch` . Příklad:

| Příkaz | Příkaz se sledováním |
| ---- | ----- |
| dotnet run | dotnet – běh kukátka |
| spuštění dotnet – f netcoreapp 3.1 | dotnet – běh kukátka – f netcoreapp 3.1 |
| příkaz dotnet Run-f netcoreapp 3.1----arg1 | dotnet Watch Run-f netcoreapp 3.1----arg1 |
| dotnet test | test výrazu dotnet |

Spusťte `dotnet watch run` ve složce *WebApp* . Výstup konzoly indikuje, že `watch` byl spuštěn.

::: moniker range=">= aspnetcore-5.0"
`dotnet watch run`Po spuštění na webové aplikaci se spustí prohlížeč, který po dokončení přejde na adresu URL aplikace. `dotnet watch` Přečtěte si výstup z konzoly aplikace a počkejte, než se zobrazí zpráva připravena <xref:Microsoft.AspNetCore.WebHost> .

`dotnet watch` aktualizuje prohlížeč, když detekuje změny sledovaných souborů. Pokud to chcete provést, příkaz Watch vloží do aplikace middleware, který upraví odpovědi HTML vytvořené aplikací. Middleware přidá do stránky blok skriptu JavaScriptu, který umožňuje `dotnet watch` dát pokyn prohlížeči k aktualizaci. V současné době změny všech sledovaných souborů, včetně statického obsahu, jako jsou soubory *. html* a *. CSS* , způsobí, že se aplikace znovu sestaví.

`dotnet watch`:

* Sleduje pouze soubory, které mají vliv na sestavení ve výchozím nastavení.
* U všech sledovaných souborů (prostřednictvím konfigurace) stále dochází k sestavení.

Další informace o konfiguraci najdete v tématu [dotnet – sledování konfigurace](#dotnet-watch-configuration) v tomto dokumentu.

::: moniker-end

> [!NOTE]
> Můžete použít `dotnet watch --project <PROJECT>` k určení projektu, který se má sledovat. Například spuštění `dotnet watch --project WebApp run` z kořenového adresáře ukázkové aplikace bude také spuštěno a sledovat projekt *WebApp* .

## <a name="make-changes-with-dotnet-watch"></a>Provést změny pomocí `dotnet watch`

Ujistěte `dotnet watch` se, že je spuštěný.

Opravte chybu v `Product` metodě *MathController.cs* tak, aby vrátila produkt, a ne součet:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Soubor uložte. Výstup konzoly indikuje, že `dotnet watch` zjistil změnu souboru a restartuje aplikaci.

Ověřování `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.

## <a name="run-tests-using-dotnet-watch"></a>Spustit testy pomocí `dotnet watch`

1. Změňte `Product` metodu *MathController.cs* zpět na vrácení součtu. Soubor uložte.
1. V příkazovém prostředí přejděte do složky *WebAppTests* .
1. Spusťte [dotnet Restore](/dotnet/core/tools/dotnet-restore).
1. Spusťte `dotnet watch test`. Jeho výstup označuje, že se test nezdařil a že sledovací proces čeká na změny souborů:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Opravte `Product` kód metody tak, aby vrátil produkt. Soubor uložte.

`dotnet watch` zjistí změnu souboru a znovu spustí testy. Výstup konzoly indikuje úspěšné testy.

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

`dotnet-watch` dá se nakonfigurovat tak, aby ignoroval výchozí nastavení. Chcete-li ignorovat konkrétní soubory, přidejte `Watch="false"` atribut do definice položky v souboru *. csproj* :

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

`dotnet-watch` není omezen na projekty C#. Je možné vytvořit vlastní projekty kukátka pro zpracování různých scénářů. Vezměte v úvahu následující rozložení projektu:

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

## <a name="dotnet-watch-configuration"></a>dotnet – sledování konfigurace

Některé možnosti konfigurace lze předat do `dotnet watch` proměnných prostředí. Dostupné proměnné jsou:

| Nastavení  | Popis |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | Pokud je nastavená na "1" nebo "true", `dotnet watch` používá sledovací proces pro cyklické soubory místo CoreFx `FileSystemWatcher` . Používá se při sledování souborů v síťových sdílených složkách nebo připojených svazcích Docker.                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | Ve výchozím nastavení `dotnet watch` optimalizuje sestavení tím, že vyloučí určité operace, jako je spuštění obnovení nebo opětovné vyhodnocení sady sledovaných souborů při každé změně souboru. Je-li nastaveno na hodnotu "1" nebo "true", jsou tyto optimalizace zakázány. |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | `dotnet watch run` pokusí se spustit prohlížeče pro webové aplikace, které jsou `launchBrowser` nakonfigurované v *launchSettings.js*. Pokud je nastaveno na hodnotu "1" nebo "true", toto chování je potlačeno. |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | `dotnet watch run` pokusí se aktualizovat prohlížeče, když detekuje změny souborů. Pokud je nastaveno na hodnotu "1" nebo "true", toto chování je potlačeno. Toto chování je také potlačeno `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` , pokud je nastaveno. |

## <a name="dotnet-watch-in-github"></a>`dotnet-watch` v GitHubu

`dotnet-watch` je součástí [úložiště dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)v GitHubu.
