---
title: Hostování ASP.NET Core v kontejnerech Docker
author: rick-anderson
description: Seznamte se s odkazy na zdroje a Naučte se, jak hostovat aplikace ASP.NET Core v kontejnerech Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/index
ms.openlocfilehash: 14758c0d35841a077b36578786402c80fa3c3b1d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408055"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="6524a-103">Hostování ASP.NET Core v kontejnerech Docker</span><span class="sxs-lookup"><span data-stu-id="6524a-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="6524a-104">Následující články jsou k dispozici pro získání informací o hostování ASP.NET Corech aplikací v Docker:</span><span class="sxs-lookup"><span data-stu-id="6524a-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="6524a-105">Úvod ke kontejnerům a Dockeru</span><span class="sxs-lookup"><span data-stu-id="6524a-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="6524a-106">Podívejte se, jak vytváření kontejnerů představuje přístup k vývoji softwaru, ve kterém je aplikace nebo služba, její závislosti a její konfigurace zabaleny společně jako image kontejneru.</span><span class="sxs-lookup"><span data-stu-id="6524a-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="6524a-107">Bitovou kopii lze otestovat a následně nasadit na hostitele.</span><span class="sxs-lookup"><span data-stu-id="6524a-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="6524a-108">Co je Docker</span><span class="sxs-lookup"><span data-stu-id="6524a-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="6524a-109">Seznamte se s tím, jak Docker je open source projekt pro automatizaci nasazení aplikací jako přenosných a vlastních kontejnerů, které můžou běžet v cloudu nebo místně.</span><span class="sxs-lookup"><span data-stu-id="6524a-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="6524a-110">Terminologie Docker</span><span class="sxs-lookup"><span data-stu-id="6524a-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="6524a-111">Seznamte se s podmínkami a definicemi pro technologii Docker.</span><span class="sxs-lookup"><span data-stu-id="6524a-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="6524a-112">Registry, image a kontejnery Dockeru</span><span class="sxs-lookup"><span data-stu-id="6524a-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="6524a-113">Zjistěte, jak jsou image kontejnerů Docker uložené v registru imagí pro konzistentní nasazení napříč prostředími.</span><span class="sxs-lookup"><span data-stu-id="6524a-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="6524a-114"><xref:host-and-deploy/docker/building-net-docker-images>Naučte se sestavovat a ukotvovat aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6524a-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="6524a-115">Prozkoumejte image Docker udržované Microsoftem a prozkoumejte případy použití.</span><span class="sxs-lookup"><span data-stu-id="6524a-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="6524a-116">Nástroje kontejneru sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6524a-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="6524a-117">Zjistěte, jak Visual Studio podporuje sestavování, ladění a spouštění aplikací ASP.NET Core cílících na .NET Framework nebo .NET Core na Docker for Windows.</span><span class="sxs-lookup"><span data-stu-id="6524a-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="6524a-118">Podporují se kontejnery Windows i Linux.</span><span class="sxs-lookup"><span data-stu-id="6524a-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="6524a-119">Publikování do služby Azure Container Registry</span><span class="sxs-lookup"><span data-stu-id="6524a-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="6524a-120">Zjistěte, jak pomocí rozšíření sady Visual Studio Container Tools nasadit aplikaci ASP.NET Core do hostitele Docker v Azure pomocí PowerShellu.</span><span class="sxs-lookup"><span data-stu-id="6524a-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="6524a-121">Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="6524a-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="6524a-122">Pro aplikace hostované za proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="6524a-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="6524a-123">Předávání požadavků prostřednictvím proxy serveru často překrývá informace o původní žádosti, jako je například schéma a IP adresa klienta.</span><span class="sxs-lookup"><span data-stu-id="6524a-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="6524a-124">Může být nutné přeslat některé informace o žádosti do aplikace ručně.</span><span class="sxs-lookup"><span data-stu-id="6524a-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="6524a-125">[GC s použitím Docker a malých kontejnerů](xref:performance/memory#sc) Popisuje výběr GC s malými kontejnery.</span><span class="sxs-lookup"><span data-stu-id="6524a-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>