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
# <a name="configure-the-linker-for-blazor"></a>Konfigurace Linkeru pro Blazor

Podle [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Provádí Blazor [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) propojení během každé sestavení pro vydání režimu odebrat nepotřebné IL z výstupního sestavení.

Můžete řídit sestavení propojení s jedním z následujících postupů:

* Zakážete globálně propojení s vlastností MSBuild.
* Ovládací prvek propojení na základě na sestavení s konfiguračním souborem.

## <a name="disable-linking-with-an-msbuild-property"></a>Zakázat propojení s vlastností MSBuild

Propojení je povolené ve výchozím nastavení v režimu vydání, při vytváření aplikace, která zahrnuje publikování. Chcete-li zakázat propojení pro všechna sestavení, nastavte `<BlazorLinkOnBuild>` vlastnost MSBuild `false` v souboru projektu:

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Ovládací prvek propojení s konfiguračním souborem

Propojení je možné řídit na základě za sestavení poskytováním konfigurační soubor XML a zadáte soubor jako položky nástroje MSBuild v souboru projektu.

Následující příklad je konfigurační soubor (*Linker.xml*):

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

Další informace o formátu souborů pro konfigurační soubor, naleznete v tématu [IL Linkeru: Syntaxe xml popisovače](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).

Zadejte konfigurační soubor v souboru projektu `BlazorLinkerDescriptor` položky:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```
