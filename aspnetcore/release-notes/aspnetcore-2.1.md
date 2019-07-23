---
title: Co je nového v ASP.NET Core 2.1
author: isaac2004
description: Informace o nových funkcích v ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2019
uid: aspnetcore-2.1
ms.openlocfilehash: 359f961db768b9048427c8ab296ee3e035879408
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086397"
---
# <a name="whats-new-in-aspnet-core-21"></a>Co je nového v ASP.NET Core 2.1

Tento článek se soustředí na nejdůležitější změny v ASP.NET Core 2.1 s odkazy na relevantní dokumentaci.

## <a name="signalr"></a>SignalR

Přepsali jsme SignalR pro ASP.NET Core 2.1. Funkce SignalR technologie ASP.NET Core zahrnuje celou řadu vylepšení:

* Zjednodušený model horizontální navýšení kapacity.
* Nový klient JavaScriptu se žádné jQuery závislostí.
* Nové kompaktní binární protokol založený na MessagePack.
* Podpora pro vlastní protokoly.
* Nový model datového proudu odpovědi.
* Podpora pro klienty založené na úplné objekty Websocket.

Další informace najdete v tématu [funkce SignalR technologie ASP.NET Core](xref:signalr/introduction).

## <a name="razor-class-libraries"></a>Knihovny tříd Razor

ASP.NET Core 2.1 usnadňuje sestavení a zahrnout do knihovny uživatelského rozhraní založeného na Razor a sdílejte ji napříč více projektů. Nová sada Razor SDK umožňuje sestavování Razor soubory do projektu knihovny tříd, který se dá zabalit do balíčku NuGet. Zobrazení a stránky v knihovnách se automaticky zjišťují a může být přepsána aplikace. Díky integraci Razor kompilace do sestavení:

* Čas spuštění aplikace je výrazně rychlejší.
* Rychlé aktualizace zobrazení syntaxe Razor a stránky v době běhu jsou stále k dispozici jako součást pracovního postupu iterativního vývoje.

Další informace najdete v tématu [vytvářet opakovaně použitelné uživatelské rozhraní pomocí projektu knihovny tříd Razor](xref:razor-pages/ui-class).

## <a name="identity-ui-library--scaffolding"></a>Knihovna uživatelského rozhraní identity & generování uživatelského rozhraní

ASP.NET Core 2.1 poskytuje [ASP.NET Core Identity](xref:security/authentication/identity) jako [knihovny tříd Razor](xref:razor-pages/ui-class). Aplikace, které zahrnují Identity můžete použít nový generátor Identity selektivně Přidání zdrojového kódu obsažen v knihovně Razor třídu (RCL) Identity. Můžete chtít vygenerování zdrojového kódu, abyste mohli upravit kód a změnit chování. Například může dát pokyn generátor pro generování kódu při registraci. Generovaného kódu mají přednost před stejný kód v Identity RCL.

Aplikací, které toho **není** zahrnout ověřování můžete použít Generátor Identity a přidejte příslušný balíček RCL Identity. Máte možnost výběru Identity kód chcete vygenerovat.

Další informace najdete v tématu [Identity vygenerované uživatelské rozhraní v projektech ASP.NET Core](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Větší zaměření na zabezpečení a ochrana osobních údajů povolení HTTPS pro web apps je důležité. Vynucení protokolu HTTPS se stává stále více striktní na webu. Servery, které nechcete používat protokol HTTPS se považují za nezabezpečené. K vynucení, webové funkce musí být volána ze zabezpečeného kontextu spuštění prohlížeče (chromu, Mozilla). [GDPR](xref:security/gdpr) vyžaduje použití protokolu HTTPS do ochrany osobních údajů uživatele. Při použití protokolu HTTPS v produkčním prostředí je velmi důležité, pomocí protokolu HTTPS ve vývoji pomáhají zabránit problémům v nasazení (například nezabezpečené odkazy). ASP.NET Core 2.1 zahrnuje množství vylepšení, které usnadňují používání protokolu HTTPS ve vývoji a konfigurace protokolu HTTPS v produkčním prostředí. Další informace najdete v tématu [vynucení HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Na ve výchozím nastavení

K usnadnění vývoje zabezpečeného webu, je teď ve výchozím nastavení povolenou komunikací HTTPS. Od verze 2.1, Kestrel naslouchá na `https://localhost:5001` Pokud je k dispozici certifikát pro místní vývoj. Certifikát pro vývoj se vytvoří:

* Jako součást první spuštění prostředí .NET Core SDK, při použití sady SDK pro první.
* Ručně pomocí nového `dev-certs` nástroj.

Spustit `dotnet dev-certs https --trust` důvěřovat certifikátům.

### <a name="https-redirection-and-enforcement"></a>Přesměrování protokolu HTTPS a vynucení

Webové aplikace obvykle potřebují naslouchání protokolu HTTP a HTTPS, ale pak přesměrovat všechen provoz protokolu HTTP na HTTPS. V 2.1 specializované middleware přesměrování HTTPS, které inteligentně přesměruje na základě přítomnosti konfigurace nebo je zavedený vázaných portů.

Použití protokolu HTTPS lze dále vynutit použití [HTTP striktní přenosu zabezpečení protokolu (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts). HSTS instruuje prohlížeče vždy přístup k webu přes protokol HTTPS. ASP.NET Core 2.1 přidá HSTS middleware, který podporuje možnosti pro maximální stáří, subdomén, a HSTS předběžné načtení seznamu.

### <a name="configuration-for-production"></a>Konfigurace pro produkční prostředí

V produkčním prostředí musí být explicitně nakonfigurován protokol HTTPS. V 2.1 výchozí konfigurace schématu konfigurace HTTPS pro Kestrel přidala. Aplikace je možné nakonfigurovat na použití:

* Více koncových bodů včetně adresy URL. Další informace najdete v tématu [implementaci Kestrel webového serveru: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Certifikát, který se má použít pro protokol HTTPS ze souboru na disku nebo z úložiště certifikátů.

## <a name="gdpr"></a>GDPR

ASP.NET Core nabízí rozhraní API a šablony, které vám pomohou splnit některé [EU obecného Regulation (GDPR)](https://www.eugdpr.org/) požadavky. Další informace najdete v tématu [podpory nařízení GDPR v ASP.NET Core](xref:security/gdpr). A [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ukazuje způsob použití a umožňuje testovat Většina rozhraní API přidat do šablony ASP.NET Core 2.1 a GDPR Rozšiřovací body.

## <a name="integration-tests"></a>Integrační testy

Nový balíček je zavedená, že test zjednodušují vytváření a spouštění. [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) balíček provádí následující úlohy:

* Zkopíruje soubor závislostí (*\*.deps*) z otestované aplikace do testovacího projektu *bin* složky.
* Nastaví obsahu kořenové do kořenového adresáře projektu testované aplikace, aby statické soubory a stránky a zobrazení se našly při spouštění testů.
* Poskytuje [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) tříd zjednodušení spuštění testované aplikaci pomocí [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

Následující test používá [xUnit](https://xunit.github.io/) zkontrolujte, že načte indexovou stránku s stavový kód úspěchu a správné hlavičky Content-Type:

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Další informace najdete v tématu [integrační testy](xref:test/integration-tests) tématu.

## <a name="apicontroller-actionresultt"></a>[Objektu ApiController], ActionResult\<T >

ASP.NET Core 2.1 přidá nový programovací vytváření názvů usnadňují čisté a popisný webová rozhraní API. `ActionResult<T>` je přidán nový typ umožňuje aplikacím vracet typ odpovědi nebo jiné akce výsledek (podobně jako IActionResult), při stále označující typ odpovědi. `[ApiController]` Atribut se také přidal jako způsob, jak vyjádřit výslovný souhlas s konvencí specifických pro webové rozhraní API a chování.

Další informace najdete v tématu [vytvářet webová rozhraní API pomocí ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1 obsahuje nový `IHttpClientFactory` služba, která usnadňuje konfiguraci a využití instancí `HttpClient` v aplikacích. `HttpClient` již obsahuje koncept delegování obslužné rutiny, které by mohly být propojeny pro odchozí požadavky HTTP. Objekt pro vytváření:

* Díky registraci instance `HttpClient` za mnohem intuitivnější pojmenované klienta.
* Implementuje Polly obslužnou rutinu, která umožňuje Polly zásad se použije pro opakování, CircuitBreakers atd.

Další informace najdete v tématu [zahájit požadavky HTTP](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Konfigurace kestrel přenosu

Verze technologie ASP.NET Core 2.1 Kestrel pro výchozí přenos je již podle Libuv ale místo toho podle spravované sokety. Další informace najdete v tématu [implementaci Kestrel webového serveru: Konfigurace přenosu](xref:fundamentals/servers/kestrel#transport-configuration).

## <a name="generic-host-builder"></a>Tvůrce obecný hostitele

Obecný Tvůrce hostitele (`HostBuilder`) byl zaveden. Tento Tvůrce lze použít pro aplikace, které není zpracovávají požadavky HTTP (zasílání zpráv, úlohy na pozadí, atd.).

Další informace najdete v tématu [obecný hostitele .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Aktualizace šablon SPA

Jednostránková aplikace šablony pro Angular, React a React s Reduxem se aktualizují, aby používat standardní projekt struktury a systémy pro každé rozhraní, sestavení.

Angular šablona je založena na Angular CLI a šablon React jsou založeny na vytvoření aplikace react.

Další informace naleznete v tématu:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>Stránky Razor vyhledávat Razor

2.1 stránky Razor vyhledejte Razor prostředků (např. rozložení a částečných zobrazení) v následujících adresářích v uvedeném pořadí:

1. Aktuální složce stránky.
1. */Pages/Shared/*
1. */Views/sdílené /*

## <a name="razor-pages-in-an-area"></a>Stránky Razor v oblasti.

Teď podporují stránky Razor [oblasti](xref:mvc/controllers/areas). Chcete-li zobrazit příklad oblasti, vytvořte nová webová aplikace Razor Pages s jednotlivými uživatelskými účty. Webové aplikace stránky Razor pomocí jednotlivých uživatelských účtů zahrnuje */Areas/Identity/Pages*.

## <a name="mvc-compatibility-version"></a>Verze kompatibility MVC

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda umožňuje aplikacím vyjádřit výslovný souhlas nebo výslovný nesouhlas s potenciálně rozbíjející změny chování zavedení v ASP.NET Core MVC 2.1 nebo novější.

Další informace naleznete v tématu <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Migrace z verze 2.0 na verzi 2.1

Zobrazit [migrovat z ASP.NET Core 2.0 k 2.1](xref:migration/20_21).

## <a name="additional-information"></a>Další informace

Úplný seznam změn, najdete v článku [poznámky k verzi pro ASP.NET Core 2.1](https://github.com/aspnet/Home/releases/tag/2.1.0).
