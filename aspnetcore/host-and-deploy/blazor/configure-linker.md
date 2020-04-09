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
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Konfigurace linkeru pro ASP.NET Core Blazor

Podle [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly provádí [propojení mezijazyk (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení k oříznutí zbytečné IL z výstupních sestavení aplikace. Propojovací systém je zakázán při vytváření konfigurace ladění. Aplikace musí být v konfiguraci vydání, aby bylo možné propojovat linker. Při nasazování aplikací Blazor WebAssembly doporučujeme vytvářet ve verzi. 

Propojení aplikace optimalizuje velikost, ale může mít škodlivé účinky. Aplikace, které používají reflexe nebo související dynamické funkce může přerušit při oříznutí, protože propojovací služba neví o tomto dynamickém chování a nemůže obecně určit, které typy jsou požadovány pro reflexi za běhu. Chcete-li tyto aplikace oříznout, propojovací program musí být informován o všech typech vyžadovaných reflexí v kódu a v balíčcích nebo architekturách, na kterých aplikace závisí. 

Chcete-li zajistit, aby oříznutá aplikace fungovala správně po nasazení, je důležité testovat sestavení aplikace release často při vývoji.

Propojení pro aplikace Blazor lze konfigurovat pomocí těchto funkcí MSBuild:

* Nakonfigurujte globální propojení s [vlastností MSBuild](#control-linking-with-an-msbuild-property).
* Řízení propojení pro jedno sestavení s [konfiguračním souborem](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Řízení propojení s vlastností MSBuild

Propojení je povoleno, když `Release` je aplikace postavena v konfiguraci. Chcete-li to `BlazorWebAssemblyEnableLinking` změnit, nakonfigurujte vlastnost MSBuild v souboru projektu:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Ovládání propojení s konfiguračním souborem

Zadejte propojení na základě sestavení poskytnutím konfiguračního souboru XML a zadáním souboru jako položky MSBuild v souboru projektu:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*LinkerConfig.xml*:

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

Další informace naleznete [v tématu Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Přidání konfiguračního souboru linkeru XML do knihovny

Chcete-li nakonfigurovat propojovací síť pro určitou knihovnu, přidejte do knihovny jako vložený prostředek konfigurační soubor linkeru XML. Vložený prostředek musí mít stejný název jako sestavení.

V následujícím příkladu je soubor *LinkerConfig.xml* určen jako vložený prostředek, který má stejný název jako sestavení knihovny:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Konfigurace propojovacího programu pro internacionalizaci

Ve výchozím nastavení blazorje propojovací zařízení konfigurace pro aplikace Blazor WebAssembly strips out internacionalizace informace s výjimkou národních prostředí výslovně požadováno. Odebrání těchto sestavení minimalizuje velikost aplikace.

Chcete-li určit, která sestavení I18N `<MonoLinkerI18NAssemblies>` budou zachována, nastavte vlastnost MSBuild v souboru projektu:

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| Hodnota oblasti     | Sestavení mono oblasti    |
| ---------------- | ----------------------- |
| `all`            | Všechny sestavy zahrnuty |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none`(výchozí) | Žádný                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Čárkou oddělte více hodnot `mideast,west`(například).

Další informace naleznete v tématu [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
