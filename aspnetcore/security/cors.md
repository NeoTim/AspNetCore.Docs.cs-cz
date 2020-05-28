---
<span data-ttu-id="51449-101">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="51449-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="51449-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="51449-102">'Blazor'</span></span>
- <span data-ttu-id="51449-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="51449-103">'Identity'</span></span>
- <span data-ttu-id="51449-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="51449-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="51449-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="51449-105">'Razor'</span></span>
- <span data-ttu-id="51449-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="51449-106">'SignalR' uid:</span></span> 

---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="51449-107">Povolit žádosti mezi zdroji (CORS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51449-107">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51449-108">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="51449-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="51449-109">Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-109">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="51449-110">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="51449-110">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="51449-111">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="51449-111">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="51449-112">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="51449-112">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="51449-113">V některých případech můžete chtít umožnit jiným webům, aby vaši aplikaci mohli vytvářet žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="51449-113">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="51449-114">Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="51449-114">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="51449-115">[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="51449-115">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="51449-116">Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="51449-116">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="51449-117">Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit.</span><span class="sxs-lookup"><span data-stu-id="51449-117">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="51449-118">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-118">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="51449-119">Další informace najdete v tématu [jak CORS funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="51449-119">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="51449-120">Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.</span><span class="sxs-lookup"><span data-stu-id="51449-120">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="51449-121">Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="51449-121">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="51449-122">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51449-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="51449-123">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="51449-123">Same origin</span></span>

<span data-ttu-id="51449-124">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="51449-124">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="51449-125">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="51449-125">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="51449-126">Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="51449-126">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="51449-127">`https://example.net`: Odlišná doména</span><span class="sxs-lookup"><span data-stu-id="51449-127">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="51449-128">`https://www.example.com/foo.html`: Odlišná subdoména</span><span class="sxs-lookup"><span data-stu-id="51449-128">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="51449-129">`http://example.com/foo.html`: Odlišné schéma</span><span class="sxs-lookup"><span data-stu-id="51449-129">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="51449-130">`https://example.com:9000/foo.html`: Jiný port</span><span class="sxs-lookup"><span data-stu-id="51449-130">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="51449-131">Povolení CORS</span><span class="sxs-lookup"><span data-stu-id="51449-131">Enable CORS</span></span>

<span data-ttu-id="51449-132">Existují tři způsoby, jak povolit CORS:</span><span class="sxs-lookup"><span data-stu-id="51449-132">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="51449-133">V middlewaru pomocí [pojmenované zásady](#np) nebo [výchozí zásady](#dp).</span><span class="sxs-lookup"><span data-stu-id="51449-133">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="51449-134">Použití [Směrování koncového bodu](#ecors).</span><span class="sxs-lookup"><span data-stu-id="51449-134">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="51449-135">S atributem [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="51449-135">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="51449-136">Použití atributu [[EnableCors]](#attr) s pojmenovanou zásadou poskytuje ovládací prvek nejlepší v omezení koncových bodů, které podporují CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-136">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="51449-137">Každý přístup je podrobně popsaný v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="51449-137">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="51449-138">CORS s pojmenovanými zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="51449-138">CORS with named policy and middleware</span></span>

<span data-ttu-id="51449-139">Middleware CORS zpracovává požadavky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-139">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="51449-140">Následující kód aplikuje zásadu CORS na všechny koncové body aplikace se zadanými zdroji:</span><span class="sxs-lookup"><span data-stu-id="51449-140">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="51449-141">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="51449-141">The preceding code:</span></span>

* <span data-ttu-id="51449-142">Nastaví název zásady na `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="51449-142">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="51449-143">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="51449-143">The policy name is arbitrary.</span></span>
* <span data-ttu-id="51449-144">Zavolá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření a určí `_myAllowSpecificOrigins` zásadu CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-144">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="51449-145">`UseCors`Přidá middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-145">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="51449-146">Volání `UseCors` musí být umístěno po `UseRouting` , ale před `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="51449-146">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="51449-147">Další informace najdete v tématu [pořadí middlewaru](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="51449-147">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="51449-148">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="51449-148">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="51449-149">Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt.</span><span class="sxs-lookup"><span data-stu-id="51449-149">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="51449-150">[Možnosti konfigurace](#cors-policy-options), například `WithOrigins` , jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="51449-150">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="51449-151">Povolí `_myAllowSpecificOrigins` zásadu CORS pro všechny koncové body řadiče.</span><span class="sxs-lookup"><span data-stu-id="51449-151">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="51449-152">Pokud chcete použít zásadu CORS na konkrétní koncové body, podívejte se na téma [Směrování koncového bodu](#ecors) .</span><span class="sxs-lookup"><span data-stu-id="51449-152">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="51449-153">Při směrování koncových bodů **musí** být MIDDLEWARe CORS nakonfigurované tak, aby se spustilo mezi voláními `UseRouting` a `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="51449-153">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="51449-154">Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="51449-154">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="51449-155"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Volání metody přidá služby CORS do kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="51449-155">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="51449-156">Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="51449-156">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="51449-157"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Metody mohou být zřetězeny, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="51449-157">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="51449-158">Poznámka: Zadaná adresa URL nesmí **obsahovat koncové** lomítko ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="51449-158">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="51449-159">Pokud adresa URL končí `/` , porovnávání se vrátí `false` a nevrátí se žádné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="51449-159">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="51449-160">CORS s výchozími zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="51449-160">CORS with default policy and middleware</span></span>

<span data-ttu-id="51449-161">Následující zvýrazněný kód umožňuje výchozí zásadu CORS:</span><span class="sxs-lookup"><span data-stu-id="51449-161">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="51449-162">Předchozí kód aplikuje výchozí zásadu CORS na všechny koncové body řadiče.</span><span class="sxs-lookup"><span data-stu-id="51449-162">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="51449-163">Povolení CORS s směrováním koncových bodů</span><span class="sxs-lookup"><span data-stu-id="51449-163">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="51449-164">Povolení CORS na základě jednotlivých koncových bodů pomocí v `RequireCors` současné **not** době nepodporuje [Automatické požadavky na kontrolu před výstupem](#apf).</span><span class="sxs-lookup"><span data-stu-id="51449-164">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="51449-165">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/aspnetcore/issues/20709) a [testování CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="51449-165">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="51449-166">S směrováním koncových bodů je možné CORS povolit na základě jednotlivých koncových bodů pomocí <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> sady rozšiřujících metod:</span><span class="sxs-lookup"><span data-stu-id="51449-166">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="51449-167">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="51449-167">In the preceding code:</span></span>

* <span data-ttu-id="51449-168">`app.UseCors`povoluje middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-168">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="51449-169">Vzhledem k tomu, že výchozí zásady nejsou nakonfigurované, `app.UseCors()` nepovolí CORS samotné.</span><span class="sxs-lookup"><span data-stu-id="51449-169">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="51449-170">`/echo`Koncové body řadiče a umožňují žádosti mezi zdroji pomocí zadaných zásad.</span><span class="sxs-lookup"><span data-stu-id="51449-170">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="51449-171">`/echo2` Razor Koncové body stránky a nepovolují požadavky mezi zdroji, protože **nebyly** zadány žádné výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="51449-171">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="51449-172">Atribut [[DisableCors]](#dc) **nevypne CORS** , která byla povolená směrováním koncových bodů s `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="51449-172">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="51449-173">Pokyny k testování kódu podobného předchozímu najdete v tématu [test CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="51449-173">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="51449-174">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="51449-174">Enable CORS with attributes</span></span>

<span data-ttu-id="51449-175">Povolení CORS s atributem [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) a použití pojmenované zásady pouze na koncové body, které vyžadují CORS, poskytuje ovládací prvek nejlepší.</span><span class="sxs-lookup"><span data-stu-id="51449-175">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="51449-176">Atribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) poskytuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="51449-176">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="51449-177">`[EnableCors]`Atribut umožňuje CORS pro vybrané koncové body místo všech koncových bodů:</span><span class="sxs-lookup"><span data-stu-id="51449-177">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="51449-178">`[EnableCors]`Určuje výchozí zásadu.</span><span class="sxs-lookup"><span data-stu-id="51449-178">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="51449-179">`[EnableCors("{Policy String}")]`Určuje pojmenovanou zásadu.</span><span class="sxs-lookup"><span data-stu-id="51449-179">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="51449-180">`[EnableCors]`Atribut lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="51449-180">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="51449-181">Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="51449-181"> Page `PageModel`</span></span>
* <span data-ttu-id="51449-182">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="51449-182">Controller</span></span>
* <span data-ttu-id="51449-183">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="51449-183">Controller action method</span></span>

<span data-ttu-id="51449-184">U řadičů, modelů stránek nebo metod akcí lze použít různé zásady s `[EnableCors]` atributem.</span><span class="sxs-lookup"><span data-stu-id="51449-184">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="51449-185">Pokud je `[EnableCors]` atribut použit na řadič, model stránky nebo metodu akce a CORS je v middleware povolená, uplatní se **obě** zásady.</span><span class="sxs-lookup"><span data-stu-id="51449-185">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="51449-186">Doporučujeme **před kombinováním zásad. Použijte** `[EnableCors]` **atribut nebo middleware, nikoli oba ve stejné aplikaci.**</span><span class="sxs-lookup"><span data-stu-id="51449-186">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="51449-187">Následující kód používá pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="51449-187">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="51449-188">Následující kód vytvoří dvě zásady CORS:</span><span class="sxs-lookup"><span data-stu-id="51449-188">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="51449-189">Pro ovládací prvek nejlepší pro omezení požadavků CORS:</span><span class="sxs-lookup"><span data-stu-id="51449-189">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="51449-190">Použijte `[EnableCors("MyPolicy")]` s pojmenovanou zásadou.</span><span class="sxs-lookup"><span data-stu-id="51449-190">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="51449-191">Nedefinujte výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="51449-191">Don't define a default policy.</span></span>
* <span data-ttu-id="51449-192">Nepoužívejte [Směrování koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="51449-192">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="51449-193">Kód v další části splňuje předchozí seznam.</span><span class="sxs-lookup"><span data-stu-id="51449-193">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="51449-194">Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="51449-194">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="51449-195">Zakázání CORS</span><span class="sxs-lookup"><span data-stu-id="51449-195">Disable CORS</span></span>

<span data-ttu-id="51449-196">Atribut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **zakáže rozhraní** CORS, které bylo povoleno [směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="51449-196">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="51449-197">Následující kód definuje zásadu CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="51449-197">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="51449-198">Následující kód zakáže CORS pro `GetValues2` akci:</span><span class="sxs-lookup"><span data-stu-id="51449-198">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="51449-199">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="51449-199">The preceding code:</span></span>

* <span data-ttu-id="51449-200">Nepovoluje CORS s [směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="51449-200">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="51449-201">Nedefinuje [výchozí zásadu CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="51449-201">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="51449-202">Pro povolení zásad CORS pro kontroler používá [[EnableCors ("MyPolicy")]](#attr) `"MyPolicy"` .</span><span class="sxs-lookup"><span data-stu-id="51449-202">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="51449-203">Zakáže CORS pro `GetValues2` metodu.</span><span class="sxs-lookup"><span data-stu-id="51449-203">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="51449-204">Pokyny k testování předchozího kódu najdete v části [test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="51449-204">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="51449-205">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="51449-205">CORS policy options</span></span>

<span data-ttu-id="51449-206">Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="51449-206">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="51449-207">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="51449-207">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="51449-208">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="51449-208">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="51449-209">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="51449-209">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="51449-210">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="51449-210">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="51449-211">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="51449-211">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="51449-212">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="51449-212">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="51449-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je volána v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="51449-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="51449-214">U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="51449-214">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="51449-215">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="51449-215">Set the allowed origins</span></span>

<span data-ttu-id="51449-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem ( `http` nebo `https` ).</span><span class="sxs-lookup"><span data-stu-id="51449-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="51449-217">`AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-217">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="51449-218">Určení `AllowAnyOrigin` a `AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="51449-218">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="51449-219">Služba CORS vrátí neplatnou odpověď CORS, pokud je aplikace nakonfigurovaná pomocí obou metod.</span><span class="sxs-lookup"><span data-stu-id="51449-219">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="51449-220">`AllowAnyOrigin`má vliv na požadavky na kontrolu a `Access-Control-Allow-Origin` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="51449-220">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="51449-221">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="51449-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="51449-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásad jako funkci, která umožňuje, aby se zdroje shodovaly s konfigurovanou doménou se zástupnými znaky při vyhodnocování, jestli je původ povolený.</span><span class="sxs-lookup"><span data-stu-id="51449-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="51449-223">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="51449-223">Set the allowed HTTP methods</span></span>

<span data-ttu-id="51449-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="51449-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="51449-225">Umožňuje jakoukoli metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="51449-225">Allows any HTTP method:</span></span>
* <span data-ttu-id="51449-226">Má vliv na požadavky na kontrolu a `Access-Control-Allow-Methods` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="51449-226">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="51449-227">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="51449-227">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="51449-228">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="51449-228">Set the allowed request headers</span></span>

<span data-ttu-id="51449-229">Pokud chcete povolit odeslání konkrétních hlaviček v žádosti CORS s názvem [záhlaví žádosti o autora](https://xhr.spec.whatwg.org/#request), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="51449-229">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="51449-230">Pokud chcete povolení všech [hlaviček žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="51449-230">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="51449-231">`AllowAnyHeader`má vliv na žádosti o předběžné kontroly a hlavičku [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="51449-231">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="51449-232">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="51449-232">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="51449-233">Zásada middlewaru CORS odpovídající konkrétním hlavičkám, které určuje, `WithHeaders` je možná jenom v případě, že se záhlaví poslala `Access-Control-Request-Headers` přesně a odpovídají hlavičkám uvedeným v `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="51449-233">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="51449-234">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="51449-234">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="51449-235">Middleware CORS odmítá požadavek na předběžné kontroly s následující hlavičkou požadavku, protože `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uvedená v tomto seznamu `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="51449-235">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="51449-236">Aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="51449-236">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="51449-237">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-237">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="51449-238">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="51449-238">Set the exposed response headers</span></span>

<span data-ttu-id="51449-239">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="51449-239">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="51449-240">Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="51449-240">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="51449-241">Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:</span><span class="sxs-lookup"><span data-stu-id="51449-241">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="51449-242">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="51449-242">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="51449-243">Pokud chcete, aby byla aplikace k dispozici ostatním hlavičkám, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="51449-243">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="51449-244">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="51449-244">Credentials in cross-origin requests</span></span>

<span data-ttu-id="51449-245">Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-245">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="51449-246">V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="51449-246">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="51449-247">Přihlašovací údaje zahrnují soubory cookie a schémata ověřování HTTP.</span><span class="sxs-lookup"><span data-stu-id="51449-247">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="51449-248">Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí být klient nastaven `XMLHttpRequest.withCredentials` na `true` .</span><span class="sxs-lookup"><span data-stu-id="51449-248">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="51449-249">`XMLHttpRequest`Přímé použití:</span><span class="sxs-lookup"><span data-stu-id="51449-249">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="51449-250">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="51449-250">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="51449-251">Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="51449-251">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="51449-252">Server musí přihlašovací údaje umožňovat.</span><span class="sxs-lookup"><span data-stu-id="51449-252">The server must allow the credentials.</span></span> <span data-ttu-id="51449-253">Pokud chcete povolení přihlašovacích údajů mezi zdroji, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="51449-253">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="51449-254">Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` hlavičku, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-254">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="51449-255">Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou `Access-Control-Allow-Credentials` hlavičku, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="51449-255">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="51449-256">Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="51449-256">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="51449-257">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="51449-257">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="51449-258">Specifikace CORS také uvádí, že pokud je hlavička k dispozici, nastavení původu na `"*"` (všechny zdroje) je neplatné `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="51449-258">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="51449-259">Požadavky na kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="51449-259">Preflight requests</span></span>

<span data-ttu-id="51449-260">U některých požadavků CORS prohlížeč před provedením samotné žádosti pošle žádost o další [Možnosti](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="51449-260">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="51449-261">Tento požadavek se nazývá [žádost o kontrolu před výstupem](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="51449-261">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="51449-262">Prohlížeč může přeskočit požadavek na kontrolu před výstupem, pokud jsou splněné **všechny** následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="51449-262">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="51449-263">Metoda Request je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="51449-263">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="51449-264">Aplikace nenastaví záhlaví žádostí s výjimkou `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` nebo `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="51449-264">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="51449-265">`Content-Type`Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="51449-265">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="51449-266">Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` na `XMLHttpRequest` objekt.</span><span class="sxs-lookup"><span data-stu-id="51449-266">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="51449-267">Specifikace CORS volá [záhlaví požadavku autora](https://www.w3.org/TR/cors/#author-request-headers)záhlaví.</span><span class="sxs-lookup"><span data-stu-id="51449-267">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="51449-268">Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent` , `Host` nebo `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="51449-268">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="51449-269">Následuje příklad odpovědi, která se podobá žádosti o kontrolu před výstupem vytvořeným tlačítkem **[PUT test]** v části [test CORS](#testc) tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="51449-269">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="51449-270">Požadavek na kontrolu před výstupem používá metodu [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="51449-270">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="51449-271">Může obsahovat následující hlavičky:</span><span class="sxs-lookup"><span data-stu-id="51449-271">It may include the following headers:</span></span>

* <span data-ttu-id="51449-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): metoda HTTP, která bude použita pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="51449-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="51449-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku.</span><span class="sxs-lookup"><span data-stu-id="51449-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="51449-274">Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="51449-274">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="51449-275">Přístup-řízení-povolení-metody</span><span class="sxs-lookup"><span data-stu-id="51449-275">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="51449-276">Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí `200 OK` odpověď, ale nenastaví HLAVIČKY CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-276">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="51449-277">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-277">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="51449-278">Příklad Zamítnuté žádosti o kontrolu před výstupem najdete v části [test CORS](#testc) tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="51449-278">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="51449-279">Pomocí nástrojů F12 zobrazuje aplikace konzoly chybu podobnou jedné z následujících v závislosti na prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="51449-279">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="51449-280">Firefox: požadavek mezi zdroji je zablokován: stejné zásady původu nepovolují čtení vzdáleného prostředku v `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="51449-280">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="51449-281">(Důvod: požadavek CORS se nezdařil)</span><span class="sxs-lookup"><span data-stu-id="51449-281">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="51449-282">Další informace</span><span class="sxs-lookup"><span data-stu-id="51449-282">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="51449-283">Chromová: přístup k načtení v ' https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 ' od původu ' https://cors3.azurewebsites.net ' byl zablokován zásadou CORS: odpověď na požadavek na kontrolu před výstupem neprojde kontrolou řízení přístupu: v požadovaném prostředku není k dispozici hlavička ' Access-Control-Allow-Origin '.</span><span class="sxs-lookup"><span data-stu-id="51449-283">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="51449-284">Pokud neprůhledná odpověď vyhovuje vašim potřebám, nastavte režim žádosti na No-CORS, aby bylo možné načíst prostředek s zakázáním CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-284">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="51449-285">Pro povolení konkrétních hlaviček zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="51449-285">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="51449-286">Pokud chcete povolení všech [hlaviček žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="51449-286">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="51449-287">Prohlížeče nejsou konzistentní v tom, jak jsou nastavené `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="51449-287">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="51449-288">Pokud buď:</span><span class="sxs-lookup"><span data-stu-id="51449-288">If either:</span></span>

* <span data-ttu-id="51449-289">Záhlaví jsou nastavena na jinou hodnotu než`"*"`</span><span class="sxs-lookup"><span data-stu-id="51449-289">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="51449-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se nazývá: uveďte aspoň `Accept` , `Content-Type` a a a `Origin` všechny vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="51449-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="51449-291">Automatický kód žádosti o kontrolu</span><span class="sxs-lookup"><span data-stu-id="51449-291">Automatic preflight request code</span></span>

<span data-ttu-id="51449-292">Když se aplikuje zásada CORS, proveďte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="51449-292">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="51449-293">Globálně voláním `app.UseCors` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="51449-293">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="51449-294">Pomocí `[EnableCors]` atributu.</span><span class="sxs-lookup"><span data-stu-id="51449-294">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="51449-295">ASP.NET Core reaguje na požadavek na předběžné možnosti.</span><span class="sxs-lookup"><span data-stu-id="51449-295">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="51449-296">Povolení CORS na základě jednotlivých koncových bodů pomocí v `RequireCors` současné **not** době nepodporuje automatické požadavky na kontrolu před výstupem.</span><span class="sxs-lookup"><span data-stu-id="51449-296">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="51449-297">V části [test CORS](#testc) tohoto dokumentu se toto chování demonstruje.</span><span class="sxs-lookup"><span data-stu-id="51449-297">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="51449-298">[HttpOptions] atribut pro žádosti o kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="51449-298">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="51449-299">Když je v rámci příslušné zásady povolená CORS, ASP.NET Core obecně reaguje na požadavky na předběžné kontroly CORS automaticky.</span><span class="sxs-lookup"><span data-stu-id="51449-299">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="51449-300">V některých scénářích to nemusí být případ.</span><span class="sxs-lookup"><span data-stu-id="51449-300">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="51449-301">Například použití [CORS se směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="51449-301">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="51449-302">Následující kód používá atribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) k vytvoření koncových bodů pro žádosti o možnosti:</span><span class="sxs-lookup"><span data-stu-id="51449-302">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="51449-303">Pokyny k testování předchozího kódu najdete v článku [test CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="51449-303">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="51449-304">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="51449-304">Set the preflight expiration time</span></span>

<span data-ttu-id="51449-305">`Access-Control-Max-Age`Záhlaví určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="51449-305">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="51449-306">Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="51449-306">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="51449-307">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="51449-307">How CORS works</span></span>

<span data-ttu-id="51449-308">Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="51449-308">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="51449-309">CORS není **funkce** zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="51449-309">CORS is **not** a security feature.</span></span> <span data-ttu-id="51449-310">CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="51449-310">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="51449-311">Škodlivý objekt actor může například použít [skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s POVOLENým CORS, aby mohl ukrást informace.</span><span class="sxs-lookup"><span data-stu-id="51449-311">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="51449-312">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-312">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="51449-313">Pro vymáhání CORS je to až klient (prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="51449-313">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="51449-314">Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="51449-314">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="51449-315">Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="51449-315">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="51449-316">Fiddler</span><span class="sxs-lookup"><span data-stu-id="51449-316">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="51449-317">Postman</span><span class="sxs-lookup"><span data-stu-id="51449-317">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="51449-318">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="51449-318">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="51449-319">Webový prohlížeč zadáním adresy URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="51449-319">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="51449-320">To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.</span><span class="sxs-lookup"><span data-stu-id="51449-320">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="51449-321">Prohlížeče bez CORS nemůžou provádět žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-321">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="51449-322">Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) .</span><span class="sxs-lookup"><span data-stu-id="51449-322">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="51449-323">JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="51449-323">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="51449-324">Skripty mohou být načteny mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-324">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="51449-325">[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-325">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="51449-326">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky.</span><span class="sxs-lookup"><span data-stu-id="51449-326">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="51449-327">Pro povolení CORS není nutný vlastní kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="51449-327">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="51449-328">[Tlačítko Vložit test](https://cors3.azurewebsites.net/test) na nasazené [ukázce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="51449-328">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="51449-329">Následuje příklad žádosti o více zdrojů z tlačítka Test [hodnot](https://cors3.azurewebsites.net/) na `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="51449-329">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="51449-330">`Origin`Hlavička:</span><span class="sxs-lookup"><span data-stu-id="51449-330">The `Origin` header:</span></span>

* <span data-ttu-id="51449-331">Poskytuje doménu lokality, ze které se vytváří požadavek.</span><span class="sxs-lookup"><span data-stu-id="51449-331">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="51449-332">Je vyžadováno a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="51449-332">Is required and must be different from the host.</span></span>

<span data-ttu-id="51449-333">**Obecné hlavičky**</span><span class="sxs-lookup"><span data-stu-id="51449-333">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="51449-334">**Hlavičky odpovědi**</span><span class="sxs-lookup"><span data-stu-id="51449-334">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="51449-335">**Hlavičky požadavku**</span><span class="sxs-lookup"><span data-stu-id="51449-335">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="51449-336">V `OPTIONS` části požadavky Server nastaví hlavičku **hlaviček odpovědi** `Access-Control-Allow-Origin: {allowed origin}` v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="51449-336">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="51449-337">Například nasazená [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [DELETE [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` obsahuje následující hlavičky:</span><span class="sxs-lookup"><span data-stu-id="51449-337">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="51449-338">**Obecné hlavičky**</span><span class="sxs-lookup"><span data-stu-id="51449-338">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="51449-339">**Hlavičky odpovědi**</span><span class="sxs-lookup"><span data-stu-id="51449-339">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="51449-340">**Hlavičky požadavku**</span><span class="sxs-lookup"><span data-stu-id="51449-340">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="51449-341">V předchozích **hlavičkách odpovědi**Server nastaví hlavičku [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="51449-341">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="51449-342">`https://cors1.azurewebsites.net`Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku.</span><span class="sxs-lookup"><span data-stu-id="51449-342">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="51449-343">Pokud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> je volána, `Access-Control-Allow-Origin: *` je vrácena hodnota zástupného znaku.</span><span class="sxs-lookup"><span data-stu-id="51449-343">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="51449-344">`AllowAnyOrigin`umožňuje jakýkoli původ.</span><span class="sxs-lookup"><span data-stu-id="51449-344">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="51449-345">Pokud odpověď nezahrnuje `Access-Control-Allow-Origin` hlavičku, požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="51449-345">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="51449-346">Konkrétně prohlížeč požadavek nepovoluje.</span><span class="sxs-lookup"><span data-stu-id="51449-346">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="51449-347">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51449-347">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="51449-348">Zobrazit žádosti o možnosti</span><span class="sxs-lookup"><span data-stu-id="51449-348">Display OPTIONS requests</span></span>

<span data-ttu-id="51449-349">Ve výchozím nastavení prohlížeče Chrome a Edge nezobrazují žádosti o možnosti na kartě síť nástrojů F12.</span><span class="sxs-lookup"><span data-stu-id="51449-349">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="51449-350">Chcete-li zobrazit žádosti o možnosti v těchto prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="51449-350">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="51449-351">`chrome://flags/#out-of-blink-cors` nebo `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="51449-351">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="51449-352">zakažte příznak.</span><span class="sxs-lookup"><span data-stu-id="51449-352">disable the flag.</span></span>
* <span data-ttu-id="51449-353">restart (restartovat).</span><span class="sxs-lookup"><span data-stu-id="51449-353">restart.</span></span>

<span data-ttu-id="51449-354">Firefox ve výchozím nastavení zobrazuje požadavky na možnosti.</span><span class="sxs-lookup"><span data-stu-id="51449-354">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="51449-355">CORS ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="51449-355">CORS in IIS</span></span>

<span data-ttu-id="51449-356">Při nasazování do služby IIS musí CORS běžet před ověřováním systému Windows, pokud server není nakonfigurovaný tak, aby povoloval anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="51449-356">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="51449-357">Pro podporu tohoto scénáře je nutné nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51449-357">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="51449-358">Test CORS</span><span class="sxs-lookup"><span data-stu-id="51449-358">Test CORS</span></span>

<span data-ttu-id="51449-359">[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) obsahuje kód pro testování CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-359">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="51449-360">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="51449-360">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="51449-361">Ukázka je projekt rozhraní API se Razor stránkami přidanými:</span><span class="sxs-lookup"><span data-stu-id="51449-361">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="51449-362">`WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="51449-362">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="51449-363">Následující příklad `ValuesController` poskytuje koncové body pro testování:</span><span class="sxs-lookup"><span data-stu-id="51449-363">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="51449-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) je k dispozici v balíčku NuGet [Rick. Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) a zobrazí informace o trasách.</span><span class="sxs-lookup"><span data-stu-id="51449-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="51449-365">Otestujte předchozí vzorový kód pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="51449-365">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="51449-366">Použijte nasazenou ukázkovou aplikaci na adrese [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="51449-366">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="51449-367">Není nutné stahovat ukázku.</span><span class="sxs-lookup"><span data-stu-id="51449-367">There is no need to download the sample.</span></span>
* <span data-ttu-id="51449-368">Spusťte ukázku s `dotnet run` použitím výchozí adresy URL `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="51449-368">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="51449-369">Spusťte ukázku ze sady Visual Studio s portem nastaveným na 44398 pro adresu URL `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="51449-369">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="51449-370">Použití prohlížeče s nástroji F12:</span><span class="sxs-lookup"><span data-stu-id="51449-370">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="51449-371">Vyberte tlačítko **hodnoty** a zkontrolujte hlavičky na kartě **síť** .</span><span class="sxs-lookup"><span data-stu-id="51449-371">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="51449-372">Vyberte tlačítko **Vložit test** .</span><span class="sxs-lookup"><span data-stu-id="51449-372">Select the **PUT test** button.</span></span> <span data-ttu-id="51449-373">Pokyny k zobrazení žádosti o možnosti najdete v tématu [Možnosti zobrazení žádosti](#options) .</span><span class="sxs-lookup"><span data-stu-id="51449-373">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="51449-374">**Test Put** vytvoří dvě požadavky, požadavek na kontrolu před výstupem a požadavek PUT.</span><span class="sxs-lookup"><span data-stu-id="51449-374">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="51449-375">Kliknutím na **`GetValues2 [DisableCors]`** tlačítko aktivujte neúspěšnou žádost CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-375">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="51449-376">Jak je uvedeno v dokumentu, odpověď vrátí 200 úspěch, ale požadavek CORS se neprovádí.</span><span class="sxs-lookup"><span data-stu-id="51449-376">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="51449-377">Kliknutím na kartu **Konzola** zobrazíte chybu CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-377">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="51449-378">V závislosti na prohlížeči se zobrazí chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="51449-378">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="51449-379">`'https://cors1.azurewebsites.net/api/values/GetValues2'` `'https://cors3.azurewebsites.net'` Zásada CORS zablokovala přístup k načtení od původu: v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.</span><span class="sxs-lookup"><span data-stu-id="51449-379">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="51449-380">Pokud neprůhledná odpověď vyhovuje vašim potřebám, nastavte režim žádosti na No-CORS, aby bylo možné načíst prostředek s zakázáním CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-380">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="51449-381">Koncové body s podporou CORS je možné testovat pomocí nástroje, jako je například [kudrlinkou](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)nebo [post](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="51449-381">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="51449-382">Při použití nástroje se musí původ požadavku určeného `Origin` hlavičkou lišit od hostitele, který požadavek přijal.</span><span class="sxs-lookup"><span data-stu-id="51449-382">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="51449-383">Pokud požadavek není *mezi zdroji* založen na hodnotě `Origin` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="51449-383">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="51449-384">Pro zpracování žádosti není nutné middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-384">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="51449-385">V odpovědi nejsou vraceny hlavičky CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-385">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="51449-386">Následující příkaz slouží `curl` k vystavení žádosti o možnosti s informacemi:</span><span class="sxs-lookup"><span data-stu-id="51449-386">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="51449-387">Testování CORS pomocí směrování koncových bodů a [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="51449-387">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="51449-388">Povolení CORS na základě jednotlivých koncových bodů pomocí v `RequireCors` současné **not** době nepodporuje [Automatické požadavky na kontrolu před výstupem](#apf).</span><span class="sxs-lookup"><span data-stu-id="51449-388">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="51449-389">Vezměte v úvahu následující kód, který pomocí [Směrování koncových bodů povoluje CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="51449-389">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="51449-390">Následující příklad `TodoItems1Controller` poskytuje koncové body pro testování:</span><span class="sxs-lookup"><span data-stu-id="51449-390">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="51449-391">Otestujte předchozí kód ze [stránky test](https://cors1.azurewebsites.net/test?number=1) v nasazené [ukázce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="51449-391">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="51449-392">Tlačítka **DELETE [EnableCors]** a **Get [EnableCors]** jsou úspěšná, protože koncové body mají `[EnableCors]` a reagují na požadavky na kontrolu před výstupem.</span><span class="sxs-lookup"><span data-stu-id="51449-392">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="51449-393">Ostatní koncové body selžou.</span><span class="sxs-lookup"><span data-stu-id="51449-393">The other endpoints fails.</span></span> <span data-ttu-id="51449-394">Tlačítko **získat** se nepovedlo, protože [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) pošle:</span><span class="sxs-lookup"><span data-stu-id="51449-394">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="51449-395">Následující `TodoItems2Controller` příkaz poskytuje podobné koncové body, ale obsahuje explicitní kód pro reakci na požadavky Options:</span><span class="sxs-lookup"><span data-stu-id="51449-395">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="51449-396">Otestujte předchozí kód ze [stránky test](https://cors1.azurewebsites.net/test?number=2) v nasazené ukázce.</span><span class="sxs-lookup"><span data-stu-id="51449-396">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="51449-397">V rozevíracím seznamu **řadič** vyberte položku **Kontrola před výstupem** a pak **nastavte kontroler**.</span><span class="sxs-lookup"><span data-stu-id="51449-397">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="51449-398">Všechna volání CORS do `TodoItems2Controller` koncových bodů jsou úspěšná.</span><span class="sxs-lookup"><span data-stu-id="51449-398">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51449-399">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="51449-399">Additional resources</span></span>

* [<span data-ttu-id="51449-400">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="51449-400">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="51449-401">Začínáme s modulem IIS CORS</span><span class="sxs-lookup"><span data-stu-id="51449-401">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="51449-402">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="51449-402">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="51449-403">Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-403">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="51449-404">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="51449-404">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="51449-405">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="51449-405">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="51449-406">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="51449-406">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="51449-407">V některých případech můžete chtít, aby ostatní weby ve vaší aplikaci provedly žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="51449-407">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="51449-408">Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="51449-408">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="51449-409">[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="51449-409">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="51449-410">Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="51449-410">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="51449-411">Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit.</span><span class="sxs-lookup"><span data-stu-id="51449-411">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="51449-412">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-412">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="51449-413">Další informace najdete v tématu [jak CORS funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="51449-413">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="51449-414">Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.</span><span class="sxs-lookup"><span data-stu-id="51449-414">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="51449-415">Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="51449-415">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="51449-416">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51449-416">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="51449-417">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="51449-417">Same origin</span></span>

<span data-ttu-id="51449-418">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="51449-418">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="51449-419">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="51449-419">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="51449-420">Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="51449-420">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="51449-421">`https://example.net`: Odlišná doména</span><span class="sxs-lookup"><span data-stu-id="51449-421">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="51449-422">`https://www.example.com/foo.html`: Odlišná subdoména</span><span class="sxs-lookup"><span data-stu-id="51449-422">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="51449-423">`http://example.com/foo.html`: Odlišné schéma</span><span class="sxs-lookup"><span data-stu-id="51449-423">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="51449-424">`https://example.com:9000/foo.html`: Jiný port</span><span class="sxs-lookup"><span data-stu-id="51449-424">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="51449-425">Internet Explorer při porovnávání zdrojů nebere v úvahu port.</span><span class="sxs-lookup"><span data-stu-id="51449-425">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="51449-426">CORS s pojmenovanými zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="51449-426">CORS with named policy and middleware</span></span>

<span data-ttu-id="51449-427">Middleware CORS zpracovává požadavky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-427">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="51449-428">Následující kód umožňuje CORS pro celou aplikaci se zadaným počátkem:</span><span class="sxs-lookup"><span data-stu-id="51449-428">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="51449-429">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="51449-429">The preceding code:</span></span>

* <span data-ttu-id="51449-430">Nastaví název zásady na " \_ myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="51449-430">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="51449-431">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="51449-431">The policy name is arbitrary.</span></span>
* <span data-ttu-id="51449-432">Volá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření, která umožňuje CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-432">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="51449-433">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="51449-433">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="51449-434">Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt.</span><span class="sxs-lookup"><span data-stu-id="51449-434">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="51449-435">[Možnosti konfigurace](#cors-policy-options), například `WithOrigins` , jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="51449-435">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="51449-436"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Volání metody přidá služby CORS do kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="51449-436">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="51449-437">Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="51449-437">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="51449-438"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Metoda může řetězit metody, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="51449-438">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="51449-439">Poznámka: adresa URL nesmí **obsahovat koncové** lomítko ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="51449-439">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="51449-440">Pokud adresa URL končí `/` , porovnávání se vrátí `false` a nevrátí se žádné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="51449-440">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="51449-441">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:</span><span class="sxs-lookup"><span data-stu-id="51449-441">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="51449-442">Poznámka: `UseCors` musí být volána před `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="51449-442">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="51449-443">Viz [Povolení CORS v rámci Razor stránek, řadičů a metod akcí](#ecors) pro APLIKOVÁNí zásad CORS na úrovni stránky, řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="51449-443">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="51449-444">Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="51449-444">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="51449-445">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="51449-445">Enable CORS with attributes</span></span>

<span data-ttu-id="51449-446">Atribut [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) představuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="51449-446">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="51449-447">`[EnableCors]`Atribut povoluje CORS pro vybrané koncové body místo všech koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="51449-447">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="51449-448">Slouží `[EnableCors]` k zadání výchozích zásad a `[EnableCors("{Policy String}")]` k určení zásad.</span><span class="sxs-lookup"><span data-stu-id="51449-448">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="51449-449">`[EnableCors]`Atribut lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="51449-449">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="51449-450">Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="51449-450"> Page `PageModel`</span></span>
* <span data-ttu-id="51449-451">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="51449-451">Controller</span></span>
* <span data-ttu-id="51449-452">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="51449-452">Controller action method</span></span>

<span data-ttu-id="51449-453">S atributem lze použít různé zásady pro kontroler/Page-model/Action `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="51449-453">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="51449-454">Pokud je `[EnableCors]` atribut použit pro řadiče/stránky modelu/akce a v middlewaru je povoleno CORS, jsou **obě** zásady aplikovány.</span><span class="sxs-lookup"><span data-stu-id="51449-454">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="51449-455">**Doporučujeme, abyste nekombinují zásady** .</span><span class="sxs-lookup"><span data-stu-id="51449-455">We recommend **not** combining policies.</span></span> <span data-ttu-id="51449-456">Použijte `[EnableCors]` atribut nebo middleware, **nikoli oba**.</span><span class="sxs-lookup"><span data-stu-id="51449-456">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="51449-457">Při použití nástroje `[EnableCors]` nedefinujte výchozí zásady. **not**</span><span class="sxs-lookup"><span data-stu-id="51449-457">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="51449-458">Následující kód používá pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="51449-458">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="51449-459">Následující kód vytvoří výchozí zásadu CORS a zásadu s názvem `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="51449-459">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="51449-460">Zakázání CORS</span><span class="sxs-lookup"><span data-stu-id="51449-460">Disable CORS</span></span>

<span data-ttu-id="51449-461">Atribut [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) zakáže CORS pro kontroler/Page-model/Action.</span><span class="sxs-lookup"><span data-stu-id="51449-461">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="51449-462">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="51449-462">CORS policy options</span></span>

<span data-ttu-id="51449-463">Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="51449-463">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="51449-464">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="51449-464">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="51449-465">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="51449-465">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="51449-466">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="51449-466">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="51449-467">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="51449-467">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="51449-468">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="51449-468">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="51449-469">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="51449-469">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="51449-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je volána v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="51449-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="51449-471">U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="51449-471">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="51449-472">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="51449-472">Set the allowed origins</span></span>

<span data-ttu-id="51449-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem ( `http` nebo `https` ).</span><span class="sxs-lookup"><span data-stu-id="51449-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="51449-474">`AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-474">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="51449-475">Určení `AllowAnyOrigin` a `AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="51449-475">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="51449-476">V případě zabezpečené aplikace zadejte přesný seznam zdrojů, pokud je klient musí autorizovat pro přístup k prostředkům serveru.</span><span class="sxs-lookup"><span data-stu-id="51449-476">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="51449-477">`AllowAnyOrigin`má vliv na požadavky na kontrolu a `Access-Control-Allow-Origin` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="51449-477">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="51449-478">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="51449-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="51449-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásad jako funkci, která umožňuje, aby se zdroje shodovaly s konfigurovanou doménou se zástupnými znaky při vyhodnocování, jestli je původ povolený.</span><span class="sxs-lookup"><span data-stu-id="51449-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="51449-480">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="51449-480">Set the allowed HTTP methods</span></span>

<span data-ttu-id="51449-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="51449-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="51449-482">Umožňuje jakoukoli metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="51449-482">Allows any HTTP method:</span></span>
* <span data-ttu-id="51449-483">Má vliv na požadavky na kontrolu a `Access-Control-Allow-Methods` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="51449-483">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="51449-484">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="51449-484">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="51449-485">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="51449-485">Set the allowed request headers</span></span>

<span data-ttu-id="51449-486">Pokud chcete povolit odeslání konkrétních hlaviček v žádosti CORS s názvem *záhlaví žádosti o autora*, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="51449-486">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="51449-487">Pokud chcete povolení všech hlaviček žádostí o autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="51449-487">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="51449-488">Toto nastavení má vliv na žádosti o předběžné kontroly a `Access-Control-Request-Headers` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="51449-488">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="51449-489">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="51449-489">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="51449-490">Middleware CORS vždycky povoluje posílání čtyř hlaviček v, `Access-Control-Request-Headers` bez ohledu na hodnoty nakonfigurované v CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="51449-490">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="51449-491">Tento seznam hlaviček obsahuje:</span><span class="sxs-lookup"><span data-stu-id="51449-491">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="51449-492">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="51449-492">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="51449-493">Middleware CORS odpoví úspěšně do žádosti o kontrolu před výstupem s následující hlavičkou požadavku, protože `Content-Language` je vždy na seznamu povolených:</span><span class="sxs-lookup"><span data-stu-id="51449-493">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="51449-494">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="51449-494">Set the exposed response headers</span></span>

<span data-ttu-id="51449-495">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="51449-495">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="51449-496">Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="51449-496">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="51449-497">Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:</span><span class="sxs-lookup"><span data-stu-id="51449-497">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="51449-498">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="51449-498">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="51449-499">Pokud chcete, aby byla aplikace k dispozici ostatním hlavičkám, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="51449-499">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="51449-500">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="51449-500">Credentials in cross-origin requests</span></span>

<span data-ttu-id="51449-501">Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-501">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="51449-502">V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="51449-502">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="51449-503">Přihlašovací údaje zahrnují soubory cookie a schémata ověřování HTTP.</span><span class="sxs-lookup"><span data-stu-id="51449-503">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="51449-504">Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí být klient nastaven `XMLHttpRequest.withCredentials` na `true` .</span><span class="sxs-lookup"><span data-stu-id="51449-504">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="51449-505">`XMLHttpRequest`Přímé použití:</span><span class="sxs-lookup"><span data-stu-id="51449-505">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="51449-506">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="51449-506">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="51449-507">Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="51449-507">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="51449-508">Server musí přihlašovací údaje umožňovat.</span><span class="sxs-lookup"><span data-stu-id="51449-508">The server must allow the credentials.</span></span> <span data-ttu-id="51449-509">Pokud chcete povolení přihlašovacích údajů mezi zdroji, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="51449-509">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="51449-510">Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` hlavičku, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-510">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="51449-511">Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou `Access-Control-Allow-Credentials` hlavičku, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="51449-511">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="51449-512">Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="51449-512">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="51449-513">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="51449-513">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="51449-514">Specifikace CORS také uvádí, že pokud je hlavička k dispozici, nastavení původu na `"*"` (všechny zdroje) je neplatné `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="51449-514">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="51449-515">Požadavky na kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="51449-515">Preflight requests</span></span>

<span data-ttu-id="51449-516">U některých požadavků CORS prohlížeč před provedením samotného požadavku pošle další požadavek.</span><span class="sxs-lookup"><span data-stu-id="51449-516">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="51449-517">Tento požadavek se nazývá *žádost o kontrolu před výstupem*.</span><span class="sxs-lookup"><span data-stu-id="51449-517">This request is called a *preflight request*.</span></span> <span data-ttu-id="51449-518">Prohlížeč může požadavek na předběžné kontroly přeskočit, pokud jsou splněné následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="51449-518">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="51449-519">Metoda Request je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="51449-519">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="51449-520">Aplikace nenastaví záhlaví žádostí s výjimkou `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` nebo `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="51449-520">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="51449-521">`Content-Type`Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="51449-521">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="51449-522">Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` na `XMLHttpRequest` objekt.</span><span class="sxs-lookup"><span data-stu-id="51449-522">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="51449-523">Specifikace CORS volá *záhlaví požadavku autora*záhlaví.</span><span class="sxs-lookup"><span data-stu-id="51449-523">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="51449-524">Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent` , `Host` nebo `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="51449-524">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="51449-525">Následuje příklad žádosti o kontrolu před výstupem:</span><span class="sxs-lookup"><span data-stu-id="51449-525">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="51449-526">Požadavek na lety používá metodu HTTP.</span><span class="sxs-lookup"><span data-stu-id="51449-526">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="51449-527">Obsahuje dvě speciální hlavičky:</span><span class="sxs-lookup"><span data-stu-id="51449-527">It includes two special headers:</span></span>

* <span data-ttu-id="51449-528">`Access-Control-Request-Method`: Metoda HTTP, která se bude používat pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="51449-528">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="51449-529">`Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku.</span><span class="sxs-lookup"><span data-stu-id="51449-529">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="51449-530">Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="51449-530">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="51449-531">Když je v rámci příslušné zásady povolená CORS, ASP.NET Core všeobecně automaticky reagovat na požadavky CORS v rámci kontroly.</span><span class="sxs-lookup"><span data-stu-id="51449-531">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="51449-532">V [případě požadavků na kontrolu před výstupem se podívejte na atribut [HttpOptions]](#pro).</span><span class="sxs-lookup"><span data-stu-id="51449-532">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="51449-533">Požadavek předběžné kontroly CORS může zahrnovat `Access-Control-Request-Headers` hlavičku, která serveru oznamuje hlavičkám, které jsou odesílány se skutečným požadavkem.</span><span class="sxs-lookup"><span data-stu-id="51449-533">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="51449-534">Pro povolení konkrétních hlaviček zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="51449-534">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="51449-535">Pokud chcete povolení všech hlaviček žádostí o autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="51449-535">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="51449-536">Prohlížeče nejsou zcela konzistentní v tom, jak nastavily `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="51449-536">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="51449-537">Pokud nastavíte záhlaví na jinou hodnotu než `"*"` (nebo použít <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), měli byste zahrnout aspoň `Accept` , `Content-Type` a a `Origin` také libovolné vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="51449-537">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="51449-538">Následuje příklad odpovědi na žádost o kontrolu před výstupem (za předpokladu, že server povoluje požadavek):</span><span class="sxs-lookup"><span data-stu-id="51449-538">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="51449-539">Odpověď obsahuje `Access-Control-Allow-Methods` hlavičku, která obsahuje seznam povolených metod a volitelně `Access-Control-Allow-Headers` záhlaví, ve kterém jsou uvedeny povolené hlavičky.</span><span class="sxs-lookup"><span data-stu-id="51449-539">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="51449-540">Pokud je žádost o kontrolu před výstupem úspěšná, prohlížeč pošle skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="51449-540">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="51449-541">Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="51449-541">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="51449-542">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-542">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="51449-543">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="51449-543">Set the preflight expiration time</span></span>

<span data-ttu-id="51449-544">`Access-Control-Max-Age`Záhlaví určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="51449-544">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="51449-545">Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="51449-545">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="51449-546">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="51449-546">How CORS works</span></span>

<span data-ttu-id="51449-547">Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="51449-547">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="51449-548">CORS není **funkce** zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="51449-548">CORS is **not** a security feature.</span></span> <span data-ttu-id="51449-549">CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="51449-549">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="51449-550">Škodlivý objekt actor by například mohl použít možnost [zabránit skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s povoleným CORS, aby mohl ukrást informace.</span><span class="sxs-lookup"><span data-stu-id="51449-550">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="51449-551">Vaše rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-551">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="51449-552">Pro vymáhání CORS je to až klient (prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="51449-552">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="51449-553">Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="51449-553">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="51449-554">Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="51449-554">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="51449-555">Fiddler</span><span class="sxs-lookup"><span data-stu-id="51449-555">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="51449-556">Postman</span><span class="sxs-lookup"><span data-stu-id="51449-556">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="51449-557">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="51449-557">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="51449-558">Webový prohlížeč zadáním adresy URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="51449-558">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="51449-559">To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.</span><span class="sxs-lookup"><span data-stu-id="51449-559">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="51449-560">Prohlížeče (bez CORS) nemůžou provádět žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-560">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="51449-561">Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) .</span><span class="sxs-lookup"><span data-stu-id="51449-561">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="51449-562">JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="51449-562">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="51449-563">Skripty mohou být načteny mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-563">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="51449-564">[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="51449-564">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="51449-565">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky.</span><span class="sxs-lookup"><span data-stu-id="51449-565">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="51449-566">Pro povolení CORS není nutný vlastní kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="51449-566">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="51449-567">Následuje příklad žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="51449-567">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="51449-568">`Origin`Hlavička poskytuje doménu lokality, ze které se vytváří požadavek.</span><span class="sxs-lookup"><span data-stu-id="51449-568">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="51449-569">`Origin`Hlavička je povinná a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="51449-569">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="51449-570">Pokud server tuto žádost povoluje, nastaví `Access-Control-Allow-Origin` hlavičku v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="51449-570">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="51449-571">Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku nebo se jedná o zástupnou hodnotu `"*"` , což znamená, že je povolen libovolný původ:</span><span class="sxs-lookup"><span data-stu-id="51449-571">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="51449-572">Pokud odpověď nezahrnuje `Access-Control-Allow-Origin` hlavičku, požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="51449-572">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="51449-573">Konkrétně prohlížeč požadavek nepovoluje.</span><span class="sxs-lookup"><span data-stu-id="51449-573">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="51449-574">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51449-574">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="51449-575">Test CORS</span><span class="sxs-lookup"><span data-stu-id="51449-575">Test CORS</span></span>

<span data-ttu-id="51449-576">Testování CORS:</span><span class="sxs-lookup"><span data-stu-id="51449-576">To test CORS:</span></span>

1. <span data-ttu-id="51449-577">[Vytvořte projekt API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="51449-577">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="51449-578">Alternativně si můžete [Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="51449-578">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="51449-579">Povolte CORS pomocí jednoho z přístupů v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="51449-579">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="51449-580">Příklad:</span><span class="sxs-lookup"><span data-stu-id="51449-580">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="51449-581">`WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="51449-581">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="51449-582">Vytvořte projekt webové aplikace ( Razor stránky nebo MVC).</span><span class="sxs-lookup"><span data-stu-id="51449-582">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="51449-583">Ukázka používá Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="51449-583">The sample uses Razor Pages.</span></span> <span data-ttu-id="51449-584">Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="51449-584">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="51449-585">Do souboru *index. cshtml* přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="51449-585">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="51449-586">V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresu URL nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="51449-586">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="51449-587">Nasaďte projekt API.</span><span class="sxs-lookup"><span data-stu-id="51449-587">Deploy the API project.</span></span> <span data-ttu-id="51449-588">Nasaďte například [do Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="51449-588">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="51449-589">Spusťte Razor stránky nebo aplikaci MVC z plochy a klikněte na tlačítko **test** .</span><span class="sxs-lookup"><span data-stu-id="51449-589">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="51449-590">Pomocí nástrojů F12 zkontrolujte chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="51449-590">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="51449-591">Odeberte původ localhost z `WithOrigins` a nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51449-591">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="51449-592">Případně spusťte klientskou aplikaci s jiným portem.</span><span class="sxs-lookup"><span data-stu-id="51449-592">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="51449-593">Například spusťte ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="51449-593">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="51449-594">Otestujte pomocí klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="51449-594">Test with the client app.</span></span> <span data-ttu-id="51449-595">Chyby CORS vracejí chybu, ale chybová zpráva není k dispozici pro JavaScript.</span><span class="sxs-lookup"><span data-stu-id="51449-595">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="51449-596">K zobrazení chyby použijte kartu konzola v nástrojích F12.</span><span class="sxs-lookup"><span data-stu-id="51449-596">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="51449-597">V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12), která je podobná následující:</span><span class="sxs-lookup"><span data-stu-id="51449-597">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="51449-598">Používání Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="51449-598">Using Microsoft Edge:</span></span>

     <span data-ttu-id="51449-599">**SEC7120: [CORS] původ `https://localhost:44375` nebyl nalezen `https://localhost:44375` v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek pro více zdrojů.`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="51449-599">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="51449-600">Použití Chrome:</span><span class="sxs-lookup"><span data-stu-id="51449-600">Using Chrome:</span></span>

     <span data-ttu-id="51449-601">**`https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` Zásada CORS zablokovala přístup k XMLHttpRequest od původu: v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.**</span><span class="sxs-lookup"><span data-stu-id="51449-601">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="51449-602">Koncové body s podporou CORS se dají testovat pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [post](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="51449-602">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="51449-603">Při použití nástroje se musí původ požadavku určeného `Origin` hlavičkou lišit od hostitele, který požadavek přijal.</span><span class="sxs-lookup"><span data-stu-id="51449-603">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="51449-604">Pokud požadavek není *mezi zdroji* založen na hodnotě `Origin` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="51449-604">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="51449-605">Pro zpracování žádosti není nutné middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-605">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="51449-606">V odpovědi nejsou vraceny hlavičky CORS.</span><span class="sxs-lookup"><span data-stu-id="51449-606">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="51449-607">CORS ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="51449-607">CORS in IIS</span></span>

<span data-ttu-id="51449-608">Při nasazování do služby IIS musí CORS běžet před ověřováním systému Windows, pokud server není nakonfigurovaný tak, aby povoloval anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="51449-608">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="51449-609">Pro podporu tohoto scénáře je nutné nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51449-609">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51449-610">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="51449-610">Additional resources</span></span>

* [<span data-ttu-id="51449-611">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="51449-611">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="51449-612">Začínáme s modulem IIS CORS</span><span class="sxs-lookup"><span data-stu-id="51449-612">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
