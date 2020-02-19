---
title: Konfigurace ověřování certifikátů v ASP.NET Core
author: blowdart
description: Přečtěte si, jak nakonfigurovat ověřování certifikátů v ASP.NET Core pro IIS a HTTP. sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 01/02/2020
uid: security/authentication/certauth
ms.openlocfilehash: 280daa86510d4445c791b6952653122961f13aeb
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447279"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Konfigurace ověřování certifikátů v ASP.NET Core

`Microsoft.AspNetCore.Authentication.Certificate` obsahuje implementaci podobnou [ověřování certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) pro ASP.NET Core. Ověřování certifikátu se provádí na úrovni protokolu TLS dlouho předtím, než se někdy získá ASP.NET Core. Přesněji platí, že se jedná o obslužnou rutinu ověřování, která certifikát ověřuje, a pak poskytuje událost, na kterou můžete tento certifikát vyřešit `ClaimsPrincipal`. 

[Nakonfigurujte hostitele](#configure-your-host-to-require-certificates) pro ověřování certifikátů, jako IIS, Kestrel, Azure Web Apps nebo cokoli jiného, co používáte.

## <a name="proxy-and-load-balancer-scenarios"></a>Scénáře proxy a nástroje pro vyrovnávání zatížení

Ověřování certifikátů je stavový scénář, který se primárně používá, když proxy nebo nástroj pro vyrovnávání zatížení nezpracovává provoz mezi klienty a servery. Pokud se používá proxy server nebo nástroj pro vyrovnávání zatížení, funguje ověřování certifikátů pouze v případě, že proxy nebo nástroj pro vyrovnávání zatížení:

* Zpracovává ověřování.
* Předá do aplikace informace o ověřování uživatele (například v hlavičce požadavku), která funguje na ověřovacích informacích.

Alternativou k ověřování certifikátů v prostředích, kde se používají proxy a nástroje pro vyrovnávání zatížení, je služba AD FS (Active Directory federovaných služeb) se službou OpenID Connect (OIDC).

## <a name="get-started"></a>Začínáme

Získejte certifikát HTTPS, použijte ho a [Nakonfigurujte hostitele](#configure-your-host-to-require-certificates) tak, aby vyžadoval certifikáty.

Do webové aplikace přidejte odkaz na balíček `Microsoft.AspNetCore.Authentication.Certificate`. Potom v metodě `Startup.ConfigureServices` volejte `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` s vašimi možnostmi a poskytněte delegátovi `OnCertificateValidated`, aby provedl dodatečné ověřování klientského certifikátu odeslaného pomocí požadavků. Zapněte tyto informace do `ClaimsPrincipal` a nastavte ji na vlastnost `context.Principal`.

Pokud se ověření nepovede, vrátí tato obslužná rutina místo `401 (Unauthorized)``403 (Forbidden)` odpověď, jak byste to mohli očekávat. Důvodem je, že při počátečním připojení TLS by mělo probíhat ověřování. V době, kdy dosáhne obslužné rutiny, je příliš pozdě. Neexistuje žádný způsob, jak upgradovat připojení z anonymního připojení k jednomu pomocí certifikátu.

Přidejte také `app.UseAuthentication();` do metody `Startup.Configure`. V opačném případě `HttpContext.User` nebude nastaven na `ClaimsPrincipal` vytvořené z certifikátu. Například:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

Předchozí příklad ukazuje výchozí způsob, jak přidat ověřování certifikátu. Obslužná rutina vytvoří objekt zabezpečení uživatele pomocí vlastností Common Certificate.

## <a name="configure-certificate-validation"></a>Konfigurace ověření certifikátu

Obslužná rutina `CertificateAuthenticationOptions` obsahuje některá Vestavěná ověření, která jsou minimálními ověřeními, která byste měli provést na certifikátu. Každé z těchto nastavení je ve výchozím nastavení povoleno.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = Chained, SelfSigned nebo All (zřetězené | SelfSigned)

Výchozí hodnota: `CertificateTypes.Chained`

Tato kontrola ověří, zda je povolen pouze příslušný typ certifikátu. Pokud aplikace používá certifikáty podepsané svým držitelem, musí být tato možnost nastavená na `CertificateTypes.All` nebo `CertificateTypes.SelfSigned`.

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Výchozí hodnota: `true`

Tato kontrola ověří, že certifikát prezentovaný klientem má rozšířené použití klíče (EKU) ověřování klienta (EKU) nebo žádný rozšířená použití klíče. Pokud se jako specifikace nezadá žádné rozšířené použití klíče, považují se všechny rozšířená použití klíče za platné.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Výchozí hodnota: `true`

Tato kontrola ověří, že se certifikát nachází v období platnosti. U každé žádosti obslužná rutina zajišťuje, že platnost certifikátu, který byl platný při jeho předložení, vypršela během aktuální relace.

### <a name="revocationflag"></a>RevocationFlag

Výchozí hodnota: `X509RevocationFlag.ExcludeRoot`

Příznak, který určuje, které certifikáty v řetězci mají být zkontrolovány pro odvolání.

Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.

### <a name="revocationmode"></a>RevocationMode

Výchozí hodnota: `X509RevocationMode.Online`

Příznak, který určuje, jak se provádí kontroly odvolání.

Zadání online kontroly může mít za následek dlouhou prodlevu při kontaktování certifikační autority.

Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Můžu aplikaci nakonfigurovat tak, aby vyžadovala certifikát jenom na určitých cestách?

To není možné. Zapamatujte si, že výměna certifikátu se dokončila, když se spustí konverzace HTTPS, a to na serveru před přijetím prvního požadavku v tomto připojení, takže není možné ho oborovat na základě jakýchkoli polí požadavků.

## <a name="handler-events"></a>Události obslužných rutin

Obslužná rutina má dvě události:

* `OnAuthenticationFailed` &ndash; volána, pokud dojde k výjimce během ověřování a umožní vám reagovat.
* `OnCertificateValidated` &ndash; voláno po ověření certifikátu, bylo úspěšně vytvořeno ověření a výchozí objekt zabezpečení. Tato událost umožňuje provádět vlastní ověřování a rozšíření nebo nahrazení objektu zabezpečení. Příklady zahrnují:
  * Určení, jestli se pro vaše služby ví certifikát.
  * Sestavování vlastního objektu zabezpečení. Vezměte v úvahu následující příklad `Startup.ConfigureServices`:

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

Pokud zjistíte, že příchozí certifikát nesplňuje vaše dodatečné ověření, zavolejte `context.Fail("failure reason")` s důvodem selhání.

Pro reálné funkce pravděpodobně budete chtít volat službu registrovanou v injektáže závislosti, který se připojuje k databázi nebo jinému typu úložiště uživatele. Ke službě získáte přístup pomocí kontextu předaného do vašeho delegáta. Vezměte v úvahu následující příklad `Startup.ConfigureServices`:

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

V koncepčním případě je ověření certifikátu v takovém případě oprávnění. Přidání kontroly, například vystavitele nebo kryptografického otisku v zásadách autorizace místo v rámci `OnCertificateValidated`, je naprosto přijatelné.

## <a name="configure-your-host-to-require-certificates"></a>Konfigurace hostitele pro vyžadování certifikátů

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

Metoda `AddCertificateForwarding` slouží k zadání:

* Název hlavičky klienta.
* Způsob načtení certifikátu (pomocí vlastnosti `HeaderConverter`).

Ve vlastních webových proxy serverech se certifikát předává jako vlastní hlavička žádosti, například `X-SSL-CERT`. Pokud ho chcete použít, nakonfigurujte předávání certifikátů v `Startup.ConfigureServices`:

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

Metoda `Startup.Configure` pak přidá middleware. `UseCertificateForwarding` se volá před volání `UseAuthentication` a `UseAuthorization`:

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

Samostatnou třídu lze použít k implementaci logiky ověřování. Protože v tomto příkladu se používá stejný certifikát podepsaný svým držitelem, ujistěte se, že se dá použít jenom váš certifikát. Ověřte, že se neshodují kryptografické otisky certifikátu klienta i certifikátu serveru. v opačném případě se dá použít libovolný certifikát, který bude pro ověření stačit. To by bylo použito v rámci metody `AddCertificate`. V případě použití zprostředkujících nebo podřízených certifikátů můžete také ověřit předmět nebo vystavitele.

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

HttpClientHandler lze přidat přímo v konstruktoru třídy HttpClient. Při vytváření instancí HttpClient byste měli věnovat pozornost. HttpClient pak certifikát odešle spolu s každou žádostí.

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

V následujícím příkladu se klientský certifikát přidá do HttpClientHandler pomocí vlastnosti vlastnost ClientCertificates z obslužné rutiny. Tato obslužná rutina se pak dá použít v pojmenované instanci HttpClient pomocí metody ConfigurePrimaryHttpMessageHandler. Toto je instalační program ve třídě Startup v metodě ConfigureServices.

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

IHttpClientFactory se pak může použít k získání pojmenované instance s obslužnou rutinou a certifikátem. Metoda CreateClient s názvem klienta definovaného ve spouštěcí třídě slouží k získání instance. Požadavek HTTP lze odeslat pomocí klienta podle potřeby.

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

Vytvoření certifikátů je nejzávažnou součástí nastavení tohoto toku. Kořenový certifikát se dá vytvořit pomocí rutiny `New-SelfSignedCertificate` PowerShellu. Při vytváření certifikátu použijte silné heslo. Je důležité přidat parametr `KeyUsageProperty` a parametr `KeyUsage`, jak je znázorněno.

#### <a name="create-root-ca"></a>Vytvořit kořenovou certifikační autoritu

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> Hodnota parametru `-DnsName` musí odpovídat cíli nasazení aplikace. Například "localhost" pro vývoj.

#### <a name="install-in-the-trusted-root"></a>Nainstalovat do důvěryhodného kořenového adresáře

Kořenový certifikát musí být v hostitelském systému důvěryhodný. Kořenový certifikát, který nebyl vytvořen certifikační autoritou, nebude ve výchozím nastavení považován za důvěryhodný. Následující odkaz vysvětluje, jak to lze provést ve Windows:

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Zprostředkující certifikát

Zprostředkující certifikát se teď dá vytvořit z kořenového certifikátu. To není vyžadováno pro všechny případy použití, ale možná budete muset vytvořit mnoho certifikátů nebo musíte aktivovat nebo zakázat skupiny certifikátů. Parametr `TextExtension` je vyžadován pro nastavení délky cesty v základních omezeních certifikátu.

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
