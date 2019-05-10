---
title: Hostitelství a nasazení Blazor na straně serveru
author: guardrex
description: Zjistěte, jak hostovat a nasazení aplikace na straně serveru Blazor pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/26/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8e44be09a4cceba2509f3e86abf3ce5fd2d077bd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901047"
---
# <a name="host-and-deploy-blazor-server-side"></a>Hostitelství a nasazení Blazor na straně serveru

Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Serverové aplikace, které používají [model hostingu na straně serveru](xref:blazor/hosting-models#server-side) může přijmout [obecný hostitele konfigurační hodnoty](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Nasazení

S [model hostingu na straně serveru](xref:blazor/hosting-models#server-side), Blazor provádí na serveru z v rámci aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.

Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný. Visual Studio obsahuje **Blazor (serverové)** šablonu projektu (`blazorserverside` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Nasazení ve verzi preview ASP.NET Core do služby Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
