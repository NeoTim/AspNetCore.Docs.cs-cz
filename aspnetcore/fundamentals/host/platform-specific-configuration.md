---
title: Použití hostování spouštěcích sestavení v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vylepšit ASP.NET Core aplikaci z externího sestavení pomocí implementace IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 0636c62c4373533234ab252d64052b476b123bbf
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405091"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Použití hostování spouštěcích sestavení v ASP.NET Core

Od [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení. Externí knihovna může například použít hostitelskou implementaci při spuštění k poskytnutí dalších poskytovatelů konfigurace nebo služeb do aplikace.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup – atribut

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.

Pro zadání sestavení nebo sestavení obsahujícího `Startup` třídu se automaticky vyhledá `HostingStartup` atribut. Seznam sestavení pro vyhledávání `HostingStartup` atributů je načten za běhu z konfigurace ve [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).

V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement` . Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup`Atribut je obvykle umístěn v souboru implementační třídy sestavení, který spouští `IHostingStartup` .

## <a name="discover-loaded-hosting-startup-assemblies"></a>Najít načtená hostování spouštěcích sestavení

Chcete-li zjistit načtená hostování spouštěcích sestavení, povolte protokolování a ověřte protokoly aplikace. Chyby, ke kterým dochází při načítání sestavení, jsou protokolovány. Načtená hostující spouštěcí sestavení jsou protokolována na úrovni ladění a všechny chyby jsou protokolovány.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Zakázat automatické načítání hostujících spouštěcích sestavení

Chcete-li zakázat automatické načítání hostujících spouštěcích sestavení, použijte jeden z následujících přístupů:

* Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1` :

  * Zabránit hostování nastavení konfigurace hostitele po spuštění:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_PREVENTHOSTINGSTARTUP`Proměnná prostředí.

* Chcete-li zabránit tomu, aby se konkrétní hostování spouštěcích sestavení načetlo, nastavte jednu z následujících hodnot na řetězec, který je hostitelem spouštěcích sestavení s oddělovači, aby při spuštění vyloučil:

  * Hostování nastavení konfigurace vyloučení sestavení hostitele:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`Proměnná prostředí.

Pokud je nastavené nastavení konfigurace hostitele i proměnná prostředí, řídí se chování nastavení hostitele.

Zakázání hostování spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik vlastností aplikace.

## <a name="project"></a>Project

Vytvořte hostování po spuštění s některým z následujících typů projektů:

* [Knihovna tříd](#class-library)
* [Konzolová aplikace bez vstupního bodu](#console-app-without-an-entry-point)

### <a name="class-library"></a>Knihovna tříd

Rozšíření spouštění hostitele lze poskytnout v knihovně tříd. Knihovna obsahuje `HostingStartup` atribut.

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor stránku aplikace, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*. Knihovna tříd:

* Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` . `ServiceKeyInjection`Přidá dvojici řetězců služby k konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu pro spuštění hostingu.

`ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.

*HostingStartupLibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Stránka indexu aplikace načte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostujícím sestavením pro spuštění knihovny tříd:

*HostingStartupApp/pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*. Balíček má stejné charakteristiky jako knihovna tříd popsané výše. Balíček:

* Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` . `ServiceKeyInjection`Přidá do konfigurace aplikace dvojici řetězců služby.
* Obsahuje `HostingStartup` atribut.

*HostingStartupPackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Stránka indexu aplikace čte a vykresluje konfigurační hodnoty pro dva klíče nastavené hostujícím sestavením pro spuštění balíčku:

*HostingStartupApp/pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konzolová aplikace bez vstupního bodu

*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*

Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut. Publikování konzolové aplikace vytváří hostující spouštěcí sestavení, které lze spotřebovat z běhového úložiště.

V tomto procesu se používá Konzolová aplikace bez vstupního bodu, protože:

* Soubor závislostí je vyžadován ke využívání hostování při spouštění v sestavení, ve kterém se spouští. Soubor závislostí je spustitelný App Asset, který je vytvořený publikováním aplikace, ne knihovny.
* Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.

Při vytváření dynamického spuštění hostování:

* Hostující spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:
  * Obsahuje třídu, která obsahuje `IHostingStartup` implementaci.
  * Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pro identifikaci `IHostingStartup` implementační třídy.
* Konzolová aplikace se publikuje, aby získala závislosti hostování při spuštění. V důsledku publikování konzolové aplikace je, že nevyužité závislosti se oříznou ze souboru závislostí.
* Soubor závislostí je upraven, aby bylo možné nastavit umístění modulu runtime hostujícího spouštěcí sestavení.
* Hostování spouštěcího sestavení a jeho souboru závislostí je umístěno do úložiště balíčků modulu runtime. Chcete-li zjistit hostující sestavení po spuštění a jeho soubor závislosti, jsou uvedeny ve dvojici proměnných prostředí.

Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost> . V následujícím příkladu je obor názvů `StartupEnhancement` a třída `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Třída implementuje `IHostingStartup` . <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace. `IHostingStartup.Configure`ve spouštěcím sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, což umožňuje přepsání libovolné konfigurace poskytované hostováním sestavení po spuštění.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Při sestavování `IHostingStartup` projektu, soubor závislosti (*.deps.jszapnuto*) nastaví `runtime` umístění sestavení do složky *bin* :

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Zobrazí se pouze část souboru. Název sestavení v příkladu je `StartupEnhancement` .

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfigurace poskytovaná spuštěním hostování

Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:

1. Poskytněte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> , aby se konfigurace po spuštění delegátů aplikace načetla <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> . Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.
1. Zadejte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> , aby se konfigurace načetla předtím, než se <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> spustí Delegáti aplikace. Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Zadejte hostující sestavení pro spuštění

V případě knihovny tříd nebo konzolové aplikace dodané po spuštění zadejte název hostujícího spouštěcího sestavení v `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí. Proměnná prostředí je seznam sestavení oddělených středníky.

Pro atribut jsou kontrolována pouze spouštěcí sestavení hostování `HostingStartup` . Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostování po spouštěcích sestaveních hostitele:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.

## <a name="activation"></a>Aktivace

Možnosti pro hostování aktivace po spuštění jsou:

* [Běhové úložiště](#runtime-store): Aktivace nevyžaduje pro aktivaci odkaz na čas kompilace. Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači. Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.
* Pro aktivaci se vyžaduje odkaz na čas kompilace.
  * [Balíček NuGet](#nuget-package)
  * [Složka Bin projektu](#project-bin-folder)

### <a name="runtime-store"></a>Běhové úložiště

Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store). Rozšířená aplikace nevyžaduje odkaz na sestavení v době kompilace.

Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do `DOTNET_SHARED_STORE` proměnné prostředí.

**Upravit a umístit soubor závislostí se spouštěním hostitele**

Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps` . `additionalDeps`umožňuje:

* Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších *.deps.js* souborů pro sloučení s vlastní *.deps.jsaplikace v* souboru při spuštění.
* Zpřístupněte hostující spouštěcí sestavení, které je zjistitelné a spustitelný.

Doporučený postup pro generování dalších souborů závislostí:

 1. Spustit `dotnet publish` v souboru manifestu úložiště modulu runtime odkazovaného v předchozí části.
 1. Odeberte odkaz na manifest z knihoven a `runtime` část výsledného *.deps.js* souboru.

V příkladu projektu `store.manifest/1.0.0` je vlastnost odebrána z `targets` `libraries` oddílu a:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Umístit *.deps.js* do souboru do následujícího umístění:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: Umístění bylo přidáno do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.
* `{SHARED FRAMEWORK NAME}`: Sdílené rozhraní požadované pro tento další soubor závislostí.
* `{SHARED FRAMEWORK VERSION}`: Minimální verze sdíleného rozhraní .NET Framework.
* `{ENHANCEMENT ASSEMBLY NAME}`: Název sestavení rozšíření.

V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí přidáno umístění souboru další závislosti `DOTNET_ADDITIONAL_DEPS` .

V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .

Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).

**Nasazení**

Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:

* Hostování běhového úložiště při spuštění.
* Hostující soubor závislostí po spuštění.
* Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` , `DOTNET_SHARED_STORE` a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění. Spusťte skript z příkazového řádku PowerShellu pro správu v systému nasazení.

### <a name="nuget-package"></a>Balíček NuGet

Rozšíření spouštění hostitele lze poskytnout v balíčku NuGet. Balíček má `HostingStartup` atribut. Typy spuštění hostování poskytované balíčkem jsou k dispozici v aplikaci pomocí některého z následujících přístupů:

* Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spouštěné v souboru projektu aplikace (Referenční dokumentace při kompilaci). Při použití reference v době kompilace, hostující sestavení po spuštění a všechny jeho závislosti jsou začleněny do souboru závislosti aplikace (*.deps.json*). Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).
* K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).

Další informace o balíčcích NuGet a úložišti modulu runtime najdete v následujících tématech:

* [Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy](/dotnet/core/deploying/creating-nuget-packages)
* [Publikování balíčků](/nuget/create-packages/publish-a-package)
* [Úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Složka Bin projektu

Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci. Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:

* Soubor projektu rozšířené aplikace vytvoří odkaz na sestavení pro spuštění hostování (Referenční dokumentace při kompilaci). Při použití reference v době kompilace, hostující sestavení po spuštění a všechny jeho závislosti jsou začleněny do souboru závislosti aplikace (*.deps.json*). Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:
  * Projekt, který je k disnáročné.
  * Umístění přístupné pro náročný projekt.
* K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).
* Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:
  * Základní cesta aplikace: složka *bin* , kde se nachází spustitelný soubor aplikace (*. exe*).
  * Globální mezipaměť sestavení (GAC): GAC ukládá sestavení, která sdílí několik aplikací .NET Framework. Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.

## <a name="sample-code"></a>Ukázka kódu

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:

* Dvě hostování spouštěcích sestavení (knihovny tříd) nastaví dvojici dvojic klíč-hodnota klíče pro konfiguraci v paměti každý:
  * Balíček NuGet (*HostingStartupPackage*)
  * Knihovna tříd (*HostingStartupLibrary*)
* Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu. Sestavení přidá do aplikace dvě middlewary při spuštění, které poskytují diagnostické informace o:
  * Registrované služby
  * Adresa (schéma, hostitel, základ cesty, cesta, řetězec dotazu)
  * Připojení (Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát)
  * Hlavičky požadavku
  * Proměnné prostředí

Spuštění ukázky:

**Aktivace z balíčku NuGet**

1. Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .
1. Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
1. Zkompilujte a spusťte aplikaci. Odkaz na balíček se nachází v rozšířené aplikaci (Referenční dokumentace při kompilaci). `<PropertyGroup>`V souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku. To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou balíčku `ServiceKeyInjection.Configure` .

Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti. K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :

```dotnetcli
dotnet nuget locals all --clear
```

**Aktivace z knihovny tříd**

1. Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .
1. Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
1. *bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary.dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.
1. Zkompilujte a spusťte aplikaci. `<ItemGroup>`V souboru projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou knihovny tříd `ServiceKeyInjection.Configure` .

**Aktivace z běhového úložiště – nasazené sestavení**

1. Projekt *StartupDiagnostics* pomocí [prostředí PowerShell](/powershell/scripting/powershell-scripting) upraví jeho *StartupDiagnostics.deps.jsv* souboru. PowerShell je ve výchozím nastavení nainstalován v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1. Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace různých verzí PowerShellu](/powershell/scripting/install/installing-powershell).
1. Spusťte skript *build.ps1* ve složce *RuntimeStore* . Skript:
   * Vygeneruje `StartupDiagnostics` balíček ve složce *obj\packages* .
   * Vygeneruje běhové úložiště pro `StartupDiagnostics` ve složce *Store* . `dotnet store`Příkaz ve skriptu používá `win7-x64` [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows. Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu. Běhové úložiště pro `StartupDiagnostics` by bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno. Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/StartupDiagnostics.dll*.
   * Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* . Další závislosti by se později přesunuly na další závislosti uživatele nebo systému. `StartupDiagnostics`Umístění instalace dalších závislostí uživatele je *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/3.0.0/StartupDiagnostics.deps.json*.
   * Umístí soubor *deploy.ps1* do složky pro *nasazení* .
1. Spusťte skript *deploy.ps1* ve složce pro *nasazení* . Skript připojí:
   * `StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
   * Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.
   * Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do `DOTNET_SHARED_STORE` proměnné prostředí.
1. Spusťte ukázkovou aplikaci.
1. Požádejte o `/services` koncový bod, aby se zobrazily registrované služby aplikace. Požádejte `/diag` koncový bod, aby zobrazil diagnostické informace.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení. Externí knihovna může například použít hostitelskou implementaci při spuštění k poskytnutí dalších poskytovatelů konfigurace nebo služeb do aplikace.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup – atribut

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.

Pro zadání sestavení nebo sestavení obsahujícího `Startup` třídu se automaticky vyhledá `HostingStartup` atribut. Seznam sestavení pro vyhledávání `HostingStartup` atributů je načten za běhu z konfigurace ve [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey). Další informace najdete v tématu [webový hostitel: hostování spouštěcích sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [webového hostitele: hostování spouštěcích vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement` . Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup`Atribut je obvykle umístěn v souboru implementační třídy sestavení, který spouští `IHostingStartup` .

## <a name="discover-loaded-hosting-startup-assemblies"></a>Najít načtená hostování spouštěcích sestavení

Chcete-li zjistit načtená hostování spouštěcích sestavení, povolte protokolování a ověřte protokoly aplikace. Chyby, ke kterým dochází při načítání sestavení, jsou protokolovány. Načtená hostující spouštěcí sestavení jsou protokolována na úrovni ladění a všechny chyby jsou protokolovány.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Zakázat automatické načítání hostujících spouštěcích sestavení

Chcete-li zakázat automatické načítání hostujících spouštěcích sestavení, použijte jeden z následujících přístupů:

* Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1` :
  * Zabrání hostování nastavení konfigurace hostitele [po spuštění](xref:fundamentals/host/web-host#prevent-hosting-startup) .
  * `ASPNETCORE_PREVENTHOSTINGSTARTUP`Proměnná prostředí.
* Chcete-li zabránit tomu, aby se konkrétní hostování spouštěcích sestavení načetlo, nastavte jednu z následujících hodnot na řetězec, který je hostitelem spouštěcích sestavení s oddělovači, aby při spuštění vyloučil:
  * Hostování nastavení konfigurace hostitele [vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)
  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`Proměnná prostředí.

Pokud je nastavené nastavení konfigurace hostitele i proměnná prostředí, řídí se chování nastavení hostitele.

Zakázání hostování spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik vlastností aplikace.

## <a name="project"></a>Project

Vytvořte hostování po spuštění s některým z následujících typů projektů:

* [Knihovna tříd](#class-library)
* [Konzolová aplikace bez vstupního bodu](#console-app-without-an-entry-point)

### <a name="class-library"></a>Knihovna tříd

Rozšíření spouštění hostitele lze poskytnout v knihovně tříd. Knihovna obsahuje `HostingStartup` atribut.

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor stránku aplikace, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*. Knihovna tříd:

* Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` . `ServiceKeyInjection`Přidá dvojici řetězců služby k konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu pro spuštění hostingu.

`ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace.

*HostingStartupLibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Stránka indexu aplikace načte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostujícím sestavením pro spuštění knihovny tříd:

*HostingStartupApp/pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*. Balíček má stejné charakteristiky jako knihovna tříd popsané výše. Balíček:

* Obsahuje hostující spouštěcí třídu, `ServiceKeyInjection` která implementuje `IHostingStartup` . `ServiceKeyInjection`Přidá do konfigurace aplikace dvojici řetězců služby.
* Obsahuje `HostingStartup` atribut.

*HostingStartupPackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Stránka indexu aplikace čte a vykresluje konfigurační hodnoty pro dva klíče nastavené hostujícím sestavením pro spuštění balíčku:

*HostingStartupApp/pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konzolová aplikace bez vstupního bodu

*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*

Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut. Publikování konzolové aplikace vytváří hostující spouštěcí sestavení, které lze spotřebovat z běhového úložiště.

V tomto procesu se používá Konzolová aplikace bez vstupního bodu, protože:

* Soubor závislostí je vyžadován ke využívání hostování při spouštění v sestavení, ve kterém se spouští. Soubor závislostí je spustitelný App Asset, který je vytvořený publikováním aplikace, ne knihovny.
* Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.

Při vytváření dynamického spuštění hostování:

* Hostující spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:
  * Obsahuje třídu, která obsahuje `IHostingStartup` implementaci.
  * Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pro identifikaci `IHostingStartup` implementační třídy.
* Konzolová aplikace se publikuje, aby získala závislosti hostování při spuštění. V důsledku publikování konzolové aplikace je, že nevyužité závislosti se oříznou ze souboru závislostí.
* Soubor závislostí je upraven, aby bylo možné nastavit umístění modulu runtime hostujícího spouštěcí sestavení.
* Hostování spouštěcího sestavení a jeho souboru závislostí je umístěno do úložiště balíčků modulu runtime. Chcete-li zjistit hostující sestavení po spuštění a jeho soubor závislosti, jsou uvedeny ve dvojici proměnných prostředí.

Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost> . V následujícím příkladu je obor názvů `StartupEnhancement` a třída `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Třída implementuje `IHostingStartup` . <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení aplikace. `IHostingStartup.Configure`ve spouštěcím sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, což umožňuje přepsání libovolné konfigurace poskytované hostováním sestavení po spuštění.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Při sestavování `IHostingStartup` projektu, soubor závislosti (*.deps.jszapnuto*) nastaví `runtime` umístění sestavení do složky *bin* :

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Zobrazí se pouze část souboru. Název sestavení v příkladu je `StartupEnhancement` .

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfigurace poskytovaná spuštěním hostování

Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:

1. Poskytněte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> , aby se konfigurace po spuštění delegátů aplikace načetla <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> . Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.
1. Zadejte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> , aby se konfigurace načetla předtím, než se <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> spustí Delegáti aplikace. Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Zadejte hostující sestavení pro spuštění

V případě knihovny tříd nebo konzolové aplikace dodané po spuštění zadejte název hostujícího spouštěcího sestavení v `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí. Proměnná prostředí je seznam sestavení oddělených středníky.

Pro atribut jsou kontrolována pouze spouštěcí sestavení hostování `HostingStartup` . Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostitele [po spouštěcích sestaveních](xref:fundamentals/host/web-host#hosting-startup-assemblies) .

Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.

## <a name="activation"></a>Aktivace

Možnosti pro hostování aktivace po spuštění jsou:

* [Běhové úložiště](#runtime-store): Aktivace nevyžaduje pro aktivaci odkaz na čas kompilace. Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači. Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.
* Pro aktivaci se vyžaduje odkaz na čas kompilace.
  * [Balíček NuGet](#nuget-package)
  * [Složka Bin projektu](#project-bin-folder)

### <a name="runtime-store"></a>Běhové úložiště

Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store). Rozšířená aplikace nevyžaduje odkaz na sestavení v době kompilace.

Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do `DOTNET_SHARED_STORE` proměnné prostředí.

**Upravit a umístit soubor závislostí se spouštěním hostitele**

Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps` . `additionalDeps`umožňuje:

* Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších *.deps.js* souborů pro sloučení s vlastní *.deps.jsaplikace v* souboru při spuštění.
* Zpřístupněte hostující spouštěcí sestavení, které je zjistitelné a spustitelný.

Doporučený postup pro generování dalších souborů závislostí:

 1. Spustit `dotnet publish` v souboru manifestu úložiště modulu runtime odkazovaného v předchozí části.
 1. Odeberte odkaz na manifest z knihoven a `runtime` část výsledného *.deps.js* souboru.

V příkladu projektu `store.manifest/1.0.0` je vlastnost odebrána z `targets` `libraries` oddílu a:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Umístit *.deps.js* do souboru do následujícího umístění:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: Umístění bylo přidáno do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.
* `{SHARED FRAMEWORK NAME}`: Sdílené rozhraní požadované pro tento další soubor závislostí.
* `{SHARED FRAMEWORK VERSION}`: Minimální verze sdíleného rozhraní .NET Framework.
* `{ENHANCEMENT ASSEMBLY NAME}`: Název sestavení rozšíření.

V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí přidáno umístění souboru další závislosti `DOTNET_ADDITIONAL_DEPS` .

V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .

Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).

**Nasazení**

Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:

* Hostování běhového úložiště při spuštění.
* Hostující soubor závislostí po spuštění.
* Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` , `DOTNET_SHARED_STORE` a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění. Spusťte skript z příkazového řádku PowerShellu pro správu v systému nasazení.

### <a name="nuget-package"></a>Balíček NuGet

Rozšíření spouštění hostitele lze poskytnout v balíčku NuGet. Balíček má `HostingStartup` atribut. Typy spuštění hostování poskytované balíčkem jsou k dispozici v aplikaci pomocí některého z následujících přístupů:

* Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spouštěné v souboru projektu aplikace (Referenční dokumentace při kompilaci). Při použití reference v době kompilace, hostující sestavení po spuštění a všechny jeho závislosti jsou začleněny do souboru závislosti aplikace (*.deps.json*). Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).
* K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).

Další informace o balíčcích NuGet a úložišti modulu runtime najdete v následujících tématech:

* [Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy](/dotnet/core/deploying/creating-nuget-packages)
* [Publikování balíčků](/nuget/create-packages/publish-a-package)
* [Úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Složka Bin projektu

Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci. Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:

* Soubor projektu rozšířené aplikace vytvoří odkaz na sestavení pro spuštění hostování (Referenční dokumentace při kompilaci). Při použití reference v době kompilace, hostující sestavení po spuštění a všechny jeho závislosti jsou začleněny do souboru závislosti aplikace (*.deps.json*). Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:
  * Projekt, který je k disnáročné.
  * Umístění přístupné pro náročný projekt.
* K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).
* Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:
  * Základní cesta aplikace: složka *bin* , kde se nachází spustitelný soubor aplikace (*. exe*).
  * Globální mezipaměť sestavení (GAC): GAC ukládá sestavení, která sdílí několik aplikací .NET Framework. Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.

## <a name="sample-code"></a>Ukázka kódu

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:

* Dvě hostování spouštěcích sestavení (knihovny tříd) nastaví dvojici dvojic klíč-hodnota klíče pro konfiguraci v paměti každý:
  * Balíček NuGet (*HostingStartupPackage*)
  * Knihovna tříd (*HostingStartupLibrary*)
* Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu. Sestavení přidá do aplikace dvě middlewary při spuštění, které poskytují diagnostické informace o:
  * Registrované služby
  * Adresa (schéma, hostitel, základ cesty, cesta, řetězec dotazu)
  * Připojení (Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát)
  * Hlavičky požadavku
  * Proměnné prostředí

Spuštění ukázky:

**Aktivace z balíčku NuGet**

1. Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .
1. Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
1. Zkompilujte a spusťte aplikaci. Odkaz na balíček se nachází v rozšířené aplikaci (Referenční dokumentace při kompilaci). `<PropertyGroup>`V souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku. To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou balíčku `ServiceKeyInjection.Configure` .

Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti. K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :

```dotnetcli
dotnet nuget locals all --clear
```

**Aktivace z knihovny tříd**

1. Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .
1. Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
1. *bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary.dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.
1. Zkompilujte a spusťte aplikaci. `<ItemGroup>`V souboru projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou knihovny tříd `ServiceKeyInjection.Configure` .

**Aktivace z běhového úložiště – nasazené sestavení**

1. Projekt *StartupDiagnostics* pomocí [prostředí PowerShell](/powershell/scripting/powershell-scripting) upraví jeho *StartupDiagnostics.deps.jsv* souboru. PowerShell je ve výchozím nastavení nainstalován v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1. Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace různých verzí PowerShellu](/powershell/scripting/install/installing-powershell).
1. Spusťte skript *build.ps1* ve složce *RuntimeStore* . Skript:
   * Vygeneruje `StartupDiagnostics` balíček ve složce *obj\packages* .
   * Vygeneruje běhové úložiště pro `StartupDiagnostics` ve složce *Store* . `dotnet store`Příkaz ve skriptu používá `win7-x64` [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows. Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu. Běhové úložiště pro `StartupDiagnostics` by bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno. Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/StartupDiagnostics.dll*.
   * Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* . Další závislosti by se později přesunuly na další závislosti uživatele nebo systému. `StartupDiagnostics`Umístění instalace dalších závislostí uživatele je *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/2.2.0/StartupDiagnostics.deps.json*.
   * Umístí soubor *deploy.ps1* do složky pro *nasazení* .
1. Spusťte skript *deploy.ps1* ve složce pro *nasazení* . Skript připojí:
   * `StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
   * Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.
   * Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do `DOTNET_SHARED_STORE` proměnné prostředí.
1. Spusťte ukázkovou aplikaci.
1. Požádejte o `/services` koncový bod, aby se zobrazily registrované služby aplikace. Požádejte `/diag` koncový bod, aby zobrazil diagnostické informace.

::: moniker-end
