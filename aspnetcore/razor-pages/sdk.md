---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Přečtěte si, jak Razor Pages v ASP.NET Core usnadňuje a produktivnější vytváření kódu v různých scénářích než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2fbdf95d02d7918236981c7fee8ebcbedf5c55e1
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963264"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Od [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Přehled

[!INCLUDE[](~/includes/2.1-SDK.md)] obsahuje sadu `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK). Sada Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* Je vyžadován k sestavení, sbalení a publikování projektů obsahujících soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projekty založené na MVC nebo [Blazor](xref:blazor/index) .
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor ( *. cshtml* nebo *. Razor*).

Sada Razor SDK obsahuje položky `Content` s atributy `Include` nastavenými na vzory expanze názvů `**\*.cshtml` a `**\*.razor`. Jsou publikovány vyhovující soubory.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardizace prostředí kolem vytváření, balení a publikování projektů, které obsahují soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projektů založených na MVC.
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor.

Sada Razor SDK obsahuje položku `Content` s atributem `Include` nastaveným na vzor expanze názvů `**\*.cshtml`. Jsou publikovány vyhovující soubory.

::: moniker-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Použití sady Razor SDK

Pro většinu webových aplikací není nutné explicitně odkazovat na sadu Razor SDK.

::: moniker range=">= aspnetcore-3.0"

Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení nebo Razor Pages Razor, doporučujeme začít se šablonou projektu knihovny tříd Razor (RCL). RCL, který se používá k sestavování souborů Blazor ( *. Razor*), vyžaduje minimální odkaz na balíček [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . RCL, který se používá k vytvoření zobrazení nebo stránek (souborů *. cshtml* ) Razor, vyžaduje minimálně cílení na `netcoreapp3.0` nebo novější a má `FrameworkReference` do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v jeho souboru projektu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení Razor nebo Razor Pages:

* Místo `Microsoft.NET.Sdk` použijte `Microsoft.NET.Sdk.Razor`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* K získání dalších závislostí, které jsou požadovány pro sestavování a kompilování Razor Pages zobrazení a Razor, je obvykle vyžadován odkaz na balíček `Microsoft.AspNetCore.Mvc`. Projekt by měl mít minimálně přidat odkazy na balíčky:

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  Balíček `Microsoft.AspNetCore.Razor.Design` poskytuje úlohy kompilace Razor a cíle pro projekt.

  Předchozí balíčky jsou zahrnuté do `Microsoft.AspNetCore.Mvc`. Následující kód ukazuje soubor projektu, který používá sadu Razor SDK k sestavení souborů Razor pro aplikaci ASP.NET Core Razor Pages:
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Balíčky `Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app). Reference k balíčku `Microsoft.AspNetCore.App` bez verze ale poskytuje Metapackage aplikaci, která neobsahuje nejnovější verzi `Microsoft.AspNetCore.Razor.Design`. Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`), aby byly zahrnuty nejnovější opravy v době sestavení pro Razor. Další informace najdete v [tomto problému GitHubu](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Vlastnosti

Následující vlastnosti řídí chování sady SDK Razor v rámci sestavení projektu:

* `RazorCompileOnBuild` &ndash;, pokud `true` kompiluje a generuje sestavení Razor jako součást sestavení projektu. Výchozí hodnota je `true`.
* `RazorCompileOnPublish` &ndash;, pokud `true`, kompiluje a generuje sestavení Razor jako součást publikování projektu. Výchozí hodnota je `true`.

Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů do sady Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Počínaje ASP.NET Core 3,0 nejsou zobrazení MVC nebo Razor Pages obsluhovány ve výchozím nastavení, pokud jsou vlastnosti `RazorCompileOnBuild` nebo `RazorCompileOnPublish` MSBuild v souboru projektu zakázané. Aplikace musí přidat explicitní odkaz na balíček [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) , pokud aplikace spoléhá na kompilaci za běhu za účelem zpracování souborů *. cshtml* .

::: moniker-end

| Položky | Popis |
| ----- | ----------- |
| `RazorGenerate` | Prvky položky (soubory *. cshtml* ), které jsou vstupy pro generování kódu. |
| `RazorComponent` | Prvky položky (soubory *. Razor* ), které jsou vstupy pro generování kódu komponenty Razor. |
| `RazorCompile` | Prvky položky (soubory *. cs* ), které jsou vstupy pro cíle kompilace Razor. Pomocí tohoto `ItemGroup` určete další soubory, které mají být zkompilovány do sestavení Razor. |
| `RazorTargetAssemblyAttribute` | Prvky položky použité pro kód generují atributy pro sestavení Razor. Příklad:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Prvky položky přidané jako vložené prostředky do vygenerovaného sestavení Razor |

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavení vytvořeného syntaxí Razor | 
| `RazorOutputPath` | Výstupní adresář Razor |
| `RazorCompileToolset` | Slouží k určení sady nástrojů používané k sestavení sestavení Razor. Platné hodnoty jsou `Implicit`, `RazorSDK` a `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí hodnota je `true`. Při `true` zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu. V případě, že jsou odkazovány prostřednictvím `Microsoft.NET.Sdk.Web`, jsou zahrnuty také soubory pod soubory *wwwroot* a config. |
| `EnableDefaultRazorGenerateItems` | Při `true` zahrnuje soubory *. cshtml* z položek `Content` v položkách `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Když `true`, vygeneruje soubor *. cs* obsahující atributy určené `RazorAssemblyAttribute` a zahrne soubor do výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Při `true` přidá výchozí sadu atributů sestavení do `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Při `true` zkopíruje soubory `RazorGenerate` ( *. cshtml*) do adresáře pro publikování. Soubory Razor nejsou typicky nutné pro publikovanou aplikaci, pokud se účastní kompilace při sestavení nebo době publikování. Výchozí hodnota je `false`. |
| `CopyRefAssembliesToPublishDirectory` | Při `true` zkopírujte položky referenčního sestavení do adresáře pro publikování. V případě, že dojde k kompilaci Razor v době sestavení nebo při publikování, se obvykle nevyžadují referenční sestavení pro publikovanou aplikaci. Nastavte na `true`, pokud vaše publikovaná aplikace vyžaduje kompilaci za běhu. Nastavte například hodnotu `true`, pokud aplikace upravuje soubory *. cshtml* za běhu nebo používá vložená zobrazení. Výchozí hodnota je `false`. |
| `IncludeRazorContentInPack` | Při `true` jsou všechny položky obsahu Razor (soubory *. cshtml* ) označeny pro zahrnutí do generovaného balíčku NuGet. Výchozí hodnota je `false`. |
| `EmbedRazorGenerateSources` | Při `true` přidá položky RazorGenerate ( *. cshtml*) jako vložené soubory do vygenerovaného sestavení Razor. Výchozí hodnota je `false`. |
| `UseRazorBuildServer` | Při `true` používá trvalý proces sestavení serveru pro přesměrování práce generování kódu. Výchozí hodnota je `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Při `true` vygeneruje sada SDK Další atributy, které MVC používá za běhu, aby bylo možné provést zjišťování částí aplikace. |

Další informace o vlastnostech naleznete v tématu [vlastnosti MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Cíle

Sada Razor SDK definuje dva primární cíle:

* `RazorGenerate` kód &ndash; generuje soubory *. cs* z prvků položky `RazorGenerate`. Pomocí vlastnosti `RazorGenerateDependsOn` určete další cíle, které lze spustit před nebo po tomto cíli.
* `RazorCompile` &ndash; zkompiluje generované soubory *. cs* do sestavení Razor. Pomocí `RazorCompileDependsOn` můžete určit další cíle, které se dají spustit před nebo po tomto cíli.
* `RazorComponentGenerate` kód &ndash; generuje soubory *. cs* pro prvky položky `RazorComponent`. Pomocí vlastnosti `RazorComponentGenerateDependsOn` určete další cíle, které lze spustit před nebo po tomto cíli.

### <a name="runtime-compilation-of-razor-views"></a>Běhová kompilace zobrazení Razor

* Ve výchozím nastavení sada Razor SDK nepublikuje referenční sestavení, která jsou nutná k provedení kompilace za běhu. Výsledkem je selhání kompilace, když aplikační model spoléhá na kompilaci modulu runtime&mdash;například aplikace používá vložená zobrazení nebo změny zobrazení po publikování aplikace. Pokud chcete pokračovat v publikování referenčních sestavení, nastavte `CopyRefAssembliesToPublishDirectory` na `true`.

* V případě webové aplikace zajistěte, aby vaše aplikace byla cílena na sadu `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Verze jazyka Razor

Při cílení na sadu SDK `Microsoft.NET.Sdk.Web` je jazyková verze Razor odvozená z verze cílového rozhraní Framework aplikace. Pro projekty, které cílí na sadu `Microsoft.NET.Sdk.Razor` SDK nebo ve vzácných případech, kdy aplikace vyžaduje jinou verzi jazyka Razor než odvozenou hodnotu, lze verzi nakonfigurovat nastavením vlastnosti `<RazorLangVersion>` v souboru projektu aplikace:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Jazyková verze Razor je úzce integrovaná s verzí modulu runtime, pro který byl sestaven. Cílení na jazykovou verzi, která není navržena pro modul runtime, není podporována a nejspíš generuje chyby sestavení.

## <a name="additional-resources"></a>Další zdroje

* [Přidání do formátu csproj pro .NET Core](/dotnet/core/tools/csproj)
* [Společné položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
