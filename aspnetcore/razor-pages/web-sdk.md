---
title: ASP.NET Core web SDK
author: Rick-Anderson
description: Přehled Microsoft. NET. SDK. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661054"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core web SDK

### <a name="overview"></a>Přehled

`Microsoft.NET.Sdk.Web` je [projekt MSBuild SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) pro sestavování aplikací ASP.NET Core. Je možné vytvořit aplikaci ASP.NET Core bez této sady SDK, ale webová sada SDK je:

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
| `DisableImplicitComponentsAnalyzers` | Zakáže implicitní odkaz na analyzátory komponent Razor při sestavování aplikací Blazor (Server). |
