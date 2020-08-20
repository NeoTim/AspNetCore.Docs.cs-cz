---
title: Ukázky ověřování pro ASP.NET Core
author: rick-anderson
description: Obsahuje odkazy na ukázky ověřování v úložišti ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 290c956b2035e47e5b34dba15fbec665461dd94a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630741"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="befd4-103">Ukázky ověřování pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="befd4-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="befd4-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="befd4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="befd4-105">[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="befd4-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="befd4-106">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="befd4-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="befd4-107">[Cookie přihlašovací](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="befd4-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="befd4-108">Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="befd4-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="befd4-109">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="befd4-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="befd4-110">[Externí deklarace identity](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="befd4-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="befd4-111">Výběr mezi cookie a dalším schématem ověřování na základě požadavku</span><span class="sxs-lookup"><span data-stu-id="befd4-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="befd4-112">Omezí přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="befd4-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="befd4-113">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="befd4-113">Run the samples</span></span>

* <span data-ttu-id="befd4-114">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="befd4-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="befd4-115">Například `release/3.1`.</span><span class="sxs-lookup"><span data-stu-id="befd4-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="befd4-116">Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="befd4-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="befd4-117">Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) , která odpovídá klonu úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="befd4-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="befd4-118">Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="befd4-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="befd4-119">[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="befd4-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="befd4-120">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="befd4-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="befd4-121">[Cookie přihlašovací](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="befd4-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="befd4-122">Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="befd4-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="befd4-123">Schémata a možnosti dynamického ověřování</span><span class="sxs-lookup"><span data-stu-id="befd4-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="befd4-124">[Externí deklarace identity](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="befd4-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="befd4-125">Výběr mezi cookie a dalším schématem ověřování na základě požadavku</span><span class="sxs-lookup"><span data-stu-id="befd4-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="befd4-126">Omezí přístup ke statickým souborům.</span><span class="sxs-lookup"><span data-stu-id="befd4-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="befd4-127">Spuštění ukázek</span><span class="sxs-lookup"><span data-stu-id="befd4-127">Run the samples</span></span>

* <span data-ttu-id="befd4-128">Vyberte [větev](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="befd4-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="befd4-129">Například `release/2.1`.</span><span class="sxs-lookup"><span data-stu-id="befd4-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="befd4-130">Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="befd4-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="befd4-131">Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) , která odpovídá klonu úložiště ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="befd4-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="befd4-132">Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="befd4-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
