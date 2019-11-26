---
title: Konfigurace linkeru pro ASP.NET Core Blazor
author: guardrex
description: Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 0bc987d72d2f684b1ecbd4a883e9a09fac7c801e
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317292"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="ae7da-103">Konfigurace linkeru pro ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ae7da-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ae7da-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ae7da-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="ae7da-105"> provádí propojování v [prostředním jazyce (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení pro odebrání zbytečného Il z výstupních sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae7da-105"> performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="ae7da-106">Ovládací prvek propojuje sestavení pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ae7da-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="ae7da-107">Zakáže propojení globálně s [vlastností MSBuild](#disable-linking-with-a-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="ae7da-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="ae7da-108">Řízení propojení podle jednotlivých sestavení pomocí [konfiguračního souboru](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="ae7da-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="ae7da-109">Zakázat propojení s vlastností MSBuild</span><span class="sxs-lookup"><span data-stu-id="ae7da-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="ae7da-110">Propojení je ve výchozím nastavení povoleno, pokud je aplikace sestavena, včetně publikování.</span><span class="sxs-lookup"><span data-stu-id="ae7da-110">Linking is enabled by default when an app is built, which includes publishing.</span></span> <span data-ttu-id="ae7da-111">Chcete-li zakázat propojování pro všechna sestavení, nastavte vlastnost `BlazorLinkOnBuild` MSBuild na `false` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="ae7da-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="ae7da-112">Řízení propojování pomocí konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="ae7da-112">Control linking with a configuration file</span></span>

<span data-ttu-id="ae7da-113">Řízení propojení na základě sestavení zadáním konfiguračního souboru XML a zadáním souboru jako položky MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="ae7da-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="ae7da-114">*Linker. XML*:</span><span class="sxs-lookup"><span data-stu-id="ae7da-114">*Linker.xml*:</span></span>

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

<span data-ttu-id="ae7da-115">Další informace naleznete v tématu [linkeru Il: syntaxe popisovače XML](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="ae7da-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="ae7da-116">Konfigurace linkeru pro mezinárodní využití</span><span class="sxs-lookup"><span data-stu-id="ae7da-116">Configure the linker for internationalization</span></span>

<span data-ttu-id="ae7da-117">Ve výchozím nastavení Blazorkonfigurace linkeru pro Blazor aplikace pro WebAssembly odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot.</span><span class="sxs-lookup"><span data-stu-id="ae7da-117">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ae7da-118">Odebrání těchto sestavení minimalizuje velikost aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae7da-118">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="ae7da-119">Chcete-li určit, která sestavení I18N jsou zachována, nastavte vlastnost `<MonoLinkerI18NAssemblies>` MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="ae7da-119">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="ae7da-120">Hodnota oblasti</span><span class="sxs-lookup"><span data-stu-id="ae7da-120">Region Value</span></span>     | <span data-ttu-id="ae7da-121">Sestavení oblasti mono</span><span class="sxs-lookup"><span data-stu-id="ae7da-121">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="ae7da-122">Všechna sestavení, která jsou součástí</span><span class="sxs-lookup"><span data-stu-id="ae7da-122">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="ae7da-123">*I18N. CJK. dll*</span><span class="sxs-lookup"><span data-stu-id="ae7da-123">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="ae7da-124">*I18N. MidEast. dll*</span><span class="sxs-lookup"><span data-stu-id="ae7da-124">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="ae7da-125">`none` (výchozí)</span><span class="sxs-lookup"><span data-stu-id="ae7da-125">`none` (default)</span></span> | <span data-ttu-id="ae7da-126">Žádné</span><span class="sxs-lookup"><span data-stu-id="ae7da-126">None</span></span>                    |
| `other`          | <span data-ttu-id="ae7da-127">*I18N. Jiná knihovna. dll*</span><span class="sxs-lookup"><span data-stu-id="ae7da-127">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="ae7da-128">*I18N. Vzácná knihovna DLL*</span><span class="sxs-lookup"><span data-stu-id="ae7da-128">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="ae7da-129">*I18N. Západ. dll*</span><span class="sxs-lookup"><span data-stu-id="ae7da-129">*I18N.West.dll*</span></span>         |

<span data-ttu-id="ae7da-130">Oddělte více hodnot pomocí čárky (například `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="ae7da-130">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="ae7da-131">Další informace najdete v tématu [i18n: Pnetlib internationaling Framework knihovnu (úložiště GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="ae7da-131">For more information, see [I18N: Pnetlib Internationalization Framework Libary (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
