---
title: Ukázky ověřování pro ASP.NET Core
author: rick-anderson
description: Obsahuje odkazy na ukázky ověřování v úložišti ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: 7b3c911d60ad4737ebd12ce6f7628ad624b11658
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236687"
---
# <a name="authentication-samples-for-aspnet-core"></a>Ukázky ověřování pro ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[ASP.NET Core úložiště](https://github.com/aspnet/AspNetCore) obsahuje následující ukázky ověřování *AspNetCore/src/Security/samples* složky:

* [Transformace deklarací identity](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Ověřování souborů cookie](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Vlastní zásady pro poskytovatele – IAuthorizationPolicyProvider](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Schémata ověřování pro dynamické a možnosti](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Externí deklarace identity](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Výběr mezi souboru cookie a další schéma ověřování na základě daného požadavku](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Omezí přístup na statické soubory](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Spuštění ukázek

* Vyberte [větev](https://github.com/aspnet/AspNetCore). Třeba `release/2.2`.
* Klonovat nebo stáhnout [ASP.NET Core úložiště](https://github.com/aspnet/AspNetCore).
* Ověřte instalaci [.NET Core SDK](https://www.microsoft.com/net/download/all) verzi, která odpovídá klon úložiště pro ASP.NET Core.
* Přejděte na ukázky v *AspNetCore/src/Security/samples* a spusťte ukázku pomocí `dotnet run`.
