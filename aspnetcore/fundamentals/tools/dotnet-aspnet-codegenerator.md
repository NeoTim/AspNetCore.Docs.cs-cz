---
title: dotnet ASPNET-CodeGenerator – příkaz
author: rick-anderson
description: Rozhraní dotnet ASPNET-CodeGenerator příkazy ASP.NET Core projekty.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: c2c815735ad1b4dcec761b26ea3992a4effebe62
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68682693"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet ASPNET – CodeGenerator

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`– Spustí modul generování uživatelského rozhraní ASP.NET Core. `dotnet aspnet-codegenerator`je vyžadován pouze pro generování uživatelského rozhraní z příkazového řádku, není nutné používat generování uživatelského rozhraní se sadou Visual Studio.

Tento článek se týká [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) a novější.

## <a name="installing-aspnet-codegenerator"></a>Instalace ASPNET-CodeGenerator

`dotnet-aspnet-codegenerator`je [globální nástroj](/dotnet/core/tools/global-tools) , který musí být nainstalován. Následující příkaz nainstaluje nejnovější stabilní verzi `dotnet-aspnet-codegenerator` nástroje:

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

Následující příkaz aktualizuje `dotnet-aspnet-codegenerator` na nejnovější stabilní verzi dostupnou z nainstalovaných sad SDK .NET Core:

```console
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Stručný obsah

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Popis

`dotnet aspnet-codegenerator` Globální příkaz spustí generátor kódu ASP.NET Core a modul generování uživatelského rozhraní.

## <a name="arguments"></a>Arguments

`generator`

Generátor kódu, který se má spustit. K dispozici jsou následující generátory:

| Generátor | Operace |
| ----------------- | ------------ | 
| Oblast      | [Generování uživatelského rozhraní oblasti](/aspnet/core/mvc/controllers/areas) |
  kontroler| [Generování uživatelského rozhraní kontroleru](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identita  | [Identita uživatelského rozhraní](/aspnet/core/security/authentication/scaffold-identity) |
  razorpage | [Razor Pages generování uživatelského rozhraní](/aspnet/core/tutorials/razor-pages/model) |
  zobrazení      | [Generování uživatelského rozhraní zobrazení](/aspnet/core/mvc/views/overview) |

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
* Identita  
* Razorpage
* Zobrazit

<a name="area"></a>

### <a name="area-options"></a>Možnosti oblasti

Tento nástroj je určený pro ASP.NET Core webové projekty s řadiči a zobrazeními. Není určena pro aplikace Razor Pages.

Použití: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Předchozí příkaz vygeneruje následující složky:

* *Oblasti*
  * *AreaNameToGenerate*
    * *Kontrolery*
    * *Data*
    * *Vzor*
    * *Zobrazení*

<a name="ctl"></a>

### <a name="controller-options"></a>Možnosti kontroleru

V následující tabulce jsou uvedeny možnosti `aspnet-codegenerator` pro `razorpage` `controller` a:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Následující tabulka uvádí možnosti, které jsou `aspnet-codegenerator controller`jedinečné pro:

| Možnost               | Popis|
| ----------------- | ------------ |
| --Controller nebo-Name | Název kontroleru |
| --useAsyncActions nebo-Async | Vygenerujte akce asynchronního kontroleru. |
| --nezobrazení nebo-NV | Negenerovat **žádná** zobrazení |
| --restWithNoViews nebo-API  | Vygenerujte kontrolér s rozhraním API stylu REST. `noViews`je předpokládaná a všechny možnosti zobrazení jsou ignorovány. |
| --readWriteActions nebo-Actions | Vygeneruje kontroler s akcemi čtení/zápisu bez modelu. |

Pro nápovědu`aspnet-codegenerator controller` k příkazu použijte přepínač:`-h`

```console
dotnet aspnet-codegenerator controller -h
```

Příklad`dotnet aspnet-codegenerator controller`naleznete v tématu [generování modelu filmu](/aspnet/core/tutorials/razor-pages/model) .

### <a name="razorpage"></a>Razorpage

<a name="rp"></a>

Razor Pages může být individuálně vygenerovaného uživatelského rozhraní zadáním názvu nové stránky a šablony, která se má použít. Podporované šablony:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Například následující příkaz používá šablonu Edit pro generování *MyEdit. cshtml* a *MyEdit.cshtml.cs*:

```console
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Obvykle není zadána šablona a vygenerovaný název souboru a jsou vytvořeny následující šablony:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

V následující tabulce jsou uvedeny možnosti `aspnet-codegenerator` pro `controller` `razorpage` a:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Následující tabulka uvádí možnosti, které jsou `aspnet-codegenerator razorpage`jedinečné pro:

| Možnost               | Popis|
| ----------------- | ------------ |
|   --Namespace nebo-Namespace | Název oboru názvů, který se má použít pro vygenerovaný PageModel |
| --partialView nebo-Partial | Vygeneruje částečné zobrazení. Možnosti rozložení – l a-UDL se při zadání tohoto nastavení ignorují. |
| --noPageModel nebo-npm | Přepněte na negenerovat třídu PageModel pro prázdnou šablonu. |

Pro nápovědu`aspnet-codegenerator razorpage` k příkazu použijte přepínač:`-h`

```console
dotnet aspnet-codegenerator razorpage -h
```

Příklad`dotnet aspnet-codegenerator razorpage`naleznete v tématu [generování modelu filmu](/aspnet/core/tutorials/razor-pages/model) .

### <a name="identity"></a>Identita

Zobrazit [identitu uživatelského rozhraní](/aspnet/core/security/authentication/scaffold-identity)
