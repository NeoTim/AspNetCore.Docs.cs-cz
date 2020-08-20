---
title: ASP.NET Core web SDK
author: Rick-Anderson
description: Přehled Microsoft. NET. SDK. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: 163bc2679deda449f97cb4e50da1093e6b1edda4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634810"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="851a8-103">ASP.NET Core web SDK</span><span class="sxs-lookup"><span data-stu-id="851a8-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="851a8-104">Přehled</span><span class="sxs-lookup"><span data-stu-id="851a8-104">Overview</span></span>

<span data-ttu-id="851a8-105">`Microsoft.NET.Sdk.Web` je [projektová sada SDK pro MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) pro vytváření ASP.NET Corech aplikací.</span><span class="sxs-lookup"><span data-stu-id="851a8-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="851a8-106">Je možné vytvořit aplikaci ASP.NET Core bez této sady SDK, ale webová sada SDK je:</span><span class="sxs-lookup"><span data-stu-id="851a8-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="851a8-107">Přizpůsobené pro poskytování prvotřídního prostředí.</span><span class="sxs-lookup"><span data-stu-id="851a8-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="851a8-108">Doporučený cíl pro většinu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="851a8-108">The recommended target for most users.</span></span>

<span data-ttu-id="851a8-109">Použijte web. SDK v projektu:</span><span class="sxs-lookup"><span data-stu-id="851a8-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="851a8-110">Funkce, které jsou povolené pomocí webové sady SDK:</span><span class="sxs-lookup"><span data-stu-id="851a8-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="851a8-111">Projekty cílené na .NET Core 3,0 nebo novější, na které se implicitně odkazují:</span><span class="sxs-lookup"><span data-stu-id="851a8-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="851a8-112">[ASP.NET Core sdílené rozhraní](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="851a8-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="851a8-113">[Analyzátory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) navržené pro vytváření ASP.NET Corech aplikací</span><span class="sxs-lookup"><span data-stu-id="851a8-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="851a8-114">Webová sada SDK importuje cíle MSBuild, které umožňují použití profilů publikování a publikování pomocí nástroje WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="851a8-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="851a8-115">Vlastnosti</span><span class="sxs-lookup"><span data-stu-id="851a8-115">Properties</span></span>

| <span data-ttu-id="851a8-116">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="851a8-116">Property</span></span> | <span data-ttu-id="851a8-117">Popis</span><span class="sxs-lookup"><span data-stu-id="851a8-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="851a8-118">Zakáže implicitní odkaz na `Microsoft.AspNetCore.App` sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="851a8-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="851a8-119">Zakáže implicitní odkaz na analyzátory ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="851a8-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="851a8-120">Zakáže implicitní odkaz na Razor analyzátory komponent při vytváření Blazor aplikací (Server).</span><span class="sxs-lookup"><span data-stu-id="851a8-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
