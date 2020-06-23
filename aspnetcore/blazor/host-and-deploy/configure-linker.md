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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: 76af450df70fe666ea1b951cb4b41696057c5e67
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243574"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Konfigurace linkeru pro ASP.NET CoreBlazor

Od [Luke Latham](https://github.com/guardrex)

BlazorPři vystřihování nepotřebného IL z výstupních sestavení aplikace provede rozhraní WebAssembly propojení s [pokročilým jazykem (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení. Linker je při sestavování konfigurace ladění zakázán. Aby bylo možné linker povolit, musí aplikace sestavit v konfiguraci vydání. Při nasazování aplikací WebAssembly doporučujeme sestavovat v vydaných verzích Blazor . 

Propojení aplikace se optimalizuje pro velikost, ale může mít škodlivé účinky. Aplikace, které používají reflexi nebo související dynamické funkce, mohou být při oříznutí přerušeny, protože linker neví o tomto dynamickém chování a nemůže určit obecně, které typy jsou požadovány pro reflexi za běhu. Aby bylo možné tyto aplikace oříznout, musí být linker informován o jakýchkoli typech vyžadovaných odrazem v kódu a v balíčcích nebo architekturách, na kterých aplikace závisí. 

Aby se zajistilo, že bude aplikace po nasazení správně fungovat, je důležité otestovat sestavení vydaných verzí aplikace často během vývoje.

Odkazy na Blazor aplikace lze konfigurovat pomocí těchto funkcí nástroje MSBuild:

* Nakonfigurujte globálně propojení s [vlastností MSBuild](#control-linking-with-an-msbuild-property).
* Řízení propojení podle jednotlivých sestavení pomocí [konfiguračního souboru](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Řízení propojení pomocí vlastnosti MSBuild

Propojení je povoleno, pokud je aplikace integrována v `Release` konfiguraci. Chcete-li toto nastavení změnit, nakonfigurujte `BlazorWebAssemblyEnableLinking` vlastnost MSBuild v souboru projektu:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Řízení propojování pomocí konfiguračního souboru

Řízení propojení na základě sestavení zadáním konfiguračního souboru XML a zadáním souboru jako položky MSBuild v souboru projektu:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

`LinkerConfig.xml`:

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

Další informace a příklady naleznete v tématu [formáty dat (úložiště GitHub mono/Linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Přidání konfiguračního souboru linkeru XML do knihovny

Chcete-li nakonfigurovat linker pro konkrétní knihovnu, přidejte konfigurační soubor linkeru XML do knihovny jako vložený prostředek. Vložený prostředek musí mít stejný název jako sestavení.

V následujícím příkladu `LinkerConfig.xml` je soubor zadán jako vložený prostředek, který má stejný název jako sestavení knihovny:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Konfigurace linkeru pro mezinárodní využití

Ve výchozím nastavení se Blazor Konfigurace linkeru pro Blazor aplikace pro WebAssembly odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot. Odebrání těchto sestavení minimalizuje velikost aplikace.

Chcete-li určit, která sestavení I18N jsou zachována, nastavte `<BlazorWebAssemblyI18NAssemblies>` vlastnost MSBuild v souboru projektu:

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| Hodnota oblasti     | Sestavení oblasti mono    |
| ---------------- | ----------------------- |
| `all`            | Všechna sestavení, která jsou součástí |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| `none`výchozí | Žádné                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

Oddělte více hodnot pomocí čárky (například `mideast,west` ).

Další informace najdete v tématu [i18n: knihovna rozhraní Pnetlib pro mezinárodní navýšení (mono/mono úložiště GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
