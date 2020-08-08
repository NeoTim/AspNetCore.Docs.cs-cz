---
title: Ukázky ověřování pro ASP.NET Core
author: rick-anderson
description: Obsahuje odkazy na ukázky ověřování v úložišti ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 71e614eb3d91e8425ec430d775b8a0163dc7258f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017789"
---
# <a name="authentication-samples-for-aspnet-core"></a>Ukázky ověřování pro ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :

* [Transformace deklarací identity](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Cookiepřihlašovací](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Schémata a možnosti dynamického ověřování](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Externí deklarace identity](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Výběr mezi cookie a dalším schématem ověřování na základě požadavku](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Omezí přístup ke statickým souborům.](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Spuštění ukázek

* Vyberte [větev](https://github.com/dotnet/AspNetCore). Například `release/3.1`.
* Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) , která odpovídá klonu úložiště ASP.NET Core.
* Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore) obsahuje následující ukázky ověřování ve složce *AspNetCore/src/Security/Samples* :

* [Transformace deklarací identity](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* [Cookiepřihlašovací](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)
* [Vlastní zprostředkovatel zásad – IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [Schémata a možnosti dynamického ověřování](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* [Externí deklarace identity](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)
* [Výběr mezi cookie a dalším schématem ověřování na základě požadavku](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [Omezí přístup ke statickým souborům.](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Spuštění ukázek

* Vyberte [větev](https://github.com/dotnet/AspNetCore). Například `release/2.1`.
* Naklonujte nebo stáhněte [úložiště ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Ověřte, že máte nainstalovanou verzi [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) , která odpovídá klonu úložiště ASP.NET Core.
* Přejděte k ukázce v *AspNetCore/src/Security/Samples* a spusťte ukázku pomocí `dotnet run` .

::: moniker-end
