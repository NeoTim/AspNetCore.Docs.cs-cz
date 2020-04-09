---
title: Migrace z ASP.NET jádra 1.x na 2.0
author: scottaddie
description: Tento článek popisuje požadavky a nejběžnější kroky pro migraci ASP.NET projektu Core 1.x do ASP.NET Core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/1x-to-2x/index
ms.openlocfilehash: c46f50a418cf630980ac2ba94407e4370d36e7d5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667613"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>Migrace z ASP.NET jádra 1.x na 2.0

Podle [Scott Addie](https://github.com/scottaddie)

V tomto článku vás provedeme aktualizací existujícího projektu ASP.NET Core 1.x, který ASP.NET Core 2.0. Migrace aplikace do ASP.NET jádrem 2.0 umožňuje využívat [mnoho nových funkcí a vylepšení výkonu](xref:aspnetcore-2.0).

Existující aplikace ASP.NET Core 1.x jsou založeny mimo šablony projektů specifické pro verzi. Jak se vyvíjí ASP.NET core framework, tak se šablony projektu a počáteční kód obsažený v nich. Kromě aktualizace ASP.NET core framework, je třeba aktualizovat kód pro vaši aplikaci.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Požadavky

Viz [Začínáme s ASP.NET jádrem](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Aktualizovat zástupný název cílového rámce (TFM)

Projekty zaměřené na rozhraní .NET Core by měly používat [TFM](/dotnet/standard/frameworks) verze větší nebo rovna .NET Core 2.0. Vyhledejte `<TargetFramework>` uzel v souboru *.csproj* a nahraďte jeho vnitřní text `netcoreapp2.0`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

Projekty zaměřené na rozhraní .NET Framework by měly používat TFM verze větší nebo rovna rozhraní .NET Framework 4.6.1. Vyhledejte `<TargetFramework>` uzel v souboru *.csproj* a nahraďte jeho vnitřní text `net461`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET Core 2.0 nabízí mnohem větší plochu než .NET Core 1.x. Pokud cílíte na rozhraní .NET Framework výhradně z důvodu chybějících rozhraní API v rozhraní .NET Core 1.x, cílení na rozhraní .NET Core 2.0 bude pravděpodobně fungovat.

Pokud soubor projektu `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`obsahuje , podívejte se na [tento problém GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Aktualizace verze sady .NET Core SDK v souboru global.json

Pokud vaše řešení spoléhá na soubor [global.json,](/dotnet/core/tools/global-json) aby se zaměřilo na `version` konkrétní verzi sady .NET Core SDK, aktualizujte jeho vlastnost tak, aby používala verzi 2.0 nainstalovanou v počítači:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Aktualizovat odkazy na balíčky

Soubor *.csproj* v projektu 1.x uvádí každý balíček NuGet používaný v projektu.

V ASP.NET projektu Core 2.0 zaměřeného na rozhraní .NET Core 2.0 nahradí kolekci balíčků jeden odkaz [na metabalíček](xref:fundamentals/metapackage) v souboru *.csproj:*

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

Všechny funkce ASP.NET Core 2.0 a Entity Framework Core 2.0 jsou zahrnuty v metabalíčku.

ASP.NET Core 2.0 projekty cílení .NET Framework by měl y nadále odkazovat na jednotlivé balíčky NuGet. Aktualizujte `Version` atribut `<PackageReference />` každého uzlu na 2.0.0.

Zde je například seznam `<PackageReference />` uzlů používaných v typickém ASP.NET cílování projektu Core 2.0 .NET Framework:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Aktualizace nástrojů rozhraní CLI .NET Core

V souboru *.csproj* `Version` aktualizujte `<DotNetCliToolReference />` atribut každého uzlu na 2.0.0.

Tady je například seznam nástrojů rozhraní CLI používaných v typickém ASP.NET cílení na projekt Core 2.0 .NET Core 2.0:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Přejmenovat záložní cíl balíčku, vlastnost

Soubor *.csproj* projektu 1.x používal `PackageTargetFallback` uzel a proměnnou:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Přejmenujte uzel i proměnnou na `AssetTargetFallback`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Aktualizovat hlavní metodu v Program.cs

V projektech 1.x vypadala `Main` metoda *Program.cs* takto:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

U projektů 2.0 `Main` byla metoda *Program.cs* zjednodušena:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

Přijetí tohoto nového vzoru 2.0 je vysoce doporučeno a je vyžadováno pro funkce produktu, jako je [core migrace entity framework (EF)](xref:data/ef-mvc/migrations) pro práci. Například spuštění `Update-Database` z okna konzoly `dotnet ef database update` Správce balíčků nebo z příkazového řádku (u projektů převedených na ASP.NET jádrem 2.0) generuje následující chybu:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Přidání zprostředkovatelů konfigurace

V projektech 1.x bylo přidání poskytovatelů konfigurace `Startup` do aplikace provedeno prostřednictvím konstruktoru. Kroky zahrnovaly vytvoření `ConfigurationBuilder`instance , načítání příslušných zprostředkovatelů (proměnné prostředí, nastavení aplikace `IConfigurationRoot`atd.) a inicializaci člena aplikace .

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

Předchozí příklad načte `Configuration` člena s nastavením konfigurace z *appsettings.json,* stejně jako všechny *appsettings.\< EnvironmentName\>.json* soubor `IHostingEnvironment.EnvironmentName` odpovídající vlastnost. Umístění těchto souborů je na stejné cestě jako *Startup.cs*.

V projektech 2.0 je standardní konfigurační kód vlastní 1.x projekty běží na pozadí. Například proměnné prostředí a nastavení aplikace se načítají při spuštění. Ekvivalentní *Startup.cs* kód je `IConfiguration` snížena na inicializaci s instřikované instance:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Chcete-li odebrat `WebHostBuilder.CreateDefaultBuilder`výchozí zprostředkovatele přidané `IConfigurationBuilder.Sources` aplikace `ConfigureAppConfiguration`, vyvolat `Clear` metodu na vlastnost uvnitř . Chcete-li přidat zprostředkovatele zpět, použijte metodu `ConfigureAppConfiguration` v *Program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

Konfigurace použitá `CreateDefaultBuilder` metodou v předchozím fragmentu kódu je k vidění [zde](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).

Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index).

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Přesunutí kódu inicializace databáze

V projektech 1.x pomocí EF Core 1.x, příkaz, jako `dotnet ef migrations add` je například následující:

1. Vytvoření instance instance `Startup`
1. Vyvolá metodu `ConfigureServices` registrace všech služeb s vkládání `DbContext` závislostí (včetně typů)
1. Provádí požadované úkoly

V 2.0 projekty pomocí EF `Program.BuildWebHost` Core 2.0, je vyvolána k získání aplikačních služeb. Na rozdíl od 1.x, to má další `Startup.Configure`vedlejší účinek vyvolání . Pokud vaše aplikace 1.x vyvolala inicializační kód databáze ve své `Configure` metodě, může dojít k neočekávaným problémům. Například pokud databáze ještě neexistuje, kód nastavení se spustí před spuštěním příkazu EF Core Migrations. Tento problém `dotnet ef migrations list` způsobí selhání příkazu, pokud databáze ještě neexistuje.

Vezměme si následující 1.x počáteční `Configure` inicializační kód v metodě *Startup.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

V projektech 2.0 `SeedData.Initialize` přesuňte `Main` volání na metodu *Program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

Od 2.0 je špatné dělat cokoli v `BuildWebHost` jiném než vytvořit a nakonfigurovat webhosting. Cokoli, co je o spuštění aplikace `BuildWebHost` &mdash; by měly `Main` být zpracovány mimo obvykle v metodě *Program.cs*.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>Zkontrolovat nastavení kompilace zobrazení Razor

Rychlejší spuštění aplikace a menší publikované balíčky jsou pro vás nesmírně důležité. Z těchto důvodů je [kompilace Razor view](xref:mvc/views/view-compilation) ve výchozím nastavení povolena v ASP.NET Core 2.0.

Nastavení `MvcRazorCompileOnPublish` vlastnosti na hodnotu true již není nutné. Pokud nezakážete kompilaci zobrazení, může být vlastnost odebrána ze souboru *.csproj.*

Při cílení na rozhraní .NET Framework je stále nutné explicitně odkazovat na balíček [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet ve vašem souboru *.csproj:*

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Spolehněte se na funkce Application Insights "light-up"

Snadné nastavení přístrojové desky výkonu aplikace je důležité. Nyní se můžete spolehnout na nové funkce [Application Insights](/azure/application-insights/app-insights-overview) "light-up", které jsou k dispozici v nástrojích Visual Studio 2017.

ASP.NET Core 1.1 projekty vytvořené ve Visual Studiu 2017 přidalapplication insights ve výchozím nastavení. Pokud nepoužíváte sdk application insights přímo, mimo *Program.cs* a *Startup.cs*, postupujte takto:

1. Pokud cílíte na jádro `<PackageReference />` .NET Core, odeberte ze souboru *.csproj* následující uzel:

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. Pokud cílení .NET Core, odeberte vyvolání metody `UseApplicationInsights` rozšíření z *Program.cs*:

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. Odeberte volání rozhraní API na straně klienta Application Insights z *_Layout.cshtml*. Skládá se z těchto dvou řádků kódu:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Pokud používáte sady Application Insights SDK přímo, pokračujte v tom. [Metabalíček](xref:fundamentals/metapackage) 2.0 obsahuje nejnovější verzi Application Insights, takže se zobrazí chyba downgradu balíčku, pokud odkazujete na starší verzi.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>Přijmout vylepšení ověřování a identity

ASP.NET Core 2.0 má nový model ověřování a řadu významných změn ASP.NET základní identity. Pokud jste projekt vytvořili s povolenými individuálními uživatelskými účty nebo jste ručně přidali ověřování nebo identitu, přečtěte si informace [o migraci ověřování a identity do ASP.NET jádra 2.0](xref:migration/1x-to-2x/identity-2x).

## <a name="additional-resources"></a>Další zdroje

* [Prolomení změn v ASP.NET jádra 2.0](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
