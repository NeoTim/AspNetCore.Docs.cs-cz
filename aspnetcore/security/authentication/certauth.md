---
title: Konfigurace ověřování certifikátů v ASP.NET Core
author: blowdart
description: Přečtěte si, jak nakonfigurovat ověřování certifikátů v ASP.NET Core pro IIS a HTTP. sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 08/19/2019
uid: security/authentication/certauth
ms.openlocfilehash: ce7bcdbfb8ce0f1febf34b49786e92c917be139c
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384834"
---
# <a name="overview"></a><span data-ttu-id="7f96a-103">Přehled</span><span class="sxs-lookup"><span data-stu-id="7f96a-103">Overview</span></span>

<span data-ttu-id="7f96a-104">`Microsoft.AspNetCore.Authentication.Certificate`obsahuje implementaci podobnou [ověřování certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f96a-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="7f96a-105">Ověřování certifikátu se provádí na úrovni protokolu TLS dlouho předtím, než se někdy získá ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f96a-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="7f96a-106">Přesněji platí, že se jedná o obslužnou rutinu ověřování, která certifikát ověřuje, a pak poskytuje událost, kde můžete tento certifikát vyřešit na `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="7f96a-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="7f96a-107">[Nakonfigurujte hostitele](#configure-your-host-to-require-certificates) pro ověřování certifikátů, jako IIS, Kestrel, Azure Web Apps nebo cokoli jiného, co používáte.</span><span class="sxs-lookup"><span data-stu-id="7f96a-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="7f96a-108">Scénáře proxy a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="7f96a-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="7f96a-109">Ověřování certifikátů je stavový scénář, který se primárně používá, když proxy nebo nástroj pro vyrovnávání zatížení nezpracovává provoz mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="7f96a-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="7f96a-110">Pokud se používá proxy server nebo nástroj pro vyrovnávání zatížení, funguje ověřování certifikátů pouze v případě, že proxy nebo nástroj pro vyrovnávání zatížení:</span><span class="sxs-lookup"><span data-stu-id="7f96a-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="7f96a-111">Zpracovává ověřování.</span><span class="sxs-lookup"><span data-stu-id="7f96a-111">Handles the authentication.</span></span>
* <span data-ttu-id="7f96a-112">Předá do aplikace informace o ověřování uživatele (například v hlavičce požadavku), která funguje na ověřovacích informacích.</span><span class="sxs-lookup"><span data-stu-id="7f96a-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="7f96a-113">Alternativou k ověřování certifikátů v prostředích, kde se používají proxy a nástroje pro vyrovnávání zatížení, je služba AD FS (Active Directory federovaných služeb) se službou OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="7f96a-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="7f96a-114">Začínáme</span><span class="sxs-lookup"><span data-stu-id="7f96a-114">Get started</span></span>

<span data-ttu-id="7f96a-115">Získejte certifikát HTTPS, použijte ho a [Nakonfigurujte hostitele](#configure-your-host-to-require-certificates) tak, aby vyžadoval certifikáty.</span><span class="sxs-lookup"><span data-stu-id="7f96a-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="7f96a-116">Do webové aplikace přidejte odkaz na `Microsoft.AspNetCore.Authentication.Certificate` balíček.</span><span class="sxs-lookup"><span data-stu-id="7f96a-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="7f96a-117">Potom v `Startup.Configure` metodě zavolejte `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` s vašimi možnostmi `OnCertificateValidated` a poskytněte delegátovi, aby provedl dodatečné ověřování klientského certifikátu odeslaného pomocí požadavků.</span><span class="sxs-lookup"><span data-stu-id="7f96a-117">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="7f96a-118">Zapněte tyto informace do `ClaimsPrincipal` a nastavte ji `context.Principal` na vlastnost.</span><span class="sxs-lookup"><span data-stu-id="7f96a-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="7f96a-119">Pokud se ověření nepovede, vrátí `403 (Forbidden)` Tato obslužná rutina odpověď `401 (Unauthorized)`místo toho, jak byste mohli očekávat.</span><span class="sxs-lookup"><span data-stu-id="7f96a-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="7f96a-120">Důvodem je, že při počátečním připojení TLS by mělo probíhat ověřování.</span><span class="sxs-lookup"><span data-stu-id="7f96a-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="7f96a-121">V době, kdy dosáhne obslužné rutiny, je příliš pozdě.</span><span class="sxs-lookup"><span data-stu-id="7f96a-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="7f96a-122">Neexistuje žádný způsob, jak upgradovat připojení z anonymního připojení k jednomu pomocí certifikátu.</span><span class="sxs-lookup"><span data-stu-id="7f96a-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="7f96a-123">Přidejte `app.UseAuthentication();` také`Startup.Configure` do metody.</span><span class="sxs-lookup"><span data-stu-id="7f96a-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="7f96a-124">V opačném případě nebude vlastnost HttpContext. User nastavena na `ClaimsPrincipal` hodnotu vytvořeno z certifikátu.</span><span class="sxs-lookup"><span data-stu-id="7f96a-124">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="7f96a-125">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7f96a-125">For example:</span></span>

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

<span data-ttu-id="7f96a-126">Předchozí příklad ukazuje výchozí způsob, jak přidat ověřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="7f96a-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="7f96a-127">Obslužná rutina vytvoří objekt zabezpečení uživatele pomocí vlastností Common Certificate.</span><span class="sxs-lookup"><span data-stu-id="7f96a-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="7f96a-128">Konfigurace ověření certifikátu</span><span class="sxs-lookup"><span data-stu-id="7f96a-128">Configure certificate validation</span></span>

<span data-ttu-id="7f96a-129">`CertificateAuthenticationOptions` Obslužná rutina obsahuje některá Vestavěná ověření, která jsou minimálními ověřeními, které byste měli provést na certifikátu.</span><span class="sxs-lookup"><span data-stu-id="7f96a-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="7f96a-130">Každé z těchto nastavení je ve výchozím nastavení povoleno.</span><span class="sxs-lookup"><span data-stu-id="7f96a-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="7f96a-131">AllowedCertificateTypes = Chained, SelfSigned nebo All (zřetězené | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="7f96a-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="7f96a-132">Tato kontrola ověří, zda je povolen pouze příslušný typ certifikátu.</span><span class="sxs-lookup"><span data-stu-id="7f96a-132">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="7f96a-133">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="7f96a-133">ValidateCertificateUse</span></span>

<span data-ttu-id="7f96a-134">Tato kontrola ověří, že certifikát prezentovaný klientem má rozšířené použití klíče (EKU) ověřování klienta (EKU) nebo žádný rozšířená použití klíče.</span><span class="sxs-lookup"><span data-stu-id="7f96a-134">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="7f96a-135">Pokud se jako specifikace nezadá žádné rozšířené použití klíče, považují se všechny rozšířená použití klíče za platné.</span><span class="sxs-lookup"><span data-stu-id="7f96a-135">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="7f96a-136">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="7f96a-136">ValidateValidityPeriod</span></span>

<span data-ttu-id="7f96a-137">Tato kontrola ověří, že se certifikát nachází v období platnosti.</span><span class="sxs-lookup"><span data-stu-id="7f96a-137">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="7f96a-138">U každé žádosti obslužná rutina zajišťuje, že platnost certifikátu, který byl platný při jeho předložení, vypršela během aktuální relace.</span><span class="sxs-lookup"><span data-stu-id="7f96a-138">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="7f96a-139">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="7f96a-139">RevocationFlag</span></span>

<span data-ttu-id="7f96a-140">Příznak, který určuje, které certifikáty v řetězci mají být zkontrolovány pro odvolání.</span><span class="sxs-lookup"><span data-stu-id="7f96a-140">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="7f96a-141">Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="7f96a-141">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="7f96a-142">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="7f96a-142">RevocationMode</span></span>

<span data-ttu-id="7f96a-143">Příznak, který určuje, jak se provádí kontroly odvolání.</span><span class="sxs-lookup"><span data-stu-id="7f96a-143">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="7f96a-144">Zadání online kontroly může mít za následek dlouhou prodlevu při kontaktování certifikační autority.</span><span class="sxs-lookup"><span data-stu-id="7f96a-144">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="7f96a-145">Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="7f96a-145">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="7f96a-146">Můžu aplikaci nakonfigurovat tak, aby vyžadovala certifikát jenom na určitých cestách?</span><span class="sxs-lookup"><span data-stu-id="7f96a-146">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="7f96a-147">To není možné.</span><span class="sxs-lookup"><span data-stu-id="7f96a-147">This isn't possible.</span></span> <span data-ttu-id="7f96a-148">Zapamatujte si, že výměna certifikátu se dokončila, když se spustí konverzace HTTPS, a to na serveru před přijetím prvního požadavku v tomto připojení, takže není možné ho oborovat na základě jakýchkoli polí požadavků.</span><span class="sxs-lookup"><span data-stu-id="7f96a-148">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="7f96a-149">Události obslužných rutin</span><span class="sxs-lookup"><span data-stu-id="7f96a-149">Handler events</span></span>

<span data-ttu-id="7f96a-150">Obslužná rutina má dvě události:</span><span class="sxs-lookup"><span data-stu-id="7f96a-150">The handler has two events:</span></span>

* <span data-ttu-id="7f96a-151">`OnAuthenticationFailed`&ndash; Volá se, pokud dojde k výjimce během ověřování a umožňuje reagovat.</span><span class="sxs-lookup"><span data-stu-id="7f96a-151">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="7f96a-152">`OnCertificateValidated`&ndash; Volá se po ověření certifikátu. vytvořil se ověření a vytvořil se výchozí objekt zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="7f96a-152">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="7f96a-153">Tato událost umožňuje provádět vlastní ověřování a rozšíření nebo nahrazení objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="7f96a-153">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="7f96a-154">Příklady zahrnují:</span><span class="sxs-lookup"><span data-stu-id="7f96a-154">For examples include:</span></span>
  * <span data-ttu-id="7f96a-155">Určení, jestli se pro vaše služby ví certifikát.</span><span class="sxs-lookup"><span data-stu-id="7f96a-155">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="7f96a-156">Sestavování vlastního objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="7f96a-156">Constructing your own principal.</span></span> <span data-ttu-id="7f96a-157">Vezměte v `Startup.ConfigureServices`úvahu následující příklad:</span><span class="sxs-lookup"><span data-stu-id="7f96a-157">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7f96a-158">Pokud zjistíte, že příchozí certifikát nesplňuje vaše dodatečné ověření, `context.Fail("failure reason")` zavolejte důvod selhání.</span><span class="sxs-lookup"><span data-stu-id="7f96a-158">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="7f96a-159">Pro reálné funkce pravděpodobně budete chtít volat službu registrovanou v injektáže závislosti, který se připojuje k databázi nebo jinému typu úložiště uživatele.</span><span class="sxs-lookup"><span data-stu-id="7f96a-159">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="7f96a-160">Ke službě získáte přístup pomocí kontextu předaného do vašeho delegáta.</span><span class="sxs-lookup"><span data-stu-id="7f96a-160">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="7f96a-161">Vezměte v `Startup.ConfigureServices`úvahu následující příklad:</span><span class="sxs-lookup"><span data-stu-id="7f96a-161">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7f96a-162">V koncepčním případě je ověření certifikátu v takovém případě oprávnění.</span><span class="sxs-lookup"><span data-stu-id="7f96a-162">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="7f96a-163">Přidání kontroly, například vystavitele nebo kryptografického otisku v zásadách autorizace, nikoli uvnitř `OnCertificateValidated`, je naprosto přijatelné.</span><span class="sxs-lookup"><span data-stu-id="7f96a-163">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="7f96a-164">Konfigurace hostitele pro vyžadování certifikátů</span><span class="sxs-lookup"><span data-stu-id="7f96a-164">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="7f96a-165">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7f96a-165">Kestrel</span></span>

<span data-ttu-id="7f96a-166">V *program.cs*nakonfigurujte Kestrel následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="7f96a-166">In *Program.cs*, configure Kestrel as follows:</span></span>

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

### <a name="iis"></a><span data-ttu-id="7f96a-167">IIS</span><span class="sxs-lookup"><span data-stu-id="7f96a-167">IIS</span></span>

<span data-ttu-id="7f96a-168">Ve Správci služby IIS proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="7f96a-168">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="7f96a-169">Na kartě **připojení** vyberte svou lokalitu.</span><span class="sxs-lookup"><span data-stu-id="7f96a-169">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="7f96a-170">Dvakrát klikněte na možnost **Nastavení SSL** v okně **zobrazení funkcí** .</span><span class="sxs-lookup"><span data-stu-id="7f96a-170">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="7f96a-171">Zaškrtněte políčko **vyžadovat protokol SSL** a v části **certifikáty klienta** vyberte tlačítko **vyžadovat** přepínač.</span><span class="sxs-lookup"><span data-stu-id="7f96a-171">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Nastavení klientského certifikátu ve službě IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="7f96a-173">Azure a vlastní webové proxy servery</span><span class="sxs-lookup"><span data-stu-id="7f96a-173">Azure and custom web proxies</span></span>

<span data-ttu-id="7f96a-174">Postup konfigurace middlewaru pro předávání certifikátů najdete v [dokumentaci k hostiteli a nasazení](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .</span><span class="sxs-lookup"><span data-stu-id="7f96a-174">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
