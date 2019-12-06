---
title: Co je nového v ASP.NET Core 2,1
author: isaac2004
description: Seznamte se s novými funkcemi v ASP.NET Core 2,1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: d969b4caab44e3e50b3a0202b25864921d6d01dc
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880860"
---
# <a name="whats-new-in-aspnet-core-21"></a>Co je nového v ASP.NET Core 2,1

Tento článek zvýrazňuje nejvýznamnější změny v ASP.NET Core 2,1 s odkazy na příslušnou dokumentaci.

## SignalR

SignalR bylo přepsáno pro ASP.NET Core 2,1. ASP.NET Core SignalR zahrnuje řadu vylepšení:

* Zjednodušený model škálování na více instancí.
* Nový klient jazyka JavaScript bez závislosti jQuery.
* Nový kompaktní binární protokol založený na MessagePack.
* Podpora vlastních protokolů.
* Nový model odpovědi streamování.
* Podpora klientů založená na holéch objektech WebSockets.

Další informace najdete v tématu [ASP.NET Core SignalR](xref:signalr/introduction).

## <a name="razor-class-libraries"></a>Knihovny RCL (Razor Class Library)

ASP.NET Core 2,1 usnadňuje sestavování a zahrnutí uživatelského rozhraní založeného na Razor do knihovny a jejich sdílení napříč více projekty. Nová sada Razor SDK umožňuje sestavovat soubory Razor do projektu knihovny tříd, který může být zabalen do balíčku NuGet. Zobrazení a stránky v knihovnách jsou automaticky zjišťovány a aplikace je může přepsat aplikací. Integrací kompilace Razor do sestavení:

* Čas spuštění aplikace je výrazně rychlejší.
* Rychlé aktualizace zobrazení a stránek Razor za běhu jsou stále k dispozici jako součást pracovního postupu iterativního vývoje.

Další informace najdete v tématu [vytvoření opakovaně použitelného uživatelského rozhraní pomocí projektu knihovny tříd Razor](xref:razor-pages/ui-class).

## <a name="identity-ui-library--scaffolding"></a>Knihovna uživatelského rozhraní identity & generování uživatelského rozhraní

ASP.NET Core 2,1 poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class). Aplikace, které obsahují identitu, můžou použít novou třídu pro generování identit k selektivnímu Přidání zdrojového kódu obsaženého v knihovně tříd identity Razor (RCL). Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování. Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci. Generovaný kód má přednost před stejným kódem v RCL identity.

Aplikace, které **neobsahují** ověřování, můžou použít generátor identity k přidání balíčku identity RCL. Máte možnost vybrat kód identity, který se má vygenerovat.

Další informace najdete v tématu [Identita uživatelského rozhraní v ASP.NET Core projektech](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Díky lepšímu zaměření na zabezpečení a ochranu osobních údajů je důležité povolit protokol HTTPS pro webové aplikace. Vynucování protokolu HTTPS se na webu stále přísnější. Lokality, které nepoužívají protokol HTTPS, se považují za nezabezpečené. Prohlížeče (chrom, Mozilla) začínají vymáhat, že webové funkce musí být používány z zabezpečeného kontextu. [GDPR](xref:security/gdpr) vyžaduje k ochraně osobních údajů uživatelů používání protokolu HTTPS. Při použití protokolu HTTPS v produkčním prostředí je použití protokolu HTTPS při vývoji důležité k tomu, aby se zabránilo problémům v nasazení (například nezabezpečeným odkazům). ASP.NET Core 2,1 obsahuje řadu vylepšení, která usnadňují používání protokolu HTTPS ve vývoji a konfiguraci HTTPS v produkčním prostředí. Další informace najdete v tématu [vymáhání protokolu HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Ve výchozím nastavení zapnuto

V zájmu usnadnění zabezpečení vývoje webu je teď ve výchozím nastavení povolený protokol HTTPS. Počínaje 2,1 Kestrel naslouchá na `https://localhost:5001`, když je k dispozici místní vývojový certifikát. Vytvoří se vývojový certifikát:

* Jako součást .NET Core SDKho prvního spuštění, při prvním použití sady SDK.
* Ručně pomocí nového nástroje `dev-certs`.

Pro důvěryhodný certifikát spusťte `dotnet dev-certs https --trust`.

### <a name="https-redirection-and-enforcement"></a>Přesměrování a vynucení HTTPS

Webové aplikace obvykle musí naslouchat na protokolech HTTP i HTTPS, ale pak přesměrují všechny přenosy HTTP na HTTPS. V 2,1 se zavedl specializovaný middleware pro přesměrování HTTPS, který inteligentně přesměrovává na základě přítomnosti konfiguračních nebo vázaných portů serveru.

Použití protokolu HTTPS se dá dál vyhovět pomocí [protokolu HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts). HSTS dá prohlížečům pokyn, aby vždycky měli přístup k webu přes protokol HTTPS. ASP.NET Core 2,1 přidává HSTS middleware, který podporuje možnosti pro maximální stáří, subdomény a seznam předload HSTS.

### <a name="configuration-for-production"></a>Konfigurace pro produkční prostředí

V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný. V 2,1 se přidalo výchozí konfigurační schéma pro konfiguraci HTTPS pro Kestrel. Aplikace je možné nakonfigurovat tak, aby používaly:

* Několik koncových bodů včetně adres URL. Další informace najdete v tématu [implementace webového serveru Kestrel: konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Certifikát, který se má použít pro protokol HTTPS buď ze souboru na disku, nebo z úložiště certifikátů.

## <a name="gdpr"></a>GDPR

ASP.NET Core poskytuje rozhraní API a šablony, které vám pomůžou splnit některé z požadavků [EU na obecné nařízení o ochraně osobních údajů (GDPR)](https://www.eugdpr.org/) . Další informace najdete v tématu [Podpora GDPR v ASP.NET Core](xref:security/gdpr). [Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ukazuje, jak použít a umožňuje testovat většinu GDPRch bodů rozšíření a rozhraní API přidaných do šablon ASP.NET Core 2,1.

## <a name="integration-tests"></a>Integrační testy

Zavádí se nový balíček, který zjednodušuje vytváření a spouštění testů. Balíček [Microsoft. AspNetCore. Mvc. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) zpracovává následující úlohy:

* Zkopíruje soubor závislosti ( *\*. DEPS*) z testované aplikace do složky *bin* testovacího projektu.
* Nastaví kořen obsahu na kořen projektu testované aplikace, aby při spuštění testů byly nalezeny statické soubory a stránky/zobrazení.
* Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění testované aplikace pomocí [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

Následující test používá [xUnit](https://xunit.github.io/) ke kontrole, zda se stránka indexu načte s kódem stavu úspěch a se správnou hlavičkou Content-Type:

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

Další informace najdete v tématu věnovaném [integračním testům](xref:test/integration-tests) .

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T >

ASP.NET Core 2,1 přidává nové konvence programování, které usnadňují vytváření čistě a popisných webových rozhraní API. `ActionResult<T>` je nový typ přidaný, aby aplikace vrátila buď typ odpovědi, nebo jakýkoli výsledek jiné akce (podobně jako IActionResult), ale stále určuje typ odpovědi. Atribut `[ApiController]` byl také přidán jako způsob, jak se přihlásit k konvencím a chováním specifickým pro webové rozhraní API.

Další informace najdete v tématu [sestavování webových rozhraní API pomocí ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2,1 obsahuje novou službu `IHttpClientFactory`, která usnadňuje konfiguraci a využívání instancí `HttpClient` v aplikacích. `HttpClient` už má koncept delegování obslužných rutin, které by se daly propojit pro odchozí požadavky HTTP. Objekt pro vytváření (Factory):

* Usnadňuje registraci instancí `HttpClient` pro každého pojmenovaného klienta.
* Implementuje obslužnou rutinu Polly, která umožňuje použití zásad Polly pro opakování, CircuitBreakers atd.

Další informace najdete v tématu [inicializace požadavků HTTP](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Konfigurace přenosu Kestrel

S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů. Další informace najdete v tématu [implementace webového serveru Kestrel: Konfigurace přenosu](xref:fundamentals/servers/kestrel#transport-configuration).

## <a name="generic-host-builder"></a>Obecný tvůrce hostitele

Byl zaveden obecný tvůrce hostitele (`HostBuilder`). Tento tvůrce se dá použít pro aplikace, které nezpracovávají požadavky HTTP (zasílání zpráv, úlohy na pozadí atd.).

Další informace najdete v tématu [obecný hostitel .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Aktualizované šablony SPA

Šablony aplikace s jednou stránkou pro úhlové, reagující a reagující na Redux jsou aktualizovány tak, aby používaly standardní struktury projektů a systémy sestavení pro každé rozhraní.

Úhlová šablona je založena na úhlů CLI a šablony s reakce jsou založené na aplikaci Create-reagují-App.

Další informace najdete v části .

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>Razor Pages vyhledávání assetů Razor

V 2,1 Razor Pages vyhledejte assety Razor (například rozložení a částečné) v následujících adresářích v uvedeném pořadí:

1. Složka aktuální stránky.
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="razor-pages-in-an-area"></a>Razor Pages v oblasti

Razor Pages nyní podporují [oblasti](xref:mvc/controllers/areas). Pokud chcete zobrazit příklad oblastí, vytvořte novou Razor Pages webovou aplikaci s využitím individuálních uživatelských účtů. Razor Pages webové aplikace s jednotlivými uživatelskými účty zahrnuje */areas/identity/Pages*.

## <a name="mvc-compatibility-version"></a>Verze kompatibility MVC

Metoda <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> umožňuje aplikaci, aby se odhlásila nebo výslovný souhlas při změnách chování, které se zavedly v ASP.NET Core MVC 2,1 nebo novější.

Další informace najdete v tématu <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Migrace z 2,0 na 2,1

Viz [migrace z ASP.NET Core 2,0 na 2,1](xref:migration/20_21).

## <a name="additional-information"></a>Další informace

Úplný seznam změn najdete v [poznámkách k verzi pro ASP.NET Core 2,1](https://github.com/aspnet/Home/releases/tag/2.1.0).
