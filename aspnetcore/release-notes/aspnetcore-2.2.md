---
title: Co je nového v ASP.NET Core 2,2
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 2,2.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.2
ms.openlocfilehash: 085a26d508af5bdce506ab923a3256046b3894ab
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722485"
---
# <a name="whats-new-in-aspnet-core-22"></a>Co je nového v ASP.NET Core 2,2

Tento článek zvýrazňuje nejvýznamnější změny v ASP.NET Core 2,2 s odkazy na příslušnou dokumentaci.

## <a name="openapi-analyzers--conventions"></a>OpenAPI analyzátory & konvence

OpenAPI (dříve označované jako Swagger) je specifikace jazyka nezávislá pro popis rozhraní REST API. Ekosystém OpenAPI obsahuje nástroje, které umožňují zjistit, testovat a vyprodukovat klientský kód pomocí specifikace. Podpora pro generování a vizualizaci dokumentů OpenAPI ve ASP.NET Core MVC je poskytována prostřednictvím projektů založených na komunitě, jako je [NSwag](https://github.com/RicoSuter/NSwag) a [swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). ASP.NET Core 2,2 poskytuje vylepšené nástroje a běhové prostředí pro vytváření dokumentů OpenAPI.

Další informace naleznete v následujících zdrojích:

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0-preview1: analyzátory OpenAPI & konvence](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>Podpora podrobností o problému

Zavedl se ASP.NET Core 2,1 na `ProblemDetails` základě specifikace [RFC 7807](https://tools.ietf.org/html/rfc7807) pro přenos podrobností o chybě s odpovědí HTTP. V 2,2 `ProblemDetails` je standardní odezva na kódy chyb klienta v řadičích s atributem `ApiControllerAttribute` . `IActionResult`Vrácení stavového kódu chyby klienta (4xx) nyní vrací `ProblemDetails` tělo. Výsledek také obsahuje ID korelace, které lze použít ke korelaci chyby pomocí protokolů požadavků. V případě chyb klienta se `ProducesResponseType` `ProblemDetails` jako typ odpovědi použije výchozí hodnota. To je dokumentováno ve výstupu OpenAPI/Swagger vygenerovaném pomocí NSwag nebo swashbuckle. AspNetCore.

## <a name="endpoint-routing"></a>Směrování koncových bodů

ASP.NET Core 2,2 používá pro lepší odesílání žádostí nový systém *Směrování koncových bodů* . Změny zahrnují nové členy rozhraní API pro generování odkazů a parametry směrování.

Další informace naleznete v následujících zdrojích:

* [Směrování koncových bodů v 2,2](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* [Transformátory parametrů směrování](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (viz část **Směrování** )
* [Rozdíly mezi směrováním na základě IRouter a koncových bodů](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>Kontroly stavu

Nové služby kontroly stavu usnadňují používání ASP.NET Core v prostředích, která vyžadují kontroly stavu, jako je například Kubernetes. Kontroly stavu zahrnují middleware a sadu knihoven, které definují `IHealthCheck` abstrakci a službu.

Kontroly stavu jsou používány nástrojem Orchestrator nebo nástrojem pro vyrovnávání zatížení, aby bylo možné rychle zjistit, zda systém reaguje na požadavky normálně. Produkt Orchestrator Container může reagovat na selhání kontroly stavu zastavením nasazení nebo restartováním kontejneru. Nástroj pro vyrovnávání zatížení může reagovat na kontrolu stavu směrováním provozu mimo selhání instance služby.

Kontroly stavu jsou zpřístupněny aplikací jako koncový bod HTTP používaný systémy monitorování. Kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase a monitorovací systémy. Služba kontroly stavu se integruje s [projektem BeatPulse](https://github.com/Xabaril/BeatPulse). Díky tomu je snazší přidat kontroly pro spoustu oblíbených systémů a závislostí.

Další informace najdete v tématu [kontroly stavu v ASP.NET Core](xref:host-and-deploy/health-checks).

## <a name="http2-in-kestrel"></a>HTTP/2 v Kestrel

ASP.NET Core 2,2 přidává podporu pro HTTP/2.

HTTP/2 je hlavní revize protokolu HTTP. Mezi významné funkce HTTP/2 patří:

* Podpora pro kompresi hlaviček.
* Plně multiplexované streamy přes jedno připojení.

I když protokol HTTP/2 zachovává sémantiku protokolu HTTP (například hlavičky a metody HTTP), jedná se o zásadní změnu z HTTP/1. x na to, jak jsou data v rámečcích a mezi klientem a serverem odesílána.

V důsledku této změny v rámcích musí servery a klienti vyjednávat použitou verzi protokolu. Vyjednávání protokolu aplikační vrstvy (ALPN) je rozšíření TLS, které umožňuje serveru a klientovi vyjednávat verzi protokolu použitou jako součást metody handshake TLS. I když je možné mít předchozí znalosti mezi serverem a klientem v protokolu, všechny hlavní prohlížeče podporují ALPN jako jediný způsob, jak vytvořit připojení HTTP/2.

Další informace najdete v tématu [Podpora protokolu HTTP/2](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).

## <a name="kestrel-configuration"></a>Konfigurace Kestrel

V dřívějších verzích ASP.NET Core možnosti Kestrel jsou konfigurovány voláním `UseKestrel` . V 2,2 jsou možnosti Kestrel konfigurovány voláním `ConfigureKestrel` v Tvůrci hostitele. Tato změna řeší problém s pořadím `IServer` registrací pro vnitroprocesové hostování v rámci procesu. Další informace naleznete v následujících zdrojích:

* [Zmírnit konflikt UseIIS](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [Konfigurace možností serveru Kestrel pomocí ConfigureKestrel](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>Hostování v rámci vnitroprocesové služby IIS

V dřívějších verzích ASP.NET Core služba IIS slouží jako reverzní proxy server. V 2,2 může modul ASP.NET Core spustit CoreCLR a hostovat aplikaci v pracovním procesu služby IIS (*w3wp.exe*). Hostování v rámci procesu poskytuje výkon a diagnostiku při používání služby IIS.

Další informace najdete v tématu [vnitroprocesové hostování pro službu IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).

## <a name="no-locsignalr-java-client"></a>SignalR Klient Java

ASP.NET Core 2,2 zavádí klienta Java pro SignalR . Tento klient podporuje připojení k serveru ASP.NET Core SignalR z kódu Java, včetně aplikací pro Android.

Další informace najdete v tématu [ASP.NET Core SignalR klient Java](../signalr/java-client.md?view=aspnetcore-2.2).

## <a name="cors-improvements"></a>Vylepšení CORS

V dřívějších verzích ASP.NET Core middleware CORS povoluje `Accept` `Accept-Language` `Content-Language` posílání hlaviček, a, a `Origin` to bez ohledu na hodnoty nakonfigurované v `CorsPolicy.Headers` . V 2,2 se shoda zásad middlewaru CORS dá provést jenom v případě, že se záhlaví navzájem `Access-Control-Request-Headers` přesně shodují se záhlavími uvedenými v `WithHeaders` .

Další informace najdete v tématu [middleware CORS](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).

## <a name="response-compression"></a>Komprese odpovědí

ASP.NET Core 2,2 může komprimovat odpovědi pomocí [formátu komprese Brotli](https://tools.ietf.org/html/rfc7932).

Další informace najdete v tématu [middleware pro kompresi odpovědí podporuje Brotli kompresi](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).

## <a name="project-templates"></a>Šablony projektů

Šablony ASP.NET Core webového projektu byly aktualizovány na [bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) a na [úhlove 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4). Nový vzhled je vizuálně jednodušší a usnadňuje zobrazení důležitých struktur aplikace.

![Stránka domů nebo index](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>Výkon ověřování

Systém ověřování MVC je navržený tak, aby byl rozšiřitelný a flexibilní, což vám umožní určit na základě jednotlivých požadavků, které validátory se vztahují na daný model. To je skvělé pro vytváření komplexních zprostředkovatelů ověřování. V nejběžnějším případě však aplikace používá pouze předdefinované validátory a nevyžaduje tuto dodatečnou flexibilitu. Předdefinované Validátory zahrnují dataanotace, jako jsou [povinné] a [StringLength] a `IValidatableObject` .

V ASP.NET Core 2,2 může MVC ověřování pomocí krátkého okruhu, pokud zjistí, že daný graf modelu nevyžaduje ověření. Při ověřování modelů, které nemohou nebo nemají žádné validátory, se výsledky ověřování přeskočí. To zahrnuje objekty, jako jsou například kolekce primitivních objektů (například `byte[]` , `string[]` , `Dictionary<string, string>` ) nebo grafy složitých objektů bez mnoha validátorů.

## <a name="http-client-performance"></a>Výkon klienta HTTP

V ASP.NET Core 2,2 se zvýšil výkon tím, že se zmenší kolize `SocketsHttpHandler` uzamčení fondu připojení. V případě aplikací, které provedou mnoho odchozích požadavků HTTP, jako jsou například některé architektury mikroslužeb, je propustnost vylepšena. V případě zátěže je `HttpClient` možné zvýšit propustnost až 60% v systémech Linux a 20% ve Windows.

Další informace najdete v [žádosti o přijetí změn, která provedla toto zlepšení](https://github.com/dotnet/corefx/pull/32568).

## <a name="additional-information"></a>Další informace

Úplný seznam změn najdete v [poznámkách k verzi pro ASP.NET Core 2,2](https://github.com/dotnet/aspnetcore/releases/tag/2.2.0).