---
title: Ukázky ověřování pro ASP.NET Core
author: rick-anderson
description: Obsahuje odkazy na ukázky ověřování v úložišti ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: 3d7e28f6e501bd8bd3908ca4b314a63cee52ebe3
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828669"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="fda23-103">Ukázky ověřování pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fda23-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="fda23-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fda23-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fda23-105">[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="fda23-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="fda23-106">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="fda23-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="fda23-107">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="fda23-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="fda23-108">Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="fda23-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="fda23-109">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="fda23-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="fda23-110">Externí deklarace identity</span><span class="sxs-lookup"><span data-stu-id="fda23-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="fda23-111">Výběr mezi souborem cookie a jiným schématem ověřování na základě požadavku</span><span class="sxs-lookup"><span data-stu-id="fda23-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="fda23-112">Omezí přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="fda23-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="fda23-113">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="fda23-113">Run the samples</span></span>

* <span data-ttu-id="fda23-114">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="fda23-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="fda23-115">Třeba `Tag:v3.0.0`.</span><span class="sxs-lookup"><span data-stu-id="fda23-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="fda23-116">Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="fda23-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="fda23-117">Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://www.microsoft.com/net/download/all) , která odpovídá klonu úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fda23-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="fda23-118">Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="fda23-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fda23-119">[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="fda23-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="fda23-120">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="fda23-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="fda23-121">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="fda23-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="fda23-122">Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="fda23-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="fda23-123">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="fda23-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="fda23-124">Externí deklarace identity</span><span class="sxs-lookup"><span data-stu-id="fda23-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="fda23-125">Výběr mezi souborem cookie a jiným schématem ověřování na základě požadavku</span><span class="sxs-lookup"><span data-stu-id="fda23-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="fda23-126">Omezí přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="fda23-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="fda23-127">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="fda23-127">Run the samples</span></span>

* <span data-ttu-id="fda23-128">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="fda23-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="fda23-129">Třeba `release/2.2`.</span><span class="sxs-lookup"><span data-stu-id="fda23-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="fda23-130">Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="fda23-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="fda23-131">Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://www.microsoft.com/net/download/all) , která odpovídá klonu úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fda23-131">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="fda23-132">Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="fda23-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
