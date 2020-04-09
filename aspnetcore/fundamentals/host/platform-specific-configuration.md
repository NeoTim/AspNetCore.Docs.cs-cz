---
title: Použití hostitelských spouštěcích sestavení v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vylepšit aplikaci ASP.NET Core z externího sestavení pomocí implementace IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: ac667b0205f5daad395d86fbe129beb509a044a6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417624"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Použití hostitelských spouštěcích sestavení v ASP.NET Core

Podle [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostování při spuštění) přidává vylepšení aplikace při spuštění z externího sestavení. Externí knihovna může například použít implementaci hostitelského spuštění k poskytování dalších poskytovatelů konfigurace nebo služeb pro aplikaci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="hostingstartup-attribute"></a>Atribut HostingStartup

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského spouštěcího sestavení, které se aktivuje za běhu.

Sestavení položky nebo sestavení `Startup` obsahující třídu je automaticky `HostingStartup` zkontrolováno pro atribut. Seznam sestavení pro `HostingStartup` vyhledávání atributů je načten za běhu z konfigurace v [souboru WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Seznam sestavení, která mají být vyloučena z zjišťování, je načten z [webu WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).

V následujícím příkladu je `StartupEnhancement`obor názvů hostitelského spouštěcího sestavení . Třída obsahující kód hostitelského `StartupEnhancementHostingStartup`spuštění je :

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Atribut `HostingStartup` je obvykle umístěn v hostitelském `IHostingStartup` spouštěcím sestavení implementační ho souboru třídy.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Objevte načtená hostitelská spouštěcí sestavení

Chcete-li zjistit načtená sestavení po spuštění hostingu, povolte protokolování a zkontrolujte protokoly aplikace. Chyby, ke kterým dochází při načítání sestavení jsou protokolovány. Načtená hostitelská spouštěcí sestavení jsou protokolována na úrovni ladění a jsou protokolovány všechny chyby.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Zakázat automatické načítání hostitelských spouštěcích sestavení

Chcete-li zakázat automatické načítání hostitelských spouštěcích sestavení, použijte jeden z následujících přístupů:

* Chcete-li zabránit načítání všech hostitelských spouštěcích `true` sestavení, nastavte jednu z následujících možností na nebo `1`:

  * Zabránit nastavení konfigurace hostitele po spuštění:

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

  * `ASPNETCORE_PREVENTHOSTINGSTARTUP`proměnné prostředí.

* Chcete-li zabránit načítání konkrétních hostitelských spouštěcích sestavení, nastavte jednu z následujících na řetězec hostitelských spouštěcích sestavení oddělených středníkem, který chcete při spuštění vyloučit:

  * Hostování nastavení konfigurace hostitele sestavení spouštění:

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

  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`proměnné prostředí.

Pokud je nastaveno nastavení konfigurace hostitele i proměnná prostředí, řídí chování nastavení hostitele.

Zakázání hostitelských spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik charakteristik aplikace.

## <a name="project"></a>Project

Vytvořte hostitelské spuštění s jedním z následujících typů projektů:

* [Knihovna tříd](#class-library)
* [Konzolová aplikace bez vstupního bodu](#console-app-without-an-entry-point)

### <a name="class-library"></a>Knihovna tříd

Vylepšení spuštění hostingu lze poskytnout v knihovně tříd. Knihovna obsahuje `HostingStartup` atribut.

[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje aplikaci Razor Pages, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*. Knihovna tříd:

* Obsahuje třídu spuštění `ServiceKeyInjection`hostitele , `IHostingStartup`která implementuje . `ServiceKeyInjection`přidá dvojici řetězců služby do konfigurace aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu hostitelského spuštění.

Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> doplněk k přidání vylepšení do aplikace. `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Stránka Index aplikace čte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostitelským spouštěcím sestavením knihovny tříd:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) také obsahuje projekt balíčku NuGet, který poskytuje samostatné hostování *spuštění, HostingStartupPackage*. Balíček má stejné vlastnosti knihovny tříd popsaných výše. Balíček:

* Obsahuje třídu spuštění `ServiceKeyInjection`hostitele , `IHostingStartup`která implementuje . `ServiceKeyInjection`přidá do konfigurace aplikace dvojici řetězců služeb.
* Zahrnuje `HostingStartup` atribut.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Stránka Index aplikace čte a vykreslí hodnoty konfigurace pro dva klíče nastavené sestavením hostitelského spuštění balíčku:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konzolová aplikace bez vstupního bodu

*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli rozhraní .NET Framework.*

Dynamické vylepšení spuštění hostingu, které nevyžaduje odkaz na aktivaci v době kompilace, může být `HostingStartup` poskytnuto v konzolové aplikaci bez vstupního bodu, který obsahuje atribut. Publikování konzolové aplikace vytvoří hostitelské spouštěcí sestavení, které lze spotřebovat z runtime úložiště.

Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:

* Soubor závislostí je nutné využívat hostování spuštění v hostitelském spouštěcím sestavení. Soubor závislostí je spustitelný prostředek aplikace, který se vytváří publikováním aplikace, nikoli knihovny.
* Knihovnu nelze přidat přímo do [úložiště balíčků runtime](/dotnet/core/deploying/runtime-store), což vyžaduje spustitelný projekt, který cílí na sdílený runtime.

Při vytváření dynamického hostování spuštění:

* Hostitelské spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:
  * Zahrnuje třídu, `IHostingStartup` která obsahuje implementaci.
  * Zahrnuje [Atribut HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) k `IHostingStartup` identifikaci třídy implementace.
* Konzolová aplikace je publikována, aby získala závislosti spuštění hostitele. Důsledkem publikování konzolové aplikace je, že nepoužívané závislosti jsou oříznuty ze souboru závislostí.
* Soubor závislostí je upraven tak, aby nastavil umístění runtime hostitelského spouštěcího sestavení.
* Hostitelské spouštěcí sestavení a jeho soubor závislostí je umístěn do úložiště balíčků runtime. Chcete-li zjistit hostitelské spouštěcí sestavení a jeho soubor závislostí, jsou uvedeny v dvojici proměnných prostředí.

Konzolová aplikace odkazuje na balíček [Microsoft.AspNetCore.Hosting.Abstractions:](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/)

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako `IHostingStartup` implementaci pro načítání a <xref:Microsoft.AspNetCore.Hosting.IWebHost>provádění při vytváření . V následujícím příkladu je `StartupEnhancement`obor názvů a `StartupEnhancementHostingStartup`třída je :

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Třída implementuje `IHostingStartup`. <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> doplněk k přidání vylepšení do aplikace. `IHostingStartup.Configure`v hostiteli spouštěcí sestavení je volána runtime před `Startup.Configure` v uživatelském kódu, který umožňuje uživatelský kód přepsat libovolnou konfiguraci poskytovanou sestavení mnoství po spuštění hostitele.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Při vytváření `IHostingStartup` projektu nastaví soubor závislostí (*.deps.json*) `runtime` umístění sestavení do složky *přihrádky:*

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Zobrazí se pouze část souboru. Název sestavení v příkladu je `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfigurace poskytovaná spuštěním hostingu

Existují dva přístupy ke zpracování konfigurace v závislosti na tom, zda chcete, aby konfigurace hostitelského spuštění měla přednost nebo aby měla přednost konfigurace aplikace:

1. Poskytněte konfiguraci aplikaci pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> načtení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> konfigurace po spuštění delegátů aplikace. Hostování konfigurace spuštění má přednost před konfigurací aplikace pomocí tohoto přístupu.
1. Poskytněte konfiguraci aplikaci pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> načtení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> konfigurace před spuštěním delegátů aplikace. Hodnoty konfigurace aplikace mají přednost před hodnotami poskytovanými spuštěním hostování pomocí tohoto přístupu.

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

## <a name="specify-the-hosting-startup-assembly"></a>Určení hostitelského spouštěcího sestavení

Pro spuštění hostování dodané knihovnou nebo konzolovou aplikací zadejte název `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hostitelského spouštěcího sestavení v proměnné prostředí. Proměnná prostředí je seznam sestav oddělených středníkem.

Pro tento `HostingStartup` atribut jsou prohledány pouze hostitelské spouštěcí sestavení. Pro ukázkovou aplikaci *HostingStartupApp*, chcete-li zjistit hostitelské startupy popsané výše, proměnná prostředí je nastavena na následující hodnotu:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Hostitelské spouštěcí sestavení lze také nastavit pomocí nastavení konfigurace hostitele hostitelských sestavení hostování:

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

Pokud je k dispozici více <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> hostitelských sestavení po spuštění, jejich metody jsou provedeny v pořadí, ve které jsou uvedena sestavení.

## <a name="activation"></a>Aktivace

Možnosti pro aktivaci pospuštění hostingu jsou:

* [Aktivace runtime úložiště](#runtime-store) &ndash; nevyžaduje odkaz na dobu kompilace pro aktivaci. Ukázková aplikace umístí hostitelské spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spuštění v prostředí s více počítači. Složka *nasazení* také obsahuje skript prostředí PowerShell, který vytváří nebo upravuje proměnné prostředí v systému nasazení, aby bylo možné povolit hostování při spuštění.
* Odkaz na dobu kompilace potřebný pro aktivaci
  * [Balíček NuGet](#nuget-package)
  * [Složka přihrádky projektu](#project-bin-folder)

### <a name="runtime-store"></a>Runtime úložiště

Implementace spuštění hostitele je umístěna v [runtime úložišti](/dotnet/core/deploying/runtime-store). Odkaz na sestavení v době kompilace není vyžadován vylepšenou aplikací.

Po vytvoření hostitelského spuštění je generováno runtime úložiště pomocí souboru manifestu projektu a příkazu [dotnet store.](/dotnet/core/tools/dotnet-store)

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

V ukázkové aplikaci *(RuntimeStore* project) se používá následující příkaz:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Pro runtime ke zjištění runtime úložiště, umístění runtime úložiště `DOTNET_SHARED_STORE` je přidán do proměnné prostředí.

**Úprava a umístění souboru závislostí hostitelského spuštění**

Chcete-li aktivovat vylepšení bez odkazu na balíček vylepšení, `additionalDeps`zadejte další závislosti na běhu s . `additionalDeps`umožňuje:

* Rozšiřte graf knihovny aplikace poskytnutím sady dalších souborů *.deps.json,* které se při spuštění sloučí s vlastním souborem *.deps.json.*
* Nastavení hostitelského spouštěcího sestavení zjistitelné a načítatelné.

Doporučený mdla pro generování dalšího souboru závislostí je:

 1. Spusťte `dotnet publish` v souboru manifestu runtime store, na který odkazuje předchozí část.
 1. Odeberte odkaz na manifest `runtime` z knihoven a části výsledného souboru *.deps.json.*

V ukázkovém `store.manifest/1.0.0` projektu je vlastnost `targets` `libraries` odebrána z oddílu a:

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

Umístěte soubor *.deps.json* do následujícího umístění:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`&ndash; Umístění přidané `DOTNET_ADDITIONAL_DEPS` do proměnné prostředí.
* `{SHARED FRAMEWORK NAME}`&ndash; Pro tento další soubor závislostí je vyžadovánsdílený rámec.
* `{SHARED FRAMEWORK VERSION}`&ndash; Minimální verze sdílené ho rámce.
* `{ENHANCEMENT ASSEMBLY NAME}`&ndash; Název sestavení vylepšení.

V ukázkové aplikaci (projekt*RuntimeStore)* se další soubor závislostí umístí do následujícího umístění:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Pro runtime ke zjištění umístění úložiště runtime je do proměnné `DOTNET_ADDITIONAL_DEPS` prostředí přidáno další umístění souboru závislostí.

V ukázkové aplikaci *(runtimestore* projektu), vytváření runtime úložiště a generování další soubor závislostí se provádí pomocí skriptu [Prostředí PowerShell.](/powershell/scripting/powershell-scripting)

Příklady nastavení proměnných prostředí pro různé operační systémy naleznete v tématu [Použití více prostředí](xref:fundamentals/environments).

**Nasazení**

Pro usnadnění nasazení hostitelského spuštění v prostředí s více počítači vytvoří ukázková aplikace složku *nasazení* v publikovaném výstupu, která obsahuje:

* Hostitelské spouštěcí runtime úložiště.
* Hostitelský soubor závislostí při spuštění.
* Skript prostředí PowerShell, který vytváří `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`nebo `DOTNET_SHARED_STORE`upravuje `DOTNET_ADDITIONAL_DEPS` , a podporuje aktivaci hostitelského spuštění. Spusťte skript z příkazového řádku prostředí PowerShell pro správu v systému nasazení.

### <a name="nuget-package"></a>Balíček NuGet

Vylepšení spuštění hostingu může být k dispozici v balíčku NuGet. Balíček `HostingStartup` má atribut. Typy spuštění hostování poskytované balíčkem jsou k dispozici aplikaci pomocí některého z následujících přístupů:

* Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spuštění v souboru projektu aplikace (odkaz na kompilaci). S odkazem na dobu kompilace na místě, hostování spouštěcí sestavení a všechny jeho závislosti jsou začleněny do souboru závislostí aplikace (*.deps.json*). Tento přístup se vztahuje na balíček sestavení hostitelského spuštění publikovaného pro [nuget.org](https://www.nuget.org/).
* Soubor závislostí hostitelského spuštění je k dispozici rozšířené aplikaci, jak je popsáno v části [Runtime store](#runtime-store) (bez odkazu na kompilaci).

Další informace o balíčcích NuGet a runtime úložišti naleznete v následujících tématech:

* [Jak vytvořit balíček NuGet s nástroji křížové platformy](/dotnet/core/deploying/creating-nuget-packages)
* [Publikování balíčků](/nuget/create-packages/publish-a-package)
* [Úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Složka přihrádky projektu

Vylepšení spouštění hostingu může být poskytnuto sestavením nasazeným *přihrádkou*v rozšířené aplikaci. Typy spuštění hostování poskytované sestavením jsou k dispozici aplikaci pomocí jednoho z následujících přístupů:

* Soubor projektu rozšířené aplikace odkazuje na hostování spuštění (odkaz na kompilaci). S odkazem na dobu kompilace na místě, hostování spouštěcí sestavení a všechny jeho závislosti jsou začleněny do souboru závislostí aplikace (*.deps.json*). Tento přístup platí, když scénář nasazení volá pro vytvoření odkazu na sestavení hostitelského spuštění *(soubor dll)* a přesunutí sestavení buď:
  * Náročný projekt.
  * Umístění přístupné náročným projektem.
* Soubor závislostí hostitelského spuštění je k dispozici rozšířené aplikaci, jak je popsáno v části [Runtime store](#runtime-store) (bez odkazu na kompilaci).
* Při cílení na rozhraní .NET Framework je sestavení načítatelné ve výchozím kontextu zatížení, což v rozhraní .NET Framework znamená, že sestavení je umístěno v některém z následujících umístění:
  * Cesta &ndash; k základně aplikace Složka *přihrádky,* ve které je umístěn spustitelný soubor aplikace (*EXE*).
  * Globální mezipaměti sestavení &ndash; (GAC) GAC ukládá sestavení, které sdílejí několik aplikací rozhraní .NET Framework. Další informace naleznete v [tématu Postup: Instalace sestavení do globální mezipaměti sestavení](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) v dokumentaci rozhraní .NET Framework.

## <a name="sample-code"></a>Ukázka kódu

[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak stáhnout](xref:index#how-to-download-a-sample)) ukazuje hostování scénářů implementace spuštění:

* Dvě hostitelská spouštěcí sestavení (knihovny tříd) nastavují dvojici párů klíč-hodnota konfigurace v paměti:
  * Balíček NuGet (*HostingStartupPackage*)
  * Knihovna tříd (*HostingStartupLibrary*)
* Hostitelské spuštění je aktivováno z runtime store nasazeného sestavení (*StartupDiagnostics*). Sestavení přidá do aplikace při spuštění dva middlewares, které poskytují diagnostické informace o:
  * Registrované služby
  * Adresa (schéma, hostitel, základna cesty, cesta, řetězec dotazu)
  * Připojení (vzdálená IP adresa, vzdálený port, lokální IP, místní port, klientský certifikát)
  * Hlavičky požadavku
  * Proměnné prostředí

Spuštění ukázky:

**Aktivace z balíčku NuGet**

1. Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet pack.](/dotnet/core/tools/dotnet-pack)
1. Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
1. Zkompilujte a spusťte aplikaci. Odkaz na balíček je k dispozici v rozšířené aplikaci (odkaz na kompilaci). A `<PropertyGroup>` v souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku. To umožňuje aplikaci používat balíček bez nahrání balíčku do [nuget.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Všimněte si, že hodnoty konfiguračního klíče služby vykreslené na stránce Index odpovídají hodnotám nastaveným `ServiceKeyInjection.Configure` metodou balíčku.

Pokud provedete změny v projektu *HostingStartupPackage* a znovu jej zkompilovat, zrušte místní mezipaměti balíčku NuGet, abyste zajistili, že *HostingStartupApp* obdrží aktualizovaný balíček a ne zastaralý balíček z místní mezipaměti. Chcete-li vymazat místní mezipaměti NuGet, spusťte následující příkaz [místní chod dotnet nuget:](/dotnet/core/tools/dotnet-nuget-locals)

```dotnetcli
dotnet nuget locals all --clear
```

**Aktivace z knihovny tříd**

1. Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet build.](/dotnet/core/tools/dotnet-build)
1. Přidejte název sestavení knihovny tříd *hostingStartupLibrary* do proměnné `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` prostředí.
1. *bin*-nasadit sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary.dll* z kompilovaného výstupu knihovny tříd do složky *bin/Ladění* aplikace.
1. Zkompilujte a spusťte aplikaci. Soubor `<ItemGroup>` projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (odkaz na kompilaci). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Všimněte si, že hodnoty konfiguračního klíče služby vykreslené `ServiceKeyInjection.Configure` na stránce Index odpovídají hodnotám nastaveným metodou knihovny tříd.

**Aktivace z runtime store nasazeného sestavení**

1. Projekt *StartupDiagnostics* používá [prostředí PowerShell](/powershell/scripting/powershell-scripting) k úpravě souboru *StartupDiagnostics.deps.json.* Prostředí PowerShell je ve výchozím nastavení nainstalováno v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1. Informace o získání prostředí PowerShell na jiných platformách naleznete [v tématu Instalace různých verzí prostředí PowerShell](/powershell/scripting/install/installing-powershell).
1. Spusťte skript *build.ps1* ve složce *RuntimeStore.* Skript:
   * Generuje balíček ve `StartupDiagnostics` složce *obj\packages.*
   * Generuje runtime úložiště `StartupDiagnostics` pro ve složce *úložiště.* Příkaz `dotnet store` ve skriptu `win7-x64` používá [identifikátor runtime (RID)](/dotnet/core/rid-catalog) pro hostitelské spuštění nasazené v systému Windows. Při poskytování hostitelského spuštění pro jiný běhový čas nahraďte správný rid na řádku 37 skriptu. Runtime úložiště `StartupDiagnostics` pro by později přesunuta do úložiště runtime uživatele nebo systému v počítači, kde bude spotřebována sestavení. Umístění instalace úložiště runtime `StartupDiagnostics` uživatele pro sestavení je *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.
   * Generuje `additionalDeps` for `StartupDiagnostics` ve složce *additionalDeps.* Další závislosti by později byly přesunuty do dalších závislostí uživatele nebo systému. Umístění `StartupDiagnostics` instalace dalších závislostí uživatele *mj.*
   * Umístí soubor *deploy.ps1* do složky *nasazení.*
1. Spusťte skript *deploy.ps1* ve složce *nasazení.* Skript se připojí:
   * `StartupDiagnostics`k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
   * Cesta k hostitelským závislostem při spuštění (ve složce `DOTNET_ADDITIONAL_DEPS` *nasazení* projektu RuntimeStore) do proměnné prostředí.
   * Cesta runtime úložiště (ve složce *nasazení* projektu RuntimeStore) do proměnné `DOTNET_SHARED_STORE` prostředí.
1. Spusťte ukázkovou aplikaci.
1. Požádejte `/services` koncový bod, abyste viděli registrované služby aplikace. Požádejte `/diag` koncový bod, abyste viděli diagnostické informace.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hostování při spuštění) přidává vylepšení aplikace při spuštění z externího sestavení. Externí knihovna může například použít implementaci hostitelského spuštění k poskytování dalších poskytovatelů konfigurace nebo služeb pro aplikaci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="hostingstartup-attribute"></a>Atribut HostingStartup

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) označuje přítomnost hostitelského spouštěcího sestavení, které se aktivuje za běhu.

Sestavení položky nebo sestavení `Startup` obsahující třídu je automaticky `HostingStartup` zkontrolováno pro atribut. Seznam sestavení pro `HostingStartup` vyhledávání atributů je načten za běhu z konfigurace v [souboru WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Seznam sestavení, která mají být vyloučena z zjišťování, je načten z [webu WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey). Další informace naleznete v [tématu Web Host: Hostování spouštěcích sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [Web Host: Hostování sestavení vyloučení spuštění](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

V následujícím příkladu je `StartupEnhancement`obor názvů hostitelského spouštěcího sestavení . Třída obsahující kód hostitelského `StartupEnhancementHostingStartup`spuštění je :

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Atribut `HostingStartup` je obvykle umístěn v hostitelském `IHostingStartup` spouštěcím sestavení implementační ho souboru třídy.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Objevte načtená hostitelská spouštěcí sestavení

Chcete-li zjistit načtená sestavení po spuštění hostingu, povolte protokolování a zkontrolujte protokoly aplikace. Chyby, ke kterým dochází při načítání sestavení jsou protokolovány. Načtená hostitelská spouštěcí sestavení jsou protokolována na úrovni ladění a jsou protokolovány všechny chyby.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Zakázat automatické načítání hostitelských spouštěcích sestavení

Chcete-li zakázat automatické načítání hostitelských spouštěcích sestavení, použijte jeden z následujících přístupů:

* Chcete-li zabránit načítání všech hostitelských spouštěcích `true` sestavení, nastavte jednu z následujících možností na nebo `1`:
  * Zabránit nastavení konfigurace hostitele [po spuštění.](xref:fundamentals/host/web-host#prevent-hosting-startup)
  * `ASPNETCORE_PREVENTHOSTINGSTARTUP`proměnné prostředí.
* Chcete-li zabránit načítání konkrétních hostitelských spouštěcích sestavení, nastavte jednu z následujících na řetězec hostitelských spouštěcích sestavení oddělených středníkem, který chcete při spuštění vyloučit:
  * Hostování nastavení konfigurace hostitele [sestavení spouštění.](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)
  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`proměnné prostředí.

Pokud je nastaveno nastavení konfigurace hostitele i proměnná prostředí, řídí chování nastavení hostitele.

Zakázání hostitelských spouštěcích sestavení pomocí nastavení hostitele nebo proměnné prostředí zakáže sestavení globálně a může zakázat několik charakteristik aplikace.

## <a name="project"></a>Project

Vytvořte hostitelské spuštění s jedním z následujících typů projektů:

* [Knihovna tříd](#class-library)
* [Konzolová aplikace bez vstupního bodu](#console-app-without-an-entry-point)

### <a name="class-library"></a>Knihovna tříd

Vylepšení spuštění hostingu lze poskytnout v knihovně tříd. Knihovna obsahuje `HostingStartup` atribut.

[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) obsahuje aplikaci Razor Pages, *HostingStartupApp*a knihovnu tříd *HostingStartupLibrary*. Knihovna tříd:

* Obsahuje třídu spuštění `ServiceKeyInjection`hostitele , `IHostingStartup`která implementuje . `ServiceKeyInjection`přidá dvojici řetězců služby do konfigurace aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Obsahuje `HostingStartup` atribut, který identifikuje obor názvů a třídu hostitelského spuštění.

Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> doplněk k přidání vylepšení do aplikace. `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Stránka Index aplikace čte a vykreslí hodnoty konfigurace pro dva klíče nastavené hostitelským spouštěcím sestavením knihovny tříd:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) také obsahuje projekt balíčku NuGet, který poskytuje samostatné hostování *spuštění, HostingStartupPackage*. Balíček má stejné vlastnosti knihovny tříd popsaných výše. Balíček:

* Obsahuje třídu spuštění `ServiceKeyInjection`hostitele , `IHostingStartup`která implementuje . `ServiceKeyInjection`přidá do konfigurace aplikace dvojici řetězců služeb.
* Zahrnuje `HostingStartup` atribut.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Stránka Index aplikace čte a vykreslí hodnoty konfigurace pro dva klíče nastavené sestavením hostitelského spuštění balíčku:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konzolová aplikace bez vstupního bodu

*Tento přístup je k dispozici pouze pro aplikace .NET Core, nikoli rozhraní .NET Framework.*

Dynamické vylepšení spuštění hostingu, které nevyžaduje odkaz na aktivaci v době kompilace, může být `HostingStartup` poskytnuto v konzolové aplikaci bez vstupního bodu, který obsahuje atribut. Publikování konzolové aplikace vytvoří hostitelské spouštěcí sestavení, které lze spotřebovat z runtime úložiště.

Konzolová aplikace bez vstupního bodu se používá v tomto procesu, protože:

* Soubor závislostí je nutné využívat hostování spuštění v hostitelském spouštěcím sestavení. Soubor závislostí je spustitelný prostředek aplikace, který se vytváří publikováním aplikace, nikoli knihovny.
* Knihovnu nelze přidat přímo do [úložiště balíčků runtime](/dotnet/core/deploying/runtime-store), což vyžaduje spustitelný projekt, který cílí na sdílený runtime.

Při vytváření dynamického hostování spuštění:

* Hostitelské spouštěcí sestavení je vytvořeno z konzolové aplikace bez vstupního bodu, který:
  * Zahrnuje třídu, `IHostingStartup` která obsahuje implementaci.
  * Zahrnuje [Atribut HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) k `IHostingStartup` identifikaci třídy implementace.
* Konzolová aplikace je publikována, aby získala závislosti spuštění hostitele. Důsledkem publikování konzolové aplikace je, že nepoužívané závislosti jsou oříznuty ze souboru závislostí.
* Soubor závislostí je upraven tak, aby nastavil umístění runtime hostitelského spouštěcího sestavení.
* Hostitelské spouštěcí sestavení a jeho soubor závislostí je umístěn do úložiště balíčků runtime. Chcete-li zjistit hostitelské spouštěcí sestavení a jeho soubor závislostí, jsou uvedeny v dvojici proměnných prostředí.

Konzolová aplikace odkazuje na balíček [Microsoft.AspNetCore.Hosting.Abstractions:](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/)

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

Atribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifikuje třídu jako `IHostingStartup` implementaci pro načítání a <xref:Microsoft.AspNetCore.Hosting.IWebHost>provádění při vytváření . V následujícím příkladu je `StartupEnhancement`obor názvů a `StartupEnhancementHostingStartup`třída je :

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Třída implementuje `IHostingStartup`. <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda třídy používá <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> doplněk k přidání vylepšení do aplikace. `IHostingStartup.Configure`v hostiteli spouštěcí sestavení je volána runtime před `Startup.Configure` v uživatelském kódu, který umožňuje uživatelský kód přepsat libovolnou konfiguraci poskytovanou sestavení mnoství po spuštění hostitele.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Při vytváření `IHostingStartup` projektu nastaví soubor závislostí (*.deps.json*) `runtime` umístění sestavení do složky *přihrádky:*

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Zobrazí se pouze část souboru. Název sestavení v příkladu je `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfigurace poskytovaná spuštěním hostingu

Existují dva přístupy ke zpracování konfigurace v závislosti na tom, zda chcete, aby konfigurace hostitelského spuštění měla přednost nebo aby měla přednost konfigurace aplikace:

1. Poskytněte konfiguraci aplikaci pomocí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> načtení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> konfigurace po spuštění delegátů aplikace. Hostování konfigurace spuštění má přednost před konfigurací aplikace pomocí tohoto přístupu.
1. Poskytněte konfiguraci aplikaci pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> načtení <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> konfigurace před spuštěním delegátů aplikace. Hodnoty konfigurace aplikace mají přednost před hodnotami poskytovanými spuštěním hostování pomocí tohoto přístupu.

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

## <a name="specify-the-hosting-startup-assembly"></a>Určení hostitelského spouštěcího sestavení

Pro spuštění hostování dodané knihovnou nebo konzolovou aplikací zadejte název `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hostitelského spouštěcího sestavení v proměnné prostředí. Proměnná prostředí je seznam sestav oddělených středníkem.

Pro tento `HostingStartup` atribut jsou prohledány pouze hostitelské spouštěcí sestavení. Pro ukázkovou aplikaci *HostingStartupApp*, chcete-li zjistit hostitelské startupy popsané výše, proměnná prostředí je nastavena na následující hodnotu:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Hostitelské spouštěcí sestavení lze také nastavit pomocí nastavení konfigurace hostitele hostitelských sestavení [hostování.](xref:fundamentals/host/web-host#hosting-startup-assemblies)

Pokud je k dispozici více <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> hostitelských sestavení po spuštění, jejich metody jsou provedeny v pořadí, ve které jsou uvedena sestavení.

## <a name="activation"></a>Aktivace

Možnosti pro aktivaci pospuštění hostingu jsou:

* [Aktivace runtime úložiště](#runtime-store) &ndash; nevyžaduje odkaz na dobu kompilace pro aktivaci. Ukázková aplikace umístí hostitelské spouštěcí sestavení a soubory závislostí do složky, *nasazení*, aby se usnadnilo nasazení hostitelského spuštění v prostředí s více počítači. Složka *nasazení* také obsahuje skript prostředí PowerShell, který vytváří nebo upravuje proměnné prostředí v systému nasazení, aby bylo možné povolit hostování při spuštění.
* Odkaz na dobu kompilace potřebný pro aktivaci
  * [Balíček NuGet](#nuget-package)
  * [Složka přihrádky projektu](#project-bin-folder)

### <a name="runtime-store"></a>Runtime úložiště

Implementace spuštění hostitele je umístěna v [runtime úložišti](/dotnet/core/deploying/runtime-store). Odkaz na sestavení v době kompilace není vyžadován vylepšenou aplikací.

Po vytvoření hostitelského spuštění je generováno runtime úložiště pomocí souboru manifestu projektu a příkazu [dotnet store.](/dotnet/core/tools/dotnet-store)

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

V ukázkové aplikaci *(RuntimeStore* project) se používá následující příkaz:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Pro runtime ke zjištění runtime úložiště, umístění runtime úložiště `DOTNET_SHARED_STORE` je přidán do proměnné prostředí.

**Úprava a umístění souboru závislostí hostitelského spuštění**

Chcete-li aktivovat vylepšení bez odkazu na balíček vylepšení, `additionalDeps`zadejte další závislosti na běhu s . `additionalDeps`umožňuje:

* Rozšiřte graf knihovny aplikace poskytnutím sady dalších souborů *.deps.json,* které se při spuštění sloučí s vlastním souborem *.deps.json.*
* Nastavení hostitelského spouštěcího sestavení zjistitelné a načítatelné.

Doporučený mdla pro generování dalšího souboru závislostí je:

 1. Spusťte `dotnet publish` v souboru manifestu runtime store, na který odkazuje předchozí část.
 1. Odeberte odkaz na manifest `runtime` z knihoven a části výsledného souboru *.deps.json.*

V ukázkovém `store.manifest/1.0.0` projektu je vlastnost `targets` `libraries` odebrána z oddílu a:

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

Umístěte soubor *.deps.json* do následujícího umístění:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`&ndash; Umístění přidané `DOTNET_ADDITIONAL_DEPS` do proměnné prostředí.
* `{SHARED FRAMEWORK NAME}`&ndash; Pro tento další soubor závislostí je vyžadovánsdílený rámec.
* `{SHARED FRAMEWORK VERSION}`&ndash; Minimální verze sdílené ho rámce.
* `{ENHANCEMENT ASSEMBLY NAME}`&ndash; Název sestavení vylepšení.

V ukázkové aplikaci (projekt*RuntimeStore)* se další soubor závislostí umístí do následujícího umístění:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Pro runtime ke zjištění umístění úložiště runtime je do proměnné `DOTNET_ADDITIONAL_DEPS` prostředí přidáno další umístění souboru závislostí.

V ukázkové aplikaci *(runtimestore* projektu), vytváření runtime úložiště a generování další soubor závislostí se provádí pomocí skriptu [Prostředí PowerShell.](/powershell/scripting/powershell-scripting)

Příklady nastavení proměnných prostředí pro různé operační systémy naleznete v tématu [Použití více prostředí](xref:fundamentals/environments).

**Nasazení**

Pro usnadnění nasazení hostitelského spuštění v prostředí s více počítači vytvoří ukázková aplikace složku *nasazení* v publikovaném výstupu, která obsahuje:

* Hostitelské spouštěcí runtime úložiště.
* Hostitelský soubor závislostí při spuštění.
* Skript prostředí PowerShell, který vytváří `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`nebo `DOTNET_SHARED_STORE`upravuje `DOTNET_ADDITIONAL_DEPS` , a podporuje aktivaci hostitelského spuštění. Spusťte skript z příkazového řádku prostředí PowerShell pro správu v systému nasazení.

### <a name="nuget-package"></a>Balíček NuGet

Vylepšení spuštění hostingu může být k dispozici v balíčku NuGet. Balíček `HostingStartup` má atribut. Typy spuštění hostování poskytované balíčkem jsou k dispozici aplikaci pomocí některého z následujících přístupů:

* Soubor projektu rozšířené aplikace vytvoří odkaz na balíček pro hostování spuštění v souboru projektu aplikace (odkaz na kompilaci). S odkazem na dobu kompilace na místě, hostování spouštěcí sestavení a všechny jeho závislosti jsou začleněny do souboru závislostí aplikace (*.deps.json*). Tento přístup se vztahuje na balíček sestavení hostitelského spuštění publikovaného pro [nuget.org](https://www.nuget.org/).
* Soubor závislostí hostitelského spuštění je k dispozici rozšířené aplikaci, jak je popsáno v části [Runtime store](#runtime-store) (bez odkazu na kompilaci).

Další informace o balíčcích NuGet a runtime úložišti naleznete v následujících tématech:

* [Jak vytvořit balíček NuGet s nástroji křížové platformy](/dotnet/core/deploying/creating-nuget-packages)
* [Publikování balíčků](/nuget/create-packages/publish-a-package)
* [Úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Složka přihrádky projektu

Vylepšení spouštění hostingu může být poskytnuto sestavením nasazeným *přihrádkou*v rozšířené aplikaci. Typy spuštění hostování poskytované sestavením jsou k dispozici aplikaci pomocí jednoho z následujících přístupů:

* Soubor projektu rozšířené aplikace odkazuje na hostování spuštění (odkaz na kompilaci). S odkazem na dobu kompilace na místě, hostování spouštěcí sestavení a všechny jeho závislosti jsou začleněny do souboru závislostí aplikace (*.deps.json*). Tento přístup platí, když scénář nasazení volá pro vytvoření odkazu na sestavení hostitelského spuštění *(soubor dll)* a přesunutí sestavení buď:
  * Náročný projekt.
  * Umístění přístupné náročným projektem.
* Soubor závislostí hostitelského spuštění je k dispozici rozšířené aplikaci, jak je popsáno v části [Runtime store](#runtime-store) (bez odkazu na kompilaci).
* Při cílení na rozhraní .NET Framework je sestavení načítatelné ve výchozím kontextu zatížení, což v rozhraní .NET Framework znamená, že sestavení je umístěno v některém z následujících umístění:
  * Cesta &ndash; k základně aplikace Složka *přihrádky,* ve které je umístěn spustitelný soubor aplikace (*EXE*).
  * Globální mezipaměti sestavení &ndash; (GAC) GAC ukládá sestavení, které sdílejí několik aplikací rozhraní .NET Framework. Další informace naleznete v [tématu Postup: Instalace sestavení do globální mezipaměti sestavení](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) v dokumentaci rozhraní .NET Framework.

## <a name="sample-code"></a>Ukázka kódu

[Ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak stáhnout](xref:index#how-to-download-a-sample)) ukazuje hostování scénářů implementace spuštění:

* Dvě hostitelská spouštěcí sestavení (knihovny tříd) nastavují dvojici párů klíč-hodnota konfigurace v paměti:
  * Balíček NuGet (*HostingStartupPackage*)
  * Knihovna tříd (*HostingStartupLibrary*)
* Hostitelské spuštění je aktivováno z runtime store nasazeného sestavení (*StartupDiagnostics*). Sestavení přidá do aplikace při spuštění dva middlewares, které poskytují diagnostické informace o:
  * Registrované služby
  * Adresa (schéma, hostitel, základna cesty, cesta, řetězec dotazu)
  * Připojení (vzdálená IP adresa, vzdálený port, lokální IP, místní port, klientský certifikát)
  * Hlavičky požadavku
  * Proměnné prostředí

Spuštění ukázky:

**Aktivace z balíčku NuGet**

1. Zkompilujte balíček *HostingStartupPackage* pomocí příkazu [dotnet pack.](/dotnet/core/tools/dotnet-pack)
1. Přidejte název sestavení balíčku *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
1. Zkompilujte a spusťte aplikaci. Odkaz na balíček je k dispozici v rozšířené aplikaci (odkaz na kompilaci). A `<PropertyGroup>` v souboru projektu aplikace určuje výstup projektu balíčku (*.. /HostingStartupPackage/bin/Debug*) jako zdroj balíčku. To umožňuje aplikaci používat balíček bez nahrání balíčku do [nuget.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Všimněte si, že hodnoty konfiguračního klíče služby vykreslené na stránce Index odpovídají hodnotám nastaveným `ServiceKeyInjection.Configure` metodou balíčku.

Pokud provedete změny v projektu *HostingStartupPackage* a znovu jej zkompilovat, zrušte místní mezipaměti balíčku NuGet, abyste zajistili, že *HostingStartupApp* obdrží aktualizovaný balíček a ne zastaralý balíček z místní mezipaměti. Chcete-li vymazat místní mezipaměti NuGet, spusťte následující příkaz [místní chod dotnet nuget:](/dotnet/core/tools/dotnet-nuget-locals)

```dotnetcli
dotnet nuget locals all --clear
```

**Aktivace z knihovny tříd**

1. Zkompilujte knihovnu tříd *HostingStartupLibrary* pomocí příkazu [dotnet build.](/dotnet/core/tools/dotnet-build)
1. Přidejte název sestavení knihovny tříd *hostingStartupLibrary* do proměnné `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` prostředí.
1. *bin*-nasadit sestavení knihovny tříd do aplikace zkopírováním souboru *HostingStartupLibrary.dll* z kompilovaného výstupu knihovny tříd do složky *bin/Ladění* aplikace.
1. Zkompilujte a spusťte aplikaci. Soubor `<ItemGroup>` projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (odkaz na kompilaci). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Všimněte si, že hodnoty konfiguračního klíče služby vykreslené `ServiceKeyInjection.Configure` na stránce Index odpovídají hodnotám nastaveným metodou knihovny tříd.

**Aktivace z runtime store nasazeného sestavení**

1. Projekt *StartupDiagnostics* používá [prostředí PowerShell](/powershell/scripting/powershell-scripting) k úpravě souboru *StartupDiagnostics.deps.json.* Prostředí PowerShell je ve výchozím nastavení nainstalováno v systému Windows počínaje systémy Windows 7 SP1 a Windows Server 2008 R2 SP1. Informace o získání prostředí PowerShell na jiných platformách naleznete [v tématu Instalace různých verzí prostředí PowerShell](/powershell/scripting/install/installing-powershell).
1. Spusťte skript *build.ps1* ve složce *RuntimeStore.* Skript:
   * Generuje balíček ve `StartupDiagnostics` složce *obj\packages.*
   * Generuje runtime úložiště `StartupDiagnostics` pro ve složce *úložiště.* Příkaz `dotnet store` ve skriptu `win7-x64` používá [identifikátor runtime (RID)](/dotnet/core/rid-catalog) pro hostitelské spuštění nasazené v systému Windows. Při poskytování hostitelského spuštění pro jiný běhový čas nahraďte správný rid na řádku 37 skriptu. Runtime úložiště `StartupDiagnostics` pro by později přesunuta do úložiště runtime uživatele nebo systému v počítači, kde bude spotřebována sestavení. Umístění instalace úložiště runtime `StartupDiagnostics` uživatele pro sestavení je *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.
   * Generuje `additionalDeps` for `StartupDiagnostics` ve složce *additionalDeps.* Další závislosti by později byly přesunuty do dalších závislostí uživatele nebo systému. Umístění `StartupDiagnostics` instalace dalších závislostí uživatele *mj.*
   * Umístí soubor *deploy.ps1* do složky *nasazení.*
1. Spusťte skript *deploy.ps1* ve složce *nasazení.* Skript se připojí:
   * `StartupDiagnostics`k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.
   * Cesta k hostitelským závislostem při spuštění (ve složce `DOTNET_ADDITIONAL_DEPS` *nasazení* projektu RuntimeStore) do proměnné prostředí.
   * Cesta runtime úložiště (ve složce *nasazení* projektu RuntimeStore) do proměnné `DOTNET_SHARED_STORE` prostředí.
1. Spusťte ukázkovou aplikaci.
1. Požádejte `/services` koncový bod, abyste viděli registrované služby aplikace. Požádejte `/diag` koncový bod, abyste viděli diagnostické informace.

::: moniker-end
