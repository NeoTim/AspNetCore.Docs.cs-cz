---
title: Použití hostování při spuštění sestavení v ASP.NET Core
author: guardrex
description: Zjistěte, jak rozšířit aplikace ASP.NET Core z externího sestavení přes IHostingStartup implementace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/02/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: f20f8c4e9b1089acca6689d14cdf84bef648d461
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081760"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Použití hostování při spuštění sestavení v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex) a [Pavel Krymets](https://github.com/pakrym)

[IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (který je hostitelem spouštěcí) implementace vylepšení přidá do aplikace při spuštění z externího sestavení. Například externí knihovnu můžete hostování implementace spuštění uvést další konfigurace zprostředkovatele nebo služby do aplikace.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Atribut HostingStartup

A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) atribut indikuje přítomnost hostování sestavení po spuštění k aktivaci za běhu.

Vstupní sestavení nebo sestavení obsahující `Startup` třídy automaticky vyhledávat `HostingStartup` atribut. Seznam sestavení, které chcete hledat `HostingStartup` atributy je načtená v době běhu z konfigurace v [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey). Načíst seznam sestavení, které chcete vyloučit ze zjišťování z [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey). Další informace najdete v tématu [webový hostitel: Hostování spouštěcích](xref:fundamentals/host/web-host#hosting-startup-assemblies) sestavení [a webového hostitele: Hostování sestavení](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)vyloučení při spuštění.

V následujícím příkladu, obor názvů hostování sestavení po spuštění je `StartupEnhancement`. Třída obsahující kód hostování spuštění je `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup` Atribut se obvykle nachází v hostitelském sestavení po spuštění `IHostingStartup` souboru implementace třídy.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Zjistit načtená hostingu při spuštění sestavení

Ke zřízení načtená hostingu při spuštění sestavení, povolte protokolování a v protokolech aplikace. Jsou zaznamenány chyby, ke kterým dochází při načítání sestavení. Načtená hostingu při spuštění sestavení jsou zaznamenány na úrovni ladění a jsou zaznamenány všechny chyby.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Vypnout automatické načítání hostování při spuštění sestavení

Vypnout automatické načítání hostování při spuštění sestavení, použijte jednu z následujících postupů:

* Abyste zabránili všechna sestavení po spuštění hostování načítání, nastavte jednu z následujících způsobů `true` nebo `1`:
  * [Zabránit spuštění hostování](xref:fundamentals/host/web-host#prevent-hosting-startup) hostovat nastavení konfigurace.
  * `ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnné prostředí.
* Pokud chcete zabránit konkrétní hostingu při spuštění sestavení z načítání, nastavte na řetězec oddělený středníkem hostování při spuštění sestavení mají vyloučit při spuštění jednu z následujících:
  * [Hostování sestavení vyloučit při spuštění](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) hostovat nastavení konfigurace.
  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` proměnné prostředí.

Pokud nastavení konfigurace hostitele a proměnné prostředí jsou nastaveny, nastavení hostitele řídí chování.

Zakázání hostingu při spuštění sestavení pomocí proměnná hostitele nastavení nebo prostředí globálně zakáže sestavení a může zakázat několik vlastností aplikace.

## <a name="project"></a>Projekt

Vytvoření hostitelského spouštěcího s jedním z následujících typů projektu:

* [Knihovna tříd](#class-library)
* [Konzolová aplikace bez vstupního bodu](#console-app-without-an-entry-point)

### <a name="class-library"></a>Knihovna tříd

Hostování rozšíření spuštění lze zadat v knihovně tříd. Knihovna obsahuje `HostingStartup` atribut.

[Ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zahrnuje aplikace stránky Razor, *HostingStartupApp*a knihovny tříd, *HostingStartupLibrary*. Knihovna tříd:

* Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`. `ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).
* Zahrnuje `HostingStartup` atribut, který identifikuje obor názvů a třídy spuštění hostování.

`ServiceKeyInjection` Třídy [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) metoda používá [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) přidání vylepšení do aplikace.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče, nastavit podle knihovny tříd hostingu při spuštění sestavení:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) také zahrnuje projektu balíček NuGet, který poskytuje samostatné hostitelské spuštění *HostingStartupPackage*. Balíček má stejné charakteristiky knihovna tříd je popsáno výše. Balíček:

* Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`. `ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace.
* Zahrnuje `HostingStartup` atribut.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče nastavil hostování sestavení po spuštění balíčku:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konzolová aplikace bez vstupního bodu

*Tento přístup je dostupná jenom pro aplikace .NET Core, není rozhraní .NET Framework.*

Dynamické vylepšení spuštění hostování, který nevyžaduje odkaz kompilace pro aktivaci lze zadat v konzolové aplikaci bez vstupního bodu, který obsahuje `HostingStartup` atribut. Publikování aplikace konzoly vytváří hostingu při spuštění sestavení, které mohou být spotřebovány z úložiště modulu runtime.

Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:

* Soubor závislostí je potřeba využívat hostování spouštění v hostitelském sestavení po spuštění. Soubor závislostí je spustitelné aplikace majetek, který je vytvořen a publikovat aplikace, ne z knihovny.
* Knihovny nelze přímo přidat [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje spustitelný projekt, který cílí na sdílený modul runtime.

Při vytváření dynamické hostování spuštění:

* Hostování sestavení po spuštění je vytvořený z konzoly aplikace bez vstupní bod, který:
  * Obsahuje třídu, která obsahuje `IHostingStartup` implementace.
  * Zahrnuje [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) atribut k identifikaci `IHostingStartup` implementace třídy.
* Publikování aplikace konzoly získat závislosti spuštění hostování. V důsledku publikování aplikace konzoly je, že se ze souboru závislosti oříznut nepoužité závislé součásti.
* Změně souboru závislosti modulu runtime pozici hostingu při spuštění sestavení.
* Hostování při spuštění sestavení a jeho závislostí souboru se umístí do úložiště balíčků modulu runtime. Ke zjištění hostingu při spuštění sestavení a jeho závislostí souboru, jsou uvedeny v pár proměnných prostředí.

Odkazy na aplikace konzoly [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) balíčku:

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) atribut určuje třídu jako implementace `IHostingStartup` pro spuštění při vytváření a načítání [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost). V následujícím příkladu je obor názvů `StartupEnhancement`, a je třída `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Třída implementuje `IHostingStartup`. Třídy [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) metoda používá [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) přidání vylepšení do aplikace. `IHostingStartup.Configure` v hostitelských spuštění sestavení je volána modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat žádnou konfiguraci poskytované hostujícím sestavení při spuštění uživatelského kódu.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Při sestavování `IHostingStartup` projektu, soubor závislosti ( *. DEPS. JSON*) nastaví `runtime` umístění sestavení do složky *bin* :

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Je zobrazena pouze část souboru. Název sestavení v příkladu je `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfigurace poskytovaná spuštěním hostování

Existují dva přístupy ke zpracování konfigurace v závislosti na tom, jestli chcete, aby měla konfigurace hostujícího spuštění přednost, nebo jestli má přednost konfigurace aplikace:

1. Poskytněte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> , aby se konfigurace po spuštění <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegátů aplikace načetla. Hostování spouštěcí konfigurace má přednost před konfigurací aplikace pomocí tohoto přístupu.
1. Zadejte konfiguraci do aplikace pomocí nástroje <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> , aby se konfigurace načetla předtím <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> , než se spustí Delegáti aplikace. Hodnoty konfigurace aplikace mají přednost před metodami poskytovanými při spuštění hostování pomocí tohoto přístupu.

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
                    "From IHostingStartup: Higher priority than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Zadejte hostitelské sestavení po spuštění

Pro třídy knihovny - nebo konzoly aplikace poskytované hostitelem spuštění, zadejte název hostingu při spuštění sestavení `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí. Proměnná prostředí je středníkem oddělený seznam sestavení.

Vyhledávají pouze na hostitelských při spuštění sestavení `HostingStartup` atribut. Ukázkové aplikace *HostingStartupApp*, ke zjištění hostování startupy je popsáno výše, je proměnná prostředí je nastavena na následující hodnotu:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Hostování sestavení po spuštění můžete nastavit také pomocí [hostování při spuštění sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) hostovat nastavení konfigurace.

Při hostování více spuštění sestaví jsou k dispozici, jejich [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) metody jsou provedeny v pořadí, že sestavení jsou uvedeny.

## <a name="activation"></a>Aktivace

Možnosti pro hostování aktivace po spuštění jsou:

* [Úložiště modulu runtime](#runtime-store) &ndash; aktivace nevyžaduje, aby kompilace odkaz pro aktivaci. Ukázková aplikace umístí hostování sestavení po spuštění a soubory závislosti do složky, *nasazení*, které usnadní nasazování hostitelských při spuštění v prostředí multimachine. *Nasazení* složka obsahuje také skript Powershellu, který vytvoří nebo změní proměnné prostředí pro nasazení systému k povolení spuštění hostování.
* Vyžadováno pro aktivaci odkazu za kompilace
  * [Balíček NuGet](#nuget-package)
  * [Složky bin projektu](#project-bin-folder)

### <a name="runtime-store"></a>Úložiště modulu runtime

Hostování implementace spuštění je umístěn v [úložiště modulu runtime](/dotnet/core/deploying/runtime-store). Kompilace odkaz na sestavení není vyžadováno vylepšené aplikace.

Po spuštění hostování sestavení, modulu runtime úložiště je generována pomocí souboru manifestu projektu a [dotnet Restore](/dotnet/core/tools/dotnet-store) příkazu.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

V ukázkové aplikaci (*RuntimeStore* project) pomocí následujícího příkazu:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Pro modul runtime ke zjišťování úložiště modulu runtime se umístění úložiště modulu runtime přidá do `DOTNET_SHARED_STORE` proměnné prostředí.

**Upravit a umístěte soubor závislosti spuštění hostování**

K aktivaci vylepšení bez odkazu na balíček ke zvýšení, zadejte další závislosti modulu runtime s `additionalDeps`. `additionalDeps` umožňuje:

* Rozšíří graf knihovny aplikace tím, že poskytuje sadu dalších souborů *. DEPS. JSON* pro sloučení s vlastním souborem *. DEPS. JSON* aplikace při spuštění.
* Ujistěte se, hostování sestavení po spuštění zjistitelné a načíst.

Doporučený postup pro vygenerování Další závislosti souboru je:

 1. Spustit `dotnet publish` v souboru manifestu úložiště runtime odkazované v předchozí části.
 1. Odeberte odkaz na manifest z knihoven a `runtime` část výsledného souboru *. DEPS. JSON* .

Ve vzorovém projektu `store.manifest/1.0.0` vlastnost je odebrána z `targets` a `libraries` části:

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

* `{ADDITIONAL DEPENDENCIES PATH}` &ndash; Přidat do umístění `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.
* `{SHARED FRAMEWORK NAME}` &ndash; Sdílené framework vyžaduje pro tento soubor Další závislosti.
* `{SHARED FRAMEWORK VERSION}` &ndash; Minimální sdílené verze rozhraní.
* `{ENHANCEMENT ASSEMBLY NAME}` &ndash; Název sestavení vylepšení.

V ukázkové aplikaci (*RuntimeStore* projektu), další závislosti soubor se umístí do následujícího umístění:

```
additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Pro modul runtime ke zjištění umístění úložiště modulu runtime, je do umístění souboru Další závislosti `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.

V ukázkové aplikaci (*RuntimeStore* projektu), vytváření úložiště modulu runtime a další závislosti, soubor se provádí pomocí generování [Powershellu](/powershell/scripting/powershell-scripting) skriptu.

Příklady toho, jak nastavit proměnné prostředí pro různé operační systémy, najdete v článku [používání více prostředí](xref:fundamentals/environments).

**Nasazení**

Pro usnadnění nasazení hostování při spuštění v prostředí multimachine, vytvoří ukázkovou aplikaci *nasazení* složky v publikované výstup, který obsahuje:

* Hostování úložiště při spuštění modulu runtime.
* Hostování souboru závislostí při spuštění.
* Skript prostředí PowerShell, který vytvoří nebo změní `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, a `DOTNET_ADDITIONAL_DEPS` kvůli podpoře aktivace spuštění hostování. Spusťte skript z příkazového řádku pro správu prostředí PowerShell v systému pro nasazení.

### <a name="nuget-package"></a>Balíček NuGet

Hostování rozšíření spuštění lze zadat balíček NuGet. Balíček nemá `HostingStartup` atribut. Hostování typům spouštění poskytovaný balíček jsou k dispozici do aplikace pomocí jedné z následujících postupů:

* Soubor projektu vylepšené aplikace je odkaz na balíček pro hostování při spuštění v souboru projektu vaší aplikace (kompilace odkaz). V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*). Vztahuje se na hostování balíček po spuštění sestavení publikovaná [nuget.org](https://www.nuget.org/).
* Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).

Další informace o balíčcích NuGet a úložiště modulu runtime naleznete v následujících tématech:

* [Vytvoření balíčku NuGet pomocí nástrojů pro různé platformy](/dotnet/core/deploying/creating-nuget-packages)
* [Publikování balíčků](/nuget/create-packages/publish-a-package)
* [Úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Složky bin projektu

Hostování rozšíření po spuštění můžete zadat *bin*– nasazení sestavení do vylepšené aplikace. Typy spuštění hostování poskytované sestavením jsou zpřístupněny pro aplikaci pomocí jednoho z následujících přístupů:

* Soubor projektu vylepšené aplikace je odkaz na sestavení pro spouštění hostování (kompilace odkaz). V případě referenčního odkazu v době kompilace jsou hostující sestavení po spuštění a všechny jeho závislosti začleněny do souboru závislostí aplikace ( *. DEPS. JSON*). Tento přístup se používá v případě, že scénář nasazení volá pro vytvoření reference v čase kompilace na sestavení hostujícího spuštění sestavení (soubor *. dll* ) a přesunutí sestavení na jednu z těchto možností:
  * Projekt, který je k disnáročné.
  * Umístění přístupné pro náročný projekt.
* Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).
* Při cílení na .NET Framework sestavení je spustitelný ve výchozím kontextu načtení, což na .NET Framework znamená, že sestavení je umístěno v jednom z následujících umístění:
  * Základní cesta &ndash; aplikace složka *bin* , kde se nachází spustitelný soubor aplikace ( *. exe*).
  * Globální mezipaměť sestavení (GAC) &ndash; GAC ukládá sestavení, která sdílí několik aplikací .NET Framework. Další informace najdete v tématu [jak: Nainstalujte sestavení do globální mezipaměti](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) sestavení (GAC) v dokumentaci k .NET Framework.

## <a name="sample-code"></a>Ukázka kódu

[Ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:index#how-to-download-a-sample)) ukazuje implementaci scénáře hostingu po spuštění:

* Dvě hostování při spuštění sestavení (knihoven tříd) nastavit pár konfigurace v paměti páry klíč hodnota každé:
  * Balíček NuGet (*HostingStartupPackage*)
  * Knihovna tříd (*HostingStartupLibrary*)
* Hostování spuštění je aktivováno z úložiště nasazení sestavení modulu runtime (*StartupDiagnostics*). Sestavení přidá do aplikace při spuštění obsahující diagnostické informace o dvou middlewares:
  * Registrované služby
  * Adresa (schéma, hostitele, základ cesty, cesta, řetězec dotazu)
  * Připojení (vzdálenou IP adresu, vzdálený port, místní IP adresu, místní port, klientského certifikátu)
  * Hlavičky požadavku
  * Proměnné prostředí

Ke spuštění ukázky:

**Aktivace z balíčku NuGet**

1. Kompilace *HostingStartupPackage* balíček s [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkazu.
1. Přidat název sestavení daného balíčku *HostingStartupPackage* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
1. Kompilace a spuštění aplikace. Odkaz na balíček je k dispozici vylepšené aplikace (kompilace odkaz). A `<PropertyGroup>` v projektu aplikace soubor Určuje výstup projektu balíček ( *... / HostingStartupPackage/bin/Debug*) jako zdroj balíčků. To umožňuje aplikacím používat balíček bez nahrává se balíček, který má [nuget.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku shodovat s hodnotami nastavenými balíček `ServiceKeyInjection.Configure` metody.

Pokud provedete změny *HostingStartupPackage* projektu a ji znovu zkompilovat, zrušte zaškrtnutí políčka místní mezipaměti balíčku NuGet a zkontrolujte, že *HostingStartupApp* obdrží aktualizovaný balíček a nejsou zastaralé balíček z místní mezipaměti. Pokud chcete vymazat místní mezipaměť NuGet, spusťte následující [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) příkaz:

```dotnetcli
dotnet nuget locals all --clear
```

**Aktivace z knihovny tříd**

1. Kompilace *HostingStartupLibrary* knihovny tříd pomocí [dotnet sestavení](/dotnet/core/tools/dotnet-build) příkazu.
1. Přidat název sestavení knihovny tříd *HostingStartupLibrary* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
1. *bin*– sestavení knihovny tříd aplikace nasadíte tak, že zkopírujete *HostingStartupLibrary.dll* soubor z knihovny tříd zkompilován výstup do aplikace *bin/Debug* složky.
1. Kompilace a spuštění aplikace. `<ItemGroup>` Soubor v projektu aplikace odkazuje na sestavení knihovny tříd ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (referenční dokumentace kompilace). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku odpovídat hodnoty nastavené v knihovně tříd `ServiceKeyInjection.Configure` metody.

**Aktivace z úložiště nasazení sestavení modulu runtime**

1. *StartupDiagnostics* používá projekt [PowerShell](/powershell/scripting/powershell-scripting) k úpravě jeho *StartupDiagnostics.deps.json* souboru. Prostředí PowerShell se instaluje standardně na Windows od verze Windows 7 SP1 a Windows Server 2008 R2 SP1. Získat PowerShell na jiných platformách, naleznete v tématu [instalace prostředí Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).
1. Spusťte skript *Build. ps1* ve složce *RuntimeStore* . Skript:
   * Vygeneruje `StartupDiagnostics` balíček.
   * Vygeneruje běhové úložiště `StartupDiagnostics` pro ve složce *Store* . Příkaz ve skriptu `win7-x64` používá [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog) pro hostování po spuštění nasazené do systému Windows. `dotnet store` Při poskytování hostitelského spuštění pro jiný modul runtime nahraďte správný identifikátor RID na řádku 37 skriptu.
   * Vygeneruje `StartupDiagnostics`prove složce *additionalDeps/Shared/Microsoft. AspNetCore. app/{Shared Framework verze}/.* `additionalDeps`
   * Umístí soubor *Deploy. ps1* do složky pro *nasazení* .
1. Spusťte skript *Deploy. ps1* ve složce pro *nasazení* . Skript připojí:
   * `StartupDiagnostics`do proměnné `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` prostředí.
   * Cesta ke spouštěcím závislostem hostitele `DOTNET_ADDITIONAL_DEPS` pro proměnnou prostředí.
   * Cesta k běhovému úložišti pro `DOTNET_SHARED_STORE` proměnnou prostředí.
1. Spuštění ukázkové aplikace.
1. Žádosti `/services` koncový bod můžete zobrazit aplikace registrované služby. Žádosti `/diag` koncový bod můžete zobrazit diagnostické informace.
