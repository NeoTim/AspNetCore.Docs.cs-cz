---
title: DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Průvodce, který poskytuje pokyny k začátku do konce na vytváření procesních toků pro DevOps pro aplikace ASP.NET Core hostované v Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: seodec18
uid: azure/devops/index
ms.openlocfilehash: 6b6f17fd917f32b9e0682414febee10643cf3d13
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121190"
---
# <a name="devops-with-aspnet-core-and-azure"></a>DevOps s využitím ASP.NET Core a Azure

[![Titulní obrázek](./media/cover-large.png)](https://aka.ms/devopsbook)

Podle [kamera Soper](https://twitter.com/camsoper) a [Scott Addie](https://twitter.com/scottaddie)

Tento průvodce je k dispozici jako [PDF e kniha ke stažení](https://aka.ms/devopsbook).

## <a name="welcome"></a>Vítej 

Vítejte v Průvodci životního cyklu vývoje Azure pro .NET Tato příručka představuje základní koncepty vytváření životního cyklu vývoje kolem Azure s využitím .NET nástrojů a procesů. Po dokončení tohoto průvodce, budete těžit z výhod až po zralé sadu nástrojů DevOps.

## <a name="who-this-guide-is-for"></a>Kdo tento průvodce je na

Měli byste být vývojáři ASP.NET Core (úroveň 200 300). Nemusíte vědět nic o Azure, jak tom dozvíme v tomto úvodu. Tento průvodce může být také užitečné pro DevOps techniky, kteří se zaměřují na operace než vývoje.

Tato příručka cílí na vývojáře Windows. Systémy Linux a macOS se ale plně podporují pomocí .NET Core. Přizpůsobit tento průvodce pro Linux nebo macOS, podívejte se na pro popisky pro Linux nebo macOS rozdíly.

## <a name="what-this-guide-doesnt-cover"></a>Co tato příručka nepopisuje

Tato příručka se zaměřuje na začátku do konce průběžné nasazování prostředí pro vývojáře na platformě .NET. Není vyčerpávající příručka k vše, co Azure, a to není zaměřit výrazně na rozhraní .NET API pro služby Azure. Důraz je po průběžnou integraci, nasazení, monitorování a ladění. Na konci Průvodce nabízí doporučení pro další kroky. Součástí návrhy jsou služeb platformy Azure, které jsou užitečné pro vývojáře v ASP.NET Core.

## <a name="whats-in-this-guide"></a>Co je v této příručce

### <a name="tools-and-downloadsxrefazuredevopstools-and-downloads"></a>[Nástroje a soubory ke stažení](xref:azure/devops/tools-and-downloads)

Zjistěte, kde získat nástroje používané v tomto průvodci.

### <a name="deploy-to-app-servicexrefazuredevopsdeploy-to-app-service"></a>[Nasazení do App Service](xref:azure/devops/deploy-to-app-service)

Přečtěte si různé metody pro nasazení aplikace ASP.NET Core do služby Azure App Service.

### <a name="continuous-integration-and-deploymentxrefazuredevopscicd"></a>[Průběžná integrace a nasazování](xref:azure/devops/cicd)

Sestavení začátku do konce průběžnou integraci a nasazení řešení pro aplikace ASP.NET Core s využitím Githubu, služby Azure DevOps a Azure.

### <a name="monitor-and-debugxrefazuredevopsmonitor"></a>[Monitorování a ladění](xref:azure/devops/monitor)

Pomocí nástrojů Azure můžete sledovat, řešení potíží a ladění aplikací.

### <a name="next-stepsxrefazuredevopsnext-steps"></a>[Další postup](xref:azure/devops/next-steps)

Další postupy výuky pro vývojáře v ASP.NET Core seznámení s Azure.

## <a name="additional-introductory-reading"></a>Další úvodní články

Pokud je toto první vystavení ke cloud computingu, tyto články vysvětlují základní informace.

* [Co je Cloud Computing?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [Příklady Cloud computingu](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [Co je IaaS?](https://azure.microsoft.com/overview/what-is-iaas/)
* [Co je PaaS?](https://azure.microsoft.com/overview/what-is-paas/)
