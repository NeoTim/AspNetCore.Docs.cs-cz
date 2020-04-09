---
title: Migrace z ASP.NET do ASP.NET jádra
author: isaac2004
description: Získejte pokyny pro migraci existujících aplikací ASP.NET MVC nebo Web API do ASP.NET Core.web
ms.author: scaddie
ms.date: 10/18/2019
uid: migration/proper-to-2x/index
ms.openlocfilehash: 68a45dc50e00bead564500a12509b62a4a193ec4
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511083"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>Migrace z ASP.NET do ASP.NET jádra

Podle [Isaac Levin](https://isaaclevin.com)

Tento článek slouží jako referenční příručka pro migraci ASP.NET aplikací, které mají ASP.NET Core.

## <a name="prerequisites"></a>Požadavky

[Sada .NET Core SDK 2.2 nebo novější](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>Cílové architektury

ASP.NET Základní projekty nabízejí vývojářům flexibilitu cílení .NET Core, .NET Framework nebo obojí. Viz [Výběr mezi rozhraním .NET Core a rozhraní MN .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) a zjistěte, který cílový rámec je nejvhodnější.

Při cílení na rozhraní .NET Framework musí projekty odkazovat na jednotlivé balíčky NuGet.

Cílení .NET Core umožňuje eliminovat četné explicitní odkazy na balíčky díky [metabalíčku](xref:fundamentals/metapackage-app)ASP.NET Core . Nainstalujte `Microsoft.AspNetCore.App` metabalíček do projektu:

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

Při použití metabalíčku nejsou s aplikací nasazeny žádné balíčky odkazované v metabalíčku. Úložiště .NET Core Runtime Store obsahuje tyto prostředky a jsou předkompilovány ke zlepšení výkonu. Další podrobnosti najdete [v metabalíčku Microsoft.AspNetCore.App pro ASP.NET Core.](xref:fundamentals/metapackage-app)

## <a name="project-structure-differences"></a>Rozdíly ve struktuře projektu

Formát souboru *.csproj* byl zjednodušen v ASP.NET Core. Některé významné změny zahrnují:

- Explicitní zahrnutí souborů není nutné, aby byly považovány za součást projektu. To snižuje riziko konfliktů sloučení XML při práci s velkými týmy.
- Neexistují žádné odkazy založené na identifikátoru GUID na jiné projekty, což zlepšuje čitelnost souborů.
- Soubor lze upravit bez jeho uvolnění v sadě Visual Studio:

    ![Upravit možnost kontextové nabídky CSPROJ v Sadě Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Nahrazení souboru Global.asax

ASP.NET Core představil nový mechanismus pro zavádění aplikace. Vstupním bodem pro ASP.NET aplikace je soubor *Global.asax.* Úkoly, jako je konfigurace postupu a registrace filtrů a oblastí, jsou zpracovány v souboru *Global.asax.*

[!code-csharp[](samples/globalasax-sample.cs)]

Tento přístup spáruje aplikaci a server, na který je nasazena způsobem, který narušuje implementaci. Ve snaze oddělit, [OWIN](https://owin.org/) byl zaveden poskytnout čistší způsob, jak používat více rámců dohromady. OWIN poskytuje kanál pro přidání pouze potřebných modulů. Hostitelské prostředí používá funkci [po spuštění](xref:fundamentals/startup) ke konfiguraci služeb a kanálu požadavků aplikace. `Startup`zaregistruje sadu middleware s aplikací. Pro každý požadavek aplikace volá každou součást middleware s ukazatelem hlavy propojeného seznamu na existující sadu obslužných rutin. Každá součást middlewaru může do kanálu zpracování požadavků přidat jednu nebo více obslužných rutin. Toho lze dosáhnout vrácením odkazu na obslužnou rutinu, která je novou hlavou seznamu. Každá obslužná rutina je zodpovědná za zapamatování a vyvolání další obslužné rutiny v seznamu. S ASP.NET jádra, vstupní bod `Startup`do aplikace je a již nemáte závislost na *Global.asax*. Při použití OWIN s rozhraním .NET Framework použijte jako kanál něco jako následující:

[!code-csharp[](samples/webapi-owin.cs)]

Tím nakonfigurujete výchozí trasy a výchozí xmlserializace přes Json. Podle potřeby přidejte do tohoto kanálu další middleware (načtení služeb, nastavení konfigurace, statické soubory atd.).

ASP.NET Core používá podobný přístup, ale nespoléhá se na OWIN pro zpracování položky. Místo toho, to se provádí prostřednictvím *metody* `Main` Program.cs `Startup` (podobně jako konzolové aplikace) a je načten přes tam.

[!code-csharp[](samples/program.cs)]

`Startup`musí obsahovat metodu. `Configure` V `Configure`, přidejte potřebné middleware do potrubí. V následujícím příkladu (z výchozí šablony webu) metody rozšíření konfigurují kanál s podporou:

- Chybové stránky
- HTTP Přísné zabezpečení přenosu
- Přesměrování HTTP na protokol HTTPS
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

Hostitel a aplikace byly odděleny, což poskytuje flexibilitu přechodu na jinou platformu v budoucnu.

> [!NOTE]
> Podrobnější odkaz na ASP.NET základního startupu a middlewaru najdete [v tématu Startup in ASP.NET Core](xref:fundamentals/startup)

## <a name="store-configurations"></a>Konfigurace úložiště

ASP.NET podporuje nastavení ukládání. Tato nastavení se používají například pro podporu prostředí, do kterého byly aplikace nasazeny. Běžnou praxí bylo ukládat všechny vlastní dvojice `<appSettings>` klíč-hodnota v části souboru *Web.config:*

[!code-xml[](samples/webconfig-sample.xml)]

Aplikace číst tato `ConfigurationManager.AppSettings` nastavení pomocí `System.Configuration` kolekce v oboru názvů:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core může ukládat konfigurační data pro aplikaci do libovolného souboru a načíst je jako součást middleware bootstrapping. Výchozí soubor použitý v šablonách projektu je *appsettings.json*:

[!code-json[](samples/appsettings-sample.json)]

Načítání tohoto souboru `IConfiguration` do instance uvnitř aplikace se provádí v *Startup.cs*:

[!code-csharp[](samples/startup-builder.cs)]

Aplikace čte `Configuration` z získat nastavení:

[!code-csharp[](samples/read-appsettings.cs)]

Existují rozšíření tohoto přístupu, aby byl proces robustnější, například pomocí [vkládání závislostí](xref:fundamentals/dependency-injection) (DI) k načtení služby s těmito hodnotami. Přístup DI poskytuje sadu objektů konfigurace silného typu.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> Podrobnější odkaz na konfiguraci ASP.NET jádra naleznete [v tématu Konfigurace v ASP.NET jádra](xref:fundamentals/configuration/index).

## <a name="native-dependency-injection"></a>Nativní vkládání závislostí

Důležitým cílem při vytváření velkých, škálovatelných aplikací je volné párování součástí a služeb. [Vkládání závislostí](xref:fundamentals/dependency-injection) je oblíbenou technikou pro dosažení tohoto cíle a je nativní součástí ASP.NET Core.

V ASP.NET aplikacích se vývojáři spoléhají na knihovnu třetích stran při implementaci vkládání závislostí. Jednou z takových knihoven je [Unity](https://github.com/unitycontainer/unity), kterou poskytuje Microsoft Patterns & Practices.

Příkladem nastavení vkládání závislostí s Unity `IDependencyResolver` je implementace, `UnityContainer`která zabalí :

[!code-csharp[](samples/sample8.cs)]

Vytvořte instanci služby `UnityContainer`, zaregistrujte službu `HttpConfiguration` a nastavte překládání závislostí na novou instanci kontejneru: `UnityResolver`

[!code-csharp[](samples/sample9.cs)]

V `IProductRepository` případě potřeby vstříkněte:

[!code-csharp[](samples/sample5.cs)]

Vzhledem k tomu, že vkládání závislostí je `ConfigureServices` součástí ASP.NET core, můžete přidat službu v metodě *Startup.cs*:

[!code-csharp[](samples/configure-services.cs)]

Úložiště může být injektováno kdekoli, jak tomu bylo u Unity.

> [!NOTE]
> Další informace o vkládání závislostí naleznete v [tématu vkládání závislostí](xref:fundamentals/dependency-injection).

## <a name="serve-static-files"></a>Obsluha statických souborů

Důležitou součástí vývoje webových aplikací je schopnost obsluhovat statické prostředky na straně klienta. Nejběžnějšípříklady statických souborů jsou HTML, CSS, Javascript a obrázky. Tyto soubory je třeba uložit do publikovaného umístění aplikace (nebo CDN) a odkazovat, aby mohly být načteny požadavkem. Tento proces se změnil v ASP.NET Core.

V ASP.NET jsou statické soubory uloženy v různých adresářích a odkazovány v zobrazeních.

V ASP.NET Core jsou statické soubory uloženy v "kořenovém adresáři*&lt;webu" (kořenový obsah&gt;/wwwroot),* pokud není nakonfigurováno jinak. Soubory jsou načteny do kanálu požadavku vyvoláním `UseStaticFiles` metody `Startup.Configure`rozšíření z :

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> Pokud cílení .NET Framework, nainstalujte balíček `Microsoft.AspNetCore.StaticFiles`NuGet .

Například datový zdroj obrázku ve složce *wwwroot/images* je přístupný `http://<app>/images/<imageFileName>`prohlížeči v umístění, například .

> [!NOTE]
> Podrobnější odkaz na zobrazování statických souborů v ASP.NET jádru naleznete v [tématu Statické soubory](xref:fundamentals/static-files).

## <a name="multi-value-cookies"></a>Soubory cookie s více hodnotami

[Soubory cookie s více hodnotami](xref:System.Web.HttpCookie.Values) nejsou v ASP.NET Core podporovány. Vytvořte jeden soubor cookie na hodnotu.

## <a name="partial-app-migration"></a>Částečná migrace aplikací

Jedním z přístupů k částečné migraci aplikací je vytvoření podaplikace služby IIS a přesunutí pouze určitých tras z ASP.NET 4.x do ASP.NET jádra při zachování struktury adresy URL aplikace. Zvažte například strukturu adresy URL aplikace ze souboru *applicationHost.config:*

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

Adresářová struktura:

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a>Další zdroje

- [Přenesení knihoven do jádra rozhraní .NET](/dotnet/core/porting/libraries)
