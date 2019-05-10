---
title: Nástroje a soubory ke stažení – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nástroje a soubory ke stažení potřebné pro vývoj a provoz s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: ae4827f735274405021e5ee539d1029b7ddb9553
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898614"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="3a7e1-103">Nástroje a soubory ke stažení</span><span class="sxs-lookup"><span data-stu-id="3a7e1-103">Tools and downloads</span></span>

<span data-ttu-id="3a7e1-104">Azure nabízí několik rozhraní pro zřizování a správu prostředků, jako [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](/cli/azure/), [prostředí Azure PowerShell](/powershell/azure/overview), [cloudu Azure Prostředí](https://shell.azure.com/bash)a sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="3a7e1-105">Tato příručka používá minimalist přístup a používá Azure Cloud Shell pokaždé, když je možné snížit kroky.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="3a7e1-106">Na webu Azure portal musí být však použít pro některé části.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a7e1-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3a7e1-107">Prerequisites</span></span>

<span data-ttu-id="3a7e1-108">Vyžadují se následující předplatná:</span><span class="sxs-lookup"><span data-stu-id="3a7e1-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="3a7e1-109">Azure &mdash; Pokud nemáte účet, [získat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="3a7e1-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="3a7e1-110">Služby Azure DevOps &mdash; vaše předplatné Azure DevOps a organizace se vytvoří v kapitole 4.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="3a7e1-111">GitHub &mdash; Pokud nemáte účet, [ZDARMA zaregistrovat](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="3a7e1-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="3a7e1-112">Tyto nástroje jsou požadovány:</span><span class="sxs-lookup"><span data-stu-id="3a7e1-112">The following tools are required:</span></span>

* <span data-ttu-id="3a7e1-113">[Git](https://git-scm.com/downloads) &mdash; základní principy Git se doporučuje pro tohoto průvodce.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="3a7e1-114">Zkontrolujte [dokumentace pro Git](https://git-scm.com/doc), konkrétně [vzdálené úložiště git](https://git-scm.com/docs/git-remote) a [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="3a7e1-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="3a7e1-115">[Sada .NET core SDK](https://www.microsoft.com/net/download/) &mdash; verze 2.1.300 nebo novější je potřebné k sestavení a spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-115">[.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="3a7e1-116">Pokud je nainstalována aplikace Visual Studio s **vývoj pro různé platformy .NET Core** již je nainstalovaná úloha, sada .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="3a7e1-117">Ověření instalace .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="3a7e1-118">Otevřete příkazové okno a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3a7e1-118">Open a command shell, and run the following command:</span></span>

    ```console
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="3a7e1-119">Doporučené nástroje (jenom Windows)</span><span class="sxs-lookup"><span data-stu-id="3a7e1-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="3a7e1-120">[Visual Studio](https://visualstudio.microsoft.com)uživatele robustní nástroje Azure poskytuje grafické uživatelské rozhraní pro většinu funkcí popsaných v této příručce.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="3a7e1-121">Bude fungovat jakékoli edici sady Visual Studio, včetně bezplatná edice Visual Studio Community.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="3a7e1-122">V kurzech se zapisují do ukazují vývoje, nasazení a DevOps s i bez sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3a7e1-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="3a7e1-123">Potvrďte, že sada Visual Studio obsahuje následující [úlohy](/visualstudio/install/modify-visual-studio) nainstalovaný:</span><span class="sxs-lookup"><span data-stu-id="3a7e1-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="3a7e1-124">Vývoj pro ASP.NET a web</span><span class="sxs-lookup"><span data-stu-id="3a7e1-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="3a7e1-125">Vývoj pro Azure</span><span class="sxs-lookup"><span data-stu-id="3a7e1-125">Azure development</span></span>
  * <span data-ttu-id="3a7e1-126">Vývoj pro různé platformy .NET core</span><span class="sxs-lookup"><span data-stu-id="3a7e1-126">.NET Core cross-platform development</span></span>
