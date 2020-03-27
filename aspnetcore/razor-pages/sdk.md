---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2284131ce2d45ec6bc01ce38f91e2c951b108605
ms.sourcegitcommit: f3b1bcfd108e5d53f73abc0bf2555890869d953b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80321002"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Přehled

[!INCLUDE[](~/includes/2.1-SDK.md)] obsahuje sadu `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK). Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* Je vyžadován k sestavení, sbalení a publikování projektů obsahujících soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projekty založené na MVC nebo [Blazor](xref:blazor/index) .
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor ( *. cshtml* nebo *. Razor*).

Sada Razor SDK obsahuje `Content` položky s `Include` atributy nastavené na `**\*.cshtml` a `**\*.razor` vzory expanze. Jsou publikovány vyhovující soubory.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardizace prostředí kolem vytváření, balení a publikování projektů, které obsahují soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projektů založených na MVC.
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborech Razor.

Sada Razor SDK obsahuje `Content` položku s atributem `Include` nastaveným na `**\*.cshtml` vzorek expanze. Jsou publikovány vyhovující soubory.

::: moniker-end

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Použití sady Razor SDK

Většina webových aplikací není nutné explicitně odkazují na sadu SDK Razor.

::: moniker range=">= aspnetcore-3.0"

Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení nebo Razor Pages Razor, doporučujeme začít se šablonou projektu knihovny tříd Razor (RCL). RCL, který se používá k sestavování souborů Blazor ( *. Razor*), vyžaduje minimální odkaz na balíček [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . RCL, který se používá k vytvoření zobrazení nebo stránek (souborů *. cshtml* ) Razor, vyžaduje minimálně cílení na `netcoreapp3.0` nebo novější a má `FrameworkReference` do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v jeho souboru projektu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Použití sady SDK Razor k vytvoření knihovny tříd obsahující zobrazení syntaxe Razor nebo stránky Razor:

* Místo `Microsoft.NET.Sdk`použijte `Microsoft.NET.Sdk.Razor`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* K získání dalších závislostí, které jsou požadovány pro sestavení a kompilaci Razor Pages a zobrazení Razor, je obvykle vyžadován odkaz na balíček `Microsoft.AspNetCore.Mvc`. Minimálně byste vašeho projektu přidat odkazy na balíčky do:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Balíček `Microsoft.AspNetCore.Razor.Design` poskytuje úlohy a cíle kompilace Razor pro projekt.

  Předchozí balíčky jsou součástí `Microsoft.AspNetCore.Mvc`. Následující kód ukazuje soubor projektu, který používá sada Razor SDK k sestavení souborů Razor pro aplikace ASP.NET Core Razor Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Balíčky `Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app). Reference k balíčku `Microsoft.AspNetCore.App` bez verze ale obsahuje Metapackage k aplikaci, která neobsahuje nejnovější verzi `Microsoft.AspNetCore.Razor.Design`. Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`), aby byly zahrnuty nejnovější opravy v době sestavení pro Razor. Další informace najdete v [tomto problému GitHubu](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Vlastnosti

Následující vlastnosti řídit chování sady SDK pro syntaxi Razor jako součást sestavení projektu:

* `RazorCompileOnBuild` &ndash; při `true`kompiluje a generuje sestavení Razor jako součást sestavení projektu. Výchozí hodnota je `true`.
* `RazorCompileOnPublish` &ndash; při `true`kompiluje a generuje sestavení Razor jako součást publikování projektu. Výchozí hodnota je `true`.

Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Počínaje ASP.NET Core 3,0 se zobrazení MVC nebo Razor Pages nezpracovávají ve výchozím nastavení, pokud jsou vlastnosti `RazorCompileOnBuild` nebo `RazorCompileOnPublish` MSBuild v souboru projektu zakázané. Aplikace musí přidat explicitní odkaz na balíček [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) , pokud aplikace spoléhá na kompilaci za běhu za účelem zpracování souborů *. cshtml* .

::: moniker-end

| Items | Popis |
| ----- | ----------- |
| `RazorGenerate` | Prvky položky (soubory *. cshtml* ), které jsou vstupy pro generování kódu. |
| `RazorComponent` | Prvky položky (soubory *. Razor* ), které jsou vstupy pro generování kódu komponenty Razor. |
| `RazorCompile` | Prvky položky (soubory *. cs* ), které jsou vstupy pro cíle kompilace Razor. Tento `ItemGroup` slouží k určení dalších souborů, které mají být zkompilovány do sestavení Razor. |
| `RazorTargetAssemblyAttribute` | Položka prvků, které slouží ke kódu generovat atributy pro sestavení Razor. Například:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Položky elementy přidané jako vložené prostředky do generovaného sestavení Razor. |

::: moniker range=">= aspnetcore-3.0"

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavení vytvořené metodou Razor. |
| `RazorOutputPath` | Výstupní adresář Razor. |
| `RazorCompileToolset` | Slouží k určení sady nástrojů použitý k vytvoření sestavení Razor. Platné hodnoty jsou `Implicit`, `RazorSDK`a `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí hodnota je `true`. Při `true`zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu. Při odkazování prostřednictvím `Microsoft.NET.Sdk.Web`jsou zahrnuty i soubory v souborech *wwwroot* a config. |
| `EnableDefaultRazorGenerateItems` | Při `true`zahrnuje soubory *. cshtml* z `Content` položek v `RazorGenerate` položkách. |
| `GenerateRazorTargetAssemblyInfo` | Při `true`vygeneruje soubor *. cs* obsahující atributy určené `RazorAssemblyAttribute` a zahrne soubor do výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Při `true`přidá do `RazorAssemblyAttribute`výchozí sadu atributů sestavení. |
| `CopyRazorGenerateFilesToPublishDirectory` | Při `true`zkopíruje soubory `RazorGenerate` Items ( *. cshtml*) do adresáře pro publikování. Obvykle nejsou publikované aplikace vyžadovat Razor soubory, pokud se účastní v sestavování v době sestavení nebo publikovat čas. Výchozí hodnota je `false`. |
| `PreserveCompilationReferences` | Při `true`zkopírujte položky referenčního sestavení do adresáře pro publikování. Obvykle nejsou publikované aplikace vyžadovat referenční sestavení, dojde v okamžiku sestavení nebo publikovat čas kompilace Razor. Nastavte na `true`, pokud publikovaná aplikace vyžaduje kompilaci modulu runtime. Nastavte například hodnotu `true`, pokud aplikace upravuje soubory *. cshtml* za běhu nebo používá vložená zobrazení. Výchozí hodnota je `false`. |
| `IncludeRazorContentInPack` | Při `true`jsou všechny položky obsahu Razor (soubory *. cshtml* ) označeny pro zahrnutí do generovaného balíčku NuGet. Výchozí hodnota je `false`. |
| `EmbedRazorGenerateSources` | Při `true`přidá položky RazorGenerate ( *. cshtml*) jako vložené soubory do vygenerovaného sestavení Razor. Výchozí hodnota je `false`. |
| `UseRazorBuildServer` | Při `true`používá trvalý proces sestavení serveru pro přesměrování práce generování kódu. Výchozí hodnota je `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Při `true`generuje sada SDK Další atributy, pomocí kterých MVC za běhu provede zjišťování částí aplikace. |
| `DefaultWebContentItemExcludes` | Vzor expanze názvů pro prvky položky, které mají být vyloučeny ze skupiny položek `Content` v projektech cílících na web nebo sadu Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Při `true`se soubory *. config* a *. JSON* nekopírují do výstupního adresáře sestavení. |
| `AddRazorSupportForMvc` | Když `true`, nakonfiguruje sadu Razor SDK tak, aby přidala podporu pro konfiguraci MVC, která je požadována při sestavování aplikací obsahujících zobrazení MVC nebo Razor Pages. Tato vlastnost je implicitně nastavená pro projekty .NET Core 3,0 nebo novější, které cílí na webovou sadu SDK. |
| `RazorLangVersion` | Verze jazyka Razor, na kterou se má cílit |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavení vytvořené metodou Razor. |
| `RazorOutputPath` | Výstupní adresář Razor. |
| `RazorCompileToolset` | Slouží k určení sady nástrojů použitý k vytvoření sestavení Razor. Platné hodnoty jsou `Implicit`, `RazorSDK`a `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí hodnota je `true`. Při `true`zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu. Při odkazování prostřednictvím `Microsoft.NET.Sdk.Web`jsou zahrnuty i soubory v souborech *wwwroot* a config. |
| `EnableDefaultRazorGenerateItems` | Při `true`zahrnuje soubory *. cshtml* z `Content` položek v `RazorGenerate` položkách. |
| `GenerateRazorTargetAssemblyInfo` | Při `true`vygeneruje soubor *. cs* obsahující atributy určené `RazorAssemblyAttribute` a zahrne soubor do výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Při `true`přidá do `RazorAssemblyAttribute`výchozí sadu atributů sestavení. |
| `CopyRazorGenerateFilesToPublishDirectory` | Při `true`zkopíruje soubory `RazorGenerate` Items ( *. cshtml*) do adresáře pro publikování. Obvykle nejsou publikované aplikace vyžadovat Razor soubory, pokud se účastní v sestavování v době sestavení nebo publikovat čas. Výchozí hodnota je `false`. |
| `CopyRefAssembliesToPublishDirectory` | Při `true`zkopírujte položky referenčního sestavení do adresáře pro publikování. Obvykle nejsou publikované aplikace vyžadovat referenční sestavení, dojde v okamžiku sestavení nebo publikovat čas kompilace Razor. Nastavte na `true`, pokud publikovaná aplikace vyžaduje kompilaci modulu runtime. Nastavte například hodnotu `true`, pokud aplikace upravuje soubory *. cshtml* za běhu nebo používá vložená zobrazení. Výchozí hodnota je `false`. |
| `IncludeRazorContentInPack` | Při `true`jsou všechny položky obsahu Razor (soubory *. cshtml* ) označeny pro zahrnutí do generovaného balíčku NuGet. Výchozí hodnota je `false`. |
| `EmbedRazorGenerateSources` | Při `true`přidá položky RazorGenerate ( *. cshtml*) jako vložené soubory do vygenerovaného sestavení Razor. Výchozí hodnota je `false`. |
| `UseRazorBuildServer` | Při `true`používá trvalý proces sestavení serveru pro přesměrování práce generování kódu. Výchozí hodnota je `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Při `true`generuje sada SDK Další atributy, pomocí kterých MVC za běhu provede zjišťování částí aplikace. |
| `DefaultWebContentItemExcludes` | Vzor expanze názvů pro prvky položky, které mají být vyloučeny ze skupiny položek `Content` v projektech cílících na web nebo sadu Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Při `true`se soubory *. config* a *. JSON* nekopírují do výstupního adresáře sestavení. |
| `AddRazorSupportForMvc` | Když `true`, nakonfiguruje sadu Razor SDK tak, aby přidala podporu pro konfiguraci MVC, která je požadována při sestavování aplikací obsahujících zobrazení MVC nebo Razor Pages. Tato vlastnost je implicitně nastavená pro projekty .NET Core 3,0 nebo novější, které cílí na webovou sadu SDK. |
| `RazorLangVersion` | Verze jazyka Razor, na kterou se má cílit |

::: moniker-end

Další informace o vlastnostech naleznete v tématu [vlastnosti MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Cíle

Sada Razor SDK definuje dva hlavní cíle:

* Kód &ndash; `RazorGenerate` generuje soubory *. cs* z prvků `RazorGenerate` položky. Vlastnost `RazorGenerateDependsOn` použijte k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.
* `RazorCompile` &ndash; kompiluje generované soubory *. cs* v sestavení do sestavení Razor. Pomocí `RazorCompileDependsOn` můžete určit další cíle, které se dají spustit před nebo po tomto cíli.
* Kód &ndash; `RazorComponentGenerate` generuje soubory *. cs* pro prvky `RazorComponent` položky. Vlastnost `RazorComponentGenerateDependsOn` použijte k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.

### <a name="runtime-compilation-of-razor-views"></a>Kompilace modulu runtime zobrazení Razor

* Ve výchozím nastavení nebude sada Razor SDK publikovat referenční sestavení, které jsou nutné k provedení kompilace modulu runtime. Výsledkem je selhání kompilace, když aplikační model spoléhá na kompilaci modulu runtime&mdash;například aplikace používá vložená zobrazení nebo změny zobrazení po publikování aplikace. Nastavte `CopyRefAssembliesToPublishDirectory` `true` pro pokračování v publikování referenčních sestavení.

* V případě webové aplikace se ujistěte, že je vaše aplikace cílena na sadu `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Verze jazyka Razor

Při cílení na sadu `Microsoft.NET.Sdk.Web` SDK je jazyková verze Razor odvozená z verze rozhraní Target Framework aplikace. Pro projekty, které cílí na sadu `Microsoft.NET.Sdk.Razor` SDK, nebo ve výjimečném případě, že aplikace vyžaduje jinou verzi jazyka Razor než odvozenou hodnotu, lze verzi nakonfigurovat nastavením vlastnosti `<RazorLangVersion>` v souboru projektu aplikace:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Jazyková verze Razor je úzce integrovaná s verzí modulu runtime, pro který byl sestaven. Cílení na jazykovou verzi, která není navržena pro modul runtime, není podporována a nejspíš generuje chyby sestavení.

## <a name="additional-resources"></a>Další zdroje

* [Přidání do formátu csproj pro .NET Core](/dotnet/core/tools/csproj)
* [Společné položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
