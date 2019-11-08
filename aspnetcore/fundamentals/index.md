---
title: ASP.NET Core základy
author: rick-anderson
description: Seznamte se se základními pojmy pro vytváření ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/index
ms.openlocfilehash: 7173a732a04bf3e598adef298fa9120c15dd52fb
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799375"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core základy

Tento článek představuje přehled klíčových témat pro porozumění, jak vyvíjet aplikace ASP.NET Core.

## <a name="the-startup-class"></a>Spouštěcí třída

`Startup` třídy je:

* Služby vyžadované aplikací jsou nakonfigurované.
* Je definovaný kanál pro zpracování požadavků.

*Služby* jsou komponenty, které aplikace používá. Komponenta protokolování je například služba. Do metody `Startup.ConfigureServices` přidáte kód pro konfiguraci (nebo *registraci*) služeb.

Kanál pro zpracování žádostí se skládá z řady *middlewarových* součástí. Middleware například může zpracovávat požadavky na statické soubory nebo přesměrovat požadavky HTTP na HTTPS. Každý middleware provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek. Do metody `Startup.Configure` se přidá kód pro konfiguraci kanálu zpracování požadavků.

Tady je ukázková `Startup` třída:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

Další informace najdete v tématu <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injektáž závislostí (služby)

ASP.NET Core má integrovanou architekturu pro vkládání závislostí (DI), která zpřístupňuje nakonfigurované služby pro třídy aplikace. Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvořit konstruktor s parametrem požadovaného typu. Parametr může být typ služby nebo rozhraní. Systém DI poskytuje službu za běhu.

Zde je třída, která používá DI k získání objektu kontextu Entity Framework Core. Zvýrazněný řádek je příkladem injektáže konstruktoru:

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

I když je systém v systému integrovaný, je navržený tak, aby umožňoval připojení kontejneru inverze správy (IoC) třetí strany, pokud budete chtít.

Další informace najdete v tématu <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Kanál pro zpracování žádostí se skládá z řady middlewarových součástí. Každá komponenta provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek.

Podle konvence je do kanálu přidána komponenta middleware, a to voláním metody rozšíření `Use...` v metodě `Startup.Configure`. Chcete-li například povolit vykreslování statických souborů, zavolejte `UseStaticFiles`.

Zvýrazněný kód v následujícím příkladu konfiguruje kanál zpracování žádostí:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

ASP.NET Core obsahuje bohatou sadu předdefinovaných middlewarů a můžete napsat vlastní middleware.

Další informace najdete v tématu <xref:fundamentals/middleware/index>.

## <a name="host"></a>Hostitel

Aplikace ASP.NET Core při spuštění vytvoří *hostitele* . Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:

* Implementace HTTP serveru
* Komponenty middlewaru
* protokolování
* DŽI
* Konfigurace

Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.

::: moniker range=">= aspnetcore-3.0"

K dispozici jsou dva hostitelé: obecný hostitel a webový hostitel. Doporučuje se použít obecného hostitele a webový hostitel je k dispozici pouze pro zpětnou kompatibilitu.

Kód pro vytvoření hostitele je `Program.Main`:

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

Metody `CreateDefaultBuilder` a `ConfigureWebHostDefaults` konfigurují hostitele pomocí běžně používaných možností, jako jsou například tyto:

* Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.
* Načíst konfiguraci z *appSettings. JSON*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.
* Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.

Další informace najdete v tématu <xref:fundamentals/host/generic-host>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel. V ASP.NET Core 2. x je obecný hostitel pouze pro scénáře, které nejsou webové.

Kód pro vytvoření hostitele je `Program.Main`:

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

Metoda `CreateDefaultBuilder` Konfiguruje hostitele pomocí běžně používaných možností, jako jsou například následující:

* Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.
* Načíst konfiguraci z *appSettings. JSON*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.
* Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.

Další informace najdete v tématu <xref:fundamentals/host/web-host>.

::: moniker-end

### <a name="non-web-scenarios"></a>Jiné než webové scénáře

Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací. Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servery

ASP.NET Core aplikace používá implementaci HTTP serveru k naslouchání požadavkům HTTP. Server objedná požadavky do aplikace jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do `HttpContext`.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/). V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.
* *Http server IIS* je server pro Windows, který používá službu IIS. S tímto serverem aplikace ASP.NET Core a služba IIS běží ve stejném procesu.
* *Http. sys* je server pro Windows, který se nepoužívá se službou IIS.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/). V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.
* *Http. sys* je server pro Windows, který se nepoužívá se službou IIS.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

Další informace najdete v tématu <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurace

ASP.NET Core poskytuje konfigurační rozhraní, které získá nastavení jako páry název-hodnota z seřazené sady poskytovatelů konfigurace. Existují předdefinované poskytovatelé konfigurace pro nejrůznější zdroje, například soubory *. JSON* , soubory *. XML* , proměnné prostředí a argumenty příkazového řádku. Můžete také psát vlastní poskytovatele konfigurace.

Například můžete určit, že konfigurace pochází z proměnných *appSettings. JSON* a proměnných prostředí. Poté, co je požadována hodnota *ConnectionString* , rozhraní vyhledá první v souboru *appSettings. JSON* . Pokud je hodnota nalezena, ale také v proměnné prostředí, bude mít přednost hodnota z proměnné prostředí.

Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Nástroj Správce tajných klíčů](xref:security/app-secrets). V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).

Další informace najdete v tématu <xref:fundamentals/configuration/index>.

## <a name="options"></a>Možnosti

Pokud je to možné, ASP.NET Core podle *vzoru možností* pro ukládání a načítání hodnot konfigurace. Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.

Například následující kód nastaví možnosti WebSockets:

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

Další informace najdete v tématu <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Environment

Spouštěcí prostředí, jako je *vývoj*, *Příprava*a *produkce*, jsou první třídou pojmu v ASP.NET Core. Nastavením proměnné prostředí `ASPNETCORE_ENVIRONMENT` můžete určit prostředí, ve kterém je aplikace spuštěná. ASP.NET Core přečte tuto proměnnou prostředí při spuštění aplikace a uloží hodnotu do implementace `IHostingEnvironment`. Objekt prostředí je k dispozici kdekoli v aplikaci přes DI.

Následující vzorový kód z `Startup` třídy nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybách pouze v případě, že běží ve vývoji:

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

Další informace najdete v tématu <xref:fundamentals/environments>.

## <a name="logging"></a>protokolování

ASP.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců. K dispozici jsou následující poskytovatelé:

* Konzola
* Ladit
* Trasování událostí ve Windows
* Protokol událostí systému Windows
* TraceSource
* Azure App Service
* Application Insights Azure

Zápis protokolů z libovolného místa v kódu aplikace získáním `ILogger` objektu z DI a volání metod protokolu.

Zde je ukázkový kód, který používá objekt `ILogger`, s vkládáním konstruktoru a volání metody protokolování zvýrazněné.

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

Rozhraní `ILogger` umožňuje předat poskytovateli protokolování libovolný počet polí. Pole jsou běžně používána k sestavení řetězce zprávy, ale zprostředkovatel je může také odeslat jako samostatné pole do úložiště dat. Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Další informace najdete v tématu <xref:fundamentals/logging/index>.

## <a name="routing"></a>Směrování

*Trasa* je vzor URL, který je namapován na obslužnou rutinu. Obslužná rutina je typicky stránka Razor, metoda akce v řadiči MVC nebo middleware. ASP.NET Core směrování vám umožní řídit adresy URL používané vaší aplikací.

Další informace najdete v tématu <xref:fundamentals/routing>.

## <a name="error-handling"></a>Zpracování chyb

ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:

* Stránka s výjimkou vývojáře
* Vlastní chybové stránky
* Statické stránky se stavovým kódem
* Zpracování výjimek při spuštění

Další informace najdete v tématu <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Vytváření požadavků HTTP

Pro vytváření instancí `HttpClient` je k dispozici implementace `IHttpClientFactory`. Objekt pro vytváření:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k GitHubu. Výchozí klient může být zaregistrován pro jiné účely.
* Podporuje registraci a řetězení více obslužných rutin delegování pro sestavení kanálu middlewaru odchozího požadavku. Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core. Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.
* Integruje se s *Polly*oblíbenou knihovnou třetích stran pro zpracování přechodného selhání.
* Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.
* Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.

Další informace najdete v tématu <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Kořen obsahu

Kořen obsahu je základní cesta k:

* Spustitelný soubor, který hostuje aplikaci ( *. exe*).
* Kompilovaná sestavení, která tvoří aplikaci ( *. dll*).
* Soubory s obsahem neobsahující kód, které používá aplikace, například:
  * Soubory Razor ( *. cshtml*, *. Razor*)
  * Konfigurační soubory ( *. JSON*, *. XML*)
  * Datové soubory ( *. DB*)
* [Webový kořenový adresář](#web-root), obvykle publikovaná složka *wwwroot* .

Během vývoje:

* Kořen obsahu se nastaví jako kořenový adresář projektu.
* Kořenový adresář projektu slouží k vytvoření:
  * Cesta k souborům obsahu bez kódu aplikace v kořenovém adresáři projektu.
  * [Webový kořenový](#web-root)adresář, obvykle složka *wwwroot* v kořenovém adresáři projektu.

::: moniker range=">= aspnetcore-3.0"

Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host). Další informace najdete v tématu <xref:fundamentals/host/generic-host#contentrootpath>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host). Další informace najdete v tématu <xref:fundamentals/host/web-host#content-root>.

::: moniker-end

## <a name="web-root"></a>Webový kořenový adresář

Kořenový adresář webu je základní cesta k souborům statických prostředků bez kódu, jako je například:

* Šablony stylů ( *. CSS*)
* JavaScript ( *. js*)
* Obrázky ( *. png*, *. jpg*)

Statické soubory jsou obsluhovány ve výchozím nastavení pouze z kořenového adresáře webu (a podadresářů).

::: moniker range=">= aspnetcore-3.0"

Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář. Další informace najdete v tématu <xref:fundamentals/host/generic-host#webroot>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář. Další informace najdete v tématu [Web root](xref:fundamentals/host/web-host#web-root).

::: moniker-end

Zabraňte publikování souborů v *wwwroot* pomocí [\<obsahu > položky projektu](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu. Následující příklad zabraňuje publikování obsahu v adresáři *wwwroot/Local* a v podadresářích:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

V souborech Razor ( *. cshtml*) směřuje vlnové lomítko (`~/`) do kořenového adresáře webu. Cesta začínající na `~/` je označována jako *virtuální cesta*.

Další informace najdete v tématu <xref:fundamentals/static-files>.
