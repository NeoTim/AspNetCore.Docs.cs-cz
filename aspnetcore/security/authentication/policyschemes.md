---
title: Zásady schémata v ASP.NET Core
author: rick-anderson
description: Schémata ověřování zásad usnadňují mít jedno logické schéma ověřování
ms.author: riande
ms.date: 2/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: c310b61e14df2b7846e32a602bb75914a5850aff
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346804"
---
# <a name="policy-schemes-in-aspnet-core"></a>Zásady schémata v ASP.NET Core

Schémata ověřování zásad usnadňují mít jedno logické schéma ověřování potenciálně použít několik přístupů. Schéma zásad může například použít ověřování Google výzev a ověřování souborů cookie pro všechno ostatní. Schémata ověřování zásad usnadňují:

* Snadno předat ověřování akce pro další schéma.
* Vpřed dynamicky na základě daného požadavku.

Všechna schémata ověřování, které použijte odvozené <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> a přidružené [ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* Jsou automaticky zásad schémata v ASP.NET Core 2.1 nebo novější.
* Je možné povolit prostřednictvím konfigurace možností na schématu.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Příklady

Následující příklad ukazuje vyšší úrovně schéma, které kombinuje nižší úrovně schémata. Ověřování Google se používá pro problémy a ověřování souborů cookie se používá pro všechno ostatní:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

Následující příklad umožňuje dynamický výběr schémata na základě žádosti. To znamená jak kombinovat soubory cookie a ověřování rozhraní API.

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
