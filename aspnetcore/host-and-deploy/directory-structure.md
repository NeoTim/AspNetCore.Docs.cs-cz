---
title: Struktura adresářů ASP.NET Core
author: guardrex
description: Další informace o struktuře adresářů publikované aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2018
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 4bc5ead8e24c4bb7fe6cd2f52fd2aa622187180c
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085499"
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
| [Nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd) | <ul><li>Publikování&dagger;<ul><li>Protokoly&dagger; (volitelné, pokud to není vyžadováno pro příjem protokolů stdout)</li><li>Zobrazení&dagger; (aplikace MVC; Pokud nejsou předkompilované zobrazení)</li><li>Stránky&dagger; (MVC Razor Pages aplikace nebo; pokud nejsou předkompilované stránky)</li><li>wwwroot&dagger;</li><li>*\.soubory knihoven DLL</li><li>{NÁZEV sestavení} deps.JSON</li><li>{NÁZEV sestavení} .dll</li><li>{NÁZEV sestavení} .pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{NÁZEV SESTAVENÍ}. Views.pdb</li><li>{NÁZEV sestavení}.runtimeconfig.json</li><li>soubor Web.config (nasazení služby IIS)</li></ul></li></ul> |
| [Samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>Publikování&dagger;<ul><li>Protokoly&dagger; (volitelné, pokud to není vyžadováno pro příjem protokolů stdout)</li><li>Zobrazení&dagger; (aplikace MVC; Pokud nejsou předkompilované zobrazení)</li><li>Stránky&dagger; (MVC Razor Pages aplikace nebo; pokud nejsou předkompilované stránky)</li><li>wwwroot&dagger;</li><li>\*knihovny DLL</li><li>{NÁZEV sestavení} deps.JSON</li><li>{NÁZEV sestavení} .dll</li><li>{NÁZEV sestavení} .exe</li><li>{NÁZEV sestavení} .pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{NÁZEV SESTAVENÍ}. Views.pdb</li><li>{NÁZEV sestavení}.runtimeconfig.json</li><li>soubor Web.config (nasazení služby IIS)</li></ul></li></ul> |

&dagger;Určuje adresář

*Publikovat* adresář představuje *obsahu kořenová cesta*, označované také jako *základní cesta aplikace*, nasazení. Název je uveden *publikovat* adresáře nasazené aplikace na serveru, její umístění slouží jako fyzická cesta serveru k hostované aplikace.

*Wwwroot* adresář, pokud jsou k dispozici, obsahuje pouze statické prostředky.

A *protokoly* lze vytvořit adresář pro nasazení pomocí jedné z následujících dvou přístupů:

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

[ASP.NET Core modulu stdout protokolování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) nevyžaduje, aby *protokoly* složky v nasazení. Modul je schopná vytvořit některou ze složek v `stdoutLogFile` cesta při vytvoření souboru protokolu. Vytváření *protokoly* složka je užitečné pro [modul ASP.NET Core rozšířené protokolování ladění](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Složky v cestě k dispozici na `<handlerSetting>` hodnoty nevytvoří modulem automaticky a měla by existovat předem v nasazení umožňuje modulu pro zápis protokolů ladění.

## <a name="additional-resources"></a>Další zdroje

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Nasazení aplikace .NET core](/dotnet/core/deploying/)
* [Cílové verze rozhraní .NET Framework](/dotnet/standard/frameworks)
* [Katalog identifikátorů RID .NET core](/dotnet/core/rid-catalog)
