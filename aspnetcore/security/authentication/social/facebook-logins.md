---
title: Externí přihlášení nastavení sítě Facebook v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování pomocí účtu uživatele Facebooku do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/facebook-logins
ms.openlocfilehash: e8ae16538b5d6844af7d983071fad629ebbe6217
ms.sourcegitcommit: 09bcda59a58019fdf47b2db5259fe87acf19dd38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708501"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="8f9bb-103">Externí přihlášení nastavení sítě Facebook v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f9bb-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="8f9bb-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8f9bb-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8f9bb-105">V tomto kurzu se dozvíte, jak povolit vašim uživatelům přihlašovat se pomocí svého účtu sítě Facebook použitím ukázkového projektu ASP.NET Core 2.0 vytvořit na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8f9bb-105">This tutorial shows you how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="8f9bb-106">Vyžaduje ověřování sítě Facebook [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-106">Facebook authentication requires the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package.</span></span> <span data-ttu-id="8f9bb-107">Začneme tím, že vytvoříte podle ID aplikace pro Facebook [oficiální kroky](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="8f9bb-107">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="8f9bb-108">Vytvoření aplikace na Facebooku</span><span class="sxs-lookup"><span data-stu-id="8f9bb-108">Create the app in Facebook</span></span>

* <span data-ttu-id="8f9bb-109">Přejděte [Facebook vývojáři aplikace](https://developers.facebook.com/apps/) stránky a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="8f9bb-110">Pokud ještě nemáte účet služby Facebook, použijte **zaregistrovat Facebooku** odkaz na přihlašovací stránku k jejímu vytvoření.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>

* <span data-ttu-id="8f9bb-111">Klepněte **přidejte novou aplikaci** tlačítko v pravém horním rohu k vytvoření nového ID aplikace.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-111">Tap the **Add a New App** button in the upper right corner to create a new App ID.</span></span>

   ![Facebook pro portál pro vývojáře otevřít v Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="8f9bb-113">Vyplňte formulář a klepněte **vytvoření ID aplikace** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-113">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Vytvoření nové aplikace ID formuláře](index/_static/FBNewAppId.png)

* <span data-ttu-id="8f9bb-115">Na **vyberte produkt** klikněte na **Set Up** na **přihlášení k Facebooku** karty.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-115">On the **Select a product** page, click **Set Up** on the **Facebook Login** card.</span></span>

  ![Stránka nastavení produktu](index/_static/FBProductSetup.png)

* <span data-ttu-id="8f9bb-117">**Rychlý Start** průvodce se spustí s **zvolte platformu** jako první stránka.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-117">The **Quickstart** wizard will launch with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="8f9bb-118">Vynechat Průvodce teď kliknutím **nastavení** odkaz v nabídce na levé straně:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-118">Bypass the wizard for now by clicking the **Settings** link in the menu on the left:</span></span>

  ![Přeskočit rychlý Start](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="8f9bb-120">Zobrazí se **nastavení klienta OAuth** stránky:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-120">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Stránka nastavení OAuth klienta](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="8f9bb-122">Zadejte identifikátor URI vývoje s */signin-facebook* připojí do **identifikátory URI pro přesměrování platný OAuth** pole (například: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="8f9bb-122">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="8f9bb-123">Ověřování přes síť Facebook později v tomto kurzu konfiguruje automaticky zpracovává požadavky na */signin-facebook* trasy, která má implementovat tok OAuth.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-123">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="8f9bb-124">Identifikátor URI */signin-facebook* je nastaven jako výchozí zpětného volání zprostředkovatele ověřování sítě Facebook.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-124">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="8f9bb-125">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middlewaru Facebook ověřování prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) Třída.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-125">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="8f9bb-126">Klikněte na tlačítko **uložit změny**.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-126">Click **Save Changes**.</span></span>

* <span data-ttu-id="8f9bb-127">Klikněte na tlačítko **nastavení** > **základní** odkaz v levém navigačním panelu.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-127">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="8f9bb-128">Na této stránce si poznamenejte vaše `App ID` a `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-128">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="8f9bb-129">Přidá do vaší aplikace ASP.NET Core v následující části:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-129">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="8f9bb-130">Při nasazování webu potřebujete revidovat **přihlášení k Facebooku** stránce instalace a registrace nový veřejný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-130">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="8f9bb-131">ID aplikace pro Store Facebook a tajný klíč aplikace</span><span class="sxs-lookup"><span data-stu-id="8f9bb-131">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="8f9bb-132">Propojit citlivá nastavení, jako je Facebook `App ID` a `App Secret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8f9bb-132">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8f9bb-133">Pro účely tohoto kurzu se název tokeny `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-133">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

<span data-ttu-id="8f9bb-134">Spusťte následující příkazy zabezpečeně ukládat `App ID` a `App Secret` pomocí manažera tajných:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-134">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="8f9bb-135">Konfigurace ověřování sítě Facebook</span><span class="sxs-lookup"><span data-stu-id="8f9bb-135">Configure Facebook Authentication</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8f9bb-136">Přidání služby Facebook v `ConfigureServices` metodu *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-136">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="8f9bb-137">Nainstalujte [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) balíčku.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-137">Install the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) package.</span></span>

* <span data-ttu-id="8f9bb-138">K instalaci tohoto balíčku pomocí sady Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-138">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="8f9bb-139">Instalace s .NET Core CLI, spusťte v adresáři projektu následující:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-139">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

<span data-ttu-id="8f9bb-140">Přidat v middlewaru Facebook `Configure` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-140">Add the Facebook middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

<span data-ttu-id="8f9bb-141">Zobrazit [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování sítě Facebook.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-141">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="8f9bb-142">Možnosti konfigurace umožňuje:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-142">Configuration options can be used to:</span></span>

* <span data-ttu-id="8f9bb-143">Požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-143">Request different information about the user.</span></span>
* <span data-ttu-id="8f9bb-144">Přidáte argumenty řetězce dotazu přizpůsobit přihlašovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-144">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="8f9bb-145">Přihlášení pomocí Facebooku</span><span class="sxs-lookup"><span data-stu-id="8f9bb-145">Sign in with Facebook</span></span>

<span data-ttu-id="8f9bb-146">Spusťte aplikaci a klikněte na tlačítko **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-146">Run your application and click **Log in**.</span></span> <span data-ttu-id="8f9bb-147">Zobrazí se možnost přihlásit se přes Facebook.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-147">You see an option to sign in with Facebook.</span></span>

![Webová aplikace: uživatel nebyl ověřen](index/_static/DoneFacebook.png)

<span data-ttu-id="8f9bb-149">Po kliknutí na **Facebook**, budete přesměrováni na Facebook pro ověření:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-149">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Stránka pro ověřování sítě Facebook](index/_static/FBLogin.png)

<span data-ttu-id="8f9bb-151">Požadavky na ověření sítě Facebook veřejný profil a e-mailovou adresu ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-151">Facebook authentication requests public profile and email address by default:</span></span>

![Stránka pro ověřování sítě Facebook](index/_static/FBLoginDone.png)

<span data-ttu-id="8f9bb-153">Po zadání vašich přihlašovacích údajů k Facebooku budete přesměrováni zpět na váš web, kde můžete nastavit e-mailu.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-153">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="8f9bb-154">Nyní jste přihlášeni pomocí vašich přihlašovacích údajů k Facebooku:</span><span class="sxs-lookup"><span data-stu-id="8f9bb-154">You are now logged in using your Facebook credentials:</span></span>

![Webová aplikace: uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="8f9bb-156">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="8f9bb-156">Troubleshooting</span></span>

* <span data-ttu-id="8f9bb-157">**ASP.NET Core 2.x pouze:** pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: musí být Zadaná možnost "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-157">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="8f9bb-158">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-158">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="8f9bb-159">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, můžete získat *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-159">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="8f9bb-160">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-160">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8f9bb-161">Další kroky</span><span class="sxs-lookup"><span data-stu-id="8f9bb-161">Next steps</span></span>

* <span data-ttu-id="8f9bb-162">V tomto článku jsme si ukázali, jak ověřování pomocí Facebooku.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-162">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="8f9bb-163">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8f9bb-163">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="8f9bb-164">Po publikování webu do webové aplikace Azure, měli byste resetovat `AppSecret` na portálu pro vývojáře služby Facebook.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-164">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="8f9bb-165">Nastavte `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-165">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="8f9bb-166">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="8f9bb-166">The configuration system is set up to read keys from environment variables.</span></span>
