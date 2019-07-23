---
title: Migrace z technologie ASP.NET Core 1.x do 2.0
author: scottaddie
description: Tento článek popisuje požadavky a nejběžnější kroky migrace projektu aplikace ASP.NET Core 1.x do ASP.NET Core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
uid: migration/1x-to-2x/index
ms.openlocfilehash: 056930f3c586153d13555bbb6036f46587e2352d
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815092"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>Migrace z technologie ASP.NET Core 1.x do 2.0

Podle [Scott Addie](https://github.com/scottaddie)

V tomto článku jsme vás provede aktualizaci existující projekt ASP.NET Core 1.x do ASP.NET Core 2.0. Migrace aplikace ASP.NET Core 2.0 umožňuje využít výhod [řadu nových funkcí a vylepšení výkonu](xref:aspnetcore-2.0).

Stávající aplikace ASP.NET Core 1.x vycházejí z šablony projektu pro konkrétní verzi. Jak rozhraní ASP.NET Core vyvíjí, proto šablony projektů a proveďte počáteční kód v nich obsažené. Kromě aktualizací rozhraní ASP.NET Core, je potřeba aktualizovat kód pro vaši aplikaci.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Požadavky

Zobrazit [Začínáme s ASP.NET Core](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Aktualizovat Moniker cílového rozhraní (TFM)

Používejte projekty cílené na .NET Core [TFM](/dotnet/standard/frameworks) verze větší než nebo rovna hodnotě .NET Core 2.0. Hledat `<TargetFramework>` uzlu v *.csproj* souboru a nahraďte jeho vnitřní text s `netcoreapp2.0`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

Projekty cílené na rozhraní .NET Framework by měl používat TFM verze větší než nebo rovna hodnotě rozhraní .NET Framework 4.6.1. Hledat `<TargetFramework>` uzlu v *.csproj* souboru a nahraďte jeho vnitřní text s `net461`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET core 2.0 nabízí mnohem větší plochu povrchu než .NET Core 1.x. Pokud na rozhraní .NET Framework pouze z důvodu chybějící rozhraní API v .NET Core 1.x cílí na .NET Core 2.0 je pravděpodobně fungovat.

Pokud soubor projektu obsahuje `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, naleznete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/3221#issuecomment-413094268).

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Aktualizovat verzi .NET Core SDK v global.json

Pokud vaše řešení závisí [ *global.json* ](/dotnet/core/tools/global-json) soubor mířila na konkrétní verzi .NET Core SDK, aktualizovat jeho `version` vlastnost na používání verze 2.0 na vašem počítači nainstalovaný:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Aktualizovat odkazy na balíček

*.Csproj* soubor v projektu 1.x uvádí každý balíček NuGet používá v projektu.

V projektu aplikace ASP.NET Core 2.0 cílí na .NET Core 2.0, jeden [Microsoft.aspnetcore.all](xref:fundamentals/metapackage) odkazovat *.csproj* soubor nahradí kolekce balíčků:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

Všechny funkce technologie ASP.NET Core 2.0 a Entity Framework Core 2.0 jsou součástí Microsoft.aspnetcore.all.

Projekty ASP.NET Core 2.0, které cílí na rozhraní .NET Framework by měl dál odkazovat na jednotlivé balíčky NuGet. Aktualizace `Version` atribut jednotlivých `<PackageReference />` uzlu 2.0.0.

Například tady je seznam `<PackageReference />` uzly použité ve obvyklou pro projekty ASP.NET Core 2.0 cílí na rozhraní .NET Framework:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Aktualizace nástrojů rozhraní příkazového řádku .NET Core

V *.csproj* soubor, aktualizovat `Version` atribut jednotlivých `<DotNetCliToolReference />` uzlu 2.0.0.

Například tady je seznam nástrojů rozhraní příkazového řádku používané obvyklou pro projekty ASP.NET Core 2.0 cílí na .NET Core 2.0:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Přejmenovat vlastnost záložní cíl balíčku

*.Csproj* soubor projektu 1.x použít `PackageTargetFallback` uzlu a proměnné:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Přejmenovat uzel a proměnnou pro `AssetTargetFallback`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Aktualizujte metodu Main v souboru Program.cs

V projektech 1.x `Main` metoda *Program.cs* podívali takto:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

V projektech pro 2.0 `Main` metoda *Program.cs* zjednodušili jsme:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

Přijetí této nové 2.0 vzor se důrazně doporučuje a je funkce produktu, jako je třeba [migrace Entity Framework (EF) Core](xref:data/ef-mvc/migrations) pracovat. Například systém `Update-Database` z okna konzoly Správce balíčků nebo `dotnet ef database update` z příkazu řádku (na projekty byly převedeny do ASP.NET Core 2.0) generuje následující chybu:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Přidejte poskytovatele konfigurace

V projektech 1.x přidání zprostředkovatele konfigurace aplikace byla nakonfigurovat pomocí `Startup` konstruktoru. Součástí kroky vytvoření instance `ConfigurationBuilder`, načítání příslušných poskytovatelů (proměnné prostředí, nastavení aplikace atd.) a inicializuje se členem `IConfigurationRoot`.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

Předchozí příklad načte `Configuration` člena s nastavením konfigurace z *appsettings.json* a také všechny *appsettings.\< EnvironmentName\>.json* odpovídající soubor `IHostingEnvironment.EnvironmentName` vlastnost. Umístění těchto souborů je na stejné cestě jako *Startup.cs*.

V projektech, 2.0 často používaný kód konfigurace přináší 1.x projekty spustí pozadí. Proměnné prostředí a nastavení aplikace jsou načteny při spuštění. Ekvivalent *Startup.cs* kódu je omezená na `IConfiguration` inicializace pomocí vloženého instance:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Chcete-li odebrat výchozí poskytovatele přidal `WebHostBuilder.CreateDefaultBuilder`, vyvolat `Clear` metodu na `IConfigurationBuilder.Sources` vlastnosti uvnitř `ConfigureAppConfiguration`. Přidání zprostředkovatele zpět, využívat `ConfigureAppConfiguration` metoda *Program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

Konfigurace používané `CreateDefaultBuilder` metoda v předchozím fragmentu kódu můžete vidět [tady](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).

Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index).

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Přesunutí databáze inicializační kód

V projektech 1.x pomocí EF Core 1.x, příkazy jako `dotnet ef migrations add` provede následující akce:

1. Vytvoří instanci `Startup` instance
1. Vyvolá `ConfigureServices` metody pro registraci všech služeb s injektáž závislostí (včetně `DbContext` typy)
1. Provádí potřebné úkoly

V 2.0 projektů pomocí EF Core 2.0 `Program.BuildWebHost` se vyvolá k získání aplikační služby. Na rozdíl od 1.x, tato akce nemá další vedlejší účinek vyvolání `Startup.Configure`. Pokud vaše aplikace 1.x vyvolat databáze inicializační kód v jeho `Configure` metodu, může dojít k neočekávaným problémům. Například pokud databáze ještě neexistuje, osazení kód se spustí před spuštěním příkazu migrace EF Core. Způsobí, že tento problém `dotnet ef migrations list` příkaz selhat, pokud databáze ještě neexistuje.

Uvažujme následující kód inicializace 1.x počáteční hodnoty v `Configure` metoda *Startup.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

V projektech, 2.0, přesunout `SeedData.Initialize` volání `Main` metoda *Program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

Od verze 2.0, je špatný postup k ničemu `BuildWebHost` s výjimkou sestavení a konfiguraci webového hostitele. Cokoli, co se týká spuštění aplikace by měly být zpracovány mimo `BuildWebHost` &mdash; obvykle v `Main` metoda *Program.cs*.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>Zkontrolujte nastavení kompilace zobrazení Razor

Rychlejší spuštění aplikace a menší publikované sady jsou naprosto vám. Z těchto důvodů [kompilace zobrazení Razor](xref:mvc/views/view-compilation) je povolené ve výchozím nastavení v ASP.NET Core 2.0.

Nastavení `MvcRazorCompileOnPublish` vlastnost na hodnotu true se už nevyžaduje. Pokud se zakázání kompilace zobrazení, vlastnosti mohou být odebrány *.csproj* souboru.

Při cílení na rozhraní .NET Framework, je stále potřeba explicitně odkazovat [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) balíčku NuGet ve vaší *.csproj* souboru:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Spolehněte se na funkce "světla nahoru" Application Insights

Snadné nastavení instrumentace aplikací výkonu je důležité. Nyní se můžete spolehnout na novém [Application Insights](/azure/application-insights/app-insights-overview) "světla nahoru" funkcí nástroje Visual Studio 2017 k dispozici.

Projekty ASP.NET Core 1.1 vytvořené v sadě Visual Studio 2017 přidat Application Insights ve výchozím nastavení. Pokud nepoužíváte sadu SDK Application Insights přímo, mimo *Program.cs* a *Startup.cs*, postupujte podle těchto kroků:

1. Pokud je zaměřen na .NET Core, odeberte následující `<PackageReference />` uzlu z *.csproj* souboru:

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. Pokud cílí na .NET Core, odeberte `UseApplicationInsights` volání metody rozšíření z *Program.cs*:

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. Odeberte volání rozhraní API služby Application Insights na straně klienta z *_Layout.cshtml*. To zahrnuje následující dva řádky kódu:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Pokud používáte sadu SDK Application Insights přímo, Uděláte to tak dál. Rozhraní příkazového řádku 2.0 [Microsoft.aspnetcore.all](xref:fundamentals/metapackage) obsahuje nejnovější verzi služby Application Insights, takže pokud už odkazuje na starší verze, zobrazí se chyba downgrade balíčku.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>Přijmout vylepšení ověřování a identita

ASP.NET Core 2.0 je nový model ověřování a počet významných změn ASP.NET Core Identity. Pokud jste vytvořili projekt s jednotlivými uživatelskými účty, které jsou povolené, nebo pokud jste ručně přidali ověřování nebo Identity, najdete v článku [migrovat ověřování a identita na technologii ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).

## <a name="additional-resources"></a>Další zdroje

* [Rozbíjející změny v ASP.NET Core 2.0](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
