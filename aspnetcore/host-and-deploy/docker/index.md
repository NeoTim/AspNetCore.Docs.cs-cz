---
title: Hostitele ASP.NET Core v kontejnerech Dockeru
author: rick-anderson
description: Vyhledat odkazy na zdroje informací pro výuku hostovat aplikace ASP.NET Core v kontejnerech Dockeru.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: e56f90ec7272ce0411651ee6f8e7c754ae44b78d
ms.sourcegitcommit: 9b7fcb4ce00a3a32e153a080ebfaae4ef417aafa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2019
ms.locfileid: "59516258"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="ba0f2-103">Hostitele ASP.NET Core v kontejnerech Dockeru</span><span class="sxs-lookup"><span data-stu-id="ba0f2-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="ba0f2-104">Pro získání informací o hostování aplikací ASP.NET Core v Dockeru k dispozici jsou následující články:</span><span class="sxs-lookup"><span data-stu-id="ba0f2-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="ba0f2-105">Úvod ke kontejnerům a Dockeru</span><span class="sxs-lookup"><span data-stu-id="ba0f2-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="ba0f2-106">Podívejte se jak kontejnerizace je přístup k vývoji softwaru, ve kterém aplikace nebo služby, jeho závislosti a jeho konfigurace spojených oprav jako image kontejneru.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="ba0f2-107">Image můžete otestovat a následně nasazen na hostiteli.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="ba0f2-108">Co je Docker</span><span class="sxs-lookup"><span data-stu-id="ba0f2-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="ba0f2-109">Zjistěte, jak Docker je open source projektu pro automatizaci nasazení aplikace jako přenosných a soběstační kontejnerů, které lze spustit v cloudu nebo místně.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="ba0f2-110">Terminologie dockeru</span><span class="sxs-lookup"><span data-stu-id="ba0f2-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="ba0f2-111">Přečtěte si podmínky a definice pro technologie Dockeru.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="ba0f2-112">Kontejnery, obrázky a registry Dockeru</span><span class="sxs-lookup"><span data-stu-id="ba0f2-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="ba0f2-113">Přečtěte si uložení imagí kontejnerů Dockeru v registru imagí pro konzistentní nasazování napříč prostředími.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

[<span data-ttu-id="ba0f2-114">Nástroje sady Visual Studio pro Docker</span><span class="sxs-lookup"><span data-stu-id="ba0f2-114">Visual Studio Tools for Docker</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="ba0f2-115">Zjistěte, jak Visual Studio 2017 podporuje vytváření, ladění a spouštění ASP.NET Core aplikace zaměřené na rozhraní .NET Framework nebo .NET Core na Docker pro Windows.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-115">Discover how Visual Studio 2017 supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="ba0f2-116">Jsou podporovány kontejnery Windows i Linuxu.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-116">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="ba0f2-117">Publikování do image Dockeru</span><span class="sxs-lookup"><span data-stu-id="ba0f2-117">Publish to a Docker Image</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="ba0f2-118">Zjistěte, jak používat nástroje Visual Studio pro rozšíření Docker pro nasazení aplikace ASP.NET Core do hostitele Docker v Azure pomocí Powershellu.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-118">Find out how to use the Visual Studio Tools for Docker extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="ba0f2-119">Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="ba0f2-119">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="ba0f2-120">Další konfigurace může být nezbytný pro aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-120">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="ba0f2-121">Informace o původní požadavek, například schéma a IP adresa klienta předáním požadavků prostřednictvím proxy serveru často skryje.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-121">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="ba0f2-122">Může být potřeba předány některé informace o požadavku ručně, aby se aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba0f2-122">It might be necessary to forwarded some information about the request manually to the app.</span></span>
