---
uid: web-api/overview/security/enabling-cross-origin-requests-in-web-api
title: Povolení žádostí nepůvodního zdroje v rozhraní ASP.NET Web API 2 | Dokumentace Microsoftu
author: MikeWasson
description: Ukazuje, jak k podpoře sdílení prostředků mezi zdroji (CORS) v rozhraní ASP.NET Web API.
ms.author: riande
ms.date: 01/29/2019
ms.assetid: 9b265a5a-6a70-4a82-adce-2d7c56ae8bdd
msc.legacyurl: /web-api/overview/security/enabling-cross-origin-requests-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 97a0027194b019b09e220493dcb593e682027fe3
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428444"
---
<a name="enable-cross-origin-requests-in-aspnet-web-api-2"></a><span data-ttu-id="2782e-103">Povolení žádostí nepůvodního v ASP.NET Web API 2</span><span class="sxs-lookup"><span data-stu-id="2782e-103">Enable cross-origin requests in ASP.NET Web API 2</span></span>
====================
<span data-ttu-id="2782e-104">podle [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="2782e-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

> <span data-ttu-id="2782e-105">Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu.</span><span class="sxs-lookup"><span data-stu-id="2782e-105">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="2782e-106">Toto omezení je volána *zásada stejného zdroje*a brání škodlivým webům ve čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="2782e-106">This restriction is called the *same-origin policy*, and prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="2782e-107">Ale v některých případech můžete chtít nechat ostatních lokalit volání webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2782e-107">However, sometimes you might want to let other sites call your web API.</span></span>
>
> <span data-ttu-id="2782e-108">[Mezi sdílení zdrojů původu](http://www.w3.org/TR/cors/) (CORS) je standard W3C, která umožňuje server zmírnit zásadu stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="2782e-108">[Cross Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) is a W3C standard that allows a server to relax the same-origin policy.</span></span> <span data-ttu-id="2782e-109">Pomocí CORS, server explicitně můžou některé požadavky cross-origin zatímco jiné odmítnout.</span><span class="sxs-lookup"><span data-stu-id="2782e-109">Using CORS, a server can explicitly allow some cross-origin requests while rejecting others.</span></span> <span data-ttu-id="2782e-110">CORS je bezpečnější a flexibilnější, než starší techniky, jako [JSONP](http://en.wikipedia.org/wiki/JSONP).</span><span class="sxs-lookup"><span data-stu-id="2782e-110">CORS is safer and more flexible than earlier techniques such as [JSONP](http://en.wikipedia.org/wiki/JSONP).</span></span> <span data-ttu-id="2782e-111">Tento kurz ukazuje postupy při povolení CORS v aplikace webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2782e-111">This tutorial shows how to enable CORS in your Web API application.</span></span>
>
> ## <a name="software-used-in-the-tutorial"></a><span data-ttu-id="2782e-112">V tomto kurzu použili softwaru</span><span class="sxs-lookup"><span data-stu-id="2782e-112">Software used in the tutorial</span></span>
>
> - [<span data-ttu-id="2782e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2782e-113">Visual Studio</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
> - <span data-ttu-id="2782e-114">Web API 2.2</span><span class="sxs-lookup"><span data-stu-id="2782e-114">Web API 2.2</span></span>

## <a name="introduction"></a><span data-ttu-id="2782e-115">Úvod</span><span class="sxs-lookup"><span data-stu-id="2782e-115">Introduction</span></span>

<span data-ttu-id="2782e-116">Tento kurz ukazuje, že podpora CORS v rozhraní ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="2782e-116">This tutorial demonstrates CORS support in ASP.NET Web API.</span></span> <span data-ttu-id="2782e-117">Začneme tím, že vytvoříte dva projekty ASP.NET – jeden volané "Webová služba", který je hostitelem kontroler Web API, a ostatní volané "WebClient", která volá webové služby.</span><span class="sxs-lookup"><span data-stu-id="2782e-117">We'll start by creating two ASP.NET projects – one called "WebService", which hosts a Web API controller, and the other called "WebClient", which calls WebService.</span></span> <span data-ttu-id="2782e-118">Protože jsou dvě aplikace hostované v různých doménách, je požadavek AJAX z webový klient webové služby žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="2782e-118">Because the two applications are hosted at different domains, an AJAX request from WebClient to WebService is a cross-origin request.</span></span>

![](enabling-cross-origin-requests-in-web-api/_static/image1.png)

### <a name="what-is-same-origin"></a><span data-ttu-id="2782e-119">Co je "stejné zdroj"?</span><span class="sxs-lookup"><span data-stu-id="2782e-119">What is "same origin"?</span></span>

<span data-ttu-id="2782e-120">Dvě adresy URL mají stejný původ, pokud mají stejné schémata, hostitele a porty.</span><span class="sxs-lookup"><span data-stu-id="2782e-120">Two URLs have the same origin if they have identical schemes, hosts, and ports.</span></span> <span data-ttu-id="2782e-121">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span><span class="sxs-lookup"><span data-stu-id="2782e-121">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span></span>

<span data-ttu-id="2782e-122">Tyto dvě adresy URL mají stejného původu:</span><span class="sxs-lookup"><span data-stu-id="2782e-122">These two URLs have the same origin:</span></span>

- `http://example.com/foo.html`
- `http://example.com/bar.html`

<span data-ttu-id="2782e-123">Tyto adresy URL mají různé zdroje než ta předchozí dvě:</span><span class="sxs-lookup"><span data-stu-id="2782e-123">These URLs have different origins than the previous two:</span></span>

- <span data-ttu-id="2782e-124">`http://example.net` -Jinou doménu</span><span class="sxs-lookup"><span data-stu-id="2782e-124">`http://example.net` - Different domain</span></span>
- <span data-ttu-id="2782e-125">`http://example.com:9000/foo.html` -Jiný port</span><span class="sxs-lookup"><span data-stu-id="2782e-125">`http://example.com:9000/foo.html` - Different port</span></span>
- <span data-ttu-id="2782e-126">`https://example.com/foo.html` -Jiné schéma</span><span class="sxs-lookup"><span data-stu-id="2782e-126">`https://example.com/foo.html` - Different scheme</span></span>
- <span data-ttu-id="2782e-127">`http://www.example.com/foo.html` – Různé subdomény</span><span class="sxs-lookup"><span data-stu-id="2782e-127">`http://www.example.com/foo.html` - Different subdomain</span></span>

> [!NOTE]
> <span data-ttu-id="2782e-128">Aplikace Internet Explorer nebere v úvahu port při porovnání zdrojů.</span><span class="sxs-lookup"><span data-stu-id="2782e-128">Internet Explorer does not consider the port when comparing origins.</span></span>

## <a name="create-the-webservice-project"></a><span data-ttu-id="2782e-129">Vytvoření projektu webové služby</span><span class="sxs-lookup"><span data-stu-id="2782e-129">Create the WebService project</span></span>

> [!NOTE]
> <span data-ttu-id="2782e-130">Této části se předpokládá, že už víte, jak vytvořit projekty webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2782e-130">This section assumes you already know how to create Web API projects.</span></span> <span data-ttu-id="2782e-131">Pokud ne, přečtěte si téma [Začínáme s rozhraním ASP.NET Web API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="2782e-131">If not, see [Getting Started with ASP.NET Web API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).</span></span>

1. <span data-ttu-id="2782e-132">Spusťte sadu Visual Studio a vytvořte nový **webová aplikace ASP.NET (.NET Framework)** projektu.</span><span class="sxs-lookup"><span data-stu-id="2782e-132">Start Visual Studio and create a new **ASP.NET Web Application (.NET Framework)** project.</span></span>
2. <span data-ttu-id="2782e-133">V **nová webová aplikace ASP.NET** dialogové okno, vyberte **prázdný** šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="2782e-133">In the **New ASP.NET Web Application** dialog box, select the **Empty** project template.</span></span> <span data-ttu-id="2782e-134">V části **přidat složky a základní odkazy pro**, vyberte **webového rozhraní API** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="2782e-134">Under **Add folders and core references for**, select the **Web API** checkbox.</span></span>

   ![ASP.NET dialogové okno nového projektu v sadě Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog.png)

3. <span data-ttu-id="2782e-136">Přidat kontroler Web API s názvem `TestController` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2782e-136">Add a Web API controller named `TestController` with the following code:</span></span>

   [!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample1.cs)]

4. <span data-ttu-id="2782e-137">Můžete spustit aplikaci místně nebo nasazení do Azure.</span><span class="sxs-lookup"><span data-stu-id="2782e-137">You can run the application locally or deploy to Azure.</span></span> <span data-ttu-id="2782e-138">(Pro snímky obrazovky v tomto kurzu, aplikace nasadí do Azure App Service Web Apps.) Pokud chcete ověřit, že je pracovní webové rozhraní API, přejděte na `http://hostname/api/test/`, kde *název hostitele* je doména, kam jste nasadili aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2782e-138">(For the screenshots in this tutorial, the app deploys to Azure App Service Web Apps.) To verify that the web API is working, navigate to `http://hostname/api/test/`, where *hostname* is the domain where you deployed the application.</span></span> <span data-ttu-id="2782e-139">Měli byste vidět text odpovědi &quot;získat: Testovací zpráva&quot;.</span><span class="sxs-lookup"><span data-stu-id="2782e-139">You should see the response text, &quot;GET: Test Message&quot;.</span></span>

   ![Webové prohlížeče zobrazující zkušební zprávy](enabling-cross-origin-requests-in-web-api/_static/image4.png)

## <a name="create-the-webclient-project"></a><span data-ttu-id="2782e-141">Vytvoření projektu WebClient</span><span class="sxs-lookup"><span data-stu-id="2782e-141">Create the WebClient project</span></span>

1. <span data-ttu-id="2782e-142">Vytvořte další **webová aplikace ASP.NET (.NET Framework)** projektu a vyberte **MVC** šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="2782e-142">Create another **ASP.NET Web Application (.NET Framework)** project and select the **MVC** project template.</span></span> <span data-ttu-id="2782e-143">Volitelně vyberte **změna ověřování** > **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="2782e-143">Optionally, select **Change Authentication** > **No Authentication**.</span></span> <span data-ttu-id="2782e-144">Pro účely tohoto kurzu nepotřebujete ověřování.</span><span class="sxs-lookup"><span data-stu-id="2782e-144">You don't need authentication for this tutorial.</span></span>

   ![Šablona MVC v dialogu Nový projekt ASP.NET v sadě Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog-mvc.png)

2. <span data-ttu-id="2782e-146">V **Průzkumníka řešení**, otevřete soubor *Views/Home/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2782e-146">In **Solution Explorer**, open the file *Views/Home/Index.cshtml*.</span></span> <span data-ttu-id="2782e-147">Nahraďte kód v tomto souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2782e-147">Replace the code in this file with the following:</span></span>

   [!code-cshtml[Main](enabling-cross-origin-requests-in-web-api/samples/sample2.cshtml?highlight=13)]

   <span data-ttu-id="2782e-148">Pro *serviceUrl* proměnné, použijte identifikátor URI aplikace webové služby.</span><span class="sxs-lookup"><span data-stu-id="2782e-148">For the *serviceUrl* variable, use the URI of the WebService app.</span></span>

3. <span data-ttu-id="2782e-149">Místní spuštění aplikace WebClient nebo ji publikovat na jiný web.</span><span class="sxs-lookup"><span data-stu-id="2782e-149">Run the WebClient app locally or publish it to another website.</span></span>

<span data-ttu-id="2782e-150">Po kliknutí na tlačítko "Vyzkoušet" požadavek AJAX se odešle do aplikace webové služby pomocí protokolu HTTP metody uvedené v rozevíracím seznamu (GET, POST a PUT).</span><span class="sxs-lookup"><span data-stu-id="2782e-150">When you click the "Try It" button, an AJAX request is submitted to the WebService app using the HTTP method listed in the dropdown box (GET, POST, or PUT).</span></span> <span data-ttu-id="2782e-151">To umožňuje prozkoumat různé požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="2782e-151">This lets you examine different cross-origin requests.</span></span> <span data-ttu-id="2782e-152">Aplikace webové služby v současné době nepodporuje CORS, takže pokud kliknete na tlačítko, zobrazí se vám chyba.</span><span class="sxs-lookup"><span data-stu-id="2782e-152">Currently, the WebService app does not support CORS, so if you click the button you'll get an error.</span></span>

![Chyba "Zkuste to" v prohlížeči](enabling-cross-origin-requests-in-web-api/_static/image7.png)

> [!NOTE]
> <span data-ttu-id="2782e-154">Pokud sledujete přenos pomocí protokolu HTTP v nástroji, jako jsou [Fiddler](https://www.telerik.com/fiddler), uvidíte, že do prohlížeče odeslat požadavek na získání a úspěšného vykonání požadavku, ale volání jazyka AJAX vrátí chybu.</span><span class="sxs-lookup"><span data-stu-id="2782e-154">If you watch the HTTP traffic in a tool like [Fiddler](https://www.telerik.com/fiddler), you'll see that the browser does send the GET request, and the request succeeds, but the AJAX call returns an error.</span></span> <span data-ttu-id="2782e-155">Je důležité pochopit, že zásada stejného zdroje nezabraňuje prohlížeče z *odesílání* požadavku.</span><span class="sxs-lookup"><span data-stu-id="2782e-155">It's important to understand that same-origin policy does not prevent the browser from *sending* the request.</span></span> <span data-ttu-id="2782e-156">Místo toho brání aplikaci v zobrazení *odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="2782e-156">Instead, it prevents the application from seeing the *response*.</span></span>

![Ladicí program webové aplikaci Fiddler zobrazují webové požadavky](enabling-cross-origin-requests-in-web-api/_static/image8.png)

## <a name="enable-cors"></a><span data-ttu-id="2782e-158">Povolení CORS</span><span class="sxs-lookup"><span data-stu-id="2782e-158">Enable CORS</span></span>

<span data-ttu-id="2782e-159">Nyní Pojďme povolení CORS v app webové služby.</span><span class="sxs-lookup"><span data-stu-id="2782e-159">Now let's enable CORS in the WebService app.</span></span> <span data-ttu-id="2782e-160">Nejprve přidejte balíček CORS NuGet.</span><span class="sxs-lookup"><span data-stu-id="2782e-160">First, add the CORS NuGet package.</span></span> <span data-ttu-id="2782e-161">V aplikaci Visual Studio z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="2782e-161">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="2782e-162">V okně konzoly Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2782e-162">In the Package Manager Console window, type the following command:</span></span>

[!code-powershell[Main](enabling-cross-origin-requests-in-web-api/samples/sample3.ps1)]

<span data-ttu-id="2782e-163">Tento příkaz nainstaluje nejnovější balíček a aktualizuje všechny závislosti, včetně základní webové rozhraní API knihovny.</span><span class="sxs-lookup"><span data-stu-id="2782e-163">This command installs the latest package and updates all dependencies, including the core Web API libraries.</span></span> <span data-ttu-id="2782e-164">Použití `-Version` příznak pro cílení na konkrétní verzi.</span><span class="sxs-lookup"><span data-stu-id="2782e-164">Use the `-Version` flag to target a specific version.</span></span> <span data-ttu-id="2782e-165">Vyžaduje balíček CORS webového rozhraní API 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="2782e-165">The CORS package requires Web API 2.0 or later.</span></span>

<span data-ttu-id="2782e-166">Otevřete soubor *aplikace\_Start/WebApiConfig.cs*.</span><span class="sxs-lookup"><span data-stu-id="2782e-166">Open the file *App\_Start/WebApiConfig.cs*.</span></span> <span data-ttu-id="2782e-167">Přidejte následující kód, který **WebApiConfig.Register** metody:</span><span class="sxs-lookup"><span data-stu-id="2782e-167">Add the following code to the **WebApiConfig.Register** method:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample4.cs?highlight=9)]

<span data-ttu-id="2782e-168">V dalším kroku přidejte **[EnableCors]** atribut `TestController` třídy:</span><span class="sxs-lookup"><span data-stu-id="2782e-168">Next, add the **[EnableCors]** attribute to the `TestController` class:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample5.cs?highlight=3,7)]

<span data-ttu-id="2782e-169">Pro *zdroje* parametrů, použijte identifikátor URI, kam jste nasadili webový klient aplikace.</span><span class="sxs-lookup"><span data-stu-id="2782e-169">For the *origins* parameter, use the URI where you deployed the WebClient application.</span></span> <span data-ttu-id="2782e-170">Díky tomu požadavky cross-origin z WebClient, při stále Probíhá zakazování všech ostatních požadavků mezi doménami.</span><span class="sxs-lookup"><span data-stu-id="2782e-170">This allows cross-origin requests from WebClient, while still disallowing all other cross-domain requests.</span></span> <span data-ttu-id="2782e-171">Později, můžu budete popisují parametry **[EnableCors]** podrobněji.</span><span class="sxs-lookup"><span data-stu-id="2782e-171">Later, I'll describe the parameters for **[EnableCors]** in more detail.</span></span>

<span data-ttu-id="2782e-172">Nezahrnují dopředné lomítko na konci *zdroje* adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2782e-172">Do not include a forward slash at the end of the *origins* URL.</span></span>

<span data-ttu-id="2782e-173">Znovu nasaďte aktualizovanou aplikaci webové služby.</span><span class="sxs-lookup"><span data-stu-id="2782e-173">Redeploy the updated WebService application.</span></span> <span data-ttu-id="2782e-174">Není nutné aktualizovat WebClient.</span><span class="sxs-lookup"><span data-stu-id="2782e-174">You don't need to update WebClient.</span></span> <span data-ttu-id="2782e-175">Nyní požadavek AJAX z WebClient uspěli.</span><span class="sxs-lookup"><span data-stu-id="2782e-175">Now the AJAX request from WebClient should succeed.</span></span> <span data-ttu-id="2782e-176">Metody GET a PUT, POST všechny povolené.</span><span class="sxs-lookup"><span data-stu-id="2782e-176">The GET, PUT, and POST methods are all allowed.</span></span>

![Webové prohlížeče zobrazující úspěšné testovací zpráva](enabling-cross-origin-requests-in-web-api/_static/image9.png)

## <a name="how-cors-works"></a><span data-ttu-id="2782e-178">Jak funguje CORS</span><span class="sxs-lookup"><span data-stu-id="2782e-178">How CORS Works</span></span>

<span data-ttu-id="2782e-179">Tato část popisuje, co se stane, že v požadavku CORS, na úrovni zprávy HTTP.</span><span class="sxs-lookup"><span data-stu-id="2782e-179">This section describes what happens in a CORS request, at the level of the HTTP messages.</span></span> <span data-ttu-id="2782e-180">Je důležité pochopit, jak funguje CORS, takže můžete nakonfigurovat **[EnableCors]** atribut správně a odstraňování potíží, pokud věci nefungují podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="2782e-180">It's important to understand how CORS works, so that you can configure the **[EnableCors]** attribute correctly and troubleshoot if things don't work as you expect.</span></span>

<span data-ttu-id="2782e-181">Specifikace CORS zavádí několik nové hlavičky protokolu HTTP, které umožňují požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="2782e-181">The CORS specification introduces several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="2782e-182">Pokud je prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky cross-origin; nemusíte dělat nic zvláštního v kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2782e-182">If a browser supports CORS, it sets these headers automatically for cross-origin requests; you don't need to do anything special in your JavaScript code.</span></span>

<span data-ttu-id="2782e-183">Tady je příklad žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="2782e-183">Here is an example of a cross-origin request.</span></span> <span data-ttu-id="2782e-184">Hlavička "Origin" dává domény, lokality, která odeslala žádost.</span><span class="sxs-lookup"><span data-stu-id="2782e-184">The "Origin" header gives the domain of the site that is making the request.</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample6.cmd?highlight=5)]

<span data-ttu-id="2782e-185">Pokud server umožňuje, aby žádosti, nastaví hlavičky Access-Control-Allow-Origin.</span><span class="sxs-lookup"><span data-stu-id="2782e-185">If the server allows the request, it sets the Access-Control-Allow-Origin header.</span></span> <span data-ttu-id="2782e-186">Hodnotu této hlavičky odpovídá hlavička počátečního nebo je hodnota zástupného znaku "\*", což znamená, že jakýkoli původ je povolen.</span><span class="sxs-lookup"><span data-stu-id="2782e-186">The value of this header either matches the Origin header, or is the wildcard value "\*", meaning that any origin is allowed.</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample7.cmd?highlight=5)]

<span data-ttu-id="2782e-187">Pokud odpověď neobsahuje hlavičky Access-Control-Allow-Origin, požadavek AJAX selže.</span><span class="sxs-lookup"><span data-stu-id="2782e-187">If the response does not include the Access-Control-Allow-Origin header, the AJAX request fails.</span></span> <span data-ttu-id="2782e-188">Konkrétně v prohlížeči zakazuje požadavku.</span><span class="sxs-lookup"><span data-stu-id="2782e-188">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="2782e-189">I v případě, že server vrátí úspěšné odpovědi, prohlížeč není zpřístupnit odpověď do klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="2782e-189">Even if the server returns a successful response, the browser does not make the response available to the client application.</span></span>

<span data-ttu-id="2782e-190">**Předběžných požadavků**</span><span class="sxs-lookup"><span data-stu-id="2782e-190">**Preflight Requests**</span></span>

<span data-ttu-id="2782e-191">U některých požadavků CORS prohlížeč odešle požadavek další, nazývá "předběžný požadavek", předtím, než odešle skutečnou žádost pro prostředek.</span><span class="sxs-lookup"><span data-stu-id="2782e-191">For some CORS requests, the browser sends an additional request, called a "preflight request", before it sends the actual request for the resource.</span></span>

<span data-ttu-id="2782e-192">Prohlížeči můžete přeskočit předběžný požadavek, pokud jsou splněny následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="2782e-192">The browser can skip the preflight request if the following conditions are true:</span></span>

- <span data-ttu-id="2782e-193">Metoda žádosti je GET, HEAD nebo POST, *a*</span><span class="sxs-lookup"><span data-stu-id="2782e-193">The request method is GET, HEAD, or POST, *and*</span></span>
- <span data-ttu-id="2782e-194">Aplikace nemá nastaven záhlaví požadavku než přijmout, Accept-Language, jazyka obsahu Content-Type nebo poslední-Event-ID, *a*</span><span class="sxs-lookup"><span data-stu-id="2782e-194">The application does not set any request headers other than Accept, Accept-Language, Content-Language, Content-Type, or Last-Event-ID, *and*</span></span>
- <span data-ttu-id="2782e-195">Hlavička Content-Type (Pokud nastavit) je jedním z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="2782e-195">The Content-Type header (if set) is one of the following:</span></span>

    - <span data-ttu-id="2782e-196">Application/x--www-form-urlencoded</span><span class="sxs-lookup"><span data-stu-id="2782e-196">application/x-www-form-urlencoded</span></span>
    - <span data-ttu-id="2782e-197">multipart/formuláře data</span><span class="sxs-lookup"><span data-stu-id="2782e-197">multipart/form-data</span></span>
    - <span data-ttu-id="2782e-198">text/plain</span><span class="sxs-lookup"><span data-stu-id="2782e-198">text/plain</span></span>

<span data-ttu-id="2782e-199">Pravidlo o hlavičky požadavku se vztahuje na hlavičky, které aplikace nastaví voláním **setRequestHeader** na **XMLHttpRequest** objektu.</span><span class="sxs-lookup"><span data-stu-id="2782e-199">The rule about request headers applies to headers that the application sets by calling **setRequestHeader** on the **XMLHttpRequest** object.</span></span> <span data-ttu-id="2782e-200">(Specifikaci CORS volá tyto "Autor hlavičky žádosti".) Toto pravidlo se nevztahuje na záhlaví *prohlížeče* můžete nastavit, jako je například uživatelského agenta, hostitele nebo Content-Length.</span><span class="sxs-lookup"><span data-stu-id="2782e-200">(The CORS specification calls these "author request headers".) The rule does not apply to headers the *browser* can set, such as User-Agent, Host, or Content-Length.</span></span>

<span data-ttu-id="2782e-201">Tady je příklad předběžný požadavek:</span><span class="sxs-lookup"><span data-stu-id="2782e-201">Here is an example of a preflight request:</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample8.cmd?highlight=4-5)]

<span data-ttu-id="2782e-202">Přípravné požadavek používá metodu HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="2782e-202">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="2782e-203">Obsahuje dva speciálními záhlavími:</span><span class="sxs-lookup"><span data-stu-id="2782e-203">It includes two special headers:</span></span>

- <span data-ttu-id="2782e-204">Access-Control-Request-Method: Metoda protokolu HTTP, který se použije pro aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="2782e-204">Access-Control-Request-Method: The HTTP method that will be used for the actual request.</span></span>
- <span data-ttu-id="2782e-205">Access-Control-Request-Headers: Seznam hlaviček požadavku, který *aplikace* nastavit u aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="2782e-205">Access-Control-Request-Headers: A list of request headers that the *application* set on the actual request.</span></span> <span data-ttu-id="2782e-206">(Znovu, nezahrnuje hlavičky, které nastaví prohlížeče.)</span><span class="sxs-lookup"><span data-stu-id="2782e-206">(Again, this does not include headers that the browser sets.)</span></span>

<span data-ttu-id="2782e-207">Tady je příklad odpovědi, za předpokladu, že server umožňuje žádosti:</span><span class="sxs-lookup"><span data-stu-id="2782e-207">Here is an example response, assuming that the server allows the request:</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample9.cmd?highlight=6-7)]

<span data-ttu-id="2782e-208">Odpověď obsahuje hlavičku přístup – ovládací prvek-Allow-Methods, který obsahuje seznam povolených metod a volitelně hlavičky Access-Control-povolit-Headers, která zobrazuje povolené hlavičky.</span><span class="sxs-lookup"><span data-stu-id="2782e-208">The response includes an Access-Control-Allow-Methods header that lists the allowed methods, and optionally an Access-Control-Allow-Headers header, which lists the allowed headers.</span></span> <span data-ttu-id="2782e-209">Pokud je předběžný požadavek úspěšné, prohlížeč odesílá skutečnou žádost, jak je popsáno výše.</span><span class="sxs-lookup"><span data-stu-id="2782e-209">If the preflight request succeeds, the browser sends the actual request, as described earlier.</span></span>

<span data-ttu-id="2782e-210">Nástroje se běžně používá k testování koncových bodů s předběžné požadavky OPTIONS (například [Fiddler](https://www.telerik.com/fiddler) a [Postman](https://www.getpostman.com/)) Neodesílat požadované záhlaví možnosti ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="2782e-210">Tools commonly used to test endpoints with preflight OPTIONS requests (for example, [Fiddler](https://www.telerik.com/fiddler) and [Postman](https://www.getpostman.com/)) don't send the required OPTIONS headers by default.</span></span> <span data-ttu-id="2782e-211">Ujistěte se, že `Access-Control-Request-Method` a `Access-Control-Request-Headers` záhlaví se posílají se požadavek a hlavičky možnosti komunikace s aplikací prostřednictvím služby IIS.</span><span class="sxs-lookup"><span data-stu-id="2782e-211">Confirm that the `Access-Control-Request-Method` and `Access-Control-Request-Headers` headers are sent with the request and that OPTIONS headers reach the app through IIS.</span></span>

<span data-ttu-id="2782e-212">Pro konfiguraci IIS a aplikace ASP.NET pro příjem a zpracování požadavků na možnost povolit, přidejte následující konfiguraci na aplikaci *web.config* soubor `<system.webServer><handlers>` části:</span><span class="sxs-lookup"><span data-stu-id="2782e-212">To configure IIS to allow an ASP.NET app to receive and handle OPTION requests, add the following configuration to the app's *web.config* file in the `<system.webServer><handlers>` section:</span></span>

```xml
<system.webServer>
  <handlers>
    <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
    <remove name="OPTIONSVerbHandler" />
    <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
  </handlers>
</system.webServer>
```

<span data-ttu-id="2782e-213">Odebrání `OPTIONSVerbHandler` zabrání zpracování možnosti žádostí služby IIS.</span><span class="sxs-lookup"><span data-stu-id="2782e-213">The removal of `OPTIONSVerbHandler` prevents IIS from handling OPTIONS requests.</span></span> <span data-ttu-id="2782e-214">Nahrazení `ExtensionlessUrlHandler-Integrated-4.0` umožňuje možnosti požadavky k dosažení aplikace, protože výchozí registraci modulu umožňuje pouze požadavky GET, HEAD, POST a ladění pomocí adresy URL bez přípony.</span><span class="sxs-lookup"><span data-stu-id="2782e-214">The replacement of `ExtensionlessUrlHandler-Integrated-4.0` allows OPTIONS requests to reach the app because the default module registration only allows GET, HEAD, POST, and DEBUG requests with extensionless URLs.</span></span>

## <a name="scope-rules-for-enablecors"></a><span data-ttu-id="2782e-215">Pravidla rozsahu pro [EnableCors]</span><span class="sxs-lookup"><span data-stu-id="2782e-215">Scope Rules for [EnableCors]</span></span>

<span data-ttu-id="2782e-216">CORS můžete povolit každou akci, na kontroler nebo globálně pro všechny kontrolery rozhraní Web API ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2782e-216">You can enable CORS per action, per controller, or globally for all Web API controllers in your application.</span></span>

<span data-ttu-id="2782e-217">**Za akci**</span><span class="sxs-lookup"><span data-stu-id="2782e-217">**Per Action**</span></span>

<span data-ttu-id="2782e-218">Chcete-li povolit CORS pro jednu akci, nastavte **[EnableCors]** atributu na metodu akce.</span><span class="sxs-lookup"><span data-stu-id="2782e-218">To enable CORS for a single action, set the **[EnableCors]** attribute on the action method.</span></span> <span data-ttu-id="2782e-219">Následující příklad umožňuje použití CORS pro `GetItem` pouze metody.</span><span class="sxs-lookup"><span data-stu-id="2782e-219">The following example enables CORS for the `GetItem` method only.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample10.cs)]

<span data-ttu-id="2782e-220">**Na kontroler**</span><span class="sxs-lookup"><span data-stu-id="2782e-220">**Per Controller**</span></span>

<span data-ttu-id="2782e-221">Pokud nastavíte **[EnableCors]** na třídu kontroleru se vztahuje na všechny akce v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2782e-221">If you set **[EnableCors]** on the controller class, it applies to all the actions on the controller.</span></span> <span data-ttu-id="2782e-222">K zákazu sdílení CORS pro akci, přidejte **[DisableCors]** atribut na akci.</span><span class="sxs-lookup"><span data-stu-id="2782e-222">To disable CORS for an action, add the **[DisableCors]** attribute to the action.</span></span> <span data-ttu-id="2782e-223">Následující příklad umožňuje použití CORS pro každou metodu s výjimkou `PutItem`.</span><span class="sxs-lookup"><span data-stu-id="2782e-223">The following example enables CORS for every method except `PutItem`.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample11.cs)]

<span data-ttu-id="2782e-224">**Globálně**</span><span class="sxs-lookup"><span data-stu-id="2782e-224">**Globally**</span></span>

<span data-ttu-id="2782e-225">Povolení CORS pro všechny řadiče webové rozhraní API ve vaší aplikaci, předejte **EnableCorsAttribute** instance na **EnableCors** metody:</span><span class="sxs-lookup"><span data-stu-id="2782e-225">To enable CORS for all Web API controllers in your application, pass an **EnableCorsAttribute** instance to the **EnableCors** method:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample12.cs)]

<span data-ttu-id="2782e-226">Pokud jste nastavili atribut na více než jednoho oboru, je pořadí podle priority:</span><span class="sxs-lookup"><span data-stu-id="2782e-226">If you set the attribute at more than one scope, the order of precedence is:</span></span>

1. <span data-ttu-id="2782e-227">Akce</span><span class="sxs-lookup"><span data-stu-id="2782e-227">Action</span></span>
2. <span data-ttu-id="2782e-228">Kontroler</span><span class="sxs-lookup"><span data-stu-id="2782e-228">Controller</span></span>
3. <span data-ttu-id="2782e-229">Globální</span><span class="sxs-lookup"><span data-stu-id="2782e-229">Global</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="2782e-230">Nastavte povolené zdroje</span><span class="sxs-lookup"><span data-stu-id="2782e-230">Set the allowed origins</span></span>

<span data-ttu-id="2782e-231">*Zdroje* parametr **[EnableCors]** atribut určuje, jaké zdroje jsou povoleny pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="2782e-231">The *origins* parameter of the **[EnableCors]** attribute specifies which origins are allowed to access the resource.</span></span> <span data-ttu-id="2782e-232">Hodnota je čárkou oddělený seznam Povolené zdroje.</span><span class="sxs-lookup"><span data-stu-id="2782e-232">The value is a comma-separated list of the allowed origins.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample13.cs)]

<span data-ttu-id="2782e-233">Můžete také použít zástupný znak hodnotu "\*" požadavky všechny původy.</span><span class="sxs-lookup"><span data-stu-id="2782e-233">You can also use the wildcard value "\*" to allow requests from any origins.</span></span>

<span data-ttu-id="2782e-234">Pečlivě zvažte předtím, než žádosti z původu.</span><span class="sxs-lookup"><span data-stu-id="2782e-234">Consider carefully before allowing requests from any origin.</span></span> <span data-ttu-id="2782e-235">To znamená, že doslova všechny weby mohl provádět volání AJAX do webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2782e-235">It means that literally any website can make AJAX calls to your web API.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample14.cs)]

## <a name="set-the-allowed-http-methods"></a><span data-ttu-id="2782e-236">Nastavte povolené metody HTTP</span><span class="sxs-lookup"><span data-stu-id="2782e-236">Set the allowed HTTP methods</span></span>

<span data-ttu-id="2782e-237">*Metody* parametr **[EnableCors]** atribut určuje, jaké metody HTTP je povolen přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="2782e-237">The *methods* parameter of the **[EnableCors]** attribute specifies which HTTP methods are allowed to access the resource.</span></span> <span data-ttu-id="2782e-238">Pokud chcete povolit všechny metody, použijte hodnotu zástupný znak "\*".</span><span class="sxs-lookup"><span data-stu-id="2782e-238">To allow all methods, use the wildcard value "\*".</span></span> <span data-ttu-id="2782e-239">Následující příklad umožňuje pouze požadavky GET a POST.</span><span class="sxs-lookup"><span data-stu-id="2782e-239">The following example allows only GET and POST requests.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample15.cs)]

## <a name="set-the-allowed-request-headers"></a><span data-ttu-id="2782e-240">Nastavit hlavičku povolené žádosti</span><span class="sxs-lookup"><span data-stu-id="2782e-240">Set the allowed request headers</span></span>

<span data-ttu-id="2782e-241">V tomto článku je popsáno dříve jak předběžný požadavek může obsahovat hlavičku Access-Control-Request-Headers, výpis hlavičky protokolu HTTP, nastavte aplikací (takzvaný ", vytvářet hlavičky žádosti").</span><span class="sxs-lookup"><span data-stu-id="2782e-241">This article described earlier how a preflight request might include an Access-Control-Request-Headers header, listing the HTTP headers set by the application (the so-called "author request headers").</span></span> <span data-ttu-id="2782e-242">*Záhlaví* parametr **[EnableCors]** atribut určuje, která autorovi požadavku záhlaví jsou povolena.</span><span class="sxs-lookup"><span data-stu-id="2782e-242">The *headers* parameter of the **[EnableCors]** attribute specifies which author request headers are allowed.</span></span> <span data-ttu-id="2782e-243">Chcete-li povolit všechny hlavičky, nastavte *záhlaví* na "\*".</span><span class="sxs-lookup"><span data-stu-id="2782e-243">To allow any headers, set *headers* to "\*".</span></span> <span data-ttu-id="2782e-244">Na seznamu povolených IP adres konkrétní záhlaví, nastavte *záhlaví* do seznamu Povolené hlavičky oddělené čárkami:</span><span class="sxs-lookup"><span data-stu-id="2782e-244">To whitelist specific headers, set *headers* to a comma-separated list of the allowed headers:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample16.cs)]

<span data-ttu-id="2782e-245">Prohlížeče však nejsou zcela konzistentní v tom, jak nastavují Access-Control-Request-Headers.</span><span class="sxs-lookup"><span data-stu-id="2782e-245">However, browsers are not entirely consistent in how they set Access-Control-Request-Headers.</span></span> <span data-ttu-id="2782e-246">Například Chrome aktuálně obsahuje "zdroj".</span><span class="sxs-lookup"><span data-stu-id="2782e-246">For example, Chrome currently includes "origin".</span></span> <span data-ttu-id="2782e-247">FireFox nezahrnuje hlavičky standardních, jako je například "Přijmout", i v případě, že aplikace nastaví ve skriptu.</span><span class="sxs-lookup"><span data-stu-id="2782e-247">FireFox does not include standard headers such as "Accept", even when the application sets them in script.</span></span>

<span data-ttu-id="2782e-248">Pokud nastavíte *záhlaví* pro nic jiného než "\*", měli byste zahrnout alespoň "přijímat", "content-type" a "zdroj" a navíc jakékoli vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="2782e-248">If you set *headers* to anything other than "\*", you should include at least "accept", "content-type", and "origin", plus any custom headers that you want to support.</span></span>

## <a name="set-the-allowed-response-headers"></a><span data-ttu-id="2782e-249">Nastavit hlavičky odpovědi povolené</span><span class="sxs-lookup"><span data-stu-id="2782e-249">Set the allowed response headers</span></span>

<span data-ttu-id="2782e-250">Ve výchozím prohlížeči nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="2782e-250">By default, the browser does not expose all of the response headers to the application.</span></span> <span data-ttu-id="2782e-251">Hlavičky odpovědi, které jsou k dispozici ve výchozím nastavení jsou:</span><span class="sxs-lookup"><span data-stu-id="2782e-251">The response headers that are available by default are:</span></span>

- <span data-ttu-id="2782e-252">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="2782e-252">Cache-Control</span></span>
- <span data-ttu-id="2782e-253">Jazyk obsahu</span><span class="sxs-lookup"><span data-stu-id="2782e-253">Content-Language</span></span>
- <span data-ttu-id="2782e-254">Typ obsahu</span><span class="sxs-lookup"><span data-stu-id="2782e-254">Content-Type</span></span>
- <span data-ttu-id="2782e-255">Vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="2782e-255">Expires</span></span>
- <span data-ttu-id="2782e-256">Datum poslední změny</span><span class="sxs-lookup"><span data-stu-id="2782e-256">Last-Modified</span></span>
- <span data-ttu-id="2782e-257">Direktiva pragma</span><span class="sxs-lookup"><span data-stu-id="2782e-257">Pragma</span></span>

<span data-ttu-id="2782e-258">Specifikace CORS volá tyto [hlavičky odpovědi jednoduché](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="2782e-258">The CORS spec calls these [simple response headers](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header).</span></span> <span data-ttu-id="2782e-259">Chcete-li zpřístupnit dalších hlaviček pro aplikaci, nastavte *exposedHeaders* parametr **[EnableCors]**.</span><span class="sxs-lookup"><span data-stu-id="2782e-259">To make other headers available to the application, set the *exposedHeaders* parameter of **[EnableCors]**.</span></span>

<span data-ttu-id="2782e-260">V následujícím příkladu, kontroler společnosti `Get` metoda nastaví vlastní hlavičku s názvem "X-Custom-Header".</span><span class="sxs-lookup"><span data-stu-id="2782e-260">In the following example, the controller's `Get` method sets a custom header named ‘X-Custom-Header'.</span></span> <span data-ttu-id="2782e-261">Ve výchozím nastavení nebude prohlížeč vystavit toto záhlaví v žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="2782e-261">By default, the browser will not expose this header in a cross-origin request.</span></span> <span data-ttu-id="2782e-262">Pokud chcete zpřístupnit záhlaví, patří "X-Custom-Header" v *exposedHeaders*.</span><span class="sxs-lookup"><span data-stu-id="2782e-262">To make the header available, include ‘X-Custom-Header' in *exposedHeaders*.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample17.cs)]

## <a name="pass-credentials-in-cross-origin-requests"></a><span data-ttu-id="2782e-263">Přihlašovací údaje předávat požadavky cross-origin</span><span class="sxs-lookup"><span data-stu-id="2782e-263">Pass credentials in cross-origin requests</span></span>

<span data-ttu-id="2782e-264">Přihlašovací údaje vyžadují speciální zacházení v požadavku CORS.</span><span class="sxs-lookup"><span data-stu-id="2782e-264">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="2782e-265">Ve výchozím prohlížeči neodešle žádné přihlašovací údaje se žádostí nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="2782e-265">By default, the browser does not send any credentials with a cross-origin request.</span></span> <span data-ttu-id="2782e-266">Přihlašovací údaje zahrnují soubory cookie, jakož i schémat ověřování protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2782e-266">Credentials include cookies as well as HTTP authentication schemes.</span></span> <span data-ttu-id="2782e-267">Odesílá pověření s žádostí nepůvodního zdroje, musíte nastavit klienta **XMLHttpRequest.withCredentials** na hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="2782e-267">To send credentials with a cross-origin request, the client must set **XMLHttpRequest.withCredentials** to true.</span></span>

<span data-ttu-id="2782e-268">Pomocí **XMLHttpRequest** přímo:</span><span class="sxs-lookup"><span data-stu-id="2782e-268">Using **XMLHttpRequest** directly:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample18.cs)]

<span data-ttu-id="2782e-269">V jQuery:</span><span class="sxs-lookup"><span data-stu-id="2782e-269">In jQuery:</span></span>

[!code-javascript[Main](enabling-cross-origin-requests-in-web-api/samples/sample19.js)]

<span data-ttu-id="2782e-270">Na serveru navíc musíte povolit přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="2782e-270">In addition, the server must allow the credentials.</span></span> <span data-ttu-id="2782e-271">Chcete-li povolit pověření nepůvodního zdroje v rozhraní Web API, nastavte **SupportsCredentials** vlastnost na hodnotu true na **[EnableCors]** atribut:</span><span class="sxs-lookup"><span data-stu-id="2782e-271">To allow cross-origin credentials in Web API, set the **SupportsCredentials** property to true on the **[EnableCors]** attribute:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample20.cs)]

<span data-ttu-id="2782e-272">Pokud je tato vlastnost hodnotu true, odpověď HTTP bude obsahovat hlavičku přístup – ovládací prvek-Allow-Credentials.</span><span class="sxs-lookup"><span data-stu-id="2782e-272">If this property is true, the HTTP response will include an Access-Control-Allow-Credentials header.</span></span> <span data-ttu-id="2782e-273">Tato hlavička sděluje prohlížeči, že server umožňuje přihlašovací údaje pro žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="2782e-273">This header tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="2782e-274">Pokud prohlížeč odesílá pověření, ale odpověď neobsahuje platnou hlavičku přístup – ovládací prvek-Allow-Credentials, prohlížeči nebude vystavení odpovědi do aplikace a požadavek AJAX selže.</span><span class="sxs-lookup"><span data-stu-id="2782e-274">If the browser sends credentials, but the response does not include a valid Access-Control-Allow-Credentials header, the browser will not expose the response to the application, and the AJAX request fails.</span></span>

<span data-ttu-id="2782e-275">Buďte opatrní při nastavení **SupportsCredentials** na hodnotu true, protože to znamená, že web v jiné doméně můžete poslat pověření přihlášeného uživatele webové rozhraní API jménem uživatele, aniž by uživatel znal.</span><span class="sxs-lookup"><span data-stu-id="2782e-275">Be careful about setting **SupportsCredentials** to true, because it means a website at another domain can send a logged-in user's credentials to your Web API on the user's behalf, without the user being aware.</span></span> <span data-ttu-id="2782e-276">Specifikace CORS také uvádí nastavení *zdroje* k &quot; \* &quot; je neplatný Pokud **SupportsCredentials** má hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="2782e-276">The CORS spec also states that setting *origins* to &quot;\*&quot; is invalid if **SupportsCredentials** is true.</span></span>

## <a name="custom-cors-policy-providers"></a><span data-ttu-id="2782e-277">Vlastní zprostředkovatelé zásad CORS</span><span class="sxs-lookup"><span data-stu-id="2782e-277">Custom CORS policy providers</span></span>

<span data-ttu-id="2782e-278">**[EnableCors]** atribut implementuje **ICorsPolicyProvider** rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2782e-278">The **[EnableCors]** attribute implements the **ICorsPolicyProvider** interface.</span></span> <span data-ttu-id="2782e-279">Můžete zadat vlastní implementaci tak, že vytvoříte třídu, která je odvozena z **atribut** a implementuje **ICorsProlicyProvider**.</span><span class="sxs-lookup"><span data-stu-id="2782e-279">You can provide your own implementation by creating a class that derives from **Attribute** and implements **ICorsProlicyProvider**.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample21.cs)]

<span data-ttu-id="2782e-280">Teď můžete použít atribut jakéhokoli místa, že vložíte **[EnableCors]**.</span><span class="sxs-lookup"><span data-stu-id="2782e-280">Now you can apply the attribute any place that you would put **[EnableCors]**.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample22.cs)]

<span data-ttu-id="2782e-281">Vlastní zprostředkovatel zásad CORS například může číst nastavení z konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="2782e-281">For example, a custom CORS policy provider could read the settings from a configuration file.</span></span>

<span data-ttu-id="2782e-282">Jako alternativu k použití atributů, můžete zaregistrovat **ICorsPolicyProviderFactory** objekt, který vytvoří **ICorsPolicyProvider** objekty.</span><span class="sxs-lookup"><span data-stu-id="2782e-282">As an alternative to using attributes, you can register an **ICorsPolicyProviderFactory** object that creates **ICorsPolicyProvider** objects.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample23.cs)]

<span data-ttu-id="2782e-283">Chcete-li nastavit **ICorsPolicyProviderFactory**, volání **SetCorsPolicyProviderFactory** rozšiřující metoda při spuštění, následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="2782e-283">To set the **ICorsPolicyProviderFactory**, call the **SetCorsPolicyProviderFactory** extension method at startup, as follows:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample24.cs)]

## <a name="browser-support"></a><span data-ttu-id="2782e-284">Podpora prohlížeče</span><span class="sxs-lookup"><span data-stu-id="2782e-284">Browser support</span></span>

<span data-ttu-id="2782e-285">Balíček CORS webového rozhraní API je technologie na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="2782e-285">The Web API CORS package is a server-side technology.</span></span> <span data-ttu-id="2782e-286">Také musí podporovat CORS webového prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2782e-286">The user's browser also needs to support CORS.</span></span> <span data-ttu-id="2782e-287">Naštěstí aktuální verze všech nejpoužívanějších prohlížečích obsahují [podporu CORS](http://caniuse.com/cors).</span><span class="sxs-lookup"><span data-stu-id="2782e-287">Fortunately, the current versions of all major browsers include [support for CORS](http://caniuse.com/cors).</span></span>
