---
title: Co je nového v ASP.NET Core 3,0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: aspnetcore-3.0
ms.openlocfilehash: a32307f6de01e03e548cbde2c38c9469db74b506
ms.sourcegitcommit: fe88748b762525cb490f7e39089a4760f6a73a24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71688045"
---
# <a name="whats-new-in-aspnet-core-30"></a>Co je nového v ASP.NET Core 3,0

Tento článek popisuje nejvýznamnější změny v ASP.NET Core 3,0 s odkazy na příslušnou dokumentaci.

## <a name="blazor"></a>Blazor

Blazor je nová architektura v ASP.NET Core pro vytváření interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET:

* Vytvářejte bohatě interaktivní uživatelská rozhraní C# pomocí místo JavaScriptu.
* Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.
* Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.

Scénáře podporované Blazor Framework:

* Opakovaně použitelné součásti uživatelského rozhraní (komponenty Razor)
* Směrování na straně klienta
* Rozložení komponent
* Podpora pro vkládání závislostí
* Formuláře a ověřování
* Sestavení knihoven součástí s knihovnami tříd Razor
* Interoperabilita JavaScriptu

Další informace naleznete v tématu <xref:blazor/index>.

### <a name="blazor-server"></a>Blazor Server

Blazor odpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní. Blazor Server poskytuje podporu pro hostování komponent Razor na serveru v aplikaci ASP.NET Core. Aktualizace uživatelského rozhraní se zpracovávají přes připojení k signalizaci. Blazor Server je podporován v ASP.NET Core 3,0.

### <a name="blazor-webassembly-preview"></a>Blazor WebAssembly (Preview)

Aplikace Blazor se dají spustit také přímo v prohlížeči pomocí technologie .NET runtime založené na WebAssembly. Blazor WebAssembly je ve verzi Preview a *není* podporované v ASP.NET Core 3,0. Blazor WebAssembly bude podporováno v budoucí verzi ASP.NET Core.

### <a name="razor-components"></a>Komponenty Razor

Aplikace Blazor jsou sestavené z komponent. Komponenty jsou samostatné bloky uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář. Komponenty jsou normální třídy .NET, které definují logiku vykreslování uživatelského rozhraní a obslužné rutiny událostí na straně klienta. Můžete vytvářet bohatě interaktivní webové aplikace bez JavaScriptu.

Komponenty v Blazor jsou obvykle vytvářeny pomocí syntaxe Razor, což je přirozená směs HTML C#a. Komponenty Razor jsou podobné zobrazením Razor Pages a MVC v tom, že používají syntaxi Razor. Na rozdíl od stránek a zobrazení, které jsou založeny na modelu požadavek-odpověď, se komponenty používají konkrétně pro zpracování kompozice uživatelského rozhraní.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* Je oblíbeným a vysoce výkonným rozhraním vzdáleného volání procedur (RPC).
* Nabízí dogmatickýmý přístup k vývoji rozhraní API, který je prvním kontraktem.
* Používá moderní technologie, jako jsou:

  * HTTP/2 pro přenos.
  * Vyrovnávací paměti protokolu jako jazyk popisu rozhraní.
  * Formát binární serializace.
* Poskytuje funkce, jako například:

  * Ověřování
  * Obousměrné streamování a řízení toku.
  * Zrušení a vypršení časového limitu.

funkce gRPC v ASP.NET Core 3,0 obsahuje:

* [Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; rozhraní ASP.NET Core Framework pro hostování služeb Grpc Services. gRPC se na ASP.NET Core integruje se standardními ASP.NET Core funkcemi, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.
* [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; klient Grpc pro .NET Core, který sestaví na známém `HttpClient`.
* [Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; Grpc Client Integration `HttpClientFactory`with.

Další informace naleznete v tématu <xref:grpc/index>.

## <a name="signalr"></a>SignalR

Pokyny k migraci najdete v tématu [aktualizace kódu signálu](xref:migration/22-to-30#signalr) . Nástroj Signal nyní používá `System.Text.Json` k serializaci nebo deserializaci zpráv JSON. Pokyny pro obnovení `Newtonsoft.Json`serializátoru založeného na [Newtonsoft. JSON najdete v tématu Přepnutí do. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson) .

V jazyce JavaScript a v klientech rozhraní .NET pro signalizaci byla přidána podpora pro automatické opětovné připojení. Ve výchozím nastavení se klient pokusí znovu připojit hned a v případě potřeby opakovat po 2, 10 a 30 sekundách. Pokud se klient úspěšně znovu připojí, obdrží nové ID připojení. Automatické opětovné připojení je výslovný souhlas:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Intervaly opětovného připojení lze zadat předáním pole doby trvání založené na milisekundách:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Vlastní implementaci lze předat v rámci úplného řízení intervalů opětovného připojení.

Pokud se opětovné připojení nezdařilo po intervalu posledního opětovného připojení:

* Klient považuje připojení za offline.
* Klient se ukončí pokus o opětovné připojení.

Během opakovaného pokusu o připojení aktualizujte uživatelské rozhraní aplikace a upozorněte uživatele, že probíhá pokus o opětovné připojení.

Pokud chcete poskytnout zpětnou vazbu uživatelského rozhraní při přerušení připojení, rozhraní API pro klienta signalizace bylo rozbaleno, aby zahrnovalo následující obslužné rutiny událostí:

* `onreconnecting`:  Dává vývojářům možnost zakázat uživatelské rozhraní nebo upozornit uživatele na to, že aplikace je offline.
* `onreconnected`: Dává vývojářům možnost aktualizovat uživatelské rozhraní po opětovném navázání připojení.

Následující kód používá `onreconnecting` k aktualizaci uživatelského rozhraní při pokusu o připojení:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Následující kód používá `onreconnected` k aktualizaci uživatelského rozhraní při připojení:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

Návěstí 3,0 a novější poskytuje vlastní prostředek pro obslužné rutiny autorizace, pokud metoda rozbočovače vyžaduje autorizaci. Prostředek je instancí `HubInvocationContext`. `HubInvocationContext` Obsahuje:

* `HubCallerContext`
* Název vyvolané metody centra
* Argumenty metody hub.

Vezměte v úvahu následující příklad aplikace chatovací místnosti umožňující přihlášení více organizací prostřednictvím Azure Active Directory. Kdokoli s účet Microsoft se může přihlásit ke konverzaci, ale jenom členové vlastnící organizace můžou zakázat nebo zobrazit historie chatu uživatelů. Aplikace může omezit určité funkce pro konkrétní uživatele.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

V předchozím kódu `DomainRestrictedRequirement` slouží jako vlastní `IAuthorizationRequirement`. Vzhledem k `HubInvocationContext` tomu, že se parametr prostředku předává, interní logika může:

* Zkontrolujte kontext, ve kterém se centrum volá.
* Rozhodnutí, jak umožnit uživateli spouštět jednotlivé metody rozbočovače.

Jednotlivé metody rozbočovače je možné dekorovat pomocí názvů zásad, které kontrolují kód v době běhu. Jelikož se klienti pokoušejí zavolat jednotlivé metody centra, obslužná `DomainRestrictedRequirement` rutina se spustí a řídí přístup k metodám. Na základě způsobu `DomainRestrictedRequirement` přístupu k ovládacím prvkům:

* Všichni přihlášení uživatelé mohou zavolat `SendMessage` metodu.
* Historie uživatelů mohou zobrazit pouze uživatelé, kteří `@jabbr.net` se přihlásili pomocí e-mailové adresy.
* Může `bob42@jabbr.net` zakázat jenom uživatele z konverzační místnosti.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

`DomainRestricted` Vytváření zásad může zahrnovat:

* V *Startup.cs*přidejte novou zásadu.
* Zadejte vlastní `DomainRestrictedRequirement` požadavek jako parametr.
* Probíhá `DomainRestricted` registrace pomocí middleware autorizace.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

Centra signalizace používají [Směrování koncového bodu](xref:fundamentals/routing). Připojení ke službě Signal hub bylo dříve provedeno explicitně:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

V předchozí verzi vývojáři potřebují k nastavování řadičů v různých umístěních, stránek Razor a Center na různých místech. Explicitní výsledky připojení v řadě téměř stejných segmentů směrování:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

Centra signálu 3,0 se dají směrovat přes směrování koncových bodů. Se směrováním koncových bodů se obvykle dá nakonfigurovat všechny `UseRouting`směrování v nástroji:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Byl přidán signál ASP.NET Core 3,0:

Streamování mezi klientem a serverem. U datových proudů mezi klientem a serverem můžou metody na straně serveru přebírat instance `IAsyncEnumerable<T>` nebo. `ChannelReader<T>` V následující C# ukázce `UploadStream` bude metoda v centru přijímat datový proud řetězců z klienta:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Klientské aplikace `IAsyncEnumerable<T>` .NET můžou předat instanci nebo `ChannelReader<T>` `UploadStream` jako `stream` argument metody hub výše.

Až se `for` smyčka dokončí a místní funkce se ukončí, pošle se dokončování datového proudu:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Klientské aplikace JavaScriptu používají pro `Subject` `stream` argument `UploadStream` výše uvedené metody centra signál (nebo [Předmět RxJS](https://rxjs.dev/api/index/class/Subject)).

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

Kód jazyka JavaScript může použít `subject.next` metodu ke zpracování řetězců při jejich zachycení a připravení na odeslání na server.

```javascript
subject.next("example");
subject.complete();
```

Pomocí kódu, například dvou předchozích fragmentů, lze vytvořit prostředí streamování v reálném čase.

### <a name="new-json-serialization"></a>Nová serializace JSON

ASP.NET Core 3,0 teď používá <xref:System.Text.Json> standardně pro serializaci JSON:

* Čte a zapisuje JSON asynchronně.
* Je optimalizován pro text v kódování UTF-8.
* Obvykle vyšší výkon než `Newtonsoft.Json`.

Pokud chcete přidat Json.NET do ASP.NET Core 3,0, přečtěte si téma [Přidání podpory formátu JSON založeného na Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Nové direktivy Razor

Následující seznam obsahuje nové direktivy Razor:

* [@attribute](xref:mvc/views/razor#attribute)&ndash; Direktivapoužijedanýatributnatřídu`@attribute` generované stránky nebo zobrazení. Například, `@attribute [Authorize]`.
* [@implements](xref:mvc/views/razor#implements)&ndash; Direktivaimplementuje`@implements` rozhraní pro generovanou třídu. Například, `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 podporuje ověřování a autorizaci pro webová rozhraní API a jednostránkové.

[IdentityServer4](https://identityserver.io) je rozhraní OpenID Connect a OAuth 2,0 framework pro ASP.NET Core 3,0. IdentityServer4 umožňuje následující funkce zabezpečení:

* Ověřování jako služba (AaaS)
* Jednotné přihlašování/vypínání (SSO) nad více typy aplikací
* Řízení přístupu pro rozhraní API
* Federační brána

Další informace najdete v tématu [Vítá vás IdentityServer4](http://docs.identityserver.io/en/latest/index.html).

## <a name="certificate-and-kerberos-authentication"></a>Ověřování pomocí certifikátů a protokolu Kerberos

Ověřování certifikátu vyžaduje:

* Konfigurace serveru pro příjem certifikátů.
* Přidání middleware ověřování v `Startup.Configure`.
* Přidání služby ověřování certifikátů v `Startup.ConfigureServices`nástroji.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Možnosti ověřování certifikátů zahrnují možnost:

* Přijměte certifikáty podepsané svým držitelem.
* Kontrolovat odvolání certifikátu.
* Ověřte, že certifikát proffered má v něm správné příznaky použití.

Výchozí objekt zabezpečení uživatele je vytvořen z vlastností certifikátu. Objekt zabezpečení uživatele obsahuje událost, která umožňuje doplňování nebo nahrazení objektu zabezpečení. Další informace naleznete v tématu <xref:security/authentication/certauth>.

[Ověřování systému Windows](/windows-server/security/windows-authentication/windows-authentication-overview) bylo rozšířeno na Linux a MacOS. V předchozích verzích bylo ověřování systému Windows omezeno na [službu IIS](xref:host-and-deploy/iis/index) a [HttpSys](xref:fundamentals/servers/httpsys). V ASP.NET Core 3,0 může [Kestrel](xref:fundamentals/servers/kestrel) používat funkci Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)a [NTLM v systémech Windows](/windows-server/security/kerberos/ntlm-overview), Linux a MacOS pro hostitele připojené k doméně systému Windows. Podporu Kestrel těchto schémat ověřování poskytuje balíček [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) . Stejně jako u ostatních ověřovacích služeb nakonfigurujte aplikaci ověřování na šířku a pak nakonfigurujte službu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Požadavky na hostitele:

* Hostitelé systému Windows musí mít k uživatelskému účtu, který je hostitelem aplikace, přidány [hlavní názvy služby](/windows/win32/ad/service-principal-names) (SPN).
* Počítače se systémem Linux a macOS musí být připojeny k doméně.
  * Pro webový proces musí být vytvořeny hlavní názvy služby (SPN).
  * Na hostitelském počítači musí být vygenerovány a nakonfigurovány [soubory keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) .

Další informace naleznete v tématu <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Změny šablony

Šablony webového uživatelského rozhraní (Razor Pages, MVC s kontrolérem a zobrazeními) mají odebraný následující:

* Uživatelské rozhraní pro vyjádření souhlasu souborů cookie již není zahrnuto. Pokud chcete povolit funkci vyjádření souhlasu souborů cookie v aplikaci vygenerovanou šablonou ASP.NET Core <xref:security/gdpr>3,0, přečtěte si téma.
* Skripty a související statické prostředky jsou nyní odkazovány jako místní soubory namísto použití sítě CDN. Další informace najdete v tématu [skripty a související statické prostředky jsou nyní odkazovány jako místní soubory namísto použití sítě CDN v závislosti na aktuálním prostředí (ASPNET/AspNetCore. Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).

Úhlová šablona se aktualizovala tak, aby používala úhlové 8.

Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor. Nová možnost šablony v aplikaci Visual Studio poskytuje podporu šablon pro stránky a zobrazení. Při vytváření RCL ze šablony v příkazovém prostředí, předejte `-support-pages-and-views` možnost (`dotnet new razorclasslib -support-pages-and-views`).

## <a name="generic-host"></a>Obecný hostitel

Šablony ASP.NET Core 3,0 používají <xref:fundamentals/host/generic-host>. Používaly <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>se předchozí verze. Použití obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) poskytuje lepší integraci ASP.NET Corech aplikací s jinými serverovými scénáři, které nejsou specifické pro web. Další informace najdete v tématu [HostBuilder nahrazuje WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Konfigurace hostitele

Před vydáním verze ASP.NET Core 3,0 byly načteny proměnné prostředí s `ASPNETCORE_` předponou pro konfiguraci hostitele webového hostitele. V 3,0 `AddEnvironmentVariables` se používá k načtení proměnných prostředí s `DOTNET_` předponou pro pro konfiguraci hostitele s `CreateDefaultBuilder`.

### <a name="changes-to-startup-contructor-injection"></a>Změny spouštěcího injektáže konstruktoru

Obecný hostitel podporuje pouze následující typy pro `Startup` injektáže konstruktoru:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Všechny služby mohou být stále vloženy přímo jako argumenty `Startup.Configure` metody. Další informace najdete v tématu [obecný hostitel omezuje úvodní vkládání spouštěcího konstruktoru (ASPNET/](https://github.com/aspnet/Announcements/issues/353)informers #353).

## <a name="kestrel"></a>Kestrel

* Konfigurace Kestrel byla aktualizována pro migraci na obecného hostitele. V 3,0 je Kestrel nakonfigurovaný na tvůrci webového hostitele, který poskytuje `ConfigureWebHostDefaults`.
* Připojovací adaptéry byly odebrány z Kestrel a nahrazeny middlewarem připojení, který je v kanálu ASP.NET Core podobný Middlewari HTTP, ale pro připojení nižší úrovně.
* Přenosová vrstva Kestrel byla vystavena jako veřejné rozhraní v `Connections.Abstractions`.
* Nejednoznačnost mezi záhlavími a přípojnou čárkou byla vyřešena přesunutím koncových hlaviček do nové kolekce.
* Synchronní rozhraní API pro vstupně- `HttpRequest.Body.Read`výstupní operace, jako je, jsou běžným zdrojem vyčerpání vlákna, který vede k chybám aplikace. V 3,0 `AllowSynchronousIO` je ve výchozím nastavení zakázáno.

Další informace naleznete v tématu <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 povoleno ve výchozím nastavení

Protokol HTTP/2 je ve výchozím nastavení povolený pro koncové body HTTPS v Kestrel. Podpora HTTP/2 pro IIS nebo HTTP. sys je povolená, když operační systém podporuje.

## <a name="eventcounters-on-request"></a>EventCounters na žádost

Hostování EventSource, `Microsoft.AspNetCore.Hosting`vygeneruje následující nové <xref:System.Diagnostics.Tracing.EventCounter> typy týkající se příchozích požadavků:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Směrování koncových bodů

Směrování koncového bodu, které umožňuje architekturám (například MVC) pracovat dobře s middlewarem, je vylepšeno:

* Pořadí middlewaru a koncových bodů je možné nakonfigurovat v kanálu `Startup.Configure`zpracování požadavků.
* Koncové body a middleware se dobře zakládají s dalšími ASP.NET Core technologiemi, jako jsou například kontroly stavu.
* Koncové body můžou implementovat zásadu, třeba CORS nebo autorizaci, a to v middlewaru i MVC.
* Filtry a atributy lze umístit na metody v řadičích.

Další informace naleznete v tématu <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Kontroly stavu

Kontroly stavu používají směrování koncových bodů u obecného hostitele. V `Startup.Configure`nástroji zavolejte `MapHealthChecks` na tvůrce koncového bodu s adresou URL koncového bodu nebo relativní cestou:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Koncové body kontrol stavu můžou:

* Zadejte minimálně jednoho povoleného hostitele nebo portů.
* Vyžadovat autorizaci.
* Vyžadovat CORS

Další informace najdete v následujících článcích:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Kanály na HttpContext

Nyní je možné číst text žádosti a zapsat tělo odpovědi pomocí <xref:System.IO.Pipelines> rozhraní API. Rozhraní <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`vlastnost poskytuje objekt <xref:System.IO.Pipelines.PipeReader> , který lze použít ke čtení textu žádosti. Rozhraní <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`vlastnost poskytuje <xref:System.IO.Pipelines.PipeWriter> , kterou lze použít k zápisu textu odpovědi. `HttpRequest.BodyReader`je analogkou `HttpRequest.Body` datového proudu. `HttpResponse.BodyWriter`je analogkou `HttpResponse.Body` datového proudu.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Vylepšené zasílání zpráv o chybách ve službě IIS

Chyby při spuštění při hostování ASP.NET Corech aplikací ve službě IIS teď vytváří rozsáhlejší diagnostická data. Tyto chyby jsou hlášeny do protokolu událostí systému Windows s trasováním zásobníku, kdykoli je to možné. Kromě toho jsou všechna upozornění, chyby a neošetřené výjimky protokolovány do protokolu událostí systému Windows.

## <a name="worker-service-and-worker-sdk"></a>Služba pracovních procesů a sada SDK pracovních procesů

.NET Core 3,0 zavádí novou šablonu aplikace služby pracovního procesu. Tato šablona poskytuje výchozí bod pro psaní dlouhodobě běžících služeb v .NET Core.

Další informace naleznete v tématu:

* [Pracovní procesy .NET Core jako služby systému Windows](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Vylepšení middlewaru u předávaných hlaviček

V předchozích verzích ASP.NET Core volání <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> a <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> bylo problematické při nasazení do Azure Linux nebo za libovolný reverzní proxy server jiný než IIS. Oprava pro předchozí verze je popsána v [části dopředného schématu pro reverzní proxy servery se systémy Linux a non-IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Tento scénář je opravený v ASP.NET Core 3,0. Hostitel povoluje [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) , pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` je proměnná prostředí nastavena na `true`. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`je nastavené `true` na obrázky kontejnerů.

## <a name="performance-improvements"></a>Vylepšení výkonu

ASP.NET Core 3,0 obsahuje mnoho vylepšení, která omezují využití paměti a zvyšují propustnost:

* Snížení využití paměti při použití integrovaného kontejneru vkládání závislostí pro vymezené služby.
* Snížení počtu přidělení napříč rámec, včetně scénářů middlewaru a směrování.
* Snížení využití paměti pro připojení protokolu WebSocket.
* Snižování paměti a vylepšení propustnosti pro připojení HTTPS.
* Nový optimalizovaný a plně asynchronní serializátor JSON.
* Snížení využití paměti a vylepšení propustnosti při analýze formuláře.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3,0 běží pouze na .NET Core 3,0

Od ASP.NET Core 3,0 .NET Framework již není podporovanou cílovou architekturou. Projekty cílené na .NET Framework můžou v plně podporovaném způsobem dál používat [LTS verzi pro .NET Core 2,1](https://www.microsoft.com/net/download/dotnet-core/2.1). Až do 3 LTS období pro .NET Core 2,1 se budou podporovat i balíčky související s ASP.NET Core 2.1. x po dobu tří let.

Informace o migraci najdete v tématu [portování kódu z .NET Framework do .NET Core](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Použití sdíleného rozhraní ASP.NET Core

Sdílené rozhraní ASP.NET Core 3,0, obsažené v souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), již nevyžaduje explicitní `<PackageReference />` prvek v souboru projektu. Sdílené rozhraní je automaticky odkazováno při použití `Microsoft.NET.Sdk.Web` sady SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Sestavení odebraná z ASP.NET Core sdílené rozhraní

Nejvýznamnější sestavení odebraný ze sdílené architektury ASP.NET Core 3,0 jsou:

* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.NET). Pokud chcete přidat Json.NET do ASP.NET Core 3,0, přečtěte si téma [Přidání podpory formátu JSON založeného na Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3,0 zavádí `System.Text.Json` pro čtení a zápis formátu JSON. Další informace najdete v tématu [Nová serializace JSON](#new-json-serialization) v tomto dokumentu.
* [Entity Framework Core](/ef/core/)

Úplný seznam sestavení odebraných ze sdíleného rozhraní najdete v tématu sestavení, [která jsou odebírána z Microsoft. AspNetCore. App 3,0](https://github.com/aspnet/AspNetCore/issues/3755). Další informace o motivaci této změny najdete v tématu přemístění [změn do Microsoft. AspNetCore. app v 3,0](https://github.com/aspnet/Announcements/issues/325) a [první pohled na změny, které přicházejí v ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
