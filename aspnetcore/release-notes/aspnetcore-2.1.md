---
title: Co je nového v ASP.NET Core 2,1
author: isaac2004
description: Seznamte se s novými funkcemi v ASP.NET Core 2,1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.1
ms.openlocfilehash: 3fb524748b9e2aa2c12b64f67a7960c4f0f911b0
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021156"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="5c392-103">Co je nového v ASP.NET Core 2,1</span><span class="sxs-lookup"><span data-stu-id="5c392-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="5c392-104">Tento článek zvýrazňuje nejvýznamnější změny v ASP.NET Core 2,1 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="5c392-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

<span data-ttu-id="5c392-105">SignalRbylo přepsáno pro ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="5c392-105">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="5c392-106">ASP.NET Core SignalR obsahuje řadu vylepšení:</span><span class="sxs-lookup"><span data-stu-id="5c392-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="5c392-107">Zjednodušený model škálování na více instancí.</span><span class="sxs-lookup"><span data-stu-id="5c392-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="5c392-108">Nový klient jazyka JavaScript bez závislosti jQuery.</span><span class="sxs-lookup"><span data-stu-id="5c392-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="5c392-109">Nový kompaktní binární protokol založený na MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5c392-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="5c392-110">Podpora vlastních protokolů.</span><span class="sxs-lookup"><span data-stu-id="5c392-110">Support for custom protocols.</span></span>
* <span data-ttu-id="5c392-111">Nový model odpovědi streamování.</span><span class="sxs-lookup"><span data-stu-id="5c392-111">A new streaming response model.</span></span>
* <span data-ttu-id="5c392-112">Podpora klientů založená na holéch objektech WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5c392-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="5c392-113">Další informace najdete v tématu [ASP.NET Core SignalR ](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="5c392-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="no-locrazor-class-libraries"></a><span data-ttu-id="5c392-114">Razorknihovny tříd</span><span class="sxs-lookup"><span data-stu-id="5c392-114">Razor class libraries</span></span>

<span data-ttu-id="5c392-115">ASP.NET Core 2,1 usnadňuje sestavování a zahrnutí Razor uživatelského rozhraní na základě knihovny a jejich sdílení napříč více projekty.</span><span class="sxs-lookup"><span data-stu-id="5c392-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="5c392-116">Nová Razor sada SDK umožňuje sestavovat Razor soubory do projektu knihovny tříd, který může být zabalen do balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="5c392-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="5c392-117">Zobrazení a stránky v knihovnách jsou automaticky zjišťovány a aplikace je může přepsat aplikací.</span><span class="sxs-lookup"><span data-stu-id="5c392-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="5c392-118">Integrací Razor kompilace do sestavení:</span><span class="sxs-lookup"><span data-stu-id="5c392-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="5c392-119">Čas spuštění aplikace je výrazně rychlejší.</span><span class="sxs-lookup"><span data-stu-id="5c392-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="5c392-120">Rychlé aktualizace Razor zobrazení a stránek za běhu jsou stále k dispozici jako součást pracovního postupu iterativního vývoje.</span><span class="sxs-lookup"><span data-stu-id="5c392-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="5c392-121">Další informace najdete v tématu [vytvoření opakovaně použitelného uživatelského rozhraní pomocí Razor projektu knihovny tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="5c392-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="no-locidentity-ui-library--scaffolding"></a><span data-ttu-id="5c392-122">IdentityKnihovna uživatelského rozhraní & generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="5c392-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="5c392-123">ASP.NET Core 2,1 poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="5c392-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5c392-124">Aplikace, které zahrnují, Identity můžou použít nový generátor, Identity aby selektivně přidal zdrojový kód obsažený v Identity Razor knihovně tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="5c392-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="5c392-125">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="5c392-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="5c392-126">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="5c392-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="5c392-127">Generovaný kód má přednost před stejným kódem v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="5c392-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="5c392-128">Aplikace, které **neobsahují** ověřování, můžou Identity pro přidání balíčku RCL použít generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="5c392-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="5c392-129">Máte možnost vybrat Identity kód, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="5c392-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="5c392-130">Další informace najdete v tématu o [generování uživatelského rozhraní Identity v ASP.NET Core projektech](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="5c392-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="5c392-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="5c392-131">HTTPS</span></span>

<span data-ttu-id="5c392-132">Díky lepšímu zaměření na zabezpečení a ochranu osobních údajů je důležité povolit protokol HTTPS pro webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c392-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="5c392-133">Vynucování protokolu HTTPS se na webu stále přísnější.</span><span class="sxs-lookup"><span data-stu-id="5c392-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="5c392-134">Lokality, které nepoužívají protokol HTTPS, se považují za nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="5c392-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="5c392-135">Prohlížeče (chrom, Mozilla) začínají vymáhat, že webové funkce musí být používány z zabezpečeného kontextu.</span><span class="sxs-lookup"><span data-stu-id="5c392-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="5c392-136">[GDPR](xref:security/gdpr) vyžaduje k ochraně osobních údajů uživatelů používání protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5c392-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="5c392-137">Při použití protokolu HTTPS v produkčním prostředí je použití protokolu HTTPS při vývoji důležité k tomu, aby se zabránilo problémům v nasazení (například nezabezpečeným odkazům).</span><span class="sxs-lookup"><span data-stu-id="5c392-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="5c392-138">ASP.NET Core 2,1 obsahuje řadu vylepšení, která usnadňují používání protokolu HTTPS ve vývoji a konfiguraci HTTPS v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="5c392-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="5c392-139">Další informace najdete v tématu [vymáhání protokolu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="5c392-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="5c392-140">Ve výchozím nastavení zapnuto</span><span class="sxs-lookup"><span data-stu-id="5c392-140">On by default</span></span>

<span data-ttu-id="5c392-141">V zájmu usnadnění zabezpečení vývoje webu je teď ve výchozím nastavení povolený protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5c392-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="5c392-142">Počínaje 2,1 Kestrel naslouchá, `https://localhost:5001` když je k dispozici místní vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="5c392-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="5c392-143">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="5c392-143">A development certificate is created:</span></span>

* <span data-ttu-id="5c392-144">Jako součást .NET Core SDKho prvního spuštění, při prvním použití sady SDK.</span><span class="sxs-lookup"><span data-stu-id="5c392-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="5c392-145">Ručně pomocí nového `dev-certs` nástroje.</span><span class="sxs-lookup"><span data-stu-id="5c392-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="5c392-146">Spusťte `dotnet dev-certs https --trust` pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="5c392-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="5c392-147">Přesměrování a vynucení HTTPS</span><span class="sxs-lookup"><span data-stu-id="5c392-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="5c392-148">Webové aplikace obvykle musí naslouchat na protokolech HTTP i HTTPS, ale pak přesměrují všechny přenosy HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5c392-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="5c392-149">V 2,1 se zavedl specializovaný middleware pro přesměrování HTTPS, který inteligentně přesměrovává na základě přítomnosti konfiguračních nebo vázaných portů serveru.</span><span class="sxs-lookup"><span data-stu-id="5c392-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="5c392-150">Použití protokolu HTTPS se dá dál vyhovět pomocí [protokolu HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="5c392-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="5c392-151">HSTS dá prohlížečům pokyn, aby vždycky měli přístup k webu přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5c392-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="5c392-152">ASP.NET Core 2,1 přidává HSTS middleware, který podporuje možnosti pro maximální stáří, subdomény a seznam předload HSTS.</span><span class="sxs-lookup"><span data-stu-id="5c392-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="5c392-153">Konfigurace pro produkční prostředí</span><span class="sxs-lookup"><span data-stu-id="5c392-153">Configuration for production</span></span>

<span data-ttu-id="5c392-154">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="5c392-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="5c392-155">V 2,1 se přidalo výchozí konfigurační schéma pro konfiguraci HTTPS pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5c392-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="5c392-156">Aplikace je možné nakonfigurovat tak, aby používaly:</span><span class="sxs-lookup"><span data-stu-id="5c392-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="5c392-157">Několik koncových bodů včetně adres URL.</span><span class="sxs-lookup"><span data-stu-id="5c392-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="5c392-158">Další informace najdete v tématu [implementace webového serveru Kestrel: konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c392-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="5c392-159">Certifikát, který se má použít pro protokol HTTPS buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="5c392-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="5c392-160">GDPR</span><span class="sxs-lookup"><span data-stu-id="5c392-160">GDPR</span></span>

<span data-ttu-id="5c392-161">ASP.NET Core poskytuje rozhraní API a šablony, které vám pomůžou splnit některé z požadavků [EU na obecné nařízení o ochraně osobních údajů (GDPR)](https://www.eugdpr.org/) .</span><span class="sxs-lookup"><span data-stu-id="5c392-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="5c392-162">Další informace najdete v tématu [Podpora GDPR v ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5c392-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="5c392-163">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ukazuje, jak použít a umožňuje testovat většinu GDPRch bodů rozšíření a rozhraní API přidaných do šablon ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="5c392-163">A [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="5c392-164">Integrační testy</span><span class="sxs-lookup"><span data-stu-id="5c392-164">Integration tests</span></span>

<span data-ttu-id="5c392-165">Zavádí se nový balíček, který zjednodušuje vytváření a spouštění testů.</span><span class="sxs-lookup"><span data-stu-id="5c392-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="5c392-166">Balíček [Microsoft. AspNetCore. Mvc. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) zpracovává následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="5c392-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="5c392-167">Zkopíruje soubor závislosti (\* \* . DEPS\*) z testované aplikace do složky *bin* testovacího projektu.</span><span class="sxs-lookup"><span data-stu-id="5c392-167">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="5c392-168">Nastaví kořen obsahu na kořen projektu testované aplikace, aby při spuštění testů byly nalezeny statické soubory a stránky/zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5c392-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="5c392-169">Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění testované aplikace pomocí [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="5c392-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="5c392-170">Následující test používá [xUnit](https://xunit.github.io/) ke kontrole, zda se stránka indexu načte s kódem stavu úspěch a se správnou hlavičkou Content-Type:</span><span class="sxs-lookup"><span data-stu-id="5c392-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="5c392-171">Další informace najdete v tématu věnovaném [integračním testům](xref:test/integration-tests) .</span><span class="sxs-lookup"><span data-stu-id="5c392-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="5c392-172">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="5c392-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="5c392-173">ASP.NET Core 2,1 přidává nové konvence programování, které usnadňují vytváření čistě a popisných webových rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5c392-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="5c392-174">`ActionResult<T>`je přidaný nový typ, který aplikaci umožní vracet buď typ odpovědi, nebo jakýkoli výsledek jiné akce (podobně jako IActionResult), ale stále určuje typ odpovědi.</span><span class="sxs-lookup"><span data-stu-id="5c392-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="5c392-175">`[ApiController]`Atribut byl také přidán jako způsob, jak se přihlásit k konvencím a chováním specifickému pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5c392-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="5c392-176">Další informace najdete v tématu [sestavování webových rozhraní API pomocí ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="5c392-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="5c392-177">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="5c392-177">IHttpClientFactory</span></span>

<span data-ttu-id="5c392-178">ASP.NET Core 2,1 obsahuje novou `IHttpClientFactory` službu, která usnadňuje konfiguraci a využívání instancí `HttpClient` v aplikacích.</span><span class="sxs-lookup"><span data-stu-id="5c392-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="5c392-179">`HttpClient`již má koncepci delegování obslužných rutin, které by mohly být propojeny pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="5c392-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="5c392-180">Objekt pro vytváření:</span><span class="sxs-lookup"><span data-stu-id="5c392-180">The factory:</span></span>

* <span data-ttu-id="5c392-181">Usnadňuje registraci instancí pro `HttpClient` s pojmenovaným klientem.</span><span class="sxs-lookup"><span data-stu-id="5c392-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="5c392-182">Implementuje obslužnou rutinu Polly, která umožňuje použití zásad Polly pro opakování, CircuitBreakers atd.</span><span class="sxs-lookup"><span data-stu-id="5c392-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="5c392-183">Další informace najdete v tématu [inicializace požadavků HTTP](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="5c392-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="5c392-184">Konfigurace přenosu Kestrel</span><span class="sxs-lookup"><span data-stu-id="5c392-184">Kestrel transport configuration</span></span>

<span data-ttu-id="5c392-185">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="5c392-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="5c392-186">Další informace najdete v tématu [implementace webového serveru Kestrel: Konfigurace přenosu](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c392-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="5c392-187">Obecný tvůrce hostitele</span><span class="sxs-lookup"><span data-stu-id="5c392-187">Generic host builder</span></span>

<span data-ttu-id="5c392-188">Byl zaveden obecný tvůrce hostitele ( `HostBuilder` ).</span><span class="sxs-lookup"><span data-stu-id="5c392-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="5c392-189">Tento tvůrce se dá použít pro aplikace, které nezpracovávají požadavky HTTP (zasílání zpráv, úlohy na pozadí atd.).</span><span class="sxs-lookup"><span data-stu-id="5c392-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="5c392-190">Další informace najdete v tématu [obecný hostitel .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="5c392-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="5c392-191">Aktualizované šablony SPA</span><span class="sxs-lookup"><span data-stu-id="5c392-191">Updated SPA templates</span></span>

<span data-ttu-id="5c392-192">Šablony aplikace s jednou stránkou pro úhlové, reagující a reagující na Redux jsou aktualizovány tak, aby používaly standardní struktury projektů a systémy sestavení pro každé rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5c392-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="5c392-193">Úhlová šablona je založena na úhlů CLI a šablony s reakce jsou založené na aplikaci Create-reagují-App.</span><span class="sxs-lookup"><span data-stu-id="5c392-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="5c392-194">Další informace:</span><span class="sxs-lookup"><span data-stu-id="5c392-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a><span data-ttu-id="5c392-195">RazorStránky vyhledávající Razor prostředky</span><span class="sxs-lookup"><span data-stu-id="5c392-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="5c392-196">V 2,1 Razor budou stránky hledat Razor prostředky (například rozložení a částečné) v následujících adresářích v uvedeném pořadí:</span><span class="sxs-lookup"><span data-stu-id="5c392-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="5c392-197">Složka aktuální stránky.</span><span class="sxs-lookup"><span data-stu-id="5c392-197">Current Pages folder.</span></span>
1. <span data-ttu-id="5c392-198">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="5c392-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="5c392-199">*/Views/Shared/*</span><span class="sxs-lookup"><span data-stu-id="5c392-199">*/Views/Shared/*</span></span>

## <a name="no-locrazor-pages-in-an-area"></a><span data-ttu-id="5c392-200">RazorStránky v oblasti</span><span class="sxs-lookup"><span data-stu-id="5c392-200">Razor Pages in an area</span></span>

<span data-ttu-id="5c392-201">RazorStránky teď podporují [oblasti](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="5c392-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="5c392-202">Pokud chcete zobrazit příklad oblastí, vytvořte novou Razor stránku webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="5c392-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="5c392-203">RazorWebové aplikace stránky s jednotlivými uživatelskými účty zahrnuje */areas/ Identity /Pages*.</span><span class="sxs-lookup"><span data-stu-id="5c392-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="5c392-204">Verze kompatibility MVC</span><span class="sxs-lookup"><span data-stu-id="5c392-204">MVC compatibility version</span></span>

<span data-ttu-id="5c392-205"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Metoda umožňuje aplikaci, aby se odhlásila nebo výslovný souhlas při změnách chování, které se zavedly v ASP.NET Core MVC 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="5c392-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="5c392-206">Další informace naleznete v tématu <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="5c392-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="5c392-207">Migrace z 2,0 na 2,1</span><span class="sxs-lookup"><span data-stu-id="5c392-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="5c392-208">Viz [migrace z ASP.NET Core 2,0 na 2,1](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="5c392-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="5c392-209">Další informace</span><span class="sxs-lookup"><span data-stu-id="5c392-209">Additional information</span></span>

<span data-ttu-id="5c392-210">Úplný seznam změn najdete v [poznámkách k verzi pro ASP.NET Core 2,1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="5c392-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span></span>
