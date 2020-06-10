---
title: Trvalé další deklarace identity a tokeny od externích zprostředkovatelů v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet další deklarace identity a tokeny od externích poskytovatelů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/additional-claims
ms.openlocfilehash: ed1f4d0d3da4ad032c6d6e4a00c989f8c6380b31
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106010"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="49c54-103">Trvalé další deklarace identity a tokeny od externích zprostředkovatelů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49c54-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49c54-104">Aplikace ASP.NET Core může navázat další deklarace identity a tokeny od externích zprostředkovatelů ověřování, jako je Facebook, Google, Microsoft a Twitter.</span><span class="sxs-lookup"><span data-stu-id="49c54-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="49c54-105">Každý zprostředkovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci uživatelských dat do dalších deklarací identity je stejný.</span><span class="sxs-lookup"><span data-stu-id="49c54-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="49c54-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49c54-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49c54-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="49c54-107">Prerequisites</span></span>

<span data-ttu-id="49c54-108">Rozhodněte, které externí zprostředkovatelé ověřování budou v aplikaci podporovat.</span><span class="sxs-lookup"><span data-stu-id="49c54-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="49c54-109">Pro každého poskytovatele Zaregistrujte aplikaci a Získejte ID klienta a tajný klíč klienta.</span><span class="sxs-lookup"><span data-stu-id="49c54-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="49c54-110">Další informace naleznete v tématu <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="49c54-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="49c54-111">Ukázková aplikace používá [poskytovatele ověřování Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="49c54-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="49c54-112">Nastavení ID klienta a tajného klíče klienta</span><span class="sxs-lookup"><span data-stu-id="49c54-112">Set the client ID and client secret</span></span>

<span data-ttu-id="49c54-113">Zprostředkovatel ověřování OAuth vytvoří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="49c54-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="49c54-114">ID klienta a hodnoty tajného klíče klienta jsou vytvořeny pro aplikaci externím zprostředkovatelem ověřování při registraci aplikace u poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="49c54-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="49c54-115">Každý externí poskytovatel, který aplikace používá, musí být nakonfigurován nezávisle s ID klienta a tajného kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="49c54-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="49c54-116">Další informace najdete v tématech věnovaném externímu poskytovateli ověřování, které se vztahují k vašemu scénáři:</span><span class="sxs-lookup"><span data-stu-id="49c54-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="49c54-117">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="49c54-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="49c54-118">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="49c54-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="49c54-119">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="49c54-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="49c54-120">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="49c54-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="49c54-121">Další zprostředkovatelé ověřování</span><span class="sxs-lookup"><span data-stu-id="49c54-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="49c54-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="49c54-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="49c54-123">Ukázková aplikace nakonfiguruje poskytovatele ověřování Google pomocí ID klienta a tajného kódu klienta poskytovaného Google:</span><span class="sxs-lookup"><span data-stu-id="49c54-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="49c54-124">Vytvoření oboru ověřování</span><span class="sxs-lookup"><span data-stu-id="49c54-124">Establish the authentication scope</span></span>

<span data-ttu-id="49c54-125">Zadejte seznam oprávnění, která se mají načíst ze zprostředkovatele, zadáním <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="49c54-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="49c54-126">V následující tabulce jsou uvedeny obory ověřování pro běžné externí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="49c54-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="49c54-127">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="49c54-127">Provider</span></span>  | <span data-ttu-id="49c54-128">Rozsah</span><span class="sxs-lookup"><span data-stu-id="49c54-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="49c54-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="49c54-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="49c54-130">Google</span><span class="sxs-lookup"><span data-stu-id="49c54-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="49c54-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="49c54-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="49c54-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="49c54-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="49c54-133">V ukázkové aplikaci `userinfo.profile` je obor Google automaticky přidán rozhraním, když <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> je volána na portálu <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="49c54-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="49c54-134">Pokud aplikace vyžaduje další obory, přidejte je do možností.</span><span class="sxs-lookup"><span data-stu-id="49c54-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="49c54-135">V následujícím příkladu `https://www.googleapis.com/auth/user.birthday.read` je přidaný obor Google, aby bylo možné načíst narozeniny uživatele:</span><span class="sxs-lookup"><span data-stu-id="49c54-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="49c54-136">Mapování klíčů uživatelských dat a vytváření deklarací</span><span class="sxs-lookup"><span data-stu-id="49c54-136">Map user data keys and create claims</span></span>

<span data-ttu-id="49c54-137">V možnostech poskytovatele zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč nebo podklíč v datech uživatele JSON externího poskytovatele, aby se identita aplikace četla při přihlášení.</span><span class="sxs-lookup"><span data-stu-id="49c54-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="49c54-138">Další informace o typech deklarací identity naleznete v tématu <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="49c54-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="49c54-139">Ukázková aplikace vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) z `locale` `picture` klíčů a v datech uživatelů Google:</span><span class="sxs-lookup"><span data-stu-id="49c54-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="49c54-140">V je aplikace `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) přihlášena do aplikace pomocí <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="49c54-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="49c54-141">Během procesu přihlašování <xref:Microsoft.AspNetCore.Identity.UserManager%601> může aplikace ukládat `ApplicationUser` deklarace identity pro uživatelská data, která jsou k dispozici v <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="49c54-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="49c54-142">V ukázkové aplikaci `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) se vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) pro přihlášené `ApplicationUser` , včetně deklarace identity pro <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="49c54-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="49c54-143">Ve výchozím nastavení jsou deklarace identity uživatele uloženy v ověřovacím souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="49c54-143">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="49c54-144">Pokud je soubor cookie ověřování příliš velký, může dojít k selhání aplikace z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="49c54-144">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="49c54-145">Prohlížeč zjistí, že hlavička souboru cookie je příliš dlouhá.</span><span class="sxs-lookup"><span data-stu-id="49c54-145">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="49c54-146">Celková velikost požadavku je příliš velká.</span><span class="sxs-lookup"><span data-stu-id="49c54-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="49c54-147">Pokud se pro zpracování uživatelských požadavků vyžaduje velké množství uživatelských dat:</span><span class="sxs-lookup"><span data-stu-id="49c54-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="49c54-148">Omezte počet a velikost deklarací identity uživatelů pro zpracování žádostí jenom na to, co aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="49c54-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="49c54-149"><xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> K ukládání identit napříč požadavky použijte vlastníky pro middleware ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="49c54-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="49c54-150">Zachovat velké množství informací o identitě na serveru, zatímco do klienta odesílá jenom malý klíč identifikátoru relace.</span><span class="sxs-lookup"><span data-stu-id="49c54-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="49c54-151">Uložení přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="49c54-151">Save the access token</span></span>

<span data-ttu-id="49c54-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definuje, jestli se mají <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po úspěšné autorizaci ukládat tokeny přístupu a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="49c54-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="49c54-153">`SaveTokens`je nastavené na výchozí hodnotu, `false` aby se snížila velikost konečného ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="49c54-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="49c54-154">Ukázková aplikace nastaví hodnotu `SaveTokens` na `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="49c54-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="49c54-155">Když `OnPostConfirmationAsync` se spustí, uložte přístupový token ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) od externího poskytovatele v `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="49c54-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="49c54-156">Ukázková aplikace uloží přístupový token do `OnPostConfirmationAsync` (registrace nového uživatele) a `OnGetCallbackAsync` (dřív registrovaný uživatel) v *účtu/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="49c54-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="49c54-157">Postup přidání dalších vlastních tokenů</span><span class="sxs-lookup"><span data-stu-id="49c54-157">How to add additional custom tokens</span></span>

<span data-ttu-id="49c54-158">Aby bylo možné předvést, jak přidat vlastní token, který je uložen v rámci `SaveTokens` , ukázková aplikace přidá a <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> aktuální <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="49c54-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="49c54-159">Vytváření a přidávání deklarací identity</span><span class="sxs-lookup"><span data-stu-id="49c54-159">Creating and adding claims</span></span>

<span data-ttu-id="49c54-160">Rozhraní poskytuje běžné akce a metody rozšíření pro vytváření a přidávání deklarací identity do kolekce.</span><span class="sxs-lookup"><span data-stu-id="49c54-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="49c54-161">Další informace naleznete v tématech <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="49c54-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="49c54-162">Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementací abstraktní <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metody.</span><span class="sxs-lookup"><span data-stu-id="49c54-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="49c54-163">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="49c54-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="49c54-164">Odebrání akcí a deklarací identity</span><span class="sxs-lookup"><span data-stu-id="49c54-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="49c54-165">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere všechny akce deklarací identity pro daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekce.</span><span class="sxs-lookup"><span data-stu-id="49c54-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="49c54-166">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklaraci identity udělené <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity.</span><span class="sxs-lookup"><span data-stu-id="49c54-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="49c54-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se primárně používá s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) k odebrání deklarací generovaných protokolem.</span><span class="sxs-lookup"><span data-stu-id="49c54-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="49c54-168">Výstup ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="49c54-168">Sample app output</span></span>

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

<span data-ttu-id="49c54-169">Aplikace ASP.NET Core může navázat další deklarace identity a tokeny od externích zprostředkovatelů ověřování, jako je Facebook, Google, Microsoft a Twitter.</span><span class="sxs-lookup"><span data-stu-id="49c54-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="49c54-170">Každý zprostředkovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci uživatelských dat do dalších deklarací identity je stejný.</span><span class="sxs-lookup"><span data-stu-id="49c54-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="49c54-171">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49c54-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49c54-172">Požadavky</span><span class="sxs-lookup"><span data-stu-id="49c54-172">Prerequisites</span></span>

<span data-ttu-id="49c54-173">Rozhodněte, které externí zprostředkovatelé ověřování budou v aplikaci podporovat.</span><span class="sxs-lookup"><span data-stu-id="49c54-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="49c54-174">Pro každého poskytovatele Zaregistrujte aplikaci a Získejte ID klienta a tajný klíč klienta.</span><span class="sxs-lookup"><span data-stu-id="49c54-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="49c54-175">Další informace naleznete v tématu <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="49c54-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="49c54-176">Ukázková aplikace používá [poskytovatele ověřování Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="49c54-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="49c54-177">Nastavení ID klienta a tajného klíče klienta</span><span class="sxs-lookup"><span data-stu-id="49c54-177">Set the client ID and client secret</span></span>

<span data-ttu-id="49c54-178">Zprostředkovatel ověřování OAuth vytvoří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="49c54-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="49c54-179">ID klienta a hodnoty tajného klíče klienta jsou vytvořeny pro aplikaci externím zprostředkovatelem ověřování při registraci aplikace u poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="49c54-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="49c54-180">Každý externí poskytovatel, který aplikace používá, musí být nakonfigurován nezávisle s ID klienta a tajného kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="49c54-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="49c54-181">Další informace najdete v tématech věnovaném externímu poskytovateli ověřování, které se vztahují k vašemu scénáři:</span><span class="sxs-lookup"><span data-stu-id="49c54-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="49c54-182">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="49c54-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="49c54-183">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="49c54-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="49c54-184">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="49c54-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="49c54-185">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="49c54-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="49c54-186">Další zprostředkovatelé ověřování</span><span class="sxs-lookup"><span data-stu-id="49c54-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="49c54-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="49c54-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="49c54-188">Ukázková aplikace nakonfiguruje poskytovatele ověřování Google pomocí ID klienta a tajného kódu klienta poskytovaného Google:</span><span class="sxs-lookup"><span data-stu-id="49c54-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="49c54-189">Vytvoření oboru ověřování</span><span class="sxs-lookup"><span data-stu-id="49c54-189">Establish the authentication scope</span></span>

<span data-ttu-id="49c54-190">Zadejte seznam oprávnění, která se mají načíst ze zprostředkovatele, zadáním <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="49c54-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="49c54-191">V následující tabulce jsou uvedeny obory ověřování pro běžné externí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="49c54-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="49c54-192">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="49c54-192">Provider</span></span>  | <span data-ttu-id="49c54-193">Rozsah</span><span class="sxs-lookup"><span data-stu-id="49c54-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="49c54-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="49c54-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="49c54-195">Google</span><span class="sxs-lookup"><span data-stu-id="49c54-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="49c54-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="49c54-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="49c54-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="49c54-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="49c54-198">V ukázkové aplikaci `userinfo.profile` je obor Google automaticky přidán rozhraním, když <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> je volána na portálu <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="49c54-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="49c54-199">Pokud aplikace vyžaduje další obory, přidejte je do možností.</span><span class="sxs-lookup"><span data-stu-id="49c54-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="49c54-200">V následujícím příkladu `https://www.googleapis.com/auth/user.birthday.read` je přidaný obor Google, aby bylo možné načíst narozeniny uživatele:</span><span class="sxs-lookup"><span data-stu-id="49c54-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="49c54-201">Mapování klíčů uživatelských dat a vytváření deklarací</span><span class="sxs-lookup"><span data-stu-id="49c54-201">Map user data keys and create claims</span></span>

<span data-ttu-id="49c54-202">V možnostech poskytovatele zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč nebo podklíč v datech uživatele JSON externího poskytovatele, aby se identita aplikace četla při přihlášení.</span><span class="sxs-lookup"><span data-stu-id="49c54-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="49c54-203">Další informace o typech deklarací identity naleznete v tématu <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="49c54-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="49c54-204">Ukázková aplikace vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) z `locale` `picture` klíčů a v datech uživatelů Google:</span><span class="sxs-lookup"><span data-stu-id="49c54-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="49c54-205">V je aplikace `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) přihlášena do aplikace pomocí <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="49c54-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="49c54-206">Během procesu přihlašování <xref:Microsoft.AspNetCore.Identity.UserManager%601> může aplikace ukládat `ApplicationUser` deklarace identity pro uživatelská data, která jsou k dispozici v <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="49c54-206">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="49c54-207">V ukázkové aplikaci `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) se vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) pro přihlášené `ApplicationUser` , včetně deklarace identity pro <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="49c54-207">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="49c54-208">Ve výchozím nastavení jsou deklarace identity uživatele uloženy v ověřovacím souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="49c54-208">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="49c54-209">Pokud je soubor cookie ověřování příliš velký, může dojít k selhání aplikace z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="49c54-209">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="49c54-210">Prohlížeč zjistí, že hlavička souboru cookie je příliš dlouhá.</span><span class="sxs-lookup"><span data-stu-id="49c54-210">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="49c54-211">Celková velikost požadavku je příliš velká.</span><span class="sxs-lookup"><span data-stu-id="49c54-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="49c54-212">Pokud se pro zpracování uživatelských požadavků vyžaduje velké množství uživatelských dat:</span><span class="sxs-lookup"><span data-stu-id="49c54-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="49c54-213">Omezte počet a velikost deklarací identity uživatelů pro zpracování žádostí jenom na to, co aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="49c54-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="49c54-214"><xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> K ukládání identit napříč požadavky použijte vlastníky pro middleware ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="49c54-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="49c54-215">Zachovat velké množství informací o identitě na serveru, zatímco do klienta odesílá jenom malý klíč identifikátoru relace.</span><span class="sxs-lookup"><span data-stu-id="49c54-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="49c54-216">Uložení přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="49c54-216">Save the access token</span></span>

<span data-ttu-id="49c54-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definuje, jestli se mají <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po úspěšné autorizaci ukládat tokeny přístupu a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="49c54-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="49c54-218">`SaveTokens`je nastavené na výchozí hodnotu, `false` aby se snížila velikost konečného ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="49c54-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="49c54-219">Ukázková aplikace nastaví hodnotu `SaveTokens` na `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="49c54-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="49c54-220">Když `OnPostConfirmationAsync` se spustí, uložte přístupový token ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) od externího poskytovatele v `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="49c54-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="49c54-221">Ukázková aplikace uloží přístupový token do `OnPostConfirmationAsync` (registrace nového uživatele) a `OnGetCallbackAsync` (dřív registrovaný uživatel) v *účtu/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="49c54-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="49c54-222">Postup přidání dalších vlastních tokenů</span><span class="sxs-lookup"><span data-stu-id="49c54-222">How to add additional custom tokens</span></span>

<span data-ttu-id="49c54-223">Aby bylo možné předvést, jak přidat vlastní token, který je uložen v rámci `SaveTokens` , ukázková aplikace přidá a <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> aktuální <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="49c54-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="49c54-224">Vytváření a přidávání deklarací identity</span><span class="sxs-lookup"><span data-stu-id="49c54-224">Creating and adding claims</span></span>

<span data-ttu-id="49c54-225">Rozhraní poskytuje běžné akce a metody rozšíření pro vytváření a přidávání deklarací identity do kolekce.</span><span class="sxs-lookup"><span data-stu-id="49c54-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="49c54-226">Další informace naleznete v tématech <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="49c54-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="49c54-227">Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementací abstraktní <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metody.</span><span class="sxs-lookup"><span data-stu-id="49c54-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="49c54-228">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="49c54-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="49c54-229">Odebrání akcí a deklarací identity</span><span class="sxs-lookup"><span data-stu-id="49c54-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="49c54-230">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere všechny akce deklarací identity pro daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekce.</span><span class="sxs-lookup"><span data-stu-id="49c54-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="49c54-231">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklaraci identity udělené <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity.</span><span class="sxs-lookup"><span data-stu-id="49c54-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="49c54-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se primárně používá s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) k odebrání deklarací generovaných protokolem.</span><span class="sxs-lookup"><span data-stu-id="49c54-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="49c54-233">Výstup ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="49c54-233">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="49c54-234">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="49c54-234">Additional resources</span></span>

* <span data-ttu-id="49c54-235">[dotnet/AspNetCore Engineering SocialSample App](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): propojená ukázková aplikace je v technické větvi [úložiště dotnet/AspNetCore GitHub](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="49c54-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="49c54-236">`master`Větev obsahuje kód v rámci aktivního vývoje pro další vydání ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49c54-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="49c54-237">Pokud chcete zobrazit verzi ukázkové aplikace pro vydanou verzi ASP.NET Core, použijte rozevírací seznam **větev** a vyberte větev vydané verze (například `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="49c54-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
