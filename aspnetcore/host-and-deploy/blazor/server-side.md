---
title: Hostitelství a nasazení ASP.NET Core Blazor na straně serveru
author: guardrex
description: Zjistěte, jak hostovat a nasazení aplikace na straně serveru Blazor pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 56a03ff583bf85497e2b3bacc70123845a046e3d
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67892701"
---
# <a name="host-and-deploy-blazor-server-side"></a>Hostitelství a nasazení Blazor na straně serveru

Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Serverové aplikace, které používají [model hostingu na straně serveru](xref:blazor/hosting-models#server-side) může přijmout [obecný hostitele konfigurační hodnoty](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Nasazení

S [model hostingu na straně serveru](xref:blazor/hosting-models#server-side), Blazor provádí na serveru z v rámci aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.

Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný. Visual Studio obsahuje **Blazor serverovou aplikaci** šablonu projektu (`blazorserverside` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).

## <a name="connection-scale-out"></a>Připojení pro horizontální navýšení kapacity

Blazor serverové aplikace vyžadují jedno aktivní připojení SignalR pro každého uživatele. Produkční nasazení na straně serveru Blazor vyžaduje řešení pro podporu tolik souběžných připojení podle potřeb aplikace. [Služby Azure SignalR](/azure/azure-signalr/) zpracovává škálování připojení a doporučuje se jako škálování řešení pro Blazor aplikace na straně serveru. Další informace naleznete v tématu <xref:signalr/publish-to-azure-web-app>.

## <a name="signalr-configuration"></a>Konfigurace funkce SignalR

SignalR je konfigurována pomocí ASP.NET Core pro nejběžnější scénáře Blazor na straně serveru. Pro vlastní a pokročilé scénáře, najdete v článcích SignalR v [další prostředky](#additional-resources) oddílu.

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/introduction>
* [Dokumentace ke službě Azure SignalR](/azure/azure-signalr/)
* [Rychlý start: Vytvoření chatovací místnosti s použitím služby SignalR](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Nasazení ve verzi preview ASP.NET Core do služby Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
