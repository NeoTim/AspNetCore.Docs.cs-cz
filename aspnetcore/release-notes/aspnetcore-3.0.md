---
title: Co je nového v ASP.NET Core 3.0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976973"
---
# <a name="whats-new-in-aspnet-core-30"></a>Co je nového v ASP.NET Core 3.0

Tento článek upozorňuje na nejvýznamnější změny v ASP.NET Jádrem 3.0 s odkazy na příslušnou dokumentaci.

## Blazor

Blazorje nový framework v ASP.NET Core pro vytváření interaktivního webového uživatelského rozhraní na straně klienta s rozhraním .NET:

* Vytvořte bohaté interaktivní umělá použití jazyka C# místo JavaScriptu.
* Sdílejte logiku aplikace na straně serveru a klienta napsanou v rozhraní .NET.
* Vykreslete ui jako HTML a CSS pro širokou podporu prohlížeče, včetně mobilních prohlížečů.

Blazorrámcových scénářů:

* Opakovaně použitelné součásti ui (komponenty Razor)
* Směrování na straně klienta
* Rozložení komponent
* Podpora vkládání závislostí
* Formuláře a ověřování
* Vytváření knihoven komponent pomocí knihoven tříd Razor
* Interoperabilita JavaScriptu

Další informace naleznete v tématu <xref:blazor/index>.

### <a name="opno-locblazor-server"></a>BlazorServer

Blazorodděluje logiku vykreslování komponent od způsobu použití aktualizací ui. BlazorServer poskytuje podporu pro hostování komponent Razor na serveru v aplikaci ASP.NET Core. Aktualizace ui jsou SignalR zpracovávány přes připojení. BlazorServer je podporován v ASP.NET Core 3.0.

### <a name="opno-locblazor-webassembly-preview"></a>BlazorWebassembly (náhled)

Blazoraplikace lze také spouštět přímo v prohlížeči pomocí runtime rozhraní .NET založeného na webové sestavě. BlazorWebAssembly je ve verzi Preview a *není* podporován a není podporován v ASP.NET Jádrem 3.0. BlazorWebAssembly bude podporovánv budoucí verzi ASP.NET Core.

### <a name="razor-components"></a>Komponenty pro břitvy

Blazoraplikace jsou sestaveny z komponent. Součásti jsou samostatné bloky uživatelského rozhraní ( UI), například stránka, dialog nebo formulář. Součásti jsou normální třídy .NET, které definují logiku vykreslování rozhraní a obslužné rutiny událostí na straně klienta. Bohaté interaktivní webové aplikace můžete vytvářet bez JavaScriptu.

Komponenty Blazor v jsou obvykle vytvářeny pomocí syntaxe Razor, přirozené směsi HTML a C#. Razor komponenty jsou podobné Razor Stránky a MVC zobrazení v tom, že oba používají Razor. Na rozdíl od stránek a zobrazení, které jsou založeny na modelu požadavku odpověď, komponenty se používají speciálně pro zpracování složení uživatelského panelu.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* Je populární, vysoce výkonné RPC (vzdálené volání procedur) framework.
* Nabízí umíněný přístup k vývoji rozhraní API.
* Využívá moderní technologie, jako jsou:

  * HTTP/2 pro přepravu.
  * Vyrovnávací paměti protokolu jako jazyk popisu rozhraní.
  * Binární formát serializace.
* Poskytuje funkce, jako jsou:

  * Authentication
  * Obousměrné streamování a řízení toku.
  * Zrušení a časové lhůty.

funkce gRPC v ASP.NET Core 3.0 obsahuje:

* [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; ASP.NET Core framework pro hostování služeb gRPC. gRPC na ASP.NET Core se integruje se standardními funkcemi ASP.NET Core, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Klient gRPC pro .NET Core, `HttpClient`který staví na známém .
* [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC integrace `HttpClientFactory`klienta s .

Další informace naleznete v tématu <xref:grpc/index>.

## SignalR

Pokyny [ SignalR k migraci najdete v tématu Update code.](xref:migration/22-to-30#signalr) SignalRNyní `System.Text.Json` používá serializovat/rekonstruovat zprávy JSON. Pokyny [k](xref:migration/22-to-30#switch-to-newtonsoftjson) obnovení serializátoru `Newtonsoft.Json`na bázi č.

V jazyce JavaScript a SignalRklienti .NET pro byla přidána podpora pro automatické opětovné připojení. Ve výchozím nastavení se klient pokusí okamžitě znovu připojit a v případě potřeby to zkusit po 2, 10 a 30 sekundách. Pokud se klient úspěšně znovu připojí, obdrží nové ID připojení. Automatické opětovné připojení je opt-in:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Intervaly opětovného připojení lze zadat předáním pole milisekundových dob trvání:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Vlastní implementace může být předána pro úplnou kontrolu intervalů opětovného připojení.

Pokud se opětovné připojení nezdaří po posledním intervalu opětovného připojení:

* Klient se domnívá, že připojení je offline.
* Klient se přestane pokoušet o opětovné připojení.

Během pokusů o opětovné připojení aktualizujte uživatelské rozhraní aplikace a upozorněte uživatele, že se pokouší o opětovné připojení.

Chcete-li poskytnout zpětnou vazbu SignalR uživatelského rozhraní při přerušení připojení, klientské rozhraní API bylo rozšířeno tak, aby zahrnovalo následující obslužné rutiny událostí:

* `onreconnecting`: Poskytuje vývojářům možnost zakázat uživatelské rozhraní nebo dát uživatelům vědět, že aplikace je offline.
* `onreconnected`: Poskytuje vývojářům možnost aktualizovat ui po obnovení připojení.

Následující kód `onreconnecting` používá k aktualizaci ui při pokusu o připojení:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Následující kód `onreconnected` používá k aktualizaci uI na připojení:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR3.0 a novější poskytuje vlastní prostředek pro obslužné rutiny autorizace, když metoda rozbočovače vyžaduje autorizaci. Prostředek je instancí třídy `HubInvocationContext`. Zahrnuje: `HubInvocationContext`

* `HubCallerContext`
* Název volané metody rozbočovače.
* Argumenty metody rozbočovače.

Vezměme si následující příklad aplikace chatovací místnosti, která umožňuje více organizací přihlášení prostřednictvím služby Azure Active Directory. Kdokoli s účtem Microsoft se může přihlásit k chatu, ale pouze členové vlastnící organizace mohou zakázat uživatelům nebo zobrazit historii chatu uživatelů. Aplikace může omezit určité funkce od konkrétních uživatelů.

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

V předchozím kódu `DomainRestrictedRequirement` slouží jako `IAuthorizationRequirement`vlastní . Vzhledem `HubInvocationContext` k tomu, že parametr prostředku je předáván, vnitřní logika může:

* Zkontrolujte kontext, ve kterém je volána hub.
* Rozhoduj se o povolení uživatele provádět jednotlivé metody hubu.

Jednotlivé metody rozbočovače mohou být označeny názvem zásady, kterou kód kontroluje za běhu. Jako klienti se pokoušejí volat `DomainRestrictedRequirement` jednotlivé metody Hub, obslužná rutina běží a řídí přístup k metodám. Na základě způsobu `DomainRestrictedRequirement` přístupu k ovládacím prvkům:

* Metodu `SendMessage` mohou volat všichni přihlášení uživatelé.
* Historii uživatelů mohou zobrazit pouze `@jabbr.net` uživatelé, kteří se přihlásili pomocí e-mailové adresy.
* Pouze `bob42@jabbr.net` může zakázat uživatelům z chatovací místnosti.

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

Vytvoření `DomainRestricted` zásady může zahrnovat:

* V *Startup.cs*přidání nové zásady.
* Zadejte `DomainRestrictedRequirement` vlastní požadavek jako parametr.
* Registrace `DomainRestricted` pomocí autorizačního middlewaru.

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

SignalRrozbočovače používají [směrování koncových bodů](xref:fundamentals/routing). SignalRpřipojení k rozbočovači bylo dříve provedeno explicitně:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

V předchozí verzi vývojáři potřebovali drátovat řadiče, stránky Razor a rozbočovače na různých místech. Explicitní připojení má za následek řadu téměř identických segmentů směrování:

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

SignalRRozbočovače 3.0 lze směrovat prostřednictvím směrování koncových bodů. Při směrování koncových bodů lze obvykle `UseRouting`nakonfigurovat všechny směrování v :

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core 3.0 SignalR přidáno:

Streamování klient-server. Při streamování klient-server mohou metody na straně serveru trvat instance buď nebo `IAsyncEnumerable<T>` . `ChannelReader<T>` V následující ukázce `UploadStream` jazyka C# metoda v centru obdrží datový proud řetězců z klienta:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Klientské aplikace .NET `IAsyncEnumerable<T>` mohou `ChannelReader<T>` předat `stream` buď nebo `UploadStream` instanci jako argument výše uvedené metody Hub.

Po `for` dokončení smyčky a ukončení místní funkce je odesláno dokončení datového proudu:

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

Klientské aplikace JavaScriptu SignalR `Subject` používají (nebo [Předmět RxJS)](https://rxjs.dev/api/index/class/Subject)pro `stream` argument výše uvedené metody `UploadStream` Hub.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

Kód JavaScriptu může `subject.next` použít metodu pro zpracování řetězců, jak jsou zachyceny a připraveny k odeslání na server.

```javascript
subject.next("example");
subject.complete();
```

Pomocí kódu, jako jsou dva předchozí úryvky, lze vytvořit prostředí streamování v reálném čase.

## <a name="new-json-serialization"></a>Nová serializace JSON

ASP.NET Core 3.0 <xref:System.Text.Json> nyní používá ve výchozím nastavení pro serializaci JSON:

* Čte a zapisuje JSON asynchronně.
* Je optimalizován pro Text UTF-8.
* Obvykle vyšší výkon `Newtonsoft.Json`než .

Chcete-li přidat Json.NET do ASP.NET Core 3.0, viz [Přidání podpory formátu JSON založeného na newtonsoftu.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Nové direktivy břitvy

Následující seznam obsahuje nové direktivy Razor:

* [`@attribute`](xref:mvc/views/razor#attribute)&ndash; Směrnice `@attribute` použije daný atribut pro třídu generované stránky nebo zobrazení. Například, `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements)&ndash; Směrnice `@implements` implementuje rozhraní pro generované třídy. Například, `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 podporuje ověřování a autorizaci pro webová api a spa

ASP.NET Core 3.0 nabízí ověřování v jednostránkových aplikacích (SPA) pomocí podpory autorizace webového rozhraní API. ASP.NET základní identity pro ověřování a ukládání uživatelů je v kombinaci s [IdentityServer4](https://identityserver.io/) pro implementaci Open ID Connect.

IdentityServer4 je openid připojit a OAuth 2.0 framework pro ASP.NET Core 3.0. Umožňuje následující funkce zabezpečení:

* Ověřování jako služba (AaaS)
* Jednotné přihlašování/vypínání (SSO) přes více typů aplikací
* Řízení přístupu pro api
* Federační brána

Další informace naleznete [v dokumentaci identityServer4](http://docs.identityserver.io/en/latest/index.html) nebo [ověřování a autorizace pro spa](xref:security/authentication/identity/spa).

## <a name="certificate-and-kerberos-authentication"></a>Ověřování certifikátu a protokolu Kerberos

Ověření certifikátu vyžaduje:

* Konfigurace serveru pro přijímání certifikátů.
* Přidání ověřovacího middlewaru do souboru `Startup.Configure`.
* Přidání služby ověřování `Startup.ConfigureServices`certifikátů do souboru .

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

* Přijímejte certifikáty podepsané svým držitelem.
* Zkontrolujte odvolání certifikátu.
* Zkontrolujte, zda je v nabízeném certifikátu správné příznaky použití.

Výchozí uživatelský objekt zabezpečení je vytvořen z vlastností certifikátu. Uživatelský objekt zabezpečení obsahuje událost, která umožňuje doplnění nebo nahrazení objektu zabezpečení. Další informace naleznete v tématu <xref:security/authentication/certauth>.

[Ověřování systému Windows](/windows-server/security/windows-authentication/windows-authentication-overview) bylo rozšířeno na Linux a macOS. V předchozích verzích bylo ověřování systému Windows omezeno na [služby IIS](xref:host-and-deploy/iis/index) a [httpsys](xref:fundamentals/servers/httpsys). V ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) má možnost používat Vyjednat, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)a [NTLM na Windows](/windows-server/security/kerberos/ntlm-overview), Linux a macOS pro windows domény-připojen hostitelé. Podpora kestrelu těchto schémat ověřování je poskytována balíčkem [Microsoft.AspNetCore.Authentication.Negotiate NuGet.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Stejně jako u ostatních ověřovacích služeb nakonfigurujte ověřovací aplikaci pro celou a pak nakonfigurujte službu:

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

Požadavky hostitele:

* Hostitelé systému Windows musí mít k uživatelskému účtu hostujícímu aplikaci [přidaná názvy hlavních](/windows/win32/ad/service-principal-names) názvů služeb (SPN).
* Počítače s Linuxem a macOS musí být připojeny k doméně.
  * Hlavní aktualizace služeb při obslužné síti musí být vytvořeny pro webový proces.
  * [Soubory záložek klíčů](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) musí být generovány a nakonfigurovány v hostitelském počítači.

Další informace naleznete v tématu <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Změny šablony

Webové šablony uživatelského uživatelského nastavení (Razor Pages, MVC s ovladačem a zobrazení) byly odstraněny následující:

* UI souhlasu se soubory cookie již není zahrnuto. Informace o povolení funkce souhlasu se soubory cookie v aplikaci ASP.NET aplikaci generovanou šablonou Core 3.0 naleznete v tématu <xref:security/gdpr>.
* Skripty a související statické datové zdroje jsou nyní odkazovány jako místní soubory namísto použití souborů CDN. Další informace naleznete v [tématu Skripty a související statické datové zdroje jsou nyní odkazovány jako místní soubory namísto použití sítí CDN na základě aktuálního prostředí (aspnet/AspNetCore.Docs #14350).](https://github.com/dotnet/AspNetCore.Docs/issues/14350)

Úhlová šablona byla aktualizována tak, aby používala úhlové 8.

Šablona třídy Razor (RCL) ve výchozím nastavení ve výchozím nastavení na vývoj komponenty Razor. Nová možnost šablony v sadě Visual Studio poskytuje podporu šablon pro stránky a zobrazení. Při vytváření RCL ze šablony v příkazovém prostředí předavěte `--support-pages-and-views` možnost (`dotnet new razorclasslib --support-pages-and-views`).

## <a name="generic-host"></a>Obecný hostitel

Používají šablony ASP.NET Core 3.0 <xref:fundamentals/host/generic-host>. Předchozí použité <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>verze . Použití obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) poskytuje lepší integraci aplikací ASP.NET Core s jinými serverovými scénáři, které nejsou specifické pro web. Další informace naleznete v tématu [HostBuilder nahrazuje WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Konfigurace hostitele

Před vydáním ASP.NET Core 3.0 byly proměnné `ASPNETCORE_` prostředí s předponou načteny pro konfiguraci hostitele webového hostitele. V 3.0 `AddEnvironmentVariables` se používá k načtení `DOTNET_` proměnných prostředí `CreateDefaultBuilder`s předponou pro konfiguraci hostitele s .

### <a name="changes-to-startup-constructor-injection"></a>Změny vstřikování konstruktoru startupu

Obecný hostitel podporuje pouze následující `Startup` typy pro vkládání konstruktoru:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Všechny služby mohou být stále vloženy `Startup.Configure` přímo jako argumenty metody. Další informace naleznete [v tématu Obecný hostitel omezuje vstřikování konstruktoru spuštění (aspnet/Oznámení #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* Konfigurace kestrelu byla aktualizována pro migraci na obecný hostitel. V 3.0, Kestrel je konfigurován na tvůrce `ConfigureWebHostDefaults`webhost poskytované .
* Adaptéry připojení byly odebrány z Kestrel a nahrazeny připojení middleware, který je podobný HTTP Middleware v kanálu ASP.NET Core, ale pro připojení nižší úrovně.
* Transportní vrstva Kestrel byla vystavena `Connections.Abstractions`jako veřejné rozhraní v .
* Nejednoznačnost mezi záhlavími a přívěsy byla vyřešena přesunutím koncových záhlaví do nové kolekce.
* Synchronní vstupně-v., jako `HttpRequest.Body.Read`je například , jsou běžným zdrojem nedostatku podprocesů, což vede k selhání aplikace. V 3.0 `AllowSynchronousIO` je ve výchozím nastavení zakázán.

Další informace naleznete v tématu <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 ve výchozím nastavení povolena

HTTP/2 je ve výchozím nastavení povolen v Kestrel u koncových bodů HTTPS. Podpora protokolu HTTP/2 pro službu IIS nebo soubor HTTP.sys je povolena, pokud ji podporuje operační systém.

## <a name="eventcounters-on-request"></a>EventCounters na vyžádání

Hosting EventSource, `Microsoft.AspNetCore.Hosting`, vydává následující <xref:System.Diagnostics.Tracing.EventCounter> nové typy související s příchozí požadavky:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Směrování koncového bodu

Směrování koncových bodů, které umožňuje, aby rozhraní (například MVC) dobře fungovaly s middlewarem, je vylepšeno:

* Pořadí middleware a koncové body je konfigurovatelné v `Startup.Configure`kanálu zpracování požadavků .
* Koncové body a middleware dobře tvoří s dalšími ASP.NET technologiemi založenými na jádru, jako jsou kontroly stavu.
* Koncové body můžete implementovat zásady, jako je například CORS nebo autorizace, v middleware a MVC.
* Filtry a atributy lze umístit na metody v řadičích.

Další informace naleznete v tématu <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Kontroly stavu

Kontroly stavu používají směrování koncových bodů s obecným hostitelem. V `Startup.Configure`aplikace `MapHealthChecks` volejte tvůrce koncového bodu s adresou URL koncového bodu nebo relativní cestou:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Koncové body kontroly stavu mohou:

* Zadejte jeden nebo více povolených hostitelů/portů.
* Vyžadovat autorizaci.
* Vyžadovat CORS.

Další informace najdete v těchto článcích:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Kanály na httpcontext

Nyní je možné číst tělo požadavku a zapsat <xref:System.IO.Pipelines> tělo odpovědi pomocí rozhraní API. Prostředek <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`vlastnost poskytuje, <xref:System.IO.Pipelines.PipeReader> které lze použít ke čtení těla požadavku. Prostředek <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`vlastnost poskytuje, <xref:System.IO.Pipelines.PipeWriter> které lze použít k zápisu těla odpovědi. `HttpRequest.BodyReader`je analogový `HttpRequest.Body` proud. `HttpResponse.BodyWriter`je analogový `HttpResponse.Body` proud.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Vylepšené zasílání zpráv o chybách ve správě IIS

Chyby při spuštění při hostování aplikací ASP.NET Core ve službě IIS nyní vytvářejí bohatší diagnostická data. Tyto chyby jsou hlášeny do protokolu událostí systému Windows s trasování zásobníku, pokud je to možné. Kromě toho jsou všechna upozornění, chyby a neošetřené výjimky zaznamenány do protokolu událostí systému Windows.

## <a name="worker-service-and-worker-sdk"></a>Pracovní služba a sada SDK pracovníka

.NET Core 3.0 zavádí novou šablonu aplikace pracovní služby. Tato šablona poskytuje výchozí bod pro zápis dlouhotrvajících služeb v rozhraní .NET Core.

Další informace naleznete v tématu:

* [Základní pracovníci rozhraní .NET jako služby systému Windows](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Vylepšení middlewaru předávaných záhlaví

V předchozích verzích ASP.NET <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> Core, volání a byly problematické při nasazení na Azure Linux nebo za reverzní proxy než IIS. Oprava předchozích verzí je popsána v části [Předat schéma pro linuxové a neisis reverzní proxy servery](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Tento scénář je opraven v ASP.NET jádrem 3.0. Hostitel povolí [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` pokud `true`je proměnná prostředí nastavena na . `ASPNETCORE_FORWARDEDHEADERS_ENABLED`je nastavena na `true` v našich nánosů.

## <a name="performance-improvements"></a>Zlepšení výkonu

ASP.NET Core 3.0 obsahuje mnoho vylepšení, která snižují využití paměti a zlepšují propustnost:

* Snížení využití paměti při použití integrovaného kontejneru vkládání závislostí pro služby s vymezeným oborem.
* Snížení přidělení v rámci, včetně middleware scénáře a směrování.
* Snížení využití paměti pro připojení WebSocket.
* Vylepšení snížení paměti a propustnost pro připojení HTTPS.
* Nový optimalizovaný a plně asynchronní serializátor JSON.
* Snížení využití paměti a zlepšení propustnosti v parsování formulářů.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 běží pouze na rozhraní .NET Core 3.0

Od ASP.NET jádra 3.0 rozhraní .NET Framework již není podporovanou cílovou architekturou. Projekty zaměřené na rozhraní .NET Framework mohou pokračovat plně podporovaným způsobem pomocí [verze .NET Core 2.1 LTS](https://dotnet.microsoft.com/download/dotnet-core/2.1). Většina ASP.NET balíčky související s jádrem 2.1.x budou podporovány neomezeně dlouho, po dobu tří LTS pro .NET Core 2.1.

Informace o migraci naleznete [v tématu Port kódu z rozhraní .NET Framework do jádra .NET](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Použití sdíleného rámce ASP.NET Core

Sdílené rozhraní ASP.NET Core 3.0 obsažené v [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app)již `<PackageReference />` nevyžaduje explicitní prvek v souboru projektu. Sdílená architektura je automaticky `Microsoft.NET.Sdk.Web` odkazována při použití sady SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Sestavení odebraná ze sdíleného rozhraní ASP.NET Core

Nejvýznamnější sestavení odebraná ze sdíleného rámce ASP.NET Core 3.0 jsou:

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET). Chcete-li přidat Json.NET do ASP.NET Core 3.0, viz [Přidání podpory formátu JSON založeného na newtonsoftu.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3.0 `System.Text.Json` zavádí pro čtení a psaní JSON. Další informace naleznete v [tématu Nová serializace JSON](#new-json-serialization) v tomto dokumentu.
* [Entity Framework Core](/ef/core/)

Úplný seznam sestavení odebraných ze sdílené ho rozhraní naleznete v [tématu Sestavení odebírá z Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755). Další informace o motivaci pro tuto změnu naleznete [v tématu Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 