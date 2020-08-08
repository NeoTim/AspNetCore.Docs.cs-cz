---
title: Další kroky – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Další způsoby učení pro DevOps s ASP.NET Core a Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: feff73b307791c0a7ad8968b5145c0388df1ca10
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012580"
---
# <a name="next-steps"></a>Další kroky

V této příručce jste vytvořili kanál DevOps pro ukázkovou aplikaci ASP.NET Core. Gratulujeme! Doufáme, že jste se naučili, jak publikovat ASP.NET Core webové aplikace a Azure App Service a automatizovat průběžnou integraci změn.

Mimo hostování webů a DevOps má Azure rozsáhlou škálu služeb typu platforma jako služba (PaaS), které jsou užitečné pro ASP.NET Core vývojářům. Tato část poskytuje stručný přehled některých z nejčastěji používaných služeb.

## <a name="storage-and-databases"></a>Úložiště a databáze

[Redis Cache](/azure/redis-cache/) je k dispozici ukládání dat do mezipaměti s vysokou propustností a nízkou latencí jako služba. Dá se použít pro ukládání výstupu stránky do mezipaměti, snižování požadavků na databázi a poskytování ASP.NET Core stavu relace napříč několika instancemi aplikace.

[Azure Storage](/azure/storage/) je rozsáhle škálovatelné cloudové úložiště Azure. Vývojáři mohou využít výhod [Queue Storage](/azure/storage/queues/storage-queues-introduction) pro spolehlivé služby Řízení front zpráv a [Table Storage](/azure/storage/tables/table-storage-overview) je úložiště NoSQL klíč-hodnota navržené pro rychlý vývoj pomocí rozsáhlých, částečně strukturovaných datových sad.

[Azure SQL Database](/azure/sql-database/) poskytuje známé funkce relační databáze jako služba využívající modul Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) globálně distribuovaná databázová služba NoSQL pro více modelů. K dispozici je více rozhraní API, včetně rozhraní SQL API (dříve nazývaného DocumentDB), Cassandra a MongoDB.

## Identity

[Azure Active Directory](/azure/active-directory/) a [Azure Active Directory B2C](/azure/active-directory-b2c/) jsou obě služby identity. Azure Active Directory je navržená pro podnikové scénáře a umožňuje spolupráci v Azure AD B2B (Business-to-Business), zatímco Azure Active Directory B2C je zamýšlené scénáře pro zákazníky, včetně přihlášení ke sociální síti.

## <a name="mobile"></a>Mobilní

[Notification Hubs](/azure/notification-hubs/) je Škálovatelný modul nabízených oznámení pro více platforem, který umožňuje rychle odesílat miliony zpráv aplikacím běžícím na různých typech zařízení.

## <a name="web-infrastructure"></a>Webová infrastruktura

[Azure Container Service](/azure/aks/) spravuje hostované prostředí Kubernetes, které umožňuje rychlé a snadné nasazení a správu kontejnerových aplikací bez znalosti orchestrace kontejnerů.

[Azure Search](/azure/search/) slouží k vytvoření podnikového řešení pro vyhledávání v rámci soukromého obsahu heterogenní.

[Service Fabric](/azure/service-fabric/) je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů.
