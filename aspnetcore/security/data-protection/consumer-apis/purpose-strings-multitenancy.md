---
title: Hierarchie účelů a víceklientská architektura v ASP.NET Core
author: rick-anderson
description: Získejte informace o řetězec hierarchie účelů a víceklientská architektura jako má vztah k rozhraní API ASP.NET Core Data Protection.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 1133d40e7b325d58b3f70e7387494dae36ff8ac9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902739"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Hierarchie účelů a víceklientská architektura v ASP.NET Core

Protože `IDataProtector` je také implicitně `IDataProtectionProvider`, účely je možné zřetězit. V tomto smyslu `provider.CreateProtector([ "purpose1", "purpose2" ])` je ekvivalentní `provider.CreateProtector("purpose1").CreateProtector("purpose2")`.

To umožňuje některé zajímavé hierarchických vztahů prostřednictvím systému ochrany dat. V předchozím příkladu z [Contoso.Messaging.SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose), komponenta SecureMessage můžete volat `provider.CreateProtector("Contoso.Messaging.SecureMessage")` jednou počáteční a uložte do mezipaměti výsledek do privátní `_myProvider` pole. Budoucí ochrany mohou být vytvořeny prostřednictvím volání do `_myProvider.CreateProtector("User: username")`, a tyto ochrany by se použít k zabezpečení jednotlivých zpráv.

To také může být obráceně. Vezměte v úvahu jednu logickou aplikaci více tenantů (systém správy obsahu zdá se, že přiměřené) a každý klient může mít nakonfigurovanou vlastní ověřování a stav systému pro správu hostitele. Zastřešující aplikace má hlavní jednoho zprostředkovatele a volá `provider.CreateProtector("Tenant 1")` a `provider.CreateProtector("Tenant 2")` poskytnout vlastní izolované řez systému ochrany dat každého tenanta. Klienti pak může odvodit vlastní jednotlivých ochrany podle vlastních potřeb, ale bez ohledu na to, jak usilovně se snaží nelze vytvářet ochrany, které kolidují s žádným jiným klientem v systému. Graficky je reprezentováno níže.

![Účely více tenantů](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Předpokladem zastřešující řízení aplikací, které rozhraní API jsou dostupná pro jednotlivé tenanty a že klienti nelze spustit libovolný kód na serveru. Pokud tenanta může spustit libovolný kód, může provádějí privátní reflexe pro přerušení záruky izolace, nebo může jen číst hlavní klíčový materiál přímo a odvodit libovolné podklíče přejí.

Systém ochrany dat ve skutečnosti používá řazení více tenantů v její výchozí konfiguraci out-of-the-box. Ve výchozím nastavení je hlavní klíčový materiál uložen ve složce profilu uživatele účet pracovního procesu (nebo registru pro identity fondu aplikací služby IIS). Ale je ve skutečnosti poměrně běžných jeden účet použít ke spuštění více aplikací, a tedy tyto aplikace skončili byste hlavního klíče materiál pro sdílení obsahu. Tento problém vyřešit, systém ochrany dat automaticky vloží jedinečný každou aplikaci identifikátor jako první prvek v řetězci celkové účel. Toto implicitní účelu slouží k [izolaci jednotlivých aplikací](xref:security/data-protection/configuration/overview#per-application-isolation) vzájemně efektivně zpracováním každou aplikaci jako jedinečný tenanta v rámci systému a proces vytvoření ochrany pomocí vypadá stejné jako na obrázku výše.
