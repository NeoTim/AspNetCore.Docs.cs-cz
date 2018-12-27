---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se základními koncepty pro vytváření aplikací ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/index
ms.openlocfilehash: 11dc6336ae7667038983c967f28232bef325f5bb
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637765"
---
# <a name="aspnet-core-fundamentals"></a>Základy ASP.NET Core

Aplikace ASP.NET Core je konzolová aplikace, která vytvoří webovým serverem v jeho `Program.Main` metoda. `Main` Aplikace je metoda *spravované vstupní bod*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs)]

.NET Core hostitele:

* Načtení [.NET Core runtime](https://github.com/dotnet/coreclr).
* První argument příkazového řádku používá jako cestu pro spravované binární soubor, který obsahuje vstupní bod (`Main`) a zahájí provádění kódu.

V metodě `Main` se volá metoda [WebHost.CreateDefaultBuilder](xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*), která poskytuje implementaci návrhového vzoru [Builder](https://wikipedia.org/wiki/Builder_pattern) a umožňuje tak sestavení hostitele webové aplikace. Tvůrce obsahuje metody, které definují webového serveru (například <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) a třída při spuštění (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>). Ve výše uvedeném příkladu je automaticky použit webový server [Kestrel](xref:fundamentals/servers/kestrel). Webového hostitele ASP.NET Core se pokusí o spuštění [Internetové informační služby (IIS)](https://www.iis.net/), pokud je k dispozici. Voláním patřičné rozšiřující metody je možné aplikaci hostovat i na jiných webových serverech, jako například [HTTP.sys](xref:fundamentals/servers/httpsys). `UseStartup` je vysvětleno dále v [spuštění](#startup) oddílu.

Typ <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, který je vrácen jako výsledek volání `WebHost.CreateDefaultBuilder`, obsahuje mnoho volitelných metod. Jednou z těchto metod je `UseHttpSys` pro hostování aplikace pomocí HTTP.sys a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> pro určení kořenového adresáře s obsahem. Metody <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> slouží k sestavení objektu <xref:Microsoft.AspNetCore.Hosting.IWebHost>, který je hostitelem aplikace, resp. zahájí naslouchání HTTP požadavků.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs)]

.NET Core hostitele:

* Načtení [.NET Core runtime](https://github.com/dotnet/coreclr).
* První argument příkazového řádku používá jako cestu pro spravované binární soubor, který obsahuje vstupní bod (`Main`) a zahájí provádění kódu.

V metodě `Main` se používá třída <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, která implementuje návrhový vzor [Builder](https://wikipedia.org/wiki/Builder_pattern) a umožňuje tak sestavení hostitele webové aplikace. Samotný Builder obsahuje metody, které specifikují webový server (například <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>), který se má použít, a třídu pro spuštění (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>). Ve výše uvedeném příkladu je použit webový server [Kestrel](xref:fundamentals/servers/kestrel). Voláním patřičné rozšiřující metody je možné aplikaci hostovat i na jiných webových serverech, jako například [HTTP.sys](xref:fundamentals/servers/httpsys). `UseStartup` je vysvětleno dále v [spuštění](#startup) oddílu.

Třída `WebHostBuilder` obsahuje mnoho volitelných metod. Jednou z těchto metod je <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> pro hostování aplikace pomocí služby IIS nebo IIS Express a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> pro určení kořenového adresáře s obsahem. Metody <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> slouží k sestavení objektu <xref:Microsoft.AspNetCore.Hosting.IWebHost>, který je hostitelem aplikace, resp. zahájí naslouchání HTTP požadavků.

::: moniker-end

## <a name="startup"></a>Třída pro spuštění

Metoda `UseStartup` třídy `WebHostBuilder` určuje spouštěcí třídu `Startup` Vaší aplikace:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs?highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs?highlight=7)]

::: moniker-end

Ve třídě `Startup` můžete definovat kanál zpracování požadavků a nakonfigurovat všechny služby, které aplikace vyžaduje. Třída `Startup` musí být veřejná a musí obsahovat následující metody:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Startup.cs)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Startup.cs)]

::: moniker-end

<xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> definuje [služby](#dependency-injection-services) používané v aplikaci (například ASP.NET, Core MVC, Entity Framework Core, Identity). <xref:Microsoft.AspNetCore.Hosting.IStartup.Configure*> definuje [middlewary](xref:fundamentals/middleware/index) pro kanál zpracování požadavků.

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="content-root"></a>Kořen obsahu

Kořen obsahu (Content Root) je bázová cesta k libovolnému obsahu využívaného aplikací, jako jsou například pohledy, [Stránky Razor](xref:razor-pages/index) a statický obsah (obrázky apod.). Ve výchozím nastavení je kořenu obsahu stejný jako bázová cesta aplikace ke spustitelnému souboru, který je hostitelem aplikace.

## <a name="web-root-webroot"></a>Kořenový adresář webové (webroot)

Webroot aplikace je adresář, do projektu obsahující veřejné, statické prostředky, jako jsou šablony stylů CSS, JavaScript a soubory obrázků. Ve výchozím nastavení *wwwroot* je webroot.

Pro syntaxi Razor (*.cshtml*) soubory tilda lomítky `~/` odkazuje webroot. Cesty začínající `~/` se označují jako virtuální cesty.

## <a name="dependency-injection-services"></a>Vkládání závislostí (služby)

*Služba* je komponenta, která je určená pro společné používání v rámci aplikace. Služby jsou zpřístupněny prostřednictvím [vkládání závislosti](xref:fundamentals/dependency-injection). ASP.NET Core zahrnuje nativní kontejner pro inverzi závislostí, který podporuje [vkládání závislostí pomocí konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection). Nativní kontejner je možné nahradit jiným. Výhoda použití vkládání závislostí spočívá ve [volnějším propojení jednotlivých komponent](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) a zpřístupnění služeb (jako je třeba [protokolování](xref:fundamentals/logging/index)) všude v aplikaci.

Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

V ASP.NET Core budujete kanál zpracování požadavků pomocí [middlewarů](xref:fundamentals/middleware/index). ASP.NET Core middleware nejprve provádí asynchronní operace nad kontextem `HttpContext`, a následně může předat řízení dalšímu middlewaru v pořadí nebo okamžitě ukončit zpracování HTTP požadavku.

Middlewarová komponenta s názvem "XYZ" se přidá pomocí volání rozšiřující metody `UseXYZ` uvnitř metody `Configure`.

ASP.NET Core obsahuje pestrý výběr vestavěných middlewarů, nebo můžete použít vlastní middleware. [Otevřete Web Interface pro .NET (OWIN)](xref:fundamentals/owin), který umožňuje webové aplikace k oddělení od webové servery, je podporováno v aplikacích pro ASP.NET Core.

Další informace naleznete v tématu <xref:fundamentals/middleware/index> a <xref:fundamentals/owin>.

::: moniker range=">= aspnetcore-2.1"

## <a name="initiate-http-requests"></a>Iniciování HTTP požadavků

<xref:System.Net.Http.IHttpClientFactory> je k dispozici pro přístup k <xref:System.Net.Http.HttpClient> instance požadavky HTTP.

Další informace naleznete v tématu <xref:fundamentals/http-requests>.

::: moniker-end

## <a name="environments"></a>Prostředí

ASP.NET Core umožňuje rozlišení vývojového *Development* a produkčního *Production* prostředí, konkrétní prostředí lze nastavit pomocí proměnných prostředí.

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="hosting"></a>Hostování

Aplikace ASP.NET Core konfiguruje a spouští *hostitele*, který je zodpovědný za správu životního cyklu aplikace.

Další informace naleznete v tématu <xref:fundamentals/host/index>.

## <a name="servers"></a>Servery

ASP.NET Core nenaslouchá přímo HTTP požadavkům. Spoléhá na HTTP server, který aplikaci předává jednotlivé požadavky.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core nabízí následující implementace serveru:

* [Kestrel](xref:fundamentals/servers/kestrel) serveru je spravovaná, napříč platformami webový server. Pomocí konfigurace reverzního proxy serveru je často spustit kestrel [IIS](https://www.iis.net/). Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.
* Server služby IIS protokolu HTTP (`IISHttpServer`) je [v procesový server](xref:fundamentals/servers/index#in-process-hosting-model) pro službu IIS.
* [Ovladač HTTP.sys](xref:fundamentals/servers/httpsys) server je webový server pro ASP.NET Core ve Windows.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core využívá [Kestrel](xref:fundamentals/servers/kestrel) implementaci serveru. Kestrel je spravovaná, napříč platformami webový server. Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core využívá [Kestrel](xref:fundamentals/servers/kestrel) implementaci serveru. Kestrel je spravovaná, napříč platformami webový server. Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](http://nginx.org) nebo [Apache](https://httpd.apache.org/). Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core nabízí následující implementace serveru:

* [Kestrel](xref:fundamentals/servers/kestrel) serveru je spravovaná, napříč platformami webový server. Pomocí konfigurace reverzního proxy serveru je často spustit kestrel [IIS](https://www.iis.net/). Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.
* [Ovladač HTTP.sys](xref:fundamentals/servers/httpsys) server je webový server pro ASP.NET Core ve Windows.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core využívá [Kestrel](xref:fundamentals/servers/kestrel) implementaci serveru. Kestrel je spravovaná, napříč platformami webový server. Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core využívá [Kestrel](xref:fundamentals/servers/kestrel) implementaci serveru. Kestrel je spravovaná, napříč platformami webový server. Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](http://nginx.org) nebo [Apache](https://httpd.apache.org/). Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.

---

::: moniker-end

Další informace naleznete v tématu <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurace

ASP.NET Core využívá konfigurační model založený na dvojicích název-hodnota. Konfigurační model již není založen na <xref:System.Configuration> nebo *web.config*. Hodnoty konfigurace jsou získávány z uspořádané množiny zprostředkovatelů konfigurace. Vestavění zprostředkovatelé konfigurace podporují množství standardních formátů (XML, JSON, INI) nebo konfiguraci pomocí proměnných prostředí. Můžete vytvořit i vlastního zprostředkovatele konfigurace.

Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="logging"></a>Protokolování

ASP.NET Core poskytuje obecné API pro protokolování, které spolupracuje s různými zprostředkovateli protokolování. Předdefinovaní zprostředkovatelé protokolování podporují odesílání záznamů do jednoho i více míst zároveň. Použít je možné i zprostředkovatele třetích stran.

Další informace naleznete v tématu <xref:fundamentals/logging/index>.

## <a name="error-handling"></a>Zpracování chyb

ASP.NET Core má integrované funkce pro zpracování chyb v aplikacích, vč. stránky pro diagnostiku výjimek, vlastní chybové stránky a zpracování výjimek při spuštění.

Další informace naleznete v tématu <xref:fundamentals/error-handling>.

## <a name="routing"></a>Směrování

ASP.NET Core nabízí funkce pro směrování požadavků do obslužné rutiny směrovače.

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="background-tasks"></a>Úlohy na pozadí

Úlohy na pozadí jsou implementovány jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.

Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.

## <a name="access-httpcontext"></a>Přístup k objektu HttpContext

`HttpContext` Při zpracování žádosti se stránkami Razor a technologií MVC je automaticky k dispozici. V případech kdy `HttpContext` není snadno k dispozici, můžete přistupovat `HttpContext` prostřednictvím <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> rozhraní a jeho výchozí implementace <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.

Další informace naleznete v tématu <xref:fundamentals/httpcontext>.
