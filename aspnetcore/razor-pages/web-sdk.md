---
title: ASP.NET Core web SDK
author: Rick-Anderson
description: Přehled Microsoft. NET. SDK. Web.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: a717d9dc0e326e62ec920d2a838e8b18be3db013
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020688"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core web SDK

### <a name="overview"></a>Přehled

`Microsoft.NET.Sdk.Web`je [projektová sada SDK pro MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) pro vytváření ASP.NET Corech aplikací. Je možné vytvořit aplikaci ASP.NET Core bez této sady SDK, ale webová sada SDK je:

* Přizpůsobené pro poskytování prvotřídního prostředí.
* Doporučený cíl pro většinu uživatelů.

Použijte web. SDK v projektu:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Funkce, které jsou povolené pomocí webové sady SDK:

* Projekty cílené na .NET Core 3,0 nebo novější, na které se implicitně odkazují:

  * [ASP.NET Core sdílené rozhraní](xref:fundamentals/metapackage-app).
  * [Analyzátory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) navržené pro vytváření ASP.NET Corech aplikací
* Webová sada SDK importuje cíle MSBuild, které umožňují použití profilů publikování a publikování pomocí nástroje WebDeploy.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Popis |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Zakáže implicitní odkaz na `Microsoft.AspNetCore.App` sdílené rozhraní. |
| `DisableImplicitAspNetCoreAnalyzers` | Zakáže implicitní odkaz na analyzátory ASP.NET Core. |
| `DisableImplicitComponentsAnalyzers` | Zakáže implicitní odkaz na Razor analyzátory komponent při vytváření Blazor aplikací (Server). |
