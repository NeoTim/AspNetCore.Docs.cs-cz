---
title: Přehled rozhraní API pro příjemce ASP.NET Core
author: rick-anderson
description: Získáte stručný přehled různých zákaznických rozhraní API dostupných v knihovně ASP.NET Core Data Protection Library.
ms.author: riande
ms.date: 06/11/2019
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
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 985c8cdc3518a51b9ec764407f4e2e3e5ff07e12
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021130"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Přehled rozhraní API pro příjemce ASP.NET Core

`IDataProtectionProvider`Rozhraní a `IDataProtector` jsou základní rozhraní, prostřednictvím kterých spotřebitelé používají systém ochrany dat. Nacházejí se v balíčku [Microsoft. AspNetCore. DataProtection. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) .

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

Rozhraní poskytovatele představuje kořen systému ochrany dat. Nedá se použít přímo k ochraně nebo neochraně dat. Místo toho musí příjemce získat odkaz na `IDataProtector` volání metody `IDataProtectionProvider.CreateProtector(purpose)` , kde účel je řetězec, který popisuje zamýšlený případ použití příjemce. Další informace o záměru tohoto parametru a o výběru příslušné hodnoty naleznete v tématu [řetězce pro účely](xref:security/data-protection/consumer-apis/purpose-strings) .

## <a name="idataprotector"></a>IDataProtector

Rozhraní ochrany je vráceno voláním metody `CreateProtector` a jedná se o rozhraní, které mohou uživatelé použít k provádění ochrany a zrušit ochranu operací.

Chcete-li chránit část dat, předejte data do `Protect` metody. Základní rozhraní definuje metodu, která převede Byte []-> Byte [], ale existuje také přetížení (poskytované jako metoda rozšíření), která převede řetězec řetězcové >. Zabezpečení nabízené těmito dvěma metodami je identické. vývojář by měl zvolit jakékoli přetížení, které je pro svůj případ použití nejpohodlnější. Bez ohledu na to, jakou přetížení jste zvolili, hodnota vrácená metodou Protect je teď chráněná (enciphered a odolná proti chybám) a aplikace ji může poslat nedůvěryhodnému klientovi.

Chcete-li zrušit ochranu dříve chráněných dat, předejte chráněná data do `Unprotect` metody. (Pro usnadnění přístupu pro vývojáře existují přetížení typu Byte [] a řetězce založené na řetězci.) Pokud byla chráněná datová část vygenerována starším voláním `Protect` na stejné `IDataProtector` , `Unprotect` Metoda vrátí původní nechráněnou datovou část. Pokud byla chráněná datová část úmyslně poškozena nebo byla vytvořena jinou `IDataProtector` `Unprotect` metodou, metoda vyvolá CryptographicException –.

Koncept stejného vs. různých vazeb se `IDataProtector` vrátí k konceptu účelu. Pokud `IDataProtector` byly vygenerovány dvě instance ze stejného kořenového adresáře `IDataProtectionProvider` , ale prostřednictvím různých řetězců s různými účely `IDataProtectionProvider.CreateProtector` , jsou považovány za [jiné ochrany](xref:security/data-protection/consumer-apis/purpose-strings)a nikdo nebude moci zrušit ochranu datových částí generovaných druhou.

## <a name="consuming-these-interfaces"></a>Využívání těchto rozhraní

Pro komponentu s podporou technologie DI je zamýšlené použití, že komponenta přebírá `IDataProtectionProvider` ve svém konstruktoru parametr a že systém di tuto službu automaticky poskytuje při vytváření instance komponenty.

> [!NOTE]
> Některé aplikace (například konzolové aplikace nebo aplikace ASP.NET 4. x) nemusí být náročné, takže nemohou použít mechanizmus popsané zde. V případě těchto scénářů se podívejte na dokument [scénáře nesouvisející s Neznámkou](xref:security/data-protection/configuration/non-di-scenarios) , kde najdete další informace o tom, jak získat instanci `IDataProtection` zprostředkovatele bez průchodu di.

Následující příklad znázorňuje tři koncepty:

1. [Přidejte systém ochrany dat](xref:security/data-protection/configuration/overview) do kontejneru služby,

2. Použití DI pro příjem instance `IDataProtectionProvider` a

3. Vytvoření `IDataProtector` z `IDataProtectionProvider` a jeho použití k ochraně a neochraně dat.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Balíček Microsoft. AspNetCore. DataProtection. abstrakce obsahuje metodu rozšíření `IServiceProvider.GetDataProtector` jako usnadnění pro vývojáře. Zapouzdřuje se jako jediná operace načítající `IDataProtectionProvider` od poskytovatele služby a volání `IDataProtectionProvider.CreateProtector` . Následující ukázka demonstruje použití.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instance `IDataProtectionProvider` a `IDataProtector` jsou bezpečné pro přístup z více vláken pro více volajících. Je určeno, že jakmile komponenta získá odkaz na `IDataProtector` volání `CreateProtector` , použije tento odkaz na více volání `Protect` a `Unprotect` . Volání `Unprotect` bude vyvolat CryptographicException –, pokud nelze ověřit nebo dešifrovat chráněnou datovou část. Některé součásti můžou během operace zrušení ochrany ignorovat chyby; komponenta, která čte ověřování cookie s, může tuto chybu zpracovat a považovat požadavek za neúspěchu v případě, že požadavek cookie neselže. Součásti, které chtějí toto chování by měly specificky zachytit CryptographicException – místo požití všech výjimek.
