---
title: Ukázky ověřování pro ASP.NET Core
author: rick-anderson
description: Obsahuje odkazy na ukázky ověřování v úložišti ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: 7b3c911d60ad4737ebd12ce6f7628ad624b11658
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902934"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="8f870-103">Ukázky ověřování pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f870-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="8f870-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8f870-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8f870-105">[ASP.NET Core úložiště](https://github.com/aspnet/AspNetCore) obsahuje následující ukázky ověřování *AspNetCore/src/Security/samples* složky:</span><span class="sxs-lookup"><span data-stu-id="8f870-105">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="8f870-106">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="8f870-106">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="8f870-107">Ověřování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="8f870-107">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="8f870-108">Vlastní zásady pro poskytovatele – IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="8f870-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="8f870-109">Schémata ověřování pro dynamické a možnosti</span><span class="sxs-lookup"><span data-stu-id="8f870-109">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="8f870-110">Externí deklarace identity</span><span class="sxs-lookup"><span data-stu-id="8f870-110">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="8f870-111">Výběr mezi souboru cookie a další schéma ověřování na základě daného požadavku</span><span class="sxs-lookup"><span data-stu-id="8f870-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="8f870-112">Omezí přístup na statické soubory</span><span class="sxs-lookup"><span data-stu-id="8f870-112">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="8f870-113">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="8f870-113">Run the samples</span></span>

* <span data-ttu-id="8f870-114">Vyberte [větev](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="8f870-114">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="8f870-115">Třeba `release/2.2`.</span><span class="sxs-lookup"><span data-stu-id="8f870-115">For example, `release/2.2`</span></span>
* <span data-ttu-id="8f870-116">Klonovat nebo stáhnout [ASP.NET Core úložiště](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="8f870-116">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="8f870-117">Ověřte instalaci [.NET Core SDK](https://www.microsoft.com/net/download/all) verzi, která odpovídá klon úložiště pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8f870-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="8f870-118">Přejděte na ukázky v *AspNetCore/src/Security/samples* a spusťte ukázku pomocí `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8f870-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>
