---
title: Hostování a nasazení ASP.NET Core Blazor na straně serveru
author: guardrex
description: Naučte se hostovat a nasazovat Blazor aplikaci na straně serveru pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8da71faf6abc5929d6cd43d42fd896e378d99ef6
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773577"
---
# <a name="host-and-deploy-blazor-server-side"></a>Hostování a nasazení Blazor na straně serveru

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Aplikace na straně serveru, které používají [model hostování na straně serveru](xref:blazor/hosting-models#server-side) , mohou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Nasazení

S [modelem hostování na straně serveru](xref:blazor/hosting-models#server-side)je Blazor spuštěn na serveru z aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes připojení k [signalizaci](xref:signalr/introduction) .

Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core. Visual Studio obsahuje šablonu projektu **aplikace Blazor serveru** (`blazorserverside` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ).

## <a name="connection-scale-out"></a>Horizontální navýšení kapacity připojení

Blazor aplikace na straně serveru vyžadují jedno aktivní připojení k signalizaci pro každého uživatele. Provozní Blazor nasazení na straně serveru vyžaduje řešení pro podporu tolika souběžných připojení, kolik vyžaduje aplikace. [Služba signalizace Azure](/azure/azure-signalr/) zpracovává škálování připojení a doporučuje se jako řešení škálování pro Blazor aplikace na straně serveru. Další informace naleznete v tématu <xref:signalr/publish-to-azure-web-app>.

## <a name="signalr-configuration"></a>Konfigurace signálu

Služba Signal je nakonfigurována ASP.NET Core pro nejběžnější scénáře Blazor na straně serveru. Vlastní a pokročilé scénáře najdete v článcích o signalizaci v části [Další zdroje informací](#additional-resources) .

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/introduction>
* [Dokumentace ke službě Azure Signal](/azure/azure-signalr/)
* [Rychlý start: Vytvoření chatovací místnosti pomocí služby Signal](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Nasazení verze Preview ASP.NET Core do Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
