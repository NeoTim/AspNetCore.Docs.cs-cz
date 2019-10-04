---
title: Trvalé další deklarace identity a tokeny od externích zprostředkovatelů v ASP.NET Core
author: guardrex
description: Naučte se vytvářet další deklarace identity a tokeny od externích poskytovatelů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: cdf263df8d1aa17ea3820a16ecbd10abce9d683d
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925158"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="52898-103">Trvalé další deklarace identity a tokeny od externích zprostředkovatelů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52898-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="52898-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="52898-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52898-105">Aplikace ASP.NET Core může navázat další deklarace identity a tokeny od externích zprostředkovatelů ověřování, jako je Facebook, Google, Microsoft a Twitter.</span><span class="sxs-lookup"><span data-stu-id="52898-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="52898-106">Každý zprostředkovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci uživatelských dat do dalších deklarací identity je stejný.</span><span class="sxs-lookup"><span data-stu-id="52898-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="52898-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="52898-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="52898-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="52898-108">Prerequisites</span></span>

<span data-ttu-id="52898-109">Rozhodněte, které externí zprostředkovatelé ověřování budou v aplikaci podporovat.</span><span class="sxs-lookup"><span data-stu-id="52898-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="52898-110">Pro každého poskytovatele Zaregistrujte aplikaci a Získejte ID klienta a tajný klíč klienta.</span><span class="sxs-lookup"><span data-stu-id="52898-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="52898-111">Další informace naleznete v tématu <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="52898-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="52898-112">Ukázková aplikace používá [poskytovatele ověřování Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="52898-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="52898-113">Nastavení ID klienta a tajného klíče klienta</span><span class="sxs-lookup"><span data-stu-id="52898-113">Set the client ID and client secret</span></span>

<span data-ttu-id="52898-114">Zprostředkovatel ověřování OAuth vytvoří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="52898-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="52898-115">ID klienta a hodnoty tajného klíče klienta jsou vytvořeny pro aplikaci externím zprostředkovatelem ověřování při registraci aplikace u poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="52898-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="52898-116">Každý externí poskytovatel, který aplikace používá, musí být nakonfigurován nezávisle s ID klienta a tajného kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="52898-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="52898-117">Další informace najdete v tématech věnovaném externímu poskytovateli ověřování, které se vztahují k vašemu scénáři:</span><span class="sxs-lookup"><span data-stu-id="52898-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="52898-118">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="52898-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="52898-119">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="52898-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="52898-120">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="52898-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="52898-121">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="52898-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="52898-122">Další zprostředkovatelé ověřování</span><span class="sxs-lookup"><span data-stu-id="52898-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="52898-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="52898-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="52898-124">Ukázková aplikace nakonfiguruje poskytovatele ověřování Google pomocí ID klienta a tajného kódu klienta poskytovaného Google:</span><span class="sxs-lookup"><span data-stu-id="52898-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="52898-125">Vytvoření oboru ověřování</span><span class="sxs-lookup"><span data-stu-id="52898-125">Establish the authentication scope</span></span>

<span data-ttu-id="52898-126">Zadejte seznam oprávnění, která se mají načíst ze zprostředkovatele, zadáním <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="52898-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="52898-127">V následující tabulce jsou uvedeny obory ověřování pro běžné externí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="52898-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="52898-128">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="52898-128">Provider</span></span>  | <span data-ttu-id="52898-129">Scope</span><span class="sxs-lookup"><span data-stu-id="52898-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="52898-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="52898-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="52898-131">Google</span><span class="sxs-lookup"><span data-stu-id="52898-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="52898-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="52898-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="52898-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="52898-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="52898-134">V ukázkové aplikaci je obor Google `userinfo.profile` automaticky přidán rozhraním, když je <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> volána na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="52898-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="52898-135">Pokud aplikace vyžaduje další obory, přidejte je do možností.</span><span class="sxs-lookup"><span data-stu-id="52898-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="52898-136">V následujícím příkladu je přidaný obor Google `https://www.googleapis.com/auth/user.birthday.read`, aby bylo možné načíst narozeniny uživatele:</span><span class="sxs-lookup"><span data-stu-id="52898-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="52898-137">Mapování klíčů uživatelských dat a vytváření deklarací</span><span class="sxs-lookup"><span data-stu-id="52898-137">Map user data keys and create claims</span></span>

<span data-ttu-id="52898-138">V možnostech poskytovatele zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč nebo podklíč v datech uživatele JSON externího poskytovatele, aby se identita aplikace četla při přihlášení.</span><span class="sxs-lookup"><span data-stu-id="52898-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="52898-139">Další informace o typech deklarací identity najdete v tématu <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="52898-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="52898-140">Ukázková aplikace vytvoří deklarace národního prostředí (`urn:google:locale`) a obrázku (`urn:google:picture`) z klíčů `locale` a `picture` v datech uživatelů Google:</span><span class="sxs-lookup"><span data-stu-id="52898-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="52898-141">V <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*> se do aplikace přihlásí <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) pomocí <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="52898-141">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="52898-142">Během procesu přihlašování může <xref:Microsoft.AspNetCore.Identity.UserManager%601> ukládat deklarace identity `ApplicationUser` pro uživatelská data, která jsou k dispozici v <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="52898-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="52898-143">V ukázkové aplikaci `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) vytvoří deklarace národního prostředí (`urn:google:locale`) a obrázku (`urn:google:picture`) pro přihlášený `ApplicationUser`, včetně deklarace pro <xref:System.Security.Claims.ClaimTypes.GivenName>:</span><span class="sxs-lookup"><span data-stu-id="52898-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="52898-144">Ve výchozím nastavení jsou deklarace identity uživatele uloženy v ověřovacím souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="52898-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="52898-145">Pokud je soubor cookie ověřování příliš velký, může dojít k selhání aplikace z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="52898-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="52898-146">Prohlížeč zjistí, že hlavička souboru cookie je příliš dlouhá.</span><span class="sxs-lookup"><span data-stu-id="52898-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="52898-147">Celková velikost požadavku je příliš velká.</span><span class="sxs-lookup"><span data-stu-id="52898-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="52898-148">Pokud se pro zpracování uživatelských požadavků vyžaduje velké množství uživatelských dat:</span><span class="sxs-lookup"><span data-stu-id="52898-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="52898-149">Omezte počet a velikost deklarací identity uživatelů pro zpracování žádostí jenom na to, co aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="52898-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="52898-150">Pro uložení identity mezi požadavky použijte vlastní <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pro @no__t middlewaru ověřování souborů cookie-1.</span><span class="sxs-lookup"><span data-stu-id="52898-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="52898-151">Zachovat velké množství informací o identitě na serveru, zatímco do klienta odesílá jenom malý klíč identifikátoru relace.</span><span class="sxs-lookup"><span data-stu-id="52898-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="52898-152">Uložení přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="52898-152">Save the access token</span></span>

<span data-ttu-id="52898-153">@no__t – 0 definuje, jestli se po úspěšné autorizaci mají v <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ukládat tokeny přístupu a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="52898-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="52898-154">`SaveTokens` je ve výchozím nastavení nastavená na `false`, aby se snížila velikost konečného ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="52898-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="52898-155">Ukázková aplikace nastaví hodnotu `SaveTokens` na `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="52898-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="52898-156">Když se spustí `OnPostConfirmationAsync`, uložte přístupový token ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) od externího poskytovatele v `AuthenticationProperties` `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="52898-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="52898-157">Ukázková aplikace uloží přístupový token do `OnPostConfirmationAsync` (registrace nového uživatele) a `OnGetCallbackAsync` (dřív registrovaný uživatel) v *účtu/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="52898-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="52898-158">Postup přidání dalších vlastních tokenů</span><span class="sxs-lookup"><span data-stu-id="52898-158">How to add additional custom tokens</span></span>

<span data-ttu-id="52898-159">Chcete-li předvést, jak přidat vlastní token, který je uložen jako součást `SaveTokens`, ukázková aplikace přidá <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> s aktuálním <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="52898-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="52898-160">Vytváření a přidávání deklarací identity</span><span class="sxs-lookup"><span data-stu-id="52898-160">Creating and adding claims</span></span>

<span data-ttu-id="52898-161">Rozhraní poskytuje běžné akce a metody rozšíření pro vytváření a přidávání deklarací identity do kolekce.</span><span class="sxs-lookup"><span data-stu-id="52898-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="52898-162">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="52898-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="52898-163">Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementací abstraktní metody <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>.</span><span class="sxs-lookup"><span data-stu-id="52898-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="52898-164">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="52898-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="52898-165">Odebrání akcí a deklarací identity</span><span class="sxs-lookup"><span data-stu-id="52898-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="52898-166">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere z kolekce všechny akce deklarací identity pro daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>.</span><span class="sxs-lookup"><span data-stu-id="52898-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="52898-167">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklaraci identity daného <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity.</span><span class="sxs-lookup"><span data-stu-id="52898-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="52898-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> se primárně používá s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) k odebrání deklarací generovaných protokolem.</span><span class="sxs-lookup"><span data-stu-id="52898-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="52898-169">Výstup ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="52898-169">Sample app output</span></span>

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

<span data-ttu-id="52898-170">Aplikace ASP.NET Core může navázat další deklarace identity a tokeny od externích zprostředkovatelů ověřování, jako je Facebook, Google, Microsoft a Twitter.</span><span class="sxs-lookup"><span data-stu-id="52898-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="52898-171">Každý zprostředkovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci uživatelských dat do dalších deklarací identity je stejný.</span><span class="sxs-lookup"><span data-stu-id="52898-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="52898-172">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="52898-172">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="52898-173">Požadavky</span><span class="sxs-lookup"><span data-stu-id="52898-173">Prerequisites</span></span>

<span data-ttu-id="52898-174">Rozhodněte, které externí zprostředkovatelé ověřování budou v aplikaci podporovat.</span><span class="sxs-lookup"><span data-stu-id="52898-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="52898-175">Pro každého poskytovatele Zaregistrujte aplikaci a Získejte ID klienta a tajný klíč klienta.</span><span class="sxs-lookup"><span data-stu-id="52898-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="52898-176">Další informace naleznete v tématu <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="52898-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="52898-177">Ukázková aplikace používá [poskytovatele ověřování Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="52898-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="52898-178">Nastavení ID klienta a tajného klíče klienta</span><span class="sxs-lookup"><span data-stu-id="52898-178">Set the client ID and client secret</span></span>

<span data-ttu-id="52898-179">Zprostředkovatel ověřování OAuth vytvoří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="52898-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="52898-180">ID klienta a hodnoty tajného klíče klienta jsou vytvořeny pro aplikaci externím zprostředkovatelem ověřování při registraci aplikace u poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="52898-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="52898-181">Každý externí poskytovatel, který aplikace používá, musí být nakonfigurován nezávisle s ID klienta a tajného kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="52898-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="52898-182">Další informace najdete v tématech věnovaném externímu poskytovateli ověřování, které se vztahují k vašemu scénáři:</span><span class="sxs-lookup"><span data-stu-id="52898-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="52898-183">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="52898-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="52898-184">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="52898-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="52898-185">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="52898-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="52898-186">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="52898-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="52898-187">Další zprostředkovatelé ověřování</span><span class="sxs-lookup"><span data-stu-id="52898-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="52898-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="52898-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="52898-189">Ukázková aplikace nakonfiguruje poskytovatele ověřování Google pomocí ID klienta a tajného kódu klienta poskytovaného Google:</span><span class="sxs-lookup"><span data-stu-id="52898-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="52898-190">Vytvoření oboru ověřování</span><span class="sxs-lookup"><span data-stu-id="52898-190">Establish the authentication scope</span></span>

<span data-ttu-id="52898-191">Zadejte seznam oprávnění, která se mají načíst ze zprostředkovatele, zadáním <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="52898-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="52898-192">V následující tabulce jsou uvedeny obory ověřování pro běžné externí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="52898-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="52898-193">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="52898-193">Provider</span></span>  | <span data-ttu-id="52898-194">Scope</span><span class="sxs-lookup"><span data-stu-id="52898-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="52898-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="52898-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="52898-196">Google</span><span class="sxs-lookup"><span data-stu-id="52898-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="52898-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="52898-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="52898-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="52898-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="52898-199">V ukázkové aplikaci je obor Google `userinfo.profile` automaticky přidán rozhraním, když je <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> volána na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="52898-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="52898-200">Pokud aplikace vyžaduje další obory, přidejte je do možností.</span><span class="sxs-lookup"><span data-stu-id="52898-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="52898-201">V následujícím příkladu je přidaný obor Google `https://www.googleapis.com/auth/user.birthday.read`, aby bylo možné načíst narozeniny uživatele:</span><span class="sxs-lookup"><span data-stu-id="52898-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="52898-202">Mapování klíčů uživatelských dat a vytváření deklarací</span><span class="sxs-lookup"><span data-stu-id="52898-202">Map user data keys and create claims</span></span>

<span data-ttu-id="52898-203">V možnostech poskytovatele zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč nebo podklíč v datech uživatele JSON externího poskytovatele, aby se identita aplikace četla při přihlášení.</span><span class="sxs-lookup"><span data-stu-id="52898-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="52898-204">Další informace o typech deklarací identity najdete v tématu <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="52898-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="52898-205">Ukázková aplikace vytvoří deklarace národního prostředí (`urn:google:locale`) a obrázku (`urn:google:picture`) z klíčů `locale` a `picture` v datech uživatelů Google:</span><span class="sxs-lookup"><span data-stu-id="52898-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="52898-206">V <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*> se do aplikace přihlásí <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) pomocí <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="52898-206">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="52898-207">Během procesu přihlašování může <xref:Microsoft.AspNetCore.Identity.UserManager%601> ukládat deklarace identity `ApplicationUser` pro uživatelská data, která jsou k dispozici v <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="52898-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="52898-208">V ukázkové aplikaci `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) vytvoří deklarace národního prostředí (`urn:google:locale`) a obrázku (`urn:google:picture`) pro přihlášený `ApplicationUser`, včetně deklarace pro <xref:System.Security.Claims.ClaimTypes.GivenName>:</span><span class="sxs-lookup"><span data-stu-id="52898-208">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="52898-209">Ve výchozím nastavení jsou deklarace identity uživatele uloženy v ověřovacím souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="52898-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="52898-210">Pokud je soubor cookie ověřování příliš velký, může dojít k selhání aplikace z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="52898-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="52898-211">Prohlížeč zjistí, že hlavička souboru cookie je příliš dlouhá.</span><span class="sxs-lookup"><span data-stu-id="52898-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="52898-212">Celková velikost požadavku je příliš velká.</span><span class="sxs-lookup"><span data-stu-id="52898-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="52898-213">Pokud se pro zpracování uživatelských požadavků vyžaduje velké množství uživatelských dat:</span><span class="sxs-lookup"><span data-stu-id="52898-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="52898-214">Omezte počet a velikost deklarací identity uživatelů pro zpracování žádostí jenom na to, co aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="52898-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="52898-215">Pro uložení identity mezi požadavky použijte vlastní <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pro @no__t middlewaru ověřování souborů cookie-1.</span><span class="sxs-lookup"><span data-stu-id="52898-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="52898-216">Zachovat velké množství informací o identitě na serveru, zatímco do klienta odesílá jenom malý klíč identifikátoru relace.</span><span class="sxs-lookup"><span data-stu-id="52898-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="52898-217">Uložení přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="52898-217">Save the access token</span></span>

<span data-ttu-id="52898-218">@no__t – 0 definuje, jestli se po úspěšné autorizaci mají v <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ukládat tokeny přístupu a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="52898-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="52898-219">`SaveTokens` je ve výchozím nastavení nastavená na `false`, aby se snížila velikost konečného ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="52898-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="52898-220">Ukázková aplikace nastaví hodnotu `SaveTokens` na `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="52898-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="52898-221">Když se spustí `OnPostConfirmationAsync`, uložte přístupový token ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) od externího poskytovatele v `AuthenticationProperties` `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="52898-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="52898-222">Ukázková aplikace uloží přístupový token do `OnPostConfirmationAsync` (registrace nového uživatele) a `OnGetCallbackAsync` (dřív registrovaný uživatel) v *účtu/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="52898-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="52898-223">Postup přidání dalších vlastních tokenů</span><span class="sxs-lookup"><span data-stu-id="52898-223">How to add additional custom tokens</span></span>

<span data-ttu-id="52898-224">Chcete-li předvést, jak přidat vlastní token, který je uložen jako součást `SaveTokens`, ukázková aplikace přidá <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> s aktuálním <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="52898-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="52898-225">Vytváření a přidávání deklarací identity</span><span class="sxs-lookup"><span data-stu-id="52898-225">Creating and adding claims</span></span>

<span data-ttu-id="52898-226">Rozhraní poskytuje běžné akce a metody rozšíření pro vytváření a přidávání deklarací identity do kolekce.</span><span class="sxs-lookup"><span data-stu-id="52898-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="52898-227">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="52898-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="52898-228">Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementací abstraktní metody <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>.</span><span class="sxs-lookup"><span data-stu-id="52898-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="52898-229">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="52898-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="52898-230">Odebrání akcí a deklarací identity</span><span class="sxs-lookup"><span data-stu-id="52898-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="52898-231">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere z kolekce všechny akce deklarací identity pro daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>.</span><span class="sxs-lookup"><span data-stu-id="52898-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="52898-232">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklaraci identity daného <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity.</span><span class="sxs-lookup"><span data-stu-id="52898-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="52898-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> se primárně používá s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) k odebrání deklarací generovaných protokolem.</span><span class="sxs-lookup"><span data-stu-id="52898-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="52898-234">Výstup ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="52898-234">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="52898-235">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="52898-235">Additional resources</span></span>

* <span data-ttu-id="52898-236">[ASPNET/AspNetCore Engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; propojená ukázková aplikace je na technické větvi `master` [úložiště GitHub/AspNetCore](https://github.com/aspnet/AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="52898-236">[aspnet/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [aspnet/AspNetCore GitHub repo's](https://github.com/aspnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="52898-237">Větev `master` obsahuje kód v části aktivní vývoj pro další verzi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="52898-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="52898-238">Pokud chcete zobrazit verzi ukázkové aplikace pro vydanou verzi ASP.NET Core, použijte rozevírací seznam **větev** a vyberte větev vydané verze (například `release/{X.Y}`).</span><span class="sxs-lookup"><span data-stu-id="52898-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
