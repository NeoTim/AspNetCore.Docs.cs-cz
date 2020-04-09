---
title: ASP.NET jádro Holicí strojek SDK
author: Rick-Anderson
description: Zjistěte, jak Razor Pages v ASP.NET Core usnadňuje a ztráží scénáře zaměřené na stránky než při používání MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2284131ce2d45ec6bc01ce38f91e2c951b108605
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80321002"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET jádro Holicí strojek SDK

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Přehled

Obsahuje [!INCLUDE[](~/includes/2.1-SDK.md)] msbuild `Microsoft.NET.Sdk.Razor` sdk (Razor SDK). Holicí strojek SDK:

::: moniker range=">= aspnetcore-3.0"

* Je nutné vytvářet, balit a publikovat projekty obsahující [razor](xref:mvc/views/razor) soubory pro ASP.NET core MVC-založené nebo [Blazor](xref:blazor/index) projekty.
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobit kompilaci souborů Razor (*.cshtml* nebo *.razor).*

Sada Razor `Content` SDK `Include` obsahuje položky `**\*.cshtml` s `**\*.razor` atributy nastavenými na vzory globbing. Jsou publikovány odpovídající soubory.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardizuje prostředí kolem vytváření, balení a publikování projektů obsahujících soubory [Razor](xref:mvc/views/razor) pro ASP.NET projekty založené na Core MVC.
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor.

Sada Razor SDK `Content` obsahuje `Include` položku s `**\*.cshtml` atributem nastaveným na vzor globbingu. Jsou publikovány odpovídající soubory.

::: moniker-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Použití sady Razor SDK

Většina webových aplikací není nutné explicitně odkazovat na holicí strojek SDK.

::: moniker range=">= aspnetcore-3.0"

Chcete-li použít razor SDK k vytváření knihoven tříd obsahujících zobrazení Razor nebo Razor Pages, doporučujeme začít s šablonou projektu knihovny tříd Razor (RCL). RCL, který se používá k vytvoření souborů Blazor (*.razor*) minimálně vyžaduje odkaz na balíček [Microsoft.AspNetCore.Components.](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) RCL, který se používá k vytváření zobrazení Razor nebo stránek *(soubory .cshtml)* minimálně vyžaduje cílení `netcoreapp3.0` nebo novější a má `FrameworkReference` [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ve svém souboru projektu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Použití sady Razor SDK k vytváření knihoven tříd obsahujících zobrazení Razor nebo Razor Pages:

* Místo `Microsoft.NET.Sdk.Razor` `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Obvykle balíček odkaz `Microsoft.AspNetCore.Mvc` na je nutné přijímat další závislosti, které jsou nutné k sestavení a kompilaci Razor Pages a Razor zobrazení. Projekt by měl minimálně přidat odkazy na balíček:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Balíček `Microsoft.AspNetCore.Razor.Design` obsahuje razor kompilace úkoly a cíle pro projekt.

  Předchozí balíčky jsou `Microsoft.AspNetCore.Mvc`zahrnuty v . Následující značky ukazují soubor projektu, který používá razor SDK k vytváření souborů Razor pro aplikaci ASP.NET Core Razor Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` Balíčky `Microsoft.AspNetCore.Mvc.Razor.Extensions` a jsou součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Odkaz na balíček `Microsoft.AspNetCore.App` bez verze však poskytuje metabalíček pro aplikaci, která neobsahuje nejnovější verzi aplikace `Microsoft.AspNetCore.Razor.Design`. Projekty musí odkazovat `Microsoft.AspNetCore.Razor.Design` konzistentní `Microsoft.AspNetCore.Mvc`verzi (nebo ) tak, aby byly zahrnuty nejnovější opravy v době sestavení pro razor. Další informace naleznete v [tomto problému GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Vlastnosti

Následující vlastnosti řídí chování sady SDK razor jako součást sestavení projektu:

* `RazorCompileOnBuild`&ndash; Když `true`zkompiluje a vyzařuje razor sestavení jako součást vytváření projektu. Výchozí hodnota `true`je na .
* `RazorCompileOnPublish`&ndash; Když `true`zkompiluje a vyzařuje Razor sestavení jako součást publikování projektu. Výchozí hodnota `true`je na .

Vlastnosti a položky v následující tabulce slouží ke konfiguraci vstupů a výstupu do sady Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Počínaje ASP.NET Core 3.0, MVC zobrazení nebo Holicí `RazorCompileOnBuild` strojek stránky nejsou obsluhovány ve výchozím nastavení, pokud nebo `RazorCompileOnPublish` MSBuild vlastnosti v souboru projektu jsou zakázány. Aplikace musí přidat explicitní odkaz na balíček [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation,](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) pokud aplikace spoléhá na kompilaci runtime ke zpracování souborů *.cshtml.*

::: moniker-end

| Items | Popis |
| ----- | ----------- |
| `RazorGenerate` | Prvky položky (*.cshtml* soubory), které jsou vstupy generování kódu. |
| `RazorComponent` | Prvky položky (*.razor* soubory), které jsou vstupy generování kódu komponenty Razor. |
| `RazorCompile` | Prvky položky (*.cs* soubory), které jsou vstupy do razor kompilace cíle. Pomocí `ItemGroup` tohoto určit další soubory, které mají být kompilovány do sestavení Razor. |
| `RazorTargetAssemblyAttribute` | Prvky položky používané ke kódu generovat atributy pro sestavení Razor. Příklad:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Prvky položky přidané jako vložené prostředky do generovaného sestavení Razor. |

::: moniker range=">= aspnetcore-3.0"

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavy vyrobené razor. |
| `RazorOutputPath` | Adresář výstupu holicího strojku. |
| `RazorCompileToolset` | Slouží k určení sady nástrojů použité k sestavení sestavy Razor. Platné hodnoty `Implicit` `RazorSDK`jsou `PrecompilationTool`, a . |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí je `true`. When `true`, includes *web.config*, *.json*, a *.cshtml* soubory jako obsah v projektu. Při odkazování `Microsoft.NET.Sdk.Web`přes , soubory pod *wwwroot* a konfigurační soubory jsou také zahrnuty. |
| `EnableDefaultRazorGenerateItems` | When `true`, obsahuje *soubory .cshtml* z `Content` položek v `RazorGenerate` položkách. |
| `GenerateRazorTargetAssemblyInfo` | Když `true`vygeneruje soubor *.cs* obsahující atributy určené a `RazorAssemblyAttribute` obsahuje soubor ve výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Když `true`aplikace přidá výchozí sadu atributů sestavení do aplikace `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Když `true`zkopíruje `RazorGenerate` položky (*.cshtml*) soubory do adresáře publikování. Soubory Razor se obvykle nevyžadují pro publikovanou aplikaci, pokud se účastní kompilace v době sestavení nebo publikování. Výchozí hodnota `false`je na . |
| `PreserveCompilationReferences` | Pokud `true`zkopírujte položky referenčního sestavení do adresáře publikování. Reference sestavení nejsou vyžadovány pro publikovanou aplikaci, pokud razor kompilace dochází v době sestavení nebo publikování. Nastavte `true` na, pokud vaše publikovaná aplikace vyžaduje kompilaci za běhu. Nastavte například hodnotu `true` na pokud aplikace upravuje soubory *.cshtml* za běhu nebo používá vložená zobrazení. Výchozí hodnota `false`je na . |
| `IncludeRazorContentInPack` | Když `true`jsou všechny položky obsahu Razor (*soubory .cshtml)* označeny pro zahrnutí do generovaného balíčku NuGet. Výchozí hodnota `false`je na . |
| `EmbedRazorGenerateSources` | Když `true`přidá položky RazorGenerate (*.cshtml*) jako vložené soubory do generovaného sestavení Razor. Výchozí hodnota `false`je na . |
| `UseRazorBuildServer` | Když `true`, používá proces trvalého sestavení serveru k přepětí generování kódu práce. Výchozí hodnota je `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Když `true`sada SDK generuje další atributy používané mvc za běhu k provedení zjišťování součástí aplikace. |
| `DefaultWebContentItemExcludes` | Vzor globbing u prvků položek, které mají `Content` být vyloučeny ze skupiny položek v projektech zaměřených na web nebo holicí strojek SDK |
| `ExcludeConfigFilesFromBuildOutput` | Soubory `true` *.config* a *JSON* nejsou zkopírovány do výstupního adresáře sestavení. |
| `AddRazorSupportForMvc` | Když `true`nakonfiguruje nástroj Razor SDK tak, aby přidal podporu pro konfiguraci MVC, která je vyžadována při vytváření aplikací obsahujících zobrazení MVC nebo Razor Pages. Tato vlastnost je implicitně nastavena pro projekty .NET Core 3.0 nebo novější, které cílí na webovou sadu SDK. |
| `RazorLangVersion` | Verze jazyka Razor cíl. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavy vyrobené razor. |
| `RazorOutputPath` | Adresář výstupu holicího strojku. |
| `RazorCompileToolset` | Slouží k určení sady nástrojů použité k sestavení sestavy Razor. Platné hodnoty `Implicit` `RazorSDK`jsou `PrecompilationTool`, a . |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí je `true`. When `true`, includes *web.config*, *.json*, a *.cshtml* soubory jako obsah v projektu. Při odkazování `Microsoft.NET.Sdk.Web`přes , soubory pod *wwwroot* a konfigurační soubory jsou také zahrnuty. |
| `EnableDefaultRazorGenerateItems` | When `true`, obsahuje *soubory .cshtml* z `Content` položek v `RazorGenerate` položkách. |
| `GenerateRazorTargetAssemblyInfo` | Když `true`vygeneruje soubor *.cs* obsahující atributy určené a `RazorAssemblyAttribute` obsahuje soubor ve výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Když `true`aplikace přidá výchozí sadu atributů sestavení do aplikace `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Když `true`zkopíruje `RazorGenerate` položky (*.cshtml*) soubory do adresáře publikování. Soubory Razor se obvykle nevyžadují pro publikovanou aplikaci, pokud se účastní kompilace v době sestavení nebo publikování. Výchozí hodnota `false`je na . |
| `CopyRefAssembliesToPublishDirectory` | Pokud `true`zkopírujte položky referenčního sestavení do adresáře publikování. Reference sestavení nejsou vyžadovány pro publikovanou aplikaci, pokud razor kompilace dochází v době sestavení nebo publikování. Nastavte `true` na, pokud vaše publikovaná aplikace vyžaduje kompilaci za běhu. Nastavte například hodnotu `true` na pokud aplikace upravuje soubory *.cshtml* za běhu nebo používá vložená zobrazení. Výchozí hodnota `false`je na . |
| `IncludeRazorContentInPack` | Když `true`jsou všechny položky obsahu Razor (*soubory .cshtml)* označeny pro zahrnutí do generovaného balíčku NuGet. Výchozí hodnota `false`je na . |
| `EmbedRazorGenerateSources` | Když `true`přidá položky RazorGenerate (*.cshtml*) jako vložené soubory do generovaného sestavení Razor. Výchozí hodnota `false`je na . |
| `UseRazorBuildServer` | Když `true`, používá proces trvalého sestavení serveru k přepětí generování kódu práce. Výchozí hodnota je `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Když `true`sada SDK generuje další atributy používané mvc za běhu k provedení zjišťování součástí aplikace. |
| `DefaultWebContentItemExcludes` | Vzor globbing u prvků položek, které mají `Content` být vyloučeny ze skupiny položek v projektech zaměřených na web nebo holicí strojek SDK |
| `ExcludeConfigFilesFromBuildOutput` | Soubory `true` *.config* a *JSON* nejsou zkopírovány do výstupního adresáře sestavení. |
| `AddRazorSupportForMvc` | Když `true`nakonfiguruje nástroj Razor SDK tak, aby přidal podporu pro konfiguraci MVC, která je vyžadována při vytváření aplikací obsahujících zobrazení MVC nebo Razor Pages. Tato vlastnost je implicitně nastavena pro projekty .NET Core 3.0 nebo novější, které cílí na webovou sadu SDK. |
| `RazorLangVersion` | Verze jazyka Razor cíl. |

::: moniker-end

Další informace o vlastnostech naleznete v tématu [MSBuild properties](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Cíle

Sada Razor SDK definuje dva primární cíle:

* `RazorGenerate`&ndash; Kód generuje *soubory .cs* z `RazorGenerate` prvků položek. Pomocí `RazorGenerateDependsOn` vlastnosti můžete zadat další cíle, které lze spustit před nebo za tento cíl.
* `RazorCompile`&ndash; Kompiluje generované soubory *.cs* do sestavení Razor. Pomocí `RazorCompileDependsOn` můžete zadat další cíle, které lze spustit před nebo za tento cíl.
* `RazorComponentGenerate`&ndash; Kód generuje *soubory .cs* pro `RazorComponent` prvky položek. Pomocí `RazorComponentGenerateDependsOn` vlastnosti můžete zadat další cíle, které lze spustit před nebo za tento cíl.

### <a name="runtime-compilation-of-razor-views"></a>Runtime kompilace zobrazení Razor

* Ve výchozím nastavení sada Razor SDK nepublikuje referenční sestavení, která jsou nutná k provedení kompilace modulu runtime. To má za následek selhání kompilace, když&mdash;model aplikace závisí na kompilaci runtime, například aplikace používá vložená zobrazení nebo změní zobrazení po publikování aplikace. Nastavte `CopyRefAssembliesToPublishDirectory` `true` na pokračování publikování referenčních sestavení.

* U webové aplikace se ujistěte, `Microsoft.NET.Sdk.Web` že vaše aplikace cílí na sdk.

## <a name="razor-language-version"></a>Jazyková verze razor

Při cílení `Microsoft.NET.Sdk.Web` na SDK, jazyková verze Razor je odvozen z cílové verze rozhraní aplikace. U projektů `Microsoft.NET.Sdk.Razor` zaměřených na sadu SDK nebo ve výjimečných případech, kdy aplikace vyžaduje jinou jazykovou verzi Razor než odvozenou hodnotu, lze nakonfigurovat `<RazorLangVersion>` verzi nastavením vlastnosti v souboru projektu aplikace:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Jazyková verze razoru je úzce integrována s verzí modulu runtime, pro který byla postavena. Cílení na jazykovou verzi, která není určena pro modul runtime, není podporováno a pravděpodobně způsobí chyby sestavení.

## <a name="additional-resources"></a>Další zdroje

* [Dodatky k formátu csproj pro .NET Core](/dotnet/core/tools/csproj)
* [Běžné položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
