---
title: Zabezpečené aplikace Blazor ASP.NET Core Server
author: guardrex
description: Přečtěte si, jak Blazor zmírnit bezpečnostní hrozby pro serverové aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626025"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Zabezpečené aplikace ASP.NET Core Blazor Server

Podle [Javier Calvarro Nelson](https://github.com/javiercn)

Aplikace Blazor Server přijímají *stavový* model zpracování dat, kde server a klient udržují dlouhodobý vztah. Trvalý stav je udržován [obvodem](xref:blazor/state-management), který může span připojení, které jsou také potenciálně životnost.

Když uživatel navštíví server Blazor server, server vytvoří okruh v paměti serveru. Obvod indikuje prohlížeči, jaký obsah se má vykreslit, a reaguje na události, například když uživatel vybere tlačítko v uživatelském rozhraní. K provedení těchto akcí obvod vyvolá funkce jazyka JavaScript v prohlížeči uživatele a metody .NET na serveru. Tato obousměrná interakce založená na JavaScriptu se označuje jako [JavaScript interop (JS interop).](xref:blazor/call-javascript-from-dotnet)

Vzhledem k tomu, že js interop dochází přes Internet a klient používá vzdálený prohlížeč, Aplikace Blazor Server sdílejí většinu problémů se zabezpečením webových aplikací. Toto téma popisuje běžné hrozby pro aplikace Blazor Server a poskytuje pokyny ke zmírnění hrozeb zaměřené na aplikace orientované na Internet.

V omezených prostředích, například v podnikových sítích nebo intranetu, některé pokyny ke zmírnění rizika:

* Neplatí v omezeném prostředí.
* Nestojí za náklady na implementaci, protože bezpečnostní riziko je nízké v omezeném prostředí.

## <a name="blazor-server-project-template"></a>Šablona projektu Blazor Server

Šablonu projektu Blazor Server lze nakonfigurovat pro ověřování při vytvoření projektu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Postupujte podle pokynů <xref:blazor/get-started> visual studio v článku k vytvoření nového projektu Blazor Server s mechanismem ověřování.

Po výběru šablony **aplikace Blazor Server** v **dialogovém** okně Vytvořit nový ASP.NET základní webové aplikace vyberte v části **Ověřování** **možnost Změnit** .

Otevře se dialogové okno, které nabízí stejnou sadu mechanismů ověřování, které jsou k dispozici pro jiné ASP.NET základní projekty:

* **Bez ověřování**
* **Jednotlivé uživatelské účty** &ndash; uživatelské účty uživatelské účty mohou být uloženy:
  * V rámci aplikace pomocí ASP.NET [systému identit core.](xref:security/authentication/identity)
  * S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Pracovní nebo školní účty**
* **Ověřování systému Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postupujte podle pokynů kódu <xref:blazor/get-started> sady Visual Studio v článku a vytvořte nový projekt Blazor Server s mechanismem ověřování:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Přípustné hodnoty ověřování`{AUTHENTICATION}`( ) jsou uvedeny v následující tabulce.

| Mechanismus ověřování                                                                 | `{AUTHENTICATION}`Hodnotu |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Bez ověřování                                                                        | `None`                   |
| Jednotlivé<br>Uživatelé uložení v aplikaci s ASP.NET Základní identita.                        | `Individual`             |
| Jednotlivé<br>Uživatelé uložená v [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Pracovní nebo školní účty<br>Organizační ověřování pro jednoho klienta.            | `SingleOrg`              |
| Pracovní nebo školní účty<br>Organizační ověřování pro více klientů.           | `MultiOrg`               |
| Ověřování systému Windows                                                                   | `Windows`                |

Příkaz vytvoří složku s názvem s hodnotou poskytnutou `{APP NAME}` pro zástupný symbol a použije název složky jako název aplikace. Další informace naleznete v novém příkazu [dotnet](/dotnet/core/tools/dotnet-new) v průvodci jádrem rozhraní .NET.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Postupujte podle pokynů Visual <xref:blazor/get-started> Studio pro Mac v článku.

1. V části **Konfigurace nové aplikace Blazor Server** vyberte v rozevíracím přehledu **Ověřování** možnost Individuální **ověřování (v aplikaci).**

1. Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci s ASP.NET Core Identity.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

Postupujte podle pokynů rozhraní .NET Core CLI v <xref:blazor/get-started> článku a vytvořte nový projekt Serveru Blazor s mechanismem ověřování:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Přípustné hodnoty ověřování`{AUTHENTICATION}`( ) jsou uvedeny v následující tabulce.

| Mechanismus ověřování                                                                 | `{AUTHENTICATION}`Hodnotu |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Bez ověřování                                                                        | `None`                   |
| Jednotlivé<br>Uživatelé uložení v aplikaci s ASP.NET Základní identita.                        | `Individual`             |
| Jednotlivé<br>Uživatelé uložená v [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Pracovní nebo školní účty<br>Organizační ověřování pro jednoho klienta.            | `SingleOrg`              |
| Pracovní nebo školní účty<br>Organizační ověřování pro více klientů.           | `MultiOrg`               |
| Ověřování systému Windows                                                                   | `Windows`                |

Příkaz vytvoří složku s názvem s hodnotou poskytnutou `{APP NAME}` pro zástupný symbol a použije název složky jako název aplikace. Další informace naleznete v novém příkazu [dotnet](/dotnet/core/tools/dotnet-new) v průvodci jádrem rozhraní .NET.

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>Předání tokenů do aplikace Blazor Server

Ověřte aplikaci Blazor Server stejně jako u běžné aplikace Razor Pages nebo MVC. Zřízení a uložení tokenů do ověřovacího souboru cookie. Příklad:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Ukázkový kód, včetně `Startup.ConfigureServices` úplného příkladu, naleznete [v tématu Předávání tokenů do aplikace Blazor na straně serveru](https://github.com/javiercn/blazor-server-aad-sample).

Definujte třídu, která má být předávána v počátečním stavu aplikace pomocí tokenů pro přístup a aktualizaci:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Definujte službu poskytovatele tokenů **s oborem,** kterou lze použít v rámci aplikace Blazor k vyřešení tokenů z DI:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

V `Startup.ConfigureServices`oblasti přidejte služby pro:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

V souboru *_Host.cshtml* vytvořte `InitialApplicationState` a předajte jej jako parametr aplikaci:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

V `App` komponentě (*App.razor*) vyřešte službu a inicializujte ji daty z parametru:

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Ve službě, která vytváří požadavek na zabezpečené rozhraní API, vložte zprostředkovatele tokenu a načtěte token pro volání rozhraní API:

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>Vyčerpání zdrojů

Vyčerpání prostředků může dojít při interakci klienta se serverem a způsobí, že server spotřebovávají nadměrné prostředky. Nadměrná spotřeba zdrojů ovlivňuje především:

* [Cpu](#cpu)
* [Paměti](#memory)
* [Připojení klientů](#client-connections)

Útoky typu DoS (Denial of Service) se obvykle snaží vyčerpat prostředky aplikace nebo serveru. Vyčerpání prostředků však nemusí být nutně výsledkem útoku na systém. Například omezené prostředky mohou být vyčerpány z důvodu vysoké poptávky uživatelů. DoS je dále zahrnuta v části [útoky odmítnutí služby (DoS).](#denial-of-service-dos-attacks)

Prostředky mimo architekturu Blazor, jako jsou databáze a popisovače souborů (používané ke čtení a zápisu souborů), může také dojít k vyčerpání prostředků. Další informace naleznete v tématu <xref:performance/performance-best-practices>.

### <a name="cpu"></a>Procesor

K vyčerpání procesoru může dojít, když jeden nebo více klientů vynutí server k provádění intenzivní práce procesoru.

Zvažte například aplikaci Blazor Server, která vypočítá *číslo Fibonnacci*. Fibonnacciho číslo je vyrobeno z Fibonnacciho sekvence, kde každé číslo v pořadí je součtem dvou předchozích čísel. Množství práce potřebné k dosažení odpovědi závisí na délce sekvence a velikosti počáteční hodnoty. Pokud aplikace neomezuje požadavek klienta, výpočty náročné na procesor mohou dominovat času procesoru a snížit výkon jiných úkolů. Nadměrná spotřeba prostředků je problém se zabezpečením, který má vliv na dostupnost.

Vyčerpání procesoru je problémem pro všechny veřejné aplikace. V běžných webových aplikacích se požadavky a připojení neposouvají jako pojistka, ale aplikace Blazor Server neposkytují stejná ochranná opatření. Aplikace Blazor Server musí před provedením potenciálně náročné práce na procesoru obsahovat příslušné kontroly a limity.

### <a name="memory"></a>Memory (Paměť)

Vyčerpání paměti může dojít, když jeden nebo více klientů vynutit server spotřebovat velké množství paměti.

Zvažte například aplikaci na straně Blazor-server s komponentou, která přijímá a zobrazuje seznam položek. Pokud aplikace Blazor neomezuje počet povolených položek nebo počet položek vykreslených zpět klientovi, může zpracování a vykreslování náročné na paměť v paměti serveru převládat do bodu, kdy dochází k výkonu serveru. Server může dojít k chybě nebo zpomalit do bodu, který se zdá, že došlo k chybě.

Zvažte následující scénář pro udržování a zobrazení seznamu položek, které se připojují k možnému scénáři vyčerpání paměti na serveru:

* Položky ve `List<MyItem>` vlastnosti nebo poli používají paměť serveru. Pokud aplikace umožňuje, aby seznam položek rostl bez omezení, existuje riziko, že serveru dojde paměť. Nedostatek paměti způsobí ukončení aktuální relace (selhání) a všechny souběžné relace v této instanci serveru obdrží výjimku z nedostatku paměti. Chcete-li zabránit tomuto scénáři, aplikace musí použít datovou strukturu, která ukládá omezení položky pro souběžné uživatele.
* Pokud schéma stránkování se nepoužívá pro vykreslování, server používá další paměť pro objekty, které nejsou viditelné v uživatelském uživatelském terminálu. Bez omezení počtu položek může požadavky na paměť vyčerpat dostupnou paměť serveru. Chcete-li tomuto scénáři zabránit, použijte jeden z následujících přístupů:
  * Při vykreslování používejte stránkované seznamy.
  * Zobrazí pouze prvních 100 až 1 000 položek a bude vyžadovat, aby uživatel zadá kritéria vyhledávání, aby vyhledal položky za zobrazené položky.
  * Pro pokročilejší scénář vykreslování implementujte seznamy nebo mřížky, které podporují *virtualizaci*. Pomocí virtualizace seznamy vykreslují pouze podmnožinu položek, které jsou aktuálně viditelné pro uživatele. Když uživatel interaguje s posuvníkem v uživatelském rozhraní, komponenta vykreslí pouze ty položky potřebné pro zobrazení. Položky, které nejsou aktuálně požadovány pro zobrazení, mohou být uloženy ve sekundárním úložišti, což je ideální přístup. Nezobrazené položky mohou být také uloženy v paměti, což je méně ideální.

Aplikace Blazor Server nabízejí podobný programovací model jako jiné architektury uživatelského rozhraní pro stavové aplikace, jako jsou WPF, Windows Forms nebo Blazor WebAssembly. Hlavní rozdíl je, že v několika rozhraní rozhraní paměti spotřebované aplikací patří klientovi a ovlivňuje pouze jednotlivé klienta. Například aplikace Blazor WebAssembly běží výhradně na straně klienta a používá pouze prostředky klientské paměti. Ve scénáři Blazor Server paměť spotřebovaná aplikací patří k serveru a je sdílena mezi klienty na instanci serveru.

Požadavky na paměť na straně serveru jsou zvažou pro všechny aplikace Blazor Server. Většina webových aplikací je však bezstavová a paměť použitá při zpracování požadavku je uvolněna při vrácení odpovědi. Jako obecné doporučení nepovolujte klientům přidělit neomezené množství paměti jako v jakékoli jiné aplikaci na straně serveru, která zachová připojení klientů. Paměť spotřebovaná aplikací Blazor Server přetrvává déle než jeden požadavek.

> [!NOTE]
> Během vývoje profiler lze použít nebo trasování zachycené k posouzení požadavků paměti klientů. Profiler nebo trasování nezachytí paměť přidělenou konkrétnímu klientovi. Chcete-li zachytit využití paměti konkrétního klienta během vývoje, zachyťte výpis a zkontrolujte požadavek paměti všech objektů zakořeněných v okruhu uživatele.

### <a name="client-connections"></a>Připojení klientů

K vyčerpání připojení může dojít, když jeden nebo více klientů otevře příliš mnoho souběžných připojení k serveru a zabrání ostatním klientům v navazování nových připojení.

Klienti Blazor uváže jedno připojení na relaci a ponechají připojení otevřené tak dlouho, dokud je otevřené okno prohlížeče. Požadavky na server údržby všech připojení nejsou specifické pro aplikace Blazor. Vzhledem k trvalé povaze připojení a stavové povaze aplikací Blazor Server je vyčerpání připojení větším rizikem pro dostupnost aplikace.

Ve výchozím nastavení není omezen počet připojení na uživatele aplikace Blazor Server. Pokud aplikace vyžaduje omezení připojení, vezměte jeden nebo více z následujících přístupů:

* Vyžadovat ověření, což přirozeně omezuje možnost neoprávněných uživatelů připojit se k aplikaci. Aby byl tento scénář efektivní, musí být uživatelům zabráněno v zřizování nových uživatelů podle vůle.
* Omezte počet připojení na uživatele. Omezení připojení lze provést pomocí následujících přístupů. Péče o zabezpečení, aby oprávněným uživatelům přístup k aplikaci (například při stanovení limitu připojení na základě IP adresy klienta).
  * Na úrovni aplikace:
    * Rozšiřitelnost směrování koncového bodu.
    * Vyžadovat ověření pro připojení k aplikaci a sledování aktivních relací na uživatele.
    * Odmítnout nové relace po dosažení limitu.
    * Proxy WebSocket připojení k aplikaci pomocí proxy serveru, jako je například [služba Azure SignalR,](/azure/azure-signalr/signalr-overview) který multiplexu připojení z klientů do aplikace. To poskytuje aplikaci s větší kapacitou připojení, než může vytvořit jeden klient, což brání klientovi v vyčerpání připojení k serveru.
  * Na úrovni serveru: Použijte proxy/bránu před aplikací. [Azure Front Door](/azure/frontdoor/front-door-overview) například umožňuje definovat, spravovat a monitorovat globální směrování webového provozu do aplikace.

## <a name="denial-of-service-dos-attacks"></a>Útoky odmítnutí služby (DoS)

Útoky odmítnutí služby (DoS) zahrnují klienta, který způsobuje, že server vyčerpá jeden nebo více prostředků, které znepřístupňují aplikaci. Aplikace Blazor Server obsahují některé výchozí limity a spoléhají se na další limity ASP.NET core a signalr, aby se ochránily před útoky DoS:

| Limit aplikace Blazor Server                            | Popis | Výchozí |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Maximální počet odpojených obvodů, které daný server uchovává v paměti současně. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | Maximální doba, po kterou je odpojený obvod před stržením v paměti. | 3 minuty |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Maximální doba, po kterou server čeká před vypršením asynchronního vyvolání funkce JavaScript. | 1 minuta |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Maximální počet nepotvrzených vykreslovacích dávek, které server uchovává v paměti na okruh v daném okamžiku pro podporu robustního opětovného připojení. Po dosažení limitu server přestane vyrábět nové dávky vykreslení, dokud klient nepotvrdí jednu nebo více dávek. | 10 |


| SignalR a ASP.NET core limit             | Popis | Výchozí |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Velikost zprávy pro jednotlivé zprávy. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interakce s prohlížečem (klientem)

Klient spolupracuje se serverem prostřednictvím js interop události odeslání a vykreslování dokončení. JS interop komunikace jde oběma směry mezi JavaScript a .NET:

* Události prohlížeče jsou odesílány z klienta na server asynchronním způsobem.
* Server reaguje asynchronně rerendering uI podle potřeby.

### <a name="javascript-functions-invoked-from-net"></a>Funkce JavaScriptu vyvolané z rozhraní .NET

Pro volání z metod .NET do Jazyka JavaScript:

* Všechny vyvolání mají konfigurovatelný časový rámec, po kterém <xref:System.OperationCanceledException> se nezdaří, vrácení a volajícímu.
  * Pro volání (`CircuitOptions.JSInteropDefaultCallTimeout`) je výchozí časový limit jedné minuty. Chcete-li tento <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>limit nakonfigurovat, přečtěte si téma .
  * Token zrušení lze poskytnout pro řízení zrušení na základě volání. Spoléhat se na výchozí časový limit volání, pokud je to možné a časově vázané jakékoli volání klientovi, pokud je k dispozici token zrušení.
* Výsledek volání javascriptu nelze důvěřovat. Klient Blazor aplikace spuštěný v prohlížeči hledá funkci JavaScriptu, kterou chcete vyvolat. Funkce je vyvolána a je vytvořen výsledek nebo chyba. Klient se zlými úmysly se může pokusit o:
  * Způsobit problém v aplikaci vrácením chyby z funkce JavaScript.
  * Vyvolat nežádoucí chování na serveru vrácením neočekávaný výsledek z funkce JavaScript.

Postupujte podle následujících opatření, abyste se ochránili před předchozími scénáři:

* Zalomit js interop volání v rámci [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) příkazy k účtu chyby, které mohou nastat během vyvolání. Další informace naleznete v tématu <xref:blazor/handle-errors#javascript-interop>.
* Před zahájením jakékoli akce ověřte data vrácená z vyvolání interop js, včetně chybových zpráv.

### <a name="net-methods-invoked-from-the-browser"></a>Metody .NET vyvolané z prohlížeče

Nedůvěřujte volání z JavaScriptu do metod .NET. Pokud je metoda .NET vystavena javascriptu, zvažte, jak je metoda .NET vyvolána:

* Zacházejte s libovolnou metodou .NET vystavenou JavaScriptu stejně jako s veřejným koncovým bodem aplikace.
  * Ověřte vstup.
    * Ujistěte se, že hodnoty jsou v rámci očekávaných rozsahů.
    * Ujistěte se, že uživatel má oprávnění k provedení požadované akce.
  * Nepřidělujte nadměrné množství zdrojů jako součást vyvolání metody .NET. Můžete například provádět kontroly a uvádět omezení pro využití procesoru a paměti.
  * Vezměte v úvahu, že statické metody a metody instance mohou být vystaveny klientům JavaScriptu. Vyhněte se sdílení stavu mezi relacemi, pokud návrh volá pro sdílení stavu s příslušnými omezeními.
    * Pro metody vystavené prostřednictvím `DotNetReference` objektů, které jsou původně vytvořeny prostřednictvím vkládání závislostí (DI), objekty by měly být registrovány jako objekty s vymezeným oborem. To platí pro všechny služby Blazor DI, které aplikace Server používá.
    * U statických metod se vyhněte vytváření stavu, který nelze určit na klienta, pokud aplikace explicitně nesdílí návrh stavu mezi všemi uživateli na instanci serveru.
  * Vyhněte se předávání dat dodaných uživatelem v parametrech voláním JavaScriptu. Pokud je přenos dat v parametrech naprosto vyžadován, ujistěte se, že kód JavaScript zpracovává předávání dat bez zavedení chyb zabezpečení [skriptování napříč weby (XSS).](#cross-site-scripting-xss) Například nezapisujte uživatelem zadaná data do modelu DOCUMENT `innerHTML` Object Model (DOM) nastavením vlastnosti prvku. Zvažte použití zásad zabezpečení obsahu `eval` [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) zakázat a další nebezpečné javascriptové primitivy.
* Vyhněte se implementaci vlastní odesílání vyvolání .NET nad implementací odesílání rozhraní. Vystavení metod .NET prohlížeči je pokročilý scénář, který Blazor se nedoporučuje pro obecný vývoj.

### <a name="events"></a>Události

Události poskytují vstupní bod Blazor do aplikace Server. Stejná pravidla pro zabezpečení koncových bodů ve Blazor webových aplikacích platí pro zpracování událostí v serverových aplikacích. Klient se zlými úmysly může odesílat všechna data, která chce odeslat jako datovou část události.

Příklad:

* Událost změny pro `<select>` může odeslat hodnotu, která není v rámci možností, které aplikace prezentovány klientovi.
* Může `<input>` odeslat všechna textová data na server, obcházet ověření na straně klienta.

Aplikace musí ověřit data pro každou událost, kterou aplikace zpracovává. Blazor [Součásti rámcových formulářů](xref:blazor/forms-validation) provádějí základní ověření. Pokud aplikace používá vlastní komponenty formulářů, musí být vlastní kód zapsán k ověření dat událostí podle potřeby.

BlazorUdálosti serveru jsou asynchronní, takže více událostí může být odesláno na server dříve, než aplikace bude mít čas reagovat vytvořením nového vykreslení. To má některé bezpečnostní důsledky, aby zvážila. Omezení akce klienta v aplikaci musí být provedeno uvnitř obslužné rutiny událostí a není závislé na aktuální matné zobrazení stavu.

Zvažte součást čítače, která by měla umožnit uživateli inkresovat čítač maximálně třikrát. Tlačítko pro zvýšení čítače je podmíněně `count`založeno na hodnotě :

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Klient může odeslat jednu nebo více událostí přírůstku před rozhraní ms vytvoří nové vykreslení této součásti. Výsledkem je, `count` že může být zvýšil *více než třikrát* uživatelem, protože tlačítko není odebrána uživatelského rozhraní dostatečně rychle. Správný způsob, jak dosáhnout `count` limitu tří přírůstků, je uveden v následujícím příkladu:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Přidáním `if (count < 3) { ... }` kontroly uvnitř obslužné `count` rutiny je rozhodnutí o zvýšení založeno na aktuálním stavu aplikace. Rozhodnutí není založeno na stavu ui, jak tomu bylo v předchozím příkladu, které mohou být dočasně zastaralé.

### <a name="guard-against-multiple-dispatches"></a>Ochrana před více násobnými odesláními

Pokud zpětné volání události vyvolá dlouho běžící operaci asynchronně, jako je například načítání dat z externí služby nebo databáze, zvažte použití guard. Ochranka může zabránit uživateli ve frontě do více operací, zatímco operace probíhá s vizuální zpětnou vazbu. Následující kód komponenty `true` `GetForecastAsync` se nastaví `isLoading` na zatímco získá data ze serveru. Zatímco `isLoading` `true`je , tlačítko je zakázáno v ui:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Vzor guard demonstrovaný v předchozím příkladu funguje, pokud je operace na `async` - `await` pozadí prováděna asynchronně se vzorem.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Zrušit předčasně a vyhnout se použití po likvidaci

Kromě použití guard, jak je popsáno v guard proti více <xref:System.Threading.CancellationToken> [odeslání](#guard-against-multiple-dispatches) části, zvažte použití zrušit dlouhotrvající operace při vyřazení komponenty. Tento přístup má další výhodu v tom, že se v součástech vyhýbá použití *po odstranění:*

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Vyhněte se událostem, které vytvářejí velké množství dat

Některé události dom, `oninput` `onscroll`například nebo , může produkovat velké množství dat. Nepoužívejte tyto Blazor události v serverových aplikacích.

## <a name="additional-security-guidance"></a>Další bezpečnostní pokyny

Pokyny pro zabezpečení aplikací ASP.NET Blazor Core se vztahují na serverové aplikace a jsou popsány v následujících částech:

* [Protokolování a citlivá data](#logging-and-sensitive-data)
* [Ochrana informací při přenosu pomocí protokolu HTTPS](#protect-information-in-transit-with-https)
* [Skriptování napříč weby (XSS)](#cross-site-scripting-xss)
* [Ochrana mezi původem](#cross-origin-protection)
* [Kliknutí -jacking](#click-jacking)
* [Otevřená přesměrování](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Protokolování a citlivá data

Interakce mezi klientem a serverem jsou zaznamenány v protokolech <xref:Microsoft.Extensions.Logging.ILogger> serveru s instancemi. Blazorzabraňuje protokolování citlivých informací, jako jsou skutečné události nebo vstupy a výstupy JS interop.

Dojde-li k chybě na serveru, rozhraní upozorní klienta a strhává relace. Ve výchozím nastavení se klientovi zobrazí obecná chybová zpráva, která se zobrazí v vývojářských nástrojích prohlížeče.

Chyba na straně klienta neobsahuje zásobník volání a neposkytuje podrobnosti o příčině chyby, ale protokoly serveru tyto informace obsahují. Pro účely vývoje citlivé informace o chybách mohou být zpřístupněny klientovi povolením podrobné chyby.

Povolit podrobné chyby s:

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`konfiguračního klíče. Například nastavte `ASPNETCORE_DETAILEDERRORS` proměnnou prostředí na `true`hodnotu .

> [!WARNING]
> Vystavení informací o chybách klientům v Internetu představuje bezpečnostní riziko, kterému je třeba se vždy vyhnout.

### <a name="protect-information-in-transit-with-https"></a>Ochrana informací při přenosu pomocí protokolu HTTPS

BlazorServer SignalR používá pro komunikaci mezi klientem a serverem. BlazorServer obvykle používá SignalR přenos, který vyjednává, což je obvykle WebSockets.

BlazorServer nezajišťuje integritu a důvěrnost dat odeslaných mezi serverem a klientem. Vždy používejte protokol HTTPS.

### <a name="cross-site-scripting-xss"></a>Skriptování mezi weby (XSS)

Skriptování mezi servery (XSS) umožňuje neautorizované straně spustit libovolnou logiku v kontextu prohlížeče. Ohrožená aplikace může potenciálně spustit libovolný kód na straně klienta. Tato chyba zabezpečení by mohla být použita k provedení řady škodlivých akcí proti serveru:

* Odešlete falešné/neplatné události na server.
* Odeslání selhání nebo neplatné dokončení vykreslení.
* Vyhněte se odesílání dokončení vykreslení.
* Odeslání interop volání z JavaScriptu do .NET.
* Upravte odpověď interop volání z .NET do JavaScriptu.
* Vyhněte se odesílání .NET do výsledků js interop.

Server Blazor framework podniká kroky k ochraně před některými z předchozích hrozeb:

* Zastaví vytváření nových aktualizací ui, pokud klient není potvrzení vykreslovací dávky. Nakonfigurován `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`o .
* Zaosí všechny volání .NET na JavaScript po jedné minutě bez přijetí odpovědi od klienta. Nakonfigurován `CircuitOptions.JSInteropDefaultCallTimeout`o .
* Provádí základní ověření na všech vstupech pocházejících z prohlížeče během JS interop:
  * Odkazy .NET jsou platné a typu očekávaného metodou .NET.
  * Data nejsou poškozená.
  * Správný počet argumentů pro metodu jsou přítomny v datové části.
  * Argumenty nebo výsledek lze rekonstruovat správně před vyvoláním metody.
* Provádí základní ověření ve všech vstupech pocházejících z prohlížeče z odeslaných událostí:
  * Událost má platný typ.
  * Data pro událost lze rekonstruovat.
  * K události je přidružena obslužná rutina události.

Kromě záruk, které rámec implementuje, musí být aplikace kódována vývojářem, aby byla chráněna před hrozbami a přijala vhodná opatření:

* Při zpracování událostí vždy ověřte data.
* Přijmout vhodná opatření po obdržení neplatných údajů:
  * Ignorujte data a vraťte se. To umožňuje aplikaci pokračovat ve zpracování požadavků.
  * Pokud aplikace zjistí, že vstup je nelegitimní a nelze je vyrobit legitimním klientem, vyvoláte výjimku. Vyvolání výjimky strhává okruh a ukončí relaci.
* Nedůvěřujte chybové zprávě poskytované dokončením dávky vykreslení zahrnuté v protokolech. Chyba je poskytována klientem a nelze obecně důvěřovat, protože klient může být ohrožena.
* Nedůvěřujte vstupu na js interop volání v obou směrech mezi JavaScript a .NET metody.
* Aplikace je zodpovědná za ověření, že obsah argumentů a výsledků jsou platné, i v případě, že argumenty nebo výsledky jsou správně rekonstruovat.

Aby existovala chyba zabezpečení XSS, musí aplikace na vykreslené stránce začlenit vstup uživatele. BlazorSoučásti serveru provést krok kompilace, kde je značka v souboru *.razor* transformována do procedurální logiky Jazyka C#. Za běhu logika Jazyka C# vytvoří *strom vykreslení* popisující prvky, text a podřízené součásti. To se aplikuje na dom prohlížeče prostřednictvím sekvence javascriptových instrukcí (nebo je serializován do HTML v případě prerendering):

* Uživatelský vstup vykreslený pomocí normální syntaxe Razor (například) neodhaluje chybu zabezpečení XSS, `@someStringValue`protože syntaxe Razor je přidána do DOM pomocí příkazů, které mohou psát pouze text. I v případě, že hodnota obsahuje značky HTML, hodnota se zobrazí jako statický text. Při předběžném vykreslování je výstup kódován html, který také zobrazuje obsah jako statický text.
* Značky skriptů nejsou povoleny a neměly by být zahrnuty do stromu vykreslení komponent aplikace. Pokud je značka skriptu zahrnuta do značky komponenty, je generována chyba v době kompilace.
* Autoři komponent mohou vytvářet komponenty v C# bez použití Razor. Autor komponenty je zodpovědný za použití správných api při vyzařování výstupu. Například použití `builder.AddContent(0, someUserSuppliedString)` a *ne* `builder.AddMarkupContent(0, someUserSuppliedString)`, protože posledně uvedené by mohlo vytvořit chybu zabezpečení XSS.

Jako součást ochrany proti útokům XSS zvažte implementaci zmírnění xss, jako je [například zásady zabezpečení obsahu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Další informace naleznete v tématu <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Ochrana mezi původem

Útoky napříč původem zahrnují klienta z jiného původu, který provádí akci proti serveru. Škodlivá akce je obvykle požadavek GET nebo formulář POST (Cross-Site Request Forgery, CSRF), ale otevření škodlivého WebSocket je také možné. BlazorServerové aplikace nabízejí [stejné záruky SignalR jako všechny ostatní aplikace používající nabídku centrálního protokolu](xref:signalr/security):

* BlazorServerové aplikace lze přistupovat cross-origin, pokud další opatření, aby se zabránilo. Chcete-li zakázat přístup mezi zdroji, zakažte cors v koncovém bodě `DisableCorsAttribute` přidáním middlewaru CORS do kanálu a přidáním metadat Blazor koncového bodu nebo omezte sadu povolených počátků [konfigurací SignalR pro sdílení prostředků mezi zdroji](xref:signalr/security#cross-origin-resource-sharing).
* Pokud je povolena CORS, další kroky mohou být vyžadovány k ochraně aplikace v závislosti na konfiguraci CORS. Pokud je cors globálně povolena, může Blazor být cors `DisableCorsAttribute` zakázán pro server rozbočovač `hub.MapBlazorHub()`přidáním metadat do metadat koncového bodu po volání .

Další informace naleznete v tématu <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Kliknutí -jacking

Click-jacking zahrnuje vykreslování webu jako `<iframe>` vnitřního webu z jiného původu, aby se uživatel i obelstil, aby provedl akce na webu, který je pod útokem.

Chcete-li aplikaci chránit `<iframe>`před vykreslováním uvnitř `X-Frame-Options` aplikace , použijte [zásady zabezpečení obsahu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) a záhlaví. Další informace naleznete v [tématu MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Otevřená přesměrování

Při Blazor spuštění relace aplikace Server server server provede základní ověření adres URL odeslaných jako součást spuštění relace. Rámec zkontroluje, zda je základní adresa URL nadřazenou aktuální adresou URL před vytvořením okruhu. Rámec neprovádí žádné další kontroly.

Když uživatel vybere odkaz na straně klienta, adresa URL pro odkaz je odeslána na server, který určuje, jaké akce má být. Aplikace může například provést navigaci na straně klienta nebo označit prohlížeči, aby přešel do nového umístění.

Součásti mohou také programově aktivovat navigační `NavigationManager`požadavky pomocí aplikace . V takových případech může aplikace provést navigaci na straně klienta nebo označit prohlížeči, aby přešel do nového umístění.

Součásti musí:

* Nepoužívejte vstup uživatele jako součást argumentů navigačního volání.
* Ověřte argumenty a ujistěte se, že cíl je povolen aplikací.

V opačném případě může uživatel se zlými úmysly vynutit, aby prohlížeč přešel na web řízený útočníkem. V tomto scénáři útočník triky aplikace do použití některé hod `NavigationManager.Navigate` uživatele jako součást vyvolání metody.

Tato rada platí také při vykreslování odkazů jako součást aplikace:

* Pokud je to možné, použijte relativní odkazy.
* Před zahrnutím na stránku ověřte, zda jsou absolutní cíle odkazů platné.

Další informace naleznete v tématu <xref:security/preventing-open-redirects>.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace

Pokyny k ověřování a <xref:security/blazor/index>autorizaci naleznete v tématu .

## <a name="security-checklist"></a>Kontrolní seznam zabezpečení

Následující seznam aspektů zabezpečení není vyčerpávající:

* Ověřte argumenty z událostí.
* Ověřte vstupy a výsledky z js interop volání.
* Vyhněte se použití (nebo ověření předem) vstup uživatele pro volání .NET na JS interop.
* Zabrání klientovi v přidělení neomezené množství paměti.
  * Data v rámci komponenty.
  * `DotNetObject`odkazy vrácené klientovi.
* Chraňte se před několika zásilkami.
* Zrušte dlouhotrvající operace při vyřazení součásti.
* Vyhněte se událostem, které vytvářejí velké množství dat.
* Pokud je to nevyhnutelné, `NavigationManager.Navigate` vyhněte se použití uživatelského vstupu jako součásti volání a ověřte vstup uživatele pro adresy URL proti sadě povolených původů.
* Neprováděte rozhodnutí o autorizaci na základě stavu ui, ale pouze ze stavu komponenty.
* Zvažte použití [zásad zabezpečení obsahu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) k ochraně před útoky XSS.
* Zvažte použití csp a [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) k ochraně proti přepojit.
* Ujistěte se, že nastavení CORS jsou vhodné Blazor při povolení CORS nebo explicitně zakázat CORS pro aplikace.
* Vyzkoušejte, zda limity na straně Blazor serveru pro aplikaci poskytují přijatelné uživatelské prostředí bez nepřijatelné úrovně rizika.
