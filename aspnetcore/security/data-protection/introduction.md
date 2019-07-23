---
title: Ochrana dat ASP.NET Core
author: rick-anderson
description: Další informace o konceptu ochranu dat a principy návrhu rozhraní API ASP.NET Core Data Protection.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/introduction
ms.openlocfilehash: 37f170a3e8a46ef2215b0999358d46dd402636df
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903279"
---
# <a name="aspnet-core-data-protection"></a>Ochrana dat ASP.NET Core

Webové aplikace často potřebují ukládat citlivá data. Windows poskytuje rozhraní DPAPI pro desktopové aplikace, ale to není vhodná pro webové aplikace. Zásobník ochrany dat ASP.NET Core poskytují jednoduché a snadno se používá API kryptografické vývojáři mohou využít k ochraně dat, včetně správu a rotaci klíčů.

Zásobník ochrany dat ASP.NET Core je navržená sloužící jako dlouhodobé náhrada za &lt;machineKey&gt; element v technologii ASP.NET 1.x – 4.x. Bylo navrženo řeší řadu nedostatků starý kryptografický zásobníku současně jako out-of-the-box řešení pro většinu případů použití, které jsou pravděpodobně dojde k moderní aplikace.

## <a name="problem-statement"></a>Popis problému

Příkaz celkový problém, můžete v jedné větě stručně uvedeno: Je potřeba zachovat důvěryhodné informace pro pozdější načtení, ale není důvěřovat mechanismus trvalosti. V podmínkách web to může být napsán jako "Potřebuji round-trip důvěryhodného stavu pomocí nedůvěryhodného klienta."

Canonical příkladem tohoto je soubor cookie pro ověřování nebo nosný token. Generuje server "Jsem Groot a mít oprávnění xyz" token a předá ho do klienta. V některé budoucí datum nabídne klientovi tento token zpět na server, ale je nutné nějaký druh ujištění, že klient nebyl založených na zfalšovaných token. Proto první požadavek: pravosti (označovaný také jako integritu, kontroly pravopisu proti).

Protože trvalého stavu je důvěryhodný pro server, Očekáváme, že tento stav může obsahovat informace, které jsou specifické pro provozní prostředí. To může být ve tvaru cestu k souboru, oprávnění, popisovač nebo jiných nepřímý odkaz nebo některé jiné části data specifická pro server. Tyto informace obecně by neměl být poskytnuty nedůvěryhodného klienta. Proto druhý požadavek: utajení.

Nakonec od moderních aplikací jsou komponentní, co jsme viděli je, že jednotlivé komponenty se chcete využít tento systém bez ohledu na ostatní součásti v systému. Například pokud komponenta tokenu nosiče používá tento zásobník, měla pracovat bez rušení anti-CSRF mechanismus, které by mohly používat se stejným zásobníkem. Proto poslední požadavek: izolace.

Abyste mohli zúžit rozsah, naše požadavky můžete zajišťuje další omezení. Předpokládáme, že jsou všechny služby, které pracují v rámci cryptosystem stejně důvěryhodné a že data nemusí generovat ani spotřebované mimo tyto služby a za naše přímou kontrolu. Kromě toho požadujeme, aby operace jsou co nejrychleji, protože každý požadavek na webovou službu projít cryptosystem jednou nebo vícekrát. Díky tomu symetrické šifrování ideální pro náš scénář a asymetrické šifrování jsme získat slevu až do takové čas, který je nezbytný.

## <a name="design-philosophy"></a>Filosofie návrhu tříd

Začali jsme díky identifikaci problémů s existující zásobníku. Jakmile jsme měli, který jsme názory na šířku existující řešení a dospělo k závěru, že žádné existující řešení úplně měli možností, které jsme chtěli. Potom jsme navržené řešení založené na několika hlavní principy.

* Systém by měl nabídnout zjednodušení konfigurace. V ideálním případě by být systém bez nutnosti konfigurace a vývojáři můžou pusťte se do práce. V situacích, kdy je potřeba nakonfigurovat konkrétní aspekty (jako jsou klíče úložiště) vývojáři by měl zvážit tyto konkrétní konfigurace maximálně jednoduché.

* Nabízí jednoduché rozhraní API určené uživatelům. Rozhraní API by měla být snadno použitelné správně a obtížně použitelnou nesprávně.

* Principy správy klíčů by neměl informace pro vývojáře. Systém by měl zpracovat algoritmus výběru a životnosti klíče jménem pro vývojáře. Vývojář v ideálním případě měli mít nikdy i přístup k nezpracované materiál klíče.

* Klíče by měly být chráněné v klidovém stavu, pokud je to možné. Systém by měl zjistit mechanismus odpovídající výchozí ochranu a použijte ji automaticky.

Pomocí těchto zásad v úvahu Vyvinuli jsme proto představují jednoduchou, [snadno použitelné](xref:security/data-protection/using-data-protection) data protection zásobníku.

Ochranu dat ASP.NET Core API nejsou určené především pro neomezenou trvalost důvěrné datových částí. Jiné technologie, jako je [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) a [Azure Rights Management](/rights-management/) jsou vhodnější pro scénář neomezené úložiště, a mají možnosti odpovídajícím způsobem silné správu klíčů. Ale nutné dodat, není nic zakazují vývojář pro dlouhodobou ochranu důvěrných dat pomocí data protection API ASP.NET Core.

## <a name="audience"></a>Cílová skupina

Systém ochrany dat je rozdělena do pěti hlavní balíčky. Tři hlavní oslovovat; cílové různé aspekty těchto rozhraní API

1. [Přehled rozhraní API příjemců](xref:security/data-protection/consumer-apis/overview) cílit na vývojáři aplikace a rozhraní framework.

   "Nechci se nyní Další informace o tom, jak funguje zásobníku nebo jeho konfiguraci. Jednoduše chci provést některé operace v jednoduché způsobem nejvíce s velkou pravděpodobností používání rozhraní API úspěšně."

2. [Rozhraní API pro konfiguraci](xref:security/data-protection/configuration/overview) cílit na vývojáře aplikací a správce systému.

   "Potřebuji systém ochrany dat říct, že Moje prostředí vyžaduje jiné než výchozí cesty nebo nastavení."

3. Rozšiřitelnost vývojáři cílové rozhraní API starosti implementace vlastních zásad. Použití těchto rozhraní API by omezena na výjimečné situace a zkušenosti vývojáře používající zabezpečení.

   "Potřebuji nahradit celý komponenty v rámci systému, protože mám stane skutečně jedinečná chování požadavky. Souhlasím s posíláním další uncommonly použít části plochy rozhraní API k vytvoření modulu plug-in, který splňuje mé požadavky."

## <a name="package-layout"></a>Rozložení balíčku

Zásobník ochrany dat se skládá z pěti balíčky.

* [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) obsahuje <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> a <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rozhraní služby ochrany dat vytvořit. Obsahuje také užitečné rozšiřující metody pro práci s těmito typy (například [IDataProtector.Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). Pokud systém ochrany dat je vytvořena instance jinde a budete používat rozhraní API, odkazovat `Microsoft.AspNetCore.DataProtection.Abstractions`.

* [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) obsahuje základní implementaci systému ochrany dat, včetně základních kryptografických operací, správu klíčů, konfigurace a rozšíření. K vytvoření instance systému ochrany dat (například jejím přidáním na <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>) nebo úpravě nebo rozšiřování své chování, odkazovat `Microsoft.AspNetCore.DataProtection`.

* [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) obsahuje další rozhraní API vývojáři můžou být užitečné, ale které nepatří do balíčku core. Například tento balíček obsahuje metody pro vytváření objektů pro vytvoření instance systému ochrany dat pro ukládání klíčů do umístění v systému souborů bez injektáž závislostí (viz <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>). Také obsahuje rozšiřující metody pro omezení životnosti chráněných datových částí (viz <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector>).

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) lze nainstalovat do stávající aplikace ASP.NET 4.x přesměrovat její `<machineKey>` operace používat nový zásobník ochrany dat ASP.NET Core. Další informace naleznete v tématu <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Microsoft.AspNetCore.Cryptography.KeyDerivation](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) poskytuje implementaci pro hashování rutina PBKDF2 hesel a mohou být využívána systémy, které musí heslo uživatele zpracovávají bezpečně. Další informace naleznete v tématu <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Další zdroje

<xref:host-and-deploy/web-farm>
