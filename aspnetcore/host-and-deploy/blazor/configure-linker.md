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
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Konfigurace linkeru pro ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly provádí propojování [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) během sestavení za účelem oříznutí zbytečných Il z výstupních sestavení aplikace. Linker je při sestavování konfigurace ladění zakázán. Aby bylo možné linker povolit, musí aplikace sestavit v konfiguraci vydání. Při nasazování aplikací Blazor WebAssembly doporučujeme sestavovat v vydaných verzích. 

Propojení aplikace se optimalizuje pro velikost, ale může mít škodlivé účinky. Aplikace, které používají reflexi nebo související dynamické funkce, mohou být při oříznutí přerušeny, protože linker neví o tomto dynamickém chování a nemůže určit obecně, které typy jsou požadovány pro reflexi za běhu. Aby bylo možné tyto aplikace oříznout, musí být linker informován o jakýchkoli typech vyžadovaných odrazem v kódu a v balíčcích nebo architekturách, na kterých aplikace závisí. 

Aby se zajistilo, že bude aplikace po nasazení správně fungovat, je důležité otestovat sestavení vydaných verzí aplikace často během vývoje.

Odkazy na aplikace Blazor se dají konfigurovat pomocí těchto funkcí MSBuild:

* Nakonfigurujte globálně propojení s [vlastností MSBuild](#control-linking-with-an-msbuild-property).
* Řízení propojení podle jednotlivých sestavení pomocí [konfiguračního souboru](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Řízení propojení pomocí vlastnosti MSBuild

Odkazování je povolené, když je aplikace sestavená `Release` konfiguračním. Chcete-li toto nastavení změnit, nakonfigurujte vlastnost `BlazorWebAssemblyEnableLinking` MSBuild v souboru projektu:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
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

Další informace naleznete v tématu [linkeru Il: syntaxe popisovače XML](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).

### <a name="configure-the-linker-for-internationalization"></a>Konfigurace linkeru pro mezinárodní využití

Ve výchozím nastavení konfigurace linkeru Blazor pro aplikace Blazor WebAssembly vyřadí informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot. Odebrání těchto sestavení minimalizuje velikost aplikace.

Chcete-li určit, která sestavení I18N jsou zachována, nastavte vlastnost `<MonoLinkerI18NAssemblies>` MSBuild v souboru projektu:

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| Hodnota oblasti     | Sestavení oblasti mono    |
| ---------------- | ----------------------- |
| `all`            | Všechna sestavení, která jsou součástí |
| `cjk`            | *I18N. CJK. dll*          |
| `mideast`        | *I18N. MidEast. dll*      |
| `none` (výchozí) | Žádná                    |
| `other`          | *I18N. Jiná knihovna. dll*        |
| `rare`           | *I18N. Vzácná knihovna DLL*         |
| `west`           | *I18N. Západ. dll*         |

Oddělte více hodnot pomocí čárky (například `mideast,west`).

Další informace najdete v tématu [i18n: knihovna rozhraní Pnetlib pro mezinárodní navýšení (mono/mono úložiště GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
