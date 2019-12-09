---
title: Vynutilit HTTPS v ASP.NET Core
author: rick-anderson
description: Naučte se vyžadovat protokol HTTPS/TLS ve ASP.NET Core webové aplikaci.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 032105c67e15ab94635ae6fadea103450c7eb0fb
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944236"
---
# <a name="enforce-https-in-aspnet-core"></a>Vynutilit HTTPS v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto dokumentu se dozvíte, jak:

* Vyžadovat protokol HTTPS pro všechny požadavky.
* Přesměrovat všechny požadavky HTTP na HTTPS.

Žádné rozhraní API nemůže klientovi zabránit v posílání citlivých dat na první požadavek.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a>Projekty API
>
> Nepoužívejte [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) pro webová rozhraní API, která přijímají citlivé informace. `RequireHttpsAttribute` používá ke přesměrování prohlížečů z HTTP na HTTPS stavové kódy HTTP. Klienti rozhraní API nemusí pochopit nebo dodržovat přesměrování z HTTP na HTTPS. Tito klienti mohou odesílat informace prostřednictvím protokolu HTTP. Webové rozhraní API by mělo mít jednu z těchto:
>
> * Neslouchat na HTTP.
> * Ukončete připojení se stavovým kódem 400 (chybný požadavek) a neobsluhuje požadavek.
>
> ## <a name="hsts-and-api-projects"></a>Projekty HSTS a API
>
> Výchozí projekty API neobsahují [HSTS](#hsts) , protože HSTS je všeobecně pouze instrukcí prohlížeče. Další volající, jako jsou telefony nebo desktopové aplikace, **nedodržují** pokyny. I v prohlížečích má jedno ověřované volání rozhraní API přes protokol HTTP rizika v nezabezpečených sítích. Bezpečným přístupem je nakonfigurovat projekty API tak, aby naslouchaly jenom a reagovaly přes HTTPS.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a>Projekty API
>
> Nepoužívejte [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) pro webová rozhraní API, která přijímají citlivé informace. `RequireHttpsAttribute` používá ke přesměrování prohlížečů z HTTP na HTTPS stavové kódy HTTP. Klienti rozhraní API nemusí pochopit nebo dodržovat přesměrování z HTTP na HTTPS. Tito klienti mohou odesílat informace prostřednictvím protokolu HTTP. Webové rozhraní API by mělo mít jednu z těchto:
>
> * Neslouchat na HTTP.
> * Ukončete připojení se stavovým kódem 400 (chybný požadavek) a neobsluhuje požadavek.

::: moniker-end

## <a name="require-https"></a>Vyžadovat protokol HTTPS

Doporučujeme, aby provozní ASP.NET Core Web Apps používaly:

* Middleware pro přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) pro přesměrování požadavků HTTP na HTTPS.
* HSTS middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) k odeslání hlaviček HSTS (http Strict Transport Security Protocol) do klientů.

> [!NOTE]
> Aplikace nasazené v konfiguraci reverzního proxy serveru umožňují proxy zpracovávat zabezpečení připojení (HTTPS). Pokud proxy také zpracovává přesměrování protokolu HTTPS, není nutné používat middleware pro přesměrování protokolu HTTPS. Pokud proxy server také zpracovává zápis hlaviček HSTS (například [nativní podporu HSTS ve službě IIS 10,0 (1709) nebo novější](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), aplikace HSTS middleware není aplikací požadována. Další informace najdete v tématu [výslovný nedostatek protokolu HTTPS/HSTS při vytváření projektu](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>UseHttpsRedirection

Následující kód volá `UseHttpsRedirection` ve třídě `Startup`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

Předchozí zvýrazněný kód:

* Používá výchozí [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* Použije výchozí [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), pokud není přepsána proměnnou prostředí `ASPNETCORE_HTTPS_PORT` nebo [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).

Doporučujeme místo trvalých přesměrování použít dočasné přesměrování. Ukládání odkazů do mezipaměti může způsobit nestabilní chování ve vývojových prostředích. Pokud upřednostňujete odeslání trvalého stavového kódu přesměrování, když je aplikace v nevývojovém prostředí, přečtěte si část [Konfigurace trvalých přesměrování v produkčním](#configure-permanent-redirects-in-production) prostředí. Doporučujeme používat [HSTS](#http-strict-transport-security-protocol-hsts) k signalizaci klientům, aby se do aplikace poslaly jenom zabezpečené požadavky na prostředky (jenom v produkčním prostředí).

### <a name="port-configuration"></a>Konfigurace portu

Pro middleware musí být k dispozici port pro přesměrování nezabezpečené žádosti na HTTPS. Pokud není k dispozici žádný port:

* Přesměrování na HTTPS neproběhne.
* Middleware zaznamená upozornění. "Nepodařilo se určit port HTTPS pro přesměrování".

Port HTTPS určete pomocí některého z následujících přístupů:

* Nastavte [HttpsRedirectionOptions. HttpsPort](#options).

::: moniker range=">= aspnetcore-3.0"

* Nastavte nastavení `https_port` [hostitele](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):

  * V konfiguraci hostitele.
  * Nastavením proměnné prostředí `ASPNETCORE_HTTPS_PORT`.
  * Přidáním položky nejvyšší úrovně v souboru *appSettings. JSON*:

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* Označení portu pomocí zabezpečeného schématu pomocí [proměnné prostředí ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls). Proměnná prostředí nakonfiguruje server. Middleware nepřímo vyhledá port HTTPS prostřednictvím <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Tento přístup nefunguje v nasazeních reverzních proxy serverů.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* Nastavte nastavení `https_port` [hostitele](xref:fundamentals/host/web-host#https-port):

  * V konfiguraci hostitele.
  * Nastavením proměnné prostředí `ASPNETCORE_HTTPS_PORT`.
  * Přidáním položky nejvyšší úrovně v souboru *appSettings. JSON*:

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* Označení portu pomocí zabezpečeného schématu pomocí [proměnné prostředí ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls). Proměnná prostředí nakonfiguruje server. Middleware nepřímo vyhledá port HTTPS prostřednictvím <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Tento přístup nefunguje v nasazeních reverzních proxy serverů.

::: moniker-end

* Ve vývojovém prostředí nastavte adresu URL HTTPS v souboru *launchsettings. JSON*. Pokud se používá IIS Express, povolte HTTPS.

* Nakonfigurujte koncový bod adresy URL HTTPS pro nasazení Edge serveru [Kestrel](xref:fundamentals/servers/kestrel) Server nebo [http. sys](xref:fundamentals/servers/httpsys) s přístupem k veřejnému. Aplikace používá jenom **jeden port HTTPS** . Middleware zjistí port prostřednictvím <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.

> [!NOTE]
> Když je aplikace spuštěná v konfiguraci reverzního proxy serveru, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> není k dispozici. Port nastavte pomocí některého z dalších přístupů popsaných v této části.

### <a name="edge-deployments"></a>Nasazení Edge 

Pokud se Kestrel nebo HTTP. sys používá jako veřejný server Edge, Kestrel nebo HTTP. sys musí být nakonfigurované tak, aby naslouchal na obou těchto počítačích:

* Zabezpečený port, ve kterém je klient přesměrován (obvykle 443 v produkčním a 5001 ve vývoji).
* Nezabezpečený port (obvykle 80 v produkčním a 5000 ve vývoji).

Aby aplikace přijímala nezabezpečený požadavek a přesměrovala klienta na zabezpečený port, musí být nezabezpečený port přístupný klientovi.

Další informace najdete v tématu [Konfigurace a <xref:fundamentals/servers/httpsys>koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) .

### <a name="deployment-scenarios"></a>Scénáře nasazení

Všechny brány firewall mezi klientem a serverem musí mít také otevřené komunikační porty pro provoz.

Pokud se požadavky předávají v konfiguraci reverzního proxy serveru, před voláním middleware pro přesměrování protokolu HTTPS použijte [middleware pro předané hlavičky](xref:host-and-deploy/proxy-load-balancer) . Middleware předaných hlaviček aktualizuje `Request.Scheme`pomocí hlavičky `X-Forwarded-Proto`. Middleware povoluje správné fungování identifikátorů URI přesměrování a dalších zásad zabezpečení. Když se nepoužije middleware předávaných hlaviček, back-end aplikace nemusí získat správné schéma a končit smyčkou přesměrování. Společná chybová zpráva koncového uživatele je, že došlo k příliš velkému počtu přesměrování.

Při nasazování do Azure App Service postupujte podle pokynů v [kurzu: vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Možnosti

Následující zvýrazněný kód volá [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) ke konfiguraci možností middlewaru:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


Volání `AddHttpsRedirection` je nezbytné pouze ke změně hodnot `HttpsPort` nebo `RedirectStatusCode`.

Předchozí zvýrazněný kód:

* Nastaví [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) na <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, což je výchozí hodnota. Pro přiřazení `RedirectStatusCode`použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy.
* Nastaví port HTTPS na 5001.

#### <a name="configure-permanent-redirects-in-production"></a>Konfigurace trvalých přesměrování v produkčním prostředí

Middleware ve výchozím nastavení odesílá [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) pomocí všech přesměrování. Pokud upřednostňujete odeslání trvalého stavového kódu přesměrování, pokud je aplikace v nevývojovém prostředí, zabalte konfiguraci možností middlewaru do podmíněné kontroly pro nevývojové prostředí.

::: moniker range=">= aspnetcore-3.0"

Při konfiguraci služeb v *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Při konfiguraci služeb v *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a>Alternativní přístup middlewaru přesměrování HTTPS

Alternativou k použití middlewaru pro přesměrování protokolu HTTPS (`UseHttpsRedirection`) je použití middleware pro přepis adres URL (`AddRedirectToHttps`). `AddRedirectToHttps` může také nastavit stavový kód a port při spuštění přesměrování. Další informace najdete v tématu [middleware pro přepis adres URL](xref:fundamentals/url-rewriting).

Při přesměrování na HTTPS bez požadavku na další pravidla přesměrování doporučujeme použít middleware Přesměrující protokol HTTPS (`UseHttpsRedirection`) popsané v tomto tématu.

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a>Protokol HTTP Strict Transport Security (HSTS)

Za [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)je [zabezpečení protokolu HTTP (HSTS Strict Transport Security)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) na základě souhlasu s hlavičkou odpovědi zadané pomocí webové aplikace. Když [prohlížeč, který podporuje HSTS,](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) obdrží tuto hlavičku:

* Prohlížeč ukládá konfiguraci pro doménu, která znemožňuje odeslání jakékoli komunikace prostřednictvím protokolu HTTP. Prohlížeč vynutí veškerou komunikaci přes protokol HTTPS.
* Prohlížeč brání uživateli v používání nedůvěryhodných nebo neplatných certifikátů. Prohlížeč zakáže výzvy, které umožní uživateli dočasně důvěřovat takovému certifikátu.

Vzhledem k tomu, že klient vynutil HSTS, má některá omezení:

* Klient musí podporovat HSTS.
* HSTS vyžaduje alespoň jednu úspěšnou žádost HTTPS k vytvoření zásady HSTS.
* Aplikace musí kontrolovat všechny požadavky HTTP a přesměrovat nebo zamítnout požadavek HTTP.

ASP.NET Core 2,1 a novější implementuje HSTS s metodou rozšíření `UseHsts`. Následující kód volá `UseHsts`, když aplikace není v [režimu pro vývoj](xref:fundamentals/environments):

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

`UseHsts` se při vývoji nedoporučuje, protože nastavení HSTS jsou prohlížeči vysoce ukládat do mezipaměti. Ve výchozím nastavení `UseHsts` nezahrnuje místní adresu zpětné smyčky.

V produkčních prostředích, která implementují protokol HTTPS poprvé, nastavte počáteční [HstsOptions. maxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) na malou hodnotu pomocí jedné z metod <xref:System.TimeSpan>. Nastavte hodnotu z hodin na ne více než jeden den pro případ, že budete potřebovat obnovit infrastrukturu HTTPS na HTTP. Až si budete jisti udržitelností konfigurace HTTPS, zvyšte hodnotu maximálního stáří HSTS; běžně používaná hodnota je jeden rok.

Následující kód:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* Nastaví parametr přednačtení záhlaví Strict-Transport-Security. Předběžné načtení není součástí [specifikace RFC HSTS](https://tools.ietf.org/html/rfc6797), ale podporuje je ve webových prohlížečích k přednačtení webů HSTS při nové instalaci. Další informace najdete tady: [https://hstspreload.org/](https://hstspreload.org/).
* Povolí [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), která aplikuje zásady HSTS na hostování subdomén.
* Explicitně nastaví parametr max-age záhlaví Strict-Transport-Security na 60 dní. Pokud není nastavené, výchozí hodnota je 30 dní. Další informace najdete v [direktivě pro maximální stáří](https://tools.ietf.org/html/rfc6797#section-6.1.1) .
* Přidá `example.com` do seznamu hostitelů, které mají být vyloučeny.

`UseHsts` vyloučí následující hostitele zpětné smyčky:

* `localhost`: adresa zpětné smyčky IPv4.
* `127.0.0.1`: adresa zpětné smyčky IPv4.
* `[::1]`: adresa zpětné smyčky protokolu IPv6.

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Výslovný nesouhlas s protokolem HTTPS/HSTS při vytváření projektu

V některých případech služby back-end, kde se zabezpečení připojení zpracovává na veřejném hraničním serveru sítě, se konfigurace zabezpečení připojení na jednotlivých uzlech nevyžaduje. Webové aplikace, které jsou generovány ze šablon v aplikaci Visual Studio nebo pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) , umožňují [přesměrování https](#require-https) a [HSTS](#http-strict-transport-security-protocol-hsts). Pro nasazení, která tyto scénáře nevyžadují, můžete při vytvoření aplikace ze šablony vyjádřit výslovný souhlas s HTTPS/HSTS.

Výslovný souhlas s protokolem HTTPS/HSTS:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Zrušte zaškrtnuté políčko **Konfigurovat pro protokol HTTPS** .

::: moniker range=">= aspnetcore-3.0"

![Dialogové okno Nová ASP.NET Core webové aplikace zobrazující nezaškrtnuté políčko konfigurovat pro protokol HTTPS.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Dialogové okno Nová ASP.NET Core webové aplikace zobrazující nezaškrtnuté políčko konfigurovat pro protokol HTTPS.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli) 

Použijte možnost `--no-https`. Příklad

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Důvěra ASP.NET Core certifikát pro vývoj HTTPS ve Windows a macOS

.NET Core SDK obsahuje certifikát pro vývoj HTTPS. Certifikát je nainstalován jako součást prvního spuštění prostředí. Například `dotnet --info` vytváří výstup podobný následujícímu:

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

Instalace .NET Core SDK nainstaluje certifikát pro vývoj ASP.NET Core HTTPS do úložiště certifikátů místního uživatele. Certifikát je nainstalovaný, ale není důvěryhodný. Pro důvěřování certifikátu proveďte jednorázový krok ke spuštění nástroje dotnet `dev-certs` Tool:

```dotnetcli
dotnet dev-certs https --trust
```

Následující příkaz poskytuje nápovědě k nástroji `dev-certs`:

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Jak nastavit certifikát pro vývojáře pro Docker

Podívejte se na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/6199).

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Důvěřovat certifikátu HTTPS v subsystému Windows pro Linux

Subsystém Windows pro Linux (WSL) vygeneruje certifikát podepsaný svým držitelem (HTTPS). Konfigurace úložiště certifikátů Windows pro důvěřování certifikátu WSL:

* Spuštěním následujícího příkazu exportujte certifikát vygenerovaný WSL: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`
* V okně WSL spusťte následující příkaz: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`

  Předchozí příkaz nastaví proměnné prostředí tak, aby Linux používal důvěryhodný certifikát Windows.

## <a name="troubleshoot-certificate-problems"></a>Řešení problémů s certifikáty

Tato část obsahuje informace o tom, kdy byl certifikát pro vývoj ASP.NET Core HTTPS [nainstalovaný a důvěryhodný](#trust), ale přesto máte upozornění prohlížeče, že certifikát není důvěryhodný. ASP.NET Core certifikát pro vývoj HTTPS používá [Kestrel](xref:fundamentals/servers/kestrel).

### <a name="all-platforms---certificate-not-trusted"></a>Všechny platformy – certifikát není důvěryhodný.

Spusťte následující příkazy:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Zavřete všechny otevřené instance prohlížeče. Otevřete nové okno prohlížeče pro aplikaci. Důvěryhodnost certifikátu je ukládána v mezipaměti prohlížeči.

Předchozí příkazy vyřeší většinu problémů s důvěryhodností prohlížečů. Pokud prohlížeč stále certifikát nepovažuje za důvěryhodný, postupujte podle následujících doporučení pro konkrétní platformu.

### <a name="docker---certificate-not-trusted"></a>Docker – certifikát není důvěryhodný.

* Odstraňte složku *C:\Users\{uživateli} \AppData\Roaming\ASP.NET\Https* .
* Vyčistěte řešení. Odstranit *bin* a *obj* složek.
* Restartujte nástroj pro vývoj. Například Visual Studio, Visual Studio Code nebo Visual Studio pro Mac.

### <a name="windows---certificate-not-trusted"></a>Windows – certifikát není důvěryhodný.

* Ověřte certifikáty v úložišti certifikátů. V části `Current User > Personal > Certificates` a `Current User > Trusted root certification authorities > Certificates` by měl být `localhost` certifikát s `ASP.NET Core HTTPS development certificate` popisným názvem.
* Odeberte všechny nalezené certifikáty z osobních i důvěryhodných kořenových certifikačních autorit. **Neodstraňujte** certifikát IIS Express localhost.
* Spusťte následující příkazy:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Zavřete všechny otevřené instance prohlížeče. Otevřete nové okno prohlížeče pro aplikaci.

### <a name="os-x---certificate-not-trusted"></a>OS X – certifikát není důvěryhodný.

* Otevřete přístup k řetězci klíčů.
* Vyberte systémový řetězec klíčů.
* Ověřte přítomnost certifikátu localhost.
* Ověřte, že obsahuje symbol `+` na ikoně pro označení, že je důvěryhodný pro všechny uživatele.
* Odeberte certifikát ze systémového řetězce klíčů.
* Spusťte následující příkazy:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Zavřete všechny otevřené instance prohlížeče. Otevřete nové okno prohlížeče pro aplikaci.

Informace o řešení problémů s certifikáty pomocí sady Visual Studio najdete v tématu [Chyba protokolu HTTPS pomocí IIS Express (ASPNET/AspNetCore #16892)](https://github.com/aspnet/AspNetCore/issues/16892) .

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a>IIS Express certifikát SSL používaný se sadou Visual Studio

Pokud chcete opravit problémy s certifikátem IIS Express, vyberte v instalačním programu sady Visual Studio možnost **opravit** .

## <a name="additional-information"></a>Další informace

* <xref:host-and-deploy/proxy-load-balancer>
* [Hostování ASP.NET Core v systému Linux pomocí Apache: Konfigurace HTTPS](xref:host-and-deploy/linux-apache#https-configuration)
* [Hostitelská ASP.NET Core v systému Linux s Nginx: Konfigurace HTTPS](xref:host-and-deploy/linux-nginx#https-configuration)
* [Jak nastavit SSL v IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [Podpora prohlížeče OWASP HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
