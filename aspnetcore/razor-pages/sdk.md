---
title: Sada Razor ASP.NET Core SDK
author: Rick-Anderson
description: Přečtěte Razor si, jak stránky v ASP.NET Core zjednodušují a produktivnější vytváření kódu na stránce, než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/sdk
ms.openlocfilehash: a49f183c4c037a1654e79bdb672b758684137cbe
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776731"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Přehled

[!INCLUDE[](~/includes/2.1-SDK.md)] Obsahuje sadu `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK). Sada Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* Je vyžadován k sestavení, sbalení a publikování projektů obsahujících soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projekty založené na MVC nebo [Blazor](xref:blazor/index) .
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor (*. cshtml* nebo *. Razor*).

Sada Razor SDK obsahuje `Content` položky s `Include` atributy nastavenými na `**\*.cshtml` vzory `**\*.razor` pro expanzi názvů a. Jsou publikovány vyhovující soubory.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardizace prostředí kolem vytváření, balení a publikování projektů, které obsahují soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projektů založených na MVC.
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor.

Sada Razor SDK obsahuje `Content` položku s `Include` atributem nastaveným na vzor `**\*.cshtml` expanze. Jsou publikovány vyhovující soubory.

::: moniker-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Použití sady Razor SDK

Pro většinu webových aplikací není nutné explicitně odkazovat na sadu Razor SDK.

::: moniker range=">= aspnetcore-3.0"

Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení nebo Razor Pages Razor, doporučujeme začít se šablonou projektu knihovny tříd Razor (RCL). RCL, který se používá k sestavování souborů Blazor (*. Razor*), vyžaduje minimální odkaz na balíček [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . RCL, který se používá k vytvoření zobrazení nebo stránek (souborů *. cshtml* ) Razor, vyžaduje minimálně cílení `netcoreapp3.0` na nebo vyšší a `FrameworkReference` má v souboru projektu [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení Razor nebo Razor Pages:

* Použít `Microsoft.NET.Sdk.Razor` místo `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* K získání dalších závislostí, `Microsoft.AspNetCore.Mvc` které jsou požadovány pro sestavení a kompilování Razor Pages a zobrazení Razor, je obvykle vyžadován odkaz na balíček. Projekt by měl mít minimálně přidat odkazy na balíčky:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design` Balíček poskytuje úlohy a cíle kompilace Razor pro projekt.

  Předchozí balíčky jsou součástí nástroje `Microsoft.AspNetCore.Mvc`. Následující kód ukazuje soubor projektu, který používá sadu Razor SDK k sestavení souborů Razor pro aplikaci ASP.NET Core Razor Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Balíčky `Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app). Reference k balíčku bez `Microsoft.AspNetCore.App` verze ale poskytuje Metapackage aplikaci, která neobsahuje nejnovější verzi nástroje. `Microsoft.AspNetCore.Razor.Design` Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`), aby byly zahrnuty nejnovější opravy v době sestavení pro Razor. Další informace najdete v [tomto problému GitHubu](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Vlastnosti

Následující vlastnosti řídí chování sady SDK Razor v rámci sestavení projektu:

* `RazorCompileOnBuild`&ndash; Když `true`, kompiluje a generuje sestavení Razor jako součást sestavení projektu. Výchozí hodnota `true`je.
* `RazorCompileOnPublish`&ndash; Když `true`, zkompiluje a vygeneruje sestavení Razor jako součást publikování projektu. Výchozí hodnota `true`je.

Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů do sady Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Počínaje ASP.NET Core 3,0 se zobrazení MVC nebo Razor Pages nezpracovávají ve výchozím nastavení, pokud `RazorCompileOnBuild` jsou `RazorCompileOnPublish` vlastnosti nebo MSBuild v souboru projektu zakázané. Aplikace musí přidat explicitní odkaz na balíček [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) , pokud aplikace spoléhá na kompilaci za běhu za účelem zpracování souborů *. cshtml* .

::: moniker-end

| Items | Popis |
| ----- | ----------- |
| `RazorGenerate` | Prvky položky (soubory *. cshtml* ), které jsou vstupy pro generování kódu. |
| `RazorComponent` | Prvky položky (soubory *. Razor* ), které jsou vstupy pro generování kódu komponenty Razor. |
| `RazorCompile` | Prvky položky (soubory *. cs* ), které jsou vstupy pro cíle kompilace Razor. Toto `ItemGroup` použijte k určení dalších souborů, které mají být zkompilovány do sestavení Razor. |
| `RazorTargetAssemblyAttribute` | Prvky položky použité pro kód generují atributy pro sestavení Razor. Příklad:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Prvky položky přidané jako vložené prostředky do vygenerovaného sestavení Razor |

::: moniker range=">= aspnetcore-3.0"

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavení vytvořeného syntaxí Razor |
| `RazorOutputPath` | Výstupní adresář Razor |
| `RazorCompileToolset` | Slouží k určení sady nástrojů používané k sestavení sestavení Razor. Platné hodnoty jsou `Implicit`, `RazorSDK`a `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí je `true`. V `true`případě zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu. V případě, `Microsoft.NET.Sdk.Web`že jsou odkazovány prostřednictvím, jsou zahrnuty také soubory pod soubory *wwwroot* a config. |
| `EnableDefaultRazorGenerateItems` | When `true`zahrnuje soubory *. cshtml* z `Content` položek v `RazorGenerate` položkách. |
| `GenerateRazorTargetAssemblyInfo` | Když `true`, vygeneruje soubor *. cs* obsahující atributy určené pomocí `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Kdy `true`přidá výchozí sadu atributů sestavení do `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Při `true`kopírování `RazorGenerate` souborů položek (*. cshtml*) do adresáře pro publikování. Soubory Razor nejsou typicky nutné pro publikovanou aplikaci, pokud se účastní kompilace při sestavení nebo době publikování. Výchozí hodnota `false`je. |
| `PreserveCompilationReferences` | Při `true`kopírování položek referenčního sestavení do adresáře pro publikování. V případě, že dojde k kompilaci Razor v době sestavení nebo při publikování, se obvykle nevyžadují referenční sestavení pro publikovanou aplikaci. Nastavte na `true` , pokud publikovaná aplikace vyžaduje kompilaci za běhu. Například nastavte hodnotu na `true` , pokud aplikace upraví soubory *. cshtml* za běhu nebo používá vložená zobrazení. Výchozí hodnota `false`je. |
| `IncludeRazorContentInPack` | V `true`případě jsou všechny položky obsahu Razor (soubory *. cshtml* ) označeny pro zahrnutí do generovaného balíčku NuGet. Výchozí hodnota `false`je. |
| `EmbedRazorGenerateSources` | Když `true`přidá položky RazorGenerate (*. cshtml*) jako vložené soubory do vygenerovaného sestavení Razor. Výchozí hodnota `false`je. |
| `UseRazorBuildServer` | V `true`případě používá trvalý proces sestavení serveru pro přesměrování práce generování kódu. Výchozí hodnota je `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Když `true`sada SDK generuje další atributy, pomocí kterých MVC za běhu provede zjišťování částí aplikace. |
| `DefaultWebContentItemExcludes` | Vzor expanze názvů pro prvky položky, které mají být vyloučeny `Content` ze skupiny položek v projektech, které cílí na web nebo sadu Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | `true`Soubory *. config* a *. JSON* se nekopírují do výstupního adresáře sestavení. |
| `AddRazorSupportForMvc` | Když `true`aplikace nakonfiguruje sadu Razor SDK tak, aby přidala podporu pro konfiguraci MVC, která je požadována při sestavování aplikací obsahujících zobrazení MVC nebo Razor Pages. Tato vlastnost je implicitně nastavená pro projekty .NET Core 3,0 nebo novější, které cílí na webovou sadu SDK. |
| `RazorLangVersion` | Verze jazyka Razor, na kterou se má cílit |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavení vytvořeného Razor. |
| `RazorOutputPath` | Razor Výstupní adresář. |
| `RazorCompileToolset` | Slouží k určení sady nástrojů používané k sestavení Razor sestavení. Platné hodnoty jsou `Implicit`, `RazorSDK`a `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí je `true`. V `true`případě zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu. V případě, `Microsoft.NET.Sdk.Web`že jsou odkazovány prostřednictvím, jsou zahrnuty také soubory pod soubory *wwwroot* a config. |
| `EnableDefaultRazorGenerateItems` | When `true`zahrnuje soubory *. cshtml* z `Content` položek v `RazorGenerate` položkách. |
| `GenerateRazorTargetAssemblyInfo` | Když `true`, vygeneruje soubor *. cs* obsahující atributy určené pomocí `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Kdy `true`přidá výchozí sadu atributů sestavení do `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Při `true`kopírování `RazorGenerate` souborů položek (*. cshtml*) do adresáře pro publikování. Pro publikovanou Razor aplikaci se obvykle nevyžadují soubory, pokud se účastní kompilace při sestavení nebo době publikování. Výchozí hodnota `false`je. |
| `CopyRefAssembliesToPublishDirectory` | Při `true`kopírování položek referenčního sestavení do adresáře pro publikování. V případě Razor , že je kompilace provedena v době sestavení nebo v době publikování, nejsou obvykle referenční sestavení vyžadována pro publikovanou aplikaci. Nastavte na `true` , pokud publikovaná aplikace vyžaduje kompilaci za běhu. Například nastavte hodnotu na `true` , pokud aplikace upraví soubory *. cshtml* za běhu nebo používá vložená zobrazení. Výchozí hodnota `false`je. |
| `IncludeRazorContentInPack` | V `true`případě jsou Razor všechny položky obsahu (soubory *. cshtml* ) označeny pro zahrnutí do generovaného balíčku NuGet. Výchozí hodnota `false`je. |
| `EmbedRazorGenerateSources` | Když `true`přidá položky RazorGenerate (*. cshtml*) jako vložené soubory do generovaného Razor sestavení. Výchozí hodnota `false`je. |
| `UseRazorBuildServer` | V `true`případě používá trvalý proces sestavení serveru pro přesměrování práce generování kódu. Výchozí hodnota je `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Když `true`sada SDK generuje další atributy, pomocí kterých MVC za běhu provede zjišťování částí aplikace. |
| `DefaultWebContentItemExcludes` | Vzor expanze názvů pro prvky položky, které mají být vyloučeny `Content` ze skupiny položek v projektech, které cílí Razor na web nebo sadu SDK |
| `ExcludeConfigFilesFromBuildOutput` | `true`Soubory *. config* a *. JSON* se nekopírují do výstupního adresáře sestavení. |
| `AddRazorSupportForMvc` | Když `true`aplikace nakonfiguruje Razor sadu SDK tak, aby přidala podporu pro konfiguraci MVC, která je požadována při sestavování aplikací obsahujících zobrazení nebo Razor stránky MVC. Tato vlastnost je implicitně nastavená pro projekty .NET Core 3,0 nebo novější, které cílí na webovou sadu SDK. |
| `RazorLangVersion` | Verze Razor jazyka, který se má cílit |

::: moniker-end

Další informace o vlastnostech naleznete v tématu [vlastnosti MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Cíle

Razor Sada SDK definuje dva primární cíle:

* `RazorGenerate`&ndash; Kód generuje soubory *. cs* z `RazorGenerate` prvků Item. `RazorGenerateDependsOn` Vlastnost použijte k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.
* `RazorCompile`&ndash; Zkompiluje vygenerované soubory *. cs* do Razor sestavení. Použijte `RazorCompileDependsOn` k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.
* `RazorComponentGenerate`&ndash; Kód generuje soubory *. cs* pro `RazorComponent` prvky položky. `RazorComponentGenerateDependsOn` Vlastnost použijte k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.

### <a name="runtime-compilation-of-razor-views"></a>Běhová kompilace Razor zobrazení

* Ve výchozím nastavení Razor SDK nepublikuje referenční sestavení, která jsou nutná k provedení kompilace za běhu. Výsledkem je selhání kompilace, když aplikační model spoléhá na kompilaci&mdash;za běhu. aplikace například používá vložená zobrazení nebo změny zobrazení po publikování aplikace. Nastavte `CopyRefAssembliesToPublishDirectory` na `true` pro pokračování v publikování referenčních sestavení.

* V případě webové aplikace zajistěte, aby vaše aplikace `Microsoft.NET.Sdk.Web` byla cílena na sadu SDK.

## <a name="razor-language-version"></a>Razorverze jazyka

Při cílení `Microsoft.NET.Sdk.Web` na sadu SDK Razor je jazyková verze odvozena z verze cílového rozhraní Framework aplikace. Pro projekty, které `Microsoft.NET.Sdk.Razor` cílí na sadu SDK, nebo v případě, že aplikace vyžaduje Razor jinou verzi jazyka než odvozená hodnota, lze verzi nakonfigurovat nastavením `<RazorLangVersion>` vlastnosti v souboru projektu aplikace:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razorverze jazyka je úzce integrovaná s verzí modulu runtime, pro který byl sestaven. Cílení na jazykovou verzi, která není navržena pro modul runtime, není podporována a nejspíš generuje chyby sestavení.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Přidání do formátu csproj pro .NET Core](/dotnet/core/tools/csproj)
* [Společné položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
