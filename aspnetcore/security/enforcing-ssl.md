---
title: Vynucení protokolu HTTPS v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak chcete vyžadovat protokol HTTPS/TLS ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: 8d48877153d6d75348e29299c669125904236de8
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692587"
---
# <a name="enforce-https-in-aspnet-core"></a>Vynucení protokolu HTTPS v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento dokument ukazuje, jak:

* Vyžadovat protokol HTTPS pro všechny požadavky.
* Přesměrujte všechny požadavky HTTP na HTTPS.

Žádné rozhraní API můžete zabránit posláním citlivých dat na první požadavek klienta.

> [!WARNING]
> Proveďte **není** použít [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) na webová rozhraní API, která zobrazit citlivé informace. `RequireHttpsAttribute` stavové kódy HTTP se používá pro přesměrování prohlížeče z HTTP na HTTPS. Klienti rozhraní API nemusí pochopit a dodržují přesměrování z HTTP na HTTPS. Tito klienti mohou odesílat informace přes protokol HTTP. Webová rozhraní API by buď:
>
> * Nelze naslouchat na HTTP.
> * Ukončete připojení s stavový kód 400 (Chybný požadavek) a není obsluhovat žádosti.

## <a name="require-https"></a>Vyžadovat protokol HTTPS

::: moniker range=">= aspnetcore-2.1"

Doporučujeme tuto produkční ASP.NET Core volání webové aplikace:

* Middleware přesměrování protokolu HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) pro přesměrování požadavků protokolu HTTP na HTTPS.
* HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) k odeslání hlavičky protokolu HTTP striktní přenosu zabezpečení protokolu (HSTS) pro klienty.

> [!NOTE]
> Aplikace nasazené v konfigurace reverzního proxy serveru povolit proxy pro zpracování zabezpečení připojení (HTTPS). Pokud proxy server zpracovává také přesměrování protokolu HTTPS, není nutné používat Middleware přesměrování protokolu HTTPS. Pokud proxy server také zpracovává zápis HSTS hlavičky (například [HSTS nativní podporu v IIS 10.0 (1709) nebo novější](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware není potřebné aplikace. Další informace najdete v tématu [výslovného nesouhlasu z HTTPS/HSTS při vytvoření projektu](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>UseHttpsRedirection

Následující kód volá `UseHttpsRedirection` v `Startup` třídy:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

Předchozí zvýrazněný kód:

* Používá výchozí [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* Používá výchozí [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), pokud není přepsán `ASPNETCORE_HTTPS_PORT` proměnné prostředí nebo [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).

Doporučujeme používat dočasné přesměrování spíše než trvalé přesměrování. Ukládání do mezipaměti odkazu může způsobit nestabilní chování ve vývojovém prostředí. Pokud chcete poslat kód stavu trvalé přesměrování, když se aplikace nachází mimo vývojové prostředí, najdete v článku [Konfigurace trvalé přesměrování v produkčním prostředí](#configure-permanent-redirects-in-production) oddílu. Doporučujeme používat [HSTS](#http-strict-transport-security-protocol-hsts) na signál pro klienty, kteří pouze zabezpečit prostředek požadavků odesílaných do aplikace (jenom v produkčním prostředí).

### <a name="port-configuration"></a>Konfigurace portu

Port musí být k dispozici pro daný middleware pro přesměrování nezabezpečený požadavek na protokol HTTPS. Pokud je k dispozici žádný port:

* Nedojde k přesměrování na protokol HTTPS.
* Middleware protokoluje upozornění "Nepovedlo se určit port https pro přesměrování."

Zadejte port HTTPS pomocí kteréhokoli z následujících postupů:

* Nastavte [HttpsRedirectionOptions.HttpsPort](#options).
* Nastavte `ASPNETCORE_HTTPS_PORT` proměnné prostředí nebo [nastavení konfigurace webového hostitele https_port](xref:fundamentals/host/web-host#https-port):

  **Klíč**: `https_port`  
  **Typ**: *řetězec*  
  **Výchozí**: Výchozí hodnota není nastavená.  
  **Sada s použitím**: `UseSetting`  
  **Proměnná prostředí**: `<PREFIX_>HTTPS_PORT` (Předpona je `ASPNETCORE_` při použití [webového hostitele](xref:fundamentals/host/web-host).)

  Při konfiguraci <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> v `Program`:

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* Označení portu pomocí zabezpečené schéma `ASPNETCORE_URLS` proměnné prostředí. Proměnná prostředí nakonfiguruje server. Middleware nepřímo zjistí port HTTPS přes <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Tento postup nefunguje v nasazeních reverzního proxy serveru.
* Při vývoji, nastavte adresu URL HTTPS *launchsettings.json*. Povolení HTTPS, když se používá služba IIS Express.
* Konfigurace koncového bodu adresy URL HTTPS nasazení veřejnou hraniční [Kestrel](xref:fundamentals/servers/kestrel) serveru nebo [HTTP.sys](xref:fundamentals/servers/httpsys) serveru. Pouze **jeden port HTTPS** používají aplikaci. Middleware zjistí port přes <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.

> [!NOTE]
> Při spuštění aplikace v konfiguraci reverzní proxy server, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> není k dispozici. Nastavte port pomocí jednoho z jiných postupů popsaných v této části.

Při Kestrel nebo ovladač HTTP.sys se používá jako veřejnou hraniční server, musí být Kestrel nebo ovladač HTTP.sys nakonfigurovaná k naslouchání na obojí:

* Zabezpečený port, ve kterém se klient přesměruje (obvykle 443 v produkčním prostředí a 5001 ve vývoji).
* Nezabezpečené port (standardně 80 v produkčním prostředí) a 5000 ve vývoji.

Nezabezpečené port musí být přístupné pro klienta v pořadí pro aplikaci pro příjem nezabezpečený požadavek a přesměrování klienta na zabezpečený port.

Další informace najdete v tématu [konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) nebo <xref:fundamentals/servers/httpsys>.

### <a name="deployment-scenarios"></a>Scénáře nasazení

Všechny brány firewall mezi klientem a serverem musí mít také komunikační porty otevřete pro provoz.

Pokud žádosti se předávají v konfiguraci reverzní proxy server, použijte [předané Middleware záhlaví](xref:host-and-deploy/proxy-load-balancer) před voláním Middleware přesměrování protokolu HTTPS. Předané Middleware záhlaví aktualizací `Request.Scheme`, použije `X-Forwarded-Proto` záhlaví. Povolení middleware přesměrování identifikátory URI a další zásady zabezpečení fungovat správně. Kdy předané Middleware záhlaví nepoužívá, back-endu aplikace nemusí dostávat správné schéma a ukládaly ve smyčce přesměrování. Běžné chybová zpráva koncovým uživatelem se, že došlo k příliš mnoha přesměrování.

Při nasazování do služby Azure App Service, postupujte podle pokynů v [kurzu: Vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Možnosti

Následující zvýrazněný kód volá [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) konfigurace middlewaru možností:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

Volání `AddHttpsRedirection` jenom je potřeba změnit hodnoty `HttpsPort` nebo `RedirectStatusCode`.

Předchozí zvýrazněný kód:

* Nastaví [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) k <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, což je výchozí hodnota. Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídu pro přiřazení k `RedirectStatusCode`.
* Nastaví HTTPS port 5001. Výchozí hodnota je 443.

#### <a name="configure-permanent-redirects-in-production"></a>Konfigurace trvalé přesměrování v produkčním prostředí

Middleware odeslání výchozí hodnota je [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) s všechny přesměrování. Pokud chcete poslat kód stavu trvalé přesměrování, když se aplikace nachází mimo vývojové prostředí, zabalte možnosti konfigurace middlewaru v podmíněnou kontrolu mimo vývojové prostředí.

Při konfiguraci `IWebHostBuilder` v *Startup.cs*:

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

## <a name="https-redirection-middleware-alternative-approach"></a>Alternativním přístupem Middleware přesměrování protokolu HTTPS

Alternativou k používání HTTPS přesměrování Middleware (`UseHttpsRedirection`), je použít Middleware pro přepis adres URL (`AddRedirectToHttps`). `AddRedirectToHttps` Můžete také nastavit stavový kód a portu při spuštění přesměrování. Další informace najdete v tématu [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting).

Při přesměrování na HTTPS bez nutnosti další přesměrování pravidla, doporučujeme používat Middleware přesměrování protokolu HTTPS (`UseHttpsRedirection`) popsané v tomto tématu.

::: moniker-end

::: moniker range="< aspnetcore-2.1"

[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) se používá tak, aby vyžadovala protokol HTTPS. `[RequireHttpsAttribute]` můžete uspořádání, řadiče nebo metod, nebo je možné uplatňovat globálně. Použít atribut globálně, přidejte následující kód, který `ConfigureServices` v `Startup`:

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

Předchozí zvýrazněný kód technologie používat všechny požadavky `HTTPS`, proto požadavky HTTP jsou ignorovány. Následující zvýrazněný kód přesměruje všechny požadavky HTTP na HTTPS:

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

Další informace najdete v tématu [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting). Middleware také umožňuje, že aplikace nastaví stavový kód nebo kód stavu a port, který při spuštění přesměrování.

Globálně vyžadování protokolu HTTPS (`options.Filters.Add(new RequireHttpsAttribute());`) je z bezpečnostních důvodů. Použití `[RequireHttps]` atribut na všechny řadiče/Razor Pages není považován za stejně bezpečné jako globálně vyžadování protokolu HTTPS. Nebudete moct zaručit, `[RequireHttps]` atributu se použije při přidání nových řadičů a stránky Razor.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="http-strict-transport-security-protocol-hsts"></a>Protokol zabezpečení striktní přenos HTTP (HSTS)

Za [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [striktní přenos HTTP zabezpečení (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) je vylepšení zabezpečení přihlášení, která je zadána pomocí hlavičky odpovědi webové aplikace. Když [prohlížeč, který podporuje HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) obdrží toto záhlaví:

* Prohlížeč ukládá konfiguraci pro doménu, která zabrání odeslání libovolné komunikaci přes protokol HTTP. Prohlížeč vynutí veškerou komunikaci přes protokol HTTPS.
* Prohlížeč znemožní uživateli pomocí certifikátů nedůvěryhodný nebo neplatný. Prohlížeč zakáže výzev, které umožní uživateli tohoto certifikátu dočasně důvěřovat.

Protože klient se vynucuje HSTS má určitá omezení:

* Klient musí podporovat HSTS.
* HSTS vyžaduje alespoň jeden úspěšného požadavku HTTPS k navázání HSTS zásad.
* Aplikace musí zkontrolovat každého požadavku HTTP a přesměrování nebo zamítnutí žádosti protokolu HTTP.

Implementuje HSTS s ASP.NET Core 2.1 nebo vyšší `UseHsts` – metoda rozšíření. Následující kód volá `UseHsts` když aplikace není v [vývojový režim](xref:fundamentals/environments):

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

`UseHsts` není doporučeno při vývoji vzhledem k tomu, že jsou nastavení HSTS dokonalé prohlížeče. Ve výchozím nastavení `UseHsts` nezahrnuje adresu místní zpětné smyčky.

Pro produkční prostředí implementace HTTPS poprvé, nastavte počáteční [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) malou hodnotu pomocí jedné z <xref:System.TimeSpan> metody. Nastavte hodnotu hodiny na no více než jeden den v případě, že budete potřebovat obnovit infrastruktury HTTPS do HTTP. Poté, co jste si jisti udržitelnost konfigurace protokolu HTTPS, zvýšit hodnotu max-age HSTS. běžně používané hodnota je 1 rok.

Následující kód:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* Nastaví parametr přednačtení záhlaví zabezpečení přenosu Strict. Předinstalační není součástí [specifikaci RFC HSTS](https://tools.ietf.org/html/rfc6797), ale podporuje webových prohlížečů přednačtení HSTS weby na čerstvou instalaci. Zobrazit [ https://hstspreload.org/ ](https://hstspreload.org/) Další informace.
* Umožňuje [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), které se vztahují zásady HSTS na hostiteli subdomény.
* Explicitně nastaví parametr max-age záhlaví zabezpečení přenosu Strict na 60 dnů. Pokud není nastavena výchozí hodnota je 30 dní. Najdete v článku [max-age směrnice](https://tools.ietf.org/html/rfc6797#section-6.1.1) Další informace.
* Přidá `example.com` do seznamu hostitelů mají vyloučit.

`UseHsts` vyloučí následující zpětné smyčky hostitele:

* `localhost` : IPv4 adresu zpětné smyčky.
* `127.0.0.1` : IPv4 adresu zpětné smyčky.
* `[::1]` : Adresu zpětné smyčky protokolu IPv6.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Odhlásit HTTPS/HSTS při vytvoření projektu

V některých scénářích služby back-endu kde zabezpečení připojení probíhá v veřejnou hraniční části sítě není vyžadována konfigurace zabezpečení připojení v každém uzlu. Webová aplikace vygenerované ze šablony v sadě Visual Studio nebo z [dotnet nové](/dotnet/core/tools/dotnet-new) povolit příkaz [HTTPS přesměrování](#require-https) a [HSTS](#http-strict-transport-security-protocol-hsts). Pro nasazení, které nevyžadují tyto scénáře které můžete výslovný nesouhlas s HTTPS/HSTS při vytvoření aplikace ze šablony.

Chcete odhlásit HTTPS/HSTS:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Zrušte zaškrtnutí políčka **konfigurace pro protokol HTTPS** zaškrtávací políčko.

![Nové webové aplikace ASP.NET Core dialogové okno zobrazující konfigurací pro HTTPS zaškrtávací políčko nevybranou.](enforcing-ssl/_static/out.png)

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli) 

Použití `--no-https` možnost. Příklad

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Důvěřovat certifikátu vývoj pro ASP.NET Core HTTPS ve Windows a macOS

Sada .NET core SDK zahrnuje vývoj nesprávný certifikát HTTPS. Certifikát je nainstalován jako součást prvního spuštění. Například `dotnet --info` vytváří výstup podobný následujícímu:

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

Instalace sady .NET Core SDK nainstaluje certifikát pro vývoj ASP.NET Core HTTPS do úložiště certifikátů místního uživatele. Certifikát je nainstalovaný, ale není důvěryhodný. Důvěřovat certifikátu provést jednorázové krok a spusťte dotnet `dev-certs` nástroje:

```console
dotnet dev-certs https --trust
```

Následující příkaz nápovědy týkající `dev-certs` nástroje:

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Jak nastavit certifikát pro vývojáře pro Docker

Zobrazit [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/6199).

::: moniker-end

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Důvěřovat certifikátu protokolu HTTPS ze subsystému Windows pro Linux

Subsystém Windows pro Linux (WSL) generuje HTTPS certifikátu podepsaného svým držitelem. Postup konfigurace úložiště certifikátů Windows důvěřovat certifikátům WSL:

* Spuštěním následujícího příkazu exportujte certifikát WSL vygeneruje: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`
* V okně WSL spusťte následující příkaz: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`

  Ve výstupu předchozího příkazu nastaví proměnné prostředí, takže tento důvěryhodný certifikát Windows používá Linux.

## <a name="additional-information"></a>Další informace

* <xref:host-and-deploy/proxy-load-balancer>
* [Hostitele ASP.NET Core v Linuxu pomocí Apache: Konfigurace protokolu HTTPS](xref:host-and-deploy/linux-apache#https-configuration)
* [Hostitele ASP.NET Core v Linuxu se serverem Nginx: Konfigurace protokolu HTTPS](xref:host-and-deploy/linux-nginx#https-configuration)
* [Nastavení protokolu SSL ve službě IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [Podpora prohlížeče OWASP HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
