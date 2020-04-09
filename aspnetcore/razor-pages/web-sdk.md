---
title: ASP.NET základní webovou sadou SDK
author: Rick-Anderson
description: Přehled webu Microsoft.NET.Sdk.Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661054"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET základní webovou sadou SDK

### <a name="overview"></a>Přehled

`Microsoft.NET.Sdk.Web`je [sada SDK projektu MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) pro vytváření ASP.NET základních aplikací. Je možné vytvořit aplikaci ASP.NET Core bez této sady SDK, webová sada SDK je však:

* Šité na míru pro poskytování prvotřídního zážitku.
* Doporučený cíl pro většinu uživatelů.

Použití sady Web.SDK v projektu:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Funkce povolené pomocí sady Web SDK:

* Projekty, které cílí na rozhraní .NET Core 3.0 nebo novější implicitně odkazují:

  * Sdílený [rámec ASP.NET Core](xref:fundamentals/metapackage-app).
  * [Analyzátory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) navržené pro vytváření aplikací ASP.NET Core.
* Sada Web SDK importuje cíle MSBuild, které umožňují použití profilů publikování a publikování pomocí aplikace WebDeploy.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Popis |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Zakáže implicitní `Microsoft.AspNetCore.App` odkaz na sdílené rozhraní. |
| `DisableImplicitAspNetCoreAnalyzers` | Zakáže implicitní odkaz na ASP.NET core analyzátory. |
| `DisableImplicitComponentsAnalyzers` | Zakáže implicitní odkaz na analyzátory komponent razor při vytváření Blazor (serverových) aplikací. |
