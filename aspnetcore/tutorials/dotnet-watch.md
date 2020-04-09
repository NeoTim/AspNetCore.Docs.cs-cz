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
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Vývoj aplikací ASP.NET Core pomocí sledovacího procesu souborů

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

[Dotnet watch](https://www.nuget.org/packages/dotnet-watch) je nástroj, který při změně zdrojových souborů spouští příkaz [.NET Core CLI.](/dotnet/core/tools) Změna souboru může například aktivovat kompilaci, spuštění testu nebo nasazení.

Tento kurz používá existující webové rozhraní API se dvěma koncovými body: jeden, který vrací součet a jeden, který vrací produkt. Metoda produktu má chybu, která je opravena v tomto kurzu.

Stáhněte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Skládá se ze dvou projektů: *WebApp* (ASP.NET základní webové rozhraní API) a *WebAppTests* (testování částí pro webové rozhraní API).

V příkazovém prostředí přejděte do složky *WebApp.* Spusťte následující příkaz:

```dotnetcli
dotnet run
```

> [!NOTE]
> Můžete použít `dotnet run --project <PROJECT>` k určení projektu ke spuštění. Například spuštění `dotnet run --project WebApp` z kořenového adresáře ukázkové aplikace také spustí projekt *WebApp.*

Výstup konzoly zobrazuje zprávy podobné následujícímu (označující, že aplikace je spuštěná a čeká na požadavky):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Ve webovém prohlížeči přejděte na adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`. Měli byste vidět `9`výsledek .

Přejděte do rozhraní`http://localhost:<port number>/api/math/product?a=4&b=5`API produktu ( ). Vrací `9`se `20` , ne tak, jak byste očekávali. Tento problém je vyřešen později v tutoriálu.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Přidat `dotnet watch` do projektu

Nástroj `dotnet watch` pro sledování souborů je součástí verze 2.1.300 sady .NET Core SDK. Při použití starší verze sady .NET Core SDK jsou vyžadovány následující kroky.

1. Přidejte `Microsoft.DotNet.Watcher.Tools` odkaz na balíček do souboru *.csproj:*

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

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Spuštění příkazů rozhraní PŘÍKAZOVÉHO PŘÍKAZU .NET Core CLI pomocí`dotnet watch`

Libovolný [příkaz CLI jádra .NET](/dotnet/core/tools#cli-commands) lze spustit pomocí příkazu `dotnet watch`. Příklad:

| Příkaz | Příkaz s hodinkami |
| ---- | ----- |
| dotnet run | dotnet sledovat spustit |
| dotnet běh -f netcoreapp2.0 | dotnet hodinky spustit -f netcoreapp2.0 |
| dotnet běh -f netcoreapp2.0 -- --arg1 | dotnet hodinky spustit -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet hodinky test |

Spustit `dotnet watch run` ve složce *WebApp.* Výstup konzoly `watch` indikuje, že byl spuštěn.

> [!NOTE]
> Můžete použít `dotnet watch --project <PROJECT>` k určení projektu ke sledování. Například spuštění `dotnet watch --project WebApp run` z kořenového adresáře ukázkové aplikace se také spustí a bude sledovat projekt *WebApp.*

## <a name="make-changes-with-dotnet-watch"></a>Provádění změn pomocí`dotnet watch`

Ujistěte `dotnet watch` se, že běží.

Opravte chybu `Product` v metodě *MathController.cs* tak, aby se vrátil produkt a ne součet:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Uložte soubor. Výstup konzoly `dotnet watch` indikuje, že zjistil změnu souboru a restartoval aplikaci.

Ověřit `http://localhost:<port number>/api/math/product?a=4&b=5` vrátí správný výsledek.

## <a name="run-tests-using-dotnet-watch"></a>Spuštění testů pomocí`dotnet watch`

1. Změňte `Product` metodu *MathController.cs* zpět na vrácení součtu. Uložte soubor.
1. V příkazovém prostředí přejděte do složky *WebAppTests.*
1. Spusťte [dotnet restore](/dotnet/core/tools/dotnet-restore).
1. Spusťte `dotnet watch test`. Jeho výstup označuje, že test se nezdařil a že sledovací proces čeká na změny souboru:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Opravte `Product` kód metody tak, aby vrátil produkt. Uložte soubor.

`dotnet watch`detekuje změnu souboru a znovu spustí testy. Výstup konzoly označuje, že testy byly předány.

## <a name="customize-files-list-to-watch"></a>Přizpůsobení seznamu souborů ke sledování

Ve výchozím `dotnet-watch` nastavení sleduje všechny soubory odpovídající následujícím vzorům globu:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Další položky lze přidat do seznamu sledovaných položek úpravou souboru *.csproj.* Položky lze zadat jednotlivě nebo pomocí glob vzory.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Odhlášení ze sledovaných souborů

`dotnet-watch`lze nakonfigurovat tak, aby ignorovala jeho výchozí nastavení. Chcete-li ignorovat určité `Watch="false"` soubory, přidejte atribut do definice položky v souboru *CSProJ:*

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

## <a name="custom-watch-projects"></a>Vlastní projekty sledování

`dotnet-watch`není omezena na projekty Jazyka C#. Vlastní sledovat projekty lze vytvořit pro zpracování různých scénářů. Zvažte následující rozložení projektu:

* **test/**
  * *UnitTests/UnitTests.csproj*
  * *IntegraceTesty/IntegraceTests.csproj*

Pokud je cílem sledovat oba projekty, vytvořte vlastní soubor projektu nakonfigurovaný tak, aby sledoval oba projekty:

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

Chcete-li spustit sledování souborů v obou projektech, změňte na *testovací* složku. Spusťte následující příkaz:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest se spustí, když se všechny změny souboru v obou testovacích projektu.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch`v GitHubu

`dotnet-watch`je součástí úložiště GitHub [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).
