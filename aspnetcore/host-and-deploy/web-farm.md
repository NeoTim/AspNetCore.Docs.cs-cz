---
title: Host ASP.NET Core ve webové farmě
author: rick-anderson
description: Zjistěte, jak hostovat více instancí aplikace ASP.NET Core se sdílenými prostředky v prostředí webové farmy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/web-farm
ms.openlocfilehash: 316c87e5f49593c05991a94cbe5e55d175a49bb3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659367"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Host ASP.NET Core ve webové farmě

Podle [Chris Ross](https://github.com/Tratcher)

*Webová farma* je skupina dvou nebo více webových serverů (nebo *uzlů),* které hostují více instancí aplikace. Když požadavky od uživatelů dorazí do webové farmy, *vyrovnávání zatížení* distribuuje požadavky na uzly webové farmy. Webové farmy zlepšují:

* **Spolehlivost/dostupnost** &ndash; Pokud jeden nebo více uzlů selže, nástroj pro vyrovnávání zatížení může směrovat požadavky na jiné funkční uzly, aby pokračovaly ve zpracování požadavků.
* **Kapacita a výkon** &ndash; Více uzlů může zpracovat více požadavků než jeden server. Vyrovnávání zatížení vyrovnává zatížení rozložením požadavků do uzlů.
* **Škálovatelnost** &ndash; Pokud je vyžadováno více nebo méně kapacity, počet aktivních uzlů může být zvýšennebo snížen tak, aby odpovídal zatížení. Technologie platformy webové farmy, jako je [například Azure App Service](https://azure.microsoft.com/services/app-service/), můžete automaticky přidávat nebo odebírat uzly na žádost správce systému nebo automaticky bez lidského zásahu.
* **Uzly udržovatelnosti** &ndash; webové farmy se mohou spolehnout na sadu sdílených služeb, což má za následek snadnější správu systému. Uzly webové farmy se například mohou spolehnout na jeden databázový server a společné síťové umístění pro statické prostředky, jako jsou obrázky a soubory ke stažení.

Toto téma popisuje konfiguraci a závislosti pro ASP.NET základní aplikace hostované ve webové farmě, které spoléhají na sdílené prostředky.

## <a name="general-configuration"></a>Obecná konfigurace

<xref:host-and-deploy/index>  
Přečtěte si, jak nastavit hostingová prostředí a nasadit ASP.NET aplikace Core. Nakonfigurujte správce procesů na každém uzlu webové farmy, aby automatizoval spuštění a restartování aplikace. Každý uzel vyžaduje ASP.NET core runtime. Další informace naleznete v tématech v [hostitelské a nasazovat](xref:host-and-deploy/index) oblasti dokumentace.

<xref:host-and-deploy/proxy-load-balancer>  
Přečtěte si o konfiguraci aplikací hostovaných za proxy servery a nástroji pro vyrovnávání zatížení, které často zakrývají důležité informace o požadavcích.

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/) je [platforma pro cloud computing společnosti Microsoft](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core. Služba App Service je plně spravovaná platforma, která poskytuje automatické škálování, vyrovnávání zatížení, opravy a průběžné nasazení.

## <a name="app-data"></a>Data aplikací

Při škálování aplikace na více instancí může být stav aplikace, který vyžaduje sdílení mezi uzly. Pokud je stav přechodný, zvažte sdílení [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Pokud sdílený stav vyžaduje trvalost, zvažte uložení sdíleného stavu v databázi.

## <a name="required-configuration"></a>Požadovaná konfigurace

Ochrana dat a ukládání do mezipaměti vyžadují konfiguraci pro aplikace nasazené do webové farmy.

### <a name="data-protection"></a>Ochrana dat

Systém [ASP.NET Core Data Protection](xref:security/data-protection/introduction) používá aplikace k ochraně dat. Ochrana dat se opírá o sadu kryptografických klíčů uložených v *kroužku*na klíče . Když je systém ochrany dat inicializován, použije [výchozí nastavení,](xref:security/data-protection/configuration/default-settings) které ukládá kroužek klíčů místně. Ve výchozí konfiguraci je na každém uzlu webové farmy uložen jedinečný kroužek klíčů. V důsledku toho nemůže každý uzel webové farmy dešifrovat data, která je zašifrována aplikací na jiném uzlu. Výchozí konfigurace není obecně vhodná pro hostování aplikací ve webové farmě. Alternativou k implementaci sdíleného kroužku klíčů je vždy směrovat požadavky uživatelů do stejného uzlu. Další informace o konfiguraci systému ochrany dat <xref:security/data-protection/configuration/overview>pro nasazení webových farem naleznete v tématu .

### <a name="caching"></a>Ukládání do mezipaměti

V prostředí webové farmy musí mechanismus ukládání do mezipaměti sdílet položky uložené v mezipaměti v uzlech webové farmy. Ukládání do mezipaměti musí spoléhat na společnou mezipaměť Redis, sdílenou databázi serveru SQL Server nebo vlastní implementaci ukládání do mezipaměti, která sdílí položky uložené v mezipaměti v celé webové farmě. Další informace naleznete v tématu <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Závislé součásti

Následující scénáře nevyžadují další konfiguraci, ale závisí na technologiích, které vyžadují konfiguraci pro webové farmy.

| Scénář | Záleží na&hellip; |
| -------- | ------------------- |
| Authentication | Ochrana osobních <xref:security/data-protection/configuration/overview>údajů (viz ).<br><br>Další informace naleznete v tématech <xref:security/authentication/cookie> a <xref:security/cookie-sharing>. |
| Identita | Ověřování a konfigurace databáze.<br><br>Další informace naleznete v tématu <xref:security/authentication/identity>. |
| Relace | Ochrana osobních údajů (šifrované soubory cookie) (viz) <xref:security/data-protection/configuration/overview>a ukládání do mezipaměti (viz <xref:performance/caching/distributed>).<br><br>Další informace naleznete [v tématu Session and state management: Session state](xref:fundamentals/app-state#session-state). |
| TempData | Ochrana dat (šifrované <xref:security/data-protection/configuration/overview>soubory cookie) (viz) nebo relace (viz [Session and state management: Session State](xref:fundamentals/app-state#session-state)).<br><br>Další informace naleznete [v tématu Session and state management: TempData](xref:fundamentals/app-state#tempdata). |
| Proti padělání | Ochrana osobních <xref:security/data-protection/configuration/overview>údajů (viz ).<br><br>Další informace naleznete v tématu <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Řešení potíží

### <a name="data-protection-and-caching"></a>Ochrana dat a ukládání do mezipaměti

Pokud není ochrana dat nebo ukládání do mezipaměti nakonfigurováno pro prostředí webové farmy, dochází při zpracování požadavků k občasným chybám. K tomu dochází, protože uzly nesdílejí stejné prostředky a požadavky uživatelů nejsou vždy směrovány zpět do stejného uzlu.

Zvažte uživatele, který se přihlásí do aplikace pomocí ověřování souborů cookie. Uživatel se přihlásí do aplikace na jednom uzlu webové farmy. Pokud jejich další požadavek dorazí do stejného uzlu, kde se přihlásili, aplikace je schopna dešifrovat ověřovací soubor cookie a umožňuje přístup k prostředku aplikace. Pokud jejich další požadavek dorazí do jiného uzlu, aplikace nemůže dešifrovat ověřovací soubor cookie z uzlu, ve kterém se uživatel přihlásil, a autorizace pro požadovaný prostředek se nezdaří.

Pokud se některý z následujících příznaků **vyskytne přerušovaně**, je problém obvykle vysledován k nesprávné ochraně dat nebo konfiguraci ukládání do mezipaměti pro prostředí webové farmy:

* Přerušení &ndash; ověřování Ověřovací soubor cookie je chybně nakonfigurován nebo jej nelze dešifrovat. Přihlášení OAuth (Facebook, Microsoft, Twitter) nebo OpenIdConnect se nezdaří s chybou "Korelace se nezdařila".
* Autorizace &ndash; přeruší identitu je ztracena.
* Stav relace ztratí data.
* Položky uložené v mezipaměti zmizí.
* TempData se nezdaří.
* POSTs &ndash; nezdaří Kontrola proti padělání se nezdaří.

Další informace o konfiguraci ochrany dat pro <xref:security/data-protection/configuration/overview>nasazení webových farem naleznete v tématu . Další informace o konfiguraci mezipaměti pro nasazení <xref:performance/caching/distributed>webové farmy naleznete v tématu .

## <a name="obtain-data-from-apps"></a>Získání dat z aplikací

Pokud jsou aplikace webové farmy schopné reagovat na požadavky, získejte požadavek, připojení a další data z aplikací pomocí terminálového inline middlewaru. Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .

## <a name="additional-resources"></a>Další zdroje

* [Vlastní rozšíření skriptu pro Windows](/azure/virtual-machines/extensions/custom-script-windows) &ndash; Stahuje a spouští skripty na virtuálních počítačích Azure, což je užitečné pro konfiguraci po nasazení a instalaci softwaru.
* <xref:host-and-deploy/proxy-load-balancer>
 