---
title: Hostitelství a nasazení Blazor na straně serveru
author: guardrex
description: Zjistěte, jak hostovat a nasazení aplikace na straně serveru Blazor pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 940020ee44d72d50395aad64bc924413c1bbecfb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614815"
---
# <a name="host-and-deploy-blazor-server-side"></a>Hostitelství a nasazení Blazor na straně serveru

Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Serverové aplikace, které používají [model hostingu na straně serveru](xref:blazor/hosting-models#server-side-hosting-model) může přijmout [obecný hostitele konfigurační hodnoty](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Nasazení

S [model hostingu na straně serveru](xref:blazor/hosting-models#server-side-hosting-model), Blazor provádí na serveru z v rámci aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.

Aplikace je součástí aplikace ASP.NET Core v publikované výstupní a dvou aplikací se nasazují společně. Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný. Pro nasazení na straně serveru, Visual Studio obsahuje **Razor komponenty** šablonu projektu (`razorcomponents` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.

Informace o nasazení do služby Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.
