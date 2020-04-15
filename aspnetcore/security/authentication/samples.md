---
title: Ukázky ověřování pro ASP.NET core
author: rick-anderson
description: Obsahuje odkazy na ukázky ověřování v úložišti ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308212"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="e14f1-103">Ukázky ověřování pro ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="e14f1-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="e14f1-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e14f1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e14f1-105">Úložiště ASP.NET Core obsahuje následující [ukázky](https://github.com/dotnet/AspNetCore) ověřování ve složce *AspNetCore/src/Security/samples:*</span><span class="sxs-lookup"><span data-stu-id="e14f1-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="e14f1-106">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="e14f1-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="e14f1-107">Ověřování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="e14f1-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="e14f1-108">Zprostředkovatel vlastních zásad - IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="e14f1-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="e14f1-109">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="e14f1-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="e14f1-110">Externí pohledávky</span><span class="sxs-lookup"><span data-stu-id="e14f1-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="e14f1-111">Výběr mezi souborem cookie a jiným schématem ověřování na základě žádosti</span><span class="sxs-lookup"><span data-stu-id="e14f1-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="e14f1-112">Omezuje přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="e14f1-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="e14f1-113">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="e14f1-113">Run the samples</span></span>

* <span data-ttu-id="e14f1-114">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e14f1-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="e14f1-115">Například `release/3.1`.</span><span class="sxs-lookup"><span data-stu-id="e14f1-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="e14f1-116">Klonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e14f1-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="e14f1-117">Ověřte, zda jste nainstalovali verzi [sady .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) odpovídající klonování úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e14f1-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="e14f1-118">Přejděte na ukázku v *aspNetCore/src/Security/samples* a spusťte vzorek s . `dotnet run`</span><span class="sxs-lookup"><span data-stu-id="e14f1-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e14f1-119">Úložiště ASP.NET Core obsahuje následující [ukázky](https://github.com/dotnet/AspNetCore) ověřování ve složce *AspNetCore/src/Security/samples:*</span><span class="sxs-lookup"><span data-stu-id="e14f1-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="e14f1-120">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="e14f1-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="e14f1-121">Ověřování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="e14f1-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="e14f1-122">Zprostředkovatel vlastních zásad - IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="e14f1-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="e14f1-123">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="e14f1-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="e14f1-124">Externí pohledávky</span><span class="sxs-lookup"><span data-stu-id="e14f1-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="e14f1-125">Výběr mezi souborem cookie a jiným schématem ověřování na základě žádosti</span><span class="sxs-lookup"><span data-stu-id="e14f1-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="e14f1-126">Omezuje přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="e14f1-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="e14f1-127">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="e14f1-127">Run the samples</span></span>

* <span data-ttu-id="e14f1-128">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e14f1-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="e14f1-129">Například `release/2.2`.</span><span class="sxs-lookup"><span data-stu-id="e14f1-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="e14f1-130">Klonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e14f1-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="e14f1-131">Ověřte, zda jste nainstalovali verzi [sady .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) odpovídající klonování úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e14f1-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="e14f1-132">Přejděte na ukázku v *aspNetCore/src/Security/samples* a spusťte vzorek s . `dotnet run`</span><span class="sxs-lookup"><span data-stu-id="e14f1-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
