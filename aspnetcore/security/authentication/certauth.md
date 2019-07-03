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
# <a name="overview"></a><span data-ttu-id="ceac9-103">Přehled</span><span class="sxs-lookup"><span data-stu-id="ceac9-103">Overview</span></span>

<span data-ttu-id="ceac9-104">`Microsoft.AspNetCore.Authentication.Certificate` obsahuje implementaci podobný [ověřování pomocí certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ceac9-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="ceac9-105">Ověřování pomocí certifikátu dochází na úrovni protokolu TLS, dlouhé nad někdy k ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ceac9-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="ceac9-106">Přesněji řečeno, toto je obslužnou rutinu ověřování, který ověří certifikát a poté přiřadí událost, ve kterém můžete vyřešit tento certifikát k `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="ceac9-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="ceac9-107">[Konfigurace hostitele](#configure-your-host-to-require-certificates) pro ověření certifikátu, už to jsou služby IIS, Kestrel, Azure Web Apps nebo cokoli, co jiného používáte.</span><span class="sxs-lookup"><span data-stu-id="ceac9-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="get-started"></a><span data-ttu-id="ceac9-108">Začínáme</span><span class="sxs-lookup"><span data-stu-id="ceac9-108">Get started</span></span>

<span data-ttu-id="ceac9-109">Získat certifikát HTTPS, použijte ho, a [konfigurace hostitele](#configure-your-host-to-require-certificates) tak, aby vyžadovala certifikáty.</span><span class="sxs-lookup"><span data-stu-id="ceac9-109">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="ceac9-110">Ve webové aplikaci, přidejte odkaz na `Microsoft.AspNetCore.Authentication.Certificate` balíčku.</span><span class="sxs-lookup"><span data-stu-id="ceac9-110">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="ceac9-111">Pak v `Startup.Configure` metody, volání `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` s možnostmi, poskytuje delegáta pro `OnCertificateValidated` provedete jakékoli doplňující ověření certifikátu klienta zaslaného s požadavky.</span><span class="sxs-lookup"><span data-stu-id="ceac9-111">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="ceac9-112">Zapnout tyto informace do aplikace `ClaimsPrincipal` a nastavte ho na `context.Principal` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ceac9-112">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="ceac9-113">Pokud se ověření nezdaří, vrátí tato obslužná rutina `403 (Forbidden)` odpovědi spíše `401 (Unauthorized)`, jak byste asi očekávali.</span><span class="sxs-lookup"><span data-stu-id="ceac9-113">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="ceac9-114">Důvody, proč je, že ověřování by mělo nastat během počátečního připojení protokolu TLS.</span><span class="sxs-lookup"><span data-stu-id="ceac9-114">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="ceac9-115">V době dosáhne obslužnou rutinu bude příliš pozdě.</span><span class="sxs-lookup"><span data-stu-id="ceac9-115">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="ceac9-116">Neexistuje žádný způsob, jak upgradovat na předplatné se certifikát připojení z anonymní připojení.</span><span class="sxs-lookup"><span data-stu-id="ceac9-116">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="ceac9-117">Přidejte také `app.UseAuthentication();` v `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="ceac9-117">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="ceac9-118">V opačném případě se nenastaví HttpContext.User `ClaimsPrincipal` vytvořili z certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ceac9-118">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="ceac9-119">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ceac9-119">For example:</span></span>

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

<span data-ttu-id="ceac9-120">Předchozí příklad ukazuje výchozí způsob, jak přidat ověřování pomocí certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ceac9-120">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="ceac9-121">Obslužná rutina vytvoří hlavní název uživatele pomocí běžné vlastnosti certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ceac9-121">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="ceac9-122">Konfigurace ověření certifikátu</span><span class="sxs-lookup"><span data-stu-id="ceac9-122">Configure certificate validation</span></span>

<span data-ttu-id="ceac9-123">`CertificateAuthenticationOptions` Obslužná rutina nemá některé integrované ověření, která jsou minimální ověření by měl provádět na certifikát.</span><span class="sxs-lookup"><span data-stu-id="ceac9-123">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="ceac9-124">Každé z těchto nastavení je standardně povolená.</span><span class="sxs-lookup"><span data-stu-id="ceac9-124">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="ceac9-125">AllowedCertificateTypes = zřetězené SelfSigned nebo všechny (zřetězené | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="ceac9-125">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="ceac9-126">Tato kontrola ověřuje, zda je povolen pouze typ příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="ceac9-126">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="ceac9-127">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="ceac9-127">ValidateCertificateUse</span></span>

<span data-ttu-id="ceac9-128">Tato kontrola ověřuje, že certifikát předložený klienta má vůbec rozšířené použití klíče (EKU), nebo žádná rozšířená použití klíče ověření klienta.</span><span class="sxs-lookup"><span data-stu-id="ceac9-128">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="ceac9-129">Podle specifikace Řekněme, pokud není zadána žádná rozšířená použití klíče, pak všechna rozšířená použití klíče jsou považovány za platné.</span><span class="sxs-lookup"><span data-stu-id="ceac9-129">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="ceac9-130">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="ceac9-130">ValidateValidityPeriod</span></span>

<span data-ttu-id="ceac9-131">Tato kontrola ověřuje, že certifikát je v období své platnosti.</span><span class="sxs-lookup"><span data-stu-id="ceac9-131">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="ceac9-132">S každým požadavkem obslužnou rutinu zajistí, že certifikát, který byl platný, když se nepředal nevypršela platnost během jeho aktuální relaci.</span><span class="sxs-lookup"><span data-stu-id="ceac9-132">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="ceac9-133">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="ceac9-133">RevocationFlag</span></span>

<span data-ttu-id="ceac9-134">Příznak, který určuje, které certifikáty v řetězu kontroluje odvolání.</span><span class="sxs-lookup"><span data-stu-id="ceac9-134">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="ceac9-135">Kontroly odvolání jsou prováděny pouze při certifikát zřetězil s kořenovým certifikátem.</span><span class="sxs-lookup"><span data-stu-id="ceac9-135">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="ceac9-136">revocationMode</span><span class="sxs-lookup"><span data-stu-id="ceac9-136">RevocationMode</span></span>

<span data-ttu-id="ceac9-137">Příznak, který určuje, jak se provádí kontroly odvolání.</span><span class="sxs-lookup"><span data-stu-id="ceac9-137">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="ceac9-138">Určení online kontrola může způsobit dlouhé zpoždění při kontaktování certifikační autority.</span><span class="sxs-lookup"><span data-stu-id="ceac9-138">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="ceac9-139">Kontroly odvolání jsou prováděny pouze při certifikát zřetězil s kořenovým certifikátem.</span><span class="sxs-lookup"><span data-stu-id="ceac9-139">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="ceac9-140">Můžete nakonfigurovat aplikaci tak, aby vyžadovala certifikát pouze na určité cesty ke složkám?</span><span class="sxs-lookup"><span data-stu-id="ceac9-140">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="ceac9-141">To není možné.</span><span class="sxs-lookup"><span data-stu-id="ceac9-141">This isn't possible.</span></span> <span data-ttu-id="ceac9-142">Mějte na paměti, že certifikát výměny se provádí, že začátek konverzace HTTPS, probíhá server před první žádost o přijetí na toto připojení, není možné obor podle jakékoli pole požadavku.</span><span class="sxs-lookup"><span data-stu-id="ceac9-142">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="ceac9-143">Obslužná rutina události</span><span class="sxs-lookup"><span data-stu-id="ceac9-143">Handler events</span></span>

<span data-ttu-id="ceac9-144">Obslužná rutina má dvě události:</span><span class="sxs-lookup"><span data-stu-id="ceac9-144">The handler has two events:</span></span>

* <span data-ttu-id="ceac9-145">`OnAuthenticationFailed` &ndash; Volá se, pokud výjimka se stane při ověřování a umožňuje reagovat.</span><span class="sxs-lookup"><span data-stu-id="ceac9-145">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="ceac9-146">`OnCertificateValidated` &ndash; Volá se po certifikát byl ověřen, zdařilo ověření a vytvoří výchozí objekt se vytvořil.</span><span class="sxs-lookup"><span data-stu-id="ceac9-146">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="ceac9-147">Tato událost vám umožňuje provést vlastní ověřování a rozšířit nebo nahradit objekt zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="ceac9-147">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="ceac9-148">Příklady zahrnují:</span><span class="sxs-lookup"><span data-stu-id="ceac9-148">For examples include:</span></span>
  * <span data-ttu-id="ceac9-149">Určení, pokud tento certifikát se známými k vašim službám.</span><span class="sxs-lookup"><span data-stu-id="ceac9-149">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="ceac9-150">Vytváření vlastních instanční objekt.</span><span class="sxs-lookup"><span data-stu-id="ceac9-150">Constructing your own principal.</span></span> <span data-ttu-id="ceac9-151">Prohlédněte si následující příklad na `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ceac9-151">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceac9-152">Pokud zjistíte příchozí certifikát nesplňuje vaše ověřování navíc, volání `context.Fail("failure reason")` s důvodem selhání.</span><span class="sxs-lookup"><span data-stu-id="ceac9-152">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="ceac9-153">Pro skutečné funkce, budete pravděpodobně chtít volání služby zaregistrovaný v injektáž závislostí, která se připojuje k databázi nebo jiný typ úložiště uživatele.</span><span class="sxs-lookup"><span data-stu-id="ceac9-153">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="ceac9-154">Přístup ke službě s použitím kontextu předané do delegáta.</span><span class="sxs-lookup"><span data-stu-id="ceac9-154">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="ceac9-155">Prohlédněte si následující příklad na `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ceac9-155">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ceac9-156">Koncepčně ověření certifikátu je autorizace.</span><span class="sxs-lookup"><span data-stu-id="ceac9-156">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="ceac9-157">Přidat kontrolu, například, vydavatele nebo kryptografický otisk v zásady autorizace, ne uvnitř `OnCertificateValidated`, je zcela přijatelné.</span><span class="sxs-lookup"><span data-stu-id="ceac9-157">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="ceac9-158">Nakonfigurujte hostitele tak, aby vyžadovala certifikáty</span><span class="sxs-lookup"><span data-stu-id="ceac9-158">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="ceac9-159">Kestrel</span><span class="sxs-lookup"><span data-stu-id="ceac9-159">Kestrel</span></span>

<span data-ttu-id="ceac9-160">V *Program.cs*, nakonfigurujte Kestrel následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="ceac9-160">In *Program.cs*, configure Kestrel as follows:</span></span>

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

### <a name="iis"></a><span data-ttu-id="ceac9-161">IIS</span><span class="sxs-lookup"><span data-stu-id="ceac9-161">IIS</span></span>

<span data-ttu-id="ceac9-162">Proveďte následující kroky ve Správci služby IIS:</span><span class="sxs-lookup"><span data-stu-id="ceac9-162">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="ceac9-163">Vyberte svou lokalitu z **připojení** kartu.</span><span class="sxs-lookup"><span data-stu-id="ceac9-163">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="ceac9-164">Dvakrát klikněte **nastavení SSL** možnost **zobrazení funkcí** okna.</span><span class="sxs-lookup"><span data-stu-id="ceac9-164">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="ceac9-165">Zkontrolujte **požadovat protokol SSL** zaškrtávací políčko a vyberte **vyžadují** přepínač v **klientské certifikáty** oddílu.</span><span class="sxs-lookup"><span data-stu-id="ceac9-165">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Nastavení certifikátu klienta ve službě IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="ceac9-167">Azure a vlastní webové proxy servery</span><span class="sxs-lookup"><span data-stu-id="ceac9-167">Azure and custom web proxies</span></span>

<span data-ttu-id="ceac9-168">Najdete v článku [hostitelství a nasazení dokumentaci](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) jak nakonfigurovat certifikát předávání middlewaru.</span><span class="sxs-lookup"><span data-stu-id="ceac9-168">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
