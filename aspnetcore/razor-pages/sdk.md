---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/25/2018
uid: razor-pages/sdk
ms.openlocfilehash: 2df7dc4234207d3dbac8a4ff47751adc8fc6a192
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284436"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/2.1-SDK.md)] Zahrnuje `Microsoft.NET.Sdk.Razor` sady MSBuild SDK (Razor SDK). Razor SDK:

* Standardizuje prostředí týkající se vytváření, balení a publikování projektů, které obsahují [Razor](xref:mvc/views/razor) souborů pro projekty ASP.NET Core MVC.
* Obsahuje sadu předdefinovaných cílů, vlastností a položek, které umožňují přizpůsobení kompilace souborech Razor.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="using-the-razor-sdk"></a>Pomocí syntaxe Razor SDK

Většina webových aplikací není nutné explicitně odkazují na sadu SDK Razor.

Použití sady SDK Razor k vytvoření knihovny tříd obsahující zobrazení syntaxe Razor nebo stránky Razor:

* Použití `Microsoft.NET.Sdk.Razor` místo `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    ...
  </Project>
  ```

* Obvykle, odkaz na balíček pro `Microsoft.AspNetCore.Mvc` se vyžaduje pro příjem Další závislosti, které jsou potřebné k sestavení a kompilace Razor Pages a zobrazeními Razor. Minimálně byste vašeho projektu přidat odkazy na balíčky do:

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
    
  `Microsoft.AspNetCore.Razor.Design` Balíček poskytuje Razor kompilace úlohy a cíle projektu.

  Předchozí balíčky jsou součástí `Microsoft.AspNetCore.Mvc`. Následující kód ukazuje soubor projektu, který používá sada Razor SDK k sestavení souborů Razor pro aplikace ASP.NET Core Razor Pages:
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` a `Microsoft.AspNetCore.Mvc.Razor.Extensions` jsou součástí balíčků [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app). Ale bez verze `Microsoft.AspNetCore.App` odkaz na balíček poskytuje Microsoft.aspnetcore.all aplikaci, která neobsahuje nejnovější verzi `Microsoft.AspNetCore.Razor.Design`. Projekty musí odkazovat na konzistentní verzi `Microsoft.AspNetCore.Razor.Design` (nebo `Microsoft.AspNetCore.Mvc`) tak, aby byly zahrnuty nejnovější opravy čas sestavení pro syntaxi Razor. Další informace najdete v tématu [tento problém Githubu](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Vlastnosti

Následující vlastnosti řídit chování sady SDK pro syntaxi Razor jako součást sestavení projektu:

* `RazorCompileOnBuild` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást sestavení projektu. Výchozí hodnota je `true`.
* `RazorCompileOnPublish` &ndash; Když `true`, zkompiluje a vysílá Razor sestavení jako součást publikování projektu. Výchozí hodnota je `true`.

Vlastnosti a položky v následující tabulce se používají ke konfiguraci vstupů a výstupů Razor SDK.

| Položky | Popis |
| ----- | ----------- |
| `RazorGenerate` | Položka elementy (*.cshtml* soubory), které jsou vstupy do cíle generování kódu. |
| `RazorCompile` | Položka elementy (*.cs* soubory), které jsou vstupy do cíle kompilace Razor. Pomocí této ItemGroup můžete určit další soubory se zkompiluje do sestavení Razor. |
| `RazorTargetAssemblyAttribute` | Položka prvků, které slouží ke kódu generovat atributy pro sestavení Razor. Příklad:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Položky elementy přidané jako vložené prostředky do generovaného sestavení Razor. |

| Vlastnost | Popis |
| -------- | ----------- |
| `RazorTargetName` | Název souboru (bez přípony) sestavení vytvořené metodou Razor. | 
| `RazorOutputPath` | Výstupní adresář Razor. |
| `RazorCompileToolset` | Slouží k určení sady nástrojů použitý k vytvoření sestavení Razor. Platné hodnoty jsou `Implicit`, `RazorSDK`, a `PrecompilationTool`. |
| `EnableDefaultContentItems` | Když `true`, obsahuje určité typy souborů, jako například *.cshtml* soubory jako obsah v projektu. Pokud je k dispozici prostřednictvím `Microsoft.NET.Sdk.Web`, soubory pod *wwwroot* a konfigurační soubory jsou zahrnuté také. |
| `EnableDefaultRazorGenerateItems` | Když `true`, zahrnuje *.cshtml* souborů z `Content` položky v `RazorGenerate` položky. |
| `GenerateRazorTargetAssemblyInfo` | Když `true`, generuje *.cs* soubor, který obsahuje atributy určené `RazorAssemblyAttribute` a obsahuje soubor ve výstupu kompilace. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Když `true`, přidá výchozí sadu atributů sestavení, které mají `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Když `true`, kopie `RazorGenerate` položky (*.cshtml*) soubory do adresáře publikovat. Obvykle nejsou publikované aplikace vyžadovat Razor soubory, pokud se účastní v sestavování v době sestavení nebo publikovat čas. Výchozí hodnota je `false`. |
| `CopyRefAssembliesToPublishDirectory` | Když `true`, zkopírovat odkaz na sestavení položky do adresáře publikovat. Obvykle nejsou publikované aplikace vyžadovat referenční sestavení, dojde v okamžiku sestavení nebo publikovat čas kompilace Razor. Nastavte na `true` Pokud publikované aplikace vyžaduje kompilace modulu runtime. Například nastavte hodnotu na `true` Pokud aplikace změní *.cshtml* soubory za běhu nebo používá vložený zobrazení. Výchozí hodnota je `false`. |
| `IncludeRazorContentInPack` | Když `true`, všechny položky obsahu Razor (*.cshtml* soubory) jsou označeny k zahrnutí vygenerovaný balíček NuGet. Výchozí hodnota je `false`. |
| `EmbedRazorGenerateSources` | Když `true`, přidá RazorGenerate (*.cshtml*) položky jako vložené soubory do generovaného sestavení Razor. Výchozí hodnota je `false`. |
| `UseRazorBuildServer` | Když `true`, využívá proces serveru trvalé sestavení přesměrovat pracovní generování kódu. Výchozí hodnota je hodnota `UseSharedCompilation`. |

### <a name="targets"></a>Cíle

Sada Razor SDK definuje dva hlavní cíle:

* `RazorGenerate` &ndash; Generuje kód *.cs* souborů z `RazorGenerate` položky elementy. Použití `RazorGenerateDependsOn` vlastnosti a určit další cílí, který můžete spustit před nebo po tento cíl.
* `RazorCompile` &ndash; Zkompiluje generované *.cs* soubory v sestavení Razor. Použití `RazorCompileDependsOn` zadat další cíle, které můžete spustit před nebo po tento cíl.

### <a name="runtime-compilation-of-razor-views"></a>Kompilace modulu runtime zobrazení Razor

* Ve výchozím nastavení nebude sada Razor SDK publikovat referenční sestavení, které jsou nutné k provedení kompilace modulu runtime. Výsledkem je selhání kompilace při aplikační model využívá kompilace modulu runtime&mdash;například aplikace používá vložený zobrazení nebo změny zobrazení po publikování aplikace. Nastavte `CopyRefAssembliesToPublishDirectory` k `true` můžete pokračovat v publikování referenční sestavení.

* Pro webovou aplikaci, ujistěte se vaše aplikace cílí `Microsoft.NET.Sdk.Web` SDK.
