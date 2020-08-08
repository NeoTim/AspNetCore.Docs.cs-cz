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
# <a name="policy-schemes-in-aspnet-core"></a>Schémata zásad v ASP.NET Core

Schémata zásad ověřování usnadňují použití jediného logického schématu ověřování, který potenciálně používá více přístupů. Schéma zásad může například používat ověřování Google pro problémy a cookie ověřování pro všechno ostatní. Schémata zásad ověřování ji zavedli:

* Snadné přeposílání jakékoli akce ověřování do jiného schématu.
* Dynamické směrování na základě požadavku.

Všechna schémata ověřování, která používají odvozená <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> a přidružená [AuthenticationHandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* Jsou automatická schémata zásad v ASP.NET Core 2,1 a novějších.
* Dá se povolit prostřednictvím konfigurace možností schématu.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Příklady

Následující příklad ukazuje schéma vyšší úrovně, které kombinuje schémata nižší úrovně. Ověřování Google se používá pro problémy a cookie ověřování se používá pro všechno ostatní:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

Následující příklad povoluje dynamický výběr schémat na základě jednotlivých požadavků. To znamená, jak kombinovat cookie s a ověřování API:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
