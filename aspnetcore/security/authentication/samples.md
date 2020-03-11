---
title: Ukázky ověřování pro ASP.NET Core
author: rick-anderson
description: Obsahuje odkazy na ukázky ověřování v úložišti ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: 3d7e28f6e501bd8bd3908ca4b314a63cee52ebe3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658415"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="7ffb9-103">Ukázky ověřování pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ffb9-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="7ffb9-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ffb9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ffb9-105">[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="7ffb9-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="7ffb9-106">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="7ffb9-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="7ffb9-107">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="7ffb9-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="7ffb9-108">Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="7ffb9-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="7ffb9-109">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="7ffb9-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="7ffb9-110">Externí deklarace identity</span><span class="sxs-lookup"><span data-stu-id="7ffb9-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="7ffb9-111">Výběr mezi souborem cookie a jiným schématem ověřování na základě požadavku</span><span class="sxs-lookup"><span data-stu-id="7ffb9-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="7ffb9-112">Omezí přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="7ffb9-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="7ffb9-113">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="7ffb9-113">Run the samples</span></span>

* <span data-ttu-id="7ffb9-114">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="7ffb9-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="7ffb9-115">Například `Tag:v3.0.0`.</span><span class="sxs-lookup"><span data-stu-id="7ffb9-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="7ffb9-116">Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="7ffb9-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="7ffb9-117">Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://www.microsoft.com/net/download/all) , která odpovídá klonu úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ffb9-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="7ffb9-118">Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="7ffb9-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ffb9-119">[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="7ffb9-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="7ffb9-120">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="7ffb9-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="7ffb9-121">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="7ffb9-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="7ffb9-122">Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="7ffb9-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="7ffb9-123">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="7ffb9-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="7ffb9-124">Externí deklarace identity</span><span class="sxs-lookup"><span data-stu-id="7ffb9-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="7ffb9-125">Výběr mezi souborem cookie a jiným schématem ověřování na základě požadavku</span><span class="sxs-lookup"><span data-stu-id="7ffb9-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="7ffb9-126">Omezí přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="7ffb9-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="7ffb9-127">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="7ffb9-127">Run the samples</span></span>

* <span data-ttu-id="7ffb9-128">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="7ffb9-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="7ffb9-129">Například `release/2.2`.</span><span class="sxs-lookup"><span data-stu-id="7ffb9-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="7ffb9-130">Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="7ffb9-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="7ffb9-131">Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://www.microsoft.com/net/download/all) , která odpovídá klonu úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ffb9-131">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="7ffb9-132">Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="7ffb9-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
