---
title: Hostitelství a nasazení součásti syntaxe Razor
author: guardrex
description: Zjistěte, jak hostovat a nasazení pomocí ASP.NET Core Razor součásti aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: host-and-deploy/razor-components-blazor/razor-components
ms.openlocfilehash: 18b09dfc80e2f517c7750ce0fe1510641624aea7
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59069774"
---
# <a name="host-and-deploy-razor-components"></a>Hostitelství a nasazení součásti syntaxe Razor

Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Součásti aplikace Razor, používající [model hostingu na straně serveru](xref:razor-components/hosting-models#server-side-hosting-model) může přijmout [obecný hostitele konfigurační hodnoty](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Nasazení

S [model hostingu na straně serveru](xref:razor-components/hosting-models#server-side-hosting-model), Razor komponent je provedeno na serveru z v rámci aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.

Aplikace je součástí aplikace ASP.NET Core v publikované výstupní a dvou aplikací se nasazují společně. Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný. Pro nasazení na straně serveru, Visual Studio obsahuje **Razor komponenty** šablonu projektu (`razorcomponents` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.

Informace o nasazení do služby Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.
