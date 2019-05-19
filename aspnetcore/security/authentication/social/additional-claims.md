---
title: Zachování další deklarace identity a tokeny od externích poskytovatelů v ASP.NET Core
author: guardrex
description: Zjistěte, jak vytvořit další deklarace identity a tokeny od externích poskytovatelů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: e18287e5a4171b3f7a6daa122111448b8447c1da
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874848"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="ebfde-103">Zachování další deklarace identity a tokeny od externích poskytovatelů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebfde-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="ebfde-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ebfde-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ebfde-105">Aplikace ASP.NET Core můžete vytvořit další deklarace identity a tokenů z externího zprostředkovatele ověřování, jako je Facebook, Google, Microsoft a Twitter.</span><span class="sxs-lookup"><span data-stu-id="ebfde-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="ebfde-106">Každý poskytovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci dat uživatele do dalších deklarací identity je stejný.</span><span class="sxs-lookup"><span data-stu-id="ebfde-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="ebfde-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ebfde-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ebfde-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ebfde-108">Prerequisites</span></span>

<span data-ttu-id="ebfde-109">Rozhodněte, které externího zprostředkovatele ověřování pro podporu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ebfde-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="ebfde-110">Pro každého zprostředkovatele aplikace registrovaly a získaly ID klienta a tajný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="ebfde-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="ebfde-111">Další informace naleznete v tématu <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="ebfde-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="ebfde-112">Tato ukázková aplikace používá [zprostředkovatele ověřování Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="ebfde-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="ebfde-113">Nastavte ID klienta a tajný kód klienta</span><span class="sxs-lookup"><span data-stu-id="ebfde-113">Set the client ID and client secret</span></span>

<span data-ttu-id="ebfde-114">Zprostředkovatele ověřování OAuth vytváří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="ebfde-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="ebfde-115">ID klienta a hodnoty tajných kódů klienta jsou vytvořené pro aplikaci zprostředkovatele externího ověřování při registraci aplikace s tímto poskytovatelem.</span><span class="sxs-lookup"><span data-stu-id="ebfde-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="ebfde-116">Každý externího poskytovatele, který používá aplikace musí mít nakonfigurovanou nezávisle na sobě ID klienta a tajný kód klienta poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="ebfde-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="ebfde-117">Další informace najdete v tématech zprostředkovatele externího ověřování, které platí pro váš scénář:</span><span class="sxs-lookup"><span data-stu-id="ebfde-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="ebfde-118">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="ebfde-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="ebfde-119">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="ebfde-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="ebfde-120">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="ebfde-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="ebfde-121">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="ebfde-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="ebfde-122">Další zprostředkovatelé ověřování</span><span class="sxs-lookup"><span data-stu-id="ebfde-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="ebfde-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="ebfde-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="ebfde-124">Ukázková aplikace nakonfiguruje zprostředkovatele ověřování Google s ID klienta a tajný kód klienta, které jsou k dispozici společností Google:</span><span class="sxs-lookup"><span data-stu-id="ebfde-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="ebfde-125">Vytvořit rozsah ověřování</span><span class="sxs-lookup"><span data-stu-id="ebfde-125">Establish the authentication scope</span></span>

<span data-ttu-id="ebfde-126">Zadejte seznam oprávnění k načtení z poskytovatele tak, že zadáte <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="ebfde-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="ebfde-127">V následující tabulce se zobrazí obory ověřování pro běžné externího poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="ebfde-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="ebfde-128">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="ebfde-128">Provider</span></span>  | <span data-ttu-id="ebfde-129">Scope</span><span class="sxs-lookup"><span data-stu-id="ebfde-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="ebfde-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="ebfde-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="ebfde-131">Google</span><span class="sxs-lookup"><span data-stu-id="ebfde-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="ebfde-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="ebfde-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="ebfde-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="ebfde-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="ebfde-134">Ukázkové aplikace, Google `userinfo.profile` oboru se automaticky přidá rozhraním, když <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> je volán na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ebfde-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="ebfde-135">Pokud aplikace vyžaduje další obory, je přidáte do požadované možnosti.</span><span class="sxs-lookup"><span data-stu-id="ebfde-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="ebfde-136">V následujícím příkladu se ke službě Google `https://www.googleapis.com/auth/user.birthday.read` oboru se přidá získat datum narození uživatele:</span><span class="sxs-lookup"><span data-stu-id="ebfde-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="ebfde-137">Mapování uživatelských dat klíčů a vytvářet deklarace identity</span><span class="sxs-lookup"><span data-stu-id="ebfde-137">Map user data keys and create claims</span></span>

<span data-ttu-id="ebfde-138">V možnostech zprostředkovatele, zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč/podklíč externího poskytovatele JSON uživatelské údaje pro identitu aplikace ke čtení na přihlášení.</span><span class="sxs-lookup"><span data-stu-id="ebfde-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="ebfde-139">Další informace o typy deklarací identity najdete v tématu <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="ebfde-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="ebfde-140">Ukázková aplikace vytvoří národní prostředí (`urn:google:locale`) a obrázku (`urn:google:picture`) deklarace identity od `locale` a `picture` klíče v Google uživatelská data:</span><span class="sxs-lookup"><span data-stu-id="ebfde-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="ebfde-141">V <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) je přihlášený k aplikaci s <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ebfde-141">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="ebfde-142">Během procesu, přihlašování <xref:Microsoft.AspNetCore.Identity.UserManager%601> můžete ukládat `ApplicationUser` deklarací identity pro uživatele nejsou k dispozici data <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="ebfde-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="ebfde-143">V ukázkové aplikaci `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) vytvoří národní prostředí (`urn:google:locale`) a obrázku (`urn:google:picture`) deklarací identity pro podepsané v `ApplicationUser`, včetně deklarací identity pro <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="ebfde-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="ebfde-144">Ve výchozím nastavení jsou deklarace identity uživatele uložené v souboru cookie pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="ebfde-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="ebfde-145">Pokud soubor cookie ověřování je moc velká, může to způsobit aplikace se nezdařila, protože:</span><span class="sxs-lookup"><span data-stu-id="ebfde-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="ebfde-146">Prohlížeč zjistí, že hlavička cookie je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="ebfde-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="ebfde-147">Celková velikost požadavku je moc velká.</span><span class="sxs-lookup"><span data-stu-id="ebfde-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="ebfde-148">Pokud velký objem dat uživatele se vyžaduje pro zpracování požadavků uživatele:</span><span class="sxs-lookup"><span data-stu-id="ebfde-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="ebfde-149">Omezte počet a velikost deklarace identity uživatelů pro zpracování pouze co aplikace vyžaduje požadavku.</span><span class="sxs-lookup"><span data-stu-id="ebfde-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="ebfde-150">Použití vlastní <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pro Middleware ověřování souborů Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> k ukládání identit napříč požadavky.</span><span class="sxs-lookup"><span data-stu-id="ebfde-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="ebfde-151">Zachovat velké množství informací identity na serveru při odesílání pouze identifikátor klíče malé relace do klienta.</span><span class="sxs-lookup"><span data-stu-id="ebfde-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="ebfde-152">Uložit přístupový token</span><span class="sxs-lookup"><span data-stu-id="ebfde-152">Save the access token</span></span>

<span data-ttu-id="ebfde-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definuje, jestli tokeny přístupu a aktualizace by měla být uložena v <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="ebfde-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="ebfde-154">`SaveTokens` je nastavena na `false` ve výchozím nastavení ke zmenšení velikosti konečné ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="ebfde-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="ebfde-155">Ukázková aplikace nastaví hodnotu `SaveTokens` k `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="ebfde-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="ebfde-156">Když `OnPostConfirmationAsync` spustí, ukládat přístupový token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) z externího poskytovatele v `ApplicationUser`společnosti `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="ebfde-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="ebfde-157">Ukázková aplikace uloží přístupového tokenu v `OnPostConfirmationAsync` (nové registrace uživatele) a `OnGetCallbackAsync` (dříve registrovaného uživatele) v *Account/ExternalLogin.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ebfde-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="ebfde-158">Jak přidat další vlastní tokeny</span><span class="sxs-lookup"><span data-stu-id="ebfde-158">How to add additional custom tokens</span></span>

<span data-ttu-id="ebfde-159">K předvedení jak přidat vlastní token, který je uložený jako součást `SaveTokens`, přidá ukázkovou aplikaci <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> s aktuálním <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) z `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="ebfde-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-28)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="ebfde-160">Vytvoření a přidání deklarace identity</span><span class="sxs-lookup"><span data-stu-id="ebfde-160">Creating and adding claims</span></span>

<span data-ttu-id="ebfde-161">Rozhraní poskytuje běžné akce a rozšiřující metody pro vytvoření a přidání deklarace identity do kolekce.</span><span class="sxs-lookup"><span data-stu-id="ebfde-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="ebfde-162">Další informace najdete v tématu <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="ebfde-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="ebfde-163">Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementaci abstraktní <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metody.</span><span class="sxs-lookup"><span data-stu-id="ebfde-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="ebfde-164">Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="ebfde-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="ebfde-165">Odebrání akce deklarace identity a deklarací identity</span><span class="sxs-lookup"><span data-stu-id="ebfde-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="ebfde-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere všechny deklarace identity akce daném <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekce.</span><span class="sxs-lookup"><span data-stu-id="ebfde-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="ebfde-167">[ClaimActionCollectionMapExtensions.DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklarace identity z daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity.</span><span class="sxs-lookup"><span data-stu-id="ebfde-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="ebfde-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> se používá především s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) odebrat vygeneruje protokol deklarací identity.</span><span class="sxs-lookup"><span data-stu-id="ebfde-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="ebfde-169">Ukázkový výstup aplikace</span><span class="sxs-lookup"><span data-stu-id="ebfde-169">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ebfde-170">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ebfde-170">Additional resources</span></span>

* <span data-ttu-id="ebfde-171">[ASPNET/AspNetCore engineering SocialSample aplikace](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; propojené ukázkové aplikace je na [úložiště GitHub aspnet/AspNetCore](https://github.com/aspnet/AspNetCore) `master` engineering větve.</span><span class="sxs-lookup"><span data-stu-id="ebfde-171">[aspnet/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [aspnet/AspNetCore GitHub repo's](https://github.com/aspnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="ebfde-172">`master` Větev obsahuje kód aktivně vyvíjí pro další verzi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebfde-172">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="ebfde-173">Pokud chcete zobrazit verzi ukázkovou aplikaci pro vydanou verzi ASP.NET Core, použijte **větev** rozevírací seznam a vyberte větev vydané verze (například `release/2.2`).</span><span class="sxs-lookup"><span data-stu-id="ebfde-173">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/2.2`).</span></span>
