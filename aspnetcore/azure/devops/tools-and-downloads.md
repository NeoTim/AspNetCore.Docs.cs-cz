---
title: Nástroje a soubory ke stažení – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nástroje a soubory ke stažení potřebné pro vývoj a provoz s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 0c64e723f1b912323103f201a66c1edaeccdcc2d
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284342"
---
# <a name="tools-and-downloads"></a>Nástroje a soubory ke stažení

Azure nabízí několik rozhraní pro zřizování a správu prostředků, jako [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](/cli/azure/), [prostředí Azure PowerShell](/powershell/azure/overview), [cloudu Azure Prostředí](https://shell.azure.com/bash)a sady Visual Studio. Tato příručka používá minimalist přístup a používá Azure Cloud Shell pokaždé, když je možné snížit kroky. Na webu Azure portal musí být však použít pro některé části.

## <a name="prerequisites"></a>Požadavky

Vyžadují se následující předplatná:

* Azure &mdash; Pokud nemáte účet, [získat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Služby Azure DevOps &mdash; vaše předplatné Azure DevOps a organizace se vytvoří v kapitole 4.
* GitHub &mdash; Pokud nemáte účet, [ZDARMA zaregistrovat](https://github.com/join).

Tyto nástroje jsou požadovány:

* [Git](https://git-scm.com/downloads) &mdash; základní principy Git se doporučuje pro tohoto průvodce. Zkontrolujte [dokumentace pro Git](https://git-scm.com/doc), konkrétně [vzdálené úložiště git](https://git-scm.com/docs/git-remote) a [git push](https://git-scm.com/docs/git-push).
* [Sada .NET core SDK](https://www.microsoft.com/net/download/) &mdash; verze 2.1.300 nebo novější je potřebné k sestavení a spuštění ukázkové aplikace. Pokud je nainstalována aplikace Visual Studio s **vývoj pro různé platformy .NET Core** již je nainstalovaná úloha, sada .NET Core SDK.

    Ověření instalace .NET Core SDK. Otevřete příkazové okno a spusťte následující příkaz:

    ```console
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Doporučené nástroje (jenom Windows)

* [Visual Studio](https://www.visualstudio.com/)uživatele robustní nástroje Azure poskytuje grafické uživatelské rozhraní pro většinu funkcí popsaných v této příručce. Bude fungovat jakékoli edici sady Visual Studio, včetně bezplatná edice Visual Studio Community. V kurzech se zapisují do ukazují vývoje, nasazení a DevOps s i bez sady Visual Studio.

  Potvrďte, že sada Visual Studio obsahuje následující [úlohy](/visualstudio/install/modify-visual-studio) nainstalovaný:

  * Vývoj pro ASP.NET a web
  * Vývoj pro Azure
  * Vývoj pro různé platformy .NET core
