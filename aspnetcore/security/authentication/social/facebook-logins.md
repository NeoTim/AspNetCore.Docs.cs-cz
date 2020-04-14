---
title: Nastavení externího přihlášení na Facebooku v ASP.NET Core
author: rick-anderson
description: Kurz s příklady kódu, které demonstrují integraci ověřování uživatelů účtu Facebook do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277298"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="bcb90-103">Nastavení externího přihlášení na Facebooku v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bcb90-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="bcb90-104">[Valerij Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bcb90-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="bcb90-105">Tento kurz s příklady kódu ukazuje, jak povolit uživatelům přihlásit se pomocí svého účtu Facebook pomocí ukázkové ASP.NET projektu Core 3.0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="bcb90-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="bcb90-106">Začneme vytvořením ID aplikace Facebook podle [oficiálních kroků](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="bcb90-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="bcb90-107">Vytvoření aplikace na Facebooku</span><span class="sxs-lookup"><span data-stu-id="bcb90-107">Create the app in Facebook</span></span>

* <span data-ttu-id="bcb90-108">Přidejte do projektu balíček [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet.</span><span class="sxs-lookup"><span data-stu-id="bcb90-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="bcb90-109">Přejděte na stránku [aplikace Facebook Developers](https://developers.facebook.com/apps/) a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="bcb90-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="bcb90-110">Pokud ještě nemáte facebookový účet, vytvořte ho pomocí odkazu **Zaregistrovat se na Facebooku** na přihlašovací stránce.</span><span class="sxs-lookup"><span data-stu-id="bcb90-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="bcb90-111">Jakmile budete mít účet na Facebooku, postupujte podle pokynů a zaregistrujte se jako vývojář facebooku.</span><span class="sxs-lookup"><span data-stu-id="bcb90-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="bcb90-112">V nabídce **Moje aplikace** vyberte **Vytvořit aplikaci** a vytvořte nové ID aplikace.</span><span class="sxs-lookup"><span data-stu-id="bcb90-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook pro vývojáře portál otevřený v Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="bcb90-114">Vyplňte formulář a klepněte na tlačítko **Vytvořit ID aplikace.**</span><span class="sxs-lookup"><span data-stu-id="bcb90-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Vytvoření nového formuláře ID aplikace](index/_static/FBNewAppId.png)

* <span data-ttu-id="bcb90-116">Na nové kartě aplikace vyberte **Přidat produkt**.</span><span class="sxs-lookup"><span data-stu-id="bcb90-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="bcb90-117">Na **přihlašovací** kartě Facebook klikněte na **Nastavit.**</span><span class="sxs-lookup"><span data-stu-id="bcb90-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Stránka Nastavení produktu](index/_static/FBProductSetup.png)

* <span data-ttu-id="bcb90-119">Průvodce **rychlým startem** se spustí s **výběrem platformy** jako první stránky.</span><span class="sxs-lookup"><span data-stu-id="bcb90-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="bcb90-120">Obejít průvodce pro tuto chvíli kliknutím na **facebook Přihlašovací** **nastavení** odkaz v nabídce v levém dolním rohu:</span><span class="sxs-lookup"><span data-stu-id="bcb90-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Přeskočit rychlý start](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="bcb90-122">Zobrazí se stránka **Nastavení oauth klienta:**</span><span class="sxs-lookup"><span data-stu-id="bcb90-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Stránka Nastavení OAuth klienta](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="bcb90-124">Zadejte identifikátor URI vývoje s *parametrem /signin-facebook* připojeným do pole Platné `https://localhost:44320/signin-facebook` **identifikátory URI přesměrování OAuth** (například: ).</span><span class="sxs-lookup"><span data-stu-id="bcb90-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="bcb90-125">Ověřování na Facebooku nakonfigurované dále v tomto kurzu bude automaticky zpracovávat požadavky na trase */signin-facebook* k implementaci toku OAuth.</span><span class="sxs-lookup"><span data-stu-id="bcb90-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="bcb90-126">Identifikátor URI */signin-facebook* je nastaven jako výchozí zpětné volání zprostředkovatele ověřování na Facebooku.</span><span class="sxs-lookup"><span data-stu-id="bcb90-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="bcb90-127">Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middlewaru ověřování na Facebooku prostřednictvím zděděné [vlastnosti RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)</span><span class="sxs-lookup"><span data-stu-id="bcb90-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="bcb90-128">Klikněte na **Save Changes** (Uložit změny).</span><span class="sxs-lookup"><span data-stu-id="bcb90-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="bcb90-129">V levé navigaci klikněte na odkaz **Nastavení** > **základní.**</span><span class="sxs-lookup"><span data-stu-id="bcb90-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="bcb90-130">Na této stránce si poznamenejte svůj `App ID` a svůj `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="bcb90-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="bcb90-131">Obě aplikace ASP.NET core přidáte do aplikace v další části:</span><span class="sxs-lookup"><span data-stu-id="bcb90-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="bcb90-132">Při nasazování webu musíte znovu navštívit stránku **nastavení přihlášení na Facebooku** a zaregistrovat nový veřejný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="bcb90-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="bcb90-133">Uložení ID aplikace Facebook a tajného klíče</span><span class="sxs-lookup"><span data-stu-id="bcb90-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="bcb90-134">Ukládat citlivá nastavení, jako je ID aplikace Facebook a tajné hodnoty pomocí [Správce tajných barev](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="bcb90-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="bcb90-135">Pro tuto ukázku použijte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="bcb90-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="bcb90-136">Inicializovat projekt pro tajné úložiště podle pokynů na [Povolit tajné úložiště](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="bcb90-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="bcb90-137">Uložte citlivá nastavení v místním tajném úložišti s tajnými klíči `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret`:</span><span class="sxs-lookup"><span data-stu-id="bcb90-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="bcb90-138">Konfigurace ověřování na Facebooku</span><span class="sxs-lookup"><span data-stu-id="bcb90-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="bcb90-139">Přidejte službu `ConfigureServices` Facebook do metody v *souboru Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="bcb90-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="bcb90-140">Přihlášení přes Facebook</span><span class="sxs-lookup"><span data-stu-id="bcb90-140">Sign in with Facebook</span></span>

* <span data-ttu-id="bcb90-141">Spusťte aplikaci a **vyberte Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="bcb90-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="bcb90-142">V části **Použít jinou službu pro přihlášení vyberte**Facebook.</span><span class="sxs-lookup"><span data-stu-id="bcb90-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="bcb90-143">Budete přesměrováni na **Facebook** k ověření.</span><span class="sxs-lookup"><span data-stu-id="bcb90-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="bcb90-144">Zadejte své přihlašovací údaje na Facebooku.</span><span class="sxs-lookup"><span data-stu-id="bcb90-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="bcb90-145">Budete přesměrováni zpět na svůj web, kde můžete nastavit svůj e-mail.</span><span class="sxs-lookup"><span data-stu-id="bcb90-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="bcb90-146">Nyní jste přihlášeni pomocí svých přihlašovacích údajů na Facebooku:</span><span class="sxs-lookup"><span data-stu-id="bcb90-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="bcb90-147">Reagovat na zrušení autorizace externího přihlášení</span><span class="sxs-lookup"><span data-stu-id="bcb90-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="bcb90-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>může poskytnout cestu přesměrování k uživatelskému agentovi, pokud uživatel neschválí požadovanou žádost o autorizaci.</span><span class="sxs-lookup"><span data-stu-id="bcb90-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="bcb90-149">Následující kód `AccessDeniedPath` nastaví `"/AccessDeniedPathInfo"`na :</span><span class="sxs-lookup"><span data-stu-id="bcb90-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="bcb90-150">Doporučujeme, `AccessDeniedPath` aby stránka obsahovala následující informace:</span><span class="sxs-lookup"><span data-stu-id="bcb90-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="bcb90-151">Vzdálené ověřování bylo zrušeno.</span><span class="sxs-lookup"><span data-stu-id="bcb90-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="bcb90-152">Tato aplikace vyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="bcb90-152">This app requires authentication.</span></span>
* <span data-ttu-id="bcb90-153">Chcete-li se přihlásit znovu, vyberte odkaz Přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="bcb90-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="bcb90-154">Testovat accessdeniedpath</span><span class="sxs-lookup"><span data-stu-id="bcb90-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="bcb90-155">Přejděte na [facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="bcb90-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="bcb90-156">Pokud jste přihlášeni, musíte se odhlásit.</span><span class="sxs-lookup"><span data-stu-id="bcb90-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="bcb90-157">Spusťte aplikaci a vyberte Přihlášení na Facebooku.</span><span class="sxs-lookup"><span data-stu-id="bcb90-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="bcb90-158">Vyberte **možnost Ne nyní**.</span><span class="sxs-lookup"><span data-stu-id="bcb90-158">Select **Not now**.</span></span> <span data-ttu-id="bcb90-159">Budete přesměrováni na `AccessDeniedPath` zadanou stránku.</span><span class="sxs-lookup"><span data-stu-id="bcb90-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="bcb90-160">Další informace o možnostech konfigurace podporovaných ověřováním na Facebooku najdete v referenční příručce rozhraní API [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions)</span><span class="sxs-lookup"><span data-stu-id="bcb90-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="bcb90-161">Možnosti konfigurace lze použít k:</span><span class="sxs-lookup"><span data-stu-id="bcb90-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="bcb90-162">Vyžádejte si různé informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="bcb90-162">Request different information about the user.</span></span>
* <span data-ttu-id="bcb90-163">Přidejte argumenty řetězce dotazu pro přizpůsobení přihlašovacího prostředí.</span><span class="sxs-lookup"><span data-stu-id="bcb90-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bcb90-164">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="bcb90-164">Troubleshooting</span></span>

* <span data-ttu-id="bcb90-165">**pouze ASP.NET Core 2.x:** Pokud identita není nakonfigurován `services.AddIdentity` `ConfigureServices`voláním , pokus o ověření bude mít za následek *ArgumentException: "SignInScheme" možnost musí být k dispozici*.</span><span class="sxs-lookup"><span data-stu-id="bcb90-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="bcb90-166">Šablona projektu použitá v tomto kurzu zajišťuje, že se tak děje.</span><span class="sxs-lookup"><span data-stu-id="bcb90-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="bcb90-167">Pokud databáze lokality nebyla vytvořena použitím počáteční migrace, zobrazí se *operace databáze, která se nezdařila při zpracování chyby požadavku.*</span><span class="sxs-lookup"><span data-stu-id="bcb90-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="bcb90-168">Klepnutím na **Použít migrace vytvořte** databázi a aktualizujte, abyste pokračovali v minulosti za chybou.</span><span class="sxs-lookup"><span data-stu-id="bcb90-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bcb90-169">Další kroky</span><span class="sxs-lookup"><span data-stu-id="bcb90-169">Next steps</span></span>

* <span data-ttu-id="bcb90-170">Tento článek ukázal, jak se můžete ověřit pomocí Facebooku.</span><span class="sxs-lookup"><span data-stu-id="bcb90-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="bcb90-171">Můžete postupovat podobným způsobem k ověření s jinými zprostředkovateli uvedenými na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="bcb90-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="bcb90-172">Po publikování webu do webové aplikace Azure byste `AppSecret` měli obnovit web na facebookovém vývojářském portálu.</span><span class="sxs-lookup"><span data-stu-id="bcb90-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="bcb90-173">Nastavte `Authentication:Facebook:AppId` nastavení `Authentication:Facebook:AppSecret` aplikace a jako na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="bcb90-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="bcb90-174">Konfigurační systém je nastaven pro čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="bcb90-174">The configuration system is set up to read keys from environment variables.</span></span>
