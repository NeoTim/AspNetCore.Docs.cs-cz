---
title: Co je nového v ASP.NET Core 2.1
author: isaac2004
description: Seznamte se s novými funkcemi v ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: af5807b782d4acec8c7d40111dc508dfa6127057
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667543"
---
# <a name="whats-new-in-aspnet-core-21"></a>Co je nového v ASP.NET Core 2.1

Tento článek upozorňuje na nejvýznamnější změny v ASP.NET jádrem 2.1 s odkazy na příslušnou dokumentaci.

## SignalR

SignalRbyl přepsán pro ASP.NET Core 2.1. ASP.NET SignalR Core obsahuje řadu vylepšení:

* Zjednodušený model škálování.
* Nový klient JavaScriptu bez závislosti jQuery.
* Nový kompaktní binární protokol založený na MessagePack.
* Podpora vlastních protokolů.
* Nový model odezvy streamování.
* Podpora pro klienty založené na holé WebSockets.

Další informace naleznete [v SignalRtématu ASP.NET Core ](xref:signalr/introduction).

## <a name="razor-class-libraries"></a>Knihovny RCL (Razor Class Library)

ASP.NET Core 2.1 usnadňuje vytváření a zařazuje cítění na základě Razor v knihovně a sdílet je mezi více projekty. Nová sada Razor SDK umožňuje vytváření souborů Razor do projektu knihovny tříd, který lze zabalit do balíčku NuGet. Zobrazení a stránky v knihovnách jsou automaticky zjištěny a mohou být přepsány aplikací. Integrací kompilace Razor do sestavení:

* Doba spuštění aplikace je výrazně rychlejší.
* Rychlé aktualizace zobrazení Razor a stránek za běhu jsou stále k dispozici jako součást iterativního vývojového pracovního postupu.

Další informace naleznete v [tématu Vytvoření opakovaně použitelného ui pomocí projektu Knihovna tříd razor](xref:razor-pages/ui-class).

## <a name="identity-ui-library--scaffolding"></a>Knihovna uživatelského & identity

ASP.NET Core 2.1 poskytuje [ASP.NET základní identitu](xref:security/authentication/identity) jako [knihovnu třídy Razor](xref:razor-pages/ui-class). Aplikace, které obsahují identity můžete použít nové identity sakuru selektivně přidat zdrojový kód obsažený v knihovně třídy Razor identity (RCL). Můžete chtít generovat zdrojový kód, takže můžete upravit kód a změnit chování. Můžete například instruovat složky scaffolder generovat kód použitý v registraci. Generovaný kód má přednost před stejným kódem v rcl identity.

Aplikace, které **nezahrnují** ověřování můžete použít identity sakum přidat balíček Identity RCL. Máte možnost vybrat kód identity, který má být generován.

Další informace naleznete [v tématu Identita lešení v ASP.NET projektů core](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Se zvýšeným zaměřením na zabezpečení a ochranu osobních údajů je důležité povolit protokol HTTPS pro webové aplikace. Prosazování HTTPS je stále přísnější na webu. Weby, které nepoužívají protokol HTTPS, jsou považovány za nezabezpečené. Prohlížeče (Chromium, Mozilla) začínají vynucovat, že webové funkce musí být používány ze zabezpečeného kontextu. [GDPR](xref:security/gdpr) vyžaduje použití protokolu HTTPS k ochraně soukromí uživatelů. Při použití protokolu HTTPS v produkčním prostředí je důležité, pomocí protokolu HTTPS ve vývoji může pomoci zabránit problémům v nasazení (například nezabezpečené odkazy). ASP.NET Core 2.1 obsahuje řadu vylepšení, která usnadňují použití protokolu HTTPS ve vývoji a konfiguraci protokolu HTTPS v produkčním prostředí. Další informace naleznete v [tématu Enforce HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Zapnuto ve výchozím nastavení

Pro usnadnění bezpečného vývoje webových stránek je nyní protokol HTTPS ve výchozím nastavení povolen. Počínaje 2.1, Kestrel poslouchá, `https://localhost:5001` když je k dispozici certifikát místního rozvoje. Je vytvořen vývojový certifikát:

* Jako součást .NET Core SDK první spuštění při prvním použití sady SDK.
* Ruční použití nového `dev-certs` nástroje.

Spusťte `dotnet dev-certs https --trust` důvěřovat certifikátu.

### <a name="https-redirection-and-enforcement"></a>Přesměrování a vynucení protokolu HTTPS

Webové aplikace obvykle potřebují naslouchat na http i https, ale pak přesměrovat veškerý provoz HTTP na HTTPS. V 2.1 byl zaveden specializovaný middleware pro přesměrování HTTPS, který inteligentně přesměrovává na základě přítomnosti portů konfigurace nebo vázaného serveru.

Použití protokolu HTTPS lze dále vynutit pomocí [protokolu HTTP Strict Transport Security Protocol (HSTS).](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) HSTS instruuje prohlížeče, aby vždy přistupovaly k webu přes HTTPS. ASP.NET Core 2.1 přidává middleware HSTS, který podporuje možnosti pro maximální stáří, subdomény a seznam předběžného načtení HSTS.

### <a name="configuration-for-production"></a>Konfigurace pro výrobu

V produkčním prostředí musí být protokol HTTPS explicitně nakonfigurován. V 2.1 bylo přidáno výchozí schéma konfigurace pro konfiguraci protokolu HTTPS pro Kestrel. Aplikace lze nakonfigurovat tak, aby používaly:

* Více koncových bodů včetně adres URL. Další informace naleznete [v tématu Implementace webového serveru Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Certifikát, který se má použít pro protokol HTTPS ze souboru na disku nebo z úložiště certifikátů.

## <a name="gdpr"></a>GDPR

ASP.NET Core poskytuje api a šablony, které pomáhají splnit některé obecné [požadavky nařízení EU o ochraně osobních údajů (GDPR).](https://www.eugdpr.org/) Další informace najdete v tématu [podpora GDPR v ASP.NET Core](xref:security/gdpr). [Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ukazuje, jak používat a umožňuje otestovat většinu rozšiřujících bodů GDPR a api přidaných do ASP.NET šablon Core 2.1.

## <a name="integration-tests"></a>Integrační testy

Je zaveden nový balíček, který zjednodušuje vytváření a provádění testů. Balíček [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) zpracovává následující úkoly:

* Zkopíruje soubor*\*závislostí ( .deps*) z testované aplikace do složky *přihrádky* testovacího projektu.
* Nastaví kořen obsahu na kořen projektu testované aplikace tak, aby statické soubory a stránky/zobrazení byly nalezeny při provádění testů.
* Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění testované aplikace pomocí [serveru TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

Následující test používá [xUnit](https://xunit.github.io/) ke kontrole, že index stránka načte s kódem stavu úspěchu a se správným obsah-typ záhlaví:

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

Další informace naleznete v tématu [testy integrace.](xref:test/integration-tests)

## <a name="apicontroller-actionresultt"></a>[ApiController], Výsledek\<akce T>

ASP.NET Core 2.1 přidává nové programovací konvence, které usnadňují vytváření čistých a popisných webových rozhraní API. `ActionResult<T>`je nový typ přidán umožnit aplikaci vrátit buď typ odpovědi nebo jakýkoli jiný výsledek akce (podobně jako IActionResult), zatímco stále označující typ odpovědi. Atribut `[ApiController]` byl také přidán jako způsob, jak se přihlásit k webovým konvencím a chováním specifickým pro webové rozhraní API.

Další informace naleznete v [tématu Vytváření webových api s ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IhttpClientFactory

ASP.NET Core 2.1 `IHttpClientFactory` obsahuje novou službu, která usnadňuje `HttpClient` konfiguraci a využití instancí v aplikacích. `HttpClient`již má koncept delegování obslužné rutiny, které by mohly být propojeny pro odchozí požadavky HTTP. Továrna:

* Umožňuje registraci instancí `HttpClient` na pojmenované klienta intuitivnější.
* Implementuje Polly obslužnou rutinu, která umožňuje Polly politiky, které mají být použity pro opakování, jističe atd.

Další informace naleznete [v tématu Initiate HTTP Requests](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Konfigurace přepravy kestrelu

S vydáním ASP.NET Core 2.1, Kestrel výchozí přenos již není založen na Libuv, ale místo toho na spravované sokety. Další informace naleznete [v tématu Implementace webového serveru Kestrel: Konfigurace přenosu](xref:fundamentals/servers/kestrel#transport-configuration).

## <a name="generic-host-builder"></a>Obecný tvůrce hostitelů

Byl zaveden obecný`HostBuilder`tvůrce hostitelů ( ). Tento tvůrce lze použít pro aplikace, které nezpracovávají požadavky HTTP (zasílání zpráv, úlohy na pozadí atd.).

Další informace naleznete [v tématu Obecný hostitel rozhraní .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Aktualizované šablony SPA

Jednoduché šablony aplikace pro úhlové, reagovat a reagovat s Redux jsou aktualizovány tak, aby používaly standardní struktury projektu a vytvářely systémy pro každou architekturu.

Úhlová šablona je založena na úhlovém cli a šablony React jsou založeny na create-react-app.

Další informace naleznete v tématu:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>Razor Stránky hledat Razor aktiva

V 2.1, Razor Stránky hledat Razor datových zdrojů (například rozložení a částečné) v následujících adresářů v uvedeném pořadí:

1. Aktuální stránky.
1. */Stránky/Sdílené/*
1. */Zobrazení/Sdíleno/*

## <a name="razor-pages-in-an-area"></a>Holicí strojek stránky v oblasti

Razor Pages nyní podporují [oblasti](xref:mvc/controllers/areas). Chcete-li zobrazit příklad oblastí, vytvořte novou webovou aplikaci Razor Pages s jednotlivými uživatelskými účty. Webová aplikace Razor Pages s jednotlivými uživatelskými účty obsahuje */Oblasti/Identity/Pages*.

## <a name="mvc-compatibility-version"></a>Verze kompatibility MVC

Tato <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> metoda umožňuje aplikaci vyjádřit výslovný nesouhlas nebo odhlásit se z potenciálně narušujících změn chování zavedených v ASP.NET core MVC 2.1 nebo novější.

Další informace naleznete v tématu <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Migrace z 2.0 na 2.1

Viz [Migrace z ASP.NET jádra 2.0 do 2.1](xref:migration/20_21).

## <a name="additional-information"></a>Další informace

Úplný seznam změn naleznete v [ASP.NET základní poznámky k verzi 2.1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).
