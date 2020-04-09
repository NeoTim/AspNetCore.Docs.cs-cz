---
title: Správa odkazů Protobuf pomocí dotnet-grpc
author: juntaoluo
description: Další informace o přidávání, aktualizaci, odebírání a výpisu odkazů Protobuf pomocí globálního nástroje dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667333"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Správa odkazů Protobuf pomocí dotnet-grpc

Podle [John Luo](https://github.com/juntaoluo)

`dotnet-grpc`je globální nástroj .NET Core pro správu odkazů [Protobuf (*.proto*)](xref:grpc/basics#proto-file) v rámci projektu .NET gRPC. Nástroj lze použít k přidání, aktualizaci, odebrání a seznamu odkazů Protobuf.

## <a name="installation"></a>Instalace

Chcete-li `dotnet-grpc` nainstalovat [globální nástroj .NET Core](/dotnet/core/tools/global-tools), spusťte následující příkaz:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Přidání odkazů

`dotnet-grpc`lze přidat odkazy Protobuf jako `<Protobuf />` položky do souboru *.csproj:*

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Odkazy Protobuf se používají ke generování prostředků klienta c# nebo serveru. Nástroj `dotnet-grpc` může:

* Vytvořte odkaz Protobuf z místních souborů na disku.
* Vytvořte odkaz Protobuf ze vzdáleného souboru určeného adresou URL.
* Ujistěte se, že správné gRPC balíček závislosti jsou přidány do projektu.

Balíček se `Grpc.AspNetCore` například přidá do webové aplikace. `Grpc.AspNetCore`obsahuje gRPC server a klientské knihovny a podporu nástrojů. Alternativně `Grpc.Net.Client`, `Grpc.Tools` a `Google.Protobuf` balíčky, které obsahují pouze gRPC klientské knihovny a podporu nástrojů, jsou přidány do aplikace Konzola.

### <a name="add-file"></a>Přidat soubor

Příkaz `add-file` se používá k přidání místních souborů na disk jako odkazy Protobuf. Cesty k souboru jsou k dispozici:

* Může být relativní vzhledem k aktuálnímu adresáři nebo absolutní cesty.
* Může obsahovat zástupné znaky pro [globbing](https://wikipedia.org/wiki/Glob_(programming))souborů založených na vzoru .

Pokud jsou některé soubory mimo `Link` adresář projektu, je přidán prvek `Protos` pro zobrazení souboru pod složkou v sadě Visual Studio.

### <a name="usage"></a>Využití

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Argumenty

| Argument | Popis |
|-|-|
| files | Protobuf soubor odkazy. Může to být cesta k globu pro místní protobufové soubory. |

#### <a name="options"></a>Možnosti

| Krátká volba | Dlouhá volba | Popis |
|-|-|-|
| -p | --projekt | Cesta k souboru projektu, na kterém chcete pracovat. Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.
| -s | --služby | Typ služeb gRPC, které by měly být generovány. Pokud `Default` je `Both` zadán, se používá `Client` pro webové projekty a používá se pro jiné než webové projekty. Přijaté hodnoty `Both` `Client`jsou `Default` `None`, `Server`, , , .
| -i | --additional-import-dirs | Další adresáře, které mají být použity při řešení importů pro soubory protobuf. Toto je středník oddělený seznam cest.
| | --přístup | Modifikátor přístupu pro generované třídy Jazyka C#. Výchozí hodnota je `Public`. Přijaté hodnoty `Internal` `Public`jsou a .

### <a name="add-url"></a>Přidat adresu URL

Příkaz `add-url` se používá k přidání vzdáleného souboru určeného zdrojovou adresou URL jako odkazu Protobuf. Musí být k dispozici cesta k souboru, která určuje, kam má být vzdálený soubor stažen. Cesta k souboru může být relativní vzhledem k aktuálnímu adresáři nebo absolutní cestě. Pokud je cesta k souboru `Link` mimo adresář projektu, přidá se `Protos` prvek, který zobrazí soubor pod virtuální složkou v sadě Visual Studio.

### <a name="usage"></a>Využití

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Argumenty

| Argument | Popis |
|-|-|
| url | Adresa URL vzdáleného souboru protobuf. |

#### <a name="options"></a>Možnosti

| Krátká volba | Dlouhá volba | Popis |
|-|-|-|
| -o | --output | Určuje cestu ke stažení pro vzdálený soubor protobuf. Tato možnost je povinná.
| -p | --projekt | Cesta k souboru projektu, na kterém chcete pracovat. Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.
| -s | --služby | Typ služeb gRPC, které by měly být generovány. Pokud `Default` je `Both` zadán, se používá `Client` pro webové projekty a používá se pro jiné než webové projekty. Přijaté hodnoty `Both` `Client`jsou `Default` `None`, `Server`, , , .
| -i | --additional-import-dirs | Další adresáře, které mají být použity při řešení importů pro soubory protobuf. Toto je středník oddělený seznam cest.
| | --přístup | Modifikátor přístupu pro generované třídy Jazyka C#. Výchozí hodnota `Public`je . Přijaté hodnoty `Internal` `Public`jsou a .

## <a name="remove"></a>Odebrat

Příkaz `remove` se používá k odebrání odkazů Protobuf ze souboru *.csproj.* Příkaz přijímá argumenty cesty a zdrojové adresy URL jako argumenty. Nástroj:

* Odebere pouze odkaz Protobuf.
* Neodstraní soubor *.proto,* i když byl původně stažen ze vzdálené adresy URL.

### <a name="usage"></a>Využití

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Argumenty

| Argument | Popis |
|-|-|
| odkazy | Adresy URL nebo cesty k souborům odkazů protobuf odebrat. |

### <a name="options"></a>Možnosti

| Krátká volba | Dlouhá volba | Popis |
|-|-|-|
| -p | --projekt | Cesta k souboru projektu, na kterém chcete pracovat. Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.

## <a name="refresh"></a>Obnovení

Příkaz `refresh` se používá k aktualizaci vzdáleného odkazu s nejnovějším obsahem ze zdrojové adresy URL. Cestu ke stažení souboru i zdrojovou adresu URL lze použít k určení odkazu, který má být aktualizován. Poznámka:

* Porovnané hodnoty hashe obsahu souboru, aby bylo možné určit, zda má být místní soubor aktualizován.
* Nejsou porovnány žádné informace o časovém razítku.

Nástroj vždy nahradí místní soubor vzdáleným souborem, pokud je nutná aktualizace.

### <a name="usage"></a>Využití

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Argumenty

| Argument | Popis |
|-|-|
| odkazy | Adresy URL nebo cesty k souborům ke vzdáleným protobuf odkazům, které by měly být aktualizovány. Ponechte tento argument prázdný, chcete-li aktualizovat všechny vzdálené odkazy. |

### <a name="options"></a>Možnosti

| Krátká volba | Dlouhá volba | Popis |
|-|-|-|
| -p | --projekt | Cesta k souboru projektu, na kterém chcete pracovat. Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.
| | --běh na suchu | Vypíše seznam souborů, které by byly aktualizovány bez stahování nového obsahu.

## <a name="list"></a>Seznam

Příkaz `list` se používá k zobrazení všech odkazů Protobuf v souboru projektu. Pokud jsou všechny hodnoty sloupce výchozími hodnotami, může být sloupec vynechán.

### <a name="usage"></a>Využití

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Možnosti

| Krátká volba | Dlouhá volba | Popis |
|-|-|-|
| -p | --projekt | Cesta k souboru projektu, na kterém chcete pracovat. Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
