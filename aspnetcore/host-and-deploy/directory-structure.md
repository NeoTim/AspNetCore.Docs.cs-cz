---
title: Struktura ASP.NET Core Directory
author: guardrex
description: Další informace o struktuře adresářů publikované aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: ba5cb96dfdcdca10034299e3bbe662ce056af791
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76870263"
---
# <a name="aspnet-core-directory-structure"></a>Struktura ASP.NET Core Directory

Podle [Luke Latham](https://github.com/guardrex)

Adresář *Publisher* obsahuje nasaditelné prostředky aplikace vytvořené pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) . Adresář obsahuje:

* Soubory aplikace
* Konfigurační soubory
* Statické prostředky
* Balíčky
* Modul runtime (jenom[samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) )

| Typ aplikace | Adresářová struktura |
| -------- | ------------------- |
| [Spustitelný soubor závislý na rozhraní (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>publikování&dagger;<ul><li>Zobrazení&dagger; aplikace MVC; Pokud zobrazení nejsou předkompilována</li><li>Stránky&dagger; MVC nebo Razor Pages aplikace, pokud nejsou stránky předkompilovány</li><li>wwwroot&dagger;</li><li>*soubory. dll</li><li>{název sestavení}. DEPS. json</li><li>{název sestavení}. dll</li><li>{název sestavení} {. Extension} *. exe* rozšíření v systému Windows, žádné rozšíření na MacOS nebo Linux</li><li>{Assembly Name}. pdb</li><li>{název sestavení}. Views. dll</li><li>{ASSEMBLY NAME}. Zobrazení. pdb</li><li>{název sestavení}. runtimeconfig. JSON</li><li>Web. config (nasazení služby IIS)</li><li>createdump ([Linux createdump Utility](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>* . (Knihovna sdílených objektů Linux)</li><li>*. a (archiv MacOS)</li><li>* . DYLIB (MacOS Dynamic Library)</li></ul></li></ul> |
| [Samostatně uzavřené nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publikování&dagger;<ul><li>Zobrazení&dagger; aplikace MVC, pokud zobrazení nejsou předkompilována</li><li>Stránky&dagger; MVC nebo Razor Pages aplikace, pokud nejsou stránky předkompilovány</li><li>wwwroot&dagger;</li><li>soubory *. dll</li><li>{ASSEMBLY NAME}. DEPS. JSON</li><li>{ASSEMBLY NAME}. dll</li><li>{ASSEMBLY NAME}. exe</li><li>{NÁZEV sestavení}. pdb</li><li>{NÁZEV SESTAVENÍ}. Views. dll</li><li>{NÁZEV SESTAVENÍ}. Views. pdb</li><li>{ASSEMBLY NAME}. runtimeconfig. JSON</li><li>Web. config (nasazení služby IIS)</li></ul></li></ul> |

&dagger;označuje adresář.

Adresář *Publisher* představuje *kořenovou cestu obsahu*, která se označuje také jako *základní cesta aplikace*nasazení. Bez ohledu na název se předává adresář pro *publikování* nasazené aplikace na serveru. jeho umístění slouží jako fyzická cesta serveru k hostované aplikaci.

Adresář *wwwroot* , pokud je k dispozici, obsahuje pouze statické prostředky.

::: moniker range="< aspnetcore-3.0"

Vytvoření složky *logs* je užitečné, když [ASP.NET Core modul rozšířené ladění protokolování](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Složky v cestě zadané `<handlerSetting>` hodnoty se nevytváří modulem automaticky a měly by být v nasazení předběžně existující, aby modul mohl zapisovat do protokolu ladění.

Adresář *protokolů* lze pro nasazení vytvořit pomocí jednoho z následujících dvou přístupů:

* Do souboru projektu přidejte následující `<Target>` element:

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   Element `<MakeDir>` vytvoří prázdnou složku *logs* v publikovaném výstupu. Element používá vlastnost `PublishDir` k určení cílového umístění pro vytvoření složky. Několik metod nasazení, například Nasazení webu, během nasazování Přeskočit prázdné složky. Element `<WriteLinesToFile>` vygeneruje soubor ve složce *logs* , který garantuje nasazení složky na server. Vytvoření složky pomocí tohoto přístupu se nepovede, pokud pracovní proces nemá oprávnění k zápisu do cílové složky.

* Fyzicky vytvořte adresář *protokolů* na serveru v nasazení.

Adresář nasazení vyžaduje oprávnění ke čtení/spouštění. Adresář *protokolů* vyžaduje oprávnění ke čtení a zápisu. Další adresáře, ve kterých jsou soubory napsány, vyžadují oprávnění ke čtení a zápisu.

::: moniker-end

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Nasazení aplikace .NET core](/dotnet/core/deploying/)
* [Cílové architektury](/dotnet/standard/frameworks)
* [Katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog)
