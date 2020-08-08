---
title: Struktura ASP.NET Core Directory
author: rick-anderson
description: Přečtěte si o adresářové struktuře publikovaných aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 8756fa5b85deb3a30991062942803e701e5e0792
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015813"
---
# <a name="aspnet-core-directory-structure"></a>Struktura ASP.NET Core Directory

::: moniker range=">= aspnetcore-3.0"

Adresář *Publisher* obsahuje nasaditelné prostředky aplikace vytvořené pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) . Adresář obsahuje:

* Soubory aplikace
* Konfigurační soubory
* Statické prostředky
* Balíčky
* Modul runtime (jenom[samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) )

| Typ aplikací | Adresářová struktura |
| -------- | ------------------- |
| [Spustitelný soubor závislý na rozhraní (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>opětovn&dagger;<ul><li>Zobrazení &dagger; aplikací MVC; Pokud zobrazení nejsou předkompilována</li><li>Stránky &dagger; MVC nebo Razor stránky aplikace, pokud nejsou stránky předem kompilovány</li><li>wwwroot&dagger;</li><li>\*soubory. dll</li><li>{NÁZEV sestavení} .deps.js.</li><li>{ASSEMBLY NAME}. dll</li><li>{NÁZEV SESTAVENÍ} {. Extension}. exe rozšíření v systému Windows, žádné rozšíření na macOS nebo Linux</li><li>{NÁZEV sestavení}. pdb</li><li>{NÁZEV SESTAVENÍ} .Views.dll</li><li>{NÁZEV SESTAVENÍ}. Views. pdb</li><li>{NÁZEV sestavení} .runtimeconfig.js.</li><li>web.config (nasazení služby IIS)</li><li>createdump ([Nástroj Linux createdump](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*. so (Knihovna sdílených objektů Linux)</li><li>\*. a (archiv macOS)</li><li>\*. DYLIB (dynamická knihovna macOS)</li></ul></li></ul> |
| [Samostatně uzavřené nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>opětovn&dagger;<ul><li>Zobrazí &dagger; aplikace MVC, pokud zobrazení nejsou předkompilována.</li><li>Stránky &dagger; MVC nebo Razor stránky aplikace, pokud nejsou stránky předem kompilovány</li><li>wwwroot&dagger;</li><li>\*soubory. dll</li><li>{NÁZEV sestavení} .deps.js.</li><li>{ASSEMBLY NAME}. dll</li><li>{ASSEMBLY NAME}. exe</li><li>{NÁZEV sestavení}. pdb</li><li>{NÁZEV SESTAVENÍ} .Views.dll</li><li>{NÁZEV SESTAVENÍ}. Views. pdb</li><li>{NÁZEV sestavení} .runtimeconfig.js.</li><li>web.config (nasazení služby IIS)</li></ul></li></ul> |

&dagger;Indikuje adresář.

Adresář *Publisher* představuje *kořenovou cestu obsahu*, která se označuje také jako *základní cesta aplikace*nasazení. Bez ohledu na název se předává adresář pro *publikování* nasazené aplikace na serveru. jeho umístění slouží jako fyzická cesta serveru k hostované aplikaci.

Adresář *wwwroot* , pokud je k dispozici, obsahuje pouze statické prostředky.

## <a name="additional-resources"></a>Další zdroje

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* [Cílové architektury](/dotnet/standard/frameworks)
* [Katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Adresář *Publisher* obsahuje nasaditelné prostředky aplikace vytvořené pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) . Adresář obsahuje:

* Soubory aplikace
* Konfigurační soubory
* Statické prostředky
* Balíčky
* Modul runtime (jenom[samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) )

| Typ aplikací | Adresářová struktura |
| -------- | ------------------- |
| [Spustitelný soubor závislý na rozhraní (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>opětovn&dagger;<ul><li>Zobrazení &dagger; aplikací MVC; Pokud zobrazení nejsou předkompilována</li><li>Stránky &dagger; MVC nebo Razor stránky aplikace, pokud nejsou stránky předem kompilovány</li><li>wwwroot&dagger;</li><li>\*soubory. dll</li><li>{NÁZEV sestavení} .deps.js.</li><li>{ASSEMBLY NAME}. dll</li><li>{NÁZEV SESTAVENÍ} {. Extension}. exe rozšíření v systému Windows, žádné rozšíření na macOS nebo Linux</li><li>{NÁZEV sestavení}. pdb</li><li>{NÁZEV SESTAVENÍ} .Views.dll</li><li>{NÁZEV SESTAVENÍ}. Views. pdb</li><li>{NÁZEV sestavení} .runtimeconfig.js.</li><li>web.config (nasazení služby IIS)</li><li>createdump ([Nástroj Linux createdump](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*. so (Knihovna sdílených objektů Linux)</li><li>\*. a (archiv macOS)</li><li>\*. DYLIB (dynamická knihovna macOS)</li></ul></li></ul> |
| [Samostatně uzavřené nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>opětovn&dagger;<ul><li>Zobrazí &dagger; aplikace MVC, pokud zobrazení nejsou předkompilována.</li><li>Stránky &dagger; MVC nebo Razor stránky aplikace, pokud nejsou stránky předem kompilovány</li><li>wwwroot&dagger;</li><li>\*soubory. dll</li><li>{NÁZEV sestavení} .deps.js.</li><li>{ASSEMBLY NAME}. dll</li><li>{ASSEMBLY NAME}. exe</li><li>{NÁZEV sestavení}. pdb</li><li>{NÁZEV SESTAVENÍ} .Views.dll</li><li>{NÁZEV SESTAVENÍ}. Views. pdb</li><li>{NÁZEV sestavení} .runtimeconfig.js.</li><li>web.config (nasazení služby IIS)</li></ul></li></ul> |

&dagger;Indikuje adresář.

Adresář *Publisher* představuje *kořenovou cestu obsahu*, která se označuje také jako *základní cesta aplikace*nasazení. Bez ohledu na název se předává adresář pro *publikování* nasazené aplikace na serveru. jeho umístění slouží jako fyzická cesta serveru k hostované aplikaci.

Adresář *wwwroot* , pokud je k dispozici, obsahuje pouze statické prostředky.

Vytvoření složky *logs* je užitečné, když [ASP.NET Core modul rozšířené ladění protokolování](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Složky v cestě zadané `<handlerSetting>` hodnotě se modulem nevytváří automaticky a měly by být v nasazení předběžně existující, aby modul mohl zapisovat protokol ladění.

Adresář *protokolů* lze pro nasazení vytvořit pomocí jednoho z následujících dvou přístupů:

* `<Target>`Do souboru projektu přidejte následující element:

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

   `<MakeDir>`Element vytvoří prázdnou složku *logs* v publikovaném výstupu. Element používá `PublishDir` vlastnost k určení cílového umístění pro vytvoření složky. Několik metod nasazení, například Nasazení webu, během nasazování Přeskočit prázdné složky. `<WriteLinesToFile>`Element vygeneruje soubor ve složce *logs* , který garantuje nasazení složky na server. Vytvoření složky pomocí tohoto přístupu se nepovede, pokud pracovní proces nemá oprávnění k zápisu do cílové složky.

* Fyzicky vytvořte adresář *protokolů* na serveru v nasazení.

Adresář nasazení vyžaduje oprávnění ke čtení/spouštění. Adresář *protokolů* vyžaduje oprávnění ke čtení a zápisu. Další adresáře, ve kterých jsou soubory napsány, vyžadují oprávnění ke čtení a zápisu.

## <a name="additional-resources"></a>Další zdroje

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* [Cílové architektury](/dotnet/standard/frameworks)
* [Katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog)

::: moniker-end
