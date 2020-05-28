---
<span data-ttu-id="f864f-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-102">'Blazor'</span></span>
- <span data-ttu-id="f864f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-103">'Identity'</span></span>
- <span data-ttu-id="f864f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-105">'Razor'</span></span>
- <span data-ttu-id="f864f-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="f864f-107">Trvalé další deklarace identity a tokeny od externích zprostředkovatelů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f864f-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f864f-108">Aplikace ASP.NET Core může navázat další deklarace identity a tokeny od externích zprostředkovatelů ověřování, jako je Facebook, Google, Microsoft a Twitter.</span><span class="sxs-lookup"><span data-stu-id="f864f-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f864f-109">Každý zprostředkovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci uživatelských dat do dalších deklarací identity je stejný.</span><span class="sxs-lookup"><span data-stu-id="f864f-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f864f-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f864f-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f864f-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f864f-111">Prerequisites</span></span>

<span data-ttu-id="f864f-112">Rozhodněte, které externí zprostředkovatelé ověřování budou v aplikaci podporovat.</span><span class="sxs-lookup"><span data-stu-id="f864f-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f864f-113">Pro každého poskytovatele Zaregistrujte aplikaci a Získejte ID klienta a tajný klíč klienta.</span><span class="sxs-lookup"><span data-stu-id="f864f-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f864f-114">Další informace naleznete v tématu <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f864f-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f864f-115">Ukázková aplikace používá [poskytovatele ověřování Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="f864f-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f864f-116">Nastavení ID klienta a tajného klíče klienta</span><span class="sxs-lookup"><span data-stu-id="f864f-116">Set the client ID and client secret</span></span>

<span data-ttu-id="f864f-117">Zprostředkovatel ověřování OAuth vytvoří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="f864f-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f864f-118">ID klienta a hodnoty tajného klíče klienta jsou vytvořeny pro aplikaci externím zprostředkovatelem ověřování při registraci aplikace u poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="f864f-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f864f-119">Každý externí poskytovatel, který aplikace používá, musí být nakonfigurován nezávisle s ID klienta a tajného kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="f864f-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f864f-120">Další informace najdete v tématech věnovaném externímu poskytovateli ověřování, které se vztahují k vašemu scénáři:</span><span class="sxs-lookup"><span data-stu-id="f864f-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f864f-121">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="f864f-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f864f-122">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="f864f-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f864f-123">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="f864f-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f864f-124">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="f864f-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f864f-125">Další zprostředkovatelé ověřování</span><span class="sxs-lookup"><span data-stu-id="f864f-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f864f-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="f864f-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f864f-127">Ukázková aplikace nakonfiguruje poskytovatele ověřování Google pomocí ID klienta a tajného kódu klienta poskytovaného Google:</span><span class="sxs-lookup"><span data-stu-id="f864f-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f864f-128">Vytvoření oboru ověřování</span><span class="sxs-lookup"><span data-stu-id="f864f-128">Establish the authentication scope</span></span>

<span data-ttu-id="f864f-129">Zadejte seznam oprávnění, která se mají načíst ze zprostředkovatele, zadáním <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="f864f-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f864f-130">V následující tabulce jsou uvedeny obory ověřování pro běžné externí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="f864f-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f864f-131">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="f864f-131">Provider</span></span>  | <span data-ttu-id="f864f-132">Rozsah</span><span class="sxs-lookup"><span data-stu-id="f864f-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="f864f-133">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-134">'Blazor'</span></span>
- <span data-ttu-id="f864f-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-135">'Identity'</span></span>
- <span data-ttu-id="f864f-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-137">'Razor'</span></span>
- <span data-ttu-id="f864f-138">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-139">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-140">'Blazor'</span></span>
- <span data-ttu-id="f864f-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-141">'Identity'</span></span>
- <span data-ttu-id="f864f-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-143">'Razor'</span></span>
- <span data-ttu-id="f864f-144">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-144">'SignalR' uid:</span></span> 

<span data-ttu-id="f864f-145">----- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-146">'Blazor'</span></span>
- <span data-ttu-id="f864f-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-147">'Identity'</span></span>
- <span data-ttu-id="f864f-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-149">'Razor'</span></span>
- <span data-ttu-id="f864f-150">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-151">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-152">'Blazor'</span></span>
- <span data-ttu-id="f864f-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-153">'Identity'</span></span>
- <span data-ttu-id="f864f-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-155">'Razor'</span></span>
- <span data-ttu-id="f864f-156">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-157">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-158">'Blazor'</span></span>
- <span data-ttu-id="f864f-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-159">'Identity'</span></span>
- <span data-ttu-id="f864f-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-161">'Razor'</span></span>
- <span data-ttu-id="f864f-162">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-163">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-164">'Blazor'</span></span>
- <span data-ttu-id="f864f-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-165">'Identity'</span></span>
- <span data-ttu-id="f864f-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-167">'Razor'</span></span>
- <span data-ttu-id="f864f-168">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-169">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-170">'Blazor'</span></span>
- <span data-ttu-id="f864f-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-171">'Identity'</span></span>
- <span data-ttu-id="f864f-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-173">'Razor'</span></span>
- <span data-ttu-id="f864f-174">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-175">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-176">'Blazor'</span></span>
- <span data-ttu-id="f864f-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-177">'Identity'</span></span>
- <span data-ttu-id="f864f-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-179">'Razor'</span></span>
- <span data-ttu-id="f864f-180">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-181">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-182">'Blazor'</span></span>
- <span data-ttu-id="f864f-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-183">'Identity'</span></span>
- <span data-ttu-id="f864f-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-185">'Razor'</span></span>
- <span data-ttu-id="f864f-186">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-187">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-188">'Blazor'</span></span>
- <span data-ttu-id="f864f-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-189">'Identity'</span></span>
- <span data-ttu-id="f864f-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-191">'Razor'</span></span>
- <span data-ttu-id="f864f-192">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-193">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-194">'Blazor'</span></span>
- <span data-ttu-id="f864f-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-195">'Identity'</span></span>
- <span data-ttu-id="f864f-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-197">'Razor'</span></span>
- <span data-ttu-id="f864f-198">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-199">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-200">'Blazor'</span></span>
- <span data-ttu-id="f864f-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-201">'Identity'</span></span>
- <span data-ttu-id="f864f-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-203">'Razor'</span></span>
- <span data-ttu-id="f864f-204">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-205">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-206">'Blazor'</span></span>
- <span data-ttu-id="f864f-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-207">'Identity'</span></span>
- <span data-ttu-id="f864f-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-209">'Razor'</span></span>
- <span data-ttu-id="f864f-210">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-211">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-212">'Blazor'</span></span>
- <span data-ttu-id="f864f-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-213">'Identity'</span></span>
- <span data-ttu-id="f864f-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-215">'Razor'</span></span>
- <span data-ttu-id="f864f-216">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-217">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-218">'Blazor'</span></span>
- <span data-ttu-id="f864f-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-219">'Identity'</span></span>
- <span data-ttu-id="f864f-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-221">'Razor'</span></span>
- <span data-ttu-id="f864f-222">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-223">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-224">'Blazor'</span></span>
- <span data-ttu-id="f864f-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-225">'Identity'</span></span>
- <span data-ttu-id="f864f-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-227">'Razor'</span></span>
- <span data-ttu-id="f864f-228">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-229">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-230">'Blazor'</span></span>
- <span data-ttu-id="f864f-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-231">'Identity'</span></span>
- <span data-ttu-id="f864f-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-233">'Razor'</span></span>
- <span data-ttu-id="f864f-234">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-235">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-236">'Blazor'</span></span>
- <span data-ttu-id="f864f-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-237">'Identity'</span></span>
- <span data-ttu-id="f864f-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-239">'Razor'</span></span>
- <span data-ttu-id="f864f-240">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-241">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-242">'Blazor'</span></span>
- <span data-ttu-id="f864f-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-243">'Identity'</span></span>
- <span data-ttu-id="f864f-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-245">'Razor'</span></span>
- <span data-ttu-id="f864f-246">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-247">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-248">'Blazor'</span></span>
- <span data-ttu-id="f864f-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-249">'Identity'</span></span>
- <span data-ttu-id="f864f-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-251">'Razor'</span></span>
- <span data-ttu-id="f864f-252">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-253">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-254">'Blazor'</span></span>
- <span data-ttu-id="f864f-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-255">'Identity'</span></span>
- <span data-ttu-id="f864f-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-257">'Razor'</span></span>
- <span data-ttu-id="f864f-258">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-259">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-260">'Blazor'</span></span>
- <span data-ttu-id="f864f-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-261">'Identity'</span></span>
- <span data-ttu-id="f864f-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-263">'Razor'</span></span>
- <span data-ttu-id="f864f-264">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-265">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-266">'Blazor'</span></span>
- <span data-ttu-id="f864f-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-267">'Identity'</span></span>
- <span data-ttu-id="f864f-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-269">'Razor'</span></span>
- <span data-ttu-id="f864f-270">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-271">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-272">'Blazor'</span></span>
- <span data-ttu-id="f864f-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-273">'Identity'</span></span>
- <span data-ttu-id="f864f-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-275">'Razor'</span></span>
- <span data-ttu-id="f864f-276">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-277">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-278">'Blazor'</span></span>
- <span data-ttu-id="f864f-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-279">'Identity'</span></span>
- <span data-ttu-id="f864f-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-281">'Razor'</span></span>
- <span data-ttu-id="f864f-282">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-283">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-284">'Blazor'</span></span>
- <span data-ttu-id="f864f-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-285">'Identity'</span></span>
- <span data-ttu-id="f864f-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-287">'Razor'</span></span>
- <span data-ttu-id="f864f-288">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-289">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-290">'Blazor'</span></span>
- <span data-ttu-id="f864f-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-291">'Identity'</span></span>
- <span data-ttu-id="f864f-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-293">'Razor'</span></span>
- <span data-ttu-id="f864f-294">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-295">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-296">'Blazor'</span></span>
- <span data-ttu-id="f864f-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-297">'Identity'</span></span>
- <span data-ttu-id="f864f-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-299">'Razor'</span></span>
- <span data-ttu-id="f864f-300">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-301">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-302">'Blazor'</span></span>
- <span data-ttu-id="f864f-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-303">'Identity'</span></span>
- <span data-ttu-id="f864f-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-305">'Razor'</span></span>
- <span data-ttu-id="f864f-306">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-307">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-308">'Blazor'</span></span>
- <span data-ttu-id="f864f-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-309">'Identity'</span></span>
- <span data-ttu-id="f864f-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-311">'Razor'</span></span>
- <span data-ttu-id="f864f-312">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-313">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-314">'Blazor'</span></span>
- <span data-ttu-id="f864f-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-315">'Identity'</span></span>
- <span data-ttu-id="f864f-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-317">'Razor'</span></span>
- <span data-ttu-id="f864f-318">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-319">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-320">'Blazor'</span></span>
- <span data-ttu-id="f864f-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-321">'Identity'</span></span>
- <span data-ttu-id="f864f-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-323">'Razor'</span></span>
- <span data-ttu-id="f864f-324">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-324">'SignalR' uid:</span></span> 

<span data-ttu-id="f864f-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="f864f-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="f864f-326">V ukázkové aplikaci `userinfo.profile` je obor Google automaticky přidán rozhraním, když <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> je volána na portálu <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="f864f-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f864f-327">Pokud aplikace vyžaduje další obory, přidejte je do možností.</span><span class="sxs-lookup"><span data-stu-id="f864f-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f864f-328">V následujícím příkladu `https://www.googleapis.com/auth/user.birthday.read` je přidaný obor Google, aby bylo možné načíst narozeniny uživatele:</span><span class="sxs-lookup"><span data-stu-id="f864f-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f864f-329">Mapování klíčů uživatelských dat a vytváření deklarací</span><span class="sxs-lookup"><span data-stu-id="f864f-329">Map user data keys and create claims</span></span>

<span data-ttu-id="f864f-330">V možnostech poskytovatele zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč nebo podklíč v datech uživatele JSON externího poskytovatele, aby se identita aplikace četla při přihlášení.</span><span class="sxs-lookup"><span data-stu-id="f864f-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f864f-331">Další informace o typech deklarací identity naleznete v tématu <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="f864f-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f864f-332">Ukázková aplikace vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) z `locale` `picture` klíčů a v datech uživatelů Google:</span><span class="sxs-lookup"><span data-stu-id="f864f-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f864f-333">V je aplikace `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) přihlášena do aplikace pomocí <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="f864f-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f864f-334">Během procesu přihlašování <xref:Microsoft.AspNetCore.Identity.UserManager%601> může aplikace ukládat `ApplicationUser` deklarace identity pro uživatelská data, která jsou k dispozici v <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="f864f-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f864f-335">V ukázkové aplikaci `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) se vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) pro přihlášené `ApplicationUser` , včetně deklarace identity pro <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="f864f-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f864f-336">Ve výchozím nastavení jsou deklarace identity uživatele uloženy v ověřovacím souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f864f-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f864f-337">Pokud je soubor cookie ověřování příliš velký, může dojít k selhání aplikace z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="f864f-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f864f-338">Prohlížeč zjistí, že hlavička souboru cookie je příliš dlouhá.</span><span class="sxs-lookup"><span data-stu-id="f864f-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f864f-339">Celková velikost požadavku je příliš velká.</span><span class="sxs-lookup"><span data-stu-id="f864f-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="f864f-340">Pokud se pro zpracování uživatelských požadavků vyžaduje velké množství uživatelských dat:</span><span class="sxs-lookup"><span data-stu-id="f864f-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f864f-341">Omezte počet a velikost deklarací identity uživatelů pro zpracování žádostí jenom na to, co aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="f864f-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f864f-342"><xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> K ukládání identit napříč požadavky použijte vlastníky pro middleware ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f864f-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f864f-343">Zachovat velké množství informací o identitě na serveru, zatímco do klienta odesílá jenom malý klíč identifikátoru relace.</span><span class="sxs-lookup"><span data-stu-id="f864f-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f864f-344">Uložení přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="f864f-344">Save the access token</span></span>

<span data-ttu-id="f864f-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definuje, jestli se mají <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po úspěšné autorizaci ukládat tokeny přístupu a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="f864f-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f864f-346">`SaveTokens`je nastavené na výchozí hodnotu, `false` aby se snížila velikost konečného ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f864f-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f864f-347">Ukázková aplikace nastaví hodnotu `SaveTokens` na `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="f864f-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f864f-348">Když `OnPostConfirmationAsync` se spustí, uložte přístupový token ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) od externího poskytovatele v `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="f864f-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f864f-349">Ukázková aplikace uloží přístupový token do `OnPostConfirmationAsync` (registrace nového uživatele) a `OnGetCallbackAsync` (dřív registrovaný uživatel) v *účtu/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f864f-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f864f-350">Postup přidání dalších vlastních tokenů</span><span class="sxs-lookup"><span data-stu-id="f864f-350">How to add additional custom tokens</span></span>

<span data-ttu-id="f864f-351">Aby bylo možné předvést, jak přidat vlastní token, který je uložen v rámci `SaveTokens` , ukázková aplikace přidá a <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> aktuální <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="f864f-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f864f-352">Vytváření a přidávání deklarací identity</span><span class="sxs-lookup"><span data-stu-id="f864f-352">Creating and adding claims</span></span>

<span data-ttu-id="f864f-353">Rozhraní poskytuje běžné akce a metody rozšíření pro vytváření a přidávání deklarací identity do kolekce.</span><span class="sxs-lookup"><span data-stu-id="f864f-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f864f-354">Další informace naleznete v tématech <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="f864f-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f864f-355">Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementací abstraktní <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metody.</span><span class="sxs-lookup"><span data-stu-id="f864f-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f864f-356">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f864f-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f864f-357">Odebrání akcí a deklarací identity</span><span class="sxs-lookup"><span data-stu-id="f864f-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="f864f-358">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere všechny akce deklarací identity pro daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekce.</span><span class="sxs-lookup"><span data-stu-id="f864f-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f864f-359">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklaraci identity udělené <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity.</span><span class="sxs-lookup"><span data-stu-id="f864f-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f864f-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se primárně používá s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) k odebrání deklarací generovaných protokolem.</span><span class="sxs-lookup"><span data-stu-id="f864f-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f864f-361">Výstup ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="f864f-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f864f-362">Aplikace ASP.NET Core může navázat další deklarace identity a tokeny od externích zprostředkovatelů ověřování, jako je Facebook, Google, Microsoft a Twitter.</span><span class="sxs-lookup"><span data-stu-id="f864f-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f864f-363">Každý zprostředkovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci uživatelských dat do dalších deklarací identity je stejný.</span><span class="sxs-lookup"><span data-stu-id="f864f-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f864f-364">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f864f-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f864f-365">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f864f-365">Prerequisites</span></span>

<span data-ttu-id="f864f-366">Rozhodněte, které externí zprostředkovatelé ověřování budou v aplikaci podporovat.</span><span class="sxs-lookup"><span data-stu-id="f864f-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f864f-367">Pro každého poskytovatele Zaregistrujte aplikaci a Získejte ID klienta a tajný klíč klienta.</span><span class="sxs-lookup"><span data-stu-id="f864f-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f864f-368">Další informace naleznete v tématu <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f864f-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f864f-369">Ukázková aplikace používá [poskytovatele ověřování Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="f864f-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f864f-370">Nastavení ID klienta a tajného klíče klienta</span><span class="sxs-lookup"><span data-stu-id="f864f-370">Set the client ID and client secret</span></span>

<span data-ttu-id="f864f-371">Zprostředkovatel ověřování OAuth vytvoří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="f864f-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f864f-372">ID klienta a hodnoty tajného klíče klienta jsou vytvořeny pro aplikaci externím zprostředkovatelem ověřování při registraci aplikace u poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="f864f-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f864f-373">Každý externí poskytovatel, který aplikace používá, musí být nakonfigurován nezávisle s ID klienta a tajného kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="f864f-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f864f-374">Další informace najdete v tématech věnovaném externímu poskytovateli ověřování, které se vztahují k vašemu scénáři:</span><span class="sxs-lookup"><span data-stu-id="f864f-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f864f-375">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="f864f-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f864f-376">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="f864f-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f864f-377">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="f864f-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f864f-378">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="f864f-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f864f-379">Další zprostředkovatelé ověřování</span><span class="sxs-lookup"><span data-stu-id="f864f-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f864f-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="f864f-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f864f-381">Ukázková aplikace nakonfiguruje poskytovatele ověřování Google pomocí ID klienta a tajného kódu klienta poskytovaného Google:</span><span class="sxs-lookup"><span data-stu-id="f864f-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f864f-382">Vytvoření oboru ověřování</span><span class="sxs-lookup"><span data-stu-id="f864f-382">Establish the authentication scope</span></span>

<span data-ttu-id="f864f-383">Zadejte seznam oprávnění, která se mají načíst ze zprostředkovatele, zadáním <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="f864f-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f864f-384">V následující tabulce jsou uvedeny obory ověřování pro běžné externí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="f864f-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f864f-385">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="f864f-385">Provider</span></span>  | <span data-ttu-id="f864f-386">Rozsah</span><span class="sxs-lookup"><span data-stu-id="f864f-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="f864f-387">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-388">'Blazor'</span></span>
- <span data-ttu-id="f864f-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-389">'Identity'</span></span>
- <span data-ttu-id="f864f-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-391">'Razor'</span></span>
- <span data-ttu-id="f864f-392">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-393">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-394">'Blazor'</span></span>
- <span data-ttu-id="f864f-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-395">'Identity'</span></span>
- <span data-ttu-id="f864f-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-397">'Razor'</span></span>
- <span data-ttu-id="f864f-398">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-398">'SignalR' uid:</span></span> 

<span data-ttu-id="f864f-399">----- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-400">'Blazor'</span></span>
- <span data-ttu-id="f864f-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-401">'Identity'</span></span>
- <span data-ttu-id="f864f-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-403">'Razor'</span></span>
- <span data-ttu-id="f864f-404">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-405">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-406">'Blazor'</span></span>
- <span data-ttu-id="f864f-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-407">'Identity'</span></span>
- <span data-ttu-id="f864f-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-409">'Razor'</span></span>
- <span data-ttu-id="f864f-410">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-411">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-412">'Blazor'</span></span>
- <span data-ttu-id="f864f-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-413">'Identity'</span></span>
- <span data-ttu-id="f864f-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-415">'Razor'</span></span>
- <span data-ttu-id="f864f-416">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-417">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-418">'Blazor'</span></span>
- <span data-ttu-id="f864f-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-419">'Identity'</span></span>
- <span data-ttu-id="f864f-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-421">'Razor'</span></span>
- <span data-ttu-id="f864f-422">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-423">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-424">'Blazor'</span></span>
- <span data-ttu-id="f864f-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-425">'Identity'</span></span>
- <span data-ttu-id="f864f-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-427">'Razor'</span></span>
- <span data-ttu-id="f864f-428">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-429">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-430">'Blazor'</span></span>
- <span data-ttu-id="f864f-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-431">'Identity'</span></span>
- <span data-ttu-id="f864f-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-433">'Razor'</span></span>
- <span data-ttu-id="f864f-434">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-435">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-436">'Blazor'</span></span>
- <span data-ttu-id="f864f-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-437">'Identity'</span></span>
- <span data-ttu-id="f864f-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-439">'Razor'</span></span>
- <span data-ttu-id="f864f-440">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-441">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-442">'Blazor'</span></span>
- <span data-ttu-id="f864f-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-443">'Identity'</span></span>
- <span data-ttu-id="f864f-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-445">'Razor'</span></span>
- <span data-ttu-id="f864f-446">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-447">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-448">'Blazor'</span></span>
- <span data-ttu-id="f864f-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-449">'Identity'</span></span>
- <span data-ttu-id="f864f-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-451">'Razor'</span></span>
- <span data-ttu-id="f864f-452">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-453">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-454">'Blazor'</span></span>
- <span data-ttu-id="f864f-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-455">'Identity'</span></span>
- <span data-ttu-id="f864f-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-457">'Razor'</span></span>
- <span data-ttu-id="f864f-458">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-459">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-460">'Blazor'</span></span>
- <span data-ttu-id="f864f-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-461">'Identity'</span></span>
- <span data-ttu-id="f864f-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-463">'Razor'</span></span>
- <span data-ttu-id="f864f-464">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-465">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-466">'Blazor'</span></span>
- <span data-ttu-id="f864f-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-467">'Identity'</span></span>
- <span data-ttu-id="f864f-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-469">'Razor'</span></span>
- <span data-ttu-id="f864f-470">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-471">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-472">'Blazor'</span></span>
- <span data-ttu-id="f864f-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-473">'Identity'</span></span>
- <span data-ttu-id="f864f-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-475">'Razor'</span></span>
- <span data-ttu-id="f864f-476">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-477">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-478">'Blazor'</span></span>
- <span data-ttu-id="f864f-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-479">'Identity'</span></span>
- <span data-ttu-id="f864f-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-481">'Razor'</span></span>
- <span data-ttu-id="f864f-482">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-483">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-484">'Blazor'</span></span>
- <span data-ttu-id="f864f-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-485">'Identity'</span></span>
- <span data-ttu-id="f864f-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-487">'Razor'</span></span>
- <span data-ttu-id="f864f-488">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-489">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-490">'Blazor'</span></span>
- <span data-ttu-id="f864f-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-491">'Identity'</span></span>
- <span data-ttu-id="f864f-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-493">'Razor'</span></span>
- <span data-ttu-id="f864f-494">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-495">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-496">'Blazor'</span></span>
- <span data-ttu-id="f864f-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-497">'Identity'</span></span>
- <span data-ttu-id="f864f-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-499">'Razor'</span></span>
- <span data-ttu-id="f864f-500">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-501">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-502">'Blazor'</span></span>
- <span data-ttu-id="f864f-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-503">'Identity'</span></span>
- <span data-ttu-id="f864f-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-505">'Razor'</span></span>
- <span data-ttu-id="f864f-506">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-507">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-508">'Blazor'</span></span>
- <span data-ttu-id="f864f-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-509">'Identity'</span></span>
- <span data-ttu-id="f864f-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-511">'Razor'</span></span>
- <span data-ttu-id="f864f-512">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-513">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-514">'Blazor'</span></span>
- <span data-ttu-id="f864f-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-515">'Identity'</span></span>
- <span data-ttu-id="f864f-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-517">'Razor'</span></span>
- <span data-ttu-id="f864f-518">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-519">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-520">'Blazor'</span></span>
- <span data-ttu-id="f864f-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-521">'Identity'</span></span>
- <span data-ttu-id="f864f-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-523">'Razor'</span></span>
- <span data-ttu-id="f864f-524">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-525">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-526">'Blazor'</span></span>
- <span data-ttu-id="f864f-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-527">'Identity'</span></span>
- <span data-ttu-id="f864f-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-529">'Razor'</span></span>
- <span data-ttu-id="f864f-530">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-531">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-532">'Blazor'</span></span>
- <span data-ttu-id="f864f-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-533">'Identity'</span></span>
- <span data-ttu-id="f864f-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-535">'Razor'</span></span>
- <span data-ttu-id="f864f-536">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-537">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-538">'Blazor'</span></span>
- <span data-ttu-id="f864f-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-539">'Identity'</span></span>
- <span data-ttu-id="f864f-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-541">'Razor'</span></span>
- <span data-ttu-id="f864f-542">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-543">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-544">'Blazor'</span></span>
- <span data-ttu-id="f864f-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-545">'Identity'</span></span>
- <span data-ttu-id="f864f-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-547">'Razor'</span></span>
- <span data-ttu-id="f864f-548">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-549">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-550">'Blazor'</span></span>
- <span data-ttu-id="f864f-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-551">'Identity'</span></span>
- <span data-ttu-id="f864f-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-553">'Razor'</span></span>
- <span data-ttu-id="f864f-554">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-555">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-556">'Blazor'</span></span>
- <span data-ttu-id="f864f-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-557">'Identity'</span></span>
- <span data-ttu-id="f864f-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-559">'Razor'</span></span>
- <span data-ttu-id="f864f-560">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-561">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-562">'Blazor'</span></span>
- <span data-ttu-id="f864f-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-563">'Identity'</span></span>
- <span data-ttu-id="f864f-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-565">'Razor'</span></span>
- <span data-ttu-id="f864f-566">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-567">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-568">'Blazor'</span></span>
- <span data-ttu-id="f864f-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-569">'Identity'</span></span>
- <span data-ttu-id="f864f-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-571">'Razor'</span></span>
- <span data-ttu-id="f864f-572">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f864f-573">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f864f-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f864f-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f864f-574">'Blazor'</span></span>
- <span data-ttu-id="f864f-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f864f-575">'Identity'</span></span>
- <span data-ttu-id="f864f-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f864f-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="f864f-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f864f-577">'Razor'</span></span>
- <span data-ttu-id="f864f-578">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f864f-578">'SignalR' uid:</span></span> 

<span data-ttu-id="f864f-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="f864f-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="f864f-580">V ukázkové aplikaci `userinfo.profile` je obor Google automaticky přidán rozhraním, když <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> je volána na portálu <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="f864f-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f864f-581">Pokud aplikace vyžaduje další obory, přidejte je do možností.</span><span class="sxs-lookup"><span data-stu-id="f864f-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f864f-582">V následujícím příkladu `https://www.googleapis.com/auth/user.birthday.read` je přidaný obor Google, aby bylo možné načíst narozeniny uživatele:</span><span class="sxs-lookup"><span data-stu-id="f864f-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f864f-583">Mapování klíčů uživatelských dat a vytváření deklarací</span><span class="sxs-lookup"><span data-stu-id="f864f-583">Map user data keys and create claims</span></span>

<span data-ttu-id="f864f-584">V možnostech poskytovatele zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč nebo podklíč v datech uživatele JSON externího poskytovatele, aby se identita aplikace četla při přihlášení.</span><span class="sxs-lookup"><span data-stu-id="f864f-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f864f-585">Další informace o typech deklarací identity naleznete v tématu <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="f864f-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f864f-586">Ukázková aplikace vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) z `locale` `picture` klíčů a v datech uživatelů Google:</span><span class="sxs-lookup"><span data-stu-id="f864f-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f864f-587">V je aplikace `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) přihlášena do aplikace pomocí <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="f864f-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f864f-588">Během procesu přihlašování <xref:Microsoft.AspNetCore.Identity.UserManager%601> může aplikace ukládat `ApplicationUser` deklarace identity pro uživatelská data, která jsou k dispozici v <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="f864f-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f864f-589">V ukázkové aplikaci `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) se vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) pro přihlášené `ApplicationUser` , včetně deklarace identity pro <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="f864f-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f864f-590">Ve výchozím nastavení jsou deklarace identity uživatele uloženy v ověřovacím souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f864f-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f864f-591">Pokud je soubor cookie ověřování příliš velký, může dojít k selhání aplikace z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="f864f-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f864f-592">Prohlížeč zjistí, že hlavička souboru cookie je příliš dlouhá.</span><span class="sxs-lookup"><span data-stu-id="f864f-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f864f-593">Celková velikost požadavku je příliš velká.</span><span class="sxs-lookup"><span data-stu-id="f864f-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="f864f-594">Pokud se pro zpracování uživatelských požadavků vyžaduje velké množství uživatelských dat:</span><span class="sxs-lookup"><span data-stu-id="f864f-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f864f-595">Omezte počet a velikost deklarací identity uživatelů pro zpracování žádostí jenom na to, co aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="f864f-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f864f-596"><xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> K ukládání identit napříč požadavky použijte vlastníky pro middleware ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f864f-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f864f-597">Zachovat velké množství informací o identitě na serveru, zatímco do klienta odesílá jenom malý klíč identifikátoru relace.</span><span class="sxs-lookup"><span data-stu-id="f864f-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f864f-598">Uložení přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="f864f-598">Save the access token</span></span>

<span data-ttu-id="f864f-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definuje, jestli se mají <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po úspěšné autorizaci ukládat tokeny přístupu a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="f864f-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f864f-600">`SaveTokens`je nastavené na výchozí hodnotu, `false` aby se snížila velikost konečného ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f864f-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f864f-601">Ukázková aplikace nastaví hodnotu `SaveTokens` na `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="f864f-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f864f-602">Když `OnPostConfirmationAsync` se spustí, uložte přístupový token ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) od externího poskytovatele v `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="f864f-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f864f-603">Ukázková aplikace uloží přístupový token do `OnPostConfirmationAsync` (registrace nového uživatele) a `OnGetCallbackAsync` (dřív registrovaný uživatel) v *účtu/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f864f-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f864f-604">Postup přidání dalších vlastních tokenů</span><span class="sxs-lookup"><span data-stu-id="f864f-604">How to add additional custom tokens</span></span>

<span data-ttu-id="f864f-605">Aby bylo možné předvést, jak přidat vlastní token, který je uložen v rámci `SaveTokens` , ukázková aplikace přidá a <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> aktuální <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="f864f-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f864f-606">Vytváření a přidávání deklarací identity</span><span class="sxs-lookup"><span data-stu-id="f864f-606">Creating and adding claims</span></span>

<span data-ttu-id="f864f-607">Rozhraní poskytuje běžné akce a metody rozšíření pro vytváření a přidávání deklarací identity do kolekce.</span><span class="sxs-lookup"><span data-stu-id="f864f-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f864f-608">Další informace naleznete v tématech <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="f864f-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f864f-609">Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementací abstraktní <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metody.</span><span class="sxs-lookup"><span data-stu-id="f864f-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f864f-610">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f864f-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f864f-611">Odebrání akcí a deklarací identity</span><span class="sxs-lookup"><span data-stu-id="f864f-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="f864f-612">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere všechny akce deklarací identity pro daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekce.</span><span class="sxs-lookup"><span data-stu-id="f864f-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f864f-613">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklaraci identity udělené <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity.</span><span class="sxs-lookup"><span data-stu-id="f864f-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f864f-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se primárně používá s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) k odebrání deklarací generovaných protokolem.</span><span class="sxs-lookup"><span data-stu-id="f864f-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f864f-615">Výstup ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="f864f-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f864f-616">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f864f-616">Additional resources</span></span>

* <span data-ttu-id="f864f-617">[dotnet/AspNetCore Engineering SocialSample App](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): propojená ukázková aplikace je v technické větvi [úložiště dotnet/AspNetCore GitHub](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="f864f-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="f864f-618">`master`Větev obsahuje kód v rámci aktivního vývoje pro další vydání ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f864f-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="f864f-619">Pokud chcete zobrazit verzi ukázkové aplikace pro vydanou verzi ASP.NET Core, použijte rozevírací seznam **větev** a vyberte větev vydané verze (například `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="f864f-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
