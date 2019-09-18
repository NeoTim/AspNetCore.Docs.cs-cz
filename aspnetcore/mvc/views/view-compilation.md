---
title: Kompilace souboru Razor v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak probíhá kompilace souborů Razor v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/20/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 0aa632bce32ef44f65d92639284c64c1d00e952e
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080816"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilace souboru Razor v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

Soubor Razor je kompilován za běhu, když je vyvolána přidružená zobrazení MVC. Publikování souboru Razor v čase sestavení není podporováno. Soubory Razor mohou být volitelně kompilovány v době publikování a nasazeny s&mdash;aplikací pomocí nástroje předkompilace.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Soubor Razor se zkompiluje za běhu, když se vyvolá přidružená stránka Razor nebo zobrazení MVC. Publikování souboru Razor v čase sestavení není podporováno. Soubory Razor mohou být volitelně kompilovány v době publikování a nasazeny s&mdash;aplikací pomocí nástroje předkompilace.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Soubor Razor se zkompiluje za běhu, když se vyvolá přidružená stránka Razor nebo zobrazení MVC. Soubory Razor jsou kompilovány jak při sestavování, tak při publikování pomocí [sady Razor SDK](xref:razor-pages/sdk).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Soubory Razor jsou kompilovány jak při sestavování, tak při publikování pomocí [sady Razor SDK](xref:razor-pages/sdk). Kompilace za běhu může být volitelně povolena konfigurací aplikace.

::: moniker-end

## <a name="razor-compilation"></a>Kompilace Razor

::: moniker range=">= aspnetcore-3.0"
Kompilace souborů Razor založená na sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK. Pokud je povoleno, kompilace za běhu doplňuje kompilaci času sestavení a umožňuje aktualizaci souborů Razor, pokud jsou upravovány.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Kompilace souborů Razor založená na sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK. Úpravy souborů Razor po jejich aktualizaci jsou podporovány v době sestavení. Ve výchozím nastavení se do vaší aplikace nasazují pouze kompilovaná *zobrazení. dll* a žádné soubory *. cshtml* nebo odkazy na sestavení, která jsou nutná pro zkompilování souborů Razor.

> [!IMPORTANT]
> Předkompilace nástroje je zastaralá a bude odebrána v ASP.NET Core 3,0. Doporučujeme, abyste provedli migraci na [sadu Razor SDK](xref:razor-pages/sdk).
>
> Sada Razor SDK je platná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předkompilaci. Například nastavením `MvcRazorCompileOnPublish` vlastnosti `true` souboru *. csproj* zakážete sadu Razor SDK.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Pokud je projekt cílen .NET Framework, nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) :

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

Pokud váš projekt cílí na .NET Core, nemusíte dělat žádné změny.

Šablony projektu ASP.NET Core 2. x implicitně nastavují `MvcRazorCompileOnPublish` vlastnost na `true` hodnotu standardně. V důsledku toho lze tento prvek bezpečně odebrat ze souboru *. csproj* .

> [!IMPORTANT]
> Předkompilace nástroje je zastaralá a bude odebrána v ASP.NET Core 3,0. Doporučujeme, abyste provedli migraci na [sadu Razor SDK](xref:razor-pages/sdk).
>
> Předkompilace souboru Razor není k dispozici při provádění [samostatně zahrnutého nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) v ASP.NET Core 2,0.

::: moniker-end

::: moniker range="= aspnetcore-1.1"

Nastavte vlastnost na `true`a nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. ViewCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) `MvcRazorCompileOnPublish` Následující ukázka *. csproj* zvýrazní tato nastavení:

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Připravte aplikaci pro [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd) pomocí [příkazu .NET Core CLI publikovat](/dotnet/core/tools/dotnet-publish). Například spusťte následující příkaz v kořenu projektu:

```dotnetcli
dotnet publish -c Release
```

PROJECT_NAME  *>\<. Soubor PrecompiledViews. dll* obsahující zkompilované soubory Razor je vytvořen po úspěšném dokončení předkompilace. Například následující snímek obrazovky znázorňuje obsah souboru *index. cshtml* v rámci *WebApplication1. PrecompiledViews. dll*:

![Zobrazení Razor v knihovně DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a>Kompilace za běhu

::: moniker range="= aspnetcore-2.1"

Kompilace v době sestavení je doplněna kompilací souborů Razor za běhu. ASP.NET Core MVC bude znovu kompilovat soubory Razor, když se změní obsah souboru *. cshtml* .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kompilace v době sestavení je doplněna kompilací souborů Razor za běhu. <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> Získávánebonastavujehodnotu,kteráurčuje,jestlisesouboryRazor(zobrazeníRazoraRazorPages)majíznovuzkompilovataaktualizovat,pokudse<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> soubory na disku mění.

Výchozí hodnota je `true` :

* Pokud je verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> nebo starší
* Pokud je verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> nebo novější a aplikace se nachází ve vývojovém prostředí. <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*> Jinými slovy, soubory Razor nebudou znovu zkompilovány v nevývojovém prostředí, pokud <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> není explicitně nastaveno.

Pokyny a příklady nastavení verze kompatibility aplikace najdete v tématu <xref:mvc/compatibility-version>.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Kompilace modulu runtime je povolena pomocí `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` balíčku. Aby bylo možné povolit kompilaci za běhu, aplikace musí:

* Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) .
* Aktualizujte `Startup.ConfigureServices` metodu projektu tak, aby zahrnovala `AddRazorRuntimeCompilation`volání:

  ```csharp
  services
      .AddControllersWithViews()
      .AddRazorRuntimeCompilation();
  ```

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
