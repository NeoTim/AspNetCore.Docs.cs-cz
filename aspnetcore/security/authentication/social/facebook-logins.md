---
title: Externí přihlášení nastavení sítě Facebook v ASP.NET Core
author: rick-anderson
description: Kurz s příklady kódu pro demonstraci integrace ověřování pomocí účtu uživatele Facebooku do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/04/2019
uid: security/authentication/facebook-logins
ms.openlocfilehash: 84b891f6cee71a26726d49a9d42ae45007d2b429
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64899859"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="739f2-103">Externí přihlášení nastavení sítě Facebook v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="739f2-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="739f2-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="739f2-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="739f2-105">V tomto kurzu s příklady kódu ukazuje, jak povolit vašim uživatelům přihlašovat se pomocí svého účtu sítě Facebook použitím ukázkového projektu ASP.NET Core 2.0 vytvořit na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="739f2-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="739f2-106">Začneme tím, že vytvoříte podle ID aplikace pro Facebook [oficiální kroky](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="739f2-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="739f2-107">Vytvoření aplikace na Facebooku</span><span class="sxs-lookup"><span data-stu-id="739f2-107">Create the app in Facebook</span></span>

* <span data-ttu-id="739f2-108">Přejděte [Facebook vývojáři aplikace](https://developers.facebook.com/apps/) stránky a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="739f2-108">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="739f2-109">Pokud ještě nemáte účet služby Facebook, použijte **zaregistrovat Facebooku** odkaz na přihlašovací stránku k jejímu vytvoření.</span><span class="sxs-lookup"><span data-stu-id="739f2-109">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>

* <span data-ttu-id="739f2-110">Klepněte **přidejte novou aplikaci** tlačítko v pravém horním rohu k vytvoření nového ID aplikace.</span><span class="sxs-lookup"><span data-stu-id="739f2-110">Tap the **Add a New App** button in the upper right corner to create a new App ID.</span></span>

   ![Facebook pro portál pro vývojáře otevřít v Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="739f2-112">Vyplňte formulář a klepněte **vytvoření ID aplikace** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="739f2-112">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Vytvoření nové aplikace ID formuláře](index/_static/FBNewAppId.png)

* <span data-ttu-id="739f2-114">Na **vyberte produkt** klikněte na **Set Up** na **přihlášení k Facebooku** karty.</span><span class="sxs-lookup"><span data-stu-id="739f2-114">On the **Select a product** page, click **Set Up** on the **Facebook Login** card.</span></span>

  ![Stránka nastavení produktu](index/_static/FBProductSetup.png)

* <span data-ttu-id="739f2-116">**Rychlý Start** průvodce se spustí s **zvolte platformu** jako první stránka.</span><span class="sxs-lookup"><span data-stu-id="739f2-116">The **Quickstart** wizard will launch with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="739f2-117">Vynechat Průvodce teď kliknutím **nastavení** odkaz v nabídce na levé straně:</span><span class="sxs-lookup"><span data-stu-id="739f2-117">Bypass the wizard for now by clicking the **Settings** link in the menu on the left:</span></span>

  ![Přeskočit rychlý Start](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="739f2-119">Zobrazí se **nastavení klienta OAuth** stránky:</span><span class="sxs-lookup"><span data-stu-id="739f2-119">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Stránka nastavení OAuth klienta](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="739f2-121">Zadejte identifikátor URI vývoje s */signin-facebook* připojí do **identifikátory URI pro přesměrování platný OAuth** pole (například: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="739f2-121">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="739f2-122">Ověřování přes síť Facebook později v tomto kurzu konfiguruje automaticky zpracovává požadavky na */signin-facebook* trasy, která má implementovat tok OAuth.</span><span class="sxs-lookup"><span data-stu-id="739f2-122">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="739f2-123">Identifikátor URI */signin-facebook* je nastaven jako výchozí zpětného volání zprostředkovatele ověřování sítě Facebook.</span><span class="sxs-lookup"><span data-stu-id="739f2-123">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="739f2-124">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middlewaru Facebook ověřování prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) Třída.</span><span class="sxs-lookup"><span data-stu-id="739f2-124">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="739f2-125">Klikněte na tlačítko **uložit změny**.</span><span class="sxs-lookup"><span data-stu-id="739f2-125">Click **Save Changes**.</span></span>

* <span data-ttu-id="739f2-126">Klikněte na tlačítko **nastavení** > **základní** odkaz v levém navigačním panelu.</span><span class="sxs-lookup"><span data-stu-id="739f2-126">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="739f2-127">Na této stránce si poznamenejte vaše `App ID` a `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="739f2-127">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="739f2-128">Přidá do vaší aplikace ASP.NET Core v následující části:</span><span class="sxs-lookup"><span data-stu-id="739f2-128">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="739f2-129">Při nasazování webu potřebujete revidovat **přihlášení k Facebooku** stránce instalace a registrace nový veřejný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="739f2-129">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="739f2-130">ID aplikace pro Store Facebook a tajný klíč aplikace</span><span class="sxs-lookup"><span data-stu-id="739f2-130">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="739f2-131">Propojit citlivá nastavení, jako je Facebook `App ID` a `App Secret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="739f2-131">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="739f2-132">Pro účely tohoto kurzu se název tokeny `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="739f2-132">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="739f2-133">Spusťte následující příkazy zabezpečeně ukládat `App ID` a `App Secret` pomocí manažera tajných:</span><span class="sxs-lookup"><span data-stu-id="739f2-133">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="739f2-134">Konfigurace ověřování sítě Facebook</span><span class="sxs-lookup"><span data-stu-id="739f2-134">Configure Facebook Authentication</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="739f2-135">Přidání služby Facebook v `ConfigureServices` metodu *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="739f2-135">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

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

<span data-ttu-id="739f2-136">Nainstalujte [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) balíčku.</span><span class="sxs-lookup"><span data-stu-id="739f2-136">Install the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) package.</span></span>

* <span data-ttu-id="739f2-137">K instalaci tohoto balíčku pomocí sady Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="739f2-137">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="739f2-138">Instalace s .NET Core CLI, spusťte v adresáři projektu následující:</span><span class="sxs-lookup"><span data-stu-id="739f2-138">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

<span data-ttu-id="739f2-139">Přidat v middlewaru Facebook `Configure` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="739f2-139">Add the Facebook middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

<span data-ttu-id="739f2-140">Zobrazit [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování sítě Facebook.</span><span class="sxs-lookup"><span data-stu-id="739f2-140">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="739f2-141">Možnosti konfigurace umožňuje:</span><span class="sxs-lookup"><span data-stu-id="739f2-141">Configuration options can be used to:</span></span>

* <span data-ttu-id="739f2-142">Požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="739f2-142">Request different information about the user.</span></span>
* <span data-ttu-id="739f2-143">Přidáte argumenty řetězce dotazu přizpůsobit přihlašovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="739f2-143">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="739f2-144">Přihlášení pomocí Facebooku</span><span class="sxs-lookup"><span data-stu-id="739f2-144">Sign in with Facebook</span></span>

<span data-ttu-id="739f2-145">Spusťte aplikaci a klikněte na tlačítko **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="739f2-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="739f2-146">Zobrazí se možnost přihlásit se přes Facebook.</span><span class="sxs-lookup"><span data-stu-id="739f2-146">You see an option to sign in with Facebook.</span></span>

![Webové aplikace: Uživatel nebyl ověřen](index/_static/DoneFacebook.png)

<span data-ttu-id="739f2-148">Po kliknutí na **Facebook**, budete přesměrováni na Facebook pro ověření:</span><span class="sxs-lookup"><span data-stu-id="739f2-148">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Stránka pro ověřování sítě Facebook](index/_static/FBLogin.png)

<span data-ttu-id="739f2-150">Požadavky na ověření sítě Facebook veřejný profil a e-mailovou adresu ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="739f2-150">Facebook authentication requests public profile and email address by default:</span></span>

![Obrazovka pro vyjádření souhlasu stránky ověřování sítě Facebook](index/_static/FBLoginDone.png)

<span data-ttu-id="739f2-152">Po zadání vašich přihlašovacích údajů k Facebooku budete přesměrováni zpět na váš web, kde můžete nastavit e-mailu.</span><span class="sxs-lookup"><span data-stu-id="739f2-152">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="739f2-153">Nyní jste přihlášeni pomocí vašich přihlašovacích údajů k Facebooku:</span><span class="sxs-lookup"><span data-stu-id="739f2-153">You are now logged in using your Facebook credentials:</span></span>

![Webové aplikace: Uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="739f2-155">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="739f2-155">Troubleshooting</span></span>

* <span data-ttu-id="739f2-156">**ASP.NET Core 2.x pouze:** Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: Musí být Zadaná možnost "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="739f2-156">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="739f2-157">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="739f2-157">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="739f2-158">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, můžete získat *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="739f2-158">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="739f2-159">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="739f2-159">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="739f2-160">Další kroky</span><span class="sxs-lookup"><span data-stu-id="739f2-160">Next steps</span></span>

* <span data-ttu-id="739f2-161">Přidat [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) balíček NuGet do projektu pro pokročilé scénáře ověřování služby Facebook.</span><span class="sxs-lookup"><span data-stu-id="739f2-161">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to your project for advanced Facebook authentication scenarios.</span></span> <span data-ttu-id="739f2-162">Tento balíček nevyžaduje integrovat funkce externí přihlášení Facebooku s vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="739f2-162">This package isn't required to integrate Facebook external login functionality with your app.</span></span> 

* <span data-ttu-id="739f2-163">V tomto článku jsme si ukázali, jak ověřování pomocí Facebooku.</span><span class="sxs-lookup"><span data-stu-id="739f2-163">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="739f2-164">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="739f2-164">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="739f2-165">Po publikování webu do webové aplikace Azure, měli byste resetovat `AppSecret` na portálu pro vývojáře služby Facebook.</span><span class="sxs-lookup"><span data-stu-id="739f2-165">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="739f2-166">Nastavte `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="739f2-166">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="739f2-167">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="739f2-167">The configuration system is set up to read keys from environment variables.</span></span>
