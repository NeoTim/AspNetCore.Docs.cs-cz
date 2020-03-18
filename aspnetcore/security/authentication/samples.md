---
title: Ukázky ověřování pro ASP.NET Core
author: rick-anderson
description: Obsahuje odkazy na ukázky ověřování v úložišti ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b013d02a65e752bbb61a87a0bf502785125378a2
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511610"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="edc3f-103">Ukázky ověřování pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="edc3f-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="edc3f-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="edc3f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="edc3f-105">[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="edc3f-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="edc3f-106">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="edc3f-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="edc3f-107">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="edc3f-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="edc3f-108">Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="edc3f-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="edc3f-109">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="edc3f-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="edc3f-110">Externí deklarace identity</span><span class="sxs-lookup"><span data-stu-id="edc3f-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="edc3f-111">Výběr mezi souborem cookie a jiným schématem ověřování na základě požadavku</span><span class="sxs-lookup"><span data-stu-id="edc3f-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="edc3f-112">Omezí přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="edc3f-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="edc3f-113">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="edc3f-113">Run the samples</span></span>

* <span data-ttu-id="edc3f-114">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="edc3f-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="edc3f-115">Například `Tag:v3.0.0`.</span><span class="sxs-lookup"><span data-stu-id="edc3f-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="edc3f-116">Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="edc3f-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="edc3f-117">Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) , která odpovídá klonu úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="edc3f-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="edc3f-118">Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="edc3f-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="edc3f-119">[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="edc3f-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="edc3f-120">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="edc3f-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="edc3f-121">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="edc3f-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="edc3f-122">Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="edc3f-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="edc3f-123">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="edc3f-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="edc3f-124">Externí deklarace identity</span><span class="sxs-lookup"><span data-stu-id="edc3f-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="edc3f-125">Výběr mezi souborem cookie a jiným schématem ověřování na základě požadavku</span><span class="sxs-lookup"><span data-stu-id="edc3f-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="edc3f-126">Omezí přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="edc3f-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="edc3f-127">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="edc3f-127">Run the samples</span></span>

* <span data-ttu-id="edc3f-128">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="edc3f-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="edc3f-129">Například `release/2.2`.</span><span class="sxs-lookup"><span data-stu-id="edc3f-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="edc3f-130">Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="edc3f-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="edc3f-131">Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) , která odpovídá klonu úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="edc3f-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="edc3f-132">Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="edc3f-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
