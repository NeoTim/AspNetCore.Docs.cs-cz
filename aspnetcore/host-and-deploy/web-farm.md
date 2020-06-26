---
title: Hostitelská ASP.NET Core ve webové farmě
author: rick-anderson
description: Naučte se hostovat více instancí ASP.NET Core aplikace se sdílenými prostředky v prostředí webové farmy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/web-farm
ms.openlocfilehash: 13c4a8e287e4b62a1429f67fbe83ff5b0dc65f52
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408276"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Hostitelská ASP.NET Core ve webové farmě

Autor – [Chris Rossův](https://github.com/Tratcher)

*Webová farma* je skupina dvou nebo více webových serverů (nebo *uzlů*), které hostují více instancí aplikace. Když se požadavky od uživatelů dorazí na webovou farmu, *Nástroj pro vyrovnávání zatížení* distribuuje požadavky do uzlů webové farmy. Vylepšení webových farem:

* **Spolehlivost/dostupnost**: Pokud selže jeden nebo více uzlů, může nástroj pro vyrovnávání zatížení směrovat požadavky do jiných funkčních uzlů, aby bylo možné pokračovat ve zpracování požadavků.
* **Kapacita/výkon**: více uzlů může zpracovávat více požadavků než jednoho serveru. Nástroj pro vyrovnávání zatížení vyrovnává zatížení úlohou tím, že distribuuje požadavky na uzly.
* **Škálovatelnost**: Pokud je potřeba víc nebo méně kapacity, můžete počet aktivních uzlů zvýšit nebo snížit tak, aby odpovídaly zatížení. Technologie webové farmy, například [Azure App Service](https://azure.microsoft.com/services/app-service/), mohou automaticky přidávat nebo odebírat uzly na žádost správce systému nebo automaticky bez zásahu člověka.
* **Udržovatelnost**: uzly webové farmy můžou spoléhat na sadu sdílených služeb, což má za následek snazší správu systému. Uzly webové farmy můžou například spoléhat na jeden databázový server a na běžné síťové umístění pro statické prostředky, jako jsou obrázky a soubory ke stažení.

Toto téma popisuje konfiguraci a závislosti pro aplikace ASP.NET Core hostované ve webové farmě, která je závislá na sdílených prostředcích.

## <a name="general-configuration"></a>Obecná konfigurace

<xref:host-and-deploy/index>  
Naučte se nastavit hostující prostředí a nasazovat aplikace ASP.NET Core. Nakonfigurujte správce procesů na každém uzlu webové farmy, aby se spouštěla aplikace a automaticky se restartuje. Každý uzel vyžaduje modul runtime ASP.NET Core. Další informace najdete v tématech v oblasti [hostitele a nasazení](xref:host-and-deploy/index) v dokumentaci.

<xref:host-and-deploy/proxy-load-balancer>  
Přečtěte si o konfiguraci pro aplikace hostované za servery proxy a nástroje pro vyrovnávání zatížení, které často překrývají důležité informace o žádostech.

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/) je [platforma cloud computingu od Microsoftu](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core. App Service je plně spravovaná platforma, která poskytuje automatické škálování, Vyrovnávání zatížení, opravy a průběžné nasazování.

## <a name="app-data"></a>Data aplikací

Když se aplikace škáluje na více instancí, může existovat stav aplikace, který vyžaduje sdílení napříč uzly. Pokud je stav přechodný, zvažte sdílení [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Pokud sdílený stav vyžaduje trvalost, zvažte uložení sdíleného stavu do databáze.

## <a name="required-configuration"></a>Požadovaná konfigurace

Ochrana dat a ukládání do mezipaměti vyžaduje konfiguraci pro aplikace nasazené do webové farmy.

### <a name="data-protection"></a>Ochrana dat

[ASP.NET Core systém ochrany dat](xref:security/data-protection/introduction) používá aplikace k ochraně dat. Ochrana dat se spoléhá na sadu kryptografických klíčů uložených v rámci *klíčového prstence*. Po inicializaci systému ochrany dat se použije [výchozí nastavení](xref:security/data-protection/configuration/default-settings) , které ukládá klíčová kroužková data místně. V rámci výchozí konfigurace je jedinečný klíč Ring uložený na každém uzlu webové farmy. V důsledku toho každý uzel webové farmy nemůže dešifrovat data zašifrovaná aplikací v jakémkoli jiném uzlu. Výchozí konfigurace není obecně vhodná pro hostování aplikací ve webové farmě. Alternativou k implementaci sdíleného klíčového prstence je vždycky směrovat požadavky uživatelů na stejný uzel. Další informace o konfiguraci systému ochrany dat pro nasazení webové farmy najdete v tématu <xref:security/data-protection/configuration/overview> .

### <a name="caching"></a>Ukládání do mezipaměti

V prostředí webové farmy musí mechanismus ukládání do mezipaměti sdílet položky v mezipaměti napříč uzly webové farmy. Ukládání do mezipaměti musí buď spoléhat na běžnou mezipaměť Redis, sdílenou databázi SQL Server, nebo vlastní implementaci ukládání do mezipaměti, která sdílí položky v mezipaměti napříč webovou farmou. Další informace naleznete v tématu <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Závislé součásti

Následující scénáře nevyžadují další konfiguraci, ale závisejí na technologiích, které vyžadují konfiguraci webových farem.

| Scénář | Závisí na&hellip; |
| -------- | ------------------- |
| Ověřování | Ochrana dat (viz <xref:security/data-protection/configuration/overview> ).<br><br>Další informace naleznete v tématech <xref:security/authentication/cookie> a <xref:security/cookie-sharing>. |
| Identity | Ověřování a konfigurace databáze.<br><br>Další informace naleznete v tématu <xref:security/authentication/identity>. |
| Relace | Ochrana dat (šifrované soubory cookie) (viz <xref:security/data-protection/configuration/overview> ) a ukládání do mezipaměti (viz <xref:performance/caching/distributed> ).<br><br>Další informace najdete v tématu [Správa stavu a relace: stav relace](xref:fundamentals/app-state#session-state). |
| TempData | Ochrana dat (šifrované soubory cookie) (viz <xref:security/data-protection/configuration/overview> ) nebo relace (viz [Správa relace a stav: stav relace](xref:fundamentals/app-state#session-state)).<br><br>Další informace najdete v tématu [Správa stavu a relace: TempData](xref:fundamentals/app-state#tempdata). |
| Ochrana proti padělání | Ochrana dat (viz <xref:security/data-protection/configuration/overview> ).<br><br>Další informace naleznete v tématu <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Řešení potíží

### <a name="data-protection-and-caching"></a>Ochrana dat a ukládání do mezipaměti

Pokud není pro prostředí webové farmy nakonfigurovaná ochrana dat nebo ukládání do mezipaměti, dojde při zpracování požadavků k přerušované chybě. K tomu dochází, protože uzly nesdílejí stejné prostředky a požadavky uživatelů nejsou vždy směrovány zpět do stejného uzlu.

Vezměte v úvahu uživatele, který se přihlásí do aplikace pomocí ověřování souborem cookie. Uživatel se přihlásí do aplikace na jednom uzlu webové farmy. Pokud bude další požadavek doručen do stejného uzlu, ve kterém se přihlásil, aplikace dokáže dešifrovat soubor cookie ověřování a povolit přístup k prostředku aplikace. Pokud jejich další požadavek dorazí na jiný uzel, aplikace nemůže dešifrovat ověřovací soubor cookie z uzlu, ve kterém se uživatel přihlásil, a autorizace požadovaného prostředku se nezdařila.

Pokud dojde k **občasnému**výskytu některého z následujících příznaků, je obvykle zaznamenána nesprávná ochrana dat nebo konfigurace ukládání do mezipaměti pro prostředí webové farmy:

* Přerušení ověřování: ověřovací soubor cookie je nesprávně nakonfigurovaný nebo ho nejde dešifrovat. OAuth (Facebook, Microsoft, Twitter) nebo OpenIdConnect přihlášení selžou s chybou korelace se nezdařila.
* Přerušení autorizace: dojde Identity ke ztrátě.
* Stav relace ztratí data.
* Položky uložené v mezipaměti zmizí.
* TempData se nezdařila.
* Příspěvky selžou: kontrolu proti padělání se nezdaří.

Další informace o konfiguraci ochrany dat pro nasazení webové farmy najdete v tématu <xref:security/data-protection/configuration/overview> . Další informace o konfiguraci ukládání do mezipaměti pro nasazení webové farmy najdete v tématu <xref:performance/caching/distributed> .

## <a name="obtain-data-from-apps"></a>Získání dat z aplikací

Pokud webové serverové farmy můžou reagovat na požadavky, získávat žádosti, připojení a další data z aplikací pomocí vloženého middlewaru terminálu. Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app> .

## <a name="additional-resources"></a>Další zdroje

* [Rozšíření vlastních skriptů pro Windows](/azure/virtual-machines/extensions/custom-script-windows): stáhne a spustí skripty na virtuálních počítačích Azure, které jsou užitečné pro konfiguraci po nasazení a instalaci softwaru.
* <xref:host-and-deploy/proxy-load-balancer>
 