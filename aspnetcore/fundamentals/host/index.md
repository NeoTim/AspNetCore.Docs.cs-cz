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
# <a name="web-host-and-generic-host-in-aspnet-core"></a><span data-ttu-id="e0cbd-103">Webového hostitele a obecné hostitele v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0cbd-103">Web Host and Generic Host in ASP.NET Core</span></span>

<span data-ttu-id="e0cbd-104">Konfigurace aplikací .NET a spouštění *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="e0cbd-104">.NET apps configure and launch a *host*.</span></span> <span data-ttu-id="e0cbd-105">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="e0cbd-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="e0cbd-106">Dva hostitele rozhraní API se dají používat:</span><span class="sxs-lookup"><span data-stu-id="e0cbd-106">Two host APIs are available for use:</span></span>

* <span data-ttu-id="e0cbd-107">[Webového hostitele](xref:fundamentals/host/web-host) &ndash; vhodná pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="e0cbd-107">[Web Host](xref:fundamentals/host/web-host) &ndash; Suitable for hosting web apps.</span></span>
* <span data-ttu-id="e0cbd-108">[Obecný hostitele](xref:fundamentals/host/generic-host) (ASP.NET Core 2.1 nebo novější) &ndash; vhodná pro hostování jiných webových aplikací (například aplikace, na kterých běží úlohy na pozadí).</span><span class="sxs-lookup"><span data-stu-id="e0cbd-108">[Generic Host](xref:fundamentals/host/generic-host) (ASP.NET Core 2.1 or later) &ndash; Suitable for hosting non-web apps (for example, apps that run background tasks).</span></span> <span data-ttu-id="e0cbd-109">V budoucí verzi bude obecný hostitele vhodný pro hostování jakékoliv aplikace, včetně webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="e0cbd-109">In a future release, the Generic Host will be suitable for hosting any kind of app, including web apps.</span></span> <span data-ttu-id="e0cbd-110">Obecný hostitel nakonec nahradí webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="e0cbd-110">The Generic Host will eventually replace the Web Host.</span></span>

<span data-ttu-id="e0cbd-111">Pro hostování ASP.NET Core *webové aplikace*, vývojáři měli použít webového hostitele na základě <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e0cbd-111">For hosting ASP.NET Core *web apps*, developers should use the Web Host based on <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>.</span></span> <span data-ttu-id="e0cbd-112">Pro hostování *mimo web apps*, vývojáři měli použít obecný hostitele na základě <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e0cbd-112">For hosting *non-web apps*, developers should use the Generic Host based on <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

<xref:fundamentals/host/hosted-services>  
<span data-ttu-id="e0cbd-113">Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0cbd-113">Learn how to implement background tasks with hosted services in ASP.NET Core.</span></span>

<xref:fundamentals/configuration/platform-specific-configuration>  
<span data-ttu-id="e0cbd-114">Objevte, jak vylepšit aplikaci ASP.NET Core z odkazované nebo neodkazovaná sestavení pomocí <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementace.</span><span class="sxs-lookup"><span data-stu-id="e0cbd-114">Discover how to enhance an ASP.NET Core app from a referenced or unreferenced assembly using an <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation.</span></span>
