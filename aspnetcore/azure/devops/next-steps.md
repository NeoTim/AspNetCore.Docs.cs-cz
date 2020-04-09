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
# <a name="next-steps"></a><span data-ttu-id="d2e32-103">Další kroky</span><span class="sxs-lookup"><span data-stu-id="d2e32-103">Next steps</span></span>

<span data-ttu-id="d2e32-104">V této příručce jste vytvořili kanál DevOps pro ukázkovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2e32-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="d2e32-105">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="d2e32-105">Congratulations!</span></span> <span data-ttu-id="d2e32-106">Doufáme, že se vám líbilo publikovat ASP.NET webových aplikací Core do služby Azure App Service a automatizovat průběžnou integraci změn.</span><span class="sxs-lookup"><span data-stu-id="d2e32-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="d2e32-107">Kromě web hostingu a DevOps má Azure širokou škálu služeb PaaS (Platform-as-a-Service), které jsou užitečné pro ASP.NET vývojářům Core.</span><span class="sxs-lookup"><span data-stu-id="d2e32-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="d2e32-108">Tato část poskytuje stručný přehled některých nejčastěji používaných služeb.</span><span class="sxs-lookup"><span data-stu-id="d2e32-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="d2e32-109">Úložiště a databáze</span><span class="sxs-lookup"><span data-stu-id="d2e32-109">Storage and databases</span></span>

<span data-ttu-id="d2e32-110">[Redis Cache](/azure/redis-cache/) je ukládání dat s vysokou propustností a nízkou latencí, které je k dispozici jako služba.</span><span class="sxs-lookup"><span data-stu-id="d2e32-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="d2e32-111">Lze jej použít pro ukládání výstupu stránky do mezipaměti, snížení požadavků na databázi a poskytování ASP.NET stavu základní relace napříč více instancemi aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2e32-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="d2e32-112">[Azure Storage](/azure/storage/) je masivně škálovatelné cloudové úložiště Azure.</span><span class="sxs-lookup"><span data-stu-id="d2e32-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="d2e32-113">Vývojáři mohou využít [služby Queue Storage](/azure/storage/queues/storage-queues-introduction) pro spolehlivé řízení front zpráv a [table storage](/azure/storage/tables/table-storage-overview) je úložiště hodnot klíčů NoSQL určené pro rychlý vývoj pomocí masivních polostrukturovaných datových sad.</span><span class="sxs-lookup"><span data-stu-id="d2e32-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="d2e32-114">[Azure SQL Database](/azure/sql-database/) poskytuje známé funkce relační databáze jako služba pomocí Microsoft SQL Server Engine.</span><span class="sxs-lookup"><span data-stu-id="d2e32-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="d2e32-115">[Cosmos DB](/azure/cosmos-db/) globálně distribuovaná databázová služba NoSQL s více modely.</span><span class="sxs-lookup"><span data-stu-id="d2e32-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="d2e32-116">K dispozici je více rozhraní API, včetně rozhraní SQL API (dříve nazývané DocumentDB), Cassandra a MongoDB.</span><span class="sxs-lookup"><span data-stu-id="d2e32-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="d2e32-117">Identita</span><span class="sxs-lookup"><span data-stu-id="d2e32-117">Identity</span></span>

<span data-ttu-id="d2e32-118">[Azure Active Directory](/azure/active-directory/) a [Azure Active Directory B2C](/azure/active-directory-b2c/) jsou obě služby identity.</span><span class="sxs-lookup"><span data-stu-id="d2e32-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="d2e32-119">Azure Active Directory je navržený pro podnikové scénáře a umožňuje spolupráci Azure AD B2B (business-to-business), zatímco Azure Active Directory B2C je určen business-to-customer scénáře, včetně přihlášení k sociální síti.</span><span class="sxs-lookup"><span data-stu-id="d2e32-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="d2e32-120">Mobilní zařízení</span><span class="sxs-lookup"><span data-stu-id="d2e32-120">Mobile</span></span>

<span data-ttu-id="d2e32-121">[Centra oznámení](/azure/notification-hubs/) jsou multiplatformní škálovatelný modul nabízených oznámení, který rychle odesílá miliony zpráv do aplikací spuštěných na různých typech zařízení.</span><span class="sxs-lookup"><span data-stu-id="d2e32-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="d2e32-122">Webová infrastruktura</span><span class="sxs-lookup"><span data-stu-id="d2e32-122">Web infrastructure</span></span>

<span data-ttu-id="d2e32-123">[Služba Azure Container Service](/azure/aks/) spravuje vaše hostované prostředí Kubernetes, takže je rychlé a snadné nasadit a spravovat kontejnerizované aplikace bez odborných znalostí orchestrace kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="d2e32-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="d2e32-124">[Azure Search](/azure/search/) se používá k vytvoření řešení podnikového vyhledávání přes soukromý, heterogenní obsah.</span><span class="sxs-lookup"><span data-stu-id="d2e32-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="d2e32-125">[Service Fabric](/azure/service-fabric/) je platforma distribuovaných systémů, která usnadňuje balení, nasazování a správu škálovatelných a spolehlivých mikroslužeb a kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="d2e32-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
