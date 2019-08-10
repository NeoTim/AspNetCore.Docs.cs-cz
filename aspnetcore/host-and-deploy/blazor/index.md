---
title: Hostování a nasazení ASP.NET Core Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: d18abbf33c71dca5130bfc6b503b46c1d5bce537
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68913929"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="59c96-103">Hostování a nasazení ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="59c96-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="59c96-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="59c96-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="59c96-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="59c96-105">Publish the app</span></span>

<span data-ttu-id="59c96-106">Aplikace jsou publikované pro nasazení v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="59c96-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="59c96-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59c96-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="59c96-108">V navigačním panelu vyberte **sestavení** > **publikovat {aplikace}** .</span><span class="sxs-lookup"><span data-stu-id="59c96-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="59c96-109">Vyberte *cíl publikování*.</span><span class="sxs-lookup"><span data-stu-id="59c96-109">Select the *publish target*.</span></span> <span data-ttu-id="59c96-110">Chcete-li publikovat místně, vyberte **Složka**.</span><span class="sxs-lookup"><span data-stu-id="59c96-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="59c96-111">Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění.</span><span class="sxs-lookup"><span data-stu-id="59c96-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="59c96-112">Vyberte tlačítko **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="59c96-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="59c96-113">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="59c96-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="59c96-114">K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="59c96-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="59c96-115">Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt.</span><span class="sxs-lookup"><span data-stu-id="59c96-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="59c96-116">V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.</span><span class="sxs-lookup"><span data-stu-id="59c96-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="59c96-117">Aplikace Blazor na straně klienta je publikovaná ve složce */bin/Release/{Target Framework}/PUBLISH/{Assembly Name}/DIST* .</span><span class="sxs-lookup"><span data-stu-id="59c96-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="59c96-118">Blazor aplikace na straně serveru je publikovaná do složky */Publish/bin/Release/{Target Framework}* .</span><span class="sxs-lookup"><span data-stu-id="59c96-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="59c96-119">Prostředky ve složce jsou nasazeny na webový server.</span><span class="sxs-lookup"><span data-stu-id="59c96-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="59c96-120">Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.</span><span class="sxs-lookup"><span data-stu-id="59c96-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="59c96-121">Nasazení</span><span class="sxs-lookup"><span data-stu-id="59c96-121">Deployment</span></span>

<span data-ttu-id="59c96-122">Pokyny k nasazení najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="59c96-122">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a><span data-ttu-id="59c96-123">Blazor hostování bez serveru pomocí Azure Storage</span><span class="sxs-lookup"><span data-stu-id="59c96-123">Blazor serverless hosting with Azure Storage</span></span>

<span data-ttu-id="59c96-124">Blazor aplikace na straně klienta je možné obsluhovat z [Azure Storage](https://azure.microsoft.com/services/storage/) jako statický obsah přímo z kontejneru úložiště.</span><span class="sxs-lookup"><span data-stu-id="59c96-124">Blazor client-side apps can be served from [Azure Storage](https://azure.microsoft.com/services/storage/) as static content directly from a storage container.</span></span>

<span data-ttu-id="59c96-125">Další informace najdete v tématu [hostování a nasazení ASP.NET Core Blazor na straně klienta (samostatné nasazení): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="59c96-125">For more information, see [Host and deploy ASP.NET Core Blazor client-side (Standalone deployment): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span></span>
