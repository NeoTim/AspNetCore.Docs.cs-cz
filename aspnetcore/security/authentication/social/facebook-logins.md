---
title: Nastavení externího přihlášení ke službě Facebook v ASP.NET Core
author: rick-anderson
description: Kurz s příklady kódu, které demonstrují integraci ověřování uživatelů z účtu Facebook do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/facebook-logins
ms.openlocfilehash: 11ddc7314a694446d488da6ef1b2e3423bf7241a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777030"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="c8f8f-103">Nastavení externího přihlášení ke službě Facebook v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8f8f-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="c8f8f-104">Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c8f8f-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="c8f8f-105">Tento kurz s příklady kódu ukazuje, jak se uživatelům umožní přihlásit se pomocí účtu na Facebooku pomocí ukázkového projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c8f8f-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="c8f8f-106">Začneme vytvořením ID aplikace Facebook pomocí následujících [oficiálních kroků](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="c8f8f-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="c8f8f-107">Vytvoření aplikace na Facebooku</span><span class="sxs-lookup"><span data-stu-id="c8f8f-107">Create the app in Facebook</span></span>

* <span data-ttu-id="c8f8f-108">Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) .</span><span class="sxs-lookup"><span data-stu-id="c8f8f-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="c8f8f-109">Přejděte na stránku [aplikace pro vývojáře na Facebooku](https://developers.facebook.com/apps/) a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="c8f8f-110">Pokud ještě nemáte účet Facebook, vytvořte ho pomocí odkazu **zaregistrovat pro Facebook** na přihlašovací stránce.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="c8f8f-111">Jakmile budete mít účet Facebook, zaregistrujte se jako vývojář Facebooku podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="c8f8f-112">V nabídce **Moje aplikace** vyberte **vytvořit aplikaci** a vytvořte nové ID aplikace.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Portál Facebooku pro vývojáře otevřený v Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="c8f8f-114">Vyplňte formulář a klepněte na tlačítko **vytvořit ID aplikace** .</span><span class="sxs-lookup"><span data-stu-id="c8f8f-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Vytvoření nového formuláře ID aplikace](index/_static/FBNewAppId.png)

* <span data-ttu-id="c8f8f-116">Na nové kartě aplikace vyberte **Přidat produkt**.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="c8f8f-117">Na **přihlašovací kartě Facebooku** klikněte na **nastavit** .</span><span class="sxs-lookup"><span data-stu-id="c8f8f-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Stránka instalace produktu](index/_static/FBProductSetup.png)

* <span data-ttu-id="c8f8f-119">Průvodce **rychlým startem** se spustí s **volbou platformy** jako první stránky.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="c8f8f-120">Vynechejte Průvodce hned kliknutím na odkaz nastavení **přihlášení na Facebooku** **Settings** v nabídce na spodní levé straně:</span><span class="sxs-lookup"><span data-stu-id="c8f8f-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Přeskočit rychlé zprovoznění](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="c8f8f-122">Zobrazí se stránka **Nastavení OAuth pro klienta** :</span><span class="sxs-lookup"><span data-stu-id="c8f8f-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Stránka nastavení OAuth pro klienta](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="c8f8f-124">Zadejte identifikátor URI pro vývoj s */SignIn-Facebook* připojením k platným poli identifikátorů **URI přesměrování OAuth** (například `https://localhost:44320/signin-facebook`:).</span><span class="sxs-lookup"><span data-stu-id="c8f8f-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="c8f8f-125">Ověřování na Facebooku nakonfigurované později v tomto kurzu automaticky zpracuje žádosti na trase */SignIn-Facebook* k implementaci toku OAuth.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="c8f8f-126">Identifikátor URI */SignIn-Facebook* je nastaven jako výchozí zpětné volání poskytovatele ověřování Facebooku.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="c8f8f-127">Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování na Facebooku prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="c8f8f-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="c8f8f-128">Klikněte na **Save Changes** (Uložit změny).</span><span class="sxs-lookup"><span data-stu-id="c8f8f-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="c8f8f-129">V levém navigačním panelu klikněte na **Nastavení** > **základní** odkaz.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="c8f8f-130">Na této stránce si poznamenejte vaše `App ID` a. `App Secret`</span><span class="sxs-lookup"><span data-stu-id="c8f8f-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="c8f8f-131">Do své aplikace ASP.NET Core přidáte v následující části:</span><span class="sxs-lookup"><span data-stu-id="c8f8f-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="c8f8f-132">Když nasadíte lokalitu, musíte znovu navštívit stránku nastavení **přihlášení na Facebooku** a zaregistrovat nový veřejný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="c8f8f-133">Uložení ID a tajného klíče aplikace na Facebooku</span><span class="sxs-lookup"><span data-stu-id="c8f8f-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="c8f8f-134">Uložte citlivá nastavení, jako je například ID aplikace Facebook a tajné hodnoty pomocí [správce tajných](xref:security/app-secrets)kódů.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c8f8f-135">V této ukázce použijte následující postup:</span><span class="sxs-lookup"><span data-stu-id="c8f8f-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="c8f8f-136">Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="c8f8f-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="c8f8f-137">Uložte citlivá nastavení do místního úložiště tajných klíčů pomocí tajných `Authentication:Facebook:AppId` klíčů `Authentication:Facebook:AppSecret`a:</span><span class="sxs-lookup"><span data-stu-id="c8f8f-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="c8f8f-138">Konfigurace ověřování na Facebooku</span><span class="sxs-lookup"><span data-stu-id="c8f8f-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="c8f8f-139">Přidejte službu Facebook do `ConfigureServices` metody v souboru *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="c8f8f-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="c8f8f-140">Přihlásit se přes Facebook</span><span class="sxs-lookup"><span data-stu-id="c8f8f-140">Sign in with Facebook</span></span>

* <span data-ttu-id="c8f8f-141">Spusťte aplikaci a vyberte **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="c8f8f-142">V části **použít jinou službu pro přihlášení**vyberte Facebook.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="c8f8f-143">Budete přesměrováni na **Facebook** pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="c8f8f-144">Zadejte svoje přihlašovací údaje k Facebooku.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="c8f8f-145">Budete přesměrováni zpět na web, kde můžete nastavit e-mail.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="c8f8f-146">Nyní jste přihlášeni pomocí svých přihlašovacích údajů k Facebooku:</span><span class="sxs-lookup"><span data-stu-id="c8f8f-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="c8f8f-147">Reakce na zrušení autorizace externího přihlášení</span><span class="sxs-lookup"><span data-stu-id="c8f8f-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="c8f8f-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>může zadat cestu přesměrování pro uživatelského agenta, když uživatel neschválí požadovanou žádost o autorizaci.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="c8f8f-149">Následující kód nastaví `AccessDeniedPath` na `"/AccessDeniedPathInfo"`:</span><span class="sxs-lookup"><span data-stu-id="c8f8f-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="c8f8f-150">Doporučujeme, aby `AccessDeniedPath` stránka obsahovala tyto informace:</span><span class="sxs-lookup"><span data-stu-id="c8f8f-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="c8f8f-151">Vzdálené ověřování se zrušilo.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="c8f8f-152">Tato aplikace vyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-152">This app requires authentication.</span></span>
* <span data-ttu-id="c8f8f-153">Pokud se chcete zkusit znovu přihlásit, vyberte odkaz pro přihlášení.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="c8f8f-154">AccessDeniedPath testu</span><span class="sxs-lookup"><span data-stu-id="c8f8f-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="c8f8f-155">Přejít na [Facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="c8f8f-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="c8f8f-156">Pokud jste přihlášeni, musíte se odhlásit.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="c8f8f-157">Spusťte aplikaci a vyberte možnost přihlašování ke službě Facebook.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="c8f8f-158">Vyberte **ne nyní**.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-158">Select **Not now**.</span></span> <span data-ttu-id="c8f8f-159">Budete přesměrováni na určenou `AccessDeniedPath` stránku.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="c8f8f-160">Další informace o možnostech Konfigurace podporovaných ověřováním na Facebooku najdete v referenčních informacích k rozhraní [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="c8f8f-161">Možnosti konfigurace lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="c8f8f-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="c8f8f-162">Vyžádejte si jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-162">Request different information about the user.</span></span>
* <span data-ttu-id="c8f8f-163">Přidejte argumenty řetězce dotazu pro přizpůsobení možností přihlášení.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="c8f8f-164">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="c8f8f-164">Troubleshooting</span></span>

* <span data-ttu-id="c8f8f-165">**Pouze ASP.NET Core 2. x:** Pokud Identity není nakonfigurováno `services.AddIdentity` voláním `ConfigureServices`v, pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="c8f8f-166">Šablona projektu použitá v tomto kurzu zajišťuje, že je to hotové.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="c8f8f-167">Pokud se databáze lokality nevytvořila při použití prvotní migrace, při *zpracování chyby žádosti se zobrazí operace databáze* .</span><span class="sxs-lookup"><span data-stu-id="c8f8f-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="c8f8f-168">Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c8f8f-169">Další kroky</span><span class="sxs-lookup"><span data-stu-id="c8f8f-169">Next steps</span></span>

* <span data-ttu-id="c8f8f-170">Tento článek ukazuje, jak se dá ověřit pomocí Facebooku.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="c8f8f-171">Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c8f8f-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="c8f8f-172">Po publikování webu do webové aplikace Azure byste měli resetovat na `AppSecret` portálu pro vývojáře na Facebooku.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="c8f8f-173">Nastavte nastavení `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret` jako nastavení aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="c8f8f-174">Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="c8f8f-174">The configuration system is set up to read keys from environment variables.</span></span>
