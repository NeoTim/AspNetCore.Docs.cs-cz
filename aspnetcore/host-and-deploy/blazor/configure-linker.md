---
title: Konfigurace linkeru pro ASP.NET Core Blazor
author: guardrex
description: Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování aplikace Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: b08ec26fb8d139223c57774600bc3cb19a56ac49
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083300"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="2eb28-103">Konfigurace linkeru pro ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="2eb28-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="2eb28-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2eb28-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2eb28-105">Blazor WebAssembly provádí propojování [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení za účelem oříznutí zbytečných Il z výstupních sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="2eb28-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="2eb28-106">Linker je při sestavování konfigurace ladění zakázán.</span><span class="sxs-lookup"><span data-stu-id="2eb28-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="2eb28-107">Aby bylo možné linker povolit, musí aplikace sestavit v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="2eb28-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="2eb28-108">Při nasazování aplikací Blazor WebAssembly doporučujeme sestavovat v vydaných verzích.</span><span class="sxs-lookup"><span data-stu-id="2eb28-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="2eb28-109">Propojení aplikace se optimalizuje pro velikost, ale může mít škodlivé účinky.</span><span class="sxs-lookup"><span data-stu-id="2eb28-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="2eb28-110">Aplikace, které používají reflexi nebo související dynamické funkce, mohou být při oříznutí přerušeny, protože linker neví o tomto dynamickém chování a nemůže určit obecně, které typy jsou požadovány pro reflexi za běhu.</span><span class="sxs-lookup"><span data-stu-id="2eb28-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="2eb28-111">Aby bylo možné tyto aplikace oříznout, musí být linker informován o jakýchkoli typech vyžadovaných odrazem v kódu a v balíčcích nebo architekturách, na kterých aplikace závisí.</span><span class="sxs-lookup"><span data-stu-id="2eb28-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="2eb28-112">Aby se zajistilo, že bude aplikace po nasazení správně fungovat, je důležité otestovat sestavení vydaných verzí aplikace často během vývoje.</span><span class="sxs-lookup"><span data-stu-id="2eb28-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="2eb28-113">Odkazy na aplikace Blazor se dají konfigurovat pomocí těchto funkcí MSBuild:</span><span class="sxs-lookup"><span data-stu-id="2eb28-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="2eb28-114">Nakonfigurujte globálně propojení s [vlastností MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="2eb28-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="2eb28-115">Řízení propojení podle jednotlivých sestavení pomocí [konfiguračního souboru](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="2eb28-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="2eb28-116">Řízení propojení pomocí vlastnosti MSBuild</span><span class="sxs-lookup"><span data-stu-id="2eb28-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="2eb28-117">Odkazování je povolené, když je aplikace sestavená `Release` konfiguračním.</span><span class="sxs-lookup"><span data-stu-id="2eb28-117">Linking is enabled when an app is built in `Release` configuation.</span></span> <span data-ttu-id="2eb28-118">Chcete-li toto nastavení změnit, nakonfigurujte vlastnost `BlazorWebAssemblyEnableLinking` MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="2eb28-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="2eb28-119">Řízení propojování pomocí konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="2eb28-119">Control linking with a configuration file</span></span>

<span data-ttu-id="2eb28-120">Řízení propojení na základě sestavení zadáním konfiguračního souboru XML a zadáním souboru jako položky MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="2eb28-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="2eb28-121">*Linker. XML*:</span><span class="sxs-lookup"><span data-stu-id="2eb28-121">*Linker.xml*:</span></span>

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

<span data-ttu-id="2eb28-122">Další informace naleznete v tématu [linkeru Il: syntaxe popisovače XML](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="2eb28-122">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="2eb28-123">Konfigurace linkeru pro mezinárodní využití</span><span class="sxs-lookup"><span data-stu-id="2eb28-123">Configure the linker for internationalization</span></span>

<span data-ttu-id="2eb28-124">Ve výchozím nastavení konfigurace linkeru Blazor pro aplikace Blazor WebAssembly vyřadí informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot.</span><span class="sxs-lookup"><span data-stu-id="2eb28-124">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="2eb28-125">Odebrání těchto sestavení minimalizuje velikost aplikace.</span><span class="sxs-lookup"><span data-stu-id="2eb28-125">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="2eb28-126">Chcete-li určit, která sestavení I18N jsou zachována, nastavte vlastnost `<MonoLinkerI18NAssemblies>` MSBuild v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="2eb28-126">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="2eb28-127">Hodnota oblasti</span><span class="sxs-lookup"><span data-stu-id="2eb28-127">Region Value</span></span>     | <span data-ttu-id="2eb28-128">Sestavení oblasti mono</span><span class="sxs-lookup"><span data-stu-id="2eb28-128">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="2eb28-129">Všechna sestavení, která jsou součástí</span><span class="sxs-lookup"><span data-stu-id="2eb28-129">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="2eb28-130">*I18N. CJK. dll*</span><span class="sxs-lookup"><span data-stu-id="2eb28-130">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="2eb28-131">*I18N. MidEast. dll*</span><span class="sxs-lookup"><span data-stu-id="2eb28-131">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="2eb28-132">`none` (výchozí)</span><span class="sxs-lookup"><span data-stu-id="2eb28-132">`none` (default)</span></span> | <span data-ttu-id="2eb28-133">Žádná</span><span class="sxs-lookup"><span data-stu-id="2eb28-133">None</span></span>                    |
| `other`          | <span data-ttu-id="2eb28-134">*I18N. Jiná knihovna. dll*</span><span class="sxs-lookup"><span data-stu-id="2eb28-134">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="2eb28-135">*I18N. Vzácná knihovna DLL*</span><span class="sxs-lookup"><span data-stu-id="2eb28-135">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="2eb28-136">*I18N. Západ. dll*</span><span class="sxs-lookup"><span data-stu-id="2eb28-136">*I18N.West.dll*</span></span>         |

<span data-ttu-id="2eb28-137">Oddělte více hodnot pomocí čárky (například `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="2eb28-137">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="2eb28-138">Další informace najdete v tématu [i18n: knihovna rozhraní Pnetlib pro mezinárodní navýšení (mono/mono úložiště GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="2eb28-138">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
