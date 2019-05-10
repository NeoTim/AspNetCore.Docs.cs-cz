---
title: Hostitele ASP.NET Core v kontejnerech Dockeru
author: rick-anderson
description: Vyhledat odkazy na zdroje informací pro výuku hostovat aplikace ASP.NET Core v kontejnerech Dockeru.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: 9189e1fbb21abcc8c8bdea947e672ee53b59bc4f
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085482"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Hostitele ASP.NET Core v kontejnerech Dockeru

Pro získání informací o hostování aplikací ASP.NET Core v Dockeru k dispozici jsou následující články:

[Úvod ke kontejnerům a Dockeru](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
Podívejte se jak kontejnerizace je přístup k vývoji softwaru, ve kterém aplikace nebo služby, jeho závislosti a jeho konfigurace spojených oprav jako image kontejneru. Image můžete otestovat a následně nasazen na hostiteli.

[Co je Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
Zjistěte, jak Docker je open source projektu pro automatizaci nasazení aplikace jako přenosných a soběstační kontejnerů, které lze spustit v cloudu nebo místně.

[Terminologie dockeru](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Přečtěte si podmínky a definice pro technologie Dockeru.

[Kontejnery, obrázky a registry Dockeru](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Přečtěte si uložení imagí kontejnerů Dockeru v registru imagí pro konzistentní nasazování napříč prostředími.

<xref:host-and-deploy/docker/building-net-docker-images> Zjistěte, jak sestavit a dockerizace aplikace ASP.NET Core. Prozkoumejte imagí Dockeru spravován společností Microsoft a prozkoumejte případy použití.

[Nástroje sady Visual Studio pro Docker](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Zjistěte, jak Visual Studio 2017 podporuje vytváření, ladění a spouštění ASP.NET Core aplikace zaměřené na rozhraní .NET Framework nebo .NET Core na Docker pro Windows. Jsou podporovány kontejnery Windows i Linuxu.

[Publikování do image Dockeru](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Zjistěte, jak používat nástroje Visual Studio pro rozšíření Docker pro nasazení aplikace ASP.NET Core do hostitele Docker v Azure pomocí Powershellu.

[Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer)  
Další konfigurace může být nezbytný pro aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení. Informace o původní požadavek, například schéma a IP adresa klienta předáním požadavků prostřednictvím proxy serveru často skryje. Může být potřeba předány některé informace o požadavku ručně, aby se aplikace.
