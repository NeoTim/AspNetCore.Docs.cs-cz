---
title: Kompilace souborů Razor v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak probíhá kompilace souborů Razor v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994609"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilace souborů Razor v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razor soubory s *příponou .cshtml* jsou kompilovány na obou sestavení a publikovat čas pomocí [Razor SDK](xref:razor-pages/sdk). Kompilace runtime může být volitelně povolena konfigurací aplikace.

## <a name="razor-compilation"></a>Kompilace holicího strojku

Kompilace souborů Razor v době sestavení a publikování je ve výchozím nastavení povolena sadou Razor SDK. Pokud je povolena, kompilace runtime doplňuje kompilaci v době sestavení, což umožňuje aktualizaci souborů Razor, pokud jsou upraveny.

## <a name="runtime-compilation"></a>Runtime kompilace

Povolení kompilace runtime pro všechna prostředí a režimy konfigurace:

1. Nainstalujte balíček [NuGet pro kompilaci Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)

1. Aktualizujte `Startup.ConfigureServices` metodu projektu tak, `AddRazorRuntimeCompilation`aby zahrnovala volání aplikace . Příklad:

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

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

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