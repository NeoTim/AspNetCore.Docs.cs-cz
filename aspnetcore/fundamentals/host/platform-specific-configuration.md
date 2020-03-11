---
title: Použití hostování při spuštění sestavení v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak rozšířit aplikace ASP.NET Core z externího sestavení přes IHostingStartup implementace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 71fd5cf1934b5374e0a393e055db23b98c03b62f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660396"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Použití hostování při spuštění sestavení v ASP.NET Core

Od [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení. Například externí knihovnu můžete hostování implementace spuštění uvést další konfigurace zprostředkovatele nebo služby do aplikace.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Atribut HostingStartup

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.

Sestavení záznamu nebo sestavení obsahující `Startup` třídy je automaticky prohledáno pro atribut `HostingStartup`. Seznam sestavení pro hledání atributů `HostingStartup` je načtena za běhu z konfigurace v [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).

V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement`. Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Atribut `HostingStartup` se obvykle nachází v souboru `IHostingStartup` implementační třídy sestavení, který spouští.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Zjistit načtená hostingu při spuštění sestavení

Ke zřízení načtená hostingu při spuštění sestavení, povolte protokolování a v protokolech aplikace. Jsou zaznamenány chyby, ke kterým dochází při načítání sestavení. Načtená hostingu při spuštění sestavení jsou zaznamenány na úrovni ladění a jsou zaznamenány všechny chyby.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Vypnout automatické načítání hostování při spuštění sestavení

Vypnout automatické načítání hostování při spuštění sestavení, použijte jednu z následujících postupů:

* Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1`:

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

  * `ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnnou prostředí.

* Pokud chcete zabránit konkrétní hostingu při spuštění sestavení z načítání, nastavte na řetězec oddělený středníkem hostování při spuštění sestavení mají vyloučit při spuštění jednu z následujících:

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

  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` proměnnou prostředí.

Pokud nastavení konfigurace hostitele a proměnné prostředí jsou nastaveny, nastavení hostitele řídí chování.

Zakázání hostingu při spuštění sestavení pomocí proměnná hostitele nastavení nebo prostředí globálně zakáže sestavení a může zakázat několik vlastností aplikace.

## <a name="project"></a>Project

Vytvoření hostitelského spouštěcího s jedním z následujících typů projektu:

* [Knihovna tříd](#class-library)
* [Konzolová aplikace bez vstupního bodu](#console-app-without-an-entry-point)

### <a name="class-library"></a>Knihovna tříd

Hostování rozšíření spuštění lze zadat v knihovně tříd. Knihovna obsahuje atribut `HostingStartup`.

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor Pages App, *HostingStartupApp*a knihovny tříd *HostingStartupLibrary*. Knihovna tříd:

* Obsahuje hostující spouštěcí třídu `ServiceKeyInjection`, která implementuje `IHostingStartup`. `ServiceKeyInjection` do konfigurace aplikace přidá dvojici řetězců služeb pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Zahrnuje atribut `HostingStartup`, který identifikuje obor názvů a třídu pro spuštění hostingu.

Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> `ServiceKeyInjection` třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení do aplikace.

*HostingStartupLibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče, nastavit podle knihovny tříd hostingu při spuštění sestavení:

*HostingStartupApp/pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*. Balíček má stejné charakteristiky knihovna tříd je popsáno výše. Balíček:

* Obsahuje hostující spouštěcí třídu `ServiceKeyInjection`, která implementuje `IHostingStartup`. `ServiceKeyInjection` do konfigurace aplikace přidá dvojici řetězců služby.
* Zahrnuje atribut `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče nastavil hostování sestavení po spuštění balíčku:

*HostingStartupApp/pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konzolová aplikace bez vstupního bodu

*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*

Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje atribut `HostingStartup`. Publikování aplikace konzoly vytváří hostingu při spuštění sestavení, které mohou být spotřebovány z úložiště modulu runtime.

Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:

* Soubor závislostí je potřeba využívat hostování spouštění v hostitelském sestavení po spuštění. Soubor závislostí je spustitelné aplikace majetek, který je vytvořen a publikovat aplikace, ne z knihovny.
* Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.

Při vytváření dynamické hostování spuštění:

* Hostování sestavení po spuštění je vytvořený z konzoly aplikace bez vstupní bod, který:
  * Obsahuje třídu, která obsahuje implementaci `IHostingStartup`.
  * Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) , který identifikuje třídu implementace `IHostingStartup`.
* Publikování aplikace konzoly získat závislosti spuštění hostování. V důsledku publikování aplikace konzoly je, že se ze souboru závislosti oříznut nepoužité závislé součásti.
* Změně souboru závislosti modulu runtime pozici hostingu při spuštění sestavení.
* Hostování při spuštění sestavení a jeho závislostí souboru se umístí do úložiště balíčků modulu runtime. Ke zjištění hostingu při spuštění sestavení a jeho závislostí souboru, jsou uvedeny v pár proměnných prostředí.

Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost>. V následujícím příkladu je obor názvů `StartupEnhancement`a třída je `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Třída implementuje `IHostingStartup`. Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidávání vylepšení aplikace. `IHostingStartup.Configure` v hostitelském sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat libovolnou konfiguraci poskytnutou hostujícím sestavením po spuštění.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Při sestavování projektu `IHostingStartup`, soubor závislosti ( *. DEPS. JSON*) nastaví `runtime` umístění sestavení do složky *bin* :

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Je zobrazena pouze část souboru. Název sestavení v příkladu je `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfigurace poskytovaná spuštěním hostování

Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:

1. Poskytněte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pro načtení konfigurace po provedení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegátů aplikace. Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.
1. Zadejte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pro načtení konfigurace před spuštěním delegátů <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikace. Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.

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

## <a name="specify-the-hosting-startup-assembly"></a>Zadejte hostitelské sestavení po spuštění

V případě knihovny tříd nebo konzolové aplikace dodané jako hostitele zadejte název hostitelského sestavení pro spuštění v proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. Proměnná prostředí je středníkem oddělený seznam sestavení.

Pro atribut `HostingStartup` je prohledávána pouze spouštěcí sestavení hostingu. Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:

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

* [Běhové úložiště](#runtime-store) &ndash; aktivace nevyžaduje pro aktivaci odkaz na čas kompilace. Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači. Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.
* Vyžadováno pro aktivaci odkazu za kompilace
  * [Balíček NuGet](#nuget-package)
  * [Složka Bin projektu](#project-bin-folder)

### <a name="runtime-store"></a>Úložiště modulu runtime

Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store). Kompilace odkaz na sestavení není vyžadováno vylepšené aplikace.

Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do proměnné prostředí `DOTNET_SHARED_STORE`.

**Upravit a umístit soubor závislostí se spouštěním hostitele**

Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps`. `additionalDeps` vám umožní:

* Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších souborů *. DEPS. JSON* pro sloučení s vlastním souborem *. DEPS. JSON* aplikace při spuštění.
* Ujistěte se, hostování sestavení po spuštění zjistitelné a načíst.

Doporučený postup pro vygenerování Další závislosti souboru je:

 1. Spusťte `dotnet publish` v souboru manifestu běhového úložiště, na který se odkazuje v předchozí části.
 1. Odeberte odkaz na manifest z knihoven a část `runtime` výsledného souboru *. DEPS. JSON* .

V příkladu projektu je vlastnost `store.manifest/1.0.0` odebrána z `targets` a `libraries` části:

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

Soubor *. DEPS. JSON* umístěte do následujícího umístění:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}` &ndash; umístění přidané do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.
* pro tento soubor dalších závislostí se vyžaduje `{SHARED FRAMEWORK NAME}` &ndash; sdílené rozhraní.
* `{SHARED FRAMEWORK VERSION}` &ndash; minimální verzi sdíleného rozhraní .NET Framework.
* `{ENHANCEMENT ASSEMBLY NAME}` &ndash; název sestavení rozšíření.

V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí `DOTNET_ADDITIONAL_DEPS` přidáno umístění souborů další závislosti.

V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .

Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).

**Nasazení**

Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:

* Hostování úložiště při spuštění modulu runtime.
* Hostování souboru závislostí při spuštění.
* Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění. Spusťte skript z příkazového řádku pro správu prostředí PowerShell v systému pro nasazení.

### <a name="nuget-package"></a>Balíček NuGet

Hostování rozšíření spuštění lze zadat balíček NuGet. Balíček má atribut `HostingStartup`. Hostování typům spouštění poskytovaný balíček jsou k dispozici do aplikace pomocí jedné z následujících postupů:

* Soubor projektu vylepšené aplikace je odkaz na balíček pro hostování při spuštění v souboru projektu vaší aplikace (kompilace odkaz). V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*). Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).
* K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).

Další informace o balíčcích NuGet a úložiště modulu runtime naleznete v následujících tématech:

* [Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy](/dotnet/core/deploying/creating-nuget-packages)
* [Publikování balíčků](/nuget/create-packages/publish-a-package)
* [Úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Složky bin projektu

Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci. Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:

* Soubor projektu vylepšené aplikace je odkaz na sestavení pro spouštění hostování (kompilace odkaz). V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*). Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:
  * Projekt, který je k disnáročné.
  * Umístění přístupné pro náročný projekt.
* K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).
* Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:
  * Základní cesta aplikace &ndash; složku *bin* , kde se nachází spustitelný soubor aplikace ( *. exe*).
  * Globální mezipaměť sestavení (GAC) &ndash; GAC ukládá sestavení, která sdílí několik .NET Frameworkch aplikací. Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.

## <a name="sample-code"></a>Ukázka kódu

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:

* Dvě hostování při spuštění sestavení (knihoven tříd) nastavit pár konfigurace v paměti páry klíč hodnota každé:
  * Balíček NuGet (*HostingStartupPackage*)
  * Knihovna tříd (*HostingStartupLibrary*)
* Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu. Sestavení přidá do aplikace při spuštění obsahující diagnostické informace o dvou middlewares:
  * Registrované služby
  * Adresa (schéma, hostitele, základ cesty, cesta, řetězec dotazu)
  * Připojení (vzdálenou IP adresu, vzdálený port, místní IP adresu, místní port, klientského certifikátu)
  * Hlavičky požadavku
  * Proměnné prostředí

Ke spuštění ukázky:

**Aktivace z balíčku NuGet**

1. Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .
1. Přidejte název sestavení balíčku *HostingStartupPackage* do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Kompilace a spuštění aplikace. Odkaz na balíček je k dispozici vylepšené aplikace (kompilace odkaz). `<PropertyGroup>` v souboru projektu aplikace určuje výstup projektu balíčku ( *.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku. To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným `ServiceKeyInjection.Configure` metodou balíčku.

Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti. K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :

```dotnetcli
dotnet nuget locals all --clear
```

**Aktivace z knihovny tříd**

1. Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .
1. Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. *bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary. dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.
1. Kompilace a spuštění aplikace. `<ItemGroup>` v souboru projektu aplikace odkazuje na sestavení knihovny tříd ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou `ServiceKeyInjection.Configure` knihovny tříd.

**Aktivace z běhového úložiště – nasazené sestavení**

1. Projekt *StartupDiagnostics* používá k úpravě souboru *StartupDiagnostics. DEPS. JSON* [PowerShell](/powershell/scripting/powershell-scripting) . Prostředí PowerShell se instaluje standardně na Windows od verze Windows 7 SP1 a Windows Server 2008 R2 SP1. Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace prostředí Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).
1. Spusťte skript *Build. ps1* ve složce *RuntimeStore* . Skript:
   * Vygeneruje balíček `StartupDiagnostics` ve složce *obj\packages* .
   * Generuje úložiště modulu runtime pro `StartupDiagnostics` ve složce *Store* . Příkaz `dotnet store` ve skriptu používá [identifikátor modulu runtime `win7-x64` (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows. Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu. Běhové úložiště pro `StartupDiagnostics` bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno. Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/startupdiagnostics. dll*.
   * Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* . Další závislosti by se později přesunuly na další závislosti uživatele nebo systému. Uživatel `StartupDiagnostics` další závislosti umístění instalace je *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/3.0.0/StartupDiagnostics. DEPS. JSON*.
   * Umístí soubor *Deploy. ps1* do složky pro *nasazení* .
1. Spusťte skript *Deploy. ps1* ve složce pro *nasazení* . Skript připojí:
   * `StartupDiagnostics` do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.
   * Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_SHARED_STORE`.
1. Spuštění ukázkové aplikace.
1. Požádejte o `/services` koncový bod pro zobrazení registrovaných služeb aplikace. Vyžádejte `/diag` koncový bod, aby se zobrazily diagnostické informace.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostující spuštění) přidává vylepšení aplikace při spuštění z externího sestavení. Například externí knihovnu můžete hostování implementace spuštění uvést další konfigurace zprostředkovatele nebo služby do aplikace.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Atribut HostingStartup

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského sestavení po spuštění, které se aktivuje za běhu.

Sestavení záznamu nebo sestavení obsahující `Startup` třídy je automaticky prohledáno pro atribut `HostingStartup`. Seznam sestavení pro hledání atributů `HostingStartup` je načtena za běhu z konfigurace v [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Seznam sestavení pro vyloučení ze zjišťování je načten z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey). Další informace najdete v tématu [webový hostitel: hostování spouštěcích sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [webového hostitele: hostování spouštěcích vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

V následujícím příkladu je obor názvů hostujícího spouštěcího sestavení `StartupEnhancement`. Třída obsahující spouštěcí kód hostování je `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Atribut `HostingStartup` se obvykle nachází v souboru `IHostingStartup` implementační třídy sestavení, který spouští.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Zjistit načtená hostingu při spuštění sestavení

Ke zřízení načtená hostingu při spuštění sestavení, povolte protokolování a v protokolech aplikace. Jsou zaznamenány chyby, ke kterým dochází při načítání sestavení. Načtená hostingu při spuštění sestavení jsou zaznamenány na úrovni ladění a jsou zaznamenány všechny chyby.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Vypnout automatické načítání hostování při spuštění sestavení

Vypnout automatické načítání hostování při spuštění sestavení, použijte jednu z následujících postupů:

* Chcete-li zabránit načtení všech spouštěcích sestavení hostování, nastavte jednu z následujících možností na `true` nebo `1`:
  * Zabrání hostování nastavení konfigurace hostitele [po spuštění](xref:fundamentals/host/web-host#prevent-hosting-startup) .
  * `ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnnou prostředí.
* Pokud chcete zabránit konkrétní hostingu při spuštění sestavení z načítání, nastavte na řetězec oddělený středníkem hostování při spuštění sestavení mají vyloučit při spuštění jednu z následujících:
  * Hostování nastavení konfigurace hostitele [vyloučení sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)
  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` proměnnou prostředí.

Pokud nastavení konfigurace hostitele a proměnné prostředí jsou nastaveny, nastavení hostitele řídí chování.

Zakázání hostingu při spuštění sestavení pomocí proměnná hostitele nastavení nebo prostředí globálně zakáže sestavení a může zakázat několik vlastností aplikace.

## <a name="project"></a>Project

Vytvoření hostitelského spouštěcího s jedním z následujících typů projektu:

* [Knihovna tříd](#class-library)
* [Konzolová aplikace bez vstupního bodu](#console-app-without-an-entry-point)

### <a name="class-library"></a>Knihovna tříd

Hostování rozšíření spuštění lze zadat v knihovně tříd. Knihovna obsahuje atribut `HostingStartup`.

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje Razor Pages App, *HostingStartupApp*a knihovny tříd *HostingStartupLibrary*. Knihovna tříd:

* Obsahuje hostující spouštěcí třídu `ServiceKeyInjection`, která implementuje `IHostingStartup`. `ServiceKeyInjection` do konfigurace aplikace přidá dvojici řetězců služeb pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Zahrnuje atribut `HostingStartup`, který identifikuje obor názvů a třídu pro spuštění hostingu.

Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> `ServiceKeyInjection` třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidání vylepšení do aplikace.

*HostingStartupLibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče, nastavit podle knihovny tříd hostingu při spuštění sestavení:

*HostingStartupApp/pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje také projekt balíčku NuGet, který poskytuje samostatné hostování spouštěné *HostingStartupPackage*. Balíček má stejné charakteristiky knihovna tříd je popsáno výše. Balíček:

* Obsahuje hostující spouštěcí třídu `ServiceKeyInjection`, která implementuje `IHostingStartup`. `ServiceKeyInjection` do konfigurace aplikace přidá dvojici řetězců služby.
* Zahrnuje atribut `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče nastavil hostování sestavení po spuštění balíčku:

*HostingStartupApp/pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konzolová aplikace bez vstupního bodu

*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli .NET Framework.*

Dynamické rozšíření spouštění, které nevyžaduje odkazování v době kompilace pro aktivaci, lze poskytnout v konzolové aplikaci bez vstupního bodu, který obsahuje atribut `HostingStartup`. Publikování aplikace konzoly vytváří hostingu při spuštění sestavení, které mohou být spotřebovány z úložiště modulu runtime.

Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:

* Soubor závislostí je potřeba využívat hostování spouštění v hostitelském sestavení po spuštění. Soubor závislostí je spustitelné aplikace majetek, který je vytvořen a publikovat aplikace, ne z knihovny.
* Knihovnu nelze přidat přímo do [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje projekt spustitelný, který cílí na sdílený modul runtime.

Při vytváření dynamické hostování spuštění:

* Hostování sestavení po spuštění je vytvořený z konzoly aplikace bez vstupní bod, který:
  * Obsahuje třídu, která obsahuje implementaci `IHostingStartup`.
  * Obsahuje atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) , který identifikuje třídu implementace `IHostingStartup`.
* Publikování aplikace konzoly získat závislosti spuštění hostování. V důsledku publikování aplikace konzoly je, že se ze souboru závislosti oříznut nepoužité závislé součásti.
* Změně souboru závislosti modulu runtime pozici hostingu při spuštění sestavení.
* Hostování při spuštění sestavení a jeho závislostí souboru se umístí do úložiště balíčků modulu runtime. Ke zjištění hostingu při spuštění sestavení a jeho závislostí souboru, jsou uvedeny v pár proměnných prostředí.

Aplikace konzoly odkazuje na balíček [Microsoft. AspNetCore. hostinging. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako implementaci `IHostingStartup` pro načítání a spouštění při sestavování <xref:Microsoft.AspNetCore.Hosting.IWebHost>. V následujícím příkladu je obor názvů `StartupEnhancement`a třída je `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Třída implementuje `IHostingStartup`. Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> k přidávání vylepšení aplikace. `IHostingStartup.Configure` v hostitelském sestavení, které je voláno modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat libovolnou konfiguraci poskytnutou hostujícím sestavením po spuštění.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Při sestavování projektu `IHostingStartup`, soubor závislosti ( *. DEPS. JSON*) nastaví `runtime` umístění sestavení do složky *bin* :

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Je zobrazena pouze část souboru. Název sestavení v příkladu je `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfigurace poskytovaná spuštěním hostování

Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:

1. Poskytněte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pro načtení konfigurace po provedení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegátů aplikace. Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.
1. Zadejte konfiguraci do aplikace pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pro načtení konfigurace před spuštěním delegátů <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikace. Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.

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

## <a name="specify-the-hosting-startup-assembly"></a>Zadejte hostitelské sestavení po spuštění

V případě knihovny tříd nebo konzolové aplikace dodané jako hostitele zadejte název hostitelského sestavení pro spuštění v proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. Proměnná prostředí je středníkem oddělený seznam sestavení.

Pro atribut `HostingStartup` je prohledávána pouze spouštěcí sestavení hostingu. Pro ukázkovou aplikaci *HostingStartupApp*, aby bylo možné zjistit, jak jsou popsány v předchozí části, je proměnná prostředí nastavena na následující hodnotu:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Hostování spouštěcího sestavení lze také nastavit pomocí nastavení konfigurace hostitele [po spouštěcích sestaveních](xref:fundamentals/host/web-host#hosting-startup-assemblies) .

Pokud je přítomno více sestavení hostování po spuštění, jejich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody jsou spouštěny v pořadí, ve kterém jsou uvedena.

## <a name="activation"></a>Aktivace

Možnosti pro hostování aktivace po spuštění jsou:

* [Běhové úložiště](#runtime-store) &ndash; aktivace nevyžaduje pro aktivaci odkaz na čas kompilace. Ukázková aplikace umístí hostující spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spouštění v prostředí s více počítači. Složka pro *nasazení* obsahuje také skript PowerShellu, který vytvoří nebo upraví proměnné prostředí v systému nasazení, aby bylo možné hostující spuštění.
* Vyžadováno pro aktivaci odkazu za kompilace
  * [Balíček NuGet](#nuget-package)
  * [Složka Bin projektu](#project-bin-folder)

### <a name="runtime-store"></a>Úložiště modulu runtime

Hostitelská implementace spuštění je umístěna v [úložišti modulu runtime](/dotnet/core/deploying/runtime-store). Kompilace odkaz na sestavení není vyžadováno vylepšené aplikace.

Po sestavení hostitelského spuštění je úložiště modulu runtime vygenerováno pomocí souboru manifestu projektu a příkazu [dotnet Store](/dotnet/core/tools/dotnet-store) .

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

V ukázkové aplikaci (projekt*RuntimeStore* ) se používá následující příkaz:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Aby modul runtime zjistil běhové úložiště, je umístění úložiště modulu runtime přidáno do proměnné prostředí `DOTNET_SHARED_STORE`.

**Upravit a umístit soubor závislostí se spouštěním hostitele**

Chcete-li aktivovat vylepšení bez odkazu na balíček na vylepšení, určete další závislosti modulu runtime pomocí `additionalDeps`. `additionalDeps` vám umožní:

* Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších souborů *. DEPS. JSON* pro sloučení s vlastním souborem *. DEPS. JSON* aplikace při spuštění.
* Ujistěte se, hostování sestavení po spuštění zjistitelné a načíst.

Doporučený postup pro vygenerování Další závislosti souboru je:

 1. Spusťte `dotnet publish` v souboru manifestu běhového úložiště, na který se odkazuje v předchozí části.
 1. Odeberte odkaz na manifest z knihoven a část `runtime` výsledného souboru *. DEPS. JSON* .

V příkladu projektu je vlastnost `store.manifest/1.0.0` odebrána z `targets` a `libraries` části:

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

Soubor *. DEPS. JSON* umístěte do následujícího umístění:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}` &ndash; umístění přidané do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.
* pro tento soubor dalších závislostí se vyžaduje `{SHARED FRAMEWORK NAME}` &ndash; sdílené rozhraní.
* `{SHARED FRAMEWORK VERSION}` &ndash; minimální verzi sdíleného rozhraní .NET Framework.
* `{ENHANCEMENT ASSEMBLY NAME}` &ndash; název sestavení rozšíření.

V ukázkové aplikaci (projekt*RuntimeStore* ) se další soubor závislosti umístí do následujícího umístění:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Aby modul runtime zjistil umístění úložiště za běhu, bylo do proměnné prostředí `DOTNET_ADDITIONAL_DEPS` přidáno umístění souborů další závislosti.

V ukázkové aplikaci (projekt*RuntimeStore* ) sestavíte běhové úložiště a vygenerujete další soubor závislosti pomocí skriptu [PowerShellu](/powershell/scripting/powershell-scripting) .

Příklady nastavení proměnných prostředí pro různé operační systémy najdete v tématu [použití více prostředí](xref:fundamentals/environments).

**Nasazení**

Aby se usnadnilo nasazení hostujícího spouštění v prostředí s více počítači, vytvoří ukázková aplikace složku pro *nasazení* v publikovaném výstupu, která obsahuje:

* Hostování úložiště při spuštění modulu runtime.
* Hostování souboru závislostí při spuštění.
* Skript PowerShellu, který vytvoří nebo upraví `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`a `DOTNET_ADDITIONAL_DEPS` pro podporu aktivace hostitelského spuštění. Spusťte skript z příkazového řádku pro správu prostředí PowerShell v systému pro nasazení.

### <a name="nuget-package"></a>Balíček NuGet

Hostování rozšíření spuštění lze zadat balíček NuGet. Balíček má atribut `HostingStartup`. Hostování typům spouštění poskytovaný balíček jsou k dispozici do aplikace pomocí jedné z následujících postupů:

* Soubor projektu vylepšené aplikace je odkaz na balíček pro hostování při spuštění v souboru projektu vaší aplikace (kompilace odkaz). V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*). Tento přístup se vztahuje na hostující hostitelský balíček pro sestavení, který je publikovaný na [NuGet.org](https://www.nuget.org/).
* K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).

Další informace o balíčcích NuGet a úložiště modulu runtime naleznete v následujících tématech:

* [Postup vytvoření balíčku NuGet pomocí nástrojů pro různé platformy](/dotnet/core/deploying/creating-nuget-packages)
* [Publikování balíčků](/nuget/create-packages/publish-a-package)
* [Úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Složky bin projektu

Rozšíření spouštění hostitele může být k dispozici sestavením nasazeným *přihrádkou*v rozšířené aplikaci. Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:

* Soubor projektu vylepšené aplikace je odkaz na sestavení pro spouštění hostování (kompilace odkaz). V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*). Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:
  * Projekt, který je k disnáročné.
  * Umístění přístupné pro náročný projekt.
* K dispozici je soubor závislostí při spuštění hostující aplikaci, jak je popsáno v části [úložiště za běhu](#runtime-store) (bez referenčních informací v době kompilace).
* Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:
  * Základní cesta aplikace &ndash; složku *bin* , kde se nachází spustitelný soubor aplikace ( *. exe*).
  * Globální mezipaměť sestavení (GAC) &ndash; GAC ukládá sestavení, která sdílí několik .NET Frameworkch aplikací. Další informace naleznete v tématu [Postupy: Instalace sestavení do globální mezipaměti sestavení (](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) GAC) v dokumentaci k .NET Framework.

## <a name="sample-code"></a>Ukázka kódu

[Vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) znázorňuje hostování scénářů implementace spouštění:

* Dvě hostování při spuštění sestavení (knihoven tříd) nastavit pár konfigurace v paměti páry klíč hodnota každé:
  * Balíček NuGet (*HostingStartupPackage*)
  * Knihovna tříd (*HostingStartupLibrary*)
* Hostující spuštění je aktivováno ze sestavení s nasazeným úložištěm (*StartupDiagnostics*) za běhu. Sestavení přidá do aplikace při spuštění obsahující diagnostické informace o dvou middlewares:
  * Registrované služby
  * Adresa (schéma, hostitele, základ cesty, cesta, řetězec dotazu)
  * Připojení (vzdálenou IP adresu, vzdálený port, místní IP adresu, místní port, klientského certifikátu)
  * Hlavičky požadavku
  * Proměnné prostředí

Ke spuštění ukázky:

**Aktivace z balíčku NuGet**

1. Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) .
1. Přidejte název sestavení balíčku *HostingStartupPackage* do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Kompilace a spuštění aplikace. Odkaz na balíček je k dispozici vylepšené aplikace (kompilace odkaz). `<PropertyGroup>` v souboru projektu aplikace určuje výstup projektu balíčku ( *.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku. To umožňuje aplikaci používat balíček bez nahrání balíčku do [NuGet.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným `ServiceKeyInjection.Configure` metodou balíčku.

Pokud provedete změny v projektu *HostingStartupPackage* a znovu zkompilujete, vymažte místní mezipaměti balíčků NuGet, abyste zajistili, že *HostingStartupApp* přijme aktualizovaný balíček a nejedná se o zastaralý balíček z místní mezipaměti. K vymazání místních mezipamětí NuGet spusťte následující příkaz [dotnet NuGet Locals](/dotnet/core/tools/dotnet-nuget-locals) :

```dotnetcli
dotnet nuget locals all --clear
```

**Aktivace z knihovny tříd**

1. Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) .
1. Přidejte název sestavení knihovny tříd *HostingStartupLibrary* do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. *bin*– nasazení sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary. dll* z zkompilovaného výstupu knihovny tříd do složky *bin nebo ladění* aplikace.
1. Kompilace a spuštění aplikace. `<ItemGroup>` v souboru projektu aplikace odkazuje na sestavení knihovny tříd ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (Referenční dokumentace při kompilaci). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Pozor, aby hodnoty konfiguračního klíče služby vygenerované stránkou indexu odpovídaly hodnotám nastaveným metodou `ServiceKeyInjection.Configure` knihovny tříd.

**Aktivace z běhového úložiště – nasazené sestavení**

1. Projekt *StartupDiagnostics* používá k úpravě souboru *StartupDiagnostics. DEPS. JSON* [PowerShell](/powershell/scripting/powershell-scripting) . Prostředí PowerShell se instaluje standardně na Windows od verze Windows 7 SP1 a Windows Server 2008 R2 SP1. Pokud chcete získat PowerShell na jiných platformách, přečtěte si téma [instalace prostředí Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).
1. Spusťte skript *Build. ps1* ve složce *RuntimeStore* . Skript:
   * Vygeneruje balíček `StartupDiagnostics` ve složce *obj\packages* .
   * Generuje úložiště modulu runtime pro `StartupDiagnostics` ve složce *Store* . Příkaz `dotnet store` ve skriptu používá [identifikátor modulu runtime `win7-x64` (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows. Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu. Běhové úložiště pro `StartupDiagnostics` bylo později přesunuto do úložiště modulu runtime uživatele nebo systému v počítači, kde bude sestavení spotřebováno. Umístění instalace úložiště modulu runtime uživatele pro `StartupDiagnostics` sestavení je *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/startupdiagnostics. dll*.
   * Vygeneruje `additionalDeps` pro `StartupDiagnostics` ve složce *additionalDeps* . Další závislosti by se později přesunuly na další závislosti uživatele nebo systému. Uživatel `StartupDiagnostics` další závislosti umístění instalace je *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/2.2.0/StartupDiagnostics. DEPS. JSON*.
   * Umístí soubor *Deploy. ps1* do složky pro *nasazení* .
1. Spusťte skript *Deploy. ps1* ve složce pro *nasazení* . Skript připojí:
   * `StartupDiagnostics` do proměnné prostředí `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Cesta ke spouštěcím závislostem hostitele (ve složce *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_ADDITIONAL_DEPS`.
   * Cesta k úložišti za běhu (ve složce pro *nasazení* projektu RuntimeStore) do proměnné prostředí `DOTNET_SHARED_STORE`.
1. Spuštění ukázkové aplikace.
1. Požádejte o `/services` koncový bod pro zobrazení registrovaných služeb aplikace. Vyžádejte `/diag` koncový bod, aby se zobrazily diagnostické informace.

::: moniker-end
