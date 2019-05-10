---
title: Hostitelství a nasazení Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: c8a65b08582102af9129cf71ac4a108a905e49fc
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085537"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="b0d7e-103">Hostitelství a nasazení Blazor</span><span class="sxs-lookup"><span data-stu-id="b0d7e-103">Host and deploy Blazor</span></span>

<span data-ttu-id="b0d7e-104">Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b0d7e-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="b0d7e-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="b0d7e-105">Publish the app</span></span>

<span data-ttu-id="b0d7e-106">Aplikace se publikují v konfiguraci vydané verze pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b0d7e-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0d7e-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b0d7e-108">Vyberte **sestavení** > **publikování {aplikace}** z navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="b0d7e-109">Vyberte *publikovat cílové*.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-109">Select the *publish target*.</span></span> <span data-ttu-id="b0d7e-110">Chcete-li publikovat místně, vyberte **složky**.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="b0d7e-111">Přijměte výchozí umístění v **zvolte složku** pole nebo zadejte jiné umístění.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="b0d7e-112">Vyberte tlačítko **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-112">Select the **Publish** button.</span></span>


# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="b0d7e-113">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b0d7e-113">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="b0d7e-114">Použití [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz pro publikování aplikací s konfiguraci vydané verze:</span><span class="sxs-lookup"><span data-stu-id="b0d7e-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="b0d7e-115">Publikování aktivační události aplikace [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a [sestavení](/dotnet/core/tools/dotnet-build) projektu před vytvořením prostředků pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="b0d7e-116">Jako součást procesu sestavení jsou odebrány nepoužívané metod a sestavení snížit velikost ke stažení aplikace a časy načtení.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="b0d7e-117">Aplikace na straně klienta Blazor publikovaná */bin/vydání / {CÍLOVÁ ARCHITEKTURA} / dist* složky.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/dist* folder.</span></span> <span data-ttu-id="b0d7e-118">Aplikace na straně serveru Blazor publikovaná */bin/vydání / {CÍLOVÁ ARCHITEKTURA} / publish* složky.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="b0d7e-119">Prostředky ve složce jsou nasazené na webový server.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="b0d7e-120">Nasazení může být ruční nebo automatizované proces v závislosti na vývojové nástroje, které používá.</span><span class="sxs-lookup"><span data-stu-id="b0d7e-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="b0d7e-121">Nasazení</span><span class="sxs-lookup"><span data-stu-id="b0d7e-121">Deployment</span></span>

<span data-ttu-id="b0d7e-122">Pokyny k nasazení naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b0d7e-122">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
