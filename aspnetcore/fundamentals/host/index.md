---
title: Webového hostitele a obecné hostitele v ASP.NET Core
author: guardrex
description: Další informace o webového hostitele ASP.NET Core a .NET obecný hostitele, která jsou odpovědná za spuštění a životního cyklu správy aplikací.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/28/2018
uid: fundamentals/host/index
ms.openlocfilehash: 43a68f67368ce37a1fb4032579c6c4e4c05d2719
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284575"
---
# <a name="web-host-and-generic-host-in-aspnet-core"></a>Webového hostitele a obecné hostitele v ASP.NET Core

Konfigurace aplikací .NET a spouštění *hostitele*. Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací. Dva hostitele rozhraní API se dají používat:

* [Webového hostitele](xref:fundamentals/host/web-host) &ndash; vhodná pro hostování webových aplikací.
* [Obecný hostitele](xref:fundamentals/host/generic-host) (ASP.NET Core 2.1 nebo novější) &ndash; vhodná pro hostování jiných webových aplikací (například aplikace, na kterých běží úlohy na pozadí). V budoucí verzi bude obecný hostitele vhodný pro hostování jakékoliv aplikace, včetně webových aplikací. Obecný hostitel nakonec nahradí webového hostitele.

Pro hostování ASP.NET Core *webové aplikace*, vývojáři měli použít webového hostitele na základě <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>. Pro hostování *mimo web apps*, vývojáři měli použít obecný hostitele na základě <xref:Microsoft.Extensions.Hosting.HostBuilder>.

<xref:fundamentals/host/hosted-services>  
Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.

<xref:fundamentals/configuration/platform-specific-configuration>  
Objevte, jak vylepšit aplikaci ASP.NET Core z odkazované nebo neodkazovaná sestavení pomocí <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementace.
