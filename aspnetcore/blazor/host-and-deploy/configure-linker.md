---
title: Konfigurace linkeru pro ASP.NET Core Blazor
author: guardrex
description: Naučte se řídit linker zprostředkujícího jazyka (IL) při sestavování Blazor aplikace.
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: 34582fdeb4951a110b03880887b978add07687f4
ms.sourcegitcommit: 0cfada7cbcd8e76aba0ae70eb6bbbf4437f287cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90081634"
---
# <a name="configure-the-linker-for-aspnet-core-no-locblazor"></a>Konfigurace linkeru pro ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex)

Blazor WebAssembly provádí propojování v [prostředním jazyce (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení za účelem oříznutí nepotřebného Il z výstupních sestavení aplikace. Linker je při sestavování konfigurace ladění zakázán. Aby bylo možné linker povolit, musí aplikace sestavit v konfiguraci vydání. Při nasazování aplikací doporučujeme sestavovat v vydaných verzích Blazor WebAssembly . 

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

Ve výchozím nastavení Blazor je konfigurace linkeru pro Blazor WebAssembly aplikace odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot. Odebrání těchto sestavení minimalizuje velikost aplikace.

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
| `none` výchozí | Žádné                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

Oddělte více hodnot pomocí čárky (například `mideast,west` ).

Další informace najdete v tématu [i18n: knihovna rozhraní Pnetlib pro mezinárodní navýšení (mono/mono úložiště GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).

## <a name="additional-resources"></a>Další materiály

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
