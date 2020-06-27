---
title: Konfigurace linkeru pro ASP.NET CoreBlazor
author: guardrex
description: Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: 568efe9971aefc11841c42789ac7f2af3004003f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402699"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="10e29-103">Konfigurace linkeru pro ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="10e29-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="10e29-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="10e29-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor WebAssembly<span data-ttu-id="10e29-105">provádí propojování v [prostředním jazyce (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení za účelem oříznutí nepotřebného Il z výstupních sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="10e29-105"> performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="10e29-106">Linker je při sestavování konfigurace ladění zakázán.</span><span class="sxs-lookup"><span data-stu-id="10e29-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="10e29-107">Aby bylo možné linker povolit, musí aplikace sestavit v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="10e29-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="10e29-108">Při nasazování aplikací doporučujeme sestavovat v vydaných verzích Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="10e29-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="10e29-109">Propojení aplikace se optimalizuje pro velikost, ale může mít škodlivé účinky.</span><span class="sxs-lookup"><span data-stu-id="10e29-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="10e29-110">Aplikace, které používají reflexi nebo související dynamické funkce, mohou být při oříznutí přerušeny, protože linker neví o tomto dynamickém chování a nemůže určit obecně, které typy jsou požadovány pro reflexi za běhu.</span><span class="sxs-lookup"><span data-stu-id="10e29-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="10e29-111">Aby bylo možné tyto aplikace oříznout, musí být linker informován o jakýchkoli typech vyžadovaných odrazem v kódu a v balíčcích nebo architekturách, na kterých aplikace závisí.</span><span class="sxs-lookup"><span data-stu-id="10e29-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="10e29-112">Aby se zajistilo, že bude aplikace po nasazení správně fungovat, je důležité otestovat sestavení vydaných verzí aplikace často během vývoje.</span><span class="sxs-lookup"><span data-stu-id="10e29-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="10e29-113">Odkazy na Blazor aplikace lze konfigurovat pomocí těchto funkcí nástroje MSBuild:</span><span class="sxs-lookup"><span data-stu-id="10e29-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="10e29-114">Nakonfigurujte globálně propojení s [vlastností MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="10e29-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="10e29-115">Řízení propojení podle jednotlivých sestavení pomocí [konfiguračního souboru](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="10e29-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="10e29-116">Řízení propojení pomocí vlastnosti MSBuild</span><span class="sxs-lookup"><span data-stu-id="10e29-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="10e29-117">Propojení je povoleno, pokud je aplikace integrována v `Release` konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="10e29-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="10e29-118">Chcete-li toto nastavení změnit, nakonfigurujte `BlazorWebAssemblyEnableLinking` vlastnost MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="10e29-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="10e29-119">Řízení propojování pomocí konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="10e29-119">Control linking with a configuration file</span></span>

<span data-ttu-id="10e29-120">Řízení propojení na základě sestavení zadáním konfiguračního souboru XML a zadáním souboru jako položky MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="10e29-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="10e29-121">`LinkerConfig.xml`:</span><span class="sxs-lookup"><span data-stu-id="10e29-121">`LinkerConfig.xml`:</span></span>

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

<span data-ttu-id="10e29-122">Další informace a příklady naleznete v tématu [formáty dat (úložiště GitHub mono/Linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="10e29-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="10e29-123">Přidání konfiguračního souboru linkeru XML do knihovny</span><span class="sxs-lookup"><span data-stu-id="10e29-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="10e29-124">Chcete-li nakonfigurovat linker pro konkrétní knihovnu, přidejte konfigurační soubor linkeru XML do knihovny jako vložený prostředek.</span><span class="sxs-lookup"><span data-stu-id="10e29-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="10e29-125">Vložený prostředek musí mít stejný název jako sestavení.</span><span class="sxs-lookup"><span data-stu-id="10e29-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="10e29-126">V následujícím příkladu `LinkerConfig.xml` je soubor zadán jako vložený prostředek, který má stejný název jako sestavení knihovny:</span><span class="sxs-lookup"><span data-stu-id="10e29-126">In the following example, the `LinkerConfig.xml` file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="10e29-127">Konfigurace linkeru pro mezinárodní využití</span><span class="sxs-lookup"><span data-stu-id="10e29-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="10e29-128">Ve výchozím nastavení Blazor je konfigurace linkeru pro Blazor WebAssembly aplikace odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot.</span><span class="sxs-lookup"><span data-stu-id="10e29-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="10e29-129">Odebrání těchto sestavení minimalizuje velikost aplikace.</span><span class="sxs-lookup"><span data-stu-id="10e29-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="10e29-130">Chcete-li určit, která sestavení I18N jsou zachována, nastavte `<BlazorWebAssemblyI18NAssemblies>` vlastnost MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="10e29-130">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="10e29-131">Hodnota oblasti</span><span class="sxs-lookup"><span data-stu-id="10e29-131">Region Value</span></span>     | <span data-ttu-id="10e29-132">Sestavení oblasti mono</span><span class="sxs-lookup"><span data-stu-id="10e29-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="10e29-133">Všechna sestavení, která jsou součástí</span><span class="sxs-lookup"><span data-stu-id="10e29-133">All assemblies included</span></span> |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| <span data-ttu-id="10e29-134">`none`výchozí</span><span class="sxs-lookup"><span data-stu-id="10e29-134">`none` (default)</span></span> | <span data-ttu-id="10e29-135">Žádná</span><span class="sxs-lookup"><span data-stu-id="10e29-135">None</span></span>                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

<span data-ttu-id="10e29-136">Oddělte více hodnot pomocí čárky (například `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="10e29-136">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="10e29-137">Další informace najdete v tématu [i18n: knihovna rozhraní Pnetlib pro mezinárodní navýšení (mono/mono úložiště GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="10e29-137">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10e29-138">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="10e29-138">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>