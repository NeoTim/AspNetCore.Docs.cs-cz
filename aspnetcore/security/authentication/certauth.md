---
title: Konfigurace ověřování certifikátů v ASP.NET Core
author: blowdart
description: Přečtěte si, jak nakonfigurovat ověřování certifikátů v ASP.NET Core pro IIS a HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: 57d46e34993148943b1e9680a372405be9c80605
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424201"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="e5d9a-103">Konfigurace ověřování certifikátů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5d9a-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="e5d9a-104">`Microsoft.AspNetCore.Authentication.Certificate` obsahuje implementaci podobnou [ověřování certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="e5d9a-105">Ověřování certifikátu se provádí na úrovni protokolu TLS dlouho předtím, než se někdy získá ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="e5d9a-106">Přesněji platí, že se jedná o obslužnou rutinu ověřování, která certifikát ověřuje, a pak poskytuje událost, kde můžete tento certifikát vyřešit na `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="e5d9a-107">[Nakonfigurujte server](#configure-your-server-to-require-certificates) pro ověřování pomocí certifikátu, jako IIS, Kestrel, Azure Web Apps nebo cokoli jiného, co používáte.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-107">[Configure your server](#configure-your-server-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="e5d9a-108">Scénáře proxy a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="e5d9a-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="e5d9a-109">Ověřování certifikátů je stavový scénář, který se primárně používá, když proxy nebo nástroj pro vyrovnávání zatížení nezpracovává provoz mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="e5d9a-110">Pokud se používá proxy server nebo nástroj pro vyrovnávání zatížení, funguje ověřování certifikátů pouze v případě, že proxy nebo nástroj pro vyrovnávání zatížení:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="e5d9a-111">Zpracovává ověřování.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-111">Handles the authentication.</span></span>
* <span data-ttu-id="e5d9a-112">Předá do aplikace informace o ověřování uživatele (například v hlavičce požadavku), která funguje na ověřovacích informacích.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="e5d9a-113">Alternativou k ověřování certifikátů v prostředích, kde se používají proxy a nástroje pro vyrovnávání zatížení, je služba AD FS (Active Directory federovaných služeb) se službou OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="e5d9a-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="e5d9a-114">Začínáme</span><span class="sxs-lookup"><span data-stu-id="e5d9a-114">Get started</span></span>

<span data-ttu-id="e5d9a-115">Získejte certifikát HTTPS, použijte ho a [nakonfigurujte server](#configure-your-server-to-require-certificates) tak, aby vyžadoval certifikáty.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-115">Acquire an HTTPS certificate, apply it, and [configure your server](#configure-your-server-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="e5d9a-116">Do webové aplikace přidejte odkaz na balíček [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-116">In your web app, add a reference to the [Microsoft.AspNetCore.Authentication.Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) package.</span></span> <span data-ttu-id="e5d9a-117">Potom v `Startup.ConfigureServices` metodě zavolejte `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` s vašimi možnostmi a poskytněte delegátovi, `OnCertificateValidated` aby provedl dodatečné ověřování klientského certifikátu odeslaného pomocí požadavků.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="e5d9a-118">Zapněte tyto informace do `ClaimsPrincipal` a nastavte ji na `context.Principal` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="e5d9a-119">Pokud se ověření nepovede, vrátí tato obslužná rutina `403 (Forbidden)` odpověď místo `401 (Unauthorized)` toho, jak byste mohli očekávat.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="e5d9a-120">Důvodem je, že při počátečním připojení TLS by mělo probíhat ověřování.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="e5d9a-121">V době, kdy dosáhne obslužné rutiny, je příliš pozdě.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="e5d9a-122">Neexistuje žádný způsob, jak upgradovat připojení z anonymního připojení k jednomu pomocí certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="e5d9a-123">Přidejte také `app.UseAuthentication();` do `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="e5d9a-124">V opačném případě nebude `HttpContext.User` nastavení `ClaimsPrincipal` vytvořeno z certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="e5d9a-125">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-125">For example:</span></span>

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

<span data-ttu-id="e5d9a-126">Předchozí příklad ukazuje výchozí způsob, jak přidat ověřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="e5d9a-127">Obslužná rutina vytvoří objekt zabezpečení uživatele pomocí vlastností Common Certificate.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="e5d9a-128">Konfigurace ověření certifikátu</span><span class="sxs-lookup"><span data-stu-id="e5d9a-128">Configure certificate validation</span></span>

<span data-ttu-id="e5d9a-129">`CertificateAuthenticationOptions`Obslužná rutina obsahuje některá Vestavěná ověření, která jsou minimálními ověřeními, které byste měli provést na certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="e5d9a-130">Každé z těchto nastavení je ve výchozím nastavení povoleno.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="e5d9a-131">AllowedCertificateTypes = Chained, SelfSigned nebo All (zřetězené | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="e5d9a-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="e5d9a-132">Výchozí hodnota: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="e5d9a-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="e5d9a-133">Tato kontrola ověří, zda je povolen pouze příslušný typ certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="e5d9a-134">Pokud aplikace používá certifikáty podepsané svým držitelem, musí být tato možnost nastavená na `CertificateTypes.All` nebo `CertificateTypes.SelfSigned` .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="e5d9a-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="e5d9a-135">ValidateCertificateUse</span></span>

<span data-ttu-id="e5d9a-136">Výchozí hodnota: `true`</span><span class="sxs-lookup"><span data-stu-id="e5d9a-136">Default value: `true`</span></span>

<span data-ttu-id="e5d9a-137">Tato kontrola ověří, že certifikát prezentovaný klientem má rozšířené použití klíče (EKU) ověřování klienta (EKU) nebo žádný rozšířená použití klíče.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="e5d9a-138">Pokud se jako specifikace nezadá žádné rozšířené použití klíče, považují se všechny rozšířená použití klíče za platné.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="e5d9a-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="e5d9a-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="e5d9a-140">Výchozí hodnota: `true`</span><span class="sxs-lookup"><span data-stu-id="e5d9a-140">Default value: `true`</span></span>

<span data-ttu-id="e5d9a-141">Tato kontrola ověří, že se certifikát nachází v období platnosti.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="e5d9a-142">U každé žádosti obslužná rutina zajišťuje, že platnost certifikátu, který byl platný při jeho předložení, vypršela během aktuální relace.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="e5d9a-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="e5d9a-143">RevocationFlag</span></span>

<span data-ttu-id="e5d9a-144">Výchozí hodnota: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="e5d9a-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="e5d9a-145">Příznak, který určuje, které certifikáty v řetězci mají být zkontrolovány pro odvolání.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="e5d9a-146">Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="e5d9a-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="e5d9a-147">RevocationMode</span></span>

<span data-ttu-id="e5d9a-148">Výchozí hodnota: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="e5d9a-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="e5d9a-149">Příznak, který určuje, jak se provádí kontroly odvolání.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="e5d9a-150">Zadání online kontroly může mít za následek dlouhou prodlevu při kontaktování certifikační autority.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="e5d9a-151">Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="e5d9a-152">Můžu aplikaci nakonfigurovat tak, aby vyžadovala certifikát jenom na určitých cestách?</span><span class="sxs-lookup"><span data-stu-id="e5d9a-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="e5d9a-153">To není možné.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-153">This isn't possible.</span></span> <span data-ttu-id="e5d9a-154">Zapamatujte si, že výměna certifikátu se dokončila, když se spustí konverzace HTTPS, a to na serveru před přijetím prvního požadavku v tomto připojení, takže není možné ho oborovat na základě jakýchkoli polí požadavků.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="e5d9a-155">Události obslužných rutin</span><span class="sxs-lookup"><span data-stu-id="e5d9a-155">Handler events</span></span>

<span data-ttu-id="e5d9a-156">Obslužná rutina má dvě události:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-156">The handler has two events:</span></span>

* <span data-ttu-id="e5d9a-157">`OnAuthenticationFailed`: Volá se, pokud dojde k výjimce během ověřování a umožňuje reagovat.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-157">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="e5d9a-158">`OnCertificateValidated`: Voláno po ověření certifikátu, úspěšné ověření a výchozí objekt zabezpečení byl vytvořen.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-158">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="e5d9a-159">Tato událost umožňuje provádět vlastní ověřování a rozšíření nebo nahrazení objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="e5d9a-160">Příklady zahrnují:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-160">For examples include:</span></span>
  * <span data-ttu-id="e5d9a-161">Určení, jestli se pro vaše služby ví certifikát.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="e5d9a-162">Sestavování vlastního objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-162">Constructing your own principal.</span></span> <span data-ttu-id="e5d9a-163">Vezměte v úvahu následující příklad `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e5d9a-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e5d9a-164">Pokud zjistíte, že příchozí certifikát nesplňuje vaše dodatečné ověření, zavolejte `context.Fail("failure reason")` důvod selhání.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="e5d9a-165">Pro reálné funkce pravděpodobně budete chtít volat službu registrovanou v injektáže závislosti, který se připojuje k databázi nebo jinému typu úložiště uživatele.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="e5d9a-166">Ke službě získáte přístup pomocí kontextu předaného do vašeho delegáta.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="e5d9a-167">Vezměte v úvahu následující příklad `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e5d9a-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

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
                        .GetRequiredService<ICertificateValidationService>();
                
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

<span data-ttu-id="e5d9a-168">V koncepčním případě je ověření certifikátu v takovém případě oprávnění.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="e5d9a-169">Přidání kontroly, například vystavitele nebo kryptografického otisku v zásadách autorizace, nikoli uvnitř `OnCertificateValidated` , je naprosto přijatelné.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-server-to-require-certificates"></a><span data-ttu-id="e5d9a-170">Konfigurace serveru tak, aby vyžadoval certifikáty</span><span class="sxs-lookup"><span data-stu-id="e5d9a-170">Configure your server to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="e5d9a-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e5d9a-171">Kestrel</span></span>

<span data-ttu-id="e5d9a-172">V *program.cs*nakonfigurujte Kestrel následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-172">In *Program.cs*, configure Kestrel as follows:</span></span>

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
> <span data-ttu-id="e5d9a-173">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="e5d9a-174">IIS</span><span class="sxs-lookup"><span data-stu-id="e5d9a-174">IIS</span></span>

<span data-ttu-id="e5d9a-175">Ve Správci služby IIS proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="e5d9a-176">Na kartě **připojení** vyberte svou lokalitu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="e5d9a-177">Dvakrát klikněte na možnost **Nastavení SSL** v okně **zobrazení funkcí** .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="e5d9a-178">Zaškrtněte políčko **vyžadovat protokol SSL** a v části **certifikáty klienta** vyberte tlačítko **vyžadovat** přepínač.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Nastavení klientského certifikátu ve službě IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="e5d9a-180">Azure a vlastní webové proxy servery</span><span class="sxs-lookup"><span data-stu-id="e5d9a-180">Azure and custom web proxies</span></span>

<span data-ttu-id="e5d9a-181">Postup konfigurace middlewaru pro předávání certifikátů najdete v [dokumentaci k hostiteli a nasazení](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="e5d9a-182">Použití ověřování certifikátů v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="e5d9a-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="e5d9a-183">Pro Azure se nevyžaduje žádná konfigurace předávání.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="e5d9a-184">Toto je již nastaveno v middlewari předávání certifikátů.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="e5d9a-185">To vyžaduje, aby byl přítomen CertificateForwardingMiddleware.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="e5d9a-186">Použití ověřování certifikátů ve vlastních webových proxy serverech</span><span class="sxs-lookup"><span data-stu-id="e5d9a-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="e5d9a-187">`AddCertificateForwarding`Metoda slouží k zadání:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="e5d9a-188">Název hlavičky klienta.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-188">The client header name.</span></span>
* <span data-ttu-id="e5d9a-189">Způsob načtení certifikátu (pomocí `HeaderConverter` Vlastnosti).</span><span class="sxs-lookup"><span data-stu-id="e5d9a-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="e5d9a-190">Ve vlastních webových proxy serverech se certifikát předává jako vlastní Hlavička požadavku, například `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="e5d9a-191">Pokud ho chcete použít, nakonfigurujte předávání certifikátů v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e5d9a-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e5d9a-192">`Startup.Configure`Metoda pak přidá middleware.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="e5d9a-193">`UseCertificateForwarding` je volána před voláním `UseAuthentication` a `UseAuthorization` :</span><span class="sxs-lookup"><span data-stu-id="e5d9a-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

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

<span data-ttu-id="e5d9a-194">Samostatnou třídu lze použít k implementaci logiky ověřování.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="e5d9a-195">Protože v tomto příkladu se používá stejný certifikát podepsaný svým držitelem, ujistěte se, že se dá použít jenom váš certifikát.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="e5d9a-196">Ověřte, že se neshodují kryptografické otisky certifikátu klienta i certifikátu serveru. v opačném případě se dá použít libovolný certifikát, který bude pro ověření stačit.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="e5d9a-197">Tato metoda by se použila uvnitř `AddCertificate` metody.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="e5d9a-198">V případě použití zprostředkujících nebo podřízených certifikátů můžete také ověřit předmět nebo vystavitele.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="e5d9a-199">Implementace HttpClient pomocí certifikátu a HttpClientHandler</span><span class="sxs-lookup"><span data-stu-id="e5d9a-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="e5d9a-200">`HttpClientHandler`Lze přidat přímo v konstruktoru `HttpClient` třídy.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-200">The `HttpClientHandler` could be added directly in the constructor of the `HttpClient` class.</span></span> <span data-ttu-id="e5d9a-201">Při vytváření instancí nástroje byste měli věnovat pozornost `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-201">Care should be taken when creating instances of the `HttpClient`.</span></span> <span data-ttu-id="e5d9a-202">`HttpClient`Pak certifikát odešle spolu s každou žádostí.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-202">The `HttpClient` will then send the certificate with each request.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="e5d9a-203">Implementace HttpClient pomocí certifikátu a pojmenovaného HttpClient z IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e5d9a-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="e5d9a-204">V následujícím příkladu je klientský certifikát přidán do a `HttpClientHandler` pomocí `ClientCertificates` vlastnosti z obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-204">In the following example, a client certificate is added to a `HttpClientHandler` using the `ClientCertificates` property from the handler.</span></span> <span data-ttu-id="e5d9a-205">Tato obslužná rutina se pak dá použít v pojmenované instanci `HttpClient` pomocí `ConfigurePrimaryHttpMessageHandler` metody.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-205">This handler can then be used in a named instance of an `HttpClient` using the `ConfigurePrimaryHttpMessageHandler` method.</span></span> <span data-ttu-id="e5d9a-206">Toto nastavení se provádí v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e5d9a-206">This is setup in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e5d9a-207">`IHttpClientFactory`Lze pak použít k získání pojmenované instance s obslužnou rutinou a certifikátem.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-207">The `IHttpClientFactory` can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="e5d9a-208">`CreateClient`Metoda s názvem klienta definovaného ve `Startup` třídě slouží k získání instance.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-208">The `CreateClient` method with the name of the client defined in the `Startup` class is used to get the instance.</span></span> <span data-ttu-id="e5d9a-209">Požadavek HTTP lze odeslat pomocí klienta podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-209">The HTTP request can be sent using the client as required.</span></span>

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

<span data-ttu-id="e5d9a-210">Pokud se do serveru pošle správný certifikát, vrátí se data.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="e5d9a-211">Pokud se nepošle žádný certifikát nebo nesprávný certifikát, vrátí se stavový kód HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="e5d9a-212">Vytvoření certifikátů v PowerShellu</span><span class="sxs-lookup"><span data-stu-id="e5d9a-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="e5d9a-213">Vytvoření certifikátů je nejzávažnou součástí nastavení tohoto toku.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="e5d9a-214">Kořenový certifikát se dá vytvořit pomocí `New-SelfSignedCertificate` rutiny PowerShellu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="e5d9a-215">Při vytváření certifikátu použijte silné heslo.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="e5d9a-216">Je důležité přidat `KeyUsageProperty` parametr a `KeyUsage` parametr, jak je znázorněno.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="e5d9a-217">Vytvořit kořenovou certifikační autoritu</span><span class="sxs-lookup"><span data-stu-id="e5d9a-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="e5d9a-218">`-DnsName`Hodnota parametru musí odpovídat cíli nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="e5d9a-219">Například "localhost" pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="e5d9a-220">Nainstalovat do důvěryhodného kořenového adresáře</span><span class="sxs-lookup"><span data-stu-id="e5d9a-220">Install in the trusted root</span></span>

<span data-ttu-id="e5d9a-221">Kořenový certifikát musí být v hostitelském systému důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="e5d9a-222">Kořenový certifikát, který nebyl vytvořen certifikační autoritou, nebude ve výchozím nastavení považován za důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="e5d9a-223">Následující odkaz vysvětluje, jak to lze provést ve Windows:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="e5d9a-224">Zprostředkující certifikát</span><span class="sxs-lookup"><span data-stu-id="e5d9a-224">Intermediate certificate</span></span>

<span data-ttu-id="e5d9a-225">Zprostředkující certifikát se teď dá vytvořit z kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="e5d9a-226">To není vyžadováno pro všechny případy použití, ale možná budete muset vytvořit mnoho certifikátů nebo musíte aktivovat nebo zakázat skupiny certifikátů.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="e5d9a-227">`TextExtension`Parametr je vyžadován pro nastavení délky cesty v základních omezeních certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="e5d9a-228">Zprostředkující certifikát je pak možné přidat do důvěryhodného zprostředkujícího certifikátu v hostitelském systému Windows.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="e5d9a-229">Vytvořit podřízený certifikát z zprostředkujícího certifikátu</span><span class="sxs-lookup"><span data-stu-id="e5d9a-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="e5d9a-230">Z zprostředkujícího certifikátu se dá vytvořit podřízený certifikát.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="e5d9a-231">Toto je koncová entita a není nutné vytvářet další podřízené certifikáty.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="e5d9a-232">Vytvořit podřízený certifikát z kořenového certifikátu</span><span class="sxs-lookup"><span data-stu-id="e5d9a-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="e5d9a-233">Podřízený certifikát lze také vytvořit přímo z kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="e5d9a-234">Příklad certifikátu root-zprostředkující certifikát – certifikát</span><span class="sxs-lookup"><span data-stu-id="e5d9a-234">Example root - intermediate certificate - certificate</span></span>

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

<span data-ttu-id="e5d9a-235">Při použití kořenových, zprostředkujících nebo podřízených certifikátů je možné certifikáty ověřit pomocí kryptografického otisku nebo PublicKey podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

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

## <a name="certificate-validation-caching"></a><span data-ttu-id="e5d9a-236">Mezipaměť ověření certifikátu</span><span class="sxs-lookup"><span data-stu-id="e5d9a-236">Certificate validation caching</span></span>

<span data-ttu-id="e5d9a-237">ASP.NET Core 5,0 a novější verze podporují možnost Povolit ukládání výsledků ověření do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-237">ASP.NET Core 5.0 and later versions support the ability to enable caching of validation results.</span></span> <span data-ttu-id="e5d9a-238">Ukládání do mezipaměti výrazně zvyšuje výkon ověřování certifikátem, protože ověřování je náročná operace.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-238">The caching dramatically improves performance of certificate authentication, as validation is an expensive operation.</span></span>

<span data-ttu-id="e5d9a-239">Ve výchozím nastavení ověřování pomocí certifikátu zakáže ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-239">By default, certificate authentication disables caching.</span></span> <span data-ttu-id="e5d9a-240">Pokud chcete povolit ukládání do mezipaměti, zavolejte `AddCertificateCache` na `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e5d9a-240">To enable caching, call `AddCertificateCache` in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e5d9a-241">Výchozí implementace ukládání do mezipaměti ukládá výsledky do paměti.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-241">The default caching implementation stores results in memory.</span></span> <span data-ttu-id="e5d9a-242">Můžete poskytnout vlastní mezipaměť implementací `ICertificateValidationCache` a registrací pomocí injektáže závislosti.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-242">You can provide your own cache by implementing `ICertificateValidationCache` and registering it with dependency injection.</span></span> <span data-ttu-id="e5d9a-243">Například, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-243">For example, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span></span>

::: moniker-end

## <a name="optional-client-certificates"></a><span data-ttu-id="e5d9a-244">Volitelné klientské certifikáty</span><span class="sxs-lookup"><span data-stu-id="e5d9a-244">Optional client certificates</span></span>

<span data-ttu-id="e5d9a-245">V této části najdete informace o aplikacích, které musí chránit podmnožinu aplikace pomocí certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-245">This section provides information for apps that must protect a subset of the app with a certificate.</span></span> <span data-ttu-id="e5d9a-246">Například Razor Stránka nebo kontrolér v aplikaci může vyžadovat klientské certifikáty.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-246">For example, a Razor Page or controller in the app might require client certificates.</span></span> <span data-ttu-id="e5d9a-247">V takovém případě se zobrazí výzvy jako klientské certifikáty:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-247">This presents challenges as client certificates:</span></span>
  
* <span data-ttu-id="e5d9a-248">Funkce TLS, nikoli funkce HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-248">Are a TLS feature, not an HTTP feature.</span></span>
* <span data-ttu-id="e5d9a-249">Jsou vyjednány po připojení a musí se vyjednávat na začátku připojení, než budou k dispozici všechna data HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-249">Are negotiated per-connection and must be be negotiated at the start of the connection before any HTTP data is available.</span></span> <span data-ttu-id="e5d9a-250">Na začátku připojení je známo pouze Indikace názvu serveru (SNI) &dagger; .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-250">At the start of the connection, only the Server Name Indication (SNI)&dagger; is known.</span></span> <span data-ttu-id="e5d9a-251">Certifikáty klienta a serveru se vyjednávají před prvním požadavkem na připojení a požadavky obecně není možné znovu vyjednávat.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-251">The client and server certificates are negotiated prior to the first request on a connection and requests generally aren't able to renegotiate.</span></span>

<span data-ttu-id="e5d9a-252">Nové vyjednávání protokolu TLS bylo staré způsob implementace volitelných klientských certifikátů.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-252">TLS renegotiation was an old way to implement optional client certificates.</span></span> <span data-ttu-id="e5d9a-253">Už to nedoporučujeme, protože:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-253">This is no longer recommended because:</span></span>
- <span data-ttu-id="e5d9a-254">V HTTP/1.1 by nové vyjednávání během žádosti POST mohlo způsobit zablokování, kde text žádosti vyplnil okno TCP a pakety opětovného vyjednávání nejde přijmout.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-254">In HTTP/1.1, renegotiating during a POST request could cause a deadlock where the request body filled up the TCP window and the renegotiation packets can't be received.</span></span>
- <span data-ttu-id="e5d9a-255">HTTP/2 [explicitně zakazuje](https://tools.ietf.org/html/rfc7540#section-9.2.1) opakované vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-255">HTTP/2 [explicitly prohibits](https://tools.ietf.org/html/rfc7540#section-9.2.1) renegotiation.</span></span>
- <span data-ttu-id="e5d9a-256">Protokol TLS 1,3 [odebral](https://tools.ietf.org/html/rfc8740#section-1) podporu pro opětovné vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-256">TLS 1.3 has [removed](https://tools.ietf.org/html/rfc8740#section-1) support for renegotiation.</span></span>

<span data-ttu-id="e5d9a-257">ASP.NET Core 5 Preview 7 a novější přináší pohodlnější podporu pro volitelné klientské certifikáty.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-257">ASP.NET Core 5 preview 7 and later adds more convenient support for optional client certificates.</span></span> <span data-ttu-id="e5d9a-258">Další informace najdete v [ukázce volitelných certifikátů](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span><span class="sxs-lookup"><span data-stu-id="e5d9a-258">For more information, see the [Optional certificates sample](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span></span>

<span data-ttu-id="e5d9a-259">Následující přístup podporuje volitelné klientské certifikáty:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-259">The following approach supports optional client certificates:</span></span>

* <span data-ttu-id="e5d9a-260">Nastavte vazbu pro doménu a subdoménu:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-260">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="e5d9a-261">Například nastavte vazby v `contoso.com` a `myClient.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-261">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="e5d9a-262">`contoso.com`Hostitel nevyžaduje klientský certifikát `myClient.contoso.com` , ale má.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-262">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="e5d9a-263">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-263">For more information, see:</span></span>
    * <span data-ttu-id="e5d9a-264">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="e5d9a-264">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="e5d9a-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="e5d9a-265">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="e5d9a-266">Poznámka Kestrel v současné době nepodporuje u jedné vazby více konfigurací TLS, budete potřebovat dvě vazby s jedinečnými IP adresami nebo porty.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-266">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="e5d9a-267">Viz https://github.com/dotnet/runtime/issues/31097.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-267">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="e5d9a-268">IIS</span><span class="sxs-lookup"><span data-stu-id="e5d9a-268">IIS</span></span>
      * [<span data-ttu-id="e5d9a-269">Hostování služby IIS</span><span class="sxs-lookup"><span data-stu-id="e5d9a-269">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="e5d9a-270">Konfigurace zabezpečení služby IIS</span><span class="sxs-lookup"><span data-stu-id="e5d9a-270">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="e5d9a-271">Http.Sys: [Konfigurace Windows serveru](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="e5d9a-271">Http.Sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>
* <span data-ttu-id="e5d9a-272">Pro požadavky na webovou aplikaci, která vyžaduje klientský certifikát, a žádný z nich:</span><span class="sxs-lookup"><span data-stu-id="e5d9a-272">For requests to the web app that require a client certificate and don't have one:</span></span>
  * <span data-ttu-id="e5d9a-273">Přesměruje se na stejnou stránku pomocí subdomény chráněné klientským certifikátem.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-273">Redirect to the same page using the client certificate protected subdomain.</span></span>
  * <span data-ttu-id="e5d9a-274">Například přesměrujte na `myClient.contoso.com/requestedPage` .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-274">For example, redirect to `myClient.contoso.com/requestedPage`.</span></span> <span data-ttu-id="e5d9a-275">Vzhledem k tomu, že se `myClient.contoso.com/requestedPage` jedná o jiný název hostitele než `contoso.com/requestedPage` , klient vytvoří jiné připojení a klientský certifikát je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-275">Because the request to `myClient.contoso.com/requestedPage` is a different hostname than `contoso.com/requestedPage`, the client establishes a different connection and the client certificate is provided.</span></span>
  * <span data-ttu-id="e5d9a-276">Další informace naleznete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-276">For more information, see <xref:security/authorization/introduction>.</span></span>

<span data-ttu-id="e5d9a-277">Ponechte dotazy, komentáře a další zpětnou vazbu k volitelným klientským certifikátům v [tomto problému diskuze GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/18720) .</span><span class="sxs-lookup"><span data-stu-id="e5d9a-277">Leave questions, comments, and other feedback on optional client certificates in [this GitHub discussion](https://github.com/dotnet/AspNetCore.Docs/issues/18720) issue.</span></span>

<span data-ttu-id="e5d9a-278">&dagger; Indikace názvu serveru (SNI) je rozšíření TLS, které zahrnuje virtuální doménu jako součást vyjednávání SSL.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-278">&dagger; Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="e5d9a-279">To efektivně znamená, že k identifikaci koncového bodu sítě lze použít název virtuální domény nebo název hostitele.</span><span class="sxs-lookup"><span data-stu-id="e5d9a-279">This effectively means the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>
