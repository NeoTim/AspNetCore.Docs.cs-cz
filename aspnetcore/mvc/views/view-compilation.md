---
title: Kompilace souborů Razor v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak probíhá kompilace souborů Razor v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440932"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilace souborů Razor v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razor soubory s *příponou .cshtml* jsou kompilovány na obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk). Runtime kompilace může být volitelně povolena konfigurací projektu.

## <a name="razor-compilation"></a>Kompilace holicího strojku

Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK. Pokud je povolena, kompilace runtime doplňuje kompilaci v době sestavení, což umožňuje aktualizaci souborů Razor, pokud jsou upraveny.

## <a name="enable-runtime-compilation-at-project-creation"></a>Povolit kompilaci za běhu při vytváření projektu

Šablony projektu Razor Pages a MVC obsahují možnost povolit kompilaci za běhu při vytvoření projektu. Tato možnost je podporována v ASP.NET jádrem 3.1 a novějším.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci:**

1. Vyberte šablonu projektu **Webová aplikace** nebo **Webová aplikace (Model-View-Controller).**
1. Zaškrtněte políčko **Povolit kompilaci modulu runtime razor.**

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Použijte `-rrc` možnost `--razor-runtime-compilation` nebo šablonu. Například následující příkaz vytvoří nový projekt Razor Pages s povolenou kompilací runtime:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Povolení kompilace za běhu v existujícím projektu

Povolení kompilace za běhu pro všechna prostředí v existujícím projektu:

1. Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)
1. Aktualizujte `Startup.ConfigureServices` metodu projektu tak, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby zahrnovala volání aplikace . Příklad:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Podmíněné povolení kompilace za běhu v existujícím projektu

Runtime kompilace může být povolena tak, že je k dispozici pouze pro místní vývoj. Podmíněné povolení tímto způsobem zajišťuje, že publikovaný výstup:

* Používá zkompilovaná zobrazení.
* Neumožňuje sledování souborů v produkčním prostředí.

Povolení kompilace za běhu pouze ve vývojovém prostředí:

1. Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)
1. Upravte oddíl `environmentVariables` profilu spuštění v *souboru launchSettings.json*:
    * Ověřte, zda `ASPNETCORE_ENVIRONMENT` je nastavena na `"Development"`.
    * `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` Nastaveno `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`na .

V následujícím příkladu je ve vývojovém prostředí `IIS Express` pro `RazorPagesApp` profily a spouštěcí profily povolena kompilace runtime:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Ve `Startup` třídě projektu nejsou potřeba žádné změny kódu. Za běhu ASP.NET Core hledá [atribut HostingStartup na](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`úrovni sestavení v aplikaci . Atribut `HostingStartup` určuje spouštěcí kód aplikace, který má být spuštěn. Tento spouštěcí kód umožňuje kompilaci runtime.

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a>Povolení kompilace za běhu pro knihovnu tříd Razor

Zvažte scénář, ve kterém projekt Razor Pages odkazuje na [knihovnu třídy Razor (RCL)](xref:razor-pages/ui-class) s názvem *MyClassLib*. RCL obsahuje soubor *_Layout.cshtml,* který všechny projekty MVC a Razor Pages vašeho týmu spotřebovávají. Chcete povolit kompilaci modulu runtime pro soubor *_Layout.cshtml* v tomto rcl. Proveďte následující změny v projektu Razor Pages:

1. Povolit kompilaci runtime s pokyny na [Podmíněně povolit kompilaci runtime v existujícím projektu](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Konfigurace možností kompilace `Startup.ConfigureServices`runtime v aplikaci :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    V předchozím kódu je vytvořena absolutní cesta k *MyClassLib* RCL. Rozhraní [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) se používá k vyhledání adresářů a souborů na této absolutní cestě. Nakonec je `PhysicalFileProvider` instance přidána do kolekce zprostředkovatelů souborů, která umožňuje přístup k souborům *.cshtml* rcl.

## <a name="additional-resources"></a>Další zdroje

* [Vlastnosti RazorCompileOnBuild a RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor soubory s *příponou .cshtml* jsou kompilovány na obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk). Kompilace runtime může být volitelně povolena konfigurací aplikace.

## <a name="razor-compilation"></a>Kompilace holicího strojku

Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK. Pokud je povolena, kompilace runtime doplňuje kompilaci v době sestavení, což umožňuje aktualizaci souborů Razor, pokud jsou upraveny.

## <a name="runtime-compilation"></a>Runtime kompilace

Povolení kompilace runtime pro všechna prostředí a režimy konfigurace:

1. Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)

1. Aktualizujte `Startup.ConfigureServices` metodu projektu tak, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby zahrnovala volání aplikace . Příklad:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Podmíněně povolit kompilaci runtime

Runtime kompilace může být povolena tak, že je k dispozici pouze pro místní vývoj. Podmíněné povolení tímto způsobem zajišťuje, že publikovaný výstup:

* Používá zkompilovaná zobrazení.
* Je menší velikost.
* Neumožňuje sledování souborů v produkčním prostředí.

Povolení kompilace runtime na základě prostředí a režimu konfigurace:

1. Podmíněně odkazovat [na microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) `Configuration` balíček na základě aktivní hodnoty:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Aktualizujte `Startup.ConfigureServices` metodu projektu tak, `AddRazorRuntimeCompilation`aby zahrnovala volání aplikace . Podmíněně `AddRazorRuntimeCompilation` spustit tak, že běží pouze `ASPNETCORE_ENVIRONMENT` v režimu `Development`ladění, pokud je proměnná nastavena na :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* [Vlastnosti RazorCompileOnBuild a RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Podívejte se [na ukázku kompilace runtime na GitHubu](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pro ukázku, která ukazuje, že kompilace runtime funguje napříč projekty.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Soubor Razor je kompilován za běhu, když je vyvolána přidružená stránka razor nebo zobrazení MVC. Razor soubory jsou kompilovány v obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk).

## <a name="razor-compilation"></a>Kompilace holicího strojku

Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK. Úpravy souborů Razor po jejich aktualizaci jsou podporovány v době sestavení. Ve výchozím nastavení jsou v aplikaci nasazeny pouze zkompilované soubory *Views.dll* a žádná sestavení *.cshtml* nebo odkazy potřebné ke kompilaci souborů Razor.

> [!IMPORTANT]
> Nástroj pro předkompilaci byl zastarala a budou odebrány v ASP.NET Core 3.0. Doporučujeme migrovat na [Razor Sdk](xref:razor-pages/sdk).
>
> Sada Razor SDK je účinná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předkompilaci. Například nastavení `MvcRazorCompileOnPublish` vlastnosti souboru *.csproj* na `true` zakázání sady Razor SDK.

## <a name="runtime-compilation"></a>Runtime kompilace

Kompilace v době sestavení je doplněna runtime kompilací souborů Razor. ASP.NET Core MVC překompiluje soubory Razor, když se změní obsah souboru *.cshtml.*

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
