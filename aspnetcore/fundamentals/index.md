---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se se základními koncepty pro vytváření aplikací ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/index
---
# <a name="aspnet-core-fundamentals"></a>Základy ASP.NET Core

Tento článek je přehled o klíčových témata pro pochopení způsobu, jak vyvíjet aplikace ASP.NET Core.

## <a name="the-startup-class"></a>Třída Startup

`Startup` Třídy je tam, kde:

* Všechny služby nezbytné aplikací jsou nakonfigurované.
* Žádost o zpracování kanálu je definována.

* Kód ke konfiguraci (nebo *zaregistrovat*) služby se přidá do `Startup.ConfigureServices` metody. *Služby* jsou komponenty, které používají aplikace. Objekt kontextu Entity Framework Core je například služba.
* Kód ke konfiguraci požadavku zpracování kanálu je přidán do `Startup.Configure` metody. Kanál se skládá jako řadu objektů *middleware* komponenty. Middleware může třeba obsluhovat požadavky na statické soubory nebo přesměrovávání požadavků HTTP na HTTPS. Každý middleware provádí asynchronní operace `HttpContext` a potom vyvolá další middleware v kanálu nebo ukončí požadavku.

::: moniker range=">= aspnetcore-2.0"

Tady je ukázka `Startup` třídy:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

::: moniker-end

Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).

## <a name="dependency-injection-services"></a>Vkládání závislostí (služby)

ASP.NET Core má integrované závislost vkládání (DI) rozhraní tohoto umožňuje nakonfigurovat služby k dispozici na třídy vaší aplikace. Jeden způsob, jak získat instanci služby ve třídě je vytvořit konstruktor s parametrem požadovaného typu. Tento parametr může být typ služby nebo rozhraní. Poskytuje tento systém DI služby za běhu.

::: moniker range=">= aspnetcore-2.0"

Tady je třída, která používá DI jak získat objekt kontextu Entity Framework Core. Zvýrazněný řádek je příklad vkládání konstruktor:

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

::: moniker-end

Přestože je součástí DI, je navržena tak, aby modulu plug-in v kontejneru řízení IOC (Inversion) třetích stran, pokud dáváte přednost.

Další informace najdete v tématu [injektáž závislostí](xref:fundamentals/dependency-injection).

## <a name="middleware"></a>Middleware

Žádost o zpracování kanálu se skládá jako řadu objektů middlewarových komponent. Jednotlivé komponenty provádí asynchronní operace `HttpContext` a potom vyvolá další middleware v kanálu nebo ukončí požadavku.

Podle konvence komponenty middlewaru se přidá do kanálu vyvoláním jeho `Use...` metody rozšíření v `Startup.Configure` metody. Například pokud chcete povolit vykreslování statických souborů, volání `UseStaticFiles`.

::: moniker range=">= aspnetcore-2.0"

Zvýrazněný kód v následujícím příkladu nastaví požadavek zpracování kanálu:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

::: moniker-end

ASP.NET Core obsahuje bohatou sadu integrovaných middleware a můžete napsat vlastního middlewaru.

Další informace najdete v tématu [Middleware](xref:fundamentals/middleware/index).

<a id="host"/>

## <a name="the-host"></a>Hostitel

Sestavení aplikace ASP.NET Core *hostitele* při spuštění. Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:

* Implementaci serveru HTTP
* Middlewarových komponent
* Protokolování
* DI
* Konfigurace

Hlavním důvodem pro všechny aplikace vzájemně závislých prostředků včetně do jednoho objektu je správa životního cyklu: kontrolu nad spuštění aplikace a řádné vypnutí.

Kód pro vytvoření hostitele je v `Program.Main` a řídí [Tvůrce modelu](https://wikipedia.org/wiki/Builder_pattern). Metody jsou volány ke konfiguraci jednotlivých prostředků, které je součástí hostitele. Tvůrce metoda je volána k stáhnout všechno dohromady a vytvoření instance objektu hostitele.

::: moniker range="<= aspnetcore-2.2"

ASP.NET Core 2.x používá webového hostitele ( `WebHost` třída) pro webové aplikace. Poskytuje rozhraní `CreateDefaultBuilder` rozšiřující metody, které nastavení hostitele se běžně používá možnosti, jako je následující:

* Použití [Kestrel](#servers) jako webového serveru a povolení integrace služby IIS.
* Načtení konfigurace z *appsettings.json*, proměnné, argumenty příkazového řádku a dalších zdrojů.
* Odeslání výstupu protokolování do konzoly a ladění zprostředkovatelů.

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

Tady je ukázkový kód, který vytváří hostitele:

[!code-csharp[](index/snapshots/2.x/Program1.cs?highlight=9)]

Další informace najdete v tématu [webového hostitele](xref:fundamentals/host/web-host).

::: moniker-end

::: moniker range="> aspnetcore-2.2"

V ASP.NET Core 3.0 webového hostitele (`WebHost` třídy) nebo obecný hostitele (`Host` třídy) je možné ve webové aplikaci. Obecný hostitele se doporučuje a webového hostitele je k dispozici pro zpětnou kompatibilitu.

Poskytuje rozhraní `CreateDefaultBuilder` a `ConfigureWebHostDefaults` rozšiřující metody, které nastavení hostitele se běžně používá možnosti, jako je následující:

* Použití [Kestrel](#servers) jako webového serveru a povolení integrace služby IIS.
* Načtení konfigurace z *appsettings.json*, *appsettings. [ EnvironmentName] .json*, proměnné a argumenty příkazového řádku.
* Odeslání výstupu protokolování do konzoly a ladění zprostředkovatelů.

Tady je ukázkový kód, který vytváří hostitele. Rozšiřující metody, které hostitele s běžně používané možnosti jsou zvýrazněné.

[!code-csharp[](index/snapshots/3.x/Program1.cs?highlight=9-10)]

Další informace najdete v tématu [obecný hostitele](xref:fundamentals/host/generic-host) a [webového hostitele](xref:fundamentals/host/web-host)

::: moniker-end

### <a name="advanced-host-scenarios"></a>Pokročilé hostitele scénáře

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Webový hostitel je navržena pro zahrnutí implementaci serveru HTTP, který není nutný pro ostatní typy aplikací .NET. Od verze 2.1, obecný hostitele (`Host` třídy) je k dispozici pro libovolnou aplikaci .NET Core používat&mdash;nejen aplikace ASP.NET Core. Obecný hostiteli vám umožní používat společné funkce, jako je například protokolování, Správa životního cyklu DI, konfigurace a aplikace v jiných typech aplikací. Další informace najdete v tématu [obecný hostitele](xref:fundamentals/host/generic-host).

::: moniker-end

::: moniker range="> aspnetcore-2.2"

Není k dispozici pro libovolnou aplikaci .NET Core používat obecný hostitel&mdash;nejen aplikace ASP.NET Core. Obecný hostiteli vám umožní používat společné funkce, jako je například protokolování, Správa životního cyklu DI, konfigurace a aplikace v jiných typech aplikací. Další informace najdete v tématu [obecný hostitele](xref:fundamentals/host/generic-host).

::: moniker-end

Hostitele taky můžete spouštět úlohy na pozadí. Další informace najdete v tématu [úloh na pozadí](xref:fundamentals/host/hosted-services).

## <a name="servers"></a>Servery

Aplikace ASP.NET Core využívá implementaci serveru HTTP pro naslouchání požadavků protokolu HTTP. Požadavky serveru plochy na aplikaci jako sada [funkce požadavků](xref:fundamentals/request-features) složený do `HttpContext`.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core nabízí následující implementace serveru:

* *Kestrel* je multiplatformní webový server. Pomocí konfigurace reverzního proxy serveru je často spustit kestrel [IIS](https://www.iis.net/). V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu.
* *Server služby IIS HTTP* je server pro systém windows, který používá službu IIS. S tímto serverem aplikace ASP.NET Core a IIS spuštění v rámci stejného procesu.
* *Ovladač HTTP.sys* je server pro Windows, která se nepoužívá se službou IIS.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core nabízí *Kestrel* implementaci serveru pro různé platformy. V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu. Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core nabízí *Kestrel* implementaci serveru pro různé platformy. V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu. Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core nabízí následující implementace serveru:

* *Kestrel* je multiplatformní webový server. Pomocí konfigurace reverzního proxy serveru je často spustit kestrel [IIS](https://www.iis.net/). V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu.
* *Ovladač HTTP.sys* je server pro Windows, která se nepoužívá se službou IIS.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core nabízí *Kestrel* implementaci serveru pro různé platformy. V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu. Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core nabízí *Kestrel* implementaci serveru pro různé platformy. V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu. Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](http://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

Další informace najdete v tématu [servery](xref:fundamentals/servers/index).

## <a name="configuration"></a>Konfigurace

ASP.NET Core poskytuje rozhraní konfigurace, která získá nastavení jako dvojice název hodnota ze seřazené sady poskytovatelů konfigurace. Integrovaná konfigurace poskytovatelů pro širokou škálu zdrojů, jako jsou *.json* soubory, *.xml* souborů, proměnných prostředí a argumenty příkazového řádku. Můžete taky psát vlastní zprostředkovatelé konfigurace.

Například můžete zadat, že konfigurace pochází z *appsettings.json* a proměnných prostředí. Následně když hodnota *ConnectionString* je požadováno rozhraní vypadá v první *appsettings.json* souboru. Pokud je nalezena hodnota existuje, ale také v proměnné prostředí, hodnotu proměnné prostředí by měl přednost.

Pro správu důvěrné konfigurační data, jako jsou hesla, ASP.NET Core nabízí [nástroj tajný klíč správce](xref:security/app-secrets). Pro tajné kódy v produkčním prostředí, doporučujeme [Azure Key Vault](/aspnet/core/security/key-vault-configuration).

Další informace najdete v tématu [konfigurace](xref:fundamentals/configuration/index).

## <a name="options"></a>Možnosti

Kde je to možné, následuje ASP.NET Core *možnosti vzor* pro ukládání a načítání hodnot konfigurace. Možnosti vzor používá k reprezentování skupiny související nastavení třídy.

Například následující kód nastaví objekty Websocket možnosti:

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

Další informace najdete v tématu [možnosti](xref:fundamentals/configuration/options).

## <a name="environments"></a>Prostředí

Spouštěcí prostředí, jako například *vývoj*, *pracovní*, a *produkční*, jsou hodnoty první třídy v ASP.NET Core. Můžete určit nastavením je spuštěno prostředí aplikace `ASPNETCORE_ENVIRONMENT` proměnné prostředí. Přečte tuto proměnnou prostředí při spuštění aplikace ASP.NET Core a uloží hodnotu v `IHostingEnvironment` implementace. Objekt prostředí je k dispozici kdekoli v aplikaci prostřednictvím DI.

::: moniker range=">= aspnetcore-2.0"

Následující ukázkový kód z `Startup` třída nakonfiguruje aplikaci pouze při spuštění ve vývoji poskytnout podrobné informace o chybě:

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

::: moniker-end

Další informace najdete v tématu [prostředí](xref:fundamentals/environments).

## <a name="logging"></a>Protokolování

ASP.NET Core podporuje protokolování rozhraní API, která funguje s různých poskytovatelů třetích stran a vestavěné protokolování. Dostupní zprostředkovatelé patří:

* Konzola
* Ladit
* Trasování událostí ve Windows
* Protokol událostí Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Zápis protokoly z kdekoli v kódu vaší aplikace s informacemi `ILogger` objekt z DI a volání metod protokolu.

::: moniker range=">= aspnetcore-2.0"

Tady je ukázkový kód, který se používá `ILogger` objekt konstruktoru vkládání a volání metody protokolování zvýrazní.

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

::: moniker-end

`ILogger` Rozhraní umožňuje předat libovolný počet polí do zprostředkovatele. Pole se běžně používají k vytvoření řetězec zprávy, ale poskytovateli jim také poslat jako oddělovače do úložiště dat. Tato funkce umožňuje protokolování poskytovatelé k implementaci [sémantického protokolování, označovaného také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Další informace najdete v tématu [protokolování](xref:fundamentals/logging/index).

## <a name="routing"></a>Směrování

A *trasy* je vzor adresy URL, který je namapovaný na obslužnou rutinu. Obslužná rutina se obvykle stránky Razor, metodu akce v kontroleru MVC nebo middleware. ASP.NET Core směrování získáte tak kontrolu nad adresy URL, která vaše aplikace používá.

Další informace najdete v tématu [směrování](xref:fundamentals/routing).

## <a name="error-handling"></a>Zpracování chyb

ASP.NET Core má integrované funkce pro zpracování chyb, jako například:

* Na stránce výjimek pro vývojáře
* Vlastní chybové stránky
* Statický stav znakové stránky
* Zpracování výjimek při spuštění

Další informace najdete v tématu [zpracování chyb](xref:fundamentals/error-handling).

::: moniker range=">= aspnetcore-2.1"

## <a name="make-http-requests"></a>Vytváření požadavků HTTP

Implementace `IHttpClientFactory` je k dispozici pro vytváření `HttpClient` instancí. Objekt pro vytváření:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logické `HttpClient` instancí. Například *githubu* klient může zaregistrované a nakonfigurovat tak, aby ke Githubu přistupovat. Výchozí klienta lze zaregistrovat k jiným účelům.
* Podporuje registraci a řetězení více Delegující obslužných rutin k sestavení kanál middleware odchozí požadavek. Tento model je podobný kanál příchozí middlewaru v ASP.NET Core. Vzor poskytuje mechanismus ke správě vyskytující aspekty kolem požadavků protokolu HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializaci a protokolování.
* Se integruje s *Polly*, Oblíbené knihovny třetí strany pro zpracování přechodných chyb.
* Spravuje sdružování a dobu života základní `HttpClientMessageHandler` instancí se vyhnout běžným potížím DNS, ke kterým dochází při správě ručně `HttpClient` životnosti.
* Přidá prostředí konfigurovat protokolování (prostřednictvím *ILogger*) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny procesem.

Další informace najdete v tématu [požadavky HTTP zkontrolujte](xref:fundamentals/http-requests).

::: moniker-end

## <a name="content-root"></a>Kořen obsahu

Obsahu kořenový adresář je základní cesta k privátní obsahu používat aplikace, jako jsou soubory Razor. Ve výchozím nastavení je obsah kořenové základní cestu pro spustitelný soubor, který je hostitelem aplikace. Alternativní umístění může být zadán při [vytváření hostitele](#host).

::: moniker range="<= aspnetcore-2.2"

Další informace najdete v tématu [obsahu kořenové](xref:fundamentals/host/web-host#content-root).

::: moniker-end

::: moniker range="> aspnetcore-2.2"

Další informace najdete v tématu [obsahu kořenové](xref:fundamentals/host/generic-host#content-root).

::: moniker-end

## <a name="web-root"></a>Kořen webu

Kořenový adresář webové (označované také jako *webroot*) je základní cesta pro veřejné, statické prostředky, jako jsou šablony stylů CSS, JavaScript a soubory obrázků. Statické soubory middleware bude sloužit pouze soubory z kořenové složky webu (a v podadresářích) ve výchozím nastavení. Kořenová cesta webové výchozí hodnota je  *\<obsahu root > / wwwroot*, ale jiné umístění může být zadán při [vytváření hostitele](#host).

V prostředí Razor (*.cshtml*) soubory tilda lomítky `~/` odkazuje na kořenový web. Cesty začínající `~/` se označují jako virtuální cesty.

Další informace najdete v tématu [statické soubory](xref:fundamentals/static-files).
