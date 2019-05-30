---
title: Kompilace souboru Razor v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak kompilace souborech Razor vyvolá se v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/30/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: b78831dd95a721e35d4bf5b44cdd8823472f6aa2
ms.sourcegitcommit: 06c4f2910dd54ded25e1b8750e09c66578748bc9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66395893"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilace souboru Razor v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

Soubor Razor je zkompilován za běhu, když uživatel vyvolá přidružené zobrazení MVC. Publikování souborů Razor čas sestavení není podporováno. Volitelně mohou být zkompilovány soubory Razor na čas publikování a nasazen s aplikací&mdash;nástrojem pro předkompilaci.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Souboru Razor je zkompilován za běhu, když uživatel vyvolá přidružené zobrazení stránky Razor nebo MVC. Publikování souborů Razor čas sestavení není podporováno. Volitelně mohou být zkompilovány soubory Razor na čas publikování a nasazen s aplikací&mdash;nástrojem pro předkompilaci.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Souboru Razor je zkompilován za běhu, když uživatel vyvolá přidružené zobrazení stránky Razor nebo MVC. Razor soubory kompilované za obě sestavení a publikování pomocí [Razor SDK](xref:razor-pages/sdk).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Razor soubory kompilované za obě sestavení a publikování pomocí [Razor SDK](xref:razor-pages/sdk). Kompilace modulu runtime může volitelně stará konfigurace vaší aplikace.

::: moniker-end

## <a name="razor-compilation"></a>Kompilace Razor

::: moniker range=">= aspnetcore-3.0"
Kompilace sestavení a publikování běhu Razor souborů je povolené ve výchozím nastavení sada Razor SDK. Pokud povolená, doplňuje kompilace modulu runtime sestavení kompilace, povolení souborech Razor aktualizovat, pokud je upravovat.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Kompilace sestavení a publikování běhu Razor souborů je povolené ve výchozím nastavení sada Razor SDK. Úprava souborů Razor, až se aktualizují je podporována v okamžiku sestavení. Ve výchozím nastavení pouze kompilované *Views.dll* a ne *.cshtml* soubory nebo odkazy na sestavení požadovaných pro kompilaci Razor soubory jsou nasazeny s vaší aplikací.

> [!IMPORTANT]
> Předkompilaci je zastaralá a v ASP.NET Core 3.0 se odebere. Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).
>
> Sada Razor SDK nabídka platí jenom v případě, že žádné vlastnosti specifické pro předkompilaci jsou nastaveny v souboru projektu. Například nastavení *.csproj* souboru `MvcRazorCompileOnPublish` vlastnost `true` zakáže Razor SDK.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Pokud váš projekt cílí na .NET Framework, nainstalujte [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) balíček NuGet:

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

Pokud váš projekt cílí na .NET Core, nejsou nutné žádné změny.

Šablony projektů ASP.NET Core 2.x implicitně nastavena `MvcRazorCompileOnPublish` vlastnost `true` ve výchozím nastavení. V důsledku toho tento prvek můžete ho bezpečně odebrat z *.csproj* souboru.

> [!IMPORTANT]
> Předkompilaci je zastaralá a v ASP.NET Core 3.0 se odebere. Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).
>
> Předkompilace Razor soubor není k dispozici při provádění [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) v ASP.NET Core 2.0.

::: moniker-end

::: moniker range="= aspnetcore-1.1"

Nastavte `MvcRazorCompileOnPublish` vlastnost `true`a nainstalujte [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) balíček NuGet. Následující *.csproj* ukázka zvýrazní tato nastavení:

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Příprava aplikace pro [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd) s [příkazu rozhraní příkazového řádku .NET Core pro publikování](/dotnet/core/tools/dotnet-publish). Můžete třeba spustíte následující příkaz v kořenovém adresáři projektu:

```console
dotnet publish -c Release
```

A  *\<project_name >. PrecompiledViews.dll* soubor obsahující kompilovaných souborech Razor, je vytvořen při předkompilaci proběhne úspěšně. Například následující snímek obrazovky znázorňuje obsah *Index.cshtml* v rámci *WebApplication1.PrecompiledViews.dll*:

![Zobrazení syntaxe Razor uvnitř knihovny DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a>Kompilace modulu runtime

::: moniker range="= aspnetcore-2.1"

Kompilace sestavení doplňují kompilace modulu runtime souborů Razor. ASP.NET Core MVC Razor zkompiluje soubory při obsah *.cshtml* změna souboru.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kompilace sestavení doplňují kompilace modulu runtime souborů Razor. <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> Získává nebo nastavuje hodnotu, která určuje, zda jsou soubory Razor (zobrazení syntaxe Razor a Razor Pages) znovu zkompilovat a pokud soubory na disku.

Výchozí hodnota je `true` pro:

* Pokud je verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> nebo dřívější
* Pokud je verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> nebo novější a je aplikace ve vývojovém prostředí <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>. Jinými slovy, souborech Razor nebude znovu zkompilovat v mimo vývojové prostředí není-li <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> explicitně nastavena.

Pokyny a příklady nastavení verze kompatibility aplikace najdete v tématu <xref:mvc/compatibility-version>.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Kompilace modulu runtime se aktivuje pomocí `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` balíčku. Pokud chcete povolit kompilace modulu runtime, musí aplikace:

* Nainstalujte [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) balíček NuGet.
* Aktualizace aplikace `ConfigureServices` zahrnout volání `AddMvcRazorRuntimeCompilation`:

  ```csharp
  services
      .AddMvc()
      .AddRazorRuntimeCompilation()
  ```

Pro kompilaci modulu runtime pro práci při nasazení, musí aplikace upravit jejich soubory projektu, chcete-li nastavit `PreserveCompilationReferences` k `true`:

[!code-xml[](view-compilation/sample/RuntimeCompilation.csproj?highlight=4)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
