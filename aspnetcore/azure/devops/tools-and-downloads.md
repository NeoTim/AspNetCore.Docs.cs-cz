---
title: Nástroje a soubory ke stažení – DevOps s ASP.NET core a Azure
author: CamSoper
description: Nástroje a soubory ke stažení potřebné pro DevOps s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511142"
---
# <a name="tools-and-downloads"></a>Nástroje a soubory ke stažení

Azure má několik rozhraní pro zřizování a správu prostředků, jako je [například portál Azure](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)a Visual Studio. Tato příručka má minimalistický přístup a používá Azure Cloud Shell kdykoli je to možné snížit požadované kroky. Portál Azure však musí být použit pro některé části.

## <a name="prerequisites"></a>Požadavky

Jsou vyžadována následující předplatná:

* Azure &mdash; Pokud nemáte účet, [získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Služby Azure DevOps, &mdash; vaše předplatné a organizace Azure DevOps, se vytvoří v kapitole 4.
* GitHub &mdash; Pokud nemáte účet, [zaregistrujte se zdarma](https://github.com/join).

Jsou vyžadovány následující nástroje:

* [Git](https://git-scm.com/downloads) &mdash; Pro tuto příručku se doporučuje základní znalost Gitu. Projděte si [dokumentaci gitu](https://git-scm.com/doc), konkrétně [git remote](https://git-scm.com/docs/git-remote) a [git push](https://git-scm.com/docs/git-push).
* K sestavení a spuštění ukázkové aplikace je potřeba sada [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; verze 2.1.300 nebo novější. Pokud je visual studio nainstalováno s **úlohou vývoje napříč platformami .NET Core,** je již nainstalována sada .NET Core SDK.

    Ověřte instalaci sady .NET Core SDK. Otevřete příkazové prostředí a spusťte následující příkaz:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Doporučené nástroje (pouze windows)

* Robustní nástroje Visual [Studio](https://visualstudio.microsoft.com)Azure poskytují grafické uživatelské rozhraní pro většinu funkcí popsaných v této příručce. Bude fungovat libovolná edice sady Visual Studio, včetně bezplatné edice Visual Studio Community Edition. Kurzy jsou napsány k předvedení vývoje, nasazení a DevOps s visual studio i bez něj.

  Zkontrolujte, zda má Visual Studio nainstalovanou následující [úlohu:](/visualstudio/install/modify-visual-studio)

  * Vývoj pro ASP.NET a web
  * Vývoj pro Azure
  * Vývoj aplikací pro různé platformy pomocí rozhraní .NET Core
