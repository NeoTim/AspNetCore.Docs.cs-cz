---
title: Migrace z ASP.NET na ASP.NET Core
author: isaac2004
description: Dodržení pokynů k migraci stávajících ASP.NET MVC nebo webových aplikací API na ASP.NET Core. Web
ms.author: scaddie
ms.date: 10/18/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/index
ms.openlocfilehash: 7f5d2835d93631ac73b3da0c3dc26d87ef64c57d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634758"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>Migrace z ASP.NET na ASP.NET Core

Od [Petr Levin](https://isaaclevin.com)

Tento článek slouží jako referenční příručka pro migraci aplikací ASP.NET na ASP.NET Core.

## <a name="prerequisites"></a>Předpoklady

[.NET Core SDK 2,2 nebo novější](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>Cílové architektury

Projekty ASP.NET Core nabízejí vývojářům flexibilitu při cílení na rozhraní .NET Core, .NET Framework nebo obojí. V tématu [Výběr mezi .NET Core a .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) určíte, které cílové rozhraní je nejvhodnější.

Při cílení na .NET Framework musí projekty odkazovat na jednotlivé balíčky NuGet.

Cílení na .NET Core vám umožní eliminovat mnoho explicitních odkazů na balíčky díky ASP.NET Core [Metapackage](xref:fundamentals/metapackage-app). Nainstalujte do `Microsoft.AspNetCore.App` projektu Metapackage:

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

Při použití Metapackage se s aplikací nasadí žádné balíčky, na které se neodkazuje v Metapackage. Běhové úložiště .NET Core zahrnuje tyto prostředky a jsou předkompilovány pro zlepšení výkonu. Další podrobnosti najdete v článku [Microsoft. AspNetCore. app Metapackage pro ASP.NET Core](xref:fundamentals/metapackage-app) .

## <a name="project-structure-differences"></a>Rozdíly struktury projektu

Formát souboru *. csproj* byl v ASP.NET Core zjednodušený. Mezi významné změny patří:

- Explicitní zahrnutí souborů není nutné, aby byly považovány za součást projektu. Tím se snižuje riziko konfliktů při sloučení XML při práci na velkých týmech.
- Neexistují žádné odkazy založené na identifikátorech GUID na jiné projekty, což vylepšuje čitelnost souboru.
- Soubor lze upravovat bez jeho uvolnění v aplikaci Visual Studio:

    ![Možnost úpravy kontextové nabídky příkazu CSPROJ v aplikaci Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Nahrazení souboru Global. asax

ASP.NET Core zavádí nový mechanismus pro zavedení aplikace. Vstupním bodem pro aplikace ASP.NET je soubor *Global. asax* . Úlohy, jako je konfigurace směrování a registrace do oblasti, jsou zpracovávány v souboru *Global. asax* .

[!code-csharp[](samples/globalasax-sample.cs)]

Tento přístup Couples aplikaci a server, na který je nasazený, způsobem, který je v konfliktu s implementací. Ve snaze oddělit se [Owin](https://owin.org/) zavedla k tomu, aby poskytovala čisticí způsob, jak používat víc platforem dohromady. OWIN poskytuje kanál pro přidání pouze těch potřebných modulů. Hostující prostředí přijímá [spouštěcí](xref:fundamentals/startup) funkci ke konfiguraci služeb a kanálu požadavků aplikace. `Startup` registruje sadu middlewaru s aplikací. Pro každý požadavek aplikace volá každou součást middlewaru s ukazatelem na hlavní seznam s ukazatelem na existující sadu obslužných rutin. Každá součást middleware může do kanálu zpracování požadavků přidat jednu nebo více obslužných rutin. To je dosaženo vrácením odkazu na obslužnou rutinu, která je nového záhlaví seznamu. Každá obslužná rutina zodpovídá za zapamatování a vyvolání další obslužné rutiny v seznamu. V ASP.NET Core je vstupním bodem aplikace `Startup` a již nemusíte mít závislost na *Global. asax*. Při použití OWIN s .NET Framework použijte jako kanál něco podobného jako v následujícím tvaru:

[!code-csharp[](samples/webapi-owin.cs)]

Tím se nakonfigurují výchozí trasy a ve výchozím nastavení se XmlSerialization přes JSON. Podle potřeby přidejte do tohoto kanálu další middleware (načítají se služby, nastavení konfigurace, statické soubory atd.).

ASP.NET Core používá podobný přístup, ale nespoléhá na OWIN k tomu, aby záznam zpracoval. Místo toho se provádí pomocí metody *program.cs* `Main` (podobně jako u konzolových aplikací) a `Startup` načítá se přes něj.

[!code-csharp[](samples/program.cs)]

`Startup` musí obsahovat `Configure` metodu. Do `Configure` kanálu přidejte potřebný middleware. V následujícím příkladu (z výchozí šablony webu) rozšiřující metody konfigurují kanál s podporou pro:

- Chybové stránky
- Striktní přenosová zabezpečení HTTP
- Přesměrování HTTP na HTTPS
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

Hostitel a aplikace se odlišily, což poskytuje flexibilitu přesunu na jinou platformu v budoucnu.

> [!NOTE]
> Podrobnější informace o ASP.NET Core spouštění a middlewaru najdete v tématu [spuštění v ASP.NET Core](xref:fundamentals/startup)

## <a name="store-configurations"></a>Konfigurace úložiště

ASP.NET podporuje ukládání nastavení. Toto nastavení se používá například pro podporu prostředí, do kterého byly aplikace nasazeny. Běžný postup je uložit všechny vlastní páry klíč-hodnota do `<appSettings>` části souboru *Web.config* :

[!code-xml[](samples/webconfig-sample.xml)]

Aplikace čtou tato nastavení pomocí `ConfigurationManager.AppSettings` kolekce v `System.Configuration` oboru názvů:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core může ukládat konfigurační data pro aplikaci do libovolného souboru a načíst je jako součást zavádění middlewaru. Výchozí soubor, který se používá v šablonách projektu, je *appsettings.js*:

[!code-json[](samples/appsettings-sample.json)]

Načtení tohoto souboru do instance `IConfiguration` uvnitř aplikace se provádí v *Startup.cs*:

[!code-csharp[](samples/startup-builder.cs)]

Aplikace načte z `Configuration` a získá nastavení:

[!code-csharp[](samples/read-appsettings.cs)]

Existují rozšíření tohoto přístupu, aby byl proces robustnější, jako je například použití [vkládání závislostí](xref:fundamentals/dependency-injection) (di) k načtení služby s těmito hodnotami. Metoda DI má k dispozici sadu konfiguračních objektů se silným typem.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> Podrobnější informace o konfiguraci ASP.NET Core najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index).

## <a name="native-dependency-injection"></a>Vkládání nativních závislostí

Důležitým cílem při sestavování rozsáhlých škálovatelných aplikací je volně přizpůsobitelné spojování komponent a služeb. [Vkládání závislostí](xref:fundamentals/dependency-injection) je oblíbená technika pro dosažení tohoto a je to nativní součást ASP.NET Core.

V aplikacích ASP.NET se vývojáři spoléhají na knihovnu třetí strany, která implementuje vkládání závislostí. Jedna taková knihovna je [Unity](https://github.com/unitycontainer/unity), kterou poskytují vzory Microsoftu & postupy.

Příkladem nastavení injektáže závislosti s Unity je implementace `IDependencyResolver` , která zabalí `UnityContainer` :

[!code-csharp[](samples/sample8.cs)]

Vytvořte instanci své `UnityContainer` služby, Zaregistrujte svoji službu a nastavte překladač závislostí `HttpConfiguration` na novou instanci `UnityResolver` pro svůj kontejner:

[!code-csharp[](samples/sample9.cs)]

Vložení `IProductRepository` tam, kde je potřeba:

[!code-csharp[](samples/sample5.cs)]

Protože vkládání závislostí je součástí ASP.NET Core, můžete přidat službu v `ConfigureServices` metodě *Startup.cs*:

[!code-csharp[](samples/configure-services.cs)]

Úložiště může být vloženo kdekoli, stejně jako u Unity.

> [!NOTE]
> Další informace o vkládání závislostí naleznete v tématu [Injektáže závislosti](xref:fundamentals/dependency-injection).

## <a name="serve-static-files"></a>Obsluhovat statické soubory

Důležitou součástí vývoje webu je schopnost poskytovat statické prostředky na straně klienta. Nejběžnějšími příklady statických souborů jsou HTML, CSS, JavaScript a image. Tyto soubory musí být uloženy v publikovaném umístění aplikace (nebo CDN) a odkazovány, aby je bylo možné načíst pomocí žádosti. Tento proces se v ASP.NET Core změnil.

V ASP.NET jsou statické soubory uloženy v různých adresářích a odkazovány v zobrazeních.

V ASP.NET Core jsou statické soubory uloženy v kořenu "Web root" (* &lt; kořenový adresář obsahu &gt; /wwwroot*), pokud není nakonfigurováno jinak. Soubory jsou načteny do kanálu požadavků vyvoláním `UseStaticFiles` metody rozšíření z `Startup.Configure` :

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> Pokud cílíte .NET Framework, nainstalujte balíček NuGet `Microsoft.AspNetCore.StaticFiles` .

Například prostředek obrázku ve složce *wwwroot/images* je přístupný prohlížeči v umístění, jako je například `http://<app>/images/<imageFileName>` .

> [!NOTE]
> Podrobnější informace o obsluze statických souborů v ASP.NET Core najdete v tématu [statické soubory](xref:fundamentals/static-files).

## <a name="multi-value-no-loccookies"></a>S více hodnotami cookie

V ASP.NET Core nejsou podporovány [hodnoty cookie s více hodnotami](xref:System.Web.HttpCookie.Values) . Vytvořte jednu cookie na hodnotu.

## <a name="partial-app-migration"></a>Migrace částečné aplikace

Jedním z možností migrace částečné aplikace je vytvoření podaplikace služby IIS a přesunutí určitých tras z ASP.NET 4. x na ASP.NET Core a zachování struktury adresy URL aplikace. Zvažte například strukturu adresy URL aplikace ze souboru *applicationHost.config* :

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

## <a name="additional-resources"></a>Další zdroje informací

- [Portování knihoven do .NET Core](/dotnet/core/porting/libraries)
