---
<span data-ttu-id="d8878-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8878-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8878-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8878-102">'Blazor'</span></span>
- <span data-ttu-id="d8878-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8878-103">'Identity'</span></span>
- <span data-ttu-id="d8878-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8878-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8878-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8878-105">'Razor'</span></span>
- <span data-ttu-id="d8878-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8878-106">'SignalR' uid:</span></span> 

---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="d8878-107">Konfigurace ověřování certifikátů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8878-107">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="d8878-108">`Microsoft.AspNetCore.Authentication.Certificate`obsahuje implementaci podobnou [ověřování certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8878-108">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="d8878-109">Ověřování certifikátu se provádí na úrovni protokolu TLS dlouho předtím, než se někdy získá ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8878-109">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="d8878-110">Přesněji platí, že se jedná o obslužnou rutinu ověřování, která certifikát ověřuje, a pak poskytuje událost, kde můžete tento certifikát vyřešit na `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="d8878-110">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="d8878-111">[Nakonfigurujte hostitele](#configure-your-host-to-require-certificates) pro ověřování certifikátů, jako IIS, Kestrel, Azure Web Apps nebo cokoli jiného, co používáte.</span><span class="sxs-lookup"><span data-stu-id="d8878-111">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="d8878-112">Scénáře proxy a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d8878-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="d8878-113">Ověřování certifikátů je stavový scénář, který se primárně používá, když proxy nebo nástroj pro vyrovnávání zatížení nezpracovává provoz mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="d8878-113">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="d8878-114">Pokud se používá proxy server nebo nástroj pro vyrovnávání zatížení, funguje ověřování certifikátů pouze v případě, že proxy nebo nástroj pro vyrovnávání zatížení:</span><span class="sxs-lookup"><span data-stu-id="d8878-114">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="d8878-115">Zpracovává ověřování.</span><span class="sxs-lookup"><span data-stu-id="d8878-115">Handles the authentication.</span></span>
* <span data-ttu-id="d8878-116">Předá do aplikace informace o ověřování uživatele (například v hlavičce požadavku), která funguje na ověřovacích informacích.</span><span class="sxs-lookup"><span data-stu-id="d8878-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="d8878-117">Alternativou k ověřování certifikátů v prostředích, kde se používají proxy a nástroje pro vyrovnávání zatížení, je služba AD FS (Active Directory federovaných služeb) se službou OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="d8878-117">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="d8878-118">Začínáme</span><span class="sxs-lookup"><span data-stu-id="d8878-118">Get started</span></span>

<span data-ttu-id="d8878-119">Získejte certifikát HTTPS, použijte ho a [Nakonfigurujte hostitele](#configure-your-host-to-require-certificates) tak, aby vyžadoval certifikáty.</span><span class="sxs-lookup"><span data-stu-id="d8878-119">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="d8878-120">Do webové aplikace přidejte odkaz na `Microsoft.AspNetCore.Authentication.Certificate` balíček.</span><span class="sxs-lookup"><span data-stu-id="d8878-120">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="d8878-121">Potom v `Startup.ConfigureServices` metodě zavolejte `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` s vašimi možnostmi a poskytněte delegátovi, `OnCertificateValidated` aby provedl dodatečné ověřování klientského certifikátu odeslaného pomocí požadavků.</span><span class="sxs-lookup"><span data-stu-id="d8878-121">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="d8878-122">Zapněte tyto informace do `ClaimsPrincipal` a nastavte ji na `context.Principal` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="d8878-122">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="d8878-123">Pokud se ověření nepovede, vrátí tato obslužná rutina `403 (Forbidden)` odpověď místo `401 (Unauthorized)` toho, jak byste mohli očekávat.</span><span class="sxs-lookup"><span data-stu-id="d8878-123">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="d8878-124">Důvodem je, že při počátečním připojení TLS by mělo probíhat ověřování.</span><span class="sxs-lookup"><span data-stu-id="d8878-124">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="d8878-125">V době, kdy dosáhne obslužné rutiny, je příliš pozdě.</span><span class="sxs-lookup"><span data-stu-id="d8878-125">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="d8878-126">Neexistuje žádný způsob, jak upgradovat připojení z anonymního připojení k jednomu pomocí certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-126">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="d8878-127">Přidejte také `app.UseAuthentication();` do `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="d8878-127">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="d8878-128">V opačném případě nebude `HttpContext.User` nastavení `ClaimsPrincipal` vytvořeno z certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-128">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="d8878-129">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d8878-129">For example:</span></span>

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

<span data-ttu-id="d8878-130">Předchozí příklad ukazuje výchozí způsob, jak přidat ověřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-130">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="d8878-131">Obslužná rutina vytvoří objekt zabezpečení uživatele pomocí vlastností Common Certificate.</span><span class="sxs-lookup"><span data-stu-id="d8878-131">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="d8878-132">Konfigurace ověření certifikátu</span><span class="sxs-lookup"><span data-stu-id="d8878-132">Configure certificate validation</span></span>

<span data-ttu-id="d8878-133">`CertificateAuthenticationOptions`Obslužná rutina obsahuje některá Vestavěná ověření, která jsou minimálními ověřeními, které byste měli provést na certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-133">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="d8878-134">Každé z těchto nastavení je ve výchozím nastavení povoleno.</span><span class="sxs-lookup"><span data-stu-id="d8878-134">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="d8878-135">AllowedCertificateTypes = Chained, SelfSigned nebo All (zřetězené | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="d8878-135">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="d8878-136">Výchozí hodnota:`CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="d8878-136">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="d8878-137">Tato kontrola ověří, zda je povolen pouze příslušný typ certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-137">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="d8878-138">Pokud aplikace používá certifikáty podepsané svým držitelem, musí být tato možnost nastavená na `CertificateTypes.All` nebo `CertificateTypes.SelfSigned` .</span><span class="sxs-lookup"><span data-stu-id="d8878-138">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="d8878-139">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="d8878-139">ValidateCertificateUse</span></span>

<span data-ttu-id="d8878-140">Výchozí hodnota:`true`</span><span class="sxs-lookup"><span data-stu-id="d8878-140">Default value: `true`</span></span>

<span data-ttu-id="d8878-141">Tato kontrola ověří, že certifikát prezentovaný klientem má rozšířené použití klíče (EKU) ověřování klienta (EKU) nebo žádný rozšířená použití klíče.</span><span class="sxs-lookup"><span data-stu-id="d8878-141">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="d8878-142">Pokud se jako specifikace nezadá žádné rozšířené použití klíče, považují se všechny rozšířená použití klíče za platné.</span><span class="sxs-lookup"><span data-stu-id="d8878-142">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="d8878-143">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="d8878-143">ValidateValidityPeriod</span></span>

<span data-ttu-id="d8878-144">Výchozí hodnota:`true`</span><span class="sxs-lookup"><span data-stu-id="d8878-144">Default value: `true`</span></span>

<span data-ttu-id="d8878-145">Tato kontrola ověří, že se certifikát nachází v období platnosti.</span><span class="sxs-lookup"><span data-stu-id="d8878-145">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="d8878-146">U každé žádosti obslužná rutina zajišťuje, že platnost certifikátu, který byl platný při jeho předložení, vypršela během aktuální relace.</span><span class="sxs-lookup"><span data-stu-id="d8878-146">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="d8878-147">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="d8878-147">RevocationFlag</span></span>

<span data-ttu-id="d8878-148">Výchozí hodnota:`X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="d8878-148">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="d8878-149">Příznak, který určuje, které certifikáty v řetězci mají být zkontrolovány pro odvolání.</span><span class="sxs-lookup"><span data-stu-id="d8878-149">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="d8878-150">Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-150">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="d8878-151">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="d8878-151">RevocationMode</span></span>

<span data-ttu-id="d8878-152">Výchozí hodnota:`X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="d8878-152">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="d8878-153">Příznak, který určuje, jak se provádí kontroly odvolání.</span><span class="sxs-lookup"><span data-stu-id="d8878-153">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="d8878-154">Zadání online kontroly může mít za následek dlouhou prodlevu při kontaktování certifikační autority.</span><span class="sxs-lookup"><span data-stu-id="d8878-154">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="d8878-155">Kontroly odvolání se provádějí jenom v případě, že je certifikát zřetězený do kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-155">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="d8878-156">Můžu aplikaci nakonfigurovat tak, aby vyžadovala certifikát jenom na určitých cestách?</span><span class="sxs-lookup"><span data-stu-id="d8878-156">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="d8878-157">To není možné.</span><span class="sxs-lookup"><span data-stu-id="d8878-157">This isn't possible.</span></span> <span data-ttu-id="d8878-158">Zapamatujte si, že výměna certifikátu se dokončila, když se spustí konverzace HTTPS, a to na serveru před přijetím prvního požadavku v tomto připojení, takže není možné ho oborovat na základě jakýchkoli polí požadavků.</span><span class="sxs-lookup"><span data-stu-id="d8878-158">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="d8878-159">Události obslužných rutin</span><span class="sxs-lookup"><span data-stu-id="d8878-159">Handler events</span></span>

<span data-ttu-id="d8878-160">Obslužná rutina má dvě události:</span><span class="sxs-lookup"><span data-stu-id="d8878-160">The handler has two events:</span></span>

* <span data-ttu-id="d8878-161">`OnAuthenticationFailed`: Volá se, pokud dojde k výjimce během ověřování a umožňuje reagovat.</span><span class="sxs-lookup"><span data-stu-id="d8878-161">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="d8878-162">`OnCertificateValidated`: Voláno po ověření certifikátu, úspěšné ověření a výchozí objekt zabezpečení byl vytvořen.</span><span class="sxs-lookup"><span data-stu-id="d8878-162">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="d8878-163">Tato událost umožňuje provádět vlastní ověřování a rozšíření nebo nahrazení objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="d8878-163">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="d8878-164">Příklady zahrnují:</span><span class="sxs-lookup"><span data-stu-id="d8878-164">For examples include:</span></span>
  * <span data-ttu-id="d8878-165">Určení, jestli se pro vaše služby ví certifikát.</span><span class="sxs-lookup"><span data-stu-id="d8878-165">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="d8878-166">Sestavování vlastního objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="d8878-166">Constructing your own principal.</span></span> <span data-ttu-id="d8878-167">Vezměte v úvahu následující příklad `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d8878-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="d8878-168">Pokud zjistíte, že příchozí certifikát nesplňuje vaše dodatečné ověření, zavolejte `context.Fail("failure reason")` důvod selhání.</span><span class="sxs-lookup"><span data-stu-id="d8878-168">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="d8878-169">Pro reálné funkce pravděpodobně budete chtít volat službu registrovanou v injektáže závislosti, který se připojuje k databázi nebo jinému typu úložiště uživatele.</span><span class="sxs-lookup"><span data-stu-id="d8878-169">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="d8878-170">Ke službě získáte přístup pomocí kontextu předaného do vašeho delegáta.</span><span class="sxs-lookup"><span data-stu-id="d8878-170">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="d8878-171">Vezměte v úvahu následující příklad `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d8878-171">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="d8878-172">V koncepčním případě je ověření certifikátu v takovém případě oprávnění.</span><span class="sxs-lookup"><span data-stu-id="d8878-172">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="d8878-173">Přidání kontroly, například vystavitele nebo kryptografického otisku v zásadách autorizace, nikoli uvnitř `OnCertificateValidated` , je naprosto přijatelné.</span><span class="sxs-lookup"><span data-stu-id="d8878-173">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="d8878-174">Konfigurace hostitele pro vyžadování certifikátů</span><span class="sxs-lookup"><span data-stu-id="d8878-174">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="d8878-175">Kestrel</span><span class="sxs-lookup"><span data-stu-id="d8878-175">Kestrel</span></span>

<span data-ttu-id="d8878-176">V *program.cs*nakonfigurujte Kestrel následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="d8878-176">In *Program.cs*, configure Kestrel as follows:</span></span>

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
> <span data-ttu-id="d8878-177">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d8878-177">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="d8878-178">IIS</span><span class="sxs-lookup"><span data-stu-id="d8878-178">IIS</span></span>

<span data-ttu-id="d8878-179">Ve Správci služby IIS proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="d8878-179">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="d8878-180">Na kartě **připojení** vyberte svou lokalitu.</span><span class="sxs-lookup"><span data-stu-id="d8878-180">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="d8878-181">Dvakrát klikněte na možnost **Nastavení SSL** v okně **zobrazení funkcí** .</span><span class="sxs-lookup"><span data-stu-id="d8878-181">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="d8878-182">Zaškrtněte políčko **vyžadovat protokol SSL** a v části **certifikáty klienta** vyberte tlačítko **vyžadovat** přepínač.</span><span class="sxs-lookup"><span data-stu-id="d8878-182">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Nastavení klientského certifikátu ve službě IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="d8878-184">Azure a vlastní webové proxy servery</span><span class="sxs-lookup"><span data-stu-id="d8878-184">Azure and custom web proxies</span></span>

<span data-ttu-id="d8878-185">Postup konfigurace middlewaru pro předávání certifikátů najdete v [dokumentaci k hostiteli a nasazení](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .</span><span class="sxs-lookup"><span data-stu-id="d8878-185">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="d8878-186">Použití ověřování certifikátů v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="d8878-186">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="d8878-187">Pro Azure se nevyžaduje žádná konfigurace předávání.</span><span class="sxs-lookup"><span data-stu-id="d8878-187">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="d8878-188">Toto je již nastaveno v middlewari předávání certifikátů.</span><span class="sxs-lookup"><span data-stu-id="d8878-188">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="d8878-189">To vyžaduje, aby byl přítomen CertificateForwardingMiddleware.</span><span class="sxs-lookup"><span data-stu-id="d8878-189">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="d8878-190">Použití ověřování certifikátů ve vlastních webových proxy serverech</span><span class="sxs-lookup"><span data-stu-id="d8878-190">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="d8878-191">`AddCertificateForwarding`Metoda slouží k zadání:</span><span class="sxs-lookup"><span data-stu-id="d8878-191">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="d8878-192">Název hlavičky klienta.</span><span class="sxs-lookup"><span data-stu-id="d8878-192">The client header name.</span></span>
* <span data-ttu-id="d8878-193">Způsob načtení certifikátu (pomocí `HeaderConverter` Vlastnosti).</span><span class="sxs-lookup"><span data-stu-id="d8878-193">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="d8878-194">Ve vlastních webových proxy serverech se certifikát předává jako vlastní Hlavička požadavku, například `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="d8878-194">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="d8878-195">Pokud ho chcete použít, nakonfigurujte předávání certifikátů v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d8878-195">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="d8878-196">`Startup.Configure`Metoda pak přidá middleware.</span><span class="sxs-lookup"><span data-stu-id="d8878-196">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="d8878-197">`UseCertificateForwarding`je volána před voláním `UseAuthentication` a `UseAuthorization` :</span><span class="sxs-lookup"><span data-stu-id="d8878-197">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

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

<span data-ttu-id="d8878-198">Samostatnou třídu lze použít k implementaci logiky ověřování.</span><span class="sxs-lookup"><span data-stu-id="d8878-198">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="d8878-199">Protože v tomto příkladu se používá stejný certifikát podepsaný svým držitelem, ujistěte se, že se dá použít jenom váš certifikát.</span><span class="sxs-lookup"><span data-stu-id="d8878-199">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="d8878-200">Ověřte, že se neshodují kryptografické otisky certifikátu klienta i certifikátu serveru. v opačném případě se dá použít libovolný certifikát, který bude pro ověření stačit.</span><span class="sxs-lookup"><span data-stu-id="d8878-200">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="d8878-201">Tato metoda by se použila uvnitř `AddCertificate` metody.</span><span class="sxs-lookup"><span data-stu-id="d8878-201">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="d8878-202">V případě použití zprostředkujících nebo podřízených certifikátů můžete také ověřit předmět nebo vystavitele.</span><span class="sxs-lookup"><span data-stu-id="d8878-202">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="d8878-203">Implementace HttpClient pomocí certifikátu a HttpClientHandler</span><span class="sxs-lookup"><span data-stu-id="d8878-203">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="d8878-204">HttpClientHandler lze přidat přímo v konstruktoru třídy HttpClient.</span><span class="sxs-lookup"><span data-stu-id="d8878-204">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="d8878-205">Při vytváření instancí HttpClient byste měli věnovat pozornost.</span><span class="sxs-lookup"><span data-stu-id="d8878-205">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="d8878-206">HttpClient pak certifikát odešle spolu s každou žádostí.</span><span class="sxs-lookup"><span data-stu-id="d8878-206">The HttpClient will then send the certificate with each request.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="d8878-207">Implementace HttpClient pomocí certifikátu a pojmenovaného HttpClient z IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="d8878-207">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="d8878-208">V následujícím příkladu se klientský certifikát přidá do HttpClientHandler pomocí vlastnosti vlastnost ClientCertificates z obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="d8878-208">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="d8878-209">Tato obslužná rutina se pak dá použít v pojmenované instanci HttpClient pomocí metody ConfigurePrimaryHttpMessageHandler.</span><span class="sxs-lookup"><span data-stu-id="d8878-209">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="d8878-210">Toto je instalační program ve třídě Startup v metodě ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="d8878-210">This is setup in the Startup class in the ConfigureServices method.</span></span>

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

<span data-ttu-id="d8878-211">IHttpClientFactory se pak může použít k získání pojmenované instance s obslužnou rutinou a certifikátem.</span><span class="sxs-lookup"><span data-stu-id="d8878-211">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="d8878-212">Metoda CreateClient s názvem klienta definovaného ve spouštěcí třídě slouží k získání instance.</span><span class="sxs-lookup"><span data-stu-id="d8878-212">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="d8878-213">Požadavek HTTP lze odeslat pomocí klienta podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="d8878-213">The HTTP request can be sent using the client as required.</span></span>

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

<span data-ttu-id="d8878-214">Pokud se do serveru pošle správný certifikát, vrátí se data.</span><span class="sxs-lookup"><span data-stu-id="d8878-214">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="d8878-215">Pokud se nepošle žádný certifikát nebo nesprávný certifikát, vrátí se stavový kód HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="d8878-215">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="d8878-216">Vytvoření certifikátů v PowerShellu</span><span class="sxs-lookup"><span data-stu-id="d8878-216">Create certificates in PowerShell</span></span>

<span data-ttu-id="d8878-217">Vytvoření certifikátů je nejzávažnou součástí nastavení tohoto toku.</span><span class="sxs-lookup"><span data-stu-id="d8878-217">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="d8878-218">Kořenový certifikát se dá vytvořit pomocí `New-SelfSignedCertificate` rutiny PowerShellu.</span><span class="sxs-lookup"><span data-stu-id="d8878-218">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="d8878-219">Při vytváření certifikátu použijte silné heslo.</span><span class="sxs-lookup"><span data-stu-id="d8878-219">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="d8878-220">Je důležité přidat `KeyUsageProperty` parametr a `KeyUsage` parametr, jak je znázorněno.</span><span class="sxs-lookup"><span data-stu-id="d8878-220">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="d8878-221">Vytvořit kořenovou certifikační autoritu</span><span class="sxs-lookup"><span data-stu-id="d8878-221">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="d8878-222">`-DnsName`Hodnota parametru musí odpovídat cíli nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8878-222">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="d8878-223">Například "localhost" pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="d8878-223">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="d8878-224">Nainstalovat do důvěryhodného kořenového adresáře</span><span class="sxs-lookup"><span data-stu-id="d8878-224">Install in the trusted root</span></span>

<span data-ttu-id="d8878-225">Kořenový certifikát musí být v hostitelském systému důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="d8878-225">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="d8878-226">Kořenový certifikát, který nebyl vytvořen certifikační autoritou, nebude ve výchozím nastavení považován za důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="d8878-226">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="d8878-227">Následující odkaz vysvětluje, jak to lze provést ve Windows:</span><span class="sxs-lookup"><span data-stu-id="d8878-227">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="d8878-228">Zprostředkující certifikát</span><span class="sxs-lookup"><span data-stu-id="d8878-228">Intermediate certificate</span></span>

<span data-ttu-id="d8878-229">Zprostředkující certifikát se teď dá vytvořit z kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-229">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="d8878-230">To není vyžadováno pro všechny případy použití, ale možná budete muset vytvořit mnoho certifikátů nebo musíte aktivovat nebo zakázat skupiny certifikátů.</span><span class="sxs-lookup"><span data-stu-id="d8878-230">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="d8878-231">`TextExtension`Parametr je vyžadován pro nastavení délky cesty v základních omezeních certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-231">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="d8878-232">Zprostředkující certifikát je pak možné přidat do důvěryhodného zprostředkujícího certifikátu v hostitelském systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d8878-232">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="d8878-233">Vytvořit podřízený certifikát z zprostředkujícího certifikátu</span><span class="sxs-lookup"><span data-stu-id="d8878-233">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="d8878-234">Z zprostředkujícího certifikátu se dá vytvořit podřízený certifikát.</span><span class="sxs-lookup"><span data-stu-id="d8878-234">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="d8878-235">Toto je koncová entita a není nutné vytvářet další podřízené certifikáty.</span><span class="sxs-lookup"><span data-stu-id="d8878-235">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="d8878-236">Vytvořit podřízený certifikát z kořenového certifikátu</span><span class="sxs-lookup"><span data-stu-id="d8878-236">Create child certificate from root certificate</span></span>

<span data-ttu-id="d8878-237">Podřízený certifikát lze také vytvořit přímo z kořenového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d8878-237">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="d8878-238">Příklad certifikátu root-zprostředkující certifikát – certifikát</span><span class="sxs-lookup"><span data-stu-id="d8878-238">Example root - intermediate certificate - certificate</span></span>

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

<span data-ttu-id="d8878-239">Při použití kořenových, zprostředkujících nebo podřízených certifikátů je možné certifikáty ověřit pomocí kryptografického otisku nebo PublicKey podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="d8878-239">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

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
