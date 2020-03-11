---
title: Kompilace souboru Razor v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak probíhá kompilace souborů Razor v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: cd096bba5eb580c0a606699a2bf7c36442fb56f7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661103"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilace souboru Razor v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

Soubor Razor je kompilován za běhu, když je vyvolána přidružená zobrazení MVC. Publikování souboru Razor v čase sestavení není podporováno. Soubory Razor mohou být volitelně kompilovány v době publikování a nasazeny s aplikací&mdash;pomocí nástroje předkompilace.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Soubor Razor se zkompiluje za běhu, když se vyvolá přidružená stránka Razor nebo zobrazení MVC. Publikování souboru Razor v čase sestavení není podporováno. Soubory Razor mohou být volitelně kompilovány v době publikování a nasazeny s aplikací&mdash;pomocí nástroje předkompilace.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Soubor Razor se zkompiluje za běhu, když se vyvolá přidružená stránka Razor nebo zobrazení MVC. Soubory Razor jsou kompilovány jak při sestavování, tak při publikování pomocí [sady Razor SDK](xref:razor-pages/sdk).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Soubory Razor s příponou *. cshtml* jsou kompilovány jak v čase sestavení, tak při publikování pomocí [sady Razor SDK](xref:razor-pages/sdk). Kompilace za běhu může být volitelně povolena konfigurací aplikace.

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
> Sada Razor SDK je platná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předkompilaci. Například nastavením vlastnosti `MvcRazorCompileOnPublish` souboru *. csproj* na `true` zakážete sadu Razor SDK.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Pokud je projekt cílen .NET Framework, nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) :

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

Pokud váš projekt cílí na .NET Core, nemusíte dělat žádné změny.

Šablony projektu ASP.NET Core 2. x implicitně nastavují vlastnost `MvcRazorCompileOnPublish` na `true` ve výchozím nastavení. V důsledku toho lze tento prvek bezpečně odebrat ze souboru *. csproj* .

> [!IMPORTANT]
> Předkompilace nástroje je zastaralá a bude odebrána v ASP.NET Core 3,0. Doporučujeme, abyste provedli migraci na [sadu Razor SDK](xref:razor-pages/sdk).
>
> Předkompilace souboru Razor není k dispozici při provádění [samostatně zahrnutého nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) v ASP.NET Core 2,0.

::: moniker-end

::: moniker range="= aspnetcore-1.1"

Nastavte vlastnost `MvcRazorCompileOnPublish` na `true`a nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) . Následující ukázka *. csproj* zvýrazní tato nastavení:

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Připravte aplikaci pro [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd) pomocí [příkazu .NET Core CLI publikovat](/dotnet/core/tools/dotnet-publish). Například spusťte následující příkaz v kořenu projektu:

```dotnetcli
dotnet publish -c Release
```

*\<project_name >. Soubor PrecompiledViews. dll* obsahující zkompilované soubory Razor je vytvořen po úspěšném dokončení předkompilace. Například následující snímek obrazovky znázorňuje obsah souboru *index. cshtml* v rámci *WebApplication1. PrecompiledViews. dll*:

![Zobrazení Razor v knihovně DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a>Kompilace za běhu

::: moniker range="= aspnetcore-2.1"

Kompilace v době sestavení je doplněna kompilací souborů Razor za běhu. ASP.NET Core MVC bude znovu kompilovat soubory Razor, když se změní obsah souboru *. cshtml* .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kompilace v době sestavení je doplněna kompilací souborů Razor za běhu. <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> Získá nebo nastaví hodnotu, která určuje, zda jsou soubory Razor (zobrazení Razor a Razor Pages) znovu zkompilovány a aktualizovány při změně souborů na disku.

Výchozí hodnota je `true` pro:

* Pokud je verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> nebo dřívější
* Pokud je verze kompatibility aplikace nastavená na <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> nebo novější a aplikace se nachází ve vývojovém prostředí <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>. Jinými slovy, soubory Razor nemusely být znovu zkompilovány v nevývojovém prostředí, pokud není explicitně nastaveno <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange>.

Pokyny a příklady nastavení verze kompatibility aplikace najdete v tématu <xref:mvc/compatibility-version>.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Povolení kompilace za běhu pro všechna prostředí a režimy konfigurace:

1. Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) .

1. Aktualizujte metodu `Startup.ConfigureServices` projektu tak, aby zahrnovala volání `AddRazorRuntimeCompilation`. Příklad:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Podmíněně povolit kompilaci za běhu

Je možné povolit kompilaci za běhu, aby byla dostupná pouze pro místní vývoj. Podmíněné povolení tímto způsobem zajistí, že publikovaný výstup:

* Používá kompilovaná zobrazení.
* Má menší velikost.
* Nepovoluje sledovací procesy souborů v produkčním prostředí.

Povolení kompilace za běhu na základě prostředí a konfiguračního režimu:

1. Podmíněně odkázat na balíček [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) na základě hodnoty Active `Configuration`:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Aktualizujte metodu `Startup.ConfigureServices` projektu tak, aby zahrnovala volání `AddRazorRuntimeCompilation`. Podmíněně vykoná `AddRazorRuntimeCompilation` tak, že se spustí pouze v režimu ladění, pokud je proměnná `ASPNETCORE_ENVIRONMENT` nastavená na `Development`:

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
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
