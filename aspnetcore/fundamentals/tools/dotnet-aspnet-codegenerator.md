---
title: příkaz DotNet aspnet codegenerator
author: rick-anderson
description: Příkaz dotnet aspnet codegenerator vygeneruje uživatelské rozhraní projekty ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: c96362f320efd84c35dc07294a2968a2c687ee94
ms.sourcegitcommit: b9e914ef274b5ec359582f299724af6234dce135
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67596143"
---
# <a name="dotnet-aspnet-codegenerator"></a>DotNet aspnet-codegenerator

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator` -Spustí modul ASP.NET Core generování uživatelského rozhraní. `dotnet aspnet-codegenerator` je jenom pro povinné pro generování uživatelského rozhraní z příkazového řádku, není potřeba používat generování uživatelského rozhraní pomocí sady Visual Studio.

Tento článek se týká [sady SDK .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) a novější.

## <a name="installing-aspnet-codegenerator"></a>Instalace aspnet codegenerator

`dotnet-aspnet-codegenerator` je [globální nástroj](/dotnet/core/tools/global-tools) , který musí být nainstalována. Následující příkaz nainstaluje nejnovější stabilní verze `dotnet-aspnet-codegenerator` nástroje:

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

Zadáním následujícího příkazu aktualizace `dotnet-aspnet-codegenerator` nejnovější stabilní verzi k dispozici z nainstalovaných sad .NET Core SDK:

```console
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Souhrn

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Popis

`dotnet aspnet-codegenerator` Globální příkaz spustí ASP.NET Core, generátor kódu a modulu generování uživatelského rozhraní.

## <a name="arguments"></a>Arguments

`generator`

Generátor kódu pro spuštění. K dispozici jsou následující zdroje:

| Generátor | Operace |
| ----------------- | ------------ | 
| Oblast      | [Nástroj scaffold oblasti](/aspnet/core/mvc/controllers/areas) |
  kontroler| [Vygeneruje uživatelské rozhraní kontroleru](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identita  | [Nástroj scaffold Identity](/aspnet/core/security/authentication/scaffold-identity) |
  razorpage | [Nástroj scaffold Razor Pages](/aspnet/core/tutorials/razor-pages/model) |
  Zobrazení      | [Vygeneruje uživatelské rozhraní zobrazení](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>Možnosti

`-n|--nuget-package-dir`

Určuje adresář balíčku NuGet.

`-c|--configuration {Debug|Release}`

Definuje konfiguraci sestavení. Výchozí hodnota je `Debug`.

`-tfm|--target-framework`

Cíl [Framework](/dotnet/standard/frameworks) používat. Například, `net46`.

`-b|--build-base-path`

Základní cesta sestavení.

`-h|--help`

Vytiskne krátký nápovědy pro příkaz.

`--no-build`

Nelze sestavit projekt před provozováním. Také implicitně nastaví `--no-restore` příznak.

`-p|--project <PATH>`

Určuje cestu k souboru projektu pro spuštění (název složky nebo úplná cesta). Pokud není zadán, použije se výchozí do aktuálního adresáře.

## <a name="generator-options"></a>Generátor možnosti

Následující části popisují možnosti dostupné pro podporované generátory:

* Oblast
* Kontroler
* Identita  
* razorpage
* Zobrazit

<a name="area"></a>

### <a name="area-options"></a>Možnosti oblasti

Tento nástroj je určen pro webové projekty ASP.NET Core s kontrolerů a zobrazení. Není určena pro aplikace stránky Razor.

Použití: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Ve výstupu předchozího příkazu generuje následující složky:

* *Oblasti*
  * *AreaNameToGenerate*
    * *Kontrolery*
    * *Data*
    * *Modely*
    * *Zobrazení*

<a name="ctl"></a>

### <a name="controller-options"></a>Možnosti řadiče

V následující tabulce jsou uvedeny možnosti `aspnet-codegenerator` `controller` a `razorpage`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

V následující tabulce jsou uvedeny možnosti, které jsou jedinečné pro `aspnet-codegenerator controller`:

| Možnost               | Popis|
| ----------------- | ------------ |
| Parametr controllerName – nebo – název | Název kontroleru. |
| --useAsyncActions nebo - asynchronní | Generovat asynchronní akce kontroleru. |
| --noViews nebo -nv | Generovat **žádné** zobrazení. |
| --restWithNoViews nebo – rozhraní api  | Generovat Kontroleru stylem REST API. `noViews` předpokládá se a zobrazit všechny související možnosti jsou ignorovány. |
| --readWriteActions nebo - akce | Generovat kontroler s akcemi čtení/zápisu bez modelu. |

Použití `-h` zapnout o pomoc `aspnet-codegenerator controller` příkaz:

```console
dotnet aspnet-codegenerator controller -h
```

Zobrazit [generování uživatelského rozhraní modelu film](/aspnet/core/tutorials/razor-pages/model) příklad `dotnet aspnet-codegenerator controller`.

### <a name="razorpage"></a>razorpage

<a name="rp"></a>

Stránky Razor můžete jednotlivě automaticky tak, že zadáte název nové stránky a šablonu, kterou chcete použít. Jsou podporované šablony:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Například následující příkaz Upravit šablony používá ke generování *MyEdit.cshtml* a *MyEdit.cshtml.cs*:

```console
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Obvykle, šablony a není zadán název generovaného souboru a jsou vytvořeny následující šablony:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

V následující tabulce jsou uvedeny možnosti `aspnet-codegenerator` `razorpage` a `controller`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

V následující tabulce jsou uvedeny možnosti, které jsou jedinečné pro `aspnet-codegenerator razorpage`:

| Možnost               | Popis|
| ----------------- | ------------ |
|   --namespaceName nebo – obor názvů | Název oboru názvů pro generovaný PageModel |
| --partialView nebo - partial | Generovat částečné zobrazení. Pokud je tento parametr zadán, jsou ignorovány rozložení možnosti -l a - udl. |
| --noPageModel nebo – npm | Přepínač nelze vygenerovat třídu PageModel pro prázdnou šablonu |

Použití `-h` zapnout o pomoc `aspnet-codegenerator razorpage` příkaz:

```console
dotnet aspnet-codegenerator razorpage -h
```

Zobrazit [generování uživatelského rozhraní modelu film](/aspnet/core/tutorials/razor-pages/model) příklad `dotnet aspnet-codegenerator razorpage`.

### <a name="identity"></a>Identita

Zobrazit [generování uživatelského rozhraní Identity](/aspnet/core/security/authentication/scaffold-identity)
