---
title: dotnet ASPNET-CodeGenerator – příkaz
author: rick-anderson
description: Rozhraní dotnet ASPNET-CodeGenerator příkazy ASP.NET Core projekty.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: af151b65d3fefb5863529de1cd97c6315f11ca82
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444124"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet ASPNET – CodeGenerator

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`– Spustí modul generování uživatelského rozhraní ASP.NET Core. `dotnet aspnet-codegenerator`je vyžadován pouze pro generování uživatelského rozhraní z příkazového řádku, není nutné používat generování uživatelského rozhraní se sadou Visual Studio.

Tento článek se týká [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) a novější.

## <a name="installing-aspnet-codegenerator"></a>Instalace ASPNET-CodeGenerator

`dotnet-aspnet-codegenerator`je [globální nástroj](/dotnet/core/tools/global-tools) , který musí být nainstalován. Následující příkaz nainstaluje nejnovější stabilní verzi `dotnet-aspnet-codegenerator` nástroje:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Následující příkaz aktualizuje `dotnet-aspnet-codegenerator` na nejnovější stabilní verzi dostupnou z nainstalovaných sad SDK .NET Core:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Stručný obsah

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Popis

`dotnet aspnet-codegenerator`Globální příkaz spustí generátor kódu ASP.NET Core a modul generování uživatelského rozhraní.

## <a name="arguments"></a>Arguments

`generator`

Generátor kódu, který se má spustit. K dispozici jsou následující generátory:

| Generátor  | Operace                                                            |
| ---------- | -------------------------------------------------------------------- |
| oblast       | [Generování uživatelského rozhraní oblasti](xref:mvc/controllers/areas)                      |
| kontroler | [Generování uživatelského rozhraní kontroleru](xref:tutorials/first-mvc-app/adding-model)  |
| identity   | [SCAFFOLDIdentity](xref:security/authentication/scaffold-identity) |
| razorpage  | [Stránky generování uživatelského rozhraní Razor](xref:tutorials/razor-pages/model)            |
| zobrazení       | [Generování uživatelského rozhraní zobrazení](xref:mvc/views/overview)                          |

## <a name="options"></a>Možnosti

`-n|--nuget-package-dir`

Určuje adresář balíčku NuGet.

`-c|--configuration {Debug|Release}`

Definuje konfiguraci sestavení. Výchozí hodnota je `Debug`.

`-tfm|--target-framework`

Cílová [Architektura](/dotnet/standard/frameworks) , která se má použít. Například, `net46`.

`-b|--build-base-path`

Základní cesta sestavení

`-h|--help`

Vypíše krátkou nápovědu k příkazu.

`--no-build`

Před spuštěním nevytvoří projekt. Také implicitně nastaví `--no-restore` příznak.

`-p|--project <PATH>`

Určuje cestu k souboru projektu, který se má spustit (název složky nebo úplná cesta). Pokud není zadaný, použije se ve výchozím nastavení aktuální adresář.

## <a name="generator-options"></a>Možnosti generátoru

Následující části obsahují podrobné informace o možnostech, které jsou k dispozici pro podporovaná generátory:

* Oblast
* Kontrolér
* Identity  
* RazorPage
* Zobrazit

<a name="area"></a>

### <a name="area-options"></a>Možnosti oblasti

Tento nástroj je určený pro ASP.NET Core webové projekty s řadiči a zobrazeními. Není určena pro Razor aplikace stránky.

Použití: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Předchozí příkaz vygeneruje následující složky:

* *Oblasti*
  * *AreaNameToGenerate*
    * *Kontrolery*
    * *Data*
    * *Modely*
    * *Zobrazení*

<a name="ctl"></a>

### <a name="controller-options"></a>Možnosti kontroleru

V následující tabulce jsou uvedeny možnosti pro `aspnet-codegenerator` `controller` a `razorpage` :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Následující tabulka uvádí možnosti, které jsou jedinečné pro `aspnet-codegenerator controller` :

| Možnost                         | Popis                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| --Controller nebo-Name      | Název kontroleru                                                                                   |
| --useAsyncActions nebo-Async    | Vygenerujte akce asynchronního kontroleru.                                                                        |
| --nezobrazení nebo-NV               | Negenerovat **žádná** zobrazení                                                                                    |
| --restWithNoViews nebo-API      | Vygenerujte kontrolér s rozhraním API stylu REST. `noViews`je předpokládaná a všechny možnosti zobrazení jsou ignorovány. |
| --readWriteActions nebo-Actions | Vygeneruje kontroler s akcemi čtení/zápisu bez modelu.                                              |

`-h`Pro nápovědu k příkazu použijte přepínač `aspnet-codegenerator controller` :

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Příklad naleznete v tématu [generování modelu filmu](xref:tutorials/first-mvc-app/adding-model) `dotnet aspnet-codegenerator controller` .

### <a name="no-locrazorpage"></a>RazorPage

<a name="rp"></a>

RazorStránky mohou být jednotlivě vygenerované pomocí uživatelského rozhraní zadáním názvu nové stránky a šablony, která se má použít. Podporované šablony:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Například následující příkaz používá šablonu Edit pro generování *MyEdit. cshtml* a *MyEdit.cshtml.cs*:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Obvykle není zadána šablona a vygenerovaný název souboru a jsou vytvořeny následující šablony:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

V následující tabulce jsou uvedeny možnosti pro `aspnet-codegenerator` `razorpage` a `controller` :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Následující tabulka uvádí možnosti, které jsou jedinečné pro `aspnet-codegenerator razorpage` :

| Možnost                        | Popis                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| --Namespace nebo-Namespace | Název oboru názvů, který se má použít pro vygenerovaný PageModel                          |
| --partialView nebo-Partial     | Vygeneruje částečné zobrazení. Možnosti rozložení – l a-UDL se při zadání tohoto nastavení ignorují. |
| --noPageModel nebo-npm         | Přepněte na negenerovat třídu PageModel pro prázdnou šablonu.                           |

`-h`Pro nápovědu k příkazu použijte přepínač `aspnet-codegenerator razorpage` :

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Příklad naleznete v tématu [generování modelu filmu](xref:tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage` .

### Identity

Viz [generování Identity uživatelského rozhraní](xref:security/authentication/scaffold-identity)
