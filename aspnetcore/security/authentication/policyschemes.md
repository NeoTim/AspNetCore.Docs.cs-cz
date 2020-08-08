---
title: Schémata zásad v ASP.NET Core
author: rick-anderson
description: Schémata zásad ověřování usnadňují jedno logické schéma ověřování.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: ddee613bf9c603542f17adf59a835a2ddbdc25a3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017802"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="05a06-103">Schémata zásad v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05a06-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="05a06-104">Schémata zásad ověřování usnadňují použití jediného logického schématu ověřování, který potenciálně používá více přístupů.</span><span class="sxs-lookup"><span data-stu-id="05a06-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="05a06-105">Schéma zásad může například používat ověřování Google pro problémy a cookie ověřování pro všechno ostatní.</span><span class="sxs-lookup"><span data-stu-id="05a06-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="05a06-106">Schémata zásad ověřování ji zavedli:</span><span class="sxs-lookup"><span data-stu-id="05a06-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="05a06-107">Snadné přeposílání jakékoli akce ověřování do jiného schématu.</span><span class="sxs-lookup"><span data-stu-id="05a06-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="05a06-108">Dynamické směrování na základě požadavku.</span><span class="sxs-lookup"><span data-stu-id="05a06-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="05a06-109">Všechna schémata ověřování, která používají odvozená <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> a přidružená [AuthenticationHandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="05a06-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="05a06-110">Jsou automatická schémata zásad v ASP.NET Core 2,1 a novějších.</span><span class="sxs-lookup"><span data-stu-id="05a06-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="05a06-111">Dá se povolit prostřednictvím konfigurace možností schématu.</span><span class="sxs-lookup"><span data-stu-id="05a06-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="05a06-112">Příklady</span><span class="sxs-lookup"><span data-stu-id="05a06-112">Examples</span></span>

<span data-ttu-id="05a06-113">Následující příklad ukazuje schéma vyšší úrovně, které kombinuje schémata nižší úrovně.</span><span class="sxs-lookup"><span data-stu-id="05a06-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="05a06-114">Ověřování Google se používá pro problémy a cookie ověřování se používá pro všechno ostatní:</span><span class="sxs-lookup"><span data-stu-id="05a06-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="05a06-115">Následující příklad povoluje dynamický výběr schémat na základě jednotlivých požadavků.</span><span class="sxs-lookup"><span data-stu-id="05a06-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="05a06-116">To znamená, jak kombinovat cookie s a ověřování API:</span><span class="sxs-lookup"><span data-stu-id="05a06-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
