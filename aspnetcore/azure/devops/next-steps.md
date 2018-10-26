---
title: DevOps s využitím ASP.NET Core a Azure | Další kroky
author: CamSoper
description: Průvodce, který poskytuje pokyny k začátku do konce na vytváření procesních toků pro DevOps pro aplikace ASP.NET Core hostované v Azure.
ms.author: casoper
ms.custom: mvc
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: b82e7251b507f8d141930673d50722cfaa576db5
ms.sourcegitcommit: 4d74644f11e0dac52b4510048490ae731c691496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50089875"
---
# <a name="next-steps"></a><span data-ttu-id="4d11e-103">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4d11e-103">Next steps</span></span>

<span data-ttu-id="4d11e-104">V tomto průvodci vytvořit kanál DevOps pro ukázkové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d11e-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="4d11e-105">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="4d11e-105">Congratulations!</span></span> <span data-ttu-id="4d11e-106">Doufáme, že se vám líbilo učení k publikování webových aplikací ASP.NET Core do služby Azure App Service a automatizovat průběžné integrace změny.</span><span class="sxs-lookup"><span data-stu-id="4d11e-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="4d11e-107">Kromě hostování webů a DevOps Azure nabízí širokou škálu služeb Platform-as-a-Service (PaaS) je užitečný pro vývojáře ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d11e-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="4d11e-108">Tato část poskytuje stručný přehled o některé z nejčastěji používaných služeb.</span><span class="sxs-lookup"><span data-stu-id="4d11e-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="4d11e-109">Úložiště a databází</span><span class="sxs-lookup"><span data-stu-id="4d11e-109">Storage and databases</span></span>

<span data-ttu-id="4d11e-110">[Redis Cache](/azure/redis-cache/) je vysokou propustností a nízkou latencí dat, ukládání do mezipaměti k dispozici jako službu.</span><span class="sxs-lookup"><span data-stu-id="4d11e-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="4d11e-111">Je možné pro ukládání výstupu stránek, snížení požadavků na databázi a poskytování stavu relace ASP.NET Core do několika instancí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4d11e-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="4d11e-112">[Azure Storage](/azure/storage/) je široce škálovatelné cloudové úložiště Azure.</span><span class="sxs-lookup"><span data-stu-id="4d11e-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="4d11e-113">Vývojáři můžou využít výhod [Queue Storage](/azure/storage/queues/storage-queues-introduction) spolehlivé front a [Table Storage](/azure/storage/tables/table-storage-overview) je úložiště dvojic klíč hodnota NoSQL navržená pro rychlý vývoj pomocí rozsáhlé, částečně strukturovaných datových sad.</span><span class="sxs-lookup"><span data-stu-id="4d11e-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="4d11e-114">[Azure SQL Database](/azure/sql-database/) poskytuje funkce známé relační databáze jako služby pomocí stroje Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4d11e-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="4d11e-115">[Cosmos DB](/azure/cosmos-db/) globálně distribuovaná a vícemodelová databázová služba NoSQL.</span><span class="sxs-lookup"><span data-stu-id="4d11e-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="4d11e-116">Několik rozhraní API jsou dostupná, včetně rozhraní SQL API (dříve se označovaly jako DocumentDB) a Cassandra, MongoDB.</span><span class="sxs-lookup"><span data-stu-id="4d11e-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="4d11e-117">Identita</span><span class="sxs-lookup"><span data-stu-id="4d11e-117">Identity</span></span>

<span data-ttu-id="4d11e-118">[Azure Active Directory](/azure/active-directory/) a [Azure Active Directory B2C](/azure/active-directory-b2c/) jsou obě služby identit.</span><span class="sxs-lookup"><span data-stu-id="4d11e-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="4d11e-119">Azure Active Directory je určená pro podnikové scénáře a umožňuje spolupráci Azure AD B2B (business-to-business), zatímco Azure Active Directory B2C je zamýšlené firmy zákazníka scénářů, včetně přihlášení sociálních sítí.</span><span class="sxs-lookup"><span data-stu-id="4d11e-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="4d11e-120">Mobilní zařízení</span><span class="sxs-lookup"><span data-stu-id="4d11e-120">Mobile</span></span>

<span data-ttu-id="4d11e-121">[Notification Hubs](/azure/notification-hubs/) je modul pro multiplatformní a škálovatelnou nabízených oznámení k rychlému rozesílání milionů zpráv aplikacím spuštěným na různých typech zařízení.</span><span class="sxs-lookup"><span data-stu-id="4d11e-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="4d11e-122">Infrastruktura webové</span><span class="sxs-lookup"><span data-stu-id="4d11e-122">Web infrastructure</span></span>

<span data-ttu-id="4d11e-123">[Služba Azure Container Service](/azure/aks/) spravuje vaše hostované prostředí Kubernetes, tak rychle a snadno nasazovat a spravovat kontejnerizované aplikace bez znalosti Orchestrace kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="4d11e-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="4d11e-124">[Služba Azure Search](/azure/search/) slouží k vytvoření podniková řešení pro hledání nad privátním heterogenním obsahem.</span><span class="sxs-lookup"><span data-stu-id="4d11e-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="4d11e-125">[Service Fabric](/azure/service-fabric/) je platforma distribuovaných systémů, která usnadňuje balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="4d11e-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
