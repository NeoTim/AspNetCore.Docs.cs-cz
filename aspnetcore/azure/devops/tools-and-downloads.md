---
title: Nástroje a soubory ke stažení – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nástroje a soubory ke stažení vyžadované pro DevOps s ASP.NET Core a Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: ed8aee214ff9b9e941aeea01887882c3bdfc56a7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400294"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="994a2-103">Nástroje a soubory ke stažení</span><span class="sxs-lookup"><span data-stu-id="994a2-103">Tools and downloads</span></span>

<span data-ttu-id="994a2-104">Azure má několik rozhraní pro zřizování a správu prostředků, jako jsou [Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)a Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="994a2-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="994a2-105">Tato příručka přebírá minimalistý přístup a používá Azure Cloud Shell, kdykoli je to možné, abyste snížili požadované kroky.</span><span class="sxs-lookup"><span data-stu-id="994a2-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="994a2-106">Azure Portal je však nutné použít pro některé části.</span><span class="sxs-lookup"><span data-stu-id="994a2-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="994a2-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="994a2-107">Prerequisites</span></span>

<span data-ttu-id="994a2-108">Jsou vyžadovány následující odběry:</span><span class="sxs-lookup"><span data-stu-id="994a2-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="994a2-109">Azure &mdash; Pokud účet nemáte, [Získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="994a2-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/dotnet/).</span></span>
* <span data-ttu-id="994a2-110">Azure DevOps Services &mdash; vaše předplatné Azure DevOps a organizace se vytvoří v kapitole 4.</span><span class="sxs-lookup"><span data-stu-id="994a2-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="994a2-111">GitHub &mdash; Pokud účet nemáte, [Zaregistrujte se zdarma](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="994a2-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="994a2-112">Jsou vyžadovány následující nástroje:</span><span class="sxs-lookup"><span data-stu-id="994a2-112">The following tools are required:</span></span>

* <span data-ttu-id="994a2-113">[Git](https://git-scm.com/downloads) &mdash; Pro tento průvodce se doporučuje základní porozumění Gitu.</span><span class="sxs-lookup"><span data-stu-id="994a2-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="994a2-114">Projděte si [dokumentaci k Gitu](https://git-scm.com/doc), konkrétně [vzdálené úložiště Git](https://git-scm.com/docs/git-remote) a [Git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="994a2-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="994a2-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; K sestavení a spuštění ukázkové aplikace se vyžaduje verze 2.1.300 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="994a2-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="994a2-116">Pokud je aplikace Visual Studio nainstalovaná s úlohou **vývoje .NET Core pro různé platformy** , .NET Core SDK je už nainstalovaná.</span><span class="sxs-lookup"><span data-stu-id="994a2-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="994a2-117">Ověřte instalaci .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="994a2-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="994a2-118">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="994a2-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="994a2-119">Doporučené nástroje (jenom Windows)</span><span class="sxs-lookup"><span data-stu-id="994a2-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="994a2-120">Robustní nástroje Azure sady [Visual Studio](https://visualstudio.microsoft.com)poskytují uživatelské rozhraní pro většinu funkcí popsaných v této příručce.</span><span class="sxs-lookup"><span data-stu-id="994a2-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="994a2-121">Všechny edice sady Visual Studio budou fungovat, včetně bezplatné edice Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="994a2-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="994a2-122">Kurzy jsou zapsány k předvedení vývoje, nasazení a DevOps s i bez sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="994a2-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="994a2-123">Ověřte, že je v aplikaci Visual Studio nainstalované následující [úlohy](/visualstudio/install/modify-visual-studio) :</span><span class="sxs-lookup"><span data-stu-id="994a2-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="994a2-124">Vývoj pro ASP.NET a web</span><span class="sxs-lookup"><span data-stu-id="994a2-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="994a2-125">Vývoj pro Azure</span><span class="sxs-lookup"><span data-stu-id="994a2-125">Azure development</span></span>
  * <span data-ttu-id="994a2-126">Vývoj aplikací pro různé platformy pomocí rozhraní .NET Core</span><span class="sxs-lookup"><span data-stu-id="994a2-126">.NET Core cross-platform development</span></span>
