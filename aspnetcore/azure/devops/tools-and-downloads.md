---
title: Nástroje a soubory ke stažení – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nástroje a soubory ke stažení vyžadované pro DevOps s ASP.NET Core a Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 3242031f07f3fa344422db4591ea1a244b6723dd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625242"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="65966-103">Nástroje a soubory ke stažení</span><span class="sxs-lookup"><span data-stu-id="65966-103">Tools and downloads</span></span>

<span data-ttu-id="65966-104">Azure má několik rozhraní pro zřizování a správu prostředků, jako jsou [Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)a Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="65966-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="65966-105">Tato příručka přebírá minimalistý přístup a používá Azure Cloud Shell, kdykoli je to možné, abyste snížili požadované kroky.</span><span class="sxs-lookup"><span data-stu-id="65966-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="65966-106">Azure Portal je však nutné použít pro některé části.</span><span class="sxs-lookup"><span data-stu-id="65966-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65966-107">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="65966-107">Prerequisites</span></span>

<span data-ttu-id="65966-108">Jsou vyžadovány následující odběry:</span><span class="sxs-lookup"><span data-stu-id="65966-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="65966-109">Azure &mdash; Pokud účet nemáte, [Získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="65966-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/dotnet/).</span></span>
* <span data-ttu-id="65966-110">Azure DevOps Services &mdash; vaše předplatné Azure DevOps a organizace se vytvoří v kapitole 4.</span><span class="sxs-lookup"><span data-stu-id="65966-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="65966-111">GitHub &mdash; Pokud účet nemáte, [Zaregistrujte se zdarma](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="65966-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="65966-112">Jsou vyžadovány následující nástroje:</span><span class="sxs-lookup"><span data-stu-id="65966-112">The following tools are required:</span></span>

* <span data-ttu-id="65966-113">[Git](https://git-scm.com/downloads) &mdash; Pro tento průvodce se doporučuje základní porozumění Gitu.</span><span class="sxs-lookup"><span data-stu-id="65966-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="65966-114">Projděte si [dokumentaci k Gitu](https://git-scm.com/doc), konkrétně [vzdálené úložiště Git](https://git-scm.com/docs/git-remote) a [Git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="65966-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="65966-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; K sestavení a spuštění ukázkové aplikace se vyžaduje verze 2.1.300 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="65966-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="65966-116">Pokud je aplikace Visual Studio nainstalovaná s úlohou **vývoje .NET Core pro různé platformy** , .NET Core SDK je už nainstalovaná.</span><span class="sxs-lookup"><span data-stu-id="65966-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="65966-117">Ověřte instalaci .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="65966-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="65966-118">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="65966-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="65966-119">Doporučené nástroje (jenom Windows)</span><span class="sxs-lookup"><span data-stu-id="65966-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="65966-120">Robustní nástroje Azure sady [Visual Studio](https://visualstudio.microsoft.com)poskytují uživatelské rozhraní pro většinu funkcí popsaných v této příručce.</span><span class="sxs-lookup"><span data-stu-id="65966-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="65966-121">Všechny edice sady Visual Studio budou fungovat, včetně bezplatné edice Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="65966-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="65966-122">Kurzy jsou zapsány k předvedení vývoje, nasazení a DevOps s i bez sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="65966-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="65966-123">Ověřte, že je v aplikaci Visual Studio nainstalované následující [úlohy](/visualstudio/install/modify-visual-studio) :</span><span class="sxs-lookup"><span data-stu-id="65966-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="65966-124">Vývoj pro ASP.NET a web</span><span class="sxs-lookup"><span data-stu-id="65966-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="65966-125">Vývoj pro Azure</span><span class="sxs-lookup"><span data-stu-id="65966-125">Azure development</span></span>
  * <span data-ttu-id="65966-126">Vývoj multiplatformních aplikací pomocí rozhraní .NET Core</span><span class="sxs-lookup"><span data-stu-id="65966-126">.NET Core cross-platform development</span></span>
