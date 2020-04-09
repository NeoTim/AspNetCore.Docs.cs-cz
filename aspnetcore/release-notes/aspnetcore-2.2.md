---
title: Co je nového v ASP.NET Core 2.2
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 2.2.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.2
ms.openlocfilehash: 54d3f1e7b0c94d69781c052694305a389a675019
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977168"
---
# <a name="whats-new-in-aspnet-core-22"></a>Co je nového v ASP.NET Core 2.2

Tento článek upozorňuje na nejvýznamnější změny v ASP.NET jádrem 2.2 s odkazy na příslušnou dokumentaci.

## <a name="openapi-analyzers--conventions"></a>OpenAPI analyzátory & konvence

OpenAPI (dříve známý jako Swagger) je specifikace pro jazyk nenostik pro popis rozhraní REST API. Ekosystém OpenAPI má nástroje, které umožňují zjišťování, testování a vytváření klientského kódu pomocí specifikace. Podpora generování a vizualizace openapi dokumentů v ASP.NET Core MVC je poskytována prostřednictvím komunitních projektů, jako jsou [NSwag](https://github.com/RicoSuter/NSwag) a [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). ASP.NET Core 2.2 poskytuje vylepšené nástroje a runtime prostředí pro vytváření dokumentů OpenAPI.

Další informace najdete v následujících materiálech:

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0-preview1: OpenAPI analyzátory & konvence](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>Podpora podrobností o problému

ASP.NET core 2.1 `ProblemDetails`zavedena na základě specifikace [RFC 7807](https://tools.ietf.org/html/rfc7807) pro přenos podrobností o chybě s odpovědí HTTP. V 2.2 `ProblemDetails` je standardní odpověď pro kódy chyb `ApiControllerAttribute`klienta v řadičích přiřazených . `IActionResult` Vrácení stavového kódu chyby klienta `ProblemDetails` (4xx) nyní vrátí tělo. Výsledek také obsahuje ID korelace, které lze použít ke korelaci chyby pomocí protokolů požadavků. Pro chyby `ProducesResponseType` klienta, `ProblemDetails` výchozí použití jako typ odpovědi. To je dokumentováno ve výstupu OpenAPI / Swagger generovaném pomocí NSwag nebo Swashbuckle.AspNetCore.

## <a name="endpoint-routing"></a>Směrování koncového bodu

ASP.NET Core 2.2 používá nový systém *směrování koncových bodů* pro lepší odesílání požadavků. Změny zahrnují nové členy rozhraní API pro generování propojení a transformátory parametrů trasy.

Další informace najdete v následujících materiálech:

* [Směrování koncového bodu v bodě 2.2](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* [Transformátory parametrů trasy](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (viz Sekce **Směrování)**
* [Rozdíly mezi směrováním založeným na IRouteru a koncovém bodu](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>Kontroly stavu

Nová služba kontroly stavu usnadňuje použití ASP.NET Core v prostředích, která vyžadují kontroly stavu, jako je například Kubernetes. Kontroly stavu zahrnuje middleware a sadu `IHealthCheck` knihoven, které definují abstrakce a služby.

Kontroly stavu jsou používány orchestrator kontejneru nebo nástroj pro vyrovnávání zatížení rychle zjistit, zda systém reaguje na požadavky normálně. Orchestrátor kontejneru může reagovat na selhání kontroly stavu zastavením postupné nasazení nebo restartování kontejneru. Správce zatížení může reagovat na kontrolu stavu směrováním provozu mimo selhání instance služby.

Kontroly stavu jsou vystaveny aplikací jako koncový bod HTTP používaný monitorovacími systémy. Kontroly stavu lze nakonfigurovat pro různé scénáře monitorování v reálném čase a monitorovací systémy. Služba zdravotních kontrol se integruje s [projektem BeatPulse](https://github.com/Xabaril/BeatPulse). což usnadňuje přidávání kontrol pro desítky populárních systémů a závislostí.

Další informace naleznete [v tématu Kontroly stavu v ASP.NET Core](xref:host-and-deploy/health-checks).

## <a name="http2-in-kestrel"></a>HTTP/2 v Poštolu

ASP.NET Core 2.2 přidává podporu pro HTTP/2.

HTTP/2 je hlavní revize protokolu HTTP. Mezi významné vlastnosti PROTOKOLU HTTP/2 patří:

* Podpora komprese záhlaví.
* Plně multiplexované datové proudy přes jedno připojení.

Zatímco HTTP/2 zachovává sémantiku HTTP (například hlavičky a metody HTTP), je to narušující změna z HTTP/1.x o tom, jak jsou data orámována a odesílána mezi klientem a serverem.

V důsledku této změny v rámování, servery a klienti musí vyjednat použitou verzi protokolu. Vyjednávání protokolu aplikační vrstvy (ALPN) je rozšíření TLS, které umožňuje serveru a klientovi vyjednat verzi protokolu použitou jako součást jejich handshake TLS. I když je možné mít předchozí znalosti mezi serverem a klientem na protokolu, všechny hlavní prohlížeče podporují ALPN jako jediný způsob, jak navázat připojení HTTP/2.

Další informace naleznete v tématu [podpora HTTP/2](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).

## <a name="kestrel-configuration"></a>Konfigurace kestrelu

V dřívějších verzích ASP.NET Core, Kestrel `UseKestrel`možnosti jsou konfigurovány voláním . V 2.2 Kestrel možnosti `ConfigureKestrel` jsou konfigurovány voláním na tvůrce hostitele. Tato změna řeší problém s `IServer` pořadím registrací pro hostování v procesu. Další informace najdete v následujících materiálech:

* [Zmírnění konfliktu useIIS](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [Konfigurovat možnosti serveru Kestrel pomocí configurekestrelu](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>Hostování v procesu služby IIS

V dřívějších verzích ASP.NET Core služba IIS slouží jako reverzní proxy server. V 2.2 ASP.NET core modul uvaděč CoreCLR a hostovat aplikaci uvnitř pracovního procesu iis *(w3wp.exe*). Hostování v průběhu procesu poskytuje zvýšení výkonu a diagnostiky při spuštění se službou IIS.

Další informace naleznete [v tématu hostování v procesu pro službu IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).

## <a name="opno-locsignalr-java-client"></a>SignalRJava klient

ASP.NET Core 2.2 představuje Java SignalRklienta pro . Tento klient podporuje připojení k SignalR ASP.NET Core Server z kódu Java, včetně aplikací pro Android.

Další informace naleznete [v SignalR tématu ASP.NET core java klienta](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).

## <a name="cors-improvements"></a>Vylepšení CORS

V dřívějších verzích ASP.NET Core umožňuje `Accept` `Accept-Language`CORS Middleware odesílat `Content-Language`hlavičky a `Origin` hlavičky bez ohledu na hodnoty nakonfigurované v aplikaci `CorsPolicy.Headers`. V 2.2, CORS Middleware zásady shoda je možné `Access-Control-Request-Headers` pouze v případě, `WithHeaders`že hlavičky odeslané v přesně odpovídají záhlaví uvedená v .

Další informace naleznete v tématu [CORS Middleware](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).

## <a name="response-compression"></a>Komprese odpovědí

ASP.NET Core 2.2 může komprimovat odpovědi s [kompresním formátem Brotli](https://tools.ietf.org/html/rfc7932).

Další informace naleznete v [tématu Response Compression Middleware supports Brotli compression](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).

## <a name="project-templates"></a>Šablony projektů

ASP.NET Základní webové projektšablony byly aktualizovány na [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) a [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4). Nový vzhled je vizuálně jednodušší a usnadňuje zobrazení důležitých struktur aplikace.

![Domovská stránka nebo stránka Rejstříku](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>Validační výkon

Validační systém MVC je navržen tak, aby byl rozšiřitelný a flexibilní, což vám umožňuje určit na základě požadavku, které validátory platí pro daný model. To je skvělé pro vytváření komplexních poskytovatelů ověření. Však v nejběžnějším případě aplikace používá pouze vestavěné validátory a nevyžadují tuto extra flexibilitu. Vestavěné validátory zahrnují dataAnotations jako [Povinné] a `IValidatableObject`[StringLength] a .

V ASP.NET Core 2.2, MVC může zkrat validace, pokud zjistí, že daný model grafu nevyžaduje ověření. Přeskočení ověření má za následek významná vylepšení při ověřování modelů, které nemohou nebo nemají žádné validátory. To zahrnuje objekty, jako jsou kolekce `byte[]` `string[]`primitiv (například , ), `Dictionary<string, string>`nebo komplexní objekt grafy bez mnoha validátorů.

## <a name="http-client-performance"></a>Výkon klienta HTTP

V ASP.NET Core 2.2, `SocketsHttpHandler` výkon byl vylepšen snížením konflikty uzamčení fondu připojení. Pro aplikace, které dělají mnoho odchozích požadavků HTTP, jako jsou některé architektury mikroslužeb, je vylepšena propustnost. Při zatížení `HttpClient` lze propustnost zlepšit až o 60 % v systému Linux a o 20 % v systému Windows.

Další informace naleznete [v žádosti o přijetí k přijetí žádosti, která provedla toto zlepšení](https://github.com/dotnet/corefx/pull/32568).

## <a name="additional-information"></a>Další informace

Úplný seznam změn naleznete v [ASP.NET základní poznámky k verzi 2.2](https://github.com/dotnet/aspnetcore/releases/tag/2.2.0).
