---
title: Offline soubor aplikace (app_offline.htm)
author: rick-anderson
description: Přečtěte si, jak funguje offline soubor aplikace ( `app_offline.htm` ) s modulem ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 95dfadd084af5909fee754308ad5d65f54d4875d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755273"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="00808-103">Offline soubor aplikace ( `app_offline.htm` )</span><span class="sxs-lookup"><span data-stu-id="00808-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="00808-104">Offline soubor aplikace ( `app_offline.htm` ) používá modul ASP.NET Core k ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="00808-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="00808-105">Pokud `app_offline.htm` se v kořenovém adresáři aplikace zjistí soubor s názvem, modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="00808-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="00808-106">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core zastaví běžící proces.</span><span class="sxs-lookup"><span data-stu-id="00808-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="00808-107">I když `app_offline.htm` je soubor přítomen, ASP.NET Core modul reaguje na požadavky odesláním obsahu `app_offline.htm` souboru zpět.</span><span class="sxs-lookup"><span data-stu-id="00808-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="00808-108">Po `app_offline.htm` Odebrání souboru se aplikace spustí dalším požadavkem.</span><span class="sxs-lookup"><span data-stu-id="00808-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="00808-109">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="00808-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="00808-110">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="00808-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="00808-111">Uzamčené soubory nasazení</span><span class="sxs-lookup"><span data-stu-id="00808-111">Locked deployment files</span></span>

<span data-ttu-id="00808-112">Soubory ve složce pro nasazení jsou zamčené, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="00808-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="00808-113">Uzamčené soubory nejde během nasazování přepsat.</span><span class="sxs-lookup"><span data-stu-id="00808-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="00808-114">`app_offline.htm` je primárním mechanismem pro uvolnění zamčených souborů.</span><span class="sxs-lookup"><span data-stu-id="00808-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="00808-115">`app_offline.htm` ji používá Nasazení webu ke správnému zastavení a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="00808-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="00808-116">`app_offline.htm` dá se ručně použít ke spuštění a zastavení aplikace (vyžaduje PowerShell 5 nebo novější):</span><span class="sxs-lookup"><span data-stu-id="00808-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

<span data-ttu-id="00808-117">V předchozím skriptu prostředí PowerShell:</span><span class="sxs-lookup"><span data-stu-id="00808-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="00808-118">Zástupný symbol `{PATH TO APP}` je cesta k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="00808-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="00808-119">`New-Item`Příkaz zastaví fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="00808-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="00808-120">`Remove-Item`Příkaz spustí fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="00808-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="00808-121">Příkazy mezi `New-Item` příkazem a `Remove-Item` příkazem poskytuje vývojář k nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="00808-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="00808-122">Soubory je taky možné odemknout ručním zastavením fondu aplikací ve Správci služby IIS na serveru.</span><span class="sxs-lookup"><span data-stu-id="00808-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="00808-123">Nepoužívejte `app_offine.htm` soubor při použití Správce služby IIS k zastavení a restartu fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="00808-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
