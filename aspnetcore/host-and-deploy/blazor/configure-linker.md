---
title: Konfigurace Linkeru pro Blazor
author: guardrex
description: Zjistěte, jak řídit Linkeru Intermediate Language (IL) při vytváření aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 01e18498a16e86392755b02b92ffda929669cb7d
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614776"
---
# <a name="configure-the-linker-for-blazor"></a>Konfigurace Linkeru pro Blazor

Podle [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Provádí Blazor [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) propojení během každé sestavení pro vydání režimu z aplikace odebrat nepotřebné IL výstupního sestavení.

Sestavení ovládacího prvku propojení některou z následujících postupů:

* Zakázat globálně propojení s [vlastnost MSBuild](#disable-linking-with-a-msbuild-property).
* Ovládací prvek propojení na základě na sestavení s [konfigurační soubor](#control-linking-with-a-configuration-file).

## <a name="disable-linking-with-a-msbuild-property"></a>Zakázat propojení s vlastností MSBuild

Propojení je povolené ve výchozím nastavení v režimu vydání, při vytváření aplikace, která zahrnuje publikování. Chcete-li zakázat propojení pro všechna sestavení, nastavte `<BlazorLinkOnBuild>` vlastnost MSBuild `false` v souboru projektu:

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Ovládací prvek propojení s konfiguračním souborem

Ovládací prvek propojení na základě za sestavení tak, že poskytuje konfigurační soubor XML a zadání souboru jako položky nástroje MSBuild v souboru projektu:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

*Linker.xml*:

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

Další informace najdete v tématu [IL Linkeru: Syntaxe xml popisovače](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).
