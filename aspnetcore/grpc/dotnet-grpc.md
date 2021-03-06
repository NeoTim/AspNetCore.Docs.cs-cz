---
title: Správa odkazů Protobuf pomocí dotnet-grpc
author: juntaoluo
description: Přečtěte si, jak přidávat, aktualizovat, odebírat a zobrazovat odkazy na Protobuf s globálním nástrojem dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: 18d504d7a130aeaa9f8ab6cca6c92dab5febfae6
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633237"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Správa odkazů Protobuf pomocí dotnet-grpc

Od [Jan Luo](https://github.com/juntaoluo)

`dotnet-grpc` je globální nástroj .NET Core pro správu [Protobuf (*.* a)](xref:grpc/basics#proto-file) v rámci projektu .NET gRPC. Nástroj lze použít k přidání, aktualizaci, odebrání a výpisu odkazů Protobuf.

## <a name="installation"></a>Instalace

Pokud chcete nainstalovat `dotnet-grpc` [globální nástroj .NET Core](/dotnet/core/tools/global-tools), spusťte následující příkaz:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Přidat odkazy

`dotnet-grpc` lze použít k přidání odkazů Protobuf jako `<Protobuf />` položek do souboru *. csproj* :

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Odkazy na Protobuf slouží ke generování prostředků v jazyce C# a/nebo serveru. `dotnet-grpc`Nástroj může:

* Vytvořte odkaz Protobuf z místních souborů na disku.
* Vytvoří odkaz Protobuf ze vzdáleného souboru zadaného adresou URL.
* Zajistěte, aby se do projektu přidaly správné závislosti balíčků gRPC.

Například `Grpc.AspNetCore` balíček se přidá do webové aplikace. `Grpc.AspNetCore` obsahuje gRPC Server a klientské knihovny a podporu nástrojů. `Grpc.Net.Client` `Grpc.Tools` `Google.Protobuf` Do konzolové aplikace jsou přidány také balíčky, a, které obsahují pouze klientské knihovny gRPC a podpora nástrojů.

### <a name="add-file"></a>Přidat soubor

`add-file`Příkaz se používá k přidání místních souborů na disk jako Protobuf odkazů. Zadané cesty k souborům:

* Může být relativní vzhledem k aktuálnímu adresáři nebo absolutním cestám.
* Může obsahovat zástupné znaky pro [expanzi](https://wikipedia.org/wiki/Glob_(programming))souborů založené na vzorcích.

Pokud jsou některé soubory mimo adresář projektu, `Link` je přidán prvek pro zobrazení souboru ve složce `Protos` v aplikaci Visual Studio.

### <a name="usage"></a>Využití

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Arguments

| Argument | Popis |
|-|-|
| files | Odkazy na soubor protobuf. Může se jednat o cestu k glob pro místní soubory protobuf. |

#### <a name="options"></a>Možnosti

| Krátká možnost | Možnost Long | Popis |
|-|-|-|
| -p | --Project | Cesta k souboru projektu, na kterém má být provozována. Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.
| -s | – služby | Typ gRPC služeb, které se mají vygenerovat Je `Default` -li zadán, `Both` je použit pro webové projekty a `Client` používá se pro newebové projekty. Přijaté hodnoty jsou `Both` , `Client` , `Default` , `None` , `Server` .
| -i | --Další-import-adresářů | Další adresáře, které se mají použít při překladu importu souborů protobuf Toto je seznam cest oddělených středníky.
| | – přístup | Modifikátor přístupu, který má být použit pro vygenerované třídy jazyka C#. Výchozí hodnota je `Public`. Přijaté hodnoty jsou `Internal` a `Public` .

### <a name="add-url"></a>Přidat adresu URL

`add-url`Příkaz se používá k přidání vzdáleného souboru zadaného zdrojovou adresou URL jako odkaz na Protobuf. Aby bylo možné určit, kam Stáhnout vzdálený soubor, je nutné zadat cestu k souboru. Cesta k souboru může být relativní vzhledem k aktuálnímu adresáři nebo absolutní cestě. Pokud je cesta k souboru mimo adresář projektu, `Link` je přidán element pro zobrazení souboru ve virtuální složce `Protos` v aplikaci Visual Studio.

### <a name="usage"></a>Využití

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Arguments

| Argument | Popis |
|-|-|
| url | Adresa URL vzdáleného souboru protobuf |

#### <a name="options"></a>Možnosti

| Krátká možnost | Možnost Long | Popis |
|-|-|-|
| -o | --output | Určuje cestu pro stažení vzdáleného souboru protobuf. Tato možnost je povinná.
| -p | --Project | Cesta k souboru projektu, na kterém má být provozována. Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.
| -s | – služby | Typ gRPC služeb, které se mají vygenerovat Je `Default` -li zadán, `Both` je použit pro webové projekty a `Client` používá se pro newebové projekty. Přijaté hodnoty jsou `Both` , `Client` , `Default` , `None` , `Server` .
| -i | --Další-import-adresářů | Další adresáře, které se mají použít při překladu importu souborů protobuf Toto je seznam cest oddělených středníky.
| | – přístup | Modifikátor přístupu, který má být použit pro vygenerované třídy jazyka C#. Výchozí hodnota je `Public` . Přijaté hodnoty jsou `Internal` a `Public` .

## <a name="remove"></a>Odebrat

`remove`Příkaz slouží k odebrání odkazů Protobuf ze souboru *. csproj* . Příkaz přijímá argumenty cesty a zdrojové adresy URL jako argumenty. Nástroj:

* Odebere pouze odkaz Protobuf.
* Neodstraní soubor *...* i v případě, že byl původně stažen ze vzdálené adresy URL.

### <a name="usage"></a>Využití

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Arguments

| Argument | Popis |
|-|-|
| odkazy | Adresy URL nebo cesty k souborům protobuf odkazů, které se mají odebrat. |

### <a name="options"></a>Možnosti

| Krátká možnost | Možnost Long | Popis |
|-|-|-|
| -p | --Project | Cesta k souboru projektu, na kterém má být provozována. Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.

## <a name="refresh"></a>Aktualizovat

`refresh`Příkaz slouží k aktualizaci vzdáleného odkazu s nejnovějším obsahem ze zdrojové adresy URL. K určení odkazu, který se má aktualizovat, se dá použít jak cestu souboru ke stažení, tak i zdrojová adresa URL. Poznámka:

* Hodnoty hash obsahu souboru se porovnávají s cílem určit, jestli se má místní soubor aktualizovat.
* Nesrovnávají se žádné informace o časovém razítku.

Nástroj vždy nahradí místní soubor vzdáleným souborem, pokud je požadována aktualizace.

### <a name="usage"></a>Využití

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Arguments

| Argument | Popis |
|-|-|
| odkazy | Adresy URL nebo cesty k souborům pro vzdálené protobuf odkazují na odkazy, které by se měly aktualizovat. Pokud chcete aktualizovat všechny vzdálené odkazy, ponechte tento argument prázdný. |

### <a name="options"></a>Možnosti

| Krátká možnost | Možnost Long | Popis |
|-|-|-|
| -p | --Project | Cesta k souboru projektu, na kterém má být provozována. Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.
| | --suché-běh | Vytvoří výstup seznamu souborů, které by se aktualizovaly bez stažení nového obsahu.

## <a name="list"></a>Seznam

`list`Příkaz slouží k zobrazení všech odkazů Protobuf v souboru projektu. Pokud jsou všechny hodnoty sloupce výchozí hodnoty, sloupec může být vynechán.

### <a name="usage"></a>Využití

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Možnosti

| Krátká možnost | Možnost Long | Popis |
|-|-|-|
| -p | --Project | Cesta k souboru projektu, na kterém má být provozována. Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.

## <a name="additional-resources"></a>Další materiály

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
