---
title: Schémata zásad v ASP.NET Core
author: rick-anderson
description: Schémata zásad ověřování usnadňují jedno logické schéma ověřování.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: ddedf62c5e8363bd93c9948fd2d3418abc566539
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767314"
---
# <a name="policy-schemes-in-aspnet-core"></a>Schémata zásad v ASP.NET Core

Schémata zásad ověřování usnadňují použití jediného logického schématu ověřování, který potenciálně používá více přístupů. Schéma zásad může například používat ověřování Google pro problémy a ověřování souborů cookie pro všechno ostatní. Schémata zásad ověřování ji zavedli:

* Snadné přeposílání jakékoli akce ověřování do jiného schématu.
* Dynamické směrování na základě požadavku.

Všechna schémata ověřování, která používají <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> odvozená a přidružená [\<AuthenticationHandler TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* Jsou automatická schémata zásad v ASP.NET Core 2,1 a novějších.
* Dá se povolit prostřednictvím konfigurace možností schématu.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Příklady

Následující příklad ukazuje schéma vyšší úrovně, které kombinuje schémata nižší úrovně. Ověřování Google se používá pro problémy a ověřování souborů cookie se používá pro všechno ostatní:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

Následující příklad povoluje dynamický výběr schémat na základě jednotlivých požadavků. To znamená, jak kombinovat soubory cookie a ověřování rozhraní API:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
