---
title: Další kroky – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Další způsoby učení pro DevOps s ASP.NET Core a Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/next-steps
ms.openlocfilehash: 59c6bba7e5d05bbb6ef7db65bbedf70c4524b092
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625346"
---
# <a name="next-steps"></a><span data-ttu-id="4988a-103">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4988a-103">Next steps</span></span>

<span data-ttu-id="4988a-104">V této příručce jste vytvořili kanál DevOps pro ukázkovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4988a-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="4988a-105">Congratulations!</span><span class="sxs-lookup"><span data-stu-id="4988a-105">Congratulations!</span></span> <span data-ttu-id="4988a-106">Doufáme, že jste se naučili, jak publikovat ASP.NET Core webové aplikace a Azure App Service a automatizovat průběžnou integraci změn.</span><span class="sxs-lookup"><span data-stu-id="4988a-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="4988a-107">Mimo hostování webů a DevOps má Azure rozsáhlou škálu služeb typu platforma jako služba (PaaS), které jsou užitečné pro ASP.NET Core vývojářům.</span><span class="sxs-lookup"><span data-stu-id="4988a-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="4988a-108">Tato část poskytuje stručný přehled některých z nejčastěji používaných služeb.</span><span class="sxs-lookup"><span data-stu-id="4988a-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="4988a-109">Úložiště a databáze</span><span class="sxs-lookup"><span data-stu-id="4988a-109">Storage and databases</span></span>

<span data-ttu-id="4988a-110">[Redis Cache](/azure/redis-cache/) je k dispozici ukládání dat do mezipaměti s vysokou propustností a nízkou latencí jako služba.</span><span class="sxs-lookup"><span data-stu-id="4988a-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="4988a-111">Dá se použít pro ukládání výstupu stránky do mezipaměti, snižování požadavků na databázi a poskytování ASP.NET Core stavu relace napříč několika instancemi aplikace.</span><span class="sxs-lookup"><span data-stu-id="4988a-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="4988a-112">[Azure Storage](/azure/storage/) je rozsáhle škálovatelné cloudové úložiště Azure.</span><span class="sxs-lookup"><span data-stu-id="4988a-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="4988a-113">Vývojáři mohou využít výhod [Queue Storage](/azure/storage/queues/storage-queues-introduction) pro spolehlivé služby Řízení front zpráv a [Table Storage](/azure/storage/tables/table-storage-overview) je úložiště NoSQL klíč-hodnota navržené pro rychlý vývoj pomocí rozsáhlých, částečně strukturovaných datových sad.</span><span class="sxs-lookup"><span data-stu-id="4988a-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="4988a-114">[Azure SQL Database](/azure/sql-database/) poskytuje známé funkce relační databáze jako služba využívající modul Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4988a-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="4988a-115">[Cosmos DB](/azure/cosmos-db/) globálně distribuovaná databázová služba NoSQL pro více modelů.</span><span class="sxs-lookup"><span data-stu-id="4988a-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="4988a-116">K dispozici je více rozhraní API, včetně rozhraní SQL API (dříve nazývaného DocumentDB), Cassandra a MongoDB.</span><span class="sxs-lookup"><span data-stu-id="4988a-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## Identity

<span data-ttu-id="4988a-117">[Azure Active Directory](/azure/active-directory/) a [Azure Active Directory B2C](/azure/active-directory-b2c/) jsou obě služby identity.</span><span class="sxs-lookup"><span data-stu-id="4988a-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="4988a-118">Azure Active Directory je navržená pro podnikové scénáře a umožňuje spolupráci v Azure AD B2B (Business-to-Business), zatímco Azure Active Directory B2C je zamýšlené scénáře pro zákazníky, včetně přihlášení ke sociální síti.</span><span class="sxs-lookup"><span data-stu-id="4988a-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="4988a-119">Mobilní</span><span class="sxs-lookup"><span data-stu-id="4988a-119">Mobile</span></span>

<span data-ttu-id="4988a-120">[Notification Hubs](/azure/notification-hubs/) je Škálovatelný modul nabízených oznámení pro více platforem, který umožňuje rychle odesílat miliony zpráv aplikacím běžícím na různých typech zařízení.</span><span class="sxs-lookup"><span data-stu-id="4988a-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="4988a-121">Webová infrastruktura</span><span class="sxs-lookup"><span data-stu-id="4988a-121">Web infrastructure</span></span>

<span data-ttu-id="4988a-122">[Azure Container Service](/azure/aks/) spravuje hostované prostředí Kubernetes, které umožňuje rychlé a snadné nasazení a správu kontejnerových aplikací bez znalosti orchestrace kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="4988a-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="4988a-123">[Azure Search](/azure/search/) slouží k vytvoření podnikového řešení pro vyhledávání v rámci soukromého obsahu heterogenní.</span><span class="sxs-lookup"><span data-stu-id="4988a-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="4988a-124">[Service Fabric](/azure/service-fabric/) je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="4988a-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
