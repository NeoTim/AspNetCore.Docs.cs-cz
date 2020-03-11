---
title: Správa a životnost klíče ochrany dat v ASP.NET Core
author: rick-anderson
description: Seznamte se se správou klíčů a životností dat v ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 2f022a4c7519485fe629ce47c27d214c8c27d5bc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667963"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Správa a životnost klíče ochrany dat v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Správa klíčů

Aplikace se pokusí zjistit své provozní prostředí a sám zpracovat konfiguraci klíče.

1. Pokud je aplikace hostovaná v [aplikacích Azure](https://azure.microsoft.com/services/app-service/), klíče se uchovávají do složky *%Home%\ASP.NET\DataProtection-Keys* . Tato složka je zajištěná síťovým úložištěm a je synchronizovaná napříč všemi počítači hostujícími aplikaci.
   * Klíče nejsou chráněny v klidovém umístění.
   * Složka *DataProtection-Keys* poskytuje klíčového prstence všem instancím aplikace v jednom slotu nasazení.
   * Samostatné sloty nasazení, jako je například pracovní a produkční, nesdílejte klíč Ring. Když proměníte mezi sloty nasazení, například swaping v produkčním prostředí nebo pomocí testování/B, nebude mít jakákoli aplikace využívající ochranu dat k dešifrování uložených dat pomocí služby Key Ring v předchozí pozici. To vede k odhlášení uživatelů z aplikace, která používá standardní ověřování souborů cookie ASP.NET Core, protože používá ochranu dat k ochraně svých souborů cookie. Pokud si přejete zakroužky klíčů nezávislé na slotech, použijte externího poskytovatele služby Key Ring, jako je například Azure Blob Storage, Azure Key Vault, úložiště SQL nebo Redis Cache.

1. Pokud je profil uživatele k dispozici, zachovají se klíče do složky *%localappdata%\ASP.NET\DataProtection-Keys* . Pokud je operační systém Windows, klíče se zašifrují v klidovém stavu pomocí DPAPI.

   Musí být povolený i [atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací. Výchozí hodnota `setProfileEnvironment` je `true`. V některých scénářích (například operační systém Windows) je `setProfileEnvironment` nastaveno na `false`. Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:

   1. Přejděte do složky *% windir%/system32/Inetsrv/config* .
   1. Otevřete soubor *ApplicationHost. config* .
   1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
   1. Potvrďte, že atribut `setProfileEnvironment` není přítomen, což nastaví hodnotu `true`, nebo explicitně nastavte hodnotu atributu na `true`.

1. Pokud je aplikace hostovaná ve službě IIS, klíče se uchovávají v registru HKLM ve speciálním klíči registru, který se ACLed jenom na účet pracovního procesu. Klíče jsou v klidovém stavu šifrované pomocí DPAPI.

1. Pokud žádná z těchto podmínek nesouhlasí, klíče nejsou trvale uložené mimo aktuální proces. Po ukončení procesu se všechny vygenerované klíče ztratí.

Vývojář je vždy v úplném řízení a může potlačit, jak a kde jsou klíče uložené. První tři možnosti výše by měly poskytnout dobré výchozí hodnoty pro většinu aplikací, podobně jako rutiny automatické generace ASP.NET **\<> machineKey** v minulosti. Poslední, záložní možnost je jediným scénářem, který vyžaduje, aby vývojář předem určil [konfiguraci](xref:security/data-protection/configuration/overview) , pokud chce, ale tato záloha nastane jenom ve výjimečných situacích.

Při hostování v kontejneru Docker by měly být klíče trvale uložené ve složce, která je svazkem Docker (sdílený svazek nebo svazek připojený k hostiteli, který přetrvává mimo životní cyklus kontejneru), nebo externímu poskytovateli, jako je například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) nebo [Redis](https://redis.io/). Externí poskytovatel je také užitečný ve scénářích webové farmy, pokud aplikace nemůžou přistupovat ke sdílenému síťovému svazku (Další informace najdete v tématu [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) ).

> [!WARNING]
> Pokud vývojář přepíše pravidla uvedená výše a odkazuje na systém ochrany dat v určitém úložišti klíčů, zakáže se automatické šifrování klíčů v klidovém režimu. Ochranu na základě REST lze znovu povolit prostřednictvím [Konfigurace](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Životnost klíče

Klíče mají ve výchozím nastavení dobu ve 90 dnech. Po vypršení platnosti klíče aplikace automaticky vygeneruje nový klíč a jako aktivní klíč nastaví nový klíč. Pokud v systému zůstanou vyřazené klíče, může vaše aplikace dešifrovat všechna chráněná data. Další informace najdete v tématu [Správa klíčů](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) .

## <a name="default-algorithms"></a>Výchozí algoritmy

Výchozí použitý algoritmus ochrany datových částí je AES-256-CBC pro zajištění důvěrnosti a HMACSHA256 pro pravost. 512 bitový hlavní klíč, který se změní každých 90 dní, se používá pro odvození dvou podklíčů používaných pro tyto algoritmy pro každou datovou část. Další informace naleznete v tématu [odvození podklíče](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) .

## <a name="additional-resources"></a>Další zdroje

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
