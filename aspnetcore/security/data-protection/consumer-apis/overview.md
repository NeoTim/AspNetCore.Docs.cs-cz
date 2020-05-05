---
title: Přehled rozhraní API pro příjemce ASP.NET Core
author: rick-anderson
description: Získáte stručný přehled různých zákaznických rozhraní API dostupných v knihovně ASP.NET Core Data Protection Library.
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: e840111d702882a45e59cf89d679b87fa537d833
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767854"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Přehled rozhraní API pro příjemce ASP.NET Core

Rozhraní `IDataProtectionProvider` a `IDataProtector` jsou základní rozhraní, prostřednictvím kterých spotřebitelé používají systém ochrany dat. Nacházejí se v balíčku [Microsoft. AspNetCore. DataProtection. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) .

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

Rozhraní poskytovatele představuje kořen systému ochrany dat. Nedá se použít přímo k ochraně nebo neochraně dat. Místo toho musí příjemce získat odkaz na `IDataProtector` volání metody `IDataProtectionProvider.CreateProtector(purpose)`, kde účel je řetězec, který popisuje zamýšlený případ použití příjemce. Další informace o záměru tohoto parametru a o výběru příslušné hodnoty naleznete v tématu [řetězce pro účely](xref:security/data-protection/consumer-apis/purpose-strings) .

## <a name="idataprotector"></a>IDataProtector

Rozhraní ochrany je vráceno voláním metody a `CreateProtector`jedná se o rozhraní, které mohou uživatelé použít k provádění ochrany a zrušit ochranu operací.

Chcete-li chránit část dat, předejte data do `Protect` metody. Základní rozhraní definuje metodu, která převede Byte []-> Byte [], ale existuje také přetížení (poskytované jako metoda rozšíření), která převede řetězec řetězcové >. Zabezpečení nabízené těmito dvěma metodami je identické. vývojář by měl zvolit jakékoli přetížení, které je pro svůj případ použití nejpohodlnější. Bez ohledu na to, jakou přetížení jste zvolili, hodnota vrácená metodou Protect je teď chráněná (enciphered a odolná proti chybám) a aplikace ji může poslat nedůvěryhodnému klientovi.

Chcete-li zrušit ochranu dříve chráněných dat, předejte chráněná data do `Unprotect` metody. (Pro usnadnění přístupu pro vývojáře existují přetížení typu Byte [] a řetězce založené na řetězci.) Pokud byla chráněná datová část vygenerována starším `Protect` voláním na `IDataProtector`stejné, `Unprotect` metoda vrátí původní nechráněnou datovou část. Pokud byla chráněná datová část úmyslně poškozena nebo byla vytvořena jinou `IDataProtector` `Unprotect` metodou, metoda vyvolá CryptographicException –.

Koncept stejného vs. různých `IDataProtector` vazeb se vrátí k konceptu účelu. Pokud byly `IDataProtector` vygenerovány dvě instance ze stejného `IDataProtectionProvider` kořenového adresáře `IDataProtectionProvider.CreateProtector`, ale prostřednictvím různých řetězců s různými účely, jsou považovány za [jiné ochrany](xref:security/data-protection/consumer-apis/purpose-strings)a nikdo nebude moci zrušit ochranu datových částí generovaných druhou.

## <a name="consuming-these-interfaces"></a>Využívání těchto rozhraní

Pro komponentu s podporou technologie DI je zamýšlené použití, že komponenta přebírá ve svém `IDataProtectionProvider` konstruktoru parametr a že systém di tuto službu automaticky poskytuje při vytváření instance komponenty.

> [!NOTE]
> Některé aplikace (například konzolové aplikace nebo aplikace ASP.NET 4. x) nemusí být náročné, takže nemohou použít mechanizmus popsané zde. V případě těchto scénářů se podívejte na dokument [scénáře nesouvisející s Neznámkou](xref:security/data-protection/configuration/non-di-scenarios) , kde najdete `IDataProtection` Další informace o tom, jak získat instanci zprostředkovatele bez průchodu di.

Následující příklad znázorňuje tři koncepty:

1. [Přidejte systém ochrany dat](xref:security/data-protection/configuration/overview) do kontejneru služby,

2. Použití DI pro příjem instance `IDataProtectionProvider`a

3. Vytvoření `IDataProtector` z `IDataProtectionProvider` a jeho použití k ochraně a neochraně dat.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Balíček Microsoft. AspNetCore. DataProtection. abstrakce obsahuje metodu `IServiceProvider.GetDataProtector` rozšíření jako usnadnění pro vývojáře. Zapouzdřuje se jako jediná operace načítající `IDataProtectionProvider` od poskytovatele služby a volání `IDataProtectionProvider.CreateProtector`. Následující ukázka demonstruje použití.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instance `IDataProtectionProvider` a `IDataProtector` jsou bezpečné pro přístup z více vláken pro více volajících. Je určeno, že jakmile komponenta získá odkaz `IDataProtector` na volání `CreateProtector`, použije tento odkaz na více volání `Protect` a. `Unprotect` Volání `Unprotect` bude vyvolat CryptographicException –, pokud nelze ověřit nebo dešifrovat chráněnou datovou část. Některé součásti můžou během operace zrušení ochrany ignorovat chyby; komponenta, která čte soubory cookie ověřování, může tuto chybu zpracovat a považovat požadavek za neúspěšného uložení souboru cookie. Součásti, které chtějí toto chování by měly specificky zachytit CryptographicException – místo požití všech výjimek.
