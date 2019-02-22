---
title: Konfigurace Linkeru pro Blazor
author: guardrex
description: Zjistěte, jak řídit Linkeru Intermediate Language (IL) při vytváření aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2019
uid: host-and-deploy/razor-components/configure-linker
ms.openlocfilehash: 7c53e7912ec3b0ae471ea38777f874f55a32487d
ms.sourcegitcommit: 0945078a09c372f17e9b003758ed87e99c2449f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56647938"
---
# <a name="configure-the-linker-for-blazor"></a><span data-ttu-id="389c3-103">Konfigurace Linkeru pro Blazor</span><span class="sxs-lookup"><span data-stu-id="389c3-103">Configure the Linker for Blazor</span></span>

<span data-ttu-id="389c3-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="389c3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="389c3-105">Provádí Blazor [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) propojení během každé sestavení pro vydání režimu odebrat nepotřebné IL z výstupního sestavení.</span><span class="sxs-lookup"><span data-stu-id="389c3-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during each Release mode build to remove unnecessary IL from the output assemblies.</span></span>

<span data-ttu-id="389c3-106">Můžete řídit sestavení propojení s jedním z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="389c3-106">You can control assembly linking with either of the following approaches:</span></span>

* <span data-ttu-id="389c3-107">Zakážete globálně propojení s vlastností MSBuild.</span><span class="sxs-lookup"><span data-stu-id="389c3-107">Disable linking globally with an MSBuild property.</span></span>
* <span data-ttu-id="389c3-108">Ovládací prvek propojení na základě na sestavení s konfiguračním souborem.</span><span class="sxs-lookup"><span data-stu-id="389c3-108">Control linking on a per-assembly basis with a configuration file.</span></span>

## <a name="disable-linking-with-an-msbuild-property"></a><span data-ttu-id="389c3-109">Zakázat propojení s vlastností MSBuild</span><span class="sxs-lookup"><span data-stu-id="389c3-109">Disable linking with an MSBuild property</span></span>

<span data-ttu-id="389c3-110">Propojení je povolené ve výchozím nastavení v režimu vydání, při vytváření aplikace, která zahrnuje publikování.</span><span class="sxs-lookup"><span data-stu-id="389c3-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="389c3-111">Chcete-li zakázat propojení pro všechna sestavení, nastavte `<BlazorLinkOnBuild>` vlastnost MSBuild `false` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="389c3-111">To disable linking for all assemblies, set the `<BlazorLinkOnBuild>` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="389c3-112">Ovládací prvek propojení s konfiguračním souborem</span><span class="sxs-lookup"><span data-stu-id="389c3-112">Control linking with a configuration file</span></span>

<span data-ttu-id="389c3-113">Propojení je možné řídit na základě za sestavení poskytováním konfigurační soubor XML a zadáte soubor jako položky nástroje MSBuild v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="389c3-113">Linking can be controlled on a per-assembly basis by providing an XML configuration file and specifying the file as an MSBuild item in the project file.</span></span>

<span data-ttu-id="389c3-114">Následující příklad je konfigurační soubor (*Linker.xml*):</span><span class="sxs-lookup"><span data-stu-id="389c3-114">The following is an example configuration file (*Linker.xml*):</span></span>

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

<span data-ttu-id="389c3-115">Další informace o formátu souborů pro konfigurační soubor, naleznete v tématu [IL Linkeru: Syntaxe xml popisovače](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="389c3-115">To learn more about the file format for the configuration file, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

<span data-ttu-id="389c3-116">Zadejte konfigurační soubor v souboru projektu `BlazorLinkerDescriptor` položky:</span><span class="sxs-lookup"><span data-stu-id="389c3-116">Specify the configuration file in the project file with the `BlazorLinkerDescriptor` item:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```
