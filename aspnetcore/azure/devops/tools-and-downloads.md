---
title: Nástroje a soubory ke stažení – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nástroje a soubory ke stažení vyžadované pro DevOps s ASP.NET Core a Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: e224121692a0965c66bd3b95b3fbf691867d5548
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012537"
---
# <a name="tools-and-downloads"></a>Nástroje a soubory ke stažení

Azure má několik rozhraní pro zřizování a správu prostředků, jako jsou [Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)a Visual Studio. Tato příručka přebírá minimalistý přístup a používá Azure Cloud Shell, kdykoli je to možné, abyste snížili požadované kroky. Azure Portal je však nutné použít pro některé části.

## <a name="prerequisites"></a>Požadavky

Jsou vyžadovány následující odběry:

* Azure &mdash; Pokud účet nemáte, [Získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/dotnet/).
* Azure DevOps Services &mdash; vaše předplatné Azure DevOps a organizace se vytvoří v kapitole 4.
* GitHub &mdash; Pokud účet nemáte, [Zaregistrujte se zdarma](https://github.com/join).

Jsou vyžadovány následující nástroje:

* [Git](https://git-scm.com/downloads) &mdash; Pro tento průvodce se doporučuje základní porozumění Gitu. Projděte si [dokumentaci k Gitu](https://git-scm.com/doc), konkrétně [vzdálené úložiště Git](https://git-scm.com/docs/git-remote) a [Git push](https://git-scm.com/docs/git-push).
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; K sestavení a spuštění ukázkové aplikace se vyžaduje verze 2.1.300 nebo novější. Pokud je aplikace Visual Studio nainstalovaná s úlohou **vývoje .NET Core pro různé platformy** , .NET Core SDK je už nainstalovaná.

    Ověřte instalaci .NET Core SDK. Otevřete příkazové prostředí a spusťte následující příkaz:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Doporučené nástroje (jenom Windows)

* Robustní nástroje Azure sady [Visual Studio](https://visualstudio.microsoft.com)poskytují uživatelské rozhraní pro většinu funkcí popsaných v této příručce. Všechny edice sady Visual Studio budou fungovat, včetně bezplatné edice Visual Studio Community Edition. Kurzy jsou zapsány k předvedení vývoje, nasazení a DevOps s i bez sady Visual Studio.

  Ověřte, že je v aplikaci Visual Studio nainstalované následující [úlohy](/visualstudio/install/modify-visual-studio) :

  * Vývoj pro ASP.NET a web
  * Vývoj pro Azure
  * Vývoj multiplatformních aplikací pomocí rozhraní .NET Core
