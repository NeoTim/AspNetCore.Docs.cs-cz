---
title: Co je nového v ASP.NET Core 2.2
author: tdykstra
description: Informace o nových funkcích v ASP.NET Core 2.2.
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/18/2018
uid: aspnetcore-2.2
ms.openlocfilehash: cdc761b645b91777bdf6084c3ad4659fcea55039
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750943"
---
# <a name="whats-new-in-aspnet-core-22"></a>Co je nového v ASP.NET Core 2.2

Tento článek se soustředí na nejdůležitější změny provedené v 2.2 technologie ASP.NET Core, odkazy na relevantní dokumentaci.

## <a name="openapi-analyzers--conventions"></a>Analyzátory OpenAPI a konvence

OpenAPI (dříve označované jako Swagger) je specifikace bez ohledu na jazyk pro popis rozhraní REST API. Ekosystém OpenAPI obsahuje nástroje, které umožňují vyhledávání, testování a produkci pomocí specifikace kód klienta. Podpora pro generování a vizualizaci OpenAPI dokumenty v ASP.NET Core MVC se poskytuje prostřednictvím projektů, jako vyvinutý komunitou [službou NSwag](https://github.com/RSuter/NSwag), a [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). ASP.NET Core 2.2 poskytuje vylepšené nástroje a modul runtime prostředí pro vytváření dokumentů OpenAPI.

Další informace naleznete v následujících materiálech:

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0-preview1: Analyzátory OpenAPI a konvence](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>Podrobnosti o problému podpory

ASP.NET Core 2.1 zavedené `ProblemDetails`na základě [RFC 7807](https://tools.ietf.org/html/rfc7807) specifikace pro provádění podrobnosti o chybě se odpověď HTTP. 2.2 `ProblemDetails` je standardní odpověď pro klienta kódy chyb v řadiče s `ApiControllerAttribute`. `IActionResult` Vrátí chyba stavový kód (4xx) nyní vrátí klientovi `ProblemDetails` textu. Výsledek bude obsahovat také Identifikátor korelace, který lze použít ke korelaci chyb pomocí protokolů z požadavku. Chyby klienta `ProducesResponseType` výchozí hodnota je pomocí `ProblemDetails` jako typ odpovědi. To je popsána v OpenAPI / Swagger výstupní vygenerované pomocí službou NSwag nebo Swashbuckle.AspNetCore.

## <a name="endpoint-routing"></a>Koncový bod směrování

ASP.NET Core 2.2 používá nový *koncový bod směrování* systému pro lepší odesílání požadavků. Mezi tyto změny patří nové propojení členy generování rozhraní API a parametr transformátory trasy.

Další informace naleznete v následujících materiálech:

* [Koncový bod směrování v 2.2](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* [Parametr transformátory trasy](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (viz **směrování** část)
* [Rozdíly mezi směrování na základě IRouter a koncového bodu](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>Kontroly stavu

Nový stav kontroluje, že služba bude snazší používat ASP.NET Core v prostředí, které vyžadují kontroly stavu, jako je třeba Kubernetes. Doplněk pro kontroly stavu zahrnuje middleware a sadu knihoven, které definují `IHealthCheck` abstrakce a služby.

Kontroly stavu jsou používány orchestrátor kontejnerů nebo Vyrovnávání zatížení do rychle zjistit, pokud je systém reagování na žádosti normálně. Orchestrátor kontejnerů může reagovat na stav selhání zkontrolovat, že zastavení nasazení se zajištěním provozu nebo kontejner se restartuje. Nástroj pro vyrovnávání zatížení může reagovat na kontrolu stavu můžete provoz nasměrovat od selhání instance služby.

Kontroly stavu jsou vystaveny jako koncový bod HTTP používají systémy pro monitorování aplikací. Kontroly stavu můžete nakonfigurovat pro různé scénáře monitorování a systémy pro monitorování v reálném čase. Doplněk pro kontroly stavu služby se integruje s [BeatPulse projektu](https://github.com/Xabaril/BeatPulse). snadněji přidání kontroly pro desítek oblíbených systémů a závislostí.

Další informace najdete v tématu [doplněk pro kontroly stavu v ASP.NET Core](xref:host-and-deploy/health-checks).

## <a name="http2-in-kestrel"></a>V Kestrel HTTP/2

ASP.NET Core 2.2 přidává podporu pro HTTP/2. 

HTTP/2 je hlavní revize protokolu HTTP. Některé důležité funkce protokolu HTTP/2 jsou podpora komprimaci hlaviček a plně multiplexní datové proudy přes samostatné připojení. Zatímco HTTP/2 zachovává sémantiku na HTTP (hlavičky protokolu HTTP, metody atd.) je zásadní změnu z HTTP/1.x na tom, jak tato data jsou uvedeny a odeslány prostřednictvím sítě jako.

Následkem této změně rámce servery a klienti potřebovat pro vyjednávání protokolu verze použitá. Vyjednávání protokolu v aplikační vrstvě (ALPN) je rozšíření protokolu TLS, který umožňuje serveru a klienta pro vyjednávání protokolu verze použitá jako součást své metody handshake TLS. I když je možné mít předchozí znalosti mezi serverem a klientem na protokol, podporují všechny hlavní prohlížeče ALPN jako jediný způsob, jak vytvořit připojení HTTP/2.

Další informace najdete v tématu [podpora HTTP/2](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).

## <a name="kestrel-configuration"></a>Konfigurace kestrel

V dřívějších verzích sady ASP.NET Core, Kestrel možnosti nakonfigurují voláním `UseKestrel`. 2.2, Kestrel možnosti nakonfigurují voláním `ConfigureKestrel` na tvůrce hostitele. Tato změna řeší problém s pořadí `IServer` registrace pro hostování v procesu. Další informace naleznete v následujících materiálech:

* [Zmírnění má konflikt](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [Nakonfigurujte Kestrel možnosti server s ConfigureKestrel](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>Hostování v procesu služby IIS

V dřívějších verzích sady ASP.NET Core služba IIS slouží jako reverzní proxy server. 2.2, že modul ASP.NET Core spouštěcí CoreCLR a hostovat aplikace uvnitř pracovní proces služby IIS (*w3wp.exe*). Hostování v procesu zajišťuje výkon a diagnostiku zisky při spuštění pomocí služby IIS.

Další informace najdete v tématu [proces hostování pro službu IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).

## <a name="signalr-java-client"></a>Klientskou sadou SignalR Java

ASP.NET Core 2.2 zavádí klientskou sadou Java pro funkci SignalR. Tento klient podporuje připojení k serveru funkce SignalR technologie ASP.NET Core z kódu Java, včetně aplikací pro Android.

Další informace najdete v tématu [klientskou sadou Java funkce SignalR technologie ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).

## <a name="cors-improvements"></a>Vylepšení CORS

V dřívějších verzích sady ASP.NET Core, CORS Middleware umožňuje `Accept`, `Accept-Language`, `Content-Language`, a `Origin` hlavičky k odeslání bez ohledu na nakonfigurované v hodnoty `CorsPolicy.Headers`. 2.2, shoda se zásadami Middlewarem CORS je možné, pouze při odeslání hlaviček `Access-Control-Request-Headers` přesně odpovídat záhlaví uvádí `WithHeaders`.

Další informace najdete v tématu [Middlewarem CORS](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).

## <a name="response-compression"></a>Komprese odpovědí

ASP.NET Core 2.2 můžete kompresi odpovědí s [formát komprese Brotli](https://tools.ietf.org/html/rfc7932).

Další informace najdete v tématu [Middleware pro kompresi odpovědí podporuje kompresi Brotli](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).

## <a name="project-templates"></a>Šablony projektů

Byly aktualizovány šablony webových projektů ASP.NET Core [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) a [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4). Nový vzhled je vizuálně jednodušší a usnadňuje naleznete v tématu důležité struktury aplikace.

![Index nebo Domovská stránka](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>Ověření výkonu

MVC ověření systému slouží k mít rozšiřitelný a flexibilní, abyste mohli určit, na každý požadavek na, což validátory platí pro daný model. To je skvělé pro vytváření poskytovatelů komplexní ověřování. V nejběžnější případ pouze používá integrované validátory aplikace a nevyžadují tato mimořádná flexibilita. Integrované validátory patří například DataAnnotations [povinné] a [StringLength] a `IValidatableObject`.

V ASP.NET Core 2.2 můžou MVC zkrácenou ověření, pokud určí, že daný model grafu nevyžaduje ověření. Výsledky ověření v významná vylepšení přeskakuje se při ověřování modelů, které nelze nebo nemají žádné validátory. To zahrnuje objekty, jako jsou kolekcí primitivních elementů (například `byte[]`, `string[]`, `Dictionary<string, string>`), nebo komplexního objektu grafy bez mnoho validátorů.

## <a name="http-client-performance"></a>Výkon klienta HTTP

V ASP.NET Core 2.2 výkon `SocketsHttpHandler` byla vylepšena snížením Uzamčení kolize fondu připojení. U aplikací, které usnadňují mnoho odchozích požadavků protokolu HTTP, jako je například některé architektury mikroslužeb se zlepšila propustnost. Pod zátěží `HttpClient` až o 60 % v Linuxu a 20 % pro Windows se dalo zlepšit propustnost.

Další informace najdete v tématu [žádosti o přijetí změn, který k tomuto vylepšení](https://github.com/dotnet/corefx/pull/32568).

## <a name="additional-information"></a>Další informace

Úplný seznam změn, najdete v článku [poznámky k verzi pro ASP.NET Core 2.2](https://github.com/aspnet/Home/releases/tag/2.2.0).
