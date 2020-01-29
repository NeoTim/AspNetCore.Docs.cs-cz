---
title: ASP.NET Core web SDK
author: Rick-Anderson
description: Přehled Microsoft. NET. SDK. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 49e21e1a432149409a01550452cedf4009dcfba7
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830672"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="ae02b-103">ASP.NET Core web SDK</span><span class="sxs-lookup"><span data-stu-id="ae02b-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="ae02b-104">Přehled</span><span class="sxs-lookup"><span data-stu-id="ae02b-104">Overview</span></span>

<span data-ttu-id="ae02b-105">`Microsoft.NET.Sdk.Web` je [projekt MSBuild SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) pro sestavování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae02b-105">`Microsoft.NET.Sdk.Web` is a [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="ae02b-106">Je možné vytvořit aplikaci ASP.NET Core bez této sady SDK, ale webová sada SDK je:</span><span class="sxs-lookup"><span data-stu-id="ae02b-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="ae02b-107">Přizpůsobené pro poskytování prvotřídního prostředí.</span><span class="sxs-lookup"><span data-stu-id="ae02b-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="ae02b-108">Doporučený cíl pro většinu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="ae02b-108">The recommended target for most users.</span></span>

<span data-ttu-id="ae02b-109">Použijte web. SDK v projektu:</span><span class="sxs-lookup"><span data-stu-id="ae02b-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="ae02b-110">Funkce, které jsou povolené pomocí webové sady SDK:</span><span class="sxs-lookup"><span data-stu-id="ae02b-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="ae02b-111">Projekty cílené na .NET Core 3,0 nebo novější, na které se implicitně odkazují:</span><span class="sxs-lookup"><span data-stu-id="ae02b-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="ae02b-112">[ASP.NET Core sdílené rozhraní](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ae02b-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="ae02b-113">[Analyzátory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) navržené pro vytváření ASP.NET Corech aplikací</span><span class="sxs-lookup"><span data-stu-id="ae02b-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="ae02b-114">WebSDK umožňuje cíle MSBuild, které umožňují použití profilů publikování a publikování pomocí nástroje WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="ae02b-114">The WebSDK enables MSBuild targets that enables the use of publish profiles, and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="ae02b-115">Vlastnosti</span><span class="sxs-lookup"><span data-stu-id="ae02b-115">Properties</span></span>

| <span data-ttu-id="ae02b-116">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="ae02b-116">Property</span></span> | <span data-ttu-id="ae02b-117">Popis</span><span class="sxs-lookup"><span data-stu-id="ae02b-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="ae02b-118">Zakáže implicitní odkaz na `Microsoft.AspNetCore.App` sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ae02b-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="ae02b-119">Zakáže implicitní odkaz na analyzátory ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae02b-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="ae02b-120">Zakáže implicitní odkaz na analyzátory komponent Razor při sestavování aplikací Blazor (Server).</span><span class="sxs-lookup"><span data-stu-id="ae02b-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
