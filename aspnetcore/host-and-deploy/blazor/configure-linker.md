---
title: Konfigurace linkeru pro ASP.NET Core Blazor
author: guardrex
description: Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování aplikace v Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: d3dd69e263e88ca1fc301eefc0da186a023aa96f
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211595"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Konfigurace linkeru pro ASP.NET Core Blazor

Podle [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor provádí propojení s [pokročilým jazykem (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení pro vydání, aby se z výstupních sestavení aplikace odstranilo zbytečné Il.

Ovládací prvek propojuje sestavení pomocí některého z následujících přístupů:

* Zakáže propojení globálně s [vlastností MSBuild](#disable-linking-with-a-msbuild-property).
* Řízení propojení podle jednotlivých sestavení pomocí [konfiguračního souboru](#control-linking-with-a-configuration-file).

## <a name="disable-linking-with-a-msbuild-property"></a>Zakázat propojení s vlastností MSBuild

Propojení je ve výchozím nastavení povoleno v režimu vydání, když je sestavena aplikace, která zahrnuje publikování. Chcete-li zakázat propojování pro všechna sestavení `BlazorLinkOnBuild` , nastavte vlastnost `false` MSBuild na hodnotu v souboru projektu:

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Řízení propojování pomocí konfiguračního souboru

Řízení propojení na základě sestavení zadáním konfiguračního souboru XML a zadáním souboru jako položky MSBuild v souboru projektu:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

*Linker. XML*:

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

Další informace najdete v tématu [linkeru Il: Syntaxe popisovače](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)XML
