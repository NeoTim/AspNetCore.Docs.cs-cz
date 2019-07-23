---
title: Hostitele ASP.NET Core ve webové farmě
author: guardrex
description: Zjistěte, jak hostovat několik instancí aplikace ASP.NET Core se sdílenými prostředky v prostředí webové farmy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/web-farm
ms.openlocfilehash: df1be8cc76a5017923f26636a241b69881dfcc81
ms.sourcegitcommit: b4ef2b00f3e1eb287138f8b43c811cb35a100d3e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970112"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Hostitele ASP.NET Core ve webové farmě

Podle [Luke Latham](https://github.com/guardrex) a [Chris Ross](https://github.com/Tratcher)

A *webové farmy* je skupina dva nebo víc webových serverů (nebo *uzly*), které hostovat několik instancí aplikace. Při obdržení požadavků od uživatelů do webové farmy *nástroj pro vyrovnávání zatížení* distribuuje požadavky na uzly webové farmy. Zlepšení webových farem:

* **Spolehlivost a dostupnost** &ndash; když dojde k selhání jednoho nebo více uzlů, nástroje pro vyrovnávání zatížení může směrovat požadavky do dalších uzlů funkční pokračovat ve zpracování žádosti.
* **Kapacity a výkonu** &ndash; více uzlů může zpracovávat žádosti více než jeden server. Nástroje pro vyrovnávání zatížení vyrovnává zatížení díky distribuci požadavků na uzly.
* **Škálovatelnost** &ndash; když více nebo méně kapacity se vyžaduje, počet aktivních uzlů může být zvýší nebo sníží tak, aby odpovídaly zatížení. Webové farmy technologií platformy, například [služby Azure App Service](https://azure.microsoft.com/services/app-service/), můžete automaticky přidávat nebo odebírat uzly na žádost správce systému nebo automaticky bez zásahu člověka.
* **Udržovatelnost** &ndash; uzly webové farmy se můžete spolehnout na sadu sdílených služeb, což vede k jednodušší správu systému. Například uzly webové farmy, můžete spoléhat na jednoho databázového serveru a běžné umístění v síti pro statické prostředky, jako jsou obrázky a soubory ke stažení.

Toto téma popisuje konfiguraci a závislostech v ASP.NET core aplikacím hostovaným ve webové farmě, které spoléhají na sdílené prostředky.

## <a name="general-configuration"></a>Obecná konfigurace

<xref:host-and-deploy/index>  
Zjistěte, jak nastavit hostitelská prostředí a nasazení aplikace ASP.NET Core. Nakonfiguruje správce procesu v každém uzlu webovou farmu k automatizaci aplikace se spustí a restartuje. Každý uzel vyžaduje modul runtime ASP.NET Core. Další informace najdete v tématech v [hostitele a nasadit](xref:host-and-deploy/index) oblasti dokumentace.

<xref:host-and-deploy/proxy-load-balancer>  
Další informace o konfiguraci aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení, které často skryl důležité vyžádat informace.

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/) je [Microsoft cloud computingu platformová služba](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core. App Service je plně spravovaná platforma, která poskytuje automatické škálování, Vyrovnávání zatížení, použití dílčích oprav a průběžné nasazování.

## <a name="app-data"></a>Data aplikací

Když je aplikace škálovat do několika instancí, může být stav aplikace, která vyžaduje sdílení mezi uzly. Pokud stav není přechodná, vezměte v úvahu pro sdílení obsahu [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Pokud sdílený stav vyžaduje trvalost, zvažte uložení sdílený stav do databáze.

## <a name="required-configuration"></a>Požadované konfigurace

Ochrana dat a ukládání do mezipaměti vyžadují konfiguraci pro aplikace nasazené do webové farmy.

### <a name="data-protection"></a>Ochrana dat

[Systému ochrany dat ASP.NET Core](xref:security/data-protection/introduction) aplikací používají k ochraně dat. Ochrana dat závisí na sadu kryptografických klíčů uložených v *klíč prstenec*. Při inicializaci systému ochrany dat, se vztahuje [výchozí nastavení](xref:security/data-protection/configuration/default-settings) , který ukládat klíče aktualizační kanál, který místně. Pod výchozí konfigurace je jedinečný klíč kanál uložen na každém uzlu webové farmy. V důsledku toho každý uzel webové serverové farmy nelze dešifrovat data, která se šifrují aplikace na libovolném uzlu. Výchozí konfigurace není obvykle vhodné pro hostování aplikací ve webové farmě. Alternativa k implementaci sdílený klíč kanál je vždy směrovat požadavky uživatelů na stejném uzlu. Další informace o konfiguraci ochrany dat systému pro nasazení webové farmy najdete v tématu <xref:security/data-protection/configuration/overview>.

### <a name="caching"></a>Ukládání do mezipaměti

V prostředí webové farmy musejí sdílet mechanizmus ukládání do mezipaměti položek v mezipaměti napříč uzly webové farmy. Ukládání do mezipaměti musí buď spoléhat na běžné mezipaměti redis cache, sdílenou databázi systému SQL Server nebo vlastní implementaci ukládání do mezipaměti, která sdílí položek v mezipaměti ve webové farmě. Další informace naleznete v tématu <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Závislé součásti

Následující scénáře nevyžadují další konfiguraci, ale jsou závislé na technologie, které vyžadují konfiguraci pro farmy webových serverů.

| Scénář | Závisí na &hellip; |
| -------- | ------------------- |
| Ověřování | Ochrana dat (viz <xref:security/data-protection/configuration/overview>).<br><br>Další informace naleznete v tématu <xref:security/authentication/cookie> a <xref:security/cookie-sharing>. |
| Identita | Konfigurace ověřování a databáze.<br><br>Další informace naleznete v tématu <xref:security/authentication/identity>. |
| Relace | Ochrana dat (šifrované soubory cookie) (viz <xref:security/data-protection/configuration/overview>) a ukládání do mezipaměti (naleznete v tématu <xref:performance/caching/distributed>).<br><br>Další informace najdete v tématu [stav relace a aplikace: Stav relace](xref:fundamentals/app-state#session-state). |
| TempData | Ochranu dat (šifrované soubory cookie) (viz <xref:security/data-protection/configuration/overview>) nebo v jiné relaci (naleznete v tématu [stav relace a aplikace: Stav relace](xref:fundamentals/app-state#session-state)).<br><br>Další informace najdete v tématu [stav relace a aplikace: TempData](xref:fundamentals/app-state#tempdata). |
| Proti padělání | Ochrana dat (viz <xref:security/data-protection/configuration/overview>).<br><br>Další informace naleznete v tématu <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Řešení potíží

### <a name="data-protection-and-caching"></a>Ochrana dat a ukládání do mezipaměti

Pokud ochranu dat nebo ukládání do mezipaměti není konfigurován pro prostředí webové farmy, dojde k občasné chyby při zpracování žádosti. K tomu dochází, protože uzly není sdílí stejné prostředky a požadavky uživatelů nejsou vždy směrují zpět do stejného uzlu.

Vezměte v úvahu uživatel, který se přihlásí do aplikace ověřování souborem cookie. Uživatel se přihlásí aplikace na uzlu jedné webové farmy. Pokud své příští žádosti o dorazí na stejný uzel, ve kterém jsou přihlášeni, aplikace je schopná dešifrovat ověřovacího souboru cookie a umožňuje přístup ke prostředek vaší aplikace. Pokud své příští žádosti o přijetí na jiný uzel, aplikace nelze dešifrovat ověřovacího souboru cookie z uzlu, kde uživatel přihlášený a autorizaci pro požadovaný prostředek se nezdaří.

Když nastane některá z následujících příznaků **přerušovaně**, problém je obvykle trasovány nesprávné ochranu dat a konfiguraci ukládání do mezipaměti pro prostředí webové farmy:

* Ověřování konce &ndash; ověřovacího souboru cookie je chybně nakonfigurovaná nebo nelze dešifrovat. OAuth (Facebook, Microsoft, Twitter) nebo přihlášení OpenIdConnect nezdaří s chybou "Korelace se nepovedlo."
* Povolení konce &ndash; Identity se ztratí.
* Data se ztratí stav relace.
* Položky uložené v mezipaměti zmizí.
* TempData selže.
* Příspěvky selhání &ndash; selže kontrola proti padělání.

Další informace o konfiguraci ochrany dat pro webových farem, naleznete v tématu <xref:security/data-protection/configuration/overview>. Další informace o konfiguraci ukládání do mezipaměti pro nasazení webové farmy, naleznete v tématu <xref:performance/caching/distributed>.

## <a name="obtain-data-from-apps"></a>Získání dat z aplikací

Pokud webové farmy aplikace jsou schopné reagovat na požadavky, získáte žádost o připojení a další data z aplikací pomocí terminálu vložené middlewaru. Další informace a ukázky kódu najdete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.
