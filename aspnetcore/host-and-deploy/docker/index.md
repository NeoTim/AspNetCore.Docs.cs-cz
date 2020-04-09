---
title: Hostování ASP.NET jádra v kontejnerech Dockeru
author: rick-anderson
description: Seznamte se s odkazy na materiály, kde se dozvíte, jak hostovat aplikace ASP.NET Core v kontejnerech Dockeru.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: cb5f774db5fab46a57f8ca4bbbca148f20f371ba
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664078"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Hostování ASP.NET jádra v kontejnerech Dockeru

Následující články jsou k dispozici pro informace o hostování ASP.NET základních aplikací v Dockeru:

[Úvod ke kontejnerům a Dockeru](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
Podívejte se, jak kontejnerizace je přístup k vývoji softwaru, ve kterém aplikace nebo služby, její závislosti a jeho konfigurace jsou zabaleny společně jako image kontejneru. Bitovou kopii lze testovat a poté nasadit do hostitele.

[Co je Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
Zjistěte, jak je Docker open source projekt pro automatizaci nasazení aplikací jako přenosných, soběstačných kontejnerů, které můžou běžet v cloudu nebo v místním prostředí.

[Terminologie Dockeru](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Seznamte se s termíny a definicemi pro technologii Dockeru.

[Registry, image a kontejnery Dockeru](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Zjistěte, jak jsou image kontejnerů Dockeru uloženy v registru bitových kopií pro konzistentní nasazení napříč prostředími.

<xref:host-and-deploy/docker/building-net-docker-images>Přečtěte si, jak vytvořit a získat ASP.NET aplikaci Core. Prozkoumejte image Dockeru spravované společností Microsoft a prozkoumejte případy použití.

[Nástroje kontejneru sady Visual Studio](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Zjistěte, jak Visual Studio podporuje vytváření, ladění a spouštění aplikací ASP.NET Core zaměřených na rozhraní .NET Framework nebo .NET Core v Dockeru pro Windows. Jsou podporovány kontejnery Windows i Linux.

[Publikování do služby Azure Container Registry](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Zjistěte, jak pomocí rozšíření Nástroje kontejneru Visual Studio nasadit aplikaci ASP.NET Core do hostitele Dockeru v Azure pomocí PowerShellu.

[Konfigurace ASP.NET Core pro práci s proxy servery a vyrovnáváním zatížení](xref:host-and-deploy/proxy-load-balancer)  
Další konfigurace může být vyžadována pro aplikace hostované za proxy servery a nástroji pro vyrovnávání zatížení. Předávání požadavků prostřednictvím proxy serveru často zakrývá informace o původním požadavku, jako je například schéma a IP klienta. Může být nutné předat některé informace o požadavku ručně do aplikace.
