---
title: ASP.NET Základní adresářová struktura
author: rick-anderson
description: Přečtěte si o adresářové struktuře publikovaných aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 0e7bf40520385b7719cb37120709e0a3fd2442e3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989732"
---
# <a name="aspnet-core-directory-structure"></a>ASP.NET Základní adresářová struktura

::: moniker range=">= aspnetcore-3.0"

Adresář *publikování* obsahuje nasaditelné prostředky aplikace vytvořené příkazem [dotnet publish.](/dotnet/core/tools/dotnet-publish) Adresář obsahuje:

* Soubory aplikací
* Konfigurační soubory
* Statické datové zdroje
* Balíčky
* Runtime (pouze[samostatné nasazení)](/dotnet/core/deploying/#self-contained-deployments-scd)

| Typ aplikací | Adresářová struktura |
| -------- | ------------------- |
| [Spustitelný soubor závislý na rámci (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>Publikovat&dagger;<ul><li>Zobrazení&dagger; aplikací MVC; Pokud zobrazení nejsou předkompilována</li><li>Stránky&dagger; Aplikace MVC nebo Razor Pages, pokud stránky nejsou předkompilované</li><li>wwwroot&dagger;</li><li>\*Soubory DLL</li><li>{NÁZEV SESTAVENÍ}.deps.json</li><li>{NÁZEV SESTAVENÍ}.dll</li><li>{NÁZEV SESTAVENÍ} {. ROZŠÍŘENÍ} *.exe* rozšíření v systému Windows, žádné rozšíření na macOS nebo Linux</li><li>{NÁZEV SESTAVENÍ}.pdb</li><li>{NÁZEV SESTAVENÍ}. Zobrazení.dll</li><li>{NÁZEV SESTAVENÍ}. Zobrazení.pdb</li><li>{NÁZEV SESTAVENÍ}.runtimeconfig.json</li><li>web.config (nasazení služby IIS)</li><li>createdump ([Linux createdump nástroj](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (Linux knihovna sdílených objektů)</li><li>\*.a (archiv macOS)</li><li>\*.dylib (dynamická knihovna macOS)</li></ul></li></ul> |
| [Samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>Publikovat&dagger;<ul><li>Zobrazení&dagger; aplikací MVC, pokud zobrazení nejsou předkompilované</li><li>Stránky&dagger; Aplikace MVC nebo Razor Pages, pokud stránky nejsou předkompilované</li><li>wwwroot&dagger;</li><li>\*Soubory DLL</li><li>{NÁZEV SESTAVENÍ}.deps.json</li><li>{NÁZEV SESTAVENÍ}.dll</li><li>{NÁZEV SESTAVENÍ}.exe</li><li>{NÁZEV SESTAVENÍ}.pdb</li><li>{NÁZEV SESTAVENÍ}. Zobrazení.dll</li><li>{NÁZEV SESTAVENÍ}. Zobrazení.pdb</li><li>{NÁZEV SESTAVENÍ}.runtimeconfig.json</li><li>web.config (nasazení služby IIS)</li></ul></li></ul> |

&dagger;Označuje adresář.

Adresář *publikování* představuje *kořenovou cestu obsahu*, označovanou také jako cesta základní *aplikace*, nasazení. Bez ohledu na název *adresáře publikování* nasazené aplikace na serveru, jeho umístění slouží jako fyzická cesta k hostované aplikaci serveru.

Adresář *wwwroot,* pokud je k dispozici, obsahuje pouze statické datové zdroje.

## <a name="additional-resources"></a>Další zdroje

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* [Cílové architektury](/dotnet/standard/frameworks)
* [Katalog RID jádra rozhraní .NET](/dotnet/core/rid-catalog)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Adresář *publikování* obsahuje nasaditelné prostředky aplikace vytvořené příkazem [dotnet publish.](/dotnet/core/tools/dotnet-publish) Adresář obsahuje:

* Soubory aplikací
* Konfigurační soubory
* Statické datové zdroje
* Balíčky
* Runtime (pouze[samostatné nasazení)](/dotnet/core/deploying/#self-contained-deployments-scd)

| Typ aplikací | Adresářová struktura |
| -------- | ------------------- |
| [Spustitelný soubor závislý na rámci (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>Publikovat&dagger;<ul><li>Zobrazení&dagger; aplikací MVC; Pokud zobrazení nejsou předkompilována</li><li>Stránky&dagger; Aplikace MVC nebo Razor Pages, pokud stránky nejsou předkompilované</li><li>wwwroot&dagger;</li><li>\*Soubory DLL</li><li>{NÁZEV SESTAVENÍ}.deps.json</li><li>{NÁZEV SESTAVENÍ}.dll</li><li>{NÁZEV SESTAVENÍ} {. ROZŠÍŘENÍ} *.exe* rozšíření v systému Windows, žádné rozšíření na macOS nebo Linux</li><li>{NÁZEV SESTAVENÍ}.pdb</li><li>{NÁZEV SESTAVENÍ}. Zobrazení.dll</li><li>{NÁZEV SESTAVENÍ}. Zobrazení.pdb</li><li>{NÁZEV SESTAVENÍ}.runtimeconfig.json</li><li>web.config (nasazení služby IIS)</li><li>createdump ([Linux createdump nástroj](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (Linux knihovna sdílených objektů)</li><li>\*.a (archiv macOS)</li><li>\*.dylib (dynamická knihovna macOS)</li></ul></li></ul> |
| [Samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>Publikovat&dagger;<ul><li>Zobrazení&dagger; aplikací MVC, pokud zobrazení nejsou předkompilované</li><li>Stránky&dagger; Aplikace MVC nebo Razor Pages, pokud stránky nejsou předkompilované</li><li>wwwroot&dagger;</li><li>\*Soubory DLL</li><li>{NÁZEV SESTAVENÍ}.deps.json</li><li>{NÁZEV SESTAVENÍ}.dll</li><li>{NÁZEV SESTAVENÍ}.exe</li><li>{NÁZEV SESTAVENÍ}.pdb</li><li>{NÁZEV SESTAVENÍ}. Zobrazení.dll</li><li>{NÁZEV SESTAVENÍ}. Zobrazení.pdb</li><li>{NÁZEV SESTAVENÍ}.runtimeconfig.json</li><li>web.config (nasazení služby IIS)</li></ul></li></ul> |
-
&dagger;Označuje adresář.

Adresář *publikování* představuje *kořenovou cestu obsahu*, označovanou také jako cesta základní *aplikace*, nasazení. Bez ohledu na název *adresáře publikování* nasazené aplikace na serveru, jeho umístění slouží jako fyzická cesta k hostované aplikaci serveru.

Adresář *wwwroot,* pokud je k dispozici, obsahuje pouze statické datové zdroje.

Vytvoření složky *Protokoly* je užitečné pro protokolování ladění s [rozšířeným laděním ASP.NET core modulem](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Složky v cestě k `<handlerSetting>` hodnotě nejsou vytvořeny modulem automaticky a měly by existovat v nasazení, aby modul mohl zapisovat protokol ladění.

Adresář *protokolů* lze vytvořit pro nasazení pomocí jednoho z následujících dvou přístupů:

* Do souboru projektu přidejte následující `<Target>` prvek:

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

   Prvek `<MakeDir>` vytvoří prázdnou složku *Protokoly* v publikovaném výstupu. Prvek používá `PublishDir` vlastnost k určení cílového umístění pro vytvoření složky. Několik metod nasazení, například nasazení webu, přeskočte prázdné složky během nasazení. Prvek `<WriteLinesToFile>` generuje soubor ve složce *Protokoly,* který zaručuje nasazení složky na server. Vytvoření složky pomocí tohoto přístupu se nezdaří, pokud pracovní proces nemá přístup pro zápis do cílové složky.

* Fyzicky vytvořte adresář *Protokoly* na serveru v nasazení.

Adresář nasazení vyžaduje oprávnění ke čtení a spouštění. Adresář *Protokoly* vyžaduje oprávnění ke čtení a zápisu. Další adresáře, kde jsou soubory zapsány vyžadují oprávnění pro čtení a zápis.

## <a name="additional-resources"></a>Další zdroje

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* [Cílové architektury](/dotnet/standard/frameworks)
* [Katalog RID jádra rozhraní .NET](/dotnet/core/rid-catalog)

::: moniker-end
