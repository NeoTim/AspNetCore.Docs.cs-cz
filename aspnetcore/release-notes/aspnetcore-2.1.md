---
title: Co je nového v ASP.NET Core 2.1
author: isaac2004
description: Seznamte se s novými funkcemi v ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: af5807b782d4acec8c7d40111dc508dfa6127057
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667543"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="e6440-103">Co je nového v ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="e6440-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="e6440-104">Tento článek upozorňuje na nejvýznamnější změny v ASP.NET jádrem 2.1 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="e6440-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

SignalR<span data-ttu-id="e6440-105">byl přepsán pro ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="e6440-105"> has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="e6440-106">ASP.NET SignalR Core obsahuje řadu vylepšení:</span><span class="sxs-lookup"><span data-stu-id="e6440-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="e6440-107">Zjednodušený model škálování.</span><span class="sxs-lookup"><span data-stu-id="e6440-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="e6440-108">Nový klient JavaScriptu bez závislosti jQuery.</span><span class="sxs-lookup"><span data-stu-id="e6440-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="e6440-109">Nový kompaktní binární protokol založený na MessagePack.</span><span class="sxs-lookup"><span data-stu-id="e6440-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="e6440-110">Podpora vlastních protokolů.</span><span class="sxs-lookup"><span data-stu-id="e6440-110">Support for custom protocols.</span></span>
* <span data-ttu-id="e6440-111">Nový model odezvy streamování.</span><span class="sxs-lookup"><span data-stu-id="e6440-111">A new streaming response model.</span></span>
* <span data-ttu-id="e6440-112">Podpora pro klienty založené na holé WebSockets.</span><span class="sxs-lookup"><span data-stu-id="e6440-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="e6440-113">Další informace naleznete [v SignalRtématu ASP.NET Core ](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="e6440-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="e6440-114">Knihovny RCL (Razor Class Library)</span><span class="sxs-lookup"><span data-stu-id="e6440-114">Razor class libraries</span></span>

<span data-ttu-id="e6440-115">ASP.NET Core 2.1 usnadňuje vytváření a zařazuje cítění na základě Razor v knihovně a sdílet je mezi více projekty.</span><span class="sxs-lookup"><span data-stu-id="e6440-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="e6440-116">Nová sada Razor SDK umožňuje vytváření souborů Razor do projektu knihovny tříd, který lze zabalit do balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="e6440-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="e6440-117">Zobrazení a stránky v knihovnách jsou automaticky zjištěny a mohou být přepsány aplikací.</span><span class="sxs-lookup"><span data-stu-id="e6440-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="e6440-118">Integrací kompilace Razor do sestavení:</span><span class="sxs-lookup"><span data-stu-id="e6440-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="e6440-119">Doba spuštění aplikace je výrazně rychlejší.</span><span class="sxs-lookup"><span data-stu-id="e6440-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="e6440-120">Rychlé aktualizace zobrazení Razor a stránek za běhu jsou stále k dispozici jako součást iterativního vývojového pracovního postupu.</span><span class="sxs-lookup"><span data-stu-id="e6440-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="e6440-121">Další informace naleznete v [tématu Vytvoření opakovaně použitelného ui pomocí projektu Knihovna tříd razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="e6440-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="e6440-122">Knihovna uživatelského & identity</span><span class="sxs-lookup"><span data-stu-id="e6440-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="e6440-123">ASP.NET Core 2.1 poskytuje [ASP.NET základní identitu](xref:security/authentication/identity) jako [knihovnu třídy Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="e6440-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="e6440-124">Aplikace, které obsahují identity můžete použít nové identity sakuru selektivně přidat zdrojový kód obsažený v knihovně třídy Razor identity (RCL).</span><span class="sxs-lookup"><span data-stu-id="e6440-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="e6440-125">Můžete chtít generovat zdrojový kód, takže můžete upravit kód a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="e6440-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="e6440-126">Můžete například instruovat složky scaffolder generovat kód použitý v registraci.</span><span class="sxs-lookup"><span data-stu-id="e6440-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="e6440-127">Generovaný kód má přednost před stejným kódem v rcl identity.</span><span class="sxs-lookup"><span data-stu-id="e6440-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="e6440-128">Aplikace, které **nezahrnují** ověřování můžete použít identity sakum přidat balíček Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="e6440-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="e6440-129">Máte možnost vybrat kód identity, který má být generován.</span><span class="sxs-lookup"><span data-stu-id="e6440-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="e6440-130">Další informace naleznete [v tématu Identita lešení v ASP.NET projektů core](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="e6440-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="e6440-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="e6440-131">HTTPS</span></span>

<span data-ttu-id="e6440-132">Se zvýšeným zaměřením na zabezpečení a ochranu osobních údajů je důležité povolit protokol HTTPS pro webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6440-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="e6440-133">Prosazování HTTPS je stále přísnější na webu.</span><span class="sxs-lookup"><span data-stu-id="e6440-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="e6440-134">Weby, které nepoužívají protokol HTTPS, jsou považovány za nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="e6440-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="e6440-135">Prohlížeče (Chromium, Mozilla) začínají vynucovat, že webové funkce musí být používány ze zabezpečeného kontextu.</span><span class="sxs-lookup"><span data-stu-id="e6440-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="e6440-136">[GDPR](xref:security/gdpr) vyžaduje použití protokolu HTTPS k ochraně soukromí uživatelů.</span><span class="sxs-lookup"><span data-stu-id="e6440-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="e6440-137">Při použití protokolu HTTPS v produkčním prostředí je důležité, pomocí protokolu HTTPS ve vývoji může pomoci zabránit problémům v nasazení (například nezabezpečené odkazy).</span><span class="sxs-lookup"><span data-stu-id="e6440-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="e6440-138">ASP.NET Core 2.1 obsahuje řadu vylepšení, která usnadňují použití protokolu HTTPS ve vývoji a konfiguraci protokolu HTTPS v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="e6440-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="e6440-139">Další informace naleznete v [tématu Enforce HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="e6440-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="e6440-140">Zapnuto ve výchozím nastavení</span><span class="sxs-lookup"><span data-stu-id="e6440-140">On by default</span></span>

<span data-ttu-id="e6440-141">Pro usnadnění bezpečného vývoje webových stránek je nyní protokol HTTPS ve výchozím nastavení povolen.</span><span class="sxs-lookup"><span data-stu-id="e6440-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="e6440-142">Počínaje 2.1, Kestrel poslouchá, `https://localhost:5001` když je k dispozici certifikát místního rozvoje.</span><span class="sxs-lookup"><span data-stu-id="e6440-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="e6440-143">Je vytvořen vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="e6440-143">A development certificate is created:</span></span>

* <span data-ttu-id="e6440-144">Jako součást .NET Core SDK první spuštění při prvním použití sady SDK.</span><span class="sxs-lookup"><span data-stu-id="e6440-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="e6440-145">Ruční použití nového `dev-certs` nástroje.</span><span class="sxs-lookup"><span data-stu-id="e6440-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="e6440-146">Spusťte `dotnet dev-certs https --trust` důvěřovat certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e6440-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="e6440-147">Přesměrování a vynucení protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="e6440-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="e6440-148">Webové aplikace obvykle potřebují naslouchat na http i https, ale pak přesměrovat veškerý provoz HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e6440-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="e6440-149">V 2.1 byl zaveden specializovaný middleware pro přesměrování HTTPS, který inteligentně přesměrovává na základě přítomnosti portů konfigurace nebo vázaného serveru.</span><span class="sxs-lookup"><span data-stu-id="e6440-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="e6440-150">Použití protokolu HTTPS lze dále vynutit pomocí [protokolu HTTP Strict Transport Security Protocol (HSTS).](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)</span><span class="sxs-lookup"><span data-stu-id="e6440-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="e6440-151">HSTS instruuje prohlížeče, aby vždy přistupovaly k webu přes HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e6440-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="e6440-152">ASP.NET Core 2.1 přidává middleware HSTS, který podporuje možnosti pro maximální stáří, subdomény a seznam předběžného načtení HSTS.</span><span class="sxs-lookup"><span data-stu-id="e6440-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="e6440-153">Konfigurace pro výrobu</span><span class="sxs-lookup"><span data-stu-id="e6440-153">Configuration for production</span></span>

<span data-ttu-id="e6440-154">V produkčním prostředí musí být protokol HTTPS explicitně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="e6440-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="e6440-155">V 2.1 bylo přidáno výchozí schéma konfigurace pro konfiguraci protokolu HTTPS pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e6440-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="e6440-156">Aplikace lze nakonfigurovat tak, aby používaly:</span><span class="sxs-lookup"><span data-stu-id="e6440-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="e6440-157">Více koncových bodů včetně adres URL.</span><span class="sxs-lookup"><span data-stu-id="e6440-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="e6440-158">Další informace naleznete [v tématu Implementace webového serveru Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="e6440-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="e6440-159">Certifikát, který se má použít pro protokol HTTPS ze souboru na disku nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="e6440-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="e6440-160">GDPR</span><span class="sxs-lookup"><span data-stu-id="e6440-160">GDPR</span></span>

<span data-ttu-id="e6440-161">ASP.NET Core poskytuje api a šablony, které pomáhají splnit některé obecné [požadavky nařízení EU o ochraně osobních údajů (GDPR).](https://www.eugdpr.org/)</span><span class="sxs-lookup"><span data-stu-id="e6440-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="e6440-162">Další informace najdete v tématu [podpora GDPR v ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="e6440-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="e6440-163">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ukazuje, jak používat a umožňuje otestovat většinu rozšiřujících bodů GDPR a api přidaných do ASP.NET šablon Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="e6440-163">A [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="e6440-164">Integrační testy</span><span class="sxs-lookup"><span data-stu-id="e6440-164">Integration tests</span></span>

<span data-ttu-id="e6440-165">Je zaveden nový balíček, který zjednodušuje vytváření a provádění testů.</span><span class="sxs-lookup"><span data-stu-id="e6440-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="e6440-166">Balíček [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) zpracovává následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="e6440-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="e6440-167">Zkopíruje soubor*\*závislostí ( .deps*) z testované aplikace do složky *přihrádky* testovacího projektu.</span><span class="sxs-lookup"><span data-stu-id="e6440-167">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="e6440-168">Nastaví kořen obsahu na kořen projektu testované aplikace tak, aby statické soubory a stránky/zobrazení byly nalezeny při provádění testů.</span><span class="sxs-lookup"><span data-stu-id="e6440-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="e6440-169">Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění testované aplikace pomocí [serveru TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="e6440-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="e6440-170">Následující test používá [xUnit](https://xunit.github.io/) ke kontrole, že index stránka načte s kódem stavu úspěchu a se správným obsah-typ záhlaví:</span><span class="sxs-lookup"><span data-stu-id="e6440-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

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

<span data-ttu-id="e6440-171">Další informace naleznete v tématu [testy integrace.](xref:test/integration-tests)</span><span class="sxs-lookup"><span data-stu-id="e6440-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="e6440-172">[ApiController], Výsledek\<akce T></span><span class="sxs-lookup"><span data-stu-id="e6440-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="e6440-173">ASP.NET Core 2.1 přidává nové programovací konvence, které usnadňují vytváření čistých a popisných webových rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e6440-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="e6440-174">`ActionResult<T>`je nový typ přidán umožnit aplikaci vrátit buď typ odpovědi nebo jakýkoli jiný výsledek akce (podobně jako IActionResult), zatímco stále označující typ odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e6440-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="e6440-175">Atribut `[ApiController]` byl také přidán jako způsob, jak se přihlásit k webovým konvencím a chováním specifickým pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e6440-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="e6440-176">Další informace naleznete v [tématu Vytváření webových api s ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="e6440-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="e6440-177">IhttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e6440-177">IHttpClientFactory</span></span>

<span data-ttu-id="e6440-178">ASP.NET Core 2.1 `IHttpClientFactory` obsahuje novou službu, která usnadňuje `HttpClient` konfiguraci a využití instancí v aplikacích.</span><span class="sxs-lookup"><span data-stu-id="e6440-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="e6440-179">`HttpClient`již má koncept delegování obslužné rutiny, které by mohly být propojeny pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="e6440-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="e6440-180">Továrna:</span><span class="sxs-lookup"><span data-stu-id="e6440-180">The factory:</span></span>

* <span data-ttu-id="e6440-181">Umožňuje registraci instancí `HttpClient` na pojmenované klienta intuitivnější.</span><span class="sxs-lookup"><span data-stu-id="e6440-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="e6440-182">Implementuje Polly obslužnou rutinu, která umožňuje Polly politiky, které mají být použity pro opakování, jističe atd.</span><span class="sxs-lookup"><span data-stu-id="e6440-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="e6440-183">Další informace naleznete [v tématu Initiate HTTP Requests](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="e6440-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="e6440-184">Konfigurace přepravy kestrelu</span><span class="sxs-lookup"><span data-stu-id="e6440-184">Kestrel transport configuration</span></span>

<span data-ttu-id="e6440-185">S vydáním ASP.NET Core 2.1, Kestrel výchozí přenos již není založen na Libuv, ale místo toho na spravované sokety.</span><span class="sxs-lookup"><span data-stu-id="e6440-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="e6440-186">Další informace naleznete [v tématu Implementace webového serveru Kestrel: Konfigurace přenosu](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="e6440-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="e6440-187">Obecný tvůrce hostitelů</span><span class="sxs-lookup"><span data-stu-id="e6440-187">Generic host builder</span></span>

<span data-ttu-id="e6440-188">Byl zaveden obecný`HostBuilder`tvůrce hostitelů ( ).</span><span class="sxs-lookup"><span data-stu-id="e6440-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="e6440-189">Tento tvůrce lze použít pro aplikace, které nezpracovávají požadavky HTTP (zasílání zpráv, úlohy na pozadí atd.).</span><span class="sxs-lookup"><span data-stu-id="e6440-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="e6440-190">Další informace naleznete [v tématu Obecný hostitel rozhraní .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e6440-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="e6440-191">Aktualizované šablony SPA</span><span class="sxs-lookup"><span data-stu-id="e6440-191">Updated SPA templates</span></span>

<span data-ttu-id="e6440-192">Jednoduché šablony aplikace pro úhlové, reagovat a reagovat s Redux jsou aktualizovány tak, aby používaly standardní struktury projektu a vytvářely systémy pro každou architekturu.</span><span class="sxs-lookup"><span data-stu-id="e6440-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="e6440-193">Úhlová šablona je založena na úhlovém cli a šablony React jsou založeny na create-react-app.</span><span class="sxs-lookup"><span data-stu-id="e6440-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="e6440-194">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="e6440-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="e6440-195">Razor Stránky hledat Razor aktiva</span><span class="sxs-lookup"><span data-stu-id="e6440-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="e6440-196">V 2.1, Razor Stránky hledat Razor datových zdrojů (například rozložení a částečné) v následujících adresářů v uvedeném pořadí:</span><span class="sxs-lookup"><span data-stu-id="e6440-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="e6440-197">Aktuální stránky.</span><span class="sxs-lookup"><span data-stu-id="e6440-197">Current Pages folder.</span></span>
1. <span data-ttu-id="e6440-198">*/Stránky/Sdílené/*</span><span class="sxs-lookup"><span data-stu-id="e6440-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="e6440-199">*/Zobrazení/Sdíleno/*</span><span class="sxs-lookup"><span data-stu-id="e6440-199">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="e6440-200">Holicí strojek stránky v oblasti</span><span class="sxs-lookup"><span data-stu-id="e6440-200">Razor Pages in an area</span></span>

<span data-ttu-id="e6440-201">Razor Pages nyní podporují [oblasti](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="e6440-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="e6440-202">Chcete-li zobrazit příklad oblastí, vytvořte novou webovou aplikaci Razor Pages s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="e6440-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="e6440-203">Webová aplikace Razor Pages s jednotlivými uživatelskými účty obsahuje */Oblasti/Identity/Pages*.</span><span class="sxs-lookup"><span data-stu-id="e6440-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="e6440-204">Verze kompatibility MVC</span><span class="sxs-lookup"><span data-stu-id="e6440-204">MVC compatibility version</span></span>

<span data-ttu-id="e6440-205">Tato <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> metoda umožňuje aplikaci vyjádřit výslovný nesouhlas nebo odhlásit se z potenciálně narušujících změn chování zavedených v ASP.NET core MVC 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="e6440-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="e6440-206">Další informace naleznete v tématu <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="e6440-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="e6440-207">Migrace z 2.0 na 2.1</span><span class="sxs-lookup"><span data-stu-id="e6440-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="e6440-208">Viz [Migrace z ASP.NET jádra 2.0 do 2.1](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="e6440-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="e6440-209">Další informace</span><span class="sxs-lookup"><span data-stu-id="e6440-209">Additional information</span></span>

<span data-ttu-id="e6440-210">Úplný seznam změn naleznete v [ASP.NET základní poznámky k verzi 2.1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="e6440-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span></span>
