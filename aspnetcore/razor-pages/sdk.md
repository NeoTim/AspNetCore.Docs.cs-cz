---
title: RazorSada ASP.NET Core SDK
author: Rick-Anderson
description: Přečtěte si, jak Razor stránky v ASP.NET Core zjednodušují a produktivnější vytváření kódu na stránce, než pomocí MVC.
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
ms.openlocfilehash: 555965b96b0d4d541c70f8a137d4f098dc06a4d6
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106621"
---
# <a name="aspnet-core-razor-sdk"></a>RazorSada ASP.NET Core SDK

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Přehled

[!INCLUDE[](~/includes/2.1-SDK.md)]Obsahuje `Microsoft.NET.Sdk.Razor` sadu MSBuild SDK ( Razor SDK). RazorSada SDK:

::: moniker range=">= aspnetcore-3.0"

* Je vyžadován pro vytváření, balení a publikování projektů, které obsahují [Razor](xref:mvc/views/razor) soubory pro ASP.NET Core [Blazor](xref:blazor/index) projekty nebo projekty MVC.
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace Razor souborů (*. cshtml* nebo *. Razor*).

RazorSada SDK obsahuje `Content` položky s `Include` atributy nastavenými na `**\*.cshtml` `**\*.razor` vzory pro expanzi názvů a. Jsou publikovány vyhovující soubory.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardizace prostředí kolem vytváření, balení a publikování projektů, které obsahují [Razor](xref:mvc/views/razor) soubory pro ASP.NET Core projekty založené na MVC.
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace Razor souborů.

RazorSada SDK obsahuje `Content` položku s `Include` atributem nastaveným na `**\*.cshtml` vzor expanze. Jsou publikovány vyhovující soubory.

::: moniker-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Použití Razor sady SDK

Většina webových aplikací není pro explicitní odkazování na Razor sadu SDK nutná.

::: moniker range=">= aspnetcore-3.0"

Chcete-li použít Razor sadu SDK k sestavení knihoven tříd, které obsahují Razor zobrazení nebo Razor stránky, doporučujeme začít se Razor šablonou projektu Knihovna tříd (RCL). RCL, který se používá k sestavování Blazor souborů (*. Razor*), vyžaduje minimální odkaz na balíček [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . RCL, který se používá k sestavování Razor zobrazení nebo stránek (soubory *. cshtml* ), vyžaduje minimální cílení `netcoreapp3.0` nebo novější a má `FrameworkReference` v souboru projektu [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Chcete-li použít Razor sadu SDK k sestavení knihoven tříd, které obsahují Razor zobrazení nebo Razor stránky:

* Použít `Microsoft.NET.Sdk.Razor` místo `Microsoft.NET.Sdk` :

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* K `Microsoft.AspNetCore.Mvc` získání dalších závislostí, které jsou požadovány pro sestavení a kompilování Razor stránek a zobrazení, je obvykle vyžadován odkaz na balíček Razor . Projekt by měl mít minimálně přidat odkazy na balíčky:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design`Balíček poskytuje Razor úlohy a cíle kompilace pro projekt.

  Předchozí balíčky jsou součástí nástroje `Microsoft.AspNetCore.Mvc` . Následující kód ukazuje soubor projektu, který používá Razor sadu SDK k sestavení Razor souborů pro Razor aplikaci ASP.NET Core Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design`Balíčky a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app). Reference k balíčku bez verze ale `Microsoft.AspNetCore.App` poskytuje Metapackage aplikaci, která neobsahuje nejnovější verzi nástroje `Microsoft.AspNetCore.Razor.Design` . Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc` ), aby byly zahrnuty nejnovější opravy v době sestavení pro Razor . Další informace najdete v [tomto problému GitHubu](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Vlastnosti

Následující vlastnosti řídí Razor chování sady SDK v rámci sestavení projektu:

* `RazorCompileOnBuild`: Když `true` , zkompiluje a vygeneruje Razor sestavení jako součást sestavení projektu. Výchozí hodnota je `true` .
* `RazorCompileOnPublish`: Když `true` , zkompiluje a vygeneruje Razor sestavení jako součást publikování projektu. Výchozí hodnota je `true` .

Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů do Razor sady SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Počínaje ASP.NET Core 3,0 se zobrazení nebo stránky MVC Razor nezpracovávají ve výchozím nastavení, pokud `RazorCompileOnBuild` `RazorCompileOnPublish` jsou vlastnosti nebo MSBuild v souboru projektu zakázané. Aplikace musí přidat explicitní odkaz na [Microsoft. AspNetCore. Mvc. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) balíček, pokud aplikace spoléhá na kompilaci modulu runtime pro zpracování souborů *. cshtml* .

::: moniker-end

| Items (Položky) | Popis |
| ----- | ----------- |
| `RazorGenerate` | Prvky položky (soubory *. cshtml* ), které jsou vstupy pro generování kódu. |
| `RazorComponent` | Prvky položky (soubory *. Razor* ), které jsou vstupy pro Razor generování kódu komponenty. |
| `RazorCompile` | Prvky položky (soubory *. cs* ), které jsou vstupy pro Razor cíle kompilace. Toto použijte `ItemGroup` k určení dalších souborů, které mají být zkompilovány do Razor sestavení. |
| `RazorTargetAssemblyAttribute` | Prvky položky použité pro kód generují atributy pro Razor sestavení. Například:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Prvky položky přidané jako vložené prostředky do generovaného Razor sestavení. |

::: moniker range=">= aspnetcore-3.0"

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavení vytvořeného Razor . |
| `RazorOutputPath` | RazorVýstupní adresář. |
| `RazorCompileToolset` | Slouží k určení sady nástrojů používané k sestavení Razor sestavení. Platné hodnoty jsou `Implicit` , `RazorSDK` a `PrecompilationTool` . |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí je `true`. `true`V případě zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu. `Microsoft.NET.Sdk.Web`V případě, že jsou odkazovány prostřednictvím, jsou zahrnuty také soubory pod soubory *wwwroot* a config. |
| `EnableDefaultRazorGenerateItems` | When `true` zahrnuje soubory *. cshtml* z `Content` položek v `RazorGenerate` položkách. |
| `GenerateRazorTargetAssemblyInfo` | Když `true` , vygeneruje soubor *. cs* obsahující atributy určené pomocí `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Kdy `true` přidá výchozí sadu atributů sestavení do `RazorAssemblyAttribute` . |
| `CopyRazorGenerateFilesToPublishDirectory` | Při `true` kopírování `RazorGenerate` souborů položek (*. cshtml*) do adresáře pro publikování. RazorPro publikovanou aplikaci se obvykle nevyžadují soubory, pokud se účastní kompilace při sestavení nebo době publikování. Výchozí hodnota je `false` . |
| `PreserveCompilationReferences` | Při `true` kopírování položek referenčního sestavení do adresáře pro publikování. V případě, že je Razor kompilace provedena v době sestavení nebo v době publikování, nejsou obvykle referenční sestavení vyžadována pro publikovanou aplikaci. Nastavte na, `true` Pokud publikovaná aplikace vyžaduje kompilaci za běhu. Například nastavte hodnotu na, `true` Pokud aplikace upraví soubory *. cshtml* za běhu nebo používá vložená zobrazení. Výchozí hodnota je `false` . |
| `IncludeRazorContentInPack` | `true` Razor V případě jsou všechny položky obsahu (soubory *. cshtml* ) označeny pro zahrnutí do generovaného balíčku NuGet. Výchozí hodnota je `false` . |
| `EmbedRazorGenerateSources` | Když `true` přidá položky RazorGenerate (*. cshtml*) jako vložené soubory do generovaného Razor sestavení. Výchozí hodnota je `false` . |
| `UseRazorBuildServer` | `true`V případě používá trvalý proces sestavení serveru pro přesměrování práce generování kódu. Výchozí hodnota je `UseSharedCompilation` . |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Když `true` sada SDK generuje další atributy, pomocí kterých MVC za běhu provede zjišťování částí aplikace. |
| `DefaultWebContentItemExcludes` | Vzor expanze názvů pro prvky položky, které mají být vyloučeny ze `Content` skupiny položek v projektech, které cílí na web nebo Razor sadu SDK |
| `ExcludeConfigFilesFromBuildOutput` | `true`Soubory *. config* a *. JSON* se nekopírují do výstupního adresáře sestavení. |
| `AddRazorSupportForMvc` | Když `true` aplikace nakonfiguruje Razor sadu SDK tak, aby přidala podporu pro konfiguraci MVC, která je požadována při sestavování aplikací obsahujících zobrazení nebo Razor stránky MVC. Tato vlastnost je implicitně nastavená pro projekty .NET Core 3,0 nebo novější, které cílí na webovou sadu SDK. |
| `RazorLangVersion` | Verze Razor jazyka, který se má cílit |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavení vytvořeného Razor . |
| `RazorOutputPath` | RazorVýstupní adresář. |
| `RazorCompileToolset` | Slouží k určení sady nástrojů používané k sestavení Razor sestavení. Platné hodnoty jsou `Implicit` , `RazorSDK` a `PrecompilationTool` . |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí je `true`. `true`V případě zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu. `Microsoft.NET.Sdk.Web`V případě, že jsou odkazovány prostřednictvím, jsou zahrnuty také soubory pod soubory *wwwroot* a config. |
| `EnableDefaultRazorGenerateItems` | When `true` zahrnuje soubory *. cshtml* z `Content` položek v `RazorGenerate` položkách. |
| `GenerateRazorTargetAssemblyInfo` | Když `true` , vygeneruje soubor *. cs* obsahující atributy určené pomocí `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Kdy `true` přidá výchozí sadu atributů sestavení do `RazorAssemblyAttribute` . |
| `CopyRazorGenerateFilesToPublishDirectory` | Při `true` kopírování `RazorGenerate` souborů položek (*. cshtml*) do adresáře pro publikování. RazorPro publikovanou aplikaci se obvykle nevyžadují soubory, pokud se účastní kompilace při sestavení nebo době publikování. Výchozí hodnota je `false` . |
| `CopyRefAssembliesToPublishDirectory` | Při `true` kopírování položek referenčního sestavení do adresáře pro publikování. V případě, že je Razor kompilace provedena v době sestavení nebo v době publikování, nejsou obvykle referenční sestavení vyžadována pro publikovanou aplikaci. Nastavte na, `true` Pokud publikovaná aplikace vyžaduje kompilaci za běhu. Například nastavte hodnotu na, `true` Pokud aplikace upraví soubory *. cshtml* za běhu nebo používá vložená zobrazení. Výchozí hodnota je `false` . |
| `IncludeRazorContentInPack` | `true` Razor V případě jsou všechny položky obsahu (soubory *. cshtml* ) označeny pro zahrnutí do generovaného balíčku NuGet. Výchozí hodnota je `false` . |
| `EmbedRazorGenerateSources` | Když `true` přidá položky RazorGenerate (*. cshtml*) jako vložené soubory do generovaného Razor sestavení. Výchozí hodnota je `false` . |
| `UseRazorBuildServer` | `true`V případě používá trvalý proces sestavení serveru pro přesměrování práce generování kódu. Výchozí hodnota je `UseSharedCompilation` . |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Když `true` sada SDK generuje další atributy, pomocí kterých MVC za běhu provede zjišťování částí aplikace. |
| `DefaultWebContentItemExcludes` | Vzor expanze názvů pro prvky položky, které mají být vyloučeny ze `Content` skupiny položek v projektech, které cílí na web nebo Razor sadu SDK |
| `ExcludeConfigFilesFromBuildOutput` | `true`Soubory *. config* a *. JSON* se nekopírují do výstupního adresáře sestavení. |
| `AddRazorSupportForMvc` | Když `true` aplikace nakonfiguruje Razor sadu SDK tak, aby přidala podporu pro konfiguraci MVC, která je požadována při sestavování aplikací obsahujících zobrazení nebo Razor stránky MVC. Tato vlastnost je implicitně nastavená pro projekty .NET Core 3,0 nebo novější, které cílí na webovou sadu SDK. |
| `RazorLangVersion` | Verze Razor jazyka, který se má cílit |

::: moniker-end

Další informace o vlastnostech naleznete v tématu [vlastnosti MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Cíle

RazorSada SDK definuje dva primární cíle:

* `RazorGenerate`: Kód generuje soubory *. cs* z `RazorGenerate` prvků Item. Vlastnost použijte `RazorGenerateDependsOn` k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.
* `RazorCompile`: Zkompiluje generované soubory *. cs* v Razor sestavení. Použijte `RazorCompileDependsOn` k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.
* `RazorComponentGenerate`: Kód generuje soubory *. cs* pro `RazorComponent` prvky položky. Vlastnost použijte `RazorComponentGenerateDependsOn` k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.

### <a name="runtime-compilation-of-razor-views"></a>Běhová kompilace Razor zobrazení

* Ve výchozím nastavení Razor SDK nepublikuje referenční sestavení, která jsou nutná k provedení kompilace za běhu. Výsledkem je selhání kompilace, když aplikační model spoléhá na kompilaci za běhu &mdash; . aplikace například používá vložená zobrazení nebo změny zobrazení po publikování aplikace. Nastavte `CopyRefAssembliesToPublishDirectory` na `true` pro pokračování v publikování referenčních sestavení.

* V případě webové aplikace zajistěte, aby vaše aplikace byla cílena na `Microsoft.NET.Sdk.Web` sadu SDK.

## <a name="razor-language-version"></a>Razorverze jazyka

Při cílení na `Microsoft.NET.Sdk.Web` sadu SDK Razor je jazyková verze odvozena z verze cílového rozhraní Framework aplikace. Pro projekty `Microsoft.NET.Sdk.Razor` , které cílí na sadu SDK, nebo v případě, že aplikace vyžaduje jinou Razor verzi jazyka než odvozená hodnota, lze verzi nakonfigurovat nastavením `<RazorLangVersion>` vlastnosti v souboru projektu aplikace:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razorverze jazyka je úzce integrovaná s verzí modulu runtime, pro který byl sestaven. Cílení na jazykovou verzi, která není navržena pro modul runtime, není podporována a nejspíš generuje chyby sestavení.

## <a name="additional-resources"></a>Další zdroje

* [Přidání do formátu csproj pro .NET Core](/dotnet/core/tools/csproj)
* [Společné položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
