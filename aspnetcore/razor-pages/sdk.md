---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 872d90662494735dc0e4caa01c46fcdcc2606bc6
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809130"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Přehled

[!INCLUDE[](~/includes/2.1-SDK.md)] Zahrnuje `Microsoft.NET.Sdk.Razor` sady MSBuild SDK (Razor SDK). Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* Je vyžadován k sestavení, sbalení a publikování projektů obsahujících soubory [Razor](xref:mvc/views/razor) pro ASP.NET Core projekty založené na MVC nebo [Blazor](xref:blazor/index) .
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborů Razor ( *. cshtml* nebo *. Razor*).

Sada Razor SDK obsahuje `Content` položky s `Include` atributy nastavené na `**\*.cshtml` a `**\*.razor` vzory expanze. Jsou publikovány vyhovující soubory.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardizuje prostředí týkající se vytváření, balení a publikování projektů, které obsahují [Razor](xref:mvc/views/razor) souborů pro projekty ASP.NET Core MVC.
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborech Razor.

Sada Razor SDK obsahuje `Content` položku s atributem `Include` nastaveným na `**\*.cshtml` vzorek expanze. Jsou publikovány vyhovující soubory.

::: moniker-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Použití sady Razor SDK

Většina webových aplikací není nutné explicitně odkazují na sadu SDK Razor.

::: moniker range=">= aspnetcore-3.0"

Chcete-li použít sadu Razor SDK k sestavení knihoven tříd, které obsahují zobrazení nebo Razor Pages Razor, doporučujeme začít se šablonou projektu knihovny tříd Razor (RCL). RCL, který se používá k sestavování souborů Blazor ( *. Razor*), vyžaduje minimální odkaz na balíček [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . RCL, který se používá k vytvoření zobrazení nebo stránek (souborů *. cshtml* ) Razor, vyžaduje minimálně cílení na `netcoreapp3.0` nebo novější a má `FrameworkReference` do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v jeho souboru projektu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Použití sady SDK Razor k vytvoření knihovny tříd obsahující zobrazení syntaxe Razor nebo stránky Razor:

* Použití `Microsoft.NET.Sdk.Razor` místo `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Obvykle, odkaz na balíček pro `Microsoft.AspNetCore.Mvc` se vyžaduje pro příjem Další závislosti, které jsou potřebné k sestavení a kompilace Razor Pages a zobrazeními Razor. Minimálně byste vašeho projektu přidat odkazy na balíčky do:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design` Balíček poskytuje Razor kompilace úlohy a cíle projektu.

  Předchozí balíčky jsou součástí `Microsoft.AspNetCore.Mvc`. Následující kód ukazuje soubor projektu, který používá sada Razor SDK k sestavení souborů Razor pro aplikace ASP.NET Core Razor Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí balíčků [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app). Ale bez verze `Microsoft.AspNetCore.App` odkaz na balíček poskytuje Microsoft.aspnetcore.all aplikaci, která neobsahuje nejnovější verzi `Microsoft.AspNetCore.Razor.Design`. Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`) tak, aby byly zahrnuty nejnovější opravy čas sestavení pro syntaxi Razor. Další informace najdete v tématu [tento problém Githubu](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Vlastnosti

Následující vlastnosti řídit chování sady SDK pro syntaxi Razor jako součást sestavení projektu:

* `RazorCompileOnBuild` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást sestavení projektu. Výchozí hodnota je `true`.
* `RazorCompileOnPublish` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást publikování projektu. Výchozí hodnota je `true`.

Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Počínaje ASP.NET Core 3,0 se zobrazení MVC nebo Razor Pages nezpracovávají ve výchozím nastavení, pokud jsou vlastnosti `RazorCompileOnBuild` nebo `RazorCompileOnPublish` MSBuild v souboru projektu zakázané. Aplikace musí přidat explicitní odkaz na balíček [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) , pokud aplikace spoléhá na kompilaci za běhu za účelem zpracování souborů *. cshtml* .

::: moniker-end

| Položky | Popis |
| ----- | ----------- |
| `RazorGenerate` | Prvky položky (soubory *. cshtml* ), které jsou vstupy pro generování kódu. |
| `RazorComponent` | Prvky položky (soubory *. Razor* ), které jsou vstupy pro generování kódu komponenty Razor. |
| `RazorCompile` | Prvky položky (soubory *. cs* ), které jsou vstupy pro cíle kompilace Razor. Tento `ItemGroup` slouží k určení dalších souborů, které mají být zkompilovány do sestavení Razor. |
| `RazorTargetAssemblyAttribute` | Položka prvků, které slouží ke kódu generovat atributy pro sestavení Razor. Příklad:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Položky elementy přidané jako vložené prostředky do generovaného sestavení Razor. |

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavení vytvořené metodou Razor. |
| `RazorOutputPath` | Výstupní adresář Razor. |
| `RazorCompileToolset` | Slouží k určení sady nástrojů použitý k vytvoření sestavení Razor. Platné hodnoty jsou `Implicit`, `RazorSDK`, a `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Výchozí hodnota je `true`. Při `true`zahrnuje soubory *Web. config*, *. JSON*a *. cshtml* jako obsah v projektu. Pokud je k dispozici prostřednictvím `Microsoft.NET.Sdk.Web`, soubory pod *wwwroot* a konfigurační soubory jsou zahrnuté také. |
| `EnableDefaultRazorGenerateItems` | Když `true`, zahrnuje *.cshtml* souborů z `Content` položky v `RazorGenerate` položky. |
| `GenerateRazorTargetAssemblyInfo` | Když `true`, generuje *.cs* soubor, který obsahuje atributy určené `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Když `true`, přidá výchozí sadu atributů sestavení, které mají `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Když `true`, kopie `RazorGenerate` položky ( *.cshtml*) soubory do adresáře publikovat. Obvykle nejsou publikované aplikace vyžadovat Razor soubory, pokud se účastní v sestavování v době sestavení nebo publikovat čas. Výchozí hodnota je `false`. |
| `CopyRefAssembliesToPublishDirectory` | Když `true`, zkopírovat odkaz na sestavení položky do adresáře publikovat. Obvykle nejsou publikované aplikace vyžadovat referenční sestavení, dojde v okamžiku sestavení nebo publikovat čas kompilace Razor. Nastavte na `true` Pokud publikované aplikace vyžaduje kompilace modulu runtime. Například nastavte hodnotu na `true` Pokud aplikace změní *.cshtml* soubory za běhu nebo používá vložený zobrazení. Výchozí hodnota je `false`. |
| `IncludeRazorContentInPack` | Když `true`, všechny položky obsahu Razor ( *.cshtml* soubory) jsou označeny k zahrnutí vygenerovaný balíček NuGet. Výchozí hodnota je `false`. |
| `EmbedRazorGenerateSources` | Když `true`, přidá RazorGenerate ( *.cshtml*) položky jako vložené soubory do generovaného sestavení Razor. Výchozí hodnota je `false`. |
| `UseRazorBuildServer` | Když `true`, využívá proces serveru trvalé sestavení přesměrovat pracovní generování kódu. Výchozí hodnota je hodnota `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Při `true`generuje sada SDK Další atributy, pomocí kterých MVC za běhu provede zjišťování částí aplikace. |
| `DefaultWebContentItemExcludes` | Vzor expanze názvů pro prvky položky, které mají být vyloučeny ze skupiny položek `Content` v projektech cílících na web nebo sadu Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Při `true`se soubory *. config* a *. JSON* nekopírují do výstupního adresáře sestavení. |
| `AddRazorSupportForMvc` | Když `true`, nakonfiguruje sadu Razor SDK tak, aby přidala podporu pro konfiguraci MVC, která je požadována při sestavování aplikací obsahujících zobrazení MVC nebo Razor Pages. Tato vlastnost je implicitně nastavená pro projekty .NET Core 3,0 nebo novější, které cílí na webovou sadu SDK. |
| `RazorLangVersion` | Verze jazyka Razor, na kterou se má cílit |

Další informace o vlastnostech naleznete v tématu [vlastnosti MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Cíle

Sada Razor SDK definuje dva hlavní cíle:

* `RazorGenerate` &ndash; Generuje kód *.cs* souborů z `RazorGenerate` položky elementy. Vlastnost `RazorGenerateDependsOn` použijte k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.
* `RazorCompile` &ndash; Zkompiluje generované *.cs* soubory v sestavení Razor. Pomocí `RazorCompileDependsOn` můžete určit další cíle, které se dají spustit před nebo po tomto cíli.
* Kód &ndash; `RazorComponentGenerate` generuje soubory *. cs* pro prvky `RazorComponent` položky. Vlastnost `RazorComponentGenerateDependsOn` použijte k určení dalších cílů, které mohou být spuštěny před nebo po tomto cíli.

### <a name="runtime-compilation-of-razor-views"></a>Kompilace modulu runtime zobrazení Razor

* Ve výchozím nastavení nebude sada Razor SDK publikovat referenční sestavení, které jsou nutné k provedení kompilace modulu runtime. Výsledkem je selhání kompilace při aplikační model využívá kompilace modulu runtime&mdash;například aplikace používá vložený zobrazení nebo změny zobrazení po publikování aplikace. Nastavte `CopyRefAssembliesToPublishDirectory` k `true` můžete pokračovat v publikování referenční sestavení.

* Pro webovou aplikaci, ujistěte se vaše aplikace cílí `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Verze jazyka Razor

Při cílení na sadu `Microsoft.NET.Sdk.Web` SDK je jazyková verze Razor odvozená z verze rozhraní Target Framework aplikace. Pro projekty, které cílí na sadu `Microsoft.NET.Sdk.Razor` SDK, nebo ve výjimečném případě, že aplikace vyžaduje jinou verzi jazyka Razor než odvozenou hodnotu, lze verzi nakonfigurovat nastavením vlastnosti `<RazorLangVersion>` v souboru projektu aplikace:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Jazyková verze Razor je úzce integrovaná s verzí modulu runtime, pro který byl sestaven. Cílení na jazykovou verzi, která není navržena pro modul runtime, není podporována a nejspíš generuje chyby sestavení.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Přidání do formátu csproj pro .NET Core](/dotnet/core/tools/csproj)
* [Společné položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
