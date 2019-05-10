---
title: Co je nového v ASP.NET Core 2.1
author: isaac2004
description: Informace o nových funkcích v ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2019
uid: aspnetcore-2.1
ms.openlocfilehash: 359f961db768b9048427c8ab296ee3e035879408
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086397"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="70aa2-103">Co je nového v ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="70aa2-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="70aa2-104">Tento článek se soustředí na nejdůležitější změny v ASP.NET Core 2.1 s odkazy na relevantní dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="70aa2-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## <a name="signalr"></a><span data-ttu-id="70aa2-105">SignalR</span><span class="sxs-lookup"><span data-stu-id="70aa2-105">SignalR</span></span>

<span data-ttu-id="70aa2-106">Přepsali jsme SignalR pro ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="70aa2-106">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="70aa2-107">Funkce SignalR technologie ASP.NET Core zahrnuje celou řadu vylepšení:</span><span class="sxs-lookup"><span data-stu-id="70aa2-107">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="70aa2-108">Zjednodušený model horizontální navýšení kapacity.</span><span class="sxs-lookup"><span data-stu-id="70aa2-108">A simplified scale-out model.</span></span>
* <span data-ttu-id="70aa2-109">Nový klient JavaScriptu se žádné jQuery závislostí.</span><span class="sxs-lookup"><span data-stu-id="70aa2-109">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="70aa2-110">Nové kompaktní binární protokol založený na MessagePack.</span><span class="sxs-lookup"><span data-stu-id="70aa2-110">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="70aa2-111">Podpora pro vlastní protokoly.</span><span class="sxs-lookup"><span data-stu-id="70aa2-111">Support for custom protocols.</span></span>
* <span data-ttu-id="70aa2-112">Nový model datového proudu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="70aa2-112">A new streaming response model.</span></span>
* <span data-ttu-id="70aa2-113">Podpora pro klienty založené na úplné objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="70aa2-113">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="70aa2-114">Další informace najdete v tématu [funkce SignalR technologie ASP.NET Core](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="70aa2-114">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="70aa2-115">Knihovny tříd Razor</span><span class="sxs-lookup"><span data-stu-id="70aa2-115">Razor class libraries</span></span>

<span data-ttu-id="70aa2-116">ASP.NET Core 2.1 usnadňuje sestavení a zahrnout do knihovny uživatelského rozhraní založeného na Razor a sdílejte ji napříč více projektů.</span><span class="sxs-lookup"><span data-stu-id="70aa2-116">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="70aa2-117">Nová sada Razor SDK umožňuje sestavování Razor soubory do projektu knihovny tříd, který se dá zabalit do balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="70aa2-117">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="70aa2-118">Zobrazení a stránky v knihovnách se automaticky zjišťují a může být přepsána aplikace.</span><span class="sxs-lookup"><span data-stu-id="70aa2-118">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="70aa2-119">Díky integraci Razor kompilace do sestavení:</span><span class="sxs-lookup"><span data-stu-id="70aa2-119">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="70aa2-120">Čas spuštění aplikace je výrazně rychlejší.</span><span class="sxs-lookup"><span data-stu-id="70aa2-120">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="70aa2-121">Rychlé aktualizace zobrazení syntaxe Razor a stránky v době běhu jsou stále k dispozici jako součást pracovního postupu iterativního vývoje.</span><span class="sxs-lookup"><span data-stu-id="70aa2-121">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="70aa2-122">Další informace najdete v tématu [vytvářet opakovaně použitelné uživatelské rozhraní pomocí projektu knihovny tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="70aa2-122">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="70aa2-123">Knihovna uživatelského rozhraní identity & generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="70aa2-123">Identity UI library & scaffolding</span></span>

<span data-ttu-id="70aa2-124">ASP.NET Core 2.1 poskytuje [ASP.NET Core Identity](xref:security/authentication/identity) jako [knihovny tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="70aa2-124">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="70aa2-125">Aplikace, které zahrnují Identity můžete použít nový generátor Identity selektivně Přidání zdrojového kódu obsažen v knihovně Razor třídu (RCL) Identity.</span><span class="sxs-lookup"><span data-stu-id="70aa2-125">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="70aa2-126">Můžete chtít vygenerování zdrojového kódu, abyste mohli upravit kód a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="70aa2-126">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="70aa2-127">Například může dát pokyn generátor pro generování kódu při registraci.</span><span class="sxs-lookup"><span data-stu-id="70aa2-127">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="70aa2-128">Generovaného kódu mají přednost před stejný kód v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="70aa2-128">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="70aa2-129">Aplikací, které toho **není** zahrnout ověřování můžete použít Generátor Identity a přidejte příslušný balíček RCL Identity.</span><span class="sxs-lookup"><span data-stu-id="70aa2-129">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="70aa2-130">Máte možnost výběru Identity kód chcete vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="70aa2-130">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="70aa2-131">Další informace najdete v tématu [Identity vygenerované uživatelské rozhraní v projektech ASP.NET Core](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="70aa2-131">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="70aa2-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="70aa2-132">HTTPS</span></span>

<span data-ttu-id="70aa2-133">Větší zaměření na zabezpečení a ochrana osobních údajů povolení HTTPS pro web apps je důležité.</span><span class="sxs-lookup"><span data-stu-id="70aa2-133">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="70aa2-134">Vynucení protokolu HTTPS se stává stále více striktní na webu.</span><span class="sxs-lookup"><span data-stu-id="70aa2-134">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="70aa2-135">Servery, které nechcete používat protokol HTTPS se považují za nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="70aa2-135">Sites that don’t use HTTPS are considered insecure.</span></span> <span data-ttu-id="70aa2-136">K vynucení, webové funkce musí být volána ze zabezpečeného kontextu spuštění prohlížeče (chromu, Mozilla).</span><span class="sxs-lookup"><span data-stu-id="70aa2-136">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="70aa2-137">[GDPR](xref:security/gdpr) vyžaduje použití protokolu HTTPS do ochrany osobních údajů uživatele.</span><span class="sxs-lookup"><span data-stu-id="70aa2-137">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="70aa2-138">Při použití protokolu HTTPS v produkčním prostředí je velmi důležité, pomocí protokolu HTTPS ve vývoji pomáhají zabránit problémům v nasazení (například nezabezpečené odkazy).</span><span class="sxs-lookup"><span data-stu-id="70aa2-138">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="70aa2-139">ASP.NET Core 2.1 zahrnuje množství vylepšení, které usnadňují používání protokolu HTTPS ve vývoji a konfigurace protokolu HTTPS v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="70aa2-139">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="70aa2-140">Další informace najdete v tématu [vynucení HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="70aa2-140">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="70aa2-141">Na ve výchozím nastavení</span><span class="sxs-lookup"><span data-stu-id="70aa2-141">On by default</span></span>

<span data-ttu-id="70aa2-142">K usnadnění vývoje zabezpečeného webu, je teď ve výchozím nastavení povolenou komunikací HTTPS.</span><span class="sxs-lookup"><span data-stu-id="70aa2-142">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="70aa2-143">Od verze 2.1, Kestrel naslouchá na `https://localhost:5001` Pokud je k dispozici certifikát pro místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="70aa2-143">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="70aa2-144">Certifikát pro vývoj se vytvoří:</span><span class="sxs-lookup"><span data-stu-id="70aa2-144">A development certificate is created:</span></span>

* <span data-ttu-id="70aa2-145">Jako součást první spuštění prostředí .NET Core SDK, při použití sady SDK pro první.</span><span class="sxs-lookup"><span data-stu-id="70aa2-145">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="70aa2-146">Ručně pomocí nového `dev-certs` nástroj.</span><span class="sxs-lookup"><span data-stu-id="70aa2-146">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="70aa2-147">Spustit `dotnet dev-certs https --trust` důvěřovat certifikátům.</span><span class="sxs-lookup"><span data-stu-id="70aa2-147">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="70aa2-148">Přesměrování protokolu HTTPS a vynucení</span><span class="sxs-lookup"><span data-stu-id="70aa2-148">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="70aa2-149">Webové aplikace obvykle potřebují naslouchání protokolu HTTP a HTTPS, ale pak přesměrovat všechen provoz protokolu HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="70aa2-149">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="70aa2-150">V 2.1 specializované middleware přesměrování HTTPS, které inteligentně přesměruje na základě přítomnosti konfigurace nebo je zavedený vázaných portů.</span><span class="sxs-lookup"><span data-stu-id="70aa2-150">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="70aa2-151">Použití protokolu HTTPS lze dále vynutit použití [HTTP striktní přenosu zabezpečení protokolu (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="70aa2-151">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="70aa2-152">HSTS instruuje prohlížeče vždy přístup k webu přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="70aa2-152">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="70aa2-153">ASP.NET Core 2.1 přidá HSTS middleware, který podporuje možnosti pro maximální stáří, subdomén, a HSTS předběžné načtení seznamu.</span><span class="sxs-lookup"><span data-stu-id="70aa2-153">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="70aa2-154">Konfigurace pro produkční prostředí</span><span class="sxs-lookup"><span data-stu-id="70aa2-154">Configuration for production</span></span>

<span data-ttu-id="70aa2-155">V produkčním prostředí musí být explicitně nakonfigurován protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="70aa2-155">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="70aa2-156">V 2.1 výchozí konfigurace schématu konfigurace HTTPS pro Kestrel přidala.</span><span class="sxs-lookup"><span data-stu-id="70aa2-156">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="70aa2-157">Aplikace je možné nakonfigurovat na použití:</span><span class="sxs-lookup"><span data-stu-id="70aa2-157">Apps can be configured to use:</span></span>

* <span data-ttu-id="70aa2-158">Více koncových bodů včetně adresy URL.</span><span class="sxs-lookup"><span data-stu-id="70aa2-158">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="70aa2-159">Další informace najdete v tématu [implementaci Kestrel webového serveru: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="70aa2-159">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="70aa2-160">Certifikát, který se má použít pro protokol HTTPS ze souboru na disku nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="70aa2-160">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="70aa2-161">GDPR</span><span class="sxs-lookup"><span data-stu-id="70aa2-161">GDPR</span></span>

<span data-ttu-id="70aa2-162">ASP.NET Core nabízí rozhraní API a šablony, které vám pomohou splnit některé [EU obecného Regulation (GDPR)](https://www.eugdpr.org/) požadavky.</span><span class="sxs-lookup"><span data-stu-id="70aa2-162">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="70aa2-163">Další informace najdete v tématu [podpory nařízení GDPR v ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="70aa2-163">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="70aa2-164">A [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ukazuje způsob použití a umožňuje testovat Většina rozhraní API přidat do šablony ASP.NET Core 2.1 a GDPR Rozšiřovací body.</span><span class="sxs-lookup"><span data-stu-id="70aa2-164">A [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="70aa2-165">Integrační testy</span><span class="sxs-lookup"><span data-stu-id="70aa2-165">Integration tests</span></span>

<span data-ttu-id="70aa2-166">Nový balíček je zavedená, že test zjednodušují vytváření a spouštění.</span><span class="sxs-lookup"><span data-stu-id="70aa2-166">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="70aa2-167">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) balíček provádí následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="70aa2-167">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="70aa2-168">Zkopíruje soubor závislostí (*\*.deps*) z otestované aplikace do testovacího projektu *bin* složky.</span><span class="sxs-lookup"><span data-stu-id="70aa2-168">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="70aa2-169">Nastaví obsahu kořenové do kořenového adresáře projektu testované aplikace, aby statické soubory a stránky a zobrazení se našly při spouštění testů.</span><span class="sxs-lookup"><span data-stu-id="70aa2-169">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="70aa2-170">Poskytuje [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) tříd zjednodušení spuštění testované aplikaci pomocí [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="70aa2-170">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="70aa2-171">Následující test používá [xUnit](https://xunit.github.io/) zkontrolujte, že načte indexovou stránku s stavový kód úspěchu a správné hlavičky Content-Type:</span><span class="sxs-lookup"><span data-stu-id="70aa2-171">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

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

<span data-ttu-id="70aa2-172">Další informace najdete v tématu [integrační testy](xref:test/integration-tests) tématu.</span><span class="sxs-lookup"><span data-stu-id="70aa2-172">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="70aa2-173">[Objektu ApiController], ActionResult\<T ></span><span class="sxs-lookup"><span data-stu-id="70aa2-173">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="70aa2-174">ASP.NET Core 2.1 přidá nový programovací vytváření názvů usnadňují čisté a popisný webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="70aa2-174">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="70aa2-175">`ActionResult<T>` je přidán nový typ umožňuje aplikacím vracet typ odpovědi nebo jiné akce výsledek (podobně jako IActionResult), při stále označující typ odpovědi.</span><span class="sxs-lookup"><span data-stu-id="70aa2-175">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="70aa2-176">`[ApiController]` Atribut se také přidal jako způsob, jak vyjádřit výslovný souhlas s konvencí specifických pro webové rozhraní API a chování.</span><span class="sxs-lookup"><span data-stu-id="70aa2-176">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="70aa2-177">Další informace najdete v tématu [vytvářet webová rozhraní API pomocí ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="70aa2-177">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="70aa2-178">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="70aa2-178">IHttpClientFactory</span></span>

<span data-ttu-id="70aa2-179">ASP.NET Core 2.1 obsahuje nový `IHttpClientFactory` služba, která usnadňuje konfiguraci a využití instancí `HttpClient` v aplikacích.</span><span class="sxs-lookup"><span data-stu-id="70aa2-179">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="70aa2-180">`HttpClient` již obsahuje koncept delegování obslužné rutiny, které by mohly být propojeny pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="70aa2-180">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="70aa2-181">Objekt pro vytváření:</span><span class="sxs-lookup"><span data-stu-id="70aa2-181">The factory:</span></span>

* <span data-ttu-id="70aa2-182">Díky registraci instance `HttpClient` za mnohem intuitivnější pojmenované klienta.</span><span class="sxs-lookup"><span data-stu-id="70aa2-182">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="70aa2-183">Implementuje Polly obslužnou rutinu, která umožňuje Polly zásad se použije pro opakování, CircuitBreakers atd.</span><span class="sxs-lookup"><span data-stu-id="70aa2-183">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="70aa2-184">Další informace najdete v tématu [zahájit požadavky HTTP](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="70aa2-184">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="70aa2-185">Konfigurace kestrel přenosu</span><span class="sxs-lookup"><span data-stu-id="70aa2-185">Kestrel transport configuration</span></span>

<span data-ttu-id="70aa2-186">Verze technologie ASP.NET Core 2.1 Kestrel pro výchozí přenos je již podle Libuv ale místo toho podle spravované sokety.</span><span class="sxs-lookup"><span data-stu-id="70aa2-186">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="70aa2-187">Další informace najdete v tématu [implementaci Kestrel webového serveru: Konfigurace přenosu](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="70aa2-187">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="70aa2-188">Tvůrce obecný hostitele</span><span class="sxs-lookup"><span data-stu-id="70aa2-188">Generic host builder</span></span>

<span data-ttu-id="70aa2-189">Obecný Tvůrce hostitele (`HostBuilder`) byl zaveden.</span><span class="sxs-lookup"><span data-stu-id="70aa2-189">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="70aa2-190">Tento Tvůrce lze použít pro aplikace, které není zpracovávají požadavky HTTP (zasílání zpráv, úlohy na pozadí, atd.).</span><span class="sxs-lookup"><span data-stu-id="70aa2-190">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="70aa2-191">Další informace najdete v tématu [obecný hostitele .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="70aa2-191">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="70aa2-192">Aktualizace šablon SPA</span><span class="sxs-lookup"><span data-stu-id="70aa2-192">Updated SPA templates</span></span>

<span data-ttu-id="70aa2-193">Jednostránková aplikace šablony pro Angular, React a React s Reduxem se aktualizují, aby používat standardní projekt struktury a systémy pro každé rozhraní, sestavení.</span><span class="sxs-lookup"><span data-stu-id="70aa2-193">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="70aa2-194">Angular šablona je založena na Angular CLI a šablon React jsou založeny na vytvoření aplikace react.</span><span class="sxs-lookup"><span data-stu-id="70aa2-194">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="70aa2-195">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="70aa2-195">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="70aa2-196">Stránky Razor vyhledávat Razor</span><span class="sxs-lookup"><span data-stu-id="70aa2-196">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="70aa2-197">2.1 stránky Razor vyhledejte Razor prostředků (např. rozložení a částečných zobrazení) v následujících adresářích v uvedeném pořadí:</span><span class="sxs-lookup"><span data-stu-id="70aa2-197">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="70aa2-198">Aktuální složce stránky.</span><span class="sxs-lookup"><span data-stu-id="70aa2-198">Current Pages folder.</span></span>
1. <span data-ttu-id="70aa2-199">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="70aa2-199">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="70aa2-200">*/Views/sdílené /*</span><span class="sxs-lookup"><span data-stu-id="70aa2-200">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="70aa2-201">Stránky Razor v oblasti.</span><span class="sxs-lookup"><span data-stu-id="70aa2-201">Razor Pages in an area</span></span>

<span data-ttu-id="70aa2-202">Teď podporují stránky Razor [oblasti](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="70aa2-202">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="70aa2-203">Chcete-li zobrazit příklad oblasti, vytvořte nová webová aplikace Razor Pages s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="70aa2-203">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="70aa2-204">Webové aplikace stránky Razor pomocí jednotlivých uživatelských účtů zahrnuje */Areas/Identity/Pages*.</span><span class="sxs-lookup"><span data-stu-id="70aa2-204">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="70aa2-205">Verze kompatibility MVC</span><span class="sxs-lookup"><span data-stu-id="70aa2-205">MVC compatibility version</span></span>

<span data-ttu-id="70aa2-206"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda umožňuje aplikacím vyjádřit výslovný souhlas nebo výslovný nesouhlas s potenciálně rozbíjející změny chování zavedení v ASP.NET Core MVC 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="70aa2-206">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="70aa2-207">Další informace naleznete v tématu <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="70aa2-207">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="70aa2-208">Migrace z verze 2.0 na verzi 2.1</span><span class="sxs-lookup"><span data-stu-id="70aa2-208">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="70aa2-209">Zobrazit [migrovat z ASP.NET Core 2.0 k 2.1](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="70aa2-209">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="70aa2-210">Další informace</span><span class="sxs-lookup"><span data-stu-id="70aa2-210">Additional information</span></span>

<span data-ttu-id="70aa2-211">Úplný seznam změn, najdete v článku [poznámky k verzi pro ASP.NET Core 2.1](https://github.com/aspnet/Home/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="70aa2-211">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/aspnet/Home/releases/tag/2.1.0).</span></span>
