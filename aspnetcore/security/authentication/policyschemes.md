---
title: Zásady schémata v ASP.NET Core
author: rick-anderson
description: Schémata ověřování zásad usnadňují mít jedno logické schéma ověřování
ms.author: riande
ms.date: 02/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: be03f349455c673b0739935ad20e596325c8cb74
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815287"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="fb3b8-103">Zásady schémata v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb3b8-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="fb3b8-104">Schémata ověřování zásad usnadňují mít jedno logické schéma ověřování potenciálně použít několik přístupů.</span><span class="sxs-lookup"><span data-stu-id="fb3b8-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="fb3b8-105">Schéma zásad může například použít ověřování Google výzev a ověřování souborů cookie pro všechno ostatní.</span><span class="sxs-lookup"><span data-stu-id="fb3b8-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="fb3b8-106">Schémata ověřování zásad usnadňují:</span><span class="sxs-lookup"><span data-stu-id="fb3b8-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="fb3b8-107">Snadno předat ověřování akce pro další schéma.</span><span class="sxs-lookup"><span data-stu-id="fb3b8-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="fb3b8-108">Vpřed dynamicky na základě daného požadavku.</span><span class="sxs-lookup"><span data-stu-id="fb3b8-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="fb3b8-109">Všechna schémata ověřování, které použijte odvozené <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> a přidružené [ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="fb3b8-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [`AuthenticationHandler<TOptions>`](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="fb3b8-110">Jsou automaticky zásad schémata v ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="fb3b8-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="fb3b8-111">Je možné povolit prostřednictvím konfigurace možností na schématu.</span><span class="sxs-lookup"><span data-stu-id="fb3b8-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="fb3b8-112">Příklady</span><span class="sxs-lookup"><span data-stu-id="fb3b8-112">Examples</span></span>

<span data-ttu-id="fb3b8-113">Následující příklad ukazuje vyšší úrovně schéma, které kombinuje nižší úrovně schémata.</span><span class="sxs-lookup"><span data-stu-id="fb3b8-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="fb3b8-114">Ověřování Google se používá pro problémy a ověřování souborů cookie se používá pro všechno ostatní:</span><span class="sxs-lookup"><span data-stu-id="fb3b8-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="fb3b8-115">Následující příklad umožňuje dynamický výběr schémata na základě žádosti.</span><span class="sxs-lookup"><span data-stu-id="fb3b8-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="fb3b8-116">To znamená jak kombinovat soubory cookie a rozhraní API ověřování:</span><span class="sxs-lookup"><span data-stu-id="fb3b8-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
