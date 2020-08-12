---
title: DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Příručka, která poskytuje kompletní pokyny k vytvoření DevOps kanálu pro ASP.NET Core aplikaci hostovanou v Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: efabfd7fdcd695186207654887292c6b2104342d
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130181"
---
# <a name="devops-with-aspnet-core-and-azure"></a>DevOps s využitím ASP.NET Core a Azure

[![Titulní obrázek](./media/cover-large.png)](https://aka.ms/devopsbook)

Pomocí [Vačk Soper](https://twitter.com/camsoper) a [Scott Addie](https://twitter.com/scottaddie)

Tato příručka je k dispozici jako [ke stažení e-knihy ve formátu PDF](https://aka.ms/devopsbook).

## <a name="welcome"></a>Vítáme vás 

Vítá vás Průvodce životním cyklem vývoje Azure pro .NET! Tato příručka zavádí základní koncepty vytváření životního cyklu vývoje kolem Azure pomocí nástrojů a procesů .NET. Po dokončení tohoto průvodce budete těžit výhody DevOpse sada nástrojůu pro dospělé.

## <a name="who-this-guide-is-for"></a>Koho je tato příručka určena pro

Měli byste být zkušeným ASP.NET Core vývojářem (úroveň 200-300). Nemusíte nic dělat na Azure, protože v tomto úvodu pokryjeme. Tato příručka může být užitečná také pro inženýry DevOps, kteří se podrobněji zaměřují na operace než vývoj.

Tato příručka cílí na vývojáře v systému Windows. Systémy Linux a macOS jsou však plně podporovány rozhraním .NET Core. Pokud chcete tento průvodce upravit pro Linux/macOS, podívejte se na popisky pro rozdíly v systému Linux/macOS.

## <a name="what-this-guide-doesnt-cover"></a>Co tato příručka nepokrývá

Tato příručka se zaměřuje na ucelené prostředí průběžného nasazování pro vývojáře na platformě .NET. Není vyčerpávajícím průvodcem pro všechny věci Azure a nezaměřuje se na to rozsáhle rozhraní API .NET pro služby Azure. Důraz je vše kolem nepřetržité integrace, nasazení, monitorování a ladění. Na konci příručky jsou k dispozici doporučení pro další kroky. Součástí návrhů jsou služby platformy Azure, které jsou užitečné pro ASP.NET Core vývojářům.

## <a name="whats-in-this-guide"></a>Co je v této příručce

### <a name="tools-and-downloads"></a>[Nástroje a soubory ke stažení](xref:azure/devops/tools-and-downloads)

Zjistěte, kde získat nástroje používané v této příručce.

### <a name="deploy-to-app-service"></a>[Nasazení do App Service](xref:azure/devops/deploy-to-app-service)

Seznamte se s různými způsoby nasazení ASP.NET Core aplikace do Azure App Service.

### <a name="continuous-integration-and-deployment"></a>[Průběžná integrace a nasazování](xref:azure/devops/cicd)

Pomocí GitHubu, Azure DevOps Services a Azure můžete vytvořit komplexní řešení pro průběžnou integraci a nasazení pro vaši aplikaci ASP.NET Core.

### <a name="monitor-and-debug"></a>[Monitorování a ladění](xref:azure/devops/monitor)

Pomocí nástrojů Azure můžete monitorovat, řešit problémy a ladit svou aplikaci.

### <a name="next-steps"></a>[Další kroky](xref:azure/devops/next-steps)

Další studijní materiály pro Azure ASP.NET Core Learning pro vývojáře

## <a name="additional-introductory-reading"></a>Dodatečné úvodní čtení

Pokud je to vaše první riziko pro cloud computing, tyto články vysvětlují základy.

* [Co je cloud computing?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [Příklady cloud computingu](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [Co je IaaS?](https://azure.microsoft.com/overview/what-is-iaas/)
* [Co je PaaS?](https://azure.microsoft.com/overview/what-is-paas/)
