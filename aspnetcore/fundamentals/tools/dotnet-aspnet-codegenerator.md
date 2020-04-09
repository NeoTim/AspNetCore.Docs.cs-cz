---
title: dotnet aspnet-codegenerator, příkaz
author: rick-anderson
description: Dotnet aspnet-codegenerator příkaz oválašiny ASP.NET základní projekty.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665184"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`- Provozuje ASP.NET lešení Core motoru. `dotnet aspnet-codegenerator`je vyžadováno pouze pro zaslístvení z příkazového řádku, není nutné používat přisycování uživatelského přisporu.

Tento článek se vztahuje na [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) a novější.

## <a name="installing-aspnet-codegenerator"></a>Instalace generátoru kódů aspnet

`dotnet-aspnet-codegenerator`je [globální nástroj,](/dotnet/core/tools/global-tools) který musí být nainstalován. Následující příkaz nainstaluje nejnovější stabilní `dotnet-aspnet-codegenerator` verzi nástroje:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Následující příkaz `dotnet-aspnet-codegenerator` se aktualizuje na nejnovější stabilní verzi dostupnou z nainstalovaných sad SDK jádra .NET:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Synopse

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Popis

Globální `dotnet aspnet-codegenerator` příkaz spouští ASP.NET generátor kódu Core a modul lešení.

## <a name="arguments"></a>Argumenty

`generator`

Generátor kódu ke spuštění. K dispozici jsou následující generátory:

| Generátor | Operace |
| ----------------- | ------------ | 
| oblast      | [Lešení a oblast](/aspnet/core/mvc/controllers/areas) |
  kontroler| [Lešení regulátor](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identity  | [Identita lešení](/aspnet/core/security/authentication/scaffold-identity) |
  razorpage | [Lešení Žiletky Stránky](/aspnet/core/tutorials/razor-pages/model) |
  Prohlédni      | [Zobrazení ve lešení](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>Možnosti

`-n|--nuget-package-dir`

Určuje adresář balíčku NuGet.

`-c|--configuration {Debug|Release}`

Definuje konfiguraci sestavení. Výchozí hodnota je `Debug`.

`-tfm|--target-framework`

Cílové [rozhraní](/dotnet/standard/frameworks) použít. Například, `net46`.

`-b|--build-base-path`

Základní cesta sestavení.

`-h|--help`

Vytiskne krátkou nápovědu pro příkaz.

`--no-build`

Nesestavuje projekt před spuštěním. Také implicitně nastaví příznak. `--no-restore`

`-p|--project <PATH>`

Určuje cestu ke spuštění souboru projektu (název složky nebo úplná cesta). Pokud není zadán, je výchozí aktuální adresář.

## <a name="generator-options"></a>Možnosti generátoru

V následujících částech jsou podrobně popsány možnosti, které jsou k dispozici pro podporované generátory:

* Oblast
* Řadič
* Identita  
* Razorpage
* Zobrazit

<a name="area"></a>

### <a name="area-options"></a>Možnosti oblasti

Tento nástroj je určen pro ASP.NET základní webové projekty s řadiči a zobrazení. Není určen pro aplikace Razor Pages.

Použití: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Předchozí příkaz generuje následující složky:

* *Oblasti*
  * *Název_oblastiTogenerovat*
    * *Kontrolery*
    * *Data*
    * *Modely*
    * *Zobrazení*

<a name="ctl"></a>

### <a name="controller-options"></a>Možnosti řadiče

V následující tabulce `aspnet-codegenerator` `controller` jsou `razorpage`uvedeny možnosti pro a :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

V následující tabulce jsou `aspnet-codegenerator controller`uvedeny možnosti jedinečné :

| Možnost               | Popis|
| ----------------- | ------------ |
| --controllerName nebo -name | Název řadiče. |
| --useAsyncActions nebo -async | Generovat akce asynchronního řadiče. |
| --noViews nebo -nv | Generovat **žádná** zobrazení. |
| --restWithNoViews nebo -api  | Vygenerujte řadič s rozhraním API stylu REST. `noViews`a všechny možnosti související s zobrazením jsou ignorovány. |
| --readWriteActions nebo -actions | Generovat řadič s akcemi pro čtení a zápis bez modelu. |

Pomocí `-h` přepínače pro `aspnet-codegenerator controller` pomoc na příkaz:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Příklad obrázku najdete [v tématu Kreaffold filmového](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator controller`modelu .

### <a name="razorpage"></a>Razorpage

<a name="rp"></a>

Razor Pages lze jednotlivě šetřený kód zadáním názvu nové stránky a šablony, která má být používána. Podporované šablony jsou:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Například následující příkaz používá šablonu Edit ke generování *myedit.cshtml* a *MyEdit.cshtml.cs*:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Šablona a název generovaného souboru obvykle nejsou zadány a jsou vytvořeny následující šablony:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

V následující tabulce `aspnet-codegenerator` `razorpage` jsou `controller`uvedeny možnosti pro a :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

V následující tabulce jsou `aspnet-codegenerator razorpage`uvedeny možnosti jedinečné :

| Možnost               | Popis|
| ----------------- | ------------ |
|   --namespaceName nebo -namespace | Název oboru názvů, který se má použít pro generovaný model_ |
| --partialView nebo -partial | Vygenerujte částečné zobrazení. Možnosti rozložení -l a -udl jsou ignorovány, pokud je zadán. |
| --noPageModel nebo -npm | Přepnout na negenerování třídy PageModel pro prázdnou šablonu |

Pomocí `-h` přepínače pro `aspnet-codegenerator razorpage` pomoc na příkaz:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Příklad obrázku najdete [v tématu Kreaffold filmového](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage`modelu .

### <a name="identity"></a>Identita

Viz [Identita lešení](/aspnet/core/security/authentication/scaffold-identity)
