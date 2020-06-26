---
title: Razorkompilace souborů v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak Razor probíhá kompilace souborů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: 71487ff2d5d7d7cf96835778f386e5f30fa32254
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405442"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Razorkompilace souborů v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razorsoubory s příponou *. cshtml* jsou kompilovány jak při sestavování, tak při publikování pomocí [ Razor sady SDK](xref:razor-pages/sdk). Kompilace za běhu může být volitelně povolena konfigurací projektu.

## <a name="razor-compilation"></a>Razorkompilace

RazorSada SDK ve výchozím nastavení povoluje kompilaci souborů pro čas sestavení a publikování Razor . Pokud je povoleno, kompilace za běhu doplňuje kompilaci při sestavení a umožňuje Razor aktualizovat soubory, pokud jsou upravovány.

## <a name="enable-runtime-compilation-at-project-creation"></a>Povolit kompilaci za běhu při vytváření projektu

RazorŠablony projektů stránky a MVC obsahují možnost Povolit kompilaci za běhu při vytvoření projektu. Tato možnost je podporovaná v ASP.NET Core 3,1 a novějších.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V dialogovém okně **vytvořit novou ASP.NET Core webovou aplikaci** :

1. Vyberte šablonu projektu webová **aplikace** nebo **Webová aplikace (model-zobrazení-kontroler)** .
1. Zaškrtněte políčko **Povolit Razor kompilaci za běhu** .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Použijte `-rrc` `--razor-runtime-compilation` možnost šablony nebo. Například následující příkaz vytvoří nový Razor projekt stránky s povolenou kompilací Runtime:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Povolit kompilaci za běhu v existujícím projektu

Povolení kompilace za běhu pro všechna prostředí v existujícím projektu:

1. Nainstalujte [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Balíček NuGet RuntimeCompilation
1. Aktualizujte metodu projektu `Startup.ConfigureServices` tak, aby zahrnovala volání <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Například:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Podmíněně povolit kompilaci za běhu v existujícím projektu

Je možné povolit kompilaci za běhu, aby byla dostupná pouze pro místní vývoj. Podmíněné povolení tímto způsobem zajistí, že publikovaný výstup:

* Používá kompilovaná zobrazení.
* Nepovoluje sledovací procesy souborů v produkčním prostředí.

Chcete-li povolit kompilaci za běhu pouze ve vývojovém prostředí:

1. Nainstalujte [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Balíček NuGet RuntimeCompilation
1. Upravte část spouštěcí profil `environmentVariables` v *launchSettings.jsdne*:
    * Ověřte `ASPNETCORE_ENVIRONMENT` , že je nastavené na `"Development"` .
    * Nastavte `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` na `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` .

V následujícím příkladu je kompilace modulu runtime ve vývojovém prostředí povolena pro `IIS Express` `RazorPagesApp` profily spuštění a:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Ve třídě projektu nejsou potřeba žádné změny kódu `Startup` . V době běhu ASP.NET Core vyhledá [atribut HostingStartup na úrovni sestavení](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) v `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` . `HostingStartup`Atribut určuje spouštěcí kód aplikace, který se má spustit. Tento spouštěcí kód umožňuje kompilaci za běhu.

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a>Povolit kompilaci za běhu pro Razor knihovnu tříd

Vezměte v úvahu scénář, ve kterém se Razor projekt stránky odkazuje na [ Razor knihovnu tříd (RCL)](xref:razor-pages/ui-class) s názvem *MyClassLib*. RCL obsahuje soubor *_Layout. cshtml* , který využívá všechny projekty MVC a Razor Pages vašeho týmu. Chcete povolit kompilaci za běhu pro soubor *_Layout. cshtml* v tomto RCL. Proveďte následující změny v Razor projektu stránky:

1. Povolit kompilaci za běhu pomocí instrukcí v [podmíněně povolit kompilaci za běhu v existujícím projektu](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Nakonfigurujte možnosti kompilace za běhu v `Startup.ConfigureServices` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    V předchozím kódu je vytvořena absolutní cesta k *MyClassLib* RCL. [Rozhraní PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) se používá k nalezení adresářů a souborů v této absolutní cestě. Nakonec `PhysicalFileProvider` je instance přidána do kolekce zprostředkovatelů souborů, která umožňuje přístup k souborům *. cshtml* RCL.

## <a name="additional-resources"></a>Další zdroje

* Vlastnosti [RazorCompileOnBuild a RazorCompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razorsoubory s příponou *. cshtml* jsou kompilovány jak při sestavování, tak při publikování pomocí [ Razor sady SDK](xref:razor-pages/sdk). Kompilace za běhu může být volitelně povolena konfigurací aplikace.

## <a name="razor-compilation"></a>Razorkompilace

RazorSada SDK ve výchozím nastavení povoluje kompilaci souborů pro čas sestavení a publikování Razor . Pokud je povoleno, kompilace za běhu doplňuje kompilaci při sestavení a umožňuje Razor aktualizovat soubory, pokud jsou upravovány.

## <a name="runtime-compilation"></a>Kompilace za běhu

Povolení kompilace za běhu pro všechna prostředí a režimy konfigurace:

1. Nainstalujte [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Balíček NuGet RuntimeCompilation

1. Aktualizujte metodu projektu `Startup.ConfigureServices` tak, aby zahrnovala volání <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Například:

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

1. Podmíněně odkázat na [Microsoft. AspNetCore. Mvc. Razor .. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) balíček na základě aktivní `Configuration` hodnoty:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Aktualizujte metodu projektu `Startup.ConfigureServices` tak, aby zahrnovala volání `AddRazorRuntimeCompilation` . Podmíněně `AddRazorRuntimeCompilation` se spustí tak, aby běžela pouze v režimu ladění, pokud `ASPNETCORE_ENVIRONMENT` je proměnná nastavena na `Development` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* Vlastnosti [RazorCompileOnBuild a RazorCompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Ukázku, která ukazuje, jak pracovat s kompilací runtime napříč projekty, najdete v [ukázce kompilace za běhu na GitHubu](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

RazorSoubor je zkompilován za běhu, když Razor je vyvolána přidružená stránka nebo zobrazení MVC. Razorsoubory jsou kompilovány jak při sestavování, tak při publikování pomocí [ Razor sady SDK](xref:razor-pages/sdk).

## <a name="razor-compilation"></a>Razorkompilace

RazorSada SDK ve výchozím nastavení povoluje kompilaci souborů sestavení a publikování Razor . Úpravy Razor souborů po jejich aktualizaci jsou podporovány v době sestavení. Ve výchozím nastavení jsou ve vaší aplikaci nasazeny pouze zkompilované *Views.dll* a žádné soubory *. cshtml* nebo odkazy na sestavení nutná ke kompilaci Razor souborů.

> [!IMPORTANT]
> Předkompilace nástroje je zastaralá a bude odebrána v ASP.NET Core 3,0. Doporučujeme migrovat na [ Razor sadu SDK](xref:razor-pages/sdk).
>
> RazorSada SDK je platná pouze v případě, že v souboru projektu nejsou nastaveny žádné vlastnosti specifické pro předem zkompilování. Například nastavením vlastnosti souboru *. csproj* `MvcRazorCompileOnPublish` `true` zakážete Razor sadu SDK.

## <a name="runtime-compilation"></a>Kompilace za běhu

Kompilace v době sestavení je doplněna kompilací souborů za běhu Razor . ASP.NET Core MVC bude Razor po změně obsahu souboru *. cshtml* znovu kompilovat soubory.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
