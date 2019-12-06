---
title: Migrace z ASP.NET Core 1. x na 2,0
author: scottaddie
description: V tomto článku najdete popis požadavků a nejběžnějších kroků migrace projektu ASP.NET Core 1. x na ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/1x-to-2x/index
ms.openlocfilehash: 1242ec9f71f4a26b07f9a56a2a960bf315b56ccf
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880018"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>Migrace z ASP.NET Core 1. x na 2,0

[Scott Addie](https://github.com/scottaddie)

V tomto článku Vás provedeme aktualizací existujícího projektu ASP.NET Core 1. x na ASP.NET Core 2,0. Migrace aplikace na ASP.NET Core 2,0 vám umožní využít [spoustu nových funkcí a vylepšení výkonu](xref:aspnetcore-2.0).

Stávající aplikace ASP.NET Core 1. x jsou založeny na šablonách projektů specifických pro danou verzi. Jak se vyvíjí rozhraní ASP.NET Core, takže proveďte šablony projektu a počáteční kód obsažený v nich. Kromě aktualizace ASP.NET Coreho rozhraní je potřeba aktualizovat kód pro vaši aplikaci.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Požadavky

Viz Začínáme [s ASP.NET Core](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Aktualizovat moniker cílového rozhraní (TFM)

Projekty cílené na .NET Core by měly používat [TFM](/dotnet/standard/frameworks) verze, která je větší nebo rovna .net Core 2,0. Vyhledejte uzel `<TargetFramework>` v souboru *. csproj* a nahraďte jeho vnitřní text `netcoreapp2.0`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

Projekty cílené na .NET Framework by měly používat TFM verze, která je větší nebo rovna .NET Framework 4.6.1. Vyhledejte uzel `<TargetFramework>` v souboru *. csproj* a nahraďte jeho vnitřní text `net461`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET Core 2,0 nabízí mnohem větší plochu než .NET Core 1. x. Pokud cílíte .NET Framework výhradně z důvodu chybějících rozhraní API v rozhraní .NET Core 1. x, bude pravděpodobně fungovat cílení na .NET Core 2,0.

Pokud soubor projektu obsahuje `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, přečtěte si [Tento problém GitHub](https://github.com/aspnet/AspNetCore/issues/3221#issuecomment-413094268).

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Aktualizace verze .NET Core SDK v Global. JSON

Pokud vaše řešení využívá soubor [Global. JSON](/dotnet/core/tools/global-json) pro cílení na konkrétní verzi .NET Core SDK, aktualizujte jeho vlastnost `version` na použití verze 2,0 nainstalované na vašem počítači:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Aktualizovat odkazy na balíček

Soubor *. csproj* v projektu 1. x obsahuje seznam všech balíčků NuGet používaných projektem.

V projektu ASP.NET Core 2,0 cíleném na rozhraní .NET Core 2,0 nahrazuje jeden odkaz [Metapackage](xref:fundamentals/metapackage) v souboru *. csproj* kolekci balíčků:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

Do Metapackage jsou zahrnuté všechny funkce ASP.NET Core 2,0 a Entity Framework Core 2,0.

Projekty ASP.NET Core 2,0 cílené na .NET Framework by měly dál odkazovat na jednotlivé balíčky NuGet. Aktualizujte atribut `Version` každého uzlu `<PackageReference />` na 2.0.0.

Tady je například seznam uzlů `<PackageReference />` používaných v typickém cílení projektu ASP.NET Core 2,0, .NET Framework:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Aktualizovat .NET Core CLI nástroje

V souboru *. csproj* aktualizujte atribut `Version` každého uzlu `<DotNetCliToolReference />` na 2.0.0.

Tady je například seznam nástrojů rozhraní příkazového řádku, které se používají v typickém ASP.NET Core 2,0 projektu cílící na .NET Core 2,0:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Přejmenovat záložní vlastnost cílového balíčku

Soubor *. csproj* v projektu 1. x používal `PackageTargetFallback` uzel a proměnnou:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Přejmenujte uzel i proměnnou na `AssetTargetFallback`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Aktualizace metody Main v Program.cs

V projektech 1. x `Main` metoda *program.cs* vypadala takto:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

V projektech 2,0 je `Main` metoda *program.cs* zjednodušená:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

Přijetí tohoto nového vzoru 2,0 se důrazně doporučuje a vyžaduje se pro funkce produktu, jako jsou [základní migrace pro Entity Framework (EF)](xref:data/ef-mvc/migrations) . Například spuštění `Update-Database` z okna konzoly Správce balíčků nebo `dotnet ef database update` z příkazového řádku (v projektech převedených na ASP.NET Core 2,0) generuje následující chybu:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Přidat poskytovatele konfigurace

V projektech 1. x bylo přidání zprostředkovatelů konfigurace do aplikace provedeno prostřednictvím konstruktoru `Startup`. Kroky při vytváření instance `ConfigurationBuilder`, načítající příslušné poskytovatele (proměnné prostředí, nastavení aplikace atd.) a inicializace člena `IConfigurationRoot`.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

Předchozí příklad načte člena `Configuration` s nastaveními konfigurace ze souboru *appSettings. JSON* a všech souborů *appSettings.\<Environment\>. JSON* , které odpovídají vlastnosti `IHostingEnvironment.EnvironmentName`. Umístění těchto souborů je na stejné cestě jako *Startup.cs*.

V projektech 2,0 se standardní konfigurační kód, který je podstatný pro 1. x, spouští za pozadí. Například proměnné prostředí a nastavení aplikace jsou načítány při spuštění. Ekvivalentní kód *Startup.cs* se zkracuje na `IConfiguration` inicializaci s vloženou instancí:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Chcete-li odebrat výchozí zprostředkovatele přidaných pomocí `WebHostBuilder.CreateDefaultBuilder`, volejte metodu `Clear` ve vlastnosti `IConfigurationBuilder.Sources` uvnitř `ConfigureAppConfiguration`. Chcete-li přidat zprostředkovatele zpět, využijte metodu `ConfigureAppConfiguration` v *program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

Konfiguraci, kterou používá metoda `CreateDefaultBuilder` v předchozím fragmentu kódu, lze zobrazit [zde](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).

Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index).

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Přesunout inicializační kód databáze

V projektech 1. x, které používají EF Core 1. x, příkaz jako `dotnet ef migrations add` provede následující:

1. Vytvoří instanci instance `Startup`.
1. Vyvolá metodu `ConfigureServices` k registraci všech služeb pomocí injektáže závislosti (včetně `DbContext`ch typů).
1. Provede požadované úlohy.

V 2,0ch projektech, které používají EF Core 2,0 `Program.BuildWebHost` je vyvoláno získání aplikačních služeb. Na rozdíl od 1. x to má další vedlejší účinek vyvolání `Startup.Configure`. Pokud vaše aplikace 1. x vyvolala inicializační kód databáze ve své metodě `Configure`, může dojít k neočekávaným potížím. Například pokud databáze ještě neexistuje, kód pro osazení se spustí před spuštěním příkazu EF Core migrace. Tento problém způsobí selhání příkazu `dotnet ef migrations list`, pokud databáze ještě neexistuje.

V metodě `Configure` *Startup.cs*zvažte následující inicializační kód počátečního použití 1. x:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

V projektech 2,0 přesuňte volání `SeedData.Initialize` do metody `Main` *program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

Od 2,0 se nejedná o špatný postup v `BuildWebHost` s výjimkou sestavení a konfigurace webového hostitele. Cokoli, co je o spuštění aplikace, by mělo být zpracováno mimo `BuildWebHost` &mdash; obvykle v metodě `Main` of *program.cs*.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>Kontrola nastavení kompilace zobrazení Razor

Rychlejší čas spuštění aplikace a menší publikované sady mají největší důležitost. Z těchto důvodů je [kompilace zobrazení Razor](xref:mvc/views/view-compilation) ve výchozím nastavení povolená v ASP.NET Core 2,0.

Nastavení vlastnosti `MvcRazorCompileOnPublish` na hodnotu true již není vyžadováno. Pokud nezakážete kompilaci zobrazení, vlastnost může být odebrána ze souboru *. csproj* .

Při cílení na .NET Framework stále musíte explicitně odkazovat na balíček NuGet [Microsoft. AspNetCore. Mvc. Razor. ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) v souboru *. csproj* :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Spoléhá se na Application Insights funkce "světlého".

Snadné nastavení instrumentace výkonu aplikace je důležité. Nyní se můžete spoléhat na [Application Insights](/azure/application-insights/app-insights-overview) nové funkce "světlého", které jsou k dispozici v nástrojích sady Visual Studio 2017.

V aplikaci Visual Studio 2017 byly ve výchozím nastavení přidány Application Insights projekty ASP.NET Core 1,1. Pokud nepoužíváte Application Insights SDK přímo, mimo *program.cs* a *Startup.cs*, postupujte podle těchto kroků:

1. Pokud cílíte na rozhraní .NET Core, odeberte následující uzel `<PackageReference />` ze souboru *. csproj* :

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. Pokud cílíte na rozhraní .NET Core, odeberte `UseApplicationInsights` volání metody rozšíření z *program.cs*:

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. Odeberte Application Insights volání rozhraní API na straně klienta z *_Layout. cshtml*. Obsahuje následující dva řádky kódu:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Pokud používáte sadu Application Insights SDK přímo, pokračujte v tomto případě. 2,0 [Metapackage](xref:fundamentals/metapackage) zahrnuje nejnovější verzi Application Insights, takže při odkazování na starší verzi se zobrazí chyba downgrade balíčku.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>Přijmout vylepšení ověřování/identity

ASP.NET Core 2,0 má nový model ověřování a řadu významných změn pro ASP.NET Coreou identitu. Pokud jste vytvořili projekt s povolenými jednotlivými uživatelskými účty nebo jste ručně přidali ověřování nebo identitu, přečtěte si téma [migrace ověřování a identity na ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x).

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Přerušující změny v ASP.NET Core 2,0](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
