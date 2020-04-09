---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se se základními koncepty pro vytváření ASP.NET základních aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: da2b42a7cf5d116a36d1dd9fa586d40ab31fc52d
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417643"
---
# <a name="aspnet-core-fundamentals"></a>Základy ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Tento článek obsahuje přehled klíčových témat, která popisují, jak vyvíjet ASP.NET základní aplikace.

## <a name="the-startup-class"></a>Spouštěcí třída

Třída `Startup` je, kde:

* Služby vyžadované aplikací jsou konfigurovány.
* Kanál zpracování požadavků aplikace je definován jako řada middleware komponent.

Zde je ukázka `Startup` třídy:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injektáž závislostí (služby)

ASP.NET Core obsahuje integrované rozhraní pro vkládání závislostí (DI), které zpřístupňuje nakonfigurované služby v celé aplikaci. Například součást protokolování je služba.

Kód pro konfiguraci (nebo *registr* `Startup.ConfigureServices` ) služby je přidán do metody. Příklad:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Služby jsou obvykle vyřešeny z DI pomocí vkládání konstruktoru. S vstřikování konstruktoru třída deklaruje parametr konstruktoru požadovaného typu nebo rozhraní. Rozhraní DI poskytuje instanci této služby za běhu.

Následující příklad používá vkládání konstruktoru k vyřešení a `RazorPagesMovieContext` z DI:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Pokud integrovaný kontejner Inversion of Control (IoC) nesplňuje všechny potřeby aplikace, lze místo toho použít kontejner IoC jiného výrobce.

Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Kanál zpracování požadavků se skládá jako řada middleware komponent. Každá komponenta provádí `HttpContext` operace na a buď vyvolá další middleware v kanálu nebo ukončí požadavek.

Podle konvence middleware komponenty je přidán do kanálu `Use...` vyvoláním metody `Startup.Configure` rozšíření v metodě. Chcete-li například povolit vykreslování statických souborů, volejte `UseStaticFiles`.

Následující příklad konfiguruje kanál zpracování požadavků:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core obsahuje bohatou sadu vestavěných middleware. Vlastní middleware komponenty mohou být také napsány.

Další informace naleznete v tématu <xref:fundamentals/middleware/index>.

## <a name="host"></a>Hostitel

Při spuštění vytvoří aplikace ASP.NET Core *hostitele*. Hostitel zapouzdřuje všechny prostředky aplikace, například:

* Implementace http serveru
* Komponenty middlewaru
* Protokolování
* Služby vkládání závislostí (DI)
* Konfigurace

Existují dva různí hostitelé: 

* Obecný hostitel rozhraní .NET
* ASP.NET základní webový hostitel

Obecný hostitel .NET se doporučuje. Základní webový hostitel ASP.NET je k dispozici pouze pro zpětnou kompatibilitu.

Následující příklad vytvoří obecný hostitel .NET:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

Metody `CreateDefaultBuilder` `ConfigureWebHostDefaults` a nakonfigurují hostitele se sadou výchozích možností, například:

* Jako webový server použijte [kestrel](#servers) a povolte integraci služby IIS.
* Načtěte konfiguraci z *souboru appsettings.json*, *appsettings.{ Název prostředí}.json*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.
* Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.

Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Scénáře mimo web

Obecný hostitel umožňuje jiným typům aplikací používat rozšíření architektury průřezu, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životnosti aplikací. Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servery

Aplikace ASP.NET Core používá implementaci http serveru k naslouchání požadavkům HTTP. Server zobrazí požadavky na aplikaci jako sadu [funkcí](xref:fundamentals/request-features) `HttpContext`požadavků složených do aplikace .

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel je často spuštěn v konfiguraci reverzníproxy pomocí [služby IIS](https://www.iis.net/). V ASP.NET Core 2.0 nebo novější, Kestrel lze spustit jako veřejný hraniční server vystavený přímo internetu.
* *Server HTTP služby IIS* je server systému Windows, který používá službu IIS. S tímto serverem běží aplikace ASP.NET Core a služba IIS ve stejném procesu.
* *HTTP.sys* je server pro systém Windows, který se nepoužívá ve službě IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform. V ASP.NET Jádrem 2.0 nebo novějším může Kestrel běžet jako veřejný hraniční server vystavený přímo internetu. Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform. V ASP.NET Jádrem 2.0 nebo novějším může Kestrel běžet jako veřejný hraniční server vystavený přímo internetu. Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

Další informace naleznete v tématu <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurace

ASP.NET Core poskytuje konfigurační rámec, který získá nastavení jako dvojice název-hodnota z uspořádané sady poskytovatelů konfigurace. Předdefinované zprostředkovatele konfigurace jsou k dispozici pro různé zdroje, jako jsou soubory *JSON,* soubory *XML,* proměnné prostředí a argumenty příkazového řádku. Napište vlastní zprostředkovatele konfigurace pro podporu jiných zdrojů.

[Ve výchozím nastavení](xref:fundamentals/configuration/index#default)jsou aplikace ASP.NET Core nakonfigurovány tak, aby četly z aplikace *appsettings.json*, proměnných prostředí, příkazového řádku a dalších. Při načtení konfigurace aplikace přepíší hodnoty z proměnných prostředí hodnoty z *appsettings.json*.

Upřednostňovaným způsobem čtení souvisejících hodnot konfigurace je použití [vzoru voleb](xref:fundamentals/configuration/options). Další informace naleznete v tématu [Vazba hierarchických konfiguračních dat pomocí vzoru voleb](xref:fundamentals/configuration/index#optpat).

Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Správce tajných barev](xref:security/app-secrets#secret-manager). Pro produkční tajné kódy doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).

Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Prostředí

Spuštění prostředí, jako `Development` `Staging`je `Production`například , a , jsou prvotřídní pojem v ASP.NET Core. Nastavením proměnné `ASPNETCORE_ENVIRONMENT` prostředí určete prostředí, ve které aplikace běží. ASP.NET Core přečte tuto proměnnou prostředí při `IWebHostEnvironment` spuštění aplikace a uloží hodnotu v implementaci. Tato implementace je k dispozici kdekoli v aplikaci prostřednictvím vkládání závislostí (DI).

Následující příklad konfiguruje aplikaci tak, `Development` aby poskytovala podrobné informace o chybě při spuštění v prostředí:

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="logging"></a>Protokolování

ASP.NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran. Mezi dostupné poskytovatele patří:

* Konzola
* Ladit
* Trasování událostí v systému Windows
* Protokol událostí systému Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Chcete-li vytvořit protokoly, vyřešit službu <xref:Microsoft.Extensions.Logging.ILogger%601> z vkládání závislostí <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>(DI) a volání metody protokolování, jako je například . Příklad:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Metody protokolování, jako `LogInformation` je například podpora libovolného počtu polí. Tato pole se běžně používají `string`k vytvoření zprávy , ale někteří poskytovatelé protokolování je odesílají do úložiště dat jako samostatná pole. Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Další informace naleznete v tématu <xref:fundamentals/logging/index>.

## <a name="routing"></a>Směrování

*Trasa* je vzor adresy URL, který je mapován na obslužnou rutinu. Obslužná rutina je obvykle Razor stránky, metoda akce v řadiči MVC nebo middleware. ASP.NET základní směrování vám dává kontrolu nad adresami URL používanými vaší aplikací.

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="error-handling"></a>Zpracování chyb

ASP.NET Core má vestavěné funkce pro zpracování chyb, jako jsou:

* Stránka výjimky pro vývojáře
* Vlastní chybové stránky
* Statické stavové kódové stránky
* Zpracování výjimek při spuštění

Další informace naleznete v tématu <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Vytváření požadavků HTTP

Implementace `IHttpClientFactory` je k dispozici `HttpClient` pro vytváření instancí. Továrna:

* Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí. Například zaregistrujte a nakonfigurujte klienta *githubpro* přístup k GitHubu. Zaregistrujte a nakonfigurujte výchozího klienta pro jiné účely.
* Podporuje registraci a řetězení více delegování obslužné rutiny k vytvoření kanálu middleware odchozí požadavek. Tento vzor je podobný ASP.NET příchozím middlewarovým kanálem core. Vzor poskytuje mechanismus pro správu průřezové obavy pro požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.
* Integruje se s *Polly*, oblíbenou knihovnou třetích stran pro přechodnou manipulaci s chybami.
* Spravuje sdružování a `HttpClientHandler` životnost základních instancí, aby `HttpClient` se zabránilo běžným problémům DNS, ke kterým dochází při ruční správě životnosti.
* Přidá konfigurovatelné prostředí <xref:Microsoft.Extensions.Logging.ILogger> protokolování prostřednictvím všech požadavků odeslaných prostřednictvím klientů vytvořených výrobcem.

Další informace naleznete v tématu <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Kořen obsahu

Kořen obsahu je základní cesta pro:

* Spustitelný soubor hostující aplikaci (*.exe*).
* Kompilovaná sestavení, která tvoří aplikaci (*DLL*).
* Soubory obsahu používané aplikací, například:
  * Razor soubory (*.cshtml*, *.razor*)
  * Konfigurační soubory (*.json*, *.xml*)
  * Datové soubory (*.db*)
* [Kořenový adresář webu](#web-root), obvykle složka *wwwroot.*

Během vývoje je kořenový obsah výchozí kořenový adresář projektu. Tento adresář je také základní cestou pro soubory obsahu aplikace i [pro kořenový adresář webu](#web-root). Zadejte jiný kořen obsahu nastavením jeho cesty při [vytváření hostitele](#host). Další informace naleznete v [tématu Kořenový obsah](xref:fundamentals/host/generic-host#contentrootpath-1).

## <a name="web-root"></a>Kořenový adresář webu

Kořenový adresář webu je základní cesta pro veřejné statické soubory prostředků, například:

* Šablony stylů (*.css*)
* JavaScript (*.js*)
* Obrázky (*.png*, *.jpg*)

Ve výchozím nastavení jsou statické soubory obsluhovány pouze z kořenového adresáře webu a jeho podadresářů. Webová kořenová cesta je výchozí na *{content root}/wwwroot*. Zadejte jiný kořen webu nastavením jeho cesty při [vytváření hostitele](#host). Další informace naleznete v [tématu Kořenový adresář webu](xref:fundamentals/host/generic-host#webroot-1).

Zabránit publikování souborů v *wwwroot* s [ \<položkou projektu Content>](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu. Následující příklad brání publikování obsahu v *wwwroot/local* a jeho podadresářích:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

V souborech Razor *.cshtml* ukazuje`~/`vlnovka ( ) na kořenový adresář webu. Cesta začínající `~/` programem se označuje jako *virtuální cesta*.

Další informace naleznete v tématu <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek je přehled klíčových témat pro pochopení, jak vyvíjet ASP.NET základní aplikace.

## <a name="the-startup-class"></a>Spouštěcí třída

Třída `Startup` je, kde:

* Služby vyžadované aplikací jsou konfigurovány.
* Je definován kanál zpracování požadavků.

*Služby* jsou součásti, které aplikace používá. Například součást protokolování je služba. Kód pro konfiguraci (nebo *registr* `Startup.ConfigureServices` ) služby je přidán do metody.

Kanál zpracování požadavků se skládá jako řada *middleware* komponent. Middleware může například zpracovávat požadavky na statické soubory nebo přesměrovat požadavky HTTP na protokol HTTPS. Každý middleware provádí asynchronní `HttpContext` operace na a potom buď vyvolá další middleware v kanálu nebo ukončí požadavek. Kód pro konfiguraci kanálu zpracování `Startup.Configure` požadavků je přidán do metody.

Zde je ukázka `Startup` třídy:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injektáž závislostí (služby)

ASP.NET Core má integrované rozhraní vkládání závislostí (DI), které zpřístupňuje nakonfigurované služby pro třídy aplikace. Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvoření konstruktoru s parametrem požadovaného typu. Parametr emituje typ služby nebo rozhraní. Systém DI poskytuje službu za běhu.

Tady je třída, která používá DI k získání objektu kontextu Core entity frameworku. Zvýrazněná čára je příkladem vstřikování konstruktoru:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Zatímco DI je vestavěný, je navržen tak, aby vám umožní připojit kontejner Inversion of Control (IoC) třetí strany, pokud dáváte přednost.

Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Kanál zpracování požadavků se skládá jako řada middleware komponent. Každá komponenta provádí asynchronní `HttpContext` operace na a potom buď vyvolá další middleware v kanálu nebo ukončí požadavek.

Podle konvence middleware komponenty je přidán do kanálu `Use...` vyvoláním jeho `Startup.Configure` rozšíření metody v metodě. Chcete-li například povolit vykreslování statických souborů, volejte `UseStaticFiles`.

Zvýrazněný kód v následujícím příkladu konfiguruje kanál zpracování požadavků:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core obsahuje bohatou sadu vestavěných middleware, a můžete psát vlastní middleware.

Další informace naleznete v tématu <xref:fundamentals/middleware/index>.

## <a name="host"></a>Hostitel

Aplikace ASP.NET Core vytvoří *hostitele* při spuštění. Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, například:

* Implementace http serveru
* Komponenty middlewaru
* Protokolování
* Di
* Konfigurace

Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.

K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel. V ASP.NET Core 2.x, obecný hostitel je pouze pro scénáře mimo web.

Kód pro vytvoření hostitele `Program.Main`je v :

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

Metoda `CreateDefaultBuilder` konfiguruje hostitele s běžně používanými možnostmi, například následujícími:

* Jako webový server použijte [kestrel](#servers) a povolte integraci služby IIS.
* Načtěte konfiguraci z *souboru appsettings.json*, *appsettings.{ Název prostředí}.json*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.
* Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.

Další informace naleznete v tématu <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Scénáře mimo web

Obecný hostitel umožňuje jiným typům aplikací používat rozšíření architektury průřezu, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životnosti aplikací. Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servery

Aplikace ASP.NET Core používá implementaci http serveru k naslouchání požadavkům HTTP. Server zobrazí požadavky na aplikaci jako sadu [funkcí](xref:fundamentals/request-features) `HttpContext`požadavků složených do aplikace .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel je často spuštěn v konfiguraci reverzníproxy pomocí [služby IIS](https://www.iis.net/). Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu.
* *Server HTTP služby IIS* je server pro systém Windows, který používá službu IIS. S tímto serverem běží aplikace ASP.NET Core a služba IIS ve stejném procesu.
* *HTTP.sys* je server pro systém Windows, který se nepoužívá ve službě IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform. Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu. Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform. Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu. Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core poskytuje následující implementace serveru:

* *Kestrel* je webový server pro různé platformy. Kestrel je často spuštěn v konfiguraci reverzníproxy pomocí [služby IIS](https://www.iis.net/). Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu.
* *HTTP.sys* je server pro systém Windows, který se nepoužívá ve službě IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform. Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu. Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform. Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu. Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Další informace naleznete v tématu <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurace

ASP.NET Core poskytuje konfigurační rámec, který získá nastavení jako dvojice název-hodnota z uspořádané sady poskytovatelů konfigurace. Existují předdefinovaní zprostředkovatelé konfigurace pro různé zdroje, jako jsou soubory *JSON,* soubory *XML,* proměnné prostředí a argumenty příkazového řádku. Můžete také psát vlastní zprostředkovatele konfigurace.

Můžete například určit, že konfigurace pochází z *proměnných appsettings.json* a prostředí. Potom při požadavku na hodnotu *ConnectionString,* rozhraní nejprve vypadá v souboru *appsettings.json.* Pokud je hodnota nalezena tam, ale také v proměnné prostředí, hodnota z proměnné prostředí by mít přednost.

Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [nástroj Správce tajných barev](xref:security/app-secrets). Pro produkční tajné kódy doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).

Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="options"></a>Možnosti

Pokud je to možné, ASP.NET Jádro se řídí *vzorem možností* pro ukládání a načítání hodnot konfigurace. Vzor voleb používá třídy k reprezentaci skupin souvisejících nastavení.

Například následující kód nastavuje možnosti WebSockets:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Prostředí

Spuštění prostředí, jako je *například vývoj*, *staging*a *výroba*, jsou prvotřídní pojem v ASP.NET core. Prostředí, ve které aplikace běží, můžete `ASPNETCORE_ENVIRONMENT` určit nastavením proměnné prostředí. ASP.NET Core přečte tuto proměnnou prostředí při `IHostingEnvironment` spuštění aplikace a uloží hodnotu v implementaci. Objekt prostředí je k dispozici kdekoli v aplikaci přes DI.

Následující ukázkový kód `Startup` z třídy konfiguruje aplikaci tak, aby poskytovala podrobné informace o chybě pouze při spuštění ve vývoji:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="logging"></a>Protokolování

ASP.NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran. Mezi dostupné poskytovatele patří následující:

* Konzola
* Ladit
* Trasování událostí v systému Windows
* Protokol událostí systému Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Zápis protokolů z libovolného místa v `ILogger` kódu aplikace získáním objektu z DI a volání mj.

Zde je ukázkový kód, který používá `ILogger` objekt, s vkládání konstruktoru a volání metody protokolování zvýrazněny.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

Rozhraní `ILogger` umožňuje předat libovolný počet polí zprostředkovateli protokolování. Pole se běžně používají k vytvoření řetězce zprávy, ale zprostředkovatel je může také odeslat jako samostatná pole do úložiště dat. Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Další informace naleznete v tématu <xref:fundamentals/logging/index>.

## <a name="routing"></a>Směrování

*Trasa* je vzor adresy URL, který je mapován na obslužnou rutinu. Obslužná rutina je obvykle Razor stránky, metoda akce v řadiči MVC nebo middleware. ASP.NET základní směrování vám dává kontrolu nad adresami URL používanými vaší aplikací.

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="error-handling"></a>Zpracování chyb

ASP.NET Core má vestavěné funkce pro zpracování chyb, jako jsou:

* Stránka výjimky pro vývojáře
* Vlastní chybové stránky
* Statické stavové kódové stránky
* Zpracování výjimek při spuštění

Další informace naleznete v tématu <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Vytváření požadavků HTTP

Implementace `IHttpClientFactory` je k dispozici `HttpClient` pro vytváření instancí. Továrna:

* Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí. Například klient *githubu* může být registrován a nakonfigurován pro přístup k GitHubu. Výchozího klienta lze zaregistrovat pro jiné účely.
* Podporuje registraci a řetězení více delegování obslužné rutiny k vytvoření kanálu middleware odchozí požadavek. Tento vzor je podobný příchozí middleware kanálu v ASP.NET Core. Vzor poskytuje mechanismus pro správu průřezové obavy kolem požadavků HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.
* Integruje se s *Polly*, oblíbenou knihovnou třetích stran pro přechodnou manipulaci s chybami.
* Spravuje sdružování a `HttpClientHandler` životnost základních instancí, aby se `HttpClient` zabránilo běžným problémům DNS, ke kterým dochází při ruční správě životnosti.
* Přidá konfigurovatelné prostředí protokolování (přes) `ILogger`pro všechny požadavky odeslané prostřednictvím klientů vytvořených výrobcem.

Další informace naleznete v tématu <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Kořen obsahu

Kořen obsahu je základní cesta k:

* Spustitelný soubor hostující aplikaci (*.exe*).
* Kompilovaná sestavení, která tvoří aplikaci (*DLL*).
* Soubory obsahu, které aplikace nepoužívá kód, například:
  * Razor soubory (*.cshtml*, *.razor*)
  * Konfigurační soubory (*.json*, *.xml*)
  * Datové soubory (*.db*)
* [Kořenový adresář webu](#web-root), obvykle publikovaná složka *wwwroot.*

Během vývoje:

* Kořenový obsah je výchozí pro kořenový adresář projektu.
* Kořenový adresář projektu se používá k vytvoření:
  * Cesta k souborům obsahu aplikace bez kódu v kořenovém adresáři projektu.
  * [Kořenový adresář webu](#web-root), obvykle složka *wwwroot* v kořenovém adresáři projektu.

Alternativní kořenová cesta obsahu může být [zadána při vytváření hostitele](#host). Další informace naleznete v tématu <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Kořenový adresář webu

Kořenový adresář webu je základní cesta k veřejným, nekódovým, statickým souborům prostředků, například:

* Šablony stylů (*.css*)
* JavaScript (*.js*)
* Obrázky (*.png*, *.jpg*)

Statické soubory jsou ve výchozím nastavení obsluhovány pouze z kořenového adresáře (a podadresářů).

Web root cesta výchozí *{content root}/wwwroot*, ale jiný kořen ový web lze zadat při [vytváření hostitele](#host). Další informace naleznete v [tématu Kořenový adresář webu](xref:fundamentals/host/web-host#web-root).

Zabránit publikování souborů v *wwwroot* s [ \<položkou projektu Content>](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu. Následující příklad brání publikování obsahu v adresáři *wwwroot/local* a podadresářích:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

V souborech Razor (*.cshtml*)`~/`ukazuje vlnoboze ( ) na kořen webu. Cesta začínající `~/` programem se označuje jako *virtuální cesta*.

Další informace naleznete v tématu <xref:fundamentals/static-files>.

::: moniker-end
