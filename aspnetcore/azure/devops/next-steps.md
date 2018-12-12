---
title: Další kroky – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Další postupy výuky pro vývoj a provoz s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284458"
---
# <a name="next-steps"></a>Další kroky

V tomto průvodci vytvořit kanál DevOps pro ukázkové aplikace ASP.NET Core. Blahopřejeme! Doufáme, že se vám líbilo učení k publikování webových aplikací ASP.NET Core do služby Azure App Service a automatizovat průběžné integrace změny.

Kromě hostování webů a DevOps Azure nabízí širokou škálu služeb Platform-as-a-Service (PaaS) je užitečný pro vývojáře ASP.NET Core. Tato část poskytuje stručný přehled o některé z nejčastěji používaných služeb.

## <a name="storage-and-databases"></a>Úložiště a databází

[Redis Cache](/azure/redis-cache/) je vysokou propustností a nízkou latencí dat, ukládání do mezipaměti k dispozici jako službu. Je možné pro ukládání výstupu stránek, snížení požadavků na databázi a poskytování stavu relace ASP.NET Core do několika instancí aplikace.

[Azure Storage](/azure/storage/) je široce škálovatelné cloudové úložiště Azure. Vývojáři můžou využít výhod [Queue Storage](/azure/storage/queues/storage-queues-introduction) spolehlivé front a [Table Storage](/azure/storage/tables/table-storage-overview) je úložiště dvojic klíč hodnota NoSQL navržená pro rychlý vývoj pomocí rozsáhlé, částečně strukturovaných datových sad.

[Azure SQL Database](/azure/sql-database/) poskytuje funkce známé relační databáze jako služby pomocí stroje Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) globálně distribuovaná a vícemodelová databázová služba NoSQL. Několik rozhraní API jsou dostupná, včetně rozhraní SQL API (dříve se označovaly jako DocumentDB) a Cassandra, MongoDB.

## <a name="identity"></a>Identita

[Azure Active Directory](/azure/active-directory/) a [Azure Active Directory B2C](/azure/active-directory-b2c/) jsou obě služby identit. Azure Active Directory je určená pro podnikové scénáře a umožňuje spolupráci Azure AD B2B (business-to-business), zatímco Azure Active Directory B2C je zamýšlené firmy zákazníka scénářů, včetně přihlášení sociálních sítí.

## <a name="mobile"></a>Mobilní zařízení

[Notification Hubs](/azure/notification-hubs/) je modul pro multiplatformní a škálovatelnou nabízených oznámení k rychlému rozesílání milionů zpráv aplikacím spuštěným na různých typech zařízení.

## <a name="web-infrastructure"></a>Infrastruktura webové

[Služba Azure Container Service](/azure/aks/) spravuje vaše hostované prostředí Kubernetes, tak rychle a snadno nasazovat a spravovat kontejnerizované aplikace bez znalosti Orchestrace kontejnerů.

[Služba Azure Search](/azure/search/) slouží k vytvoření podniková řešení pro hledání nad privátním heterogenním obsahem.

[Service Fabric](/azure/service-fabric/) je platforma distribuovaných systémů, která usnadňuje balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů.
