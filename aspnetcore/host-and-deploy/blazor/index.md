---
title: Hostitelství a nasazení Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 774fbb6fdaab14a015db4fb39de2e1ea73a1837b
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982637"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="8070a-103">Hostitelství a nasazení Blazor</span><span class="sxs-lookup"><span data-stu-id="8070a-103">Host and deploy Blazor</span></span>

<span data-ttu-id="8070a-104">Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8070a-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="8070a-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="8070a-105">Publish the app</span></span>

<span data-ttu-id="8070a-106">Aplikace budou publikovány pro nasazení v rámci konfigurace verze se [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu.</span><span class="sxs-lookup"><span data-stu-id="8070a-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="8070a-107">Integrované vývojové prostředí (IDE) může zpracovat provádění `dotnet publish` příkaz automaticky pomocí jeho integrované funkce pro publikování, takže nemusí být potřeba ručně spusťte příkaz z příkazového řádku v závislosti na vývoj nástroje používané.</span><span class="sxs-lookup"><span data-stu-id="8070a-107">An integrated development environment (IDE) may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="8070a-108">`dotnet publish` aktivační události [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a [sestavení](/dotnet/core/tools/dotnet-build) projektu před vytvořením prostředků pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="8070a-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="8070a-109">Jako součást procesu sestavení jsou odebrány nepoužívané metod a sestavení snížit velikost ke stažení aplikace a časy načtení.</span><span class="sxs-lookup"><span data-stu-id="8070a-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="8070a-110">Nasazení se vytvoří v */bin/vydání / {CÍLOVÁ ARCHITEKTURA} / publish* složky.</span><span class="sxs-lookup"><span data-stu-id="8070a-110">The deployment is created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="8070a-111">Prostředky v *publikovat* složky jsou nasazené na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="8070a-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="8070a-112">Nasazení může být ruční nebo automatizované proces v závislosti na vývojové nástroje, které používá.</span><span class="sxs-lookup"><span data-stu-id="8070a-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="8070a-113">Nasazení</span><span class="sxs-lookup"><span data-stu-id="8070a-113">Deployment</span></span>

<span data-ttu-id="8070a-114">Pokyny k nasazení naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="8070a-114">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
