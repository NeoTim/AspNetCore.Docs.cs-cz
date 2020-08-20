---
title: Ochrana dat ASP.NET Core
author: rick-anderson
description: Přečtěte si o konceptu ochrany dat a zásadách návrhu rozhraní API ochrany ASP.NET Core dat.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/introduction
ms.openlocfilehash: 4f578e30a972b0d4ce5db08b2ec844e270c11406
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630078"
---
# <a name="aspnet-core-data-protection"></a>Ochrana dat ASP.NET Core

Webové aplikace často potřebují ukládat data citlivá na zabezpečení. Systém Windows poskytuje rozhraní DPAPI pro aplikace klasické pracovní plochy, ale není vhodné pro webové aplikace. Sada ASP.NET Core Data Protection Stack nabízí jednoduché a snadno použitelné kryptografické rozhraní API, které může vývojář použít k ochraně dat, včetně správy klíčů a rotace.

Sada ASP.NET Core Data Protection Stack je navržena tak, aby sloužila jako dlouhodobá náhrada &lt; &gt; elementu machineKey v ASP.NET 1. x-4. x. Byl navržen tak, aby využíval mnoho nedostatků starého kryptografického zásobníku a zároveň poskytovalo předem připravené řešení pro většinu případů použití moderních aplikací, které se mohou vyskytnout.

## <a name="problem-statement"></a>Popis problému

Celkový příkaz problému může být stručně uveden v jedné větě: Potřebuji zachovat důvěryhodné informace pro pozdější načtení, ale nedůvěřujete tak mechanismu trvalosti. Ve webových případech to může být zapsáno jako "Potřebuji k výměně důvěryhodného stavu prostřednictvím nedůvěryhodného klienta".

Kanonický příklad představuje ověřování cookie nebo nosný token. Server vygeneruje token "jsem Groot a má oprávnění xyz" a předá ho klientovi. V některých budoucích kalendářních verzích klienta předloží token zpátky na server, ale server potřebuje určitý druh záruky, že klient nezfalšovaný token. Proto první požadavek: pravost (označuje se také jako integrita, kontrola proti falšování).

Vzhledem k tomu, že trvalý stav je důvěryhodný pro server, předpokládáme, že tento stav může obsahovat informace, které jsou specifické pro operační prostředí. Může to být ve formě cesty k souboru, oprávnění, popisovače nebo jiného nepřímých odkazů nebo některých jiných dat specifických pro server. Tyto informace by obecně neměly být zveřejněné nedůvěryhodnému klientovi. Proto druhý požadavek: důvěrnost.

Nakonec vzhledem k tomu, že moderní aplikace jsou součástí komponenty, k čemu jsme zjistili, že jednotlivé komponenty budou chtít využít výhod tohoto systému bez ohledu na jiné součásti systému. Například pokud komponenta nosného tokenu používá tento zásobník, měla by fungovat bez rušivého mechanismu CSRF, který může také používat stejný zásobník. Proto konečný požadavek: izolace.

Abychom mohli zúžit rozsah našich požadavků, můžeme vám poskytnout další omezení. Předpokládáme, že všechny služby, které fungují v rámci cryptosystem, jsou stejně důvěryhodné a že je nemusíte vygenerovat ani spotřebovat mimo služby v rámci našeho přímého řízení. Kromě toho vyžadujeme, aby operace byly co nejrychlejší, protože každý požadavek na webovou službu může projít cryptosystem jednou nebo víckrát. To usnadňuje symetrickou kryptografii pro náš scénář a můžeme vyzvýhodněnit asymetrické šifrování až do doby, kdy je potřeba.

## <a name="design-philosophy"></a>Filozofie návrhu

Zahájili jsme identifikaci problémů s existujícím zásobníkem. Až to máme, provedli jsme šetření na šířku stávajících řešení a uzavřeli jsme si, že žádné řešení ještě neobsahovalo možnosti, které jsme si vyžádali. Pak jsme navrhli řešení na základě několika principů pro GUID.

* Systém by měl nabízet jednoduchost konfigurace. V ideálním případě by měl být systém nulový – konfigurace a vývojáři by mohli dosáhnout provozu. V situacích, kdy vývojáři potřebují nakonfigurovat konkrétní aspekt (například úložiště klíčů), je třeba zvážit, že tyto konkrétní konfigurace budou jednoduché.

* Nabízí jednoduché rozhraní API zaměřené na uživatele. Rozhraní API by se mělo snadno používat správně a obtížné ho používat nesprávně.

* Vývojáři se nedozvěděli o zásadách správy klíčů. Systém by měl zpracovat výběr algoritmu a životnost klíče jménem vývojáře. V ideálním případě by vývojář neměl nikdy mít přístup k nezpracovanému materiálu klíče.

* Pokud je to možné, měly by se klíče chránit v klidovém umístění. Systém by měl zjistit vhodný výchozí mechanismus ochrany a použít ho automaticky.

Díky těmto principům jsme vyvinuli jednoduchý a [snadno použitelný](xref:security/data-protection/using-data-protection) zásobník ochrany dat.

Rozhraní API pro ochranu ASP.NET Core dat nejsou primárně určena pro neomezenou perzistenci důvěrných datových částí. Další technologie, jako je [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) a [Azure Rights Management](/rights-management/) , jsou vhodnější pro scénář neurčitého úložiště a mají odpovídající možnosti silné správy klíčů. V takovém případě nebrání vývojářům v používání rozhraní API ochrany ASP.NET Core dat pro dlouhodobou ochranu důvěrných dat.

## <a name="audience"></a>Cílová skupina

Systém ochrany dat je rozdělen na pět hlavních balíčků. Různé aspekty těchto rozhraní API cílí na tři hlavní cílové skupiny;

1. [Přehled rozhraní API pro uživatele](xref:security/data-protection/consumer-apis/overview) cílí na vývojáře aplikací a platforem.

   "Nechci se dozvědět, jak zásobník funguje, nebo o tom, jak je nakonfigurované. Můžu jednoduše provést určitou operaci způsobem, jak je možné, s vysokou pravděpodobností používání rozhraní API úspěšně. "

2. [Rozhraní API pro konfiguraci](xref:security/data-protection/configuration/overview) vývojářů cílové aplikace a správce systému.

   "Potřebuji sdělit systém ochrany dat, že moje prostředí vyžaduje jiné než výchozí cesty nebo nastavení."

3. Rozhraní API pro rozšiřitelnost cílí vývojářům při implementaci vlastních zásad. Použití těchto rozhraní API by se mělo omezit na výjimečné situace a zkušenosti, a to pro vývojáře v zabezpečení.

   "Musím v systému nahradit celou součást, protože mám skutečně jedinečné požadavky na chování. Beru na vyznámení s neobvyklými využitími plochy rozhraní API, aby bylo možné vytvořit modul plug-in, který splňuje požadavky. "

## <a name="package-layout"></a>Rozložení balíčku

Zásobník ochrany dat se skládá z pěti balíčků.

* [Microsoft. AspNetCore. DataProtection. abstrakce](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) obsahují <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> rozhraní a <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> pro vytváření služeb ochrany dat. Obsahuje také užitečné metody rozšíření pro práci s těmito typy (například [IDataProtector. Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). Pokud je systém ochrany dat vytvořen jinde a používáte rozhraní API, reference `Microsoft.AspNetCore.DataProtection.Abstractions` .

* [Microsoft. AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) obsahuje základní implementaci systému ochrany dat, včetně základních kryptografických operací, správy klíčů, konfigurace a rozšiřitelnosti. Chcete-li vytvořit instanci systému ochrany dat (například přidat ho do objektu <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ) nebo upravit nebo rozšířit jeho chování, odkaz `Microsoft.AspNetCore.DataProtection` .

* [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) obsahuje další rozhraní API, která vývojáři můžou najít užitečnou, ale nepatří do balíčku Core. Například tento balíček obsahuje metody výrobního rozhraní pro vytvoření instance systému ochrany dat pro ukládání klíčů do umístění v systému souborů bez vkládání závislostí (viz <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> ). Obsahuje také metody rozšíření pro omezení životnosti chráněných datových částí (viz <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector> ).

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) můžete nainstalovat do existující aplikace ASP.NET 4. x, která přesměruje své `<machineKey>` operace na použití nového zásobníku ASP.NET Core ochrany dat. Další informace naleznete v tématu <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Odvození od společnosti Microsoft. AspNetCore. Cryptography. devozuje](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) poskytuje implementaci rutiny hash hesla PBKDF2 a může být používána systémy, které musí bezpečně zpracovávat hesla uživatelů. Další informace naleznete v tématu <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Další materiály

<xref:host-and-deploy/web-farm>
