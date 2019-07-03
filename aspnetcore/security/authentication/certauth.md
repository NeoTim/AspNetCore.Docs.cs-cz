---
title: Konfigurace ověření certifikátu v ASP.NET Core
author: blowdart
description: Zjistěte, jak nakonfigurovat ověřování pomocí certifikátu v ASP.NET Core pro službu IIS a HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 06/11/2019
uid: security/authentication/certauth
ms.openlocfilehash: 8609c58265340da1d618135795915d6c49e750a3
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538725"
---
# <a name="overview"></a>Přehled

`Microsoft.AspNetCore.Authentication.Certificate` obsahuje implementaci podobný [ověřování pomocí certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) pro ASP.NET Core. Ověřování pomocí certifikátu dochází na úrovni protokolu TLS, dlouhé nad někdy k ASP.NET Core. Přesněji řečeno, toto je obslužnou rutinu ověřování, který ověří certifikát a poté přiřadí událost, ve kterém můžete vyřešit tento certifikát k `ClaimsPrincipal`. 

[Konfigurace hostitele](#configure-your-host-to-require-certificates) pro ověření certifikátu, už to jsou služby IIS, Kestrel, Azure Web Apps nebo cokoli, co jiného používáte.

## <a name="get-started"></a>Začínáme

Získat certifikát HTTPS, použijte ho, a [konfigurace hostitele](#configure-your-host-to-require-certificates) tak, aby vyžadovala certifikáty.

Ve webové aplikaci, přidejte odkaz na `Microsoft.AspNetCore.Authentication.Certificate` balíčku. Pak v `Startup.Configure` metody, volání `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` s možnostmi, poskytuje delegáta pro `OnCertificateValidated` provedete jakékoli doplňující ověření certifikátu klienta zaslaného s požadavky. Zapnout tyto informace do aplikace `ClaimsPrincipal` a nastavte ho na `context.Principal` vlastnost.

Pokud se ověření nezdaří, vrátí tato obslužná rutina `403 (Forbidden)` odpovědi spíše `401 (Unauthorized)`, jak byste asi očekávali. Důvody, proč je, že ověřování by mělo nastat během počátečního připojení protokolu TLS. V době dosáhne obslužnou rutinu bude příliš pozdě. Neexistuje žádný způsob, jak upgradovat na předplatné se certifikát připojení z anonymní připojení.

Přidejte také `app.UseAuthentication();` v `Startup.Configure` metody. V opačném případě se nenastaví HttpContext.User `ClaimsPrincipal` vytvořili z certifikátu. Příklad:

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

Předchozí příklad ukazuje výchozí způsob, jak přidat ověřování pomocí certifikátu. Obslužná rutina vytvoří hlavní název uživatele pomocí běžné vlastnosti certifikátu.

## <a name="configure-certificate-validation"></a>Konfigurace ověření certifikátu

`CertificateAuthenticationOptions` Obslužná rutina nemá některé integrované ověření, která jsou minimální ověření by měl provádět na certifikát. Každé z těchto nastavení je standardně povolená.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = zřetězené SelfSigned nebo všechny (zřetězené | SelfSigned)

Tato kontrola ověřuje, zda je povolen pouze typ příslušný certifikát.

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Tato kontrola ověřuje, že certifikát předložený klienta má vůbec rozšířené použití klíče (EKU), nebo žádná rozšířená použití klíče ověření klienta. Podle specifikace Řekněme, pokud není zadána žádná rozšířená použití klíče, pak všechna rozšířená použití klíče jsou považovány za platné.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Tato kontrola ověřuje, že certifikát je v období své platnosti. S každým požadavkem obslužnou rutinu zajistí, že certifikát, který byl platný, když se nepředal nevypršela platnost během jeho aktuální relaci.

### <a name="revocationflag"></a>RevocationFlag

Příznak, který určuje, které certifikáty v řetězu kontroluje odvolání.

Kontroly odvolání jsou prováděny pouze při certifikát zřetězil s kořenovým certifikátem.

### <a name="revocationmode"></a>revocationMode

Příznak, který určuje, jak se provádí kontroly odvolání.

Určení online kontrola může způsobit dlouhé zpoždění při kontaktování certifikační autority.

Kontroly odvolání jsou prováděny pouze při certifikát zřetězil s kořenovým certifikátem.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Můžete nakonfigurovat aplikaci tak, aby vyžadovala certifikát pouze na určité cesty ke složkám?

To není možné. Mějte na paměti, že certifikát výměny se provádí, že začátek konverzace HTTPS, probíhá server před první žádost o přijetí na toto připojení, není možné obor podle jakékoli pole požadavku.

## <a name="handler-events"></a>Obslužná rutina události

Obslužná rutina má dvě události:

* `OnAuthenticationFailed` &ndash; Volá se, pokud výjimka se stane při ověřování a umožňuje reagovat.
* `OnCertificateValidated` &ndash; Volá se po certifikát byl ověřen, zdařilo ověření a vytvoří výchozí objekt se vytvořil. Tato událost vám umožňuje provést vlastní ověřování a rozšířit nebo nahradit objekt zabezpečení. Příklady zahrnují:
  * Určení, pokud tento certifikát se známými k vašim službám.
  * Vytváření vlastních instanční objekt. Prohlédněte si následující příklad na `Startup.ConfigureServices`:

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

Pokud zjistíte příchozí certifikát nesplňuje vaše ověřování navíc, volání `context.Fail("failure reason")` s důvodem selhání.

Pro skutečné funkce, budete pravděpodobně chtít volání služby zaregistrovaný v injektáž závislostí, která se připojuje k databázi nebo jiný typ úložiště uživatele. Přístup ke službě s použitím kontextu předané do delegáta. Prohlédněte si následující příklad na `Startup.ConfigureServices`:

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

Koncepčně ověření certifikátu je autorizace. Přidat kontrolu, například, vydavatele nebo kryptografický otisk v zásady autorizace, ne uvnitř `OnCertificateValidated`, je zcela přijatelné.

## <a name="configure-your-host-to-require-certificates"></a>Nakonfigurujte hostitele tak, aby vyžadovala certifikáty

### <a name="kestrel"></a>Kestrel

V *Program.cs*, nakonfigurujte Kestrel následujícím způsobem:

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel(options =>
        {
            options.ConfigureHttpsDefaults(opt => 
                opt.ClientCertificateMode = 
                    ClientCertificateMode.RequireCertificate);
        })
        .Build();
```

### <a name="iis"></a>IIS

Proveďte následující kroky ve Správci služby IIS:

1. Vyberte svou lokalitu z **připojení** kartu.
1. Dvakrát klikněte **nastavení SSL** možnost **zobrazení funkcí** okna.
1. Zkontrolujte **požadovat protokol SSL** zaškrtávací políčko a vyberte **vyžadují** přepínač v **klientské certifikáty** oddílu.

![Nastavení certifikátu klienta ve službě IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure a vlastní webové proxy servery

Najdete v článku [hostitelství a nasazení dokumentaci](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) jak nakonfigurovat certifikát předávání middlewaru.
