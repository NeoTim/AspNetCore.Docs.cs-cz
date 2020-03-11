---
title: Nástroje a soubory ke stažení – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nástroje a soubory ke stažení potřebné pro vývoj a provoz s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 25ce311373b0aaddfa3bc2728c39e503acbca69d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659486"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="666f3-103">Nástroje a soubory ke stažení</span><span class="sxs-lookup"><span data-stu-id="666f3-103">Tools and downloads</span></span>

<span data-ttu-id="666f3-104">Azure má několik rozhraní pro zřizování a správu prostředků, jako jsou [Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)a Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="666f3-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="666f3-105">Tato příručka používá minimalist přístup a používá Azure Cloud Shell pokaždé, když je možné snížit kroky.</span><span class="sxs-lookup"><span data-stu-id="666f3-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="666f3-106">Na webu Azure portal musí být však použít pro některé části.</span><span class="sxs-lookup"><span data-stu-id="666f3-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="666f3-107">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="666f3-107">Prerequisites</span></span>

<span data-ttu-id="666f3-108">Vyžadují se následující předplatná:</span><span class="sxs-lookup"><span data-stu-id="666f3-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="666f3-109">Azure &mdash; Pokud účet nemáte, [Získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="666f3-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="666f3-110">Azure DevOps Services &mdash; vaše předplatné Azure DevOps a organizace se vytvoří v kapitole 4.</span><span class="sxs-lookup"><span data-stu-id="666f3-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="666f3-111">&mdash; GitHubu Pokud účet nemáte, [Zaregistrujte se zdarma](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="666f3-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="666f3-112">Tyto nástroje jsou požadovány:</span><span class="sxs-lookup"><span data-stu-id="666f3-112">The following tools are required:</span></span>

* <span data-ttu-id="666f3-113">Pro tento průvodce se doporučuje pro [git &mdash; základní](https://git-scm.com/downloads) porozumění Gitu.</span><span class="sxs-lookup"><span data-stu-id="666f3-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="666f3-114">Projděte si [dokumentaci k Gitu](https://git-scm.com/doc), konkrétně [vzdálené úložiště Git](https://git-scm.com/docs/git-remote) a [Git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="666f3-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="666f3-115">Pro sestavení a spuštění ukázkové aplikace se vyžaduje [.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; verze 2.1.300 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="666f3-115">[.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="666f3-116">Pokud je aplikace Visual Studio nainstalovaná s úlohou **vývoje .NET Core pro různé platformy** , .NET Core SDK je už nainstalovaná.</span><span class="sxs-lookup"><span data-stu-id="666f3-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="666f3-117">Ověření instalace .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="666f3-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="666f3-118">Otevřete příkazové okno a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="666f3-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="666f3-119">Doporučené nástroje (jenom Windows)</span><span class="sxs-lookup"><span data-stu-id="666f3-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="666f3-120">Robustní nástroje Azure sady [Visual Studio](https://visualstudio.microsoft.com)poskytují uživatelské rozhraní pro většinu funkcí popsaných v této příručce.</span><span class="sxs-lookup"><span data-stu-id="666f3-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="666f3-121">Bude fungovat jakékoli edici sady Visual Studio, včetně bezplatná edice Visual Studio Community.</span><span class="sxs-lookup"><span data-stu-id="666f3-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="666f3-122">V kurzech se zapisují do ukazují vývoje, nasazení a DevOps s i bez sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="666f3-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="666f3-123">Ověřte, že je v aplikaci Visual Studio nainstalované následující [úlohy](/visualstudio/install/modify-visual-studio) :</span><span class="sxs-lookup"><span data-stu-id="666f3-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="666f3-124">Vývoj pro ASP.NET a web</span><span class="sxs-lookup"><span data-stu-id="666f3-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="666f3-125">Vývoj pro Azure</span><span class="sxs-lookup"><span data-stu-id="666f3-125">Azure development</span></span>
  * <span data-ttu-id="666f3-126">Vývoj aplikací pro různé platformy pomocí rozhraní .NET Core</span><span class="sxs-lookup"><span data-stu-id="666f3-126">.NET Core cross-platform development</span></span>
