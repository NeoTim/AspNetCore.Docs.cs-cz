---
title: Nastavení externí přihlášení pomocí ASP.NET Core na twitteru
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Twitter do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/twitter-logins
ms.openlocfilehash: 43a5ea59d8853d297ae2c1ec3f4b1c0c14ec80c3
ms.sourcegitcommit: 09bcda59a58019fdf47b2db5259fe87acf19dd38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708423"
---
# <a name="twitter-external-login-setup-with-aspnet-core"></a><span data-ttu-id="7d44e-103">Nastavení externí přihlášení pomocí ASP.NET Core na twitteru</span><span class="sxs-lookup"><span data-stu-id="7d44e-103">Twitter external login setup with ASP.NET Core</span></span>

<span data-ttu-id="7d44e-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7d44e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7d44e-105">V tomto kurzu se dozvíte, jak povolit uživatelům [Přihlaste se pomocí svého účtu Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) použitím ukázkového projektu ASP.NET Core 2.0 na vytvoří [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7d44e-105">This tutorial shows you how to enable your users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="7d44e-106">Vytvořit aplikaci na Twitteru</span><span class="sxs-lookup"><span data-stu-id="7d44e-106">Create the app in Twitter</span></span>

* <span data-ttu-id="7d44e-107">Přejděte do [ https://apps.twitter.com/ ](https://apps.twitter.com/) a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="7d44e-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="7d44e-108">Pokud ještě nemáte účet na Twitteru, použijte **[zaregistrujte se hned teď](https://twitter.com/signup)** odkaz pro vytvoření.</span><span class="sxs-lookup"><span data-stu-id="7d44e-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span> <span data-ttu-id="7d44e-109">Po přihlášení **správy aplikací** stránka je zobrazena:</span><span class="sxs-lookup"><span data-stu-id="7d44e-109">After signing in, the **Application Management** page is shown:</span></span>

  ![Správa aplikací Twitter otevřít v Microsoft Edge](index/_static/TwitterAppManage.png)

* <span data-ttu-id="7d44e-111">Klepněte na **vytvořit novou aplikaci** a vyplnit **název**, **popis** a veřejné **webu** identifikátoru URI (může to být dočasný dokud Tento název domény zaregistrujte):</span><span class="sxs-lookup"><span data-stu-id="7d44e-111">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

  ![Vytvoření stránky aplikace](index/_static/TwitterCreate.png)

* <span data-ttu-id="7d44e-113">Zadejte identifikátor URI vývoje s `/signin-twitter` připojí do **identifikátory URI pro přesměrování platný OAuth** pole (například: `https://localhost:44320/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="7d44e-113">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-twitter`).</span></span> <span data-ttu-id="7d44e-114">Schéma ověřování Twitteru později v tomto kurzu konfiguruje automaticky zpracovává požadavky na `/signin-twitter` trasy, která má implementovat tok OAuth.</span><span class="sxs-lookup"><span data-stu-id="7d44e-114">The Twitter authentication scheme configured later in this tutorial will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="7d44e-115">Segment identifikátoru URI `/signin-twitter` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Twitteru.</span><span class="sxs-lookup"><span data-stu-id="7d44e-115">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="7d44e-116">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování služby Twitter prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) Třída.</span><span class="sxs-lookup"><span data-stu-id="7d44e-116">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="7d44e-117">Vyplňte zbývající části formuláře a klepněte na **vytvoření aplikace Twitter**.</span><span class="sxs-lookup"><span data-stu-id="7d44e-117">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="7d44e-118">Podrobnosti o nové aplikace se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="7d44e-118">New application details are displayed:</span></span>

  ![Podrobnosti karty na stránce aplikace](index/_static/TwitterAppDetails.png)

* <span data-ttu-id="7d44e-120">Při nasazování webu budete potřebovat revidovat **správy aplikací** stránky a zaregistrujte nový veřejný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="7d44e-120">When deploying the site you'll need to revisit the **Application Management** page and register a new public URI.</span></span>

## <a name="storing-twitter-consumerkey-and-consumersecret"></a><span data-ttu-id="7d44e-121">Ukládání Twitter ConsumerKey a ConsumerSecret</span><span class="sxs-lookup"><span data-stu-id="7d44e-121">Storing Twitter ConsumerKey and ConsumerSecret</span></span>

<span data-ttu-id="7d44e-122">Propojit citlivá nastavení, jako je Twitter `Consumer Key` a `Consumer Secret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7d44e-122">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="7d44e-123">Pro účely tohoto kurzu se název tokeny `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret`.</span><span class="sxs-lookup"><span data-stu-id="7d44e-123">For the purposes of this tutorial, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="7d44e-124">Tyto tokeny můžete najít na **klíče a přístupové tokeny** kartu po vytvoření nové aplikace Twitter:</span><span class="sxs-lookup"><span data-stu-id="7d44e-124">These tokens can be found on the **Keys and Access Tokens** tab after creating your new Twitter application:</span></span>

![Karta klíče a přístupové tokeny](index/_static/TwitterKeys.png)

## <a name="configure-twitter-authentication"></a><span data-ttu-id="7d44e-126">Konfigurace ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="7d44e-126">Configure Twitter Authentication</span></span>

<span data-ttu-id="7d44e-127">Šablona projektu použité v tomto kurzu zajistí, že [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) balíček je už nainstalovaný.</span><span class="sxs-lookup"><span data-stu-id="7d44e-127">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) package is already installed.</span></span>

* <span data-ttu-id="7d44e-128">K instalaci tohoto balíčku pomocí sady Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="7d44e-128">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="7d44e-129">Instalace s .NET Core CLI, spusťte v adresáři projektu následující:</span><span class="sxs-lookup"><span data-stu-id="7d44e-129">To install with .NET Core CLI, execute the following in your project directory:</span></span>

  `dotnet add package Microsoft.AspNetCore.Authentication.Twitter`

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7d44e-130">Přidat službu Twitter `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="7d44e-130">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddTwitter(twitterOptions =>
{
    twitterOptions.ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"];
    twitterOptions.ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7d44e-131">Přidat v middlewaru Twitter `Configure` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="7d44e-131">Add the Twitter middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseTwitterAuthentication(new TwitterOptions()
{
    ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"],
    ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"]
});
```

::: moniker-end

<span data-ttu-id="7d44e-132">Zobrazit [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování Twitteru.</span><span class="sxs-lookup"><span data-stu-id="7d44e-132">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="7d44e-133">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="7d44e-133">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="7d44e-134">Přihlaste se pomocí Twitteru</span><span class="sxs-lookup"><span data-stu-id="7d44e-134">Sign in with Twitter</span></span>

<span data-ttu-id="7d44e-135">Spusťte aplikaci a klikněte na tlačítko **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="7d44e-135">Run your application and click **Log in**.</span></span> <span data-ttu-id="7d44e-136">Zobrazí se možnost přihlásit se přes Twitter:</span><span class="sxs-lookup"><span data-stu-id="7d44e-136">An option to sign in with Twitter appears:</span></span>

![Webová aplikace: uživatel nebyl ověřen](index/_static/DoneTwitter.png)

<span data-ttu-id="7d44e-138">Kliknutím na **Twitteru** přesměruje na Twitter pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="7d44e-138">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

![Stránka ověřování twitteru](index/_static/TwitterLogin.png)

<span data-ttu-id="7d44e-140">Po zadání vaše přihlašovací údaje k Twitteru, budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.</span><span class="sxs-lookup"><span data-stu-id="7d44e-140">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="7d44e-141">Nyní jste přihlášeni pomocí svých přihlašovacích údajů Twitter:</span><span class="sxs-lookup"><span data-stu-id="7d44e-141">You are now logged in using your Twitter credentials:</span></span>

![Webová aplikace: uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="7d44e-143">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="7d44e-143">Troubleshooting</span></span>

* <span data-ttu-id="7d44e-144">**ASP.NET Core 2.x pouze:** pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: musí být Zadaná možnost "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="7d44e-144">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="7d44e-145">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="7d44e-145">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="7d44e-146">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="7d44e-146">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="7d44e-147">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="7d44e-147">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7d44e-148">Další kroky</span><span class="sxs-lookup"><span data-stu-id="7d44e-148">Next steps</span></span>

* <span data-ttu-id="7d44e-149">V tomto článku jsme si ukázali, jak ověřování pomocí Twitteru.</span><span class="sxs-lookup"><span data-stu-id="7d44e-149">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="7d44e-150">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7d44e-150">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="7d44e-151">Po publikování webu do webové aplikace Azure, měli byste resetovat `ConsumerSecret` na portálu pro vývojáře Twitter.</span><span class="sxs-lookup"><span data-stu-id="7d44e-151">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="7d44e-152">Nastavte `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7d44e-152">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="7d44e-153">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="7d44e-153">The configuration system is set up to read keys from environment variables.</span></span>
