---
title: Další kroky – DevOps s ASP.NET core a Azure
author: CamSoper
description: Další výukové trasy pro DevOps s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659472"
---
# <a name="next-steps"></a>Další kroky

V této příručce jste vytvořili kanál DevOps pro ukázkovou aplikaci ASP.NET Core. Blahopřejeme! Doufáme, že se vám líbilo publikovat ASP.NET webových aplikací Core do služby Azure App Service a automatizovat průběžnou integraci změn.

Kromě web hostingu a DevOps má Azure širokou škálu služeb PaaS (Platform-as-a-Service), které jsou užitečné pro ASP.NET vývojářům Core. Tato část poskytuje stručný přehled některých nejčastěji používaných služeb.

## <a name="storage-and-databases"></a>Úložiště a databáze

[Redis Cache](/azure/redis-cache/) je ukládání dat s vysokou propustností a nízkou latencí, které je k dispozici jako služba. Lze jej použít pro ukládání výstupu stránky do mezipaměti, snížení požadavků na databázi a poskytování ASP.NET stavu základní relace napříč více instancemi aplikace.

[Azure Storage](/azure/storage/) je masivně škálovatelné cloudové úložiště Azure. Vývojáři mohou využít [služby Queue Storage](/azure/storage/queues/storage-queues-introduction) pro spolehlivé řízení front zpráv a [table storage](/azure/storage/tables/table-storage-overview) je úložiště hodnot klíčů NoSQL určené pro rychlý vývoj pomocí masivních polostrukturovaných datových sad.

[Azure SQL Database](/azure/sql-database/) poskytuje známé funkce relační databáze jako služba pomocí Microsoft SQL Server Engine.

[Cosmos DB](/azure/cosmos-db/) globálně distribuovaná databázová služba NoSQL s více modely. K dispozici je více rozhraní API, včetně rozhraní SQL API (dříve nazývané DocumentDB), Cassandra a MongoDB.

## <a name="identity"></a>Identita

[Azure Active Directory](/azure/active-directory/) a [Azure Active Directory B2C](/azure/active-directory-b2c/) jsou obě služby identity. Azure Active Directory je navržený pro podnikové scénáře a umožňuje spolupráci Azure AD B2B (business-to-business), zatímco Azure Active Directory B2C je určen business-to-customer scénáře, včetně přihlášení k sociální síti.

## <a name="mobile"></a>Mobilní zařízení

[Centra oznámení](/azure/notification-hubs/) jsou multiplatformní škálovatelný modul nabízených oznámení, který rychle odesílá miliony zpráv do aplikací spuštěných na různých typech zařízení.

## <a name="web-infrastructure"></a>Webová infrastruktura

[Služba Azure Container Service](/azure/aks/) spravuje vaše hostované prostředí Kubernetes, takže je rychlé a snadné nasadit a spravovat kontejnerizované aplikace bez odborných znalostí orchestrace kontejnerů.

[Azure Search](/azure/search/) se používá k vytvoření řešení podnikového vyhledávání přes soukromý, heterogenní obsah.

[Service Fabric](/azure/service-fabric/) je platforma distribuovaných systémů, která usnadňuje balení, nasazování a správu škálovatelných a spolehlivých mikroslužeb a kontejnerů.
