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
# <a name="authentication-samples-for-aspnet-core"></a>Ukázky ověřování pro ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :

* [Transformace deklarací identity](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [Ověřování souborem cookie](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [Schémata a možnosti dynamického ověřování](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [Externí deklarace identity](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [Výběr mezi souborem cookie a jiným schématem ověřování na základě požadavku](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [Omezí přístup ke statickým souborům.](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Spuštění ukázek

* Vyberte [větev](https://github.com/dotnet/AspNetCore). Například `Tag:v3.0.0`.
* Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://www.microsoft.com/net/download/all) , která odpovídá klonu úložiště ASP.NET Core.
* Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :

* [Transformace deklarací identity](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Ověřování souborem cookie](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Schémata a možnosti dynamického ověřování](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Externí deklarace identity](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Výběr mezi souborem cookie a jiným schématem ověřování na základě požadavku](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Omezí přístup ke statickým souborům.](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Spuštění ukázek

* Vyberte [větev](https://github.com/dotnet/AspNetCore). Například `release/2.2`.
* Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://www.microsoft.com/net/download/all) , která odpovídá klonu úložiště ASP.NET Core.
* Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run`.

::: moniker-end
