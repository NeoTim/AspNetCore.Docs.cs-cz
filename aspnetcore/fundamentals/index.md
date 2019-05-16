---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se se základními koncepty pro vytváření aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: fundamentals/index
ms.openlocfilehash: a1fed574db0baab391ebb9cfc44664ceddbfa69b
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "58809286"
---
# <a name="aspnet-core-fundamentals"></a>Základy ASP.NET Core

Tento článek je přehled klíčových témat pro pochopení způsobu vývoje ASP.NET Core aplikací.

## <a name="the-startup-class"></a>Třída Startup

Třída `Startup` je místo, kde:

* Jsou nakonfigurovány všechny služby požadované aplikací.
* Je definován kanál pro zpracování požadavků (request handling pipeline).

* Kód pro konfiguraci (nebo *registraci*) služeb je přidán do metody `Startup.ConfigureServices`. *Služby* jsou komponenty, které se používají v aplikaci. Například kontextový objekt Entity Framework Core je takovou službou.
* Kód pro konfiguraci kanálu pro zpracování požadavků (request handling pipeline) je přidán do metody `Startup.Configure`. Kanál se skládá z řady *middlewarových* komponent. Middleware může třeba obsluhovat požadavky na statické soubory nebo přesměrovávání požadavků HTTP na HTTPS. Každý middleware provádí asynchronní operace na kontextu `HttpContext` a následně vyvolá další middleware v kanálu nebo ukončí požadavek.

Zde je ukázka třídy `Startup`:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Vkládání závislostí (služby)

ASP.NET Core má integrovanou architekturu pro vkládání závislostí (Dependency Injection, DI), která zpřístupňuje nakonfigurované služby třídám vaší aplikace. Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvoření konstruktoru s parametrem požadovaného typu. Tento parametr může mít typ požadované služby nebo jejího rozhraní. Systém vkládání závislostí poskytuje služby za běhu.

Zde je třída, která využívá DI pro získání kontextového objektu Entity Framework Core. Zvýrazněný řádek je příklad konstruktorového vkládání:

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

Přestože je vkládání závislostí integrováno v architektuře, je navrženo tak, aby bylo možné použít kontejner IOC (Inversion of Control) třetích stran, pokud tomu dáváte přednost.

Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Kanál pro zpracování požadavků se skládá z řady middlewarových komponent. Jednotlivé komponenty provádí asynchronní operace na kontextu `HttpContext` a následně vyvolají další middleware v kanálu nebo ukončí požadavek.

Konvenčně se komponenty middlewaru přidávají do kanálu zavoláním rozšiřující metody `Use...` v metodě `Startup.Configure`. Pro povolení vykreslování statických souborů se například volá metoda `UseStaticFiles`.

Zvýrazněný kód v následujícím příkladu nastaví kanál pro zpracování požadavku:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

ASP.NET Core obsahuje bohatou sadu integrovaných middleware a zároveň je možné napsat také vlastní middleware.

Další informace naleznete v tématu <xref:fundamentals/middleware/index>.

<a id="host"/>

## <a name="the-host"></a>Hostitel

ASP.NET Core aplikace vytváří *hostitele* při spuštění. Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:

* Implementaci HTTP serveru
* Middlewarové komponenty
* Protokolování
* DI
* Konfiguraci

Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.

Kód pro vytvoření hostitele je umístěn v `Program.Main` a implementuje [návrhový vzor Builder](https://wikipedia.org/wiki/Builder_pattern). Metody jsou volány pro konfiguraci jednotlivých prostředků, které jsou součástí hostitele. Následně je volána metoda `Build`, která vše zkonsoliduje a vytvoří instanci objektu hostitele.

::: moniker range=">= aspnetcore-3.0"

V ASP.NET Core 3.0 a novějším je ve webové aplikaci možné využít obecného hostitele (třída `Host`) nebo webového hostitele (třída `WebHost`). Doporučuje se použít obecného hostitele, webový hostitel je k dispozici pro zpětnou kompatibilitu.

Architektura poskytuje metody `CreateDefaultBuilder` a `ConfigureWebHostDefaults` pro nastavení hostitele nejčastěji používanými možnostmi nastavení, jako jsou následující:

* Použití [Kestrelu](#servers) jako webového serveru a povolení integrace do služby IIS.
* Načtení konfigurace z *appsettings.json*, *appsettings.{ Název prostředí }.json*, proměnných prostředí, argumentů příkazového řádku a dalších zdrojů konfigurace.
* Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.

Zde je ukázkový kód, který vytváří hostitele. Metody, které nastavují hostitele běžně používanými možnosti, jsou zvýrazněné:

[!code-csharp[](index/snapshots/3.x/Program1.cs?highlight=9-10)]

Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/web-host>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.x používá webového hostitele (třída `WebHost`) pro webové aplikace. Architektura poskytuje metodu `CreateDefaultBuilder` pro nastavení hostitele nejčastěji používanými možnostmi nastavení, jako jsou následující:

* Použití [Kestrel](#servers) jako webového serveru a povolení integrace do služby IIS.
* Načtení konfigurace z *appsettings.json*, *appsettings.{ Název prostředí }.json*, proměnných prostředí, argumentů příkazového řádku a dalších zdrojů konfigurace.
* Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.

Zde je ukázkový kód, který vytváří hostitele:

[!code-csharp[](index/snapshots/2.x/Program1.cs?highlight=9)]

Další informace naleznete v tématu <xref:fundamentals/host/web-host>.

::: moniker-end

### <a name="advanced-host-scenarios"></a>Pokročilé scénáře s hostitelem

::: moniker range=">= aspnetcore-3.0"

Obecný hostitel je k dispozici pro libovolnou aplikaci .NET Core &ndash; nejen pro aplikace ASP.NET Core. Obecný hostitel (třída `Host`) umožňuje jiným typům aplikací využívat společná rozšíření architektury, jako jsou například protokolování, DI, konfigurace a správa životního cyklu aplikace. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Webový hostitel je navržen pro použití s implementací HTTP serveru, který však není vyžadován u ostatních typů .NET aplikací. Od verze ASP.NET Core 2.1 je k dispozici obecný hostitel (třída `Host`) pro využití s libovolným typem .NET Core aplikace &ndash; nejen s aplikacemi ASP.NET Core. Obecný hostitel umožňuje jiným typům aplikací využívat společná rozšíření frameworku, jako jsou například protokolování, DI, konfigurace a správa životního cyklu aplikace. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

::: moniker-end

Hostitele můžete také využít pro spouštění úlohy na pozadí. Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servery

Aplikace ASP.NET Core využívá implementaci HTTP serveru pro naslouchání požadavků protokolu HTTP. Server projevuje požadavky do aplikace jako sadu [rysů požadavku](xref:fundamentals/request-features) složených do kontextu `HttpContext`.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core nabízí následující implementace serveru:

* *Kestrel* je multiplatformní webový server. Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s použitím [IIS](https://www.iis.net/). V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.
* *IIS HTTP server* je server pro systém Windows, který využívá službu IIS. S tímto serverem jsou aplikace ASP.NET Core a služba IIS spuštěny v jednom společném procesu.
* *HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy. V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu. Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy. V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu. Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core nabízí následující implementace serveru:

* *Kestrel* je multiplatformní webový server. Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s použitím [IIS](https://www.iis.net/). V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.
* *HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy. V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu. Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy. V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu. Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

Další informace naleznete v tématu <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurace

ASP.NET Core poskytuje konfigurační framework, který získává nastavení jako dvojice název-hodnota z uspořádané množiny poskytovatelů konfigurace. Existuje široká škála integrovaných poskytovatelů konfigurace, např. pro soubory *.json*, *.xml*, proměnné prostředí a argumenty příkazového řádku. Můžete si také napsat vlastního zprostředkovatele konfigurace.

Jako příklad můžete ve své aplikaci specifikovat konfiguraci s využitím *appsettings.json* a proměnných prostředí. Když je následně požadována hodnota *ConnectionString*, framework ji prvně vyhledá v souboru *appsettings.json*. Pokud je hodnota nalezena v souboru, ale zároveň také v proměnné prostředí, hodnota proměnné prostředí bude mít přednost.

Pro správu důvěrných konfiguračních dat, jako jsou hesla, poskytuje ASP.NET Core [nástroj pro správu tajemství](xref:security/app-secrets). Pro tajemství v produkčním prostředí doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).

Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="options"></a>Možnosti

ASP.NET Core využívá *vzor možností nastavení* pro ukládání a načítání hodnot konfigurace, kde je to možné. *Vzor možností nastavení* využívá třídy pro reprezentaci skupin souvisejících nastavení.

Následující kód například nastavuje možnosti Websocketů:

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Prostředí

Prostředí spuštění aplikace, jako například *Developement*, *Staging* a *Production*, je výsadní pojem v ASP.NET Core. Prostředí, ve kterém je aplikace spuštěna, můžete určit pomocí proměnné prostředí `ASPNETCORE_ENVIRONMENT`. ASP.NET Core čte tuto proměnnou při spuštění aplikace a uloží její hodnotu v implementaci `IHostingEnvironment`. Objekt prostředí je k dispozici kdekoli v aplikaci prostřednictvím DI.

Následující ukázkový kód z třídy `Startup` nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybě pouze ve vývojovém (Development) prostředí:

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="logging"></a>Protokolování

ASP.NET Core podporuje API pro protokolování, které funguje s množstvím zabudovaných poskytovatelů protokolování a poskytovatelů třetích stran. Mezi dostupné poskytovatele patří:

* Konzola
* Ladění
* Trasování událostí ve Windows
* Protokol událostí Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Zapisujte do protokolu kdekoli v kódu vaší aplikace voláním patřičných metod objektu `ILogger`, který je možné získat pomocí vkládání  závislostí.

Zde je ukázkový kód, který používá objekt `ILogger`, se zvýrazněním konstruktorového vkládání a protokolovacích metod.

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

Rozhraní `ILogger` umožňuje předat libovolný počet polí poskytovateli protokolování. Pole se běžně používají pro konstrukci textu protokolovací zprávy, ale poskytovatel je může také odesílat odděleně do úložiště dat. Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Další informace naleznete v tématu <xref:fundamentals/logging/index>.

## <a name="routing"></a>Směrování

*Trasa* (route) je vzor adresy URL, který je namapovaný na obslužnou rutinu. Obslužnou rutinou je obvykle stránka Razor, metoda akce v MVC kontroleru nebo middleware. Směřování ASP.NET Core Vám umožňuje kontrolovat adresy URL používané Vaší aplikací.

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="error-handling"></a>Zpracování chyb

ASP.NET Core má integrované funkce pro zpracování chyb, jako například:

* Stránka výjimek pro vývojáře
* Vlastní chybové stránky
* Statické stránky chybových kódů
* Zpracování výjimek při spuštění

Další informace naleznete v tématu <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Vytváření požadavků HTTP

Implementace `IHttpClientFactory` je k dispozici pro vytváření instancí `HttpClient`. Objekt pro vytváření (Factory):

* Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Například *github* klient může být zaregistrován a nakonfigurován tak, aby přistupoval ke GitHubu. Výchozí klient může být  zaregistrován k jiným účelům.
* Podporuje registraci a řetězení více delegujících obslužných rutin k vytvoření middlewarového kanálu požadavků. Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.
* Se integruje s *Polly*, oblíbenou knihovnou třetí strany pro zpracování přechodných chyb.
* Spravuje sdružování a životní cyklus instancí `HttpClientMessageHandler`, aby zabránil častým problémům s DNS, ke kterým dochází při manuální správě životnosti `HttpClient`.
* Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).

Další informace naleznete v tématu <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Kořenový adresář obsahu

Kořenový adresář obsahu je základní cesta k privátnímu obsahu používanému aplikací, kterým jsou například soubory Razor. Ve výchozím nastavení je kořenový adresář obsahu shodný se základní cestou spustitelného souboru, který je hostitelem aplikace. Alternativní umístění může být specifikováno při [vytváření hostitele](#host).

::: moniker range=">= aspnetcore-3.0"

Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/host/generic-host#content-root).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/host/web-host#content-root).

::: moniker-end

## <a name="web-root"></a>Kořenový adresář webu

Kořenový adresář webu (označovaný také jako *webroot*) je základní cesta k veřejným, statickým prostředkům, jako jsou CSS styly, JavaScript a soubory obrázků. Middleware pro statické soubory bude ve výchozím nastavení obsluhovat pouze soubory z tohoto kořenového adresáře webu (a jeho podadresářů). Výchozí hodnota kořenového adresáře webu je *{Kořenový adresář obsahu} / wwwroot*, jiné umístění však může být zadáno při [vytváření hostitele](#host).

V Razor souborech (*.cshtml*) odkazuje symbol vlnovky následované lomítkem `~/` na kořenový adresář webu. Cesty začínající `~/` jsou označovány jako virtuální cesty.

Další informace naleznete v tématu <xref:fundamentals/static-files>.
