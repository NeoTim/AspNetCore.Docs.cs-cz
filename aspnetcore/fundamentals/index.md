---
title: ASP.NET Core základy
author: rick-anderson
description: Seznamte se se základními pojmy pro vytváření ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: fundamentals/index
ms.openlocfilehash: 3fbfc7c4c0d5e568339bc00a7cbe84a3932acf1f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664232"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core základy

Tento článek je přehled klíčových témat pro pochopení způsobu vývoje ASP.NET Core aplikací.

## <a name="the-startup-class"></a>Třída Startup

`Startup` třídy je:

* Služby vyžadované aplikací jsou nakonfigurované.
* Je definován kanál pro zpracování požadavků

*Služby* jsou komponenty, které aplikace používá. Komponenta protokolování je například služba. Do metody `Startup.ConfigureServices` přidáte kód pro konfiguraci (nebo *registraci*) služeb.

Kanál pro zpracování žádostí se skládá z řady *middlewarových* součástí. Middleware může třeba obsluhovat požadavky na statické soubory nebo přesměrovávání požadavků HTTP na HTTPS. Každý middleware provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek. Do metody `Startup.Configure` se přidá kód pro konfiguraci kanálu zpracování požadavků.

Tady je ukázková `Startup` třída:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Vkládání závislostí (služby)

ASP.NET Core má integrovanou architekturu pro vkládání závislostí (Dependency Injection, DI), která zpřístupňuje nakonfigurované služby třídám vaší aplikace. Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvoření konstruktoru s parametrem požadovaného typu. Tento parametr může mít typ požadované služby nebo jejího rozhraní. Systém vkládání závislostí poskytuje služby za běhu.

Zde je třída, která využívá DI pro získání kontextového objektu Entity Framework Core. Zvýrazněný řádek je příklad konstruktorového vkládání:

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

Přestože je vkládání závislostí integrováno v architektuře, je navrženo tak, aby bylo možné použít kontejner IOC (Inversion of Control) třetích stran, pokud tomu dáváte přednost.

Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Kanál pro zpracování požadavků se skládá z řady middlewarových komponent. Každá komponenta provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek.

Podle konvence je do kanálu přidána komponenta middleware, a to voláním metody rozšíření `Use...` v metodě `Startup.Configure`. Chcete-li například povolit vykreslování statických souborů, zavolejte `UseStaticFiles`.

Zvýrazněný kód v následujícím příkladu nastaví kanál pro zpracování požadavku:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

ASP.NET Core obsahuje bohatou sadu integrovaných middleware a zároveň je možné napsat také vlastní middleware.

Další informace naleznete v tématu <xref:fundamentals/middleware/index>.

## <a name="host"></a>Hostitel

Aplikace ASP.NET Core při spuštění vytvoří *hostitele* . Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:

* Implementaci HTTP serveru
* Middlewarové komponenty
* Protokolování
* DI
* Konfigurace

Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.

::: moniker range=">= aspnetcore-3.0"

K dispozici jsou dva hostitelé: obecný hostitel a webový hostitel. Doporučuje se použít obecného hostitele a webový hostitel je k dispozici pouze pro zpětnou kompatibilitu.

Kód pro vytvoření hostitele je `Program.Main`:

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

Metody `CreateDefaultBuilder` a `ConfigureWebHostDefaults` konfigurují hostitele pomocí běžně používaných možností, jako jsou například tyto:

* Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.
* Načíst konfiguraci z *appSettings. JSON*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.
* Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.

Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel. V ASP.NET Core 2. x je obecný hostitel pouze pro scénáře, které nejsou webové.

Kód pro vytvoření hostitele je `Program.Main`:

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

Metoda `CreateDefaultBuilder` Konfiguruje hostitele pomocí běžně používaných možností, jako jsou například následující:

* Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.
* Načíst konfiguraci z *appSettings. JSON*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.
* Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.

Další informace naleznete v tématu <xref:fundamentals/host/web-host>.

::: moniker-end

### <a name="non-web-scenarios"></a>Jiné než webové scénáře

Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací. Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servery

Aplikace ASP.NET Core využívá implementaci HTTP serveru pro naslouchání požadavků protokolu HTTP. Server objedná požadavky do aplikace jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do `HttpContext`.

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/). V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.
* *Http server IIS* je server pro Windows, který používá službu IIS. S tímto serverem jsou aplikace ASP.NET Core a služba IIS spuštěny v jednom společném procesu.
* *Http. sys* je server pro Windows, který se nepoužívá se službou IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/). V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.
* *Http. sys* je server pro Windows, který se nepoužívá se službou IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy. V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu. Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

Další informace naleznete v tématu <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurace

ASP.NET Core poskytuje konfigurační framework, který získává nastavení jako dvojice název-hodnota z uspořádané množiny poskytovatelů konfigurace. Existují předdefinované poskytovatelé konfigurace pro nejrůznější zdroje, například soubory *. JSON* , soubory *. XML* , proměnné prostředí a argumenty příkazového řádku. Můžete si také napsat vlastního zprostředkovatele konfigurace.

Například můžete určit, že konfigurace pochází z proměnných *appSettings. JSON* a proměnných prostředí. Poté, co je požadována hodnota *ConnectionString* , rozhraní vyhledá první v souboru *appSettings. JSON* . Pokud je hodnota nalezena v souboru, ale zároveň také v proměnné prostředí, hodnota proměnné prostředí bude mít přednost.

Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Nástroj Správce tajných klíčů](xref:security/app-secrets). V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).

Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="options"></a>Možnosti

Pokud je to možné, ASP.NET Core podle *vzoru možností* pro ukládání a načítání hodnot konfigurace. Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.

Následující kód například nastavuje možnosti Websocketů:

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Prostředí

Spouštěcí prostředí, jako je *vývoj*, *Příprava*a *produkce*, jsou první třídou pojmu v ASP.NET Core. Nastavením proměnné prostředí `ASPNETCORE_ENVIRONMENT` můžete určit prostředí, ve kterém je aplikace spuštěná. ASP.NET Core přečte tuto proměnnou prostředí při spuštění aplikace a uloží hodnotu do implementace `IHostingEnvironment`. Objekt prostředí je k dispozici kdekoli v aplikaci prostřednictvím DI.

Následující vzorový kód z `Startup` třídy nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybách pouze v případě, že běží ve vývoji:

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="logging"></a>Protokolování

ASP.NET Core podporuje API pro protokolování, které funguje s množstvím zabudovaných poskytovatelů protokolování a poskytovatelů třetích stran. Mezi dostupné poskytovatele patří:

* Konzola
* Ladění
* Trasování událostí ve Windows
* Protokol událostí systému Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Zápis protokolů z libovolného místa v kódu aplikace získáním `ILogger` objektu z DI a volání metod protokolu.

Zde je ukázkový kód, který používá objekt `ILogger`, s vkládáním konstruktoru a volání metody protokolování zvýrazněné.

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

Rozhraní `ILogger` umožňuje předat poskytovateli protokolování libovolný počet polí. Pole se běžně používají pro konstrukci textu protokolovací zprávy, ale poskytovatel je může také odesílat odděleně do úložiště dat. Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Další informace naleznete v tématu <xref:fundamentals/logging/index>.

## <a name="routing"></a>Směrování

*Trasa* je vzor URL, který je namapován na obslužnou rutinu. Obslužnou rutinou je obvykle stránka Razor, metoda akce v MVC kontroleru nebo middleware. Směřování ASP.NET Core Vám umožňuje kontrolovat adresy URL používané Vaší aplikací.

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="error-handling"></a>Zpracování chyb

ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:

* Stránka výjimek pro vývojáře
* Vlastní chybové stránky
* Statické stránky chybových kódů
* Zpracování výjimek při spuštění

Další informace naleznete v tématu <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Vytváření požadavků HTTP

Pro vytváření instancí `HttpClient` je k dispozici implementace `IHttpClientFactory`. Objekt pro vytváření (Factory):

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k GitHubu. Výchozí klient může být zaregistrován k jiným účelům.
* Podporuje registraci a řetězení více delegujících obslužných rutin k vytvoření middlewarového kanálu požadavků. Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core. Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.
* Integruje se s *Polly*oblíbenou knihovnou třetích stran pro zpracování přechodného selhání.
* Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.
* Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.

Další informace naleznete v tématu <xref:fundamentals/http-requests>.

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

Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host). Další informace naleznete v tématu <xref:fundamentals/host/generic-host#contentrootpath>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host). Další informace naleznete v tématu <xref:fundamentals/host/web-host#content-root>.

::: moniker-end

## <a name="web-root"></a>Kořenový adresář webu

Kořenový adresář webu je základní cesta k souborům statických prostředků bez kódu, jako je například:

* Šablony stylů ( *. CSS*)
* JavaScript ( *. js*)
* Obrázky ( *. png*, *. jpg*)

Statické soubory jsou obsluhovány ve výchozím nastavení pouze z kořenového adresáře webu (a podadresářů).

::: moniker range=">= aspnetcore-3.0"

Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář. Další informace naleznete v tématu <xref:fundamentals/host/generic-host#webroot>.

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

::: moniker range=">= aspnetcore-3.0"

Pokud chcete zabránit publikování prostředků statických identit do kořenového adresáře webu, přečtěte si téma <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.

::: moniker-end

V souborech Razor ( *. cshtml*) směřuje vlnové lomítko (`~/`) do kořenového adresáře webu. Cesta začínající na `~/` je označována jako *virtuální cesta*.

Další informace naleznete v tématu <xref:fundamentals/static-files>.
