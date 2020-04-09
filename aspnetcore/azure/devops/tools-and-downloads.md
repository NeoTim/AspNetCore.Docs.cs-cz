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
# <a name="tools-and-downloads"></a><span data-ttu-id="346c2-103">Nástroje a soubory ke stažení</span><span class="sxs-lookup"><span data-stu-id="346c2-103">Tools and downloads</span></span>

<span data-ttu-id="346c2-104">Azure má několik rozhraní pro zřizování a správu prostředků, jako je [například portál Azure](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)a Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="346c2-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="346c2-105">Tato příručka má minimalistický přístup a používá Azure Cloud Shell kdykoli je to možné snížit požadované kroky.</span><span class="sxs-lookup"><span data-stu-id="346c2-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="346c2-106">Portál Azure však musí být použit pro některé části.</span><span class="sxs-lookup"><span data-stu-id="346c2-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="346c2-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="346c2-107">Prerequisites</span></span>

<span data-ttu-id="346c2-108">Jsou vyžadována následující předplatná:</span><span class="sxs-lookup"><span data-stu-id="346c2-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="346c2-109">Azure &mdash; Pokud nemáte účet, [získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="346c2-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="346c2-110">Služby Azure DevOps, &mdash; vaše předplatné a organizace Azure DevOps, se vytvoří v kapitole 4.</span><span class="sxs-lookup"><span data-stu-id="346c2-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="346c2-111">GitHub &mdash; Pokud nemáte účet, [zaregistrujte se zdarma](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="346c2-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="346c2-112">Jsou vyžadovány následující nástroje:</span><span class="sxs-lookup"><span data-stu-id="346c2-112">The following tools are required:</span></span>

* <span data-ttu-id="346c2-113">[Git](https://git-scm.com/downloads) &mdash; Pro tuto příručku se doporučuje základní znalost Gitu.</span><span class="sxs-lookup"><span data-stu-id="346c2-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="346c2-114">Projděte si [dokumentaci gitu](https://git-scm.com/doc), konkrétně [git remote](https://git-scm.com/docs/git-remote) a [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="346c2-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="346c2-115">K sestavení a spuštění ukázkové aplikace je potřeba sada [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; verze 2.1.300 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="346c2-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="346c2-116">Pokud je visual studio nainstalováno s **úlohou vývoje napříč platformami .NET Core,** je již nainstalována sada .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="346c2-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="346c2-117">Ověřte instalaci sady .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="346c2-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="346c2-118">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="346c2-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="346c2-119">Doporučené nástroje (pouze windows)</span><span class="sxs-lookup"><span data-stu-id="346c2-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="346c2-120">Robustní nástroje Visual [Studio](https://visualstudio.microsoft.com)Azure poskytují grafické uživatelské rozhraní pro většinu funkcí popsaných v této příručce.</span><span class="sxs-lookup"><span data-stu-id="346c2-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="346c2-121">Bude fungovat libovolná edice sady Visual Studio, včetně bezplatné edice Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="346c2-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="346c2-122">Kurzy jsou napsány k předvedení vývoje, nasazení a DevOps s visual studio i bez něj.</span><span class="sxs-lookup"><span data-stu-id="346c2-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="346c2-123">Zkontrolujte, zda má Visual Studio nainstalovanou následující [úlohu:](/visualstudio/install/modify-visual-studio)</span><span class="sxs-lookup"><span data-stu-id="346c2-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="346c2-124">Vývoj pro ASP.NET a web</span><span class="sxs-lookup"><span data-stu-id="346c2-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="346c2-125">Vývoj pro Azure</span><span class="sxs-lookup"><span data-stu-id="346c2-125">Azure development</span></span>
  * <span data-ttu-id="346c2-126">Vývoj aplikací pro různé platformy pomocí rozhraní .NET Core</span><span class="sxs-lookup"><span data-stu-id="346c2-126">.NET Core cross-platform development</span></span>
