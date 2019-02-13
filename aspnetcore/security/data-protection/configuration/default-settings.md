---
title: Správa klíčů ochrany dat a životnosti v ASP.NET Core
author: rick-anderson
description: Další informace o správu klíčů ochranu dat a životnosti v ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 2f022a4c7519485fe629ce47c27d214c8c27d5bc
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159208"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Správa klíčů ochrany dat a životnosti v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Správa klíčů

Aplikace se pokusí zjistit jeho provozní prostředí a zpracovávat konfiguraci klíče sama o sobě.

1. Pokud je aplikace hostovaná v [Azure Apps](https://azure.microsoft.com/services/app-service/), jsou zachované klíče *%HOME%\ASP.NET\DataProtection-Keys* složky. Tato složka je zajištěná síťového úložiště a synchronizují ve všech počítačích, který je hostitelem aplikace.
   * Klíče nejsou chráněné v klidovém stavu.
   * *DataProtection klíče* složky poskytuje kanál klíč na všechny instance aplikace ve slotu jedno nasazení.
   * Samostatné nasazovacích slotů, jako je například přípravným a produkčním prostředím, Nesdílejte klíč kanál. Když přepínat mezi sloty nasazení, například pracovní do produkčního prostředí záměna nebo pomocí A / B testování, jakoukoli aplikaci pomocí ochrany dat nebude možné dešifrovat uloženými daty pracujete pomocí aktualizační kanál, který klíč uvnitř předchozí slot. To vede k zaprotokolování mimo aplikaci, která využívá standardní ověřování souborů cookie s ASP.NET Core, protože používá ochranu dat k ochraně souborů cookie uživatele. Pokud vyžadujete, aby okruhy slotu nezávislé na klíč, použití poskytovatele vnější prstenec klíče, jako je Azure Blob Storage, Azure Key Vault, úložišti SQL, nebo z mezipaměti Redis.

1. Pokud se profil uživatele je k dispozici, jsou zachované klíče *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* složky. Pokud je operační systém Windows, že klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI.

   Fond aplikací [setProfileEnvironment atribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) také musí být povolené. Výchozí hodnota `setProfileEnvironment` je `true`. V některých případech (například Windows operačního systému) `setProfileEnvironment` je nastavena na `false`. Pokud se klíče nejsou uloženy v adresáři profilu uživatele jako očekávání:

   1. Přejděte *%windir%/system32/inetsrv/config* složky.
   1. Otevřít *applicationHost.config* souboru.
   1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
   1. Ujistěte se, že `setProfileEnvironment` atribut není k dispozici, která má výchozí hodnotu hodnotu k `true`, nebo explicitně nastavit hodnotu atributu na `true`.

1. Pokud je aplikace hostovaná ve službě IIS, jsou zachované v klíči registru HKLM ve speciální registru klíč, který je ACLed pouze pro účet pracovního procesu. Klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI.

1. Pokud neodpovídá žádná z těchto podmínek, nejsou trvalé klíče mimo aktuální proces. Když se proces ukončí, všechny vygenerované klíče se ztratí.

Vývojář je vždy plně pod kontrolou a můžete přepsat, jak a kde jsou uloženy klíče. První tři možnosti výše by měla poskytnout vhodné výchozí hodnoty pro většinu aplikací tím, jak podobné technologie ASP.NET  **\<machineKey >** automatické generování rutiny pracovali v minulosti. Možnost konečné, použití náhradní lokality je jediným případem, která vyžaduje pro vývojáře k určení [konfigurace](xref:security/data-protection/configuration/overview) předem, pokud chtějí trvalost klíče, ale tento záložní dochází pouze ve výjimečných případech.

Při hostování v kontejneru Dockeru, by měl nastavit jako trvalý klíčů, ve složce, která je svazek Docker (sdíleného svazku nebo svazek připojený hostitel, který potrvá déle než doba platnosti kontejneru) nebo do externího poskytovatele, jako například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) nebo [Redis](https://redis.io/). Externího poskytovatele je také užitečné ve webových farem aplikací nemůže získat přístup k sdíleného svazku (viz [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) Další informace).

> [!WARNING]
> Pokud vývojář přepsání pravidel uvedených výše a odkazuje systému ochrany dat na konkrétní úložiště klíčů, je zakázané automatické šifrování klíčů v klidovém stavu. V klidovém stavu ochrany může být znovu povolena prostřednictvím [konfigurace](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Životnost klíče

Klíče mají životnost 90 dní ve výchozím nastavení. Když vyprší platnost klíče, aplikace automaticky vygeneruje nový klíč a nastaví nový klíč jako aktivní klíč. Tak dlouho, dokud vyřazeno klíče zůstávají v systému, může vaše aplikace dešifrování jakýchkoli dat chráněné s nimi. Zobrazit [Správa klíčů](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) Další informace.

## <a name="default-algorithms"></a>Výchozí algoritmy

Výchozí datová část ochrany algoritmus používaný je AES-256-CBC důvěrnost a HMACSHA256 pravosti. 512 bitů hlavního klíče, změnit každých 90 dnů, se používá k odvození dvě dílčí klíče používané pro tyto algoritmy na základě na datovou část. Zobrazit [podklíče odvození](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) Další informace.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
