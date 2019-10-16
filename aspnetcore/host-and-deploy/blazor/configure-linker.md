---
title: Konfigurace linkeru pro ASP.NET Core Blazor
author: guardrex
description: Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování aplikace v Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: a7e59e63c163986c40155e230dc644028e78e5fd
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391450"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="386ef-103">Konfigurace linkeru pro ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="386ef-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="386ef-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="386ef-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="386ef-105">Blazor provádí propojení s [pokročilým jazykem (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení pro vydání, aby se z výstupních sestavení aplikace odstranilo zbytečné Il.</span><span class="sxs-lookup"><span data-stu-id="386ef-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a Release build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="386ef-106">Ovládací prvek propojuje sestavení pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="386ef-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="386ef-107">Zakáže propojení globálně s [vlastností MSBuild](#disable-linking-with-a-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="386ef-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="386ef-108">Řízení propojení podle jednotlivých sestavení pomocí [konfiguračního souboru](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="386ef-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="386ef-109">Zakázat propojení s vlastností MSBuild</span><span class="sxs-lookup"><span data-stu-id="386ef-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="386ef-110">Propojení je ve výchozím nastavení povoleno v režimu vydání, když je sestavena aplikace, která zahrnuje publikování.</span><span class="sxs-lookup"><span data-stu-id="386ef-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="386ef-111">Chcete-li zakázat propojování pro všechna sestavení, nastavte vlastnost MSBuild `BlazorLinkOnBuild` na hodnotu `false` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="386ef-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="386ef-112">Řízení propojování pomocí konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="386ef-112">Control linking with a configuration file</span></span>

<span data-ttu-id="386ef-113">Řízení propojení na základě sestavení zadáním konfiguračního souboru XML a zadáním souboru jako položky MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="386ef-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="386ef-114">*Linker. XML*:</span><span class="sxs-lookup"><span data-stu-id="386ef-114">*Linker.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/aspnet/Blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="386ef-115">Další informace naleznete v tématu [linkeru Il: syntaxe popisovače XML](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="386ef-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>
