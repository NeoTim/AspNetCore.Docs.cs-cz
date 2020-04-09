---
title: Konfigurace propojovacího programu pro ASP.NET jádraBlazor
author: guardrex
description: Přečtěte si, jak řídit propojovací Blazor linku mezilehlého jazyka (IL) při vytváření aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218658"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="cfcd1-103">Konfigurace linkeru pro ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cfcd1-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="cfcd1-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cfcd1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="cfcd1-105">Blazor WebAssembly provádí [propojení mezijazyk (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení k oříznutí zbytečné IL z výstupních sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="cfcd1-106">Propojovací systém je zakázán při vytváření konfigurace ladění.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="cfcd1-107">Aplikace musí být v konfiguraci vydání, aby bylo možné propojovat linker.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="cfcd1-108">Při nasazování aplikací Blazor WebAssembly doporučujeme vytvářet ve verzi.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="cfcd1-109">Propojení aplikace optimalizuje velikost, ale může mít škodlivé účinky.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="cfcd1-110">Aplikace, které používají reflexe nebo související dynamické funkce může přerušit při oříznutí, protože propojovací služba neví o tomto dynamickém chování a nemůže obecně určit, které typy jsou požadovány pro reflexi za běhu.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="cfcd1-111">Chcete-li tyto aplikace oříznout, propojovací program musí být informován o všech typech vyžadovaných reflexí v kódu a v balíčcích nebo architekturách, na kterých aplikace závisí.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="cfcd1-112">Chcete-li zajistit, aby oříznutá aplikace fungovala správně po nasazení, je důležité testovat sestavení aplikace release často při vývoji.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="cfcd1-113">Propojení pro aplikace Blazor lze konfigurovat pomocí těchto funkcí MSBuild:</span><span class="sxs-lookup"><span data-stu-id="cfcd1-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="cfcd1-114">Nakonfigurujte globální propojení s [vlastností MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="cfcd1-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="cfcd1-115">Řízení propojení pro jedno sestavení s [konfiguračním souborem](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="cfcd1-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="cfcd1-116">Řízení propojení s vlastností MSBuild</span><span class="sxs-lookup"><span data-stu-id="cfcd1-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="cfcd1-117">Propojení je povoleno, když `Release` je aplikace postavena v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="cfcd1-118">Chcete-li to `BlazorWebAssemblyEnableLinking` změnit, nakonfigurujte vlastnost MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="cfcd1-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="cfcd1-119">Ovládání propojení s konfiguračním souborem</span><span class="sxs-lookup"><span data-stu-id="cfcd1-119">Control linking with a configuration file</span></span>

<span data-ttu-id="cfcd1-120">Zadejte propojení na základě sestavení poskytnutím konfiguračního souboru XML a zadáním souboru jako položky MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="cfcd1-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="cfcd1-121">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="cfcd1-121">*LinkerConfig.xml*:</span></span>

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
      Fixes: https://github.com/dotnet/blazor/issues/239
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

<span data-ttu-id="cfcd1-122">Další informace naleznete [v tématu Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span><span class="sxs-lookup"><span data-stu-id="cfcd1-122">For more information, see [Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="cfcd1-123">Přidání konfiguračního souboru linkeru XML do knihovny</span><span class="sxs-lookup"><span data-stu-id="cfcd1-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="cfcd1-124">Chcete-li nakonfigurovat propojovací síť pro určitou knihovnu, přidejte do knihovny jako vložený prostředek konfigurační soubor linkeru XML.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="cfcd1-125">Vložený prostředek musí mít stejný název jako sestavení.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="cfcd1-126">V následujícím příkladu je soubor *LinkerConfig.xml* určen jako vložený prostředek, který má stejný název jako sestavení knihovny:</span><span class="sxs-lookup"><span data-stu-id="cfcd1-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="cfcd1-127">Konfigurace propojovacího programu pro internacionalizaci</span><span class="sxs-lookup"><span data-stu-id="cfcd1-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="cfcd1-128">Ve výchozím nastavení blazorje propojovací zařízení konfigurace pro aplikace Blazor WebAssembly strips out internacionalizace informace s výjimkou národních prostředí výslovně požadováno.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="cfcd1-129">Odebrání těchto sestavení minimalizuje velikost aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfcd1-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="cfcd1-130">Chcete-li určit, která sestavení I18N `<MonoLinkerI18NAssemblies>` budou zachována, nastavte vlastnost MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="cfcd1-130">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="cfcd1-131">Hodnota oblasti</span><span class="sxs-lookup"><span data-stu-id="cfcd1-131">Region Value</span></span>     | <span data-ttu-id="cfcd1-132">Sestavení mono oblasti</span><span class="sxs-lookup"><span data-stu-id="cfcd1-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="cfcd1-133">Všechny sestavy zahrnuty</span><span class="sxs-lookup"><span data-stu-id="cfcd1-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="cfcd1-134">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="cfcd1-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="cfcd1-135">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="cfcd1-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="cfcd1-136">`none`(výchozí)</span><span class="sxs-lookup"><span data-stu-id="cfcd1-136">`none` (default)</span></span> | <span data-ttu-id="cfcd1-137">Žádný</span><span class="sxs-lookup"><span data-stu-id="cfcd1-137">None</span></span>                    |
| `other`          | <span data-ttu-id="cfcd1-138">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="cfcd1-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="cfcd1-139">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="cfcd1-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="cfcd1-140">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="cfcd1-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="cfcd1-141">Čárkou oddělte více hodnot `mideast,west`(například).</span><span class="sxs-lookup"><span data-stu-id="cfcd1-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="cfcd1-142">Další informace naleznete v tématu [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="cfcd1-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
