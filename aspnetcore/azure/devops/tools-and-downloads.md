---
title: Nástroje a soubory ke stažení – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nástroje a soubory ke stažení potřebné pro vývoj a provoz s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511142"
---
# <a name="tools-and-downloads"></a>Nástroje a soubory ke stažení

Azure má několik rozhraní pro zřizování a správu prostředků, jako jsou [Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)a Visual Studio. Tato příručka používá minimalist přístup a používá Azure Cloud Shell pokaždé, když je možné snížit kroky. Na webu Azure portal musí být však použít pro některé části.

## <a name="prerequisites"></a>Předpoklady

Vyžadují se následující předplatná:

* Azure &mdash; Pokud účet nemáte, [Získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Azure DevOps Services &mdash; vaše předplatné Azure DevOps a organizace se vytvoří v kapitole 4.
* &mdash; GitHubu Pokud účet nemáte, [Zaregistrujte se zdarma](https://github.com/join).

Tyto nástroje jsou požadovány:

* Pro tento průvodce se doporučuje pro [git &mdash; základní](https://git-scm.com/downloads) porozumění Gitu. Projděte si [dokumentaci k Gitu](https://git-scm.com/doc), konkrétně [vzdálené úložiště Git](https://git-scm.com/docs/git-remote) a [Git push](https://git-scm.com/docs/git-push).
* Pro sestavení a spuštění ukázkové aplikace se vyžaduje [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; verze 2.1.300 nebo novější. Pokud je aplikace Visual Studio nainstalovaná s úlohou **vývoje .NET Core pro různé platformy** , .NET Core SDK je už nainstalovaná.

    Ověření instalace .NET Core SDK. Otevřete příkazové okno a spusťte následující příkaz:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Doporučené nástroje (jenom Windows)

* Robustní nástroje Azure sady [Visual Studio](https://visualstudio.microsoft.com)poskytují uživatelské rozhraní pro většinu funkcí popsaných v této příručce. Bude fungovat jakékoli edici sady Visual Studio, včetně bezplatná edice Visual Studio Community. V kurzech se zapisují do ukazují vývoje, nasazení a DevOps s i bez sady Visual Studio.

  Ověřte, že je v aplikaci Visual Studio nainstalované následující [úlohy](/visualstudio/install/modify-visual-studio) :

  * Vývoj pro ASP.NET a web
  * Vývoj pro Azure
  * Vývoj aplikací pro různé platformy pomocí rozhraní .NET Core
