---
title: Struktura adresářů ASP.NET Core
author: guardrex
description: Další informace o struktuře adresářů publikované aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2018
uid: host-and-deploy/directory-structure
ms.openlocfilehash: ee0bebb8b5c688f8471d6420d1641b87ac271f6c
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284562"
---
# <a name="aspnet-core-directory-structure"></a>Struktura adresářů ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

*Publikovat* adresář obsahuje nasaditelné prostředky aplikace vytvořené metodou [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu. Adresář obsahuje:

* Soubory aplikace
* Konfigurační soubory
* Statické prostředky
* Balíčky
* Modul runtime ([samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) jenom)

| Typ aplikace | Adresářová struktura |
| -------- | ------------------- |
| [Nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd) | <ul><li>Publikování&dagger;<ul><li>Protokoly&dagger; (volitelné, pokud to není vyžadováno pro příjem protokolů stdout)</li><li>Zobrazení&dagger; (aplikace MVC; Pokud nejsou předkompilované zobrazení)</li><li>Stránky&dagger; (MVC Razor Pages aplikace nebo; pokud nejsou předkompilované stránky)</li><li>Wwwroot&dagger;</li><li>*\.soubory knihoven DLL</li><li>{NÁZEV sestavení} deps.JSON</li><li>{NÁZEV sestavení} .dll</li><li>{NÁZEV sestavení} .pdb</li><li>{NÁZEV SESTAVENÍ}. Views.dll</li><li>{NÁZEV SESTAVENÍ}. Views.pdb</li><li>{NÁZEV sestavení}.runtimeconfig.json</li><li>soubor Web.config (nasazení služby IIS)</li></ul></li></ul> |
| [Samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>Publikování&dagger;<ul><li>Protokoly&dagger; (volitelné, pokud to není vyžadováno pro příjem protokolů stdout)</li><li>Zobrazení&dagger; (aplikace MVC; Pokud nejsou předkompilované zobrazení)</li><li>Stránky&dagger; (MVC Razor Pages aplikace nebo; pokud nejsou předkompilované stránky)</li><li>Wwwroot&dagger;</li><li>\*knihovny DLL</li><li>{NÁZEV sestavení} deps.JSON</li><li>{NÁZEV sestavení} .dll</li><li>{NÁZEV sestavení} .exe</li><li>{NÁZEV sestavení} .pdb</li><li>{NÁZEV SESTAVENÍ}. Views.dll</li><li>{NÁZEV SESTAVENÍ}. Views.pdb</li><li>{NÁZEV sestavení}.runtimeconfig.json</li><li>soubor Web.config (nasazení služby IIS)</li></ul></li></ul> |

&dagger;Určuje adresář

*Publikovat* adresář představuje *obsahu kořenová cesta*, označované také jako *základní cesta aplikace*, nasazení. Název je uveden *publikovat* adresáře nasazené aplikace na serveru, její umístění slouží jako fyzická cesta serveru k hostované aplikace.

*Wwwroot* adresář, pokud jsou k dispozici, obsahuje pouze statické prostředky.

Stdout *protokoly* lze vytvořit adresář pro nasazení pomocí jedné z následujících dvou přístupů:

* Přidejte následující `<Target>` element do souboru projektu:

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

   `<MakeDir>` Element vytvoří prázdnou *protokoly* složky v publikované výstup. Element používá `PublishDir` a určí cílové umístění pro vytvoření složky. Několik metod nasazení, jako je nasazení webu, přeskočte prázdné složky během nasazení. `<WriteLinesToFile>` Element generuje soubor v *protokoly* složky, která zaručuje nasazení složky na serveru. Vytvoření složky Tento přístup selže, pokud pracovní proces nemá oprávnění k zápisu do cílové složky.

* Fyzicky vytvořit *protokoly* adresáře na serveru v nasazení.

Adresář nasazení vyžaduje oprávnění ke čtení a spouštění. *Protokoly* directory vyžaduje oprávnění ke čtení/zápisu. Další adresáře, kde soubory jsou zapsány vyžadují oprávnění pro čtení a zápis.

## <a name="additional-resources"></a>Další zdroje

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Nasazení aplikace .NET core](/dotnet/core/deploying/)
* [Cílové verze rozhraní .NET Framework](/dotnet/standard/frameworks)
* [Katalog identifikátorů RID .NET core](/dotnet/core/rid-catalog)
