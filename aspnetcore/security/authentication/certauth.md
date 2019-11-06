---
title: Konfigurace ověřování certifikátů v ASP.NET Core
author: blowdart
description: Přečtěte si, jak nakonfigurovat ověřování certifikátů v ASP.NET Core pro IIS a HTTP. sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 11/05/2019
uid: security/authentication/certauth
ms.openlocfilehash: 081935e6e6248b5fe9b7bf4cd966dc73761d2ec1
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634056"
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

Přidejte také `app.UseAuthentication();` do metody `Startup.Configure`. V opačném případě vlastnost HttpContext. User nebude nastavena na `ClaimsPrincipal` vytvořenou z certifikátu. Příklad:

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

Tato kontrola ověří, zda je povolen pouze příslušný typ certifikátu.

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Tato kontrola ověří, že certifikát prezentovaný klientem má rozšířené použití klíče (EKU) ověřování klienta (EKU) nebo žádný rozšířená použití klíče. Pokud se jako specifikace nezadá žádné rozšířené použití klíče, považují se všechny rozšířená použití klíče za platné.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Tato kontrola ověří, že se certifikát nachází v období platnosti. U každé žádosti obslužná rutina zajišťuje, že platnost certifikátu, který byl platný při jeho předložení, vypršela během aktuální relace.

### <a name="revocationflag"></a>RevocationFlag

Příznak, který určuje, které certifikáty v řetězci mají být zkontrolovány pro odvolání.

Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.

### <a name="revocationmode"></a>RevocationMode

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
  * Sestavování vlastního objektu zabezpečení. Vezměte v úvahu následující příklad v `Startup.ConfigureServices`:

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

Pro reálné funkce pravděpodobně budete chtít volat službu registrovanou v injektáže závislosti, který se připojuje k databázi nebo jinému typu úložiště uživatele. Ke službě získáte přístup pomocí kontextu předaného do vašeho delegáta. Vezměte v úvahu následující příklad v `Startup.ConfigureServices`:

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
                        o.ConfigureHttpsDefaults(o => o.ClientCertificateMode = ClientCertificateMode.RequireCertificate);
                    });
                });
}
```

### <a name="iis"></a>IIS

Ve Správci služby IIS proveďte následující kroky:

1. Na kartě **připojení** vyberte svou lokalitu.
1. Dvakrát klikněte na možnost **Nastavení SSL** v okně **zobrazení funkcí** .
1. Zaškrtněte políčko **vyžadovat protokol SSL** a v části **certifikáty klienta** vyberte tlačítko **vyžadovat** přepínač.

![Nastavení klientského certifikátu ve službě IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure a vlastní webové proxy servery

Postup konfigurace middlewaru pro předávání certifikátů najdete v [dokumentaci k hostiteli a nasazení](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .
