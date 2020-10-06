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
# <a name="app-offline-file-app_offlinehtm"></a>Offline soubor aplikace ( `app_offline.htm` )

Offline soubor aplikace ( `app_offline.htm` ) používá modul ASP.NET Core k ukončení aplikace.

Pokud `app_offline.htm` se v kořenovém adresáři aplikace zjistí soubor s názvem, modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků. Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core zastaví běžící proces.

I když `app_offline.htm` je soubor přítomen, ASP.NET Core modul reaguje na požadavky odesláním obsahu `app_offline.htm` souboru zpět. Po `app_offline.htm` Odebrání souboru se aplikace spustí dalším požadavkem.

Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení. Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.

## <a name="locked-deployment-files"></a>Uzamčené soubory nasazení

Soubory ve složce pro nasazení jsou zamčené, když je aplikace spuštěná. Uzamčené soubory nejde během nasazování přepsat.

`app_offline.htm` je primárním mechanismem pro uvolnění zamčených souborů. `app_offline.htm` ji používá Nasazení webu ke správnému zastavení a spuštění aplikace.

`app_offline.htm` dá se ručně použít ke spuštění a zastavení aplikace (vyžaduje PowerShell 5 nebo novější):

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

V předchozím skriptu prostředí PowerShell:

* Zástupný symbol `{PATH TO APP}` je cesta k aplikaci.
* `New-Item`Příkaz zastaví fond aplikací.
* `Remove-Item`Příkaz spustí fond aplikací.
* Příkazy mezi `New-Item` příkazem a `Remove-Item` příkazem poskytuje vývojář k nasazení aplikace.

Soubory je taky možné odemknout ručním zastavením fondu aplikací ve Správci služby IIS na serveru. Nepoužívejte `app_offine.htm` soubor při použití Správce služby IIS k zastavení a restartu fondu aplikací.
