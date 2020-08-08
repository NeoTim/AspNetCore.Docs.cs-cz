---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se se základními pojmy pro vytváření ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/index
ms.openlocfilehash: f141e9248a702ad9a1d9737f82543a0ccc8fb573
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017204"
---
# <a name="aspnet-core-fundamentals"></a>Základy ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Tento článek poskytuje přehled klíčových témat pro porozumění, jak vyvíjet aplikace ASP.NET Core.

## <a name="the-startup-class"></a>Spouštěcí třída

`Startup`Třída je:

* Služby vyžadované aplikací jsou nakonfigurované.
* Kanál pro zpracování požadavků aplikace je definován jako série součástí middlewaru.

Tady je ukázková `Startup` Třída:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injektáž závislostí (služby)

ASP.NET Core obsahuje integrovanou platformu pro vkládání závislostí (DI), která zpřístupňuje nakonfigurované služby v celé aplikaci. Komponenta protokolování je například služba.

Do metody se přidá kód pro konfiguraci (nebo *registrování*) služeb `Startup.ConfigureServices` . Například:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Služby jsou obvykle vyřešeny z DI pomocí injektáže konstruktoru. Pomocí injektáže konstruktoru třída deklaruje parametr konstruktoru buď požadovaného typu, nebo rozhraní. Rozhraní DI Framework poskytuje instanci této služby za běhu.

Následující příklad používá vkládání konstruktoru k vyřešení `RazorPagesMovieContext` od di:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Pokud integrovaný IoC kontejner inverze nesplňuje všechny požadavky aplikace, můžete místo toho použít kontejner IoC třetí strany.

Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Kanál pro zpracování žádostí se skládá z řady middlewarových součástí. Každá komponenta provádí operace s `HttpContext` a buď vyvolá další middleware v kanálu, nebo ukončí požadavek.

Podle konvence je do kanálu přidána komponenta middleware vyvoláním `Use...` metody rozšíření v `Startup.Configure` metodě. Chcete-li například povolit vykreslování statických souborů, zavolejte `UseStaticFiles` .

Následující příklad konfiguruje kanál zpracování žádostí:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core obsahuje bohatou sadu předdefinovaných middlewarů. Je také možné zapsat vlastní součásti middlewaru.

Další informace naleznete v tématu <xref:fundamentals/middleware/index>.

## <a name="host"></a>Hostitel

Při spuštění aplikace ASP.NET Core vytvoří *hostitele*. Hostitel zapouzdřuje všechny prostředky aplikace, například:

* Implementace HTTP serveru
* Komponenty middlewaru
* protokolování
* Služby pro vkládání závislostí (DI)
* Konfigurace

Existují dva různé hostitele: 

* Obecný hostitel .NET
* ASP.NET Core webového hostitele

Doporučuje se obecný hostitel .NET. Webový hostitel ASP.NET Core je k dispozici pouze pro zpětnou kompatibilitu.

Následující příklad vytvoří obecný hostitel .NET:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

`CreateDefaultBuilder`Metody a `ConfigureWebHostDefaults` konfigurují hostitele pomocí sady výchozích možností, jako například:

* Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.
* Načíst konfiguraci z *appsettings.jsna*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.
* Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.

Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Jiné než webové scénáře

Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací. Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servery

ASP.NET Core aplikace používá implementaci HTTP serveru k naslouchání požadavkům HTTP. Serverový povrch vyžádá aplikaci jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do `HttpContext` .

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/). V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.
* *Http server IIS* je server pro Windows, který používá službu IIS. S tímto serverem aplikace ASP.NET Core a služba IIS běží ve stejném procesu.
* *HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2,0 nebo novějším může Kestrel běžet jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2,0 nebo novějším může Kestrel běžet jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

Další informace naleznete v tématu <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurace

ASP.NET Core poskytuje konfigurační rozhraní, které získá nastavení jako páry název-hodnota z seřazené sady poskytovatelů konfigurace. Předdefinovaná poskytovatelé konfigurace jsou k dispozici pro nejrůznější zdroje, například soubory *. JSON* , soubory *. XML* , proměnné prostředí a argumenty příkazového řádku. Napište vlastní poskytovatele konfigurace pro podporu jiných zdrojů.

Ve [výchozím nastavení](xref:fundamentals/configuration/index#default)jsou aplikace ASP.NET Core nakonfigurované pro čtení z *appsettings.jsna*, proměnných prostředí, příkazového řádku a dalších. Když je načtena konfigurace aplikace, hodnoty z proměnných prostředí přepíší hodnoty z *appsettings.js*.

Upřednostňovaným způsobem, jak číst související konfigurační hodnoty, je použít [vzor možností](xref:fundamentals/configuration/options). Další informace najdete v tématu [vázání hierarchických konfiguračních dat pomocí vzoru možností](xref:fundamentals/configuration/index#optpat).

Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [správce tajných klíčů](xref:security/app-secrets#secret-manager). V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).

Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Prostředí

Spouštěcí prostředí, jako jsou `Development` , `Staging` a `Production` , jsou první třídou pojmu v ASP.NET Core. Určete prostředí, ve kterém je aplikace spuštěná, nastavením `ASPNETCORE_ENVIRONMENT` proměnné prostředí. ASP.NET Core přečte tuto proměnnou prostředí při spuštění aplikace a uloží hodnotu v `IWebHostEnvironment` implementaci. Tato implementace je k dispozici kdekoli v aplikaci prostřednictvím injektáže závislosti (DI).

Následující příklad nakonfiguruje aplikaci tak, aby při spuštění v prostředí poskytovala podrobné informace o chybě `Development` :

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="logging"></a>protokolování

ASP.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců. Mezi dostupné zprostředkovatele patří:

* Konzola
* Ladění
* Trasování událostí ve Windows
* Protokol událostí systému Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Chcete-li vytvořit protokoly, vyřešte <xref:Microsoft.Extensions.Logging.ILogger%601> službu z vkládání závislostí (di) a metody protokolování volání jako <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> . Například:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Metody protokolování, jako je například `LogInformation` Podpora libovolného počtu polí. Tato pole se běžně používají k vytvoření zprávy `string` , ale někteří zprostředkovatelé protokolování odesílají tato data do úložiště dat jako samostatná pole. Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Další informace naleznete v tématu <xref:fundamentals/logging/index>.

## <a name="routing"></a>Směrování

*Trasa* je vzor URL, který je namapován na obslužnou rutinu. Obslužná rutina je typicky Razor Stránka, metoda akce v řadiči MVC nebo middleware. ASP.NET Core směrování vám umožní řídit adresy URL používané vaší aplikací.

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="error-handling"></a>Zpracování chyb

ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:

* Stránka s výjimkou vývojáře
* Vlastní chybové stránky
* Statické stránky se stavovým kódem
* Zpracování výjimek při spuštění

Další informace naleznete v tématu <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Vytváření požadavků HTTP

Implementace `IHttpClientFactory` je k dispozici pro vytváření `HttpClient` instancí. Objekt pro vytváření:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Můžete například zaregistrovat a nakonfigurovat klienta *GitHub* pro přístup k GitHubu. Zaregistrujte a nakonfigurujte výchozího klienta pro jiné účely.
* Podporuje registraci a řetězení více obslužných rutin delegování pro sestavení kanálu middlewaru odchozího požadavku. Tento model je podobný vstupnímu kanálu middleware ASP.NET Core. Vzor poskytuje mechanismus pro správu otázek pro různé průřezy pro požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.
* Integruje se s *Polly*oblíbenou knihovnou třetích stran pro zpracování přechodného selhání.
* Spravuje sdružování a životnost základních `HttpClientHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při `HttpClient` Ruční správě životního cyklu.
* Přidá konfigurovatelné prostředí protokolování prostřednictvím <xref:Microsoft.Extensions.Logging.ILogger> všech požadavků odesílaných klienty vytvořenými pomocí továrny.

Další informace naleznete v tématu <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Kořen obsahu

Kořen obsahu je základní cesta pro:

* Spustitelný soubor, který hostuje aplikaci (*. exe*).
* Kompilovaná sestavení, která tvoří aplikaci (*. dll*).
* Soubory obsahu používané aplikací, jako například:
  * Razorsoubory (*. cshtml*, *. Razor*)
  * Konfigurační soubory (*. JSON*, *. XML*)
  * Datové soubory (*. DB*)
* [Kořenový adresář webu](#web-root), obvykle složka *wwwroot* .

Během vývoje se výchozí hodnota kořenu obsahu nastaví na kořenový adresář projektu. Tento adresář je také základní cestou pro soubory obsahu aplikace i pro [kořenový adresář webu](#web-root). Určete jiný kořen obsahu nastavením jeho cesty při [sestavování hostitele](#host). Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/host/generic-host#contentroot).

## <a name="web-root"></a>Webový kořenový adresář

Kořenový adresář webu je základní cesta pro veřejné a statické soubory prostředků, jako například:

* Šablony stylů (*. CSS*)
* JavaScript (*. js*)
* Obrázky (*. png*, *. jpg*)

Ve výchozím nastavení jsou statické soubory obsluhovány pouze z kořenového adresáře webu a jeho podadresářů. Kořenová cesta webu je ve výchozím nastavení *{root obsahu}/wwwroot*. Určete jiný webový kořenový adresář nastavením jeho cesty při [sestavování hostitele](#host). Další informace najdete v tématu [Web root](xref:fundamentals/host/generic-host#webroot).

Zabraňte publikování souborů v *wwwroot* pomocí [ \<Content> položky projektu](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu. Následující příklad brání publikování obsahu v *wwwroot/Local* a jeho podadresářích:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

V Razor souborech *. cshtml* odkazuje vlnové lomítko ( `~/` ) na kořenový adresář webu. Cesta začínající `~/` na je označována jako *virtuální cesta*.

Další informace naleznete v tématu <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek představuje přehled klíčových témat pro porozumění, jak vyvíjet aplikace ASP.NET Core.

## <a name="the-startup-class"></a>Spouštěcí třída

`Startup`Třída je:

* Služby vyžadované aplikací jsou nakonfigurované.
* Je definovaný kanál pro zpracování požadavků.

*Služby* jsou komponenty, které aplikace používá. Komponenta protokolování je například služba. Do metody se přidá kód pro konfiguraci (nebo *registrování*) služeb `Startup.ConfigureServices` .

Kanál pro zpracování žádostí se skládá z řady *middlewarových* součástí. Middleware například může zpracovávat požadavky na statické soubory nebo přesměrovat požadavky HTTP na HTTPS. Každý middleware provádí asynchronní operace s `HttpContext` a pak buď vyvolá další middleware v kanálu, nebo ukončí požadavek. Do metody se přidá kód pro konfiguraci kanálu zpracování požadavků `Startup.Configure` .

Tady je ukázková `Startup` Třída:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injektáž závislostí (služby)

ASP.NET Core má integrovanou architekturu pro vkládání závislostí (DI), která zpřístupňuje nakonfigurované služby pro třídy aplikace. Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvořit konstruktor s parametrem požadovaného typu. Parametr může být typ služby nebo rozhraní. Systém DI poskytuje službu za běhu.

Zde je třída, která používá DI k získání objektu kontextu Entity Framework Core. Zvýrazněný řádek je příkladem injektáže konstruktoru:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

I když je systém v systému integrovaný, je navržený tak, aby umožňoval připojení kontejneru inverze správy (IoC) třetí strany, pokud budete chtít.

Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Kanál pro zpracování žádostí se skládá z řady middlewarových součástí. Každá komponenta provádí asynchronní operace s `HttpContext` a pak buď vyvolá další middleware v kanálu, nebo ukončí požadavek.

Podle konvence je do kanálu přidána komponenta middleware vyvoláním `Use...` metody rozšíření v `Startup.Configure` metodě. Chcete-li například povolit vykreslování statických souborů, zavolejte `UseStaticFiles` .

Zvýrazněný kód v následujícím příkladu konfiguruje kanál zpracování žádostí:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core obsahuje bohatou sadu předdefinovaných middlewarů a můžete napsat vlastní middleware.

Další informace naleznete v tématu <xref:fundamentals/middleware/index>.

## <a name="host"></a>Hostitel

Aplikace ASP.NET Core při spuštění vytvoří *hostitele* . Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:

* Implementace HTTP serveru
* Komponenty middlewaru
* protokolování
* DŽI
* Konfigurace

Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.

K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel. V ASP.NET Core 2. x je obecný hostitel pouze pro scénáře, které nejsou webové.

Kód pro vytvoření hostitele je v `Program.Main` :

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

`CreateDefaultBuilder`Metoda Konfiguruje hostitele pomocí běžně používaných možností, jako například následujících:

* Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.
* Načíst konfiguraci z *appsettings.jsna*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.
* Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.

Další informace naleznete v tématu <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Jiné než webové scénáře

Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací. Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servery

ASP.NET Core aplikace používá implementaci HTTP serveru k naslouchání požadavkům HTTP. Serverový povrch vyžádá aplikaci jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do `HttpContext` .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/). Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.
* *Http server IIS* je server pro Windows, který používá službu IIS. S tímto serverem aplikace ASP.NET Core a služba IIS běží ve stejném procesu.
* *HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/). Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.
* *HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Další informace naleznete v tématu <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurace

ASP.NET Core poskytuje konfigurační rozhraní, které získá nastavení jako páry název-hodnota z seřazené sady poskytovatelů konfigurace. Existují předdefinované poskytovatelé konfigurace pro nejrůznější zdroje, například soubory *. JSON* , soubory *. XML* , proměnné prostředí a argumenty příkazového řádku. Můžete také psát vlastní poskytovatele konfigurace.

Například můžete určit, že konfigurace pochází z *appsettings.js* a proměnných prostředí. Poté, co je požadována hodnota *ConnectionString* , rozhraní nejprve vyhledá v souboru *appsettings.js* . Pokud je hodnota nalezena, ale také v proměnné prostředí, bude mít přednost hodnota z proměnné prostředí.

Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Nástroj Správce tajných klíčů](xref:security/app-secrets). V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).

Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="options"></a>Možnosti

Pokud je to možné, ASP.NET Core podle *vzoru možností* pro ukládání a načítání hodnot konfigurace. Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.

Například následující kód nastaví možnosti WebSockets:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Prostředí

Spouštěcí prostředí, jako je *vývoj*, *Příprava*a *produkce*, jsou první třídou pojmu v ASP.NET Core. Nastavením proměnné prostředí můžete určit prostředí, ve kterém je aplikace spuštěná `ASPNETCORE_ENVIRONMENT` . ASP.NET Core přečte tuto proměnnou prostředí při spuštění aplikace a uloží hodnotu v `IHostingEnvironment` implementaci. Objekt prostředí je k dispozici kdekoli v aplikaci přes DI.

Následující vzorový kód z `Startup` třídy nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybách pouze v případě, že běží ve vývoji:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="logging"></a>protokolování

ASP.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců. K dispozici jsou následující poskytovatelé:

* Konzola
* Ladění
* Trasování událostí ve Windows
* Protokol událostí systému Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Zápis protokolů z libovolného místa v kódu aplikace získáním `ILogger` objektu z metody di a volání metod protokolu.

Zde je ukázkový kód, který používá `ILogger` objekt, s vkládáním konstruktoru a volání metody protokolování zvýrazněné.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

`ILogger`Rozhraní vám umožní předat do zprostředkovatele protokolování libovolný počet polí. Pole jsou běžně používána k sestavení řetězce zprávy, ale zprostředkovatel je může také odeslat jako samostatné pole do úložiště dat. Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Další informace naleznete v tématu <xref:fundamentals/logging/index>.

## <a name="routing"></a>Směrování

*Trasa* je vzor URL, který je namapován na obslužnou rutinu. Obslužná rutina je typicky Razor Stránka, metoda akce v řadiči MVC nebo middleware. ASP.NET Core směrování vám umožní řídit adresy URL používané vaší aplikací.

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="error-handling"></a>Zpracování chyb

ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:

* Stránka s výjimkou vývojáře
* Vlastní chybové stránky
* Statické stránky se stavovým kódem
* Zpracování výjimek při spuštění

Další informace naleznete v tématu <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Vytváření požadavků HTTP

Implementace `IHttpClientFactory` je k dispozici pro vytváření `HttpClient` instancí. Objekt pro vytváření:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k GitHubu. Výchozí klient může být zaregistrován pro jiné účely.
* Podporuje registraci a řetězení více obslužných rutin delegování pro sestavení kanálu middlewaru odchozího požadavku. Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core. Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.
* Integruje se s *Polly*oblíbenou knihovnou třetích stran pro zpracování přechodného selhání.
* Spravuje sdružování a životnost základních `HttpClientHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.
* Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.

Další informace naleznete v tématu <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Kořen obsahu

Kořen obsahu je základní cesta k:

* Spustitelný soubor, který hostuje aplikaci (*. exe*).
* Kompilovaná sestavení, která tvoří aplikaci (*. dll*).
* Soubory s obsahem neobsahující kód, které používá aplikace, například:
  * Razorsoubory (*. cshtml*, *. Razor*)
  * Konfigurační soubory (*. JSON*, *. XML*)
  * Datové soubory (*. DB*)
* [Webový kořenový adresář](#web-root), obvykle publikovaná složka *wwwroot* .

Během vývoje:

* Kořen obsahu se nastaví jako kořenový adresář projektu.
* Kořenový adresář projektu slouží k vytvoření:
  * Cesta k souborům obsahu bez kódu aplikace v kořenovém adresáři projektu.
  * [Webový kořenový](#web-root)adresář, obvykle složka *wwwroot* v kořenovém adresáři projektu.

Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host). Další informace naleznete v tématu <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Webový kořenový adresář

Kořenový adresář webu je základní cesta k souborům statických prostředků bez kódu, jako je například:

* Šablony stylů (*. CSS*)
* JavaScript (*. js*)
* Obrázky (*. png*, *. jpg*)

Statické soubory jsou obsluhovány ve výchozím nastavení pouze z kořenového adresáře webu (a podadresářů).

Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář. Další informace najdete v tématu [Web root](xref:fundamentals/host/web-host#web-root).

Zabraňte publikování souborů v *wwwroot* pomocí [ \<Content> položky projektu](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu. Následující příklad zabraňuje publikování obsahu v adresáři *wwwroot/Local* a v podadresářích:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

V Razor souborech (*. cshtml*) odkazuje vlnové lomítko ( `~/` ) na kořenový adresář webu. Cesta začínající `~/` na je označována jako *virtuální cesta*.

Další informace naleznete v tématu <xref:fundamentals/static-files>.

::: moniker-end
