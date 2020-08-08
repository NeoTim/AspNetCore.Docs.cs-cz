---
title: Konfigurace ověřování certifikátů v ASP.NET Core
author: blowdart
description: Přečtěte si, jak nakonfigurovat ověřování certifikátů v ASP.NET Core pro IIS a HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/certauth
ms.openlocfilehash: 7a23f2b17cc8fb3a4989b9fddd5c128add13db5b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021949"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Konfigurace ověřování certifikátů v ASP.NET Core

`Microsoft.AspNetCore.Authentication.Certificate`obsahuje implementaci podobnou [ověřování certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) pro ASP.NET Core. Ověřování certifikátu se provádí na úrovni protokolu TLS dlouho předtím, než se někdy získá ASP.NET Core. Přesněji platí, že se jedná o obslužnou rutinu ověřování, která certifikát ověřuje, a pak poskytuje událost, kde můžete tento certifikát vyřešit na `ClaimsPrincipal` . 

[Nakonfigurujte server](#configure-your-server-to-require-certificates) pro ověřování pomocí certifikátu, jako IIS, Kestrel, Azure Web Apps nebo cokoli jiného, co používáte.

## <a name="proxy-and-load-balancer-scenarios"></a>Scénáře proxy a nástroje pro vyrovnávání zatížení

Ověřování certifikátů je stavový scénář, který se primárně používá, když proxy nebo nástroj pro vyrovnávání zatížení nezpracovává provoz mezi klienty a servery. Pokud se používá proxy server nebo nástroj pro vyrovnávání zatížení, funguje ověřování certifikátů pouze v případě, že proxy nebo nástroj pro vyrovnávání zatížení:

* Zpracovává ověřování.
* Předá do aplikace informace o ověřování uživatele (například v hlavičce požadavku), která funguje na ověřovacích informacích.

Alternativou k ověřování certifikátů v prostředích, kde se používají proxy a nástroje pro vyrovnávání zatížení, je služba AD FS (Active Directory federovaných služeb) se službou OpenID Connect (OIDC).

## <a name="get-started"></a>Začínáme

Získejte certifikát HTTPS, použijte ho a [nakonfigurujte server](#configure-your-server-to-require-certificates) tak, aby vyžadoval certifikáty.

Do webové aplikace přidejte odkaz na balíček [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) . Potom v `Startup.ConfigureServices` metodě zavolejte `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` s vašimi možnostmi a poskytněte delegátovi, `OnCertificateValidated` aby provedl dodatečné ověřování klientského certifikátu odeslaného pomocí požadavků. Zapněte tyto informace do `ClaimsPrincipal` a nastavte ji na `context.Principal` vlastnost.

Pokud se ověření nepovede, vrátí tato obslužná rutina `403 (Forbidden)` odpověď místo `401 (Unauthorized)` toho, jak byste mohli očekávat. Důvodem je, že při počátečním připojení TLS by mělo probíhat ověřování. V době, kdy dosáhne obslužné rutiny, je příliš pozdě. Neexistuje žádný způsob, jak upgradovat připojení z anonymního připojení k jednomu pomocí certifikátu.

Přidejte také `app.UseAuthentication();` do `Startup.Configure` metody. V opačném případě nebude `HttpContext.User` nastavení `ClaimsPrincipal` vytvořeno z certifikátu. Například:

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

Předchozí příklad ukazuje výchozí způsob, jak přidat ověřování certifikátu. Obslužná rutina vytvoří objekt zabezpečení uživatele pomocí vlastností Common Certificate.

## <a name="configure-certificate-validation"></a>Konfigurace ověření certifikátu

`CertificateAuthenticationOptions`Obslužná rutina obsahuje některá Vestavěná ověření, která jsou minimálními ověřeními, které byste měli provést na certifikátu. Každé z těchto nastavení je ve výchozím nastavení povoleno.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = Chained, SelfSigned nebo All (zřetězené | SelfSigned)

Výchozí hodnota:`CertificateTypes.Chained`

Tato kontrola ověří, zda je povolen pouze příslušný typ certifikátu. Pokud aplikace používá certifikáty podepsané svým držitelem, musí být tato možnost nastavená na `CertificateTypes.All` nebo `CertificateTypes.SelfSigned` .

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Výchozí hodnota:`true`

Tato kontrola ověří, že certifikát prezentovaný klientem má rozšířené použití klíče (EKU) ověřování klienta (EKU) nebo žádný rozšířená použití klíče. Pokud se jako specifikace nezadá žádné rozšířené použití klíče, považují se všechny rozšířená použití klíče za platné.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Výchozí hodnota:`true`

Tato kontrola ověří, že se certifikát nachází v období platnosti. U každé žádosti obslužná rutina zajišťuje, že platnost certifikátu, který byl platný při jeho předložení, vypršela během aktuální relace.

### <a name="revocationflag"></a>RevocationFlag

Výchozí hodnota:`X509RevocationFlag.ExcludeRoot`

Příznak, který určuje, které certifikáty v řetězci mají být zkontrolovány pro odvolání.

Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.

### <a name="revocationmode"></a>RevocationMode

Výchozí hodnota:`X509RevocationMode.Online`

Příznak, který určuje, jak se provádí kontroly odvolání.

Zadání online kontroly může mít za následek dlouhou prodlevu při kontaktování certifikační autority.

Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Můžu aplikaci nakonfigurovat tak, aby vyžadovala certifikát jenom na určitých cestách?

To není možné. Zapamatujte si, že výměna certifikátu se dokončila, když se spustí konverzace HTTPS, a to na serveru před přijetím prvního požadavku v tomto připojení, takže není možné ho oborovat na základě jakýchkoli polí požadavků.

## <a name="handler-events"></a>Události obslužných rutin

Obslužná rutina má dvě události:

* `OnAuthenticationFailed`: Volá se, pokud dojde k výjimce během ověřování a umožňuje reagovat.
* `OnCertificateValidated`: Voláno po ověření certifikátu, úspěšné ověření a výchozí objekt zabezpečení byl vytvořen. Tato událost umožňuje provádět vlastní ověřování a rozšíření nebo nahrazení objektu zabezpečení. Příklady zahrnují:
  * Určení, jestli se pro vaše služby ví certifikát.
  * Sestavování vlastního objektu zabezpečení. Vezměte v úvahu následující příklad `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

Pokud zjistíte, že příchozí certifikát nesplňuje vaše dodatečné ověření, zavolejte `context.Fail("failure reason")` důvod selhání.

Pro reálné funkce pravděpodobně budete chtít volat službu registrovanou v injektáže závislosti, který se připojuje k databázi nebo jinému typu úložiště uživatele. Ke službě získáte přístup pomocí kontextu předaného do vašeho delegáta. Vezměte v úvahu následující příklad `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

V koncepčním případě je ověření certifikátu v takovém případě oprávnění. Přidání kontroly, například vystavitele nebo kryptografického otisku v zásadách autorizace, nikoli uvnitř `OnCertificateValidated` , je naprosto přijatelné.

## <a name="configure-your-server-to-require-certificates"></a>Konfigurace serveru tak, aby vyžadoval certifikáty

### <a name="kestrel"></a>Kestrel

V *program.cs*nakonfigurujte Kestrel následujícím způsobem:

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.

### <a name="iis"></a>IIS

Ve Správci služby IIS proveďte následující kroky:

1. Na kartě **připojení** vyberte svou lokalitu.
1. Dvakrát klikněte na možnost **Nastavení SSL** v okně **zobrazení funkcí** .
1. Zaškrtněte políčko **vyžadovat protokol SSL** a v části **certifikáty klienta** vyberte tlačítko **vyžadovat** přepínač.

![Nastavení klientského certifikátu ve službě IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure a vlastní webové proxy servery

Postup konfigurace middlewaru pro předávání certifikátů najdete v [dokumentaci k hostiteli a nasazení](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Použití ověřování certifikátů v Azure Web Apps

Pro Azure se nevyžaduje žádná konfigurace předávání. Toto je již nastaveno v middlewari předávání certifikátů.

> [!NOTE]
> To vyžaduje, aby byl přítomen CertificateForwardingMiddleware.

### <a name="use-certificate-authentication-in-custom-web-proxies"></a>Použití ověřování certifikátů ve vlastních webových proxy serverech

`AddCertificateForwarding`Metoda slouží k zadání:

* Název hlavičky klienta.
* Způsob načtení certifikátu (pomocí `HeaderConverter` Vlastnosti).

Ve vlastních webových proxy serverech se certifikát předává jako vlastní Hlavička požadavku, například `X-SSL-CERT` . Pokud ho chcete použít, nakonfigurujte předávání certifikátů v nástroji `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

`Startup.Configure`Metoda pak přidá middleware. `UseCertificateForwarding`je volána před voláním `UseAuthentication` a `UseAuthorization` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Samostatnou třídu lze použít k implementaci logiky ověřování. Protože v tomto příkladu se používá stejný certifikát podepsaný svým držitelem, ujistěte se, že se dá použít jenom váš certifikát. Ověřte, že se neshodují kryptografické otisky certifikátu klienta i certifikátu serveru. v opačném případě se dá použít libovolný certifikát, který bude pro ověření stačit. Tato metoda by se použila uvnitř `AddCertificate` metody. V případě použití zprostředkujících nebo podřízených certifikátů můžete také ověřit předmět nebo vystavitele.

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a>Implementace HttpClient pomocí certifikátu a HttpClientHandler

`HttpClientHandler`Lze přidat přímo v konstruktoru `HttpClient` třídy. Při vytváření instancí nástroje byste měli věnovat pozornost `HttpClient` . `HttpClient`Pak certifikát odešle spolu s každou žádostí.

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a>Implementace HttpClient pomocí certifikátu a pojmenovaného HttpClient z IHttpClientFactory 

V následujícím příkladu je klientský certifikát přidán do a `HttpClientHandler` pomocí `ClientCertificates` vlastnosti z obslužné rutiny. Tato obslužná rutina se pak dá použít v pojmenované instanci `HttpClient` pomocí `ConfigurePrimaryHttpMessageHandler` metody. Toto nastavení se provádí v nástroji `Startup.ConfigureServices` :

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

`IHttpClientFactory`Lze pak použít k získání pojmenované instance s obslužnou rutinou a certifikátem. `CreateClient`Metoda s názvem klienta definovaného ve `Startup` třídě slouží k získání instance. Požadavek HTTP lze odeslat pomocí klienta podle potřeby.

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

Pokud se do serveru pošle správný certifikát, vrátí se data. Pokud se nepošle žádný certifikát nebo nesprávný certifikát, vrátí se stavový kód HTTP 403.

### <a name="create-certificates-in-powershell"></a>Vytvoření certifikátů v PowerShellu

Vytvoření certifikátů je nejzávažnou součástí nastavení tohoto toku. Kořenový certifikát se dá vytvořit pomocí `New-SelfSignedCertificate` rutiny PowerShellu. Při vytváření certifikátu použijte silné heslo. Je důležité přidat `KeyUsageProperty` parametr a `KeyUsage` parametr, jak je znázorněno.

#### <a name="create-root-ca"></a>Vytvořit kořenovou certifikační autoritu

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> `-DnsName`Hodnota parametru musí odpovídat cíli nasazení aplikace. Například "localhost" pro vývoj.

#### <a name="install-in-the-trusted-root"></a>Nainstalovat do důvěryhodného kořenového adresáře

Kořenový certifikát musí být v hostitelském systému důvěryhodný. Kořenový certifikát, který nebyl vytvořen certifikační autoritou, nebude ve výchozím nastavení považován za důvěryhodný. Následující odkaz vysvětluje, jak to lze provést ve Windows:

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Zprostředkující certifikát

Zprostředkující certifikát se teď dá vytvořit z kořenového certifikátu. To není vyžadováno pro všechny případy použití, ale možná budete muset vytvořit mnoho certifikátů nebo musíte aktivovat nebo zakázat skupiny certifikátů. `TextExtension`Parametr je vyžadován pro nastavení délky cesty v základních omezeních certifikátu.

Zprostředkující certifikát je pak možné přidat do důvěryhodného zprostředkujícího certifikátu v hostitelském systému Windows.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>Vytvořit podřízený certifikát z zprostředkujícího certifikátu

Z zprostředkujícího certifikátu se dá vytvořit podřízený certifikát. Toto je koncová entita a není nutné vytvářet další podřízené certifikáty.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>Vytvořit podřízený certifikát z kořenového certifikátu

Podřízený certifikát lze také vytvořit přímo z kořenového certifikátu. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>Příklad certifikátu root-zprostředkující certifikát – certifikát

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

Při použití kořenových, zprostředkujících nebo podřízených certifikátů je možné certifikáty ověřit pomocí kryptografického otisku nebo PublicKey podle potřeby.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a>Mezipaměť ověření certifikátu

ASP.NET Core 5,0 a novější verze podporují možnost Povolit ukládání výsledků ověření do mezipaměti. Ukládání do mezipaměti výrazně zvyšuje výkon ověřování certifikátem, protože ověřování je náročná operace.

Ve výchozím nastavení ověřování pomocí certifikátu zakáže ukládání do mezipaměti. Pokud chcete povolit ukládání do mezipaměti, zavolejte `AddCertificateCache` na `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

Výchozí implementace ukládání do mezipaměti ukládá výsledky do paměti. Můžete poskytnout vlastní mezipaměť implementací `ICertificateValidationCache` a registrací pomocí injektáže závislosti. Například, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.

::: moniker-end

## <a name="optional-client-certificates"></a>Volitelné klientské certifikáty

V této části najdete informace o aplikacích, které musí chránit podmnožinu aplikace pomocí certifikátu. Například Razor Stránka nebo kontrolér v aplikaci může vyžadovat klientské certifikáty. V takovém případě se zobrazí výzvy jako klientské certifikáty:
  
* Funkce TLS, nikoli funkce HTTP.
* Jsou vyjednány po připojení a musí se vyjednávat na začátku připojení, než budou k dispozici všechna data HTTP. Na začátku připojení je známo pouze Indikace názvu serveru (SNI) &dagger; . Certifikáty klienta a serveru se vyjednávají před prvním požadavkem na připojení a požadavky obecně není možné znovu vyjednávat.

Nové vyjednávání protokolu TLS bylo staré způsob implementace volitelných klientských certifikátů. Už to nedoporučujeme, protože:
- V HTTP/1.1 by nové vyjednávání během žádosti POST mohlo způsobit zablokování, kde text žádosti vyplnil okno TCP a pakety opětovného vyjednávání nejde přijmout.
- HTTP/2 [explicitně zakazuje](https://tools.ietf.org/html/rfc7540#section-9.2.1) opakované vyjednávání.
- Protokol TLS 1,3 [odebral](https://tools.ietf.org/html/rfc8740#section-1) podporu pro opětovné vyjednávání.

ASP.NET Core 5 Preview 7 a novější přináší pohodlnější podporu pro volitelné klientské certifikáty. Další informace najdete v [ukázce volitelných certifikátů](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).

Následující přístup podporuje volitelné klientské certifikáty:

* Nastavte vazbu pro doménu a subdoménu:
  * Například nastavte vazby v `contoso.com` a `myClient.contoso.com` . `contoso.com`Hostitel nevyžaduje klientský certifikát `myClient.contoso.com` , ale má.
  * Další informace:
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Poznámka Kestrel v současné době nepodporuje u jedné vazby více konfigurací TLS, budete potřebovat dvě vazby s jedinečnými IP adresami nebo porty. Sihttps://github.com/dotnet/runtime/issues/31097
    * IIS
      * [Hostování služby IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [Konfigurace zabezpečení služby IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * Http.Sys: [Konfigurace Windows serveru](xref:fundamentals/servers/httpsys#configure-windows-server)
* Pro požadavky na webovou aplikaci, která vyžaduje klientský certifikát, a žádný z nich:
  * Přesměruje se na stejnou stránku pomocí subdomény chráněné klientským certifikátem.
  * Například přesměrujte na `myClient.contoso.com/requestedPage` . Vzhledem k tomu, že se `myClient.contoso.com/requestedPage` jedná o jiný název hostitele než `contoso.com/requestedPage` , klient vytvoří jiné připojení a klientský certifikát je k dispozici.
  * Další informace naleznete v tématu <xref:security/authorization/introduction>.

Ponechte dotazy, komentáře a další zpětnou vazbu k volitelným klientským certifikátům v [tomto problému diskuze GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/18720) .

&dagger;Indikace názvu serveru (SNI) je rozšíření TLS, které zahrnuje virtuální doménu jako součást vyjednávání SSL. To efektivně znamená, že k identifikaci koncového bodu sítě lze použít název virtuální domény nebo název hostitele.
