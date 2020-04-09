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
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="0bdb6-103">ASP.NET základní webovou sadou SDK</span><span class="sxs-lookup"><span data-stu-id="0bdb6-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="0bdb6-104">Přehled</span><span class="sxs-lookup"><span data-stu-id="0bdb6-104">Overview</span></span>

<span data-ttu-id="0bdb6-105">`Microsoft.NET.Sdk.Web`je [sada SDK projektu MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) pro vytváření ASP.NET základních aplikací.</span><span class="sxs-lookup"><span data-stu-id="0bdb6-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="0bdb6-106">Je možné vytvořit aplikaci ASP.NET Core bez této sady SDK, webová sada SDK je však:</span><span class="sxs-lookup"><span data-stu-id="0bdb6-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="0bdb6-107">Šité na míru pro poskytování prvotřídního zážitku.</span><span class="sxs-lookup"><span data-stu-id="0bdb6-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="0bdb6-108">Doporučený cíl pro většinu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="0bdb6-108">The recommended target for most users.</span></span>

<span data-ttu-id="0bdb6-109">Použití sady Web.SDK v projektu:</span><span class="sxs-lookup"><span data-stu-id="0bdb6-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="0bdb6-110">Funkce povolené pomocí sady Web SDK:</span><span class="sxs-lookup"><span data-stu-id="0bdb6-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="0bdb6-111">Projekty, které cílí na rozhraní .NET Core 3.0 nebo novější implicitně odkazují:</span><span class="sxs-lookup"><span data-stu-id="0bdb6-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="0bdb6-112">Sdílený [rámec ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="0bdb6-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="0bdb6-113">[Analyzátory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) navržené pro vytváření aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0bdb6-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="0bdb6-114">Sada Web SDK importuje cíle MSBuild, které umožňují použití profilů publikování a publikování pomocí aplikace WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="0bdb6-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="0bdb6-115">Vlastnosti</span><span class="sxs-lookup"><span data-stu-id="0bdb6-115">Properties</span></span>

| <span data-ttu-id="0bdb6-116">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="0bdb6-116">Property</span></span> | <span data-ttu-id="0bdb6-117">Popis</span><span class="sxs-lookup"><span data-stu-id="0bdb6-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="0bdb6-118">Zakáže implicitní `Microsoft.AspNetCore.App` odkaz na sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0bdb6-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="0bdb6-119">Zakáže implicitní odkaz na ASP.NET core analyzátory.</span><span class="sxs-lookup"><span data-stu-id="0bdb6-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="0bdb6-120">Zakáže implicitní odkaz na analyzátory komponent razor při vytváření Blazor (serverových) aplikací.</span><span class="sxs-lookup"><span data-stu-id="0bdb6-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
