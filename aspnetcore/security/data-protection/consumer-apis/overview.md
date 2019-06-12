---
title: Přehled rozhraní API příjemců pro ASP.NET Core
author: rick-anderson
description: Zobrazit stručný přehled různých uživatelů rozhraní API dostupná v rámci knihovny ochrany dat ASP.NET Core.
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: ff9badb55813cae0aa72d3a95dc53792332f109b
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837383"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Přehled rozhraní API příjemců pro ASP.NET Core

`IDataProtectionProvider` a `IDataProtector` základní rozhraní, pomocí kterých zákazníci používají systém ochrany dat jsou rozhraní. Nacházejí se v [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) balíčku.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

Rozhraní poskytovatele reprezentuje kořen dat ochrany systému. Jej nelze použít přímo ochrana nebo zrušení ochrany dat. Místo toho uživatel musí získat odkaz na `IDataProtector` voláním `IDataProtectionProvider.CreateProtector(purpose)`, kde účel je řetězec, který popisuje případ použití zamýšlený příjemce. Zobrazit [účelové řetězce](xref:security/data-protection/consumer-apis/purpose-strings) mnohem Další informace o záměru tento parametr a jak zvolit odpovídající hodnotu.

## <a name="idataprotector"></a>IDataProtector

Vrátí rozhraní ochrany pomocí volání `CreateProtector`a tato rozhraní, které uživatele můžete použít k provedení chránit nebo přestat operace.

K ochraně část dat, předejte data, která mají `Protect` metody. Základní rozhraní definuje metody, která převede byte [] -> byte [], ale je také přetížení (ve formě rozšiřující metoda), který převádí řetězec -> řetězec. Zabezpečení, které nabízí dvě metody je stejný jako; Vývojář by měl vybrat toho přetížení je nejvhodnější pro jejich případu použití. Bez ohledu na přetížení vybrán, hodnota vrácená chránit je teď chráněný (enciphered a zabezpečeny proti) a aplikaci můžete ho odeslat do nedůvěryhodného klienta.

Pokud chcete odemknout dřív chránila část dat, předejte chráněných dat na `Unprotect` metody. (Existují byte [] – na základě a založené na řetězci přetížení pro usnadnění práce vývojářů.) Pokud se chráněný datová část byla vygenerována dřívějším volání `Protect` na tento stejný `IDataProtector`, `Unprotect` metoda vrátí původní nechráněný datové části. Pokud chráněné datové části bylo manipulováno, nebo se vytvořil parametrem jiný `IDataProtector`, `Unprotect` vyvolá metoda výjimku cryptographicexception –.

Koncept stejné nebo jiné `IDataProtector` ties zpět na konceptu účel. Pokud dvě `IDataProtector` instance byly vygenerovány z stejným kořenem `IDataProtectionProvider` ale prostřednictvím různých účelové řetězce ve volání `IDataProtectionProvider.CreateProtector`, pak budou považovány za [různých chrániče](xref:security/data-protection/consumer-apis/purpose-strings), a jeden nebude možné zrušit ochranu druhý generované datové části.

## <a name="consuming-these-interfaces"></a>Použití těchto rozhraní

Pro komponentu s ohledem na DI zamýšlené použití je, že součást má `IDataProtectionProvider` parametr v konstruktoru a že DI poskytuje tento systém automaticky tato služba při vytváření instance komponenty.

> [!NOTE]
> Některé aplikace (například konzolové aplikace nebo aplikace ASP.NET 4.x) nemusí být DI s ohledem na tak nemůžete použít tento mechanismus je popsáno zde. Pro tyto scénáře najdete [scénáře bez DI](xref:security/data-protection/configuration/non-di-scenarios) dokument pro další informace o získání instance `IDataProtection` poskytovatele bez nutnosti kontaktovat DI.

Následující příklad ukazuje tři koncepty:

1. [Přidat systém ochrany dat](xref:security/data-protection/configuration/overview) ke kontejneru služby

2. Pomocí DI získat instanci `IDataProtectionProvider`, a

3. Vytvoření `IDataProtector` ze `IDataProtectionProvider` a jeho použití k ochraně a zrušení ochrany dat.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Balíček Microsoft.AspNetCore.DataProtection.Abstractions obsahuje metodu rozšíření `IServiceProvider.GetDataProtector` v zájmu usnadnění práce vývojářů. Zapouzdřuje jako jediná operace obou načítání `IDataProtectionProvider` od poskytovatele služeb a volání `IDataProtectionProvider.CreateProtector`. Následující příklad ukazuje jeho použití.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instance `IDataProtectionProvider` a `IDataProtector` jsou bezpečné pro vlákna pro více volání. Se předpokládá, který po získá odkaz na komponentu `IDataProtector` prostřednictvím volání `CreateProtector`, použije tento odkaz pro více volání `Protect` a `Unprotect`. Volání `Unprotect` cryptographicexception – vyvolá výjimku, pokud nelze ověřit nebo dešifrovat znalosti chráněné datové části. Některé součásti staví na Ignorovat chyby během odemknout operace komponenta, která načte ověřovací soubory cookie může zpracovat tuto chybu a zpracovávat žádosti, jako kdyby byla žádný soubor cookie vůbec spíše než nesplní žádost rovnou předplatit. Komponenty, které chcete toto chování musí konkrétně zachytit cryptographicexception – místo požití všechny výjimky.
