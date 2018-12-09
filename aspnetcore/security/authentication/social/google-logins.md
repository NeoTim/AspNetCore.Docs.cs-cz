---
title: Nastavení Google externí přihlášení v technologii ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Google do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/11/2018
uid: security/authentication/google-logins
ms.openlocfilehash: e5deda5d521643e3155be00f4630a86c6a82575c
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121528"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="fba21-103">Nastavení Google externí přihlášení v technologii ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fba21-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="fba21-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fba21-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fba21-105">V tomto kurzu se dozvíte, jak mohou uživatelé přihlásit s účtem Google + použitím ukázkového projektu ASP.NET Core 2.0 vytvořené na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="fba21-105">This tutorial shows you how to enable your users to sign in with their Google+ account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="fba21-106">Začneme podle [oficiální kroky](https://developers.google.com/identity/sign-in/web/devconsole-project) k vytvoření nové aplikace konzoly rozhraní API Google.</span><span class="sxs-lookup"><span data-stu-id="fba21-106">We start by following the [official steps](https://developers.google.com/identity/sign-in/web/devconsole-project) to create a new app in Google API Console.</span></span>

## <a name="create-the-app-in-google-api-console"></a><span data-ttu-id="fba21-107">Vytvoření aplikace v konzole rozhraní API Google</span><span class="sxs-lookup"><span data-stu-id="fba21-107">Create the app in Google API Console</span></span>

* <span data-ttu-id="fba21-108">Přejděte do [ https://console.developers.google.com/projectselector/apis/library ](https://console.developers.google.com/projectselector/apis/library) a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="fba21-108">Navigate to [https://console.developers.google.com/projectselector/apis/library](https://console.developers.google.com/projectselector/apis/library) and sign in.</span></span> <span data-ttu-id="fba21-109">Pokud ještě nemáte účet Google, použijte **další možnosti** > **[vytvořit účet](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)**  odkaz pro vytvoření:</span><span class="sxs-lookup"><span data-stu-id="fba21-109">If you don't already have a Google account, use **More options** > **[Create account](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)** link to create one:</span></span>

![Konzola rozhraní API Google](index/_static/GoogleConsoleLogin.png)

* <span data-ttu-id="fba21-111">Budete přesměrováni na **správce rozhraní API knihovny** stránky:</span><span class="sxs-lookup"><span data-stu-id="fba21-111">You are redirected to **API Manager Library** page:</span></span>

![Cílová na stránce rozhraní API Správce knihovny](index/_static/GoogleConsoleSwitchboard.png)

* <span data-ttu-id="fba21-113">Klepněte na **vytvořit** a zadejte vaše **název projektu**:</span><span class="sxs-lookup"><span data-stu-id="fba21-113">Tap **Create** and enter your **Project name**:</span></span>

![Dialogové okno nového projektu](index/_static/GoogleConsoleNewProj.png)

* <span data-ttu-id="fba21-115">Po přijetí dialogového okna, budete přesměrováni zpět na stránku knihovny umožňuje zvolit funkce pro novou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fba21-115">After accepting the dialog, you are redirected back to the Library page allowing you to choose features for your new app.</span></span> <span data-ttu-id="fba21-116">Najít **rozhraní API Google +** v seznamu a klikněte na odkaz Přidat funkci rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="fba21-116">Find **Google+ API** in the list and click on its link to add the API feature:</span></span>

![Vyhledejte "rozhraní API Google +" na stránce rozhraní API Správce knihovny](index/_static/GoogleConsoleChooseApi.png)

* <span data-ttu-id="fba21-118">Zobrazí se stránka pro nově přidané rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="fba21-118">The page for the newly added API is displayed.</span></span> <span data-ttu-id="fba21-119">Klepněte na **povolit** přidání funkce do vaší aplikace Google + přihlásit ve funkci:</span><span class="sxs-lookup"><span data-stu-id="fba21-119">Tap **Enable** to add Google+ sign in feature to your app:</span></span>

![Cílová na stránce Správce rozhraní API Google + API](index/_static/GoogleConsoleEnableApi.png)

* <span data-ttu-id="fba21-121">Po povolení rozhraní API, klepněte na **Vytvořte přihlašovací údaje** konfigurace tajné klíče:</span><span class="sxs-lookup"><span data-stu-id="fba21-121">After enabling the API, tap **Create credentials** to configure the secrets:</span></span>

![Vytvoření tlačítka přihlašovací údaje na stránce Správce rozhraní API Google + API](index/_static/GoogleConsoleGoCredentials.png)

* <span data-ttu-id="fba21-123">Zvolte:</span><span class="sxs-lookup"><span data-stu-id="fba21-123">Choose:</span></span>
  * <span data-ttu-id="fba21-124">**Google + API**</span><span class="sxs-lookup"><span data-stu-id="fba21-124">**Google+ API**</span></span>
  * <span data-ttu-id="fba21-125">**Webový server (například node.js, Tomcat)**, a</span><span class="sxs-lookup"><span data-stu-id="fba21-125">**Web server (e.g. node.js, Tomcat)**, and</span></span>
  * <span data-ttu-id="fba21-126">**Uživatelská data**:</span><span class="sxs-lookup"><span data-stu-id="fba21-126">**User data**:</span></span>

![Stránka přihlašovací údaje správce rozhraní API: Podívejte se, jaké druhy přihlašovací údaje můžete potřebovat panel](index/_static/GoogleConsoleChooseCred.png)

* <span data-ttu-id="fba21-128">Klepněte na **jaké přihlašovací údaje potřebuji?** který přejde na druhý krok konfigurace aplikace **vytvoření ID klienta OAuth 2.0**:</span><span class="sxs-lookup"><span data-stu-id="fba21-128">Tap **What credentials do I need?** which takes you to the second step of app configuration, **Create an OAuth 2.0 client ID**:</span></span>

![Stránka přihlašovací údaje správce rozhraní API: vytvoření ID klienta OAuth 2.0](index/_static/GoogleConsoleCreateClient.png)

* <span data-ttu-id="fba21-130">Protože se právě jednu funkci (přihlášení), abychom mohli zadat stejné vytváříme projektu Google + **název** pro ID klienta OAuth 2.0, jako jste použili pro projekt.</span><span class="sxs-lookup"><span data-stu-id="fba21-130">Because we are creating a Google+ project with just one feature (sign in), we can enter the same **Name** for the OAuth 2.0 client ID as the one we used for the project.</span></span>

* <span data-ttu-id="fba21-131">Zadejte identifikátor URI vývoje s `/signin-google` připojí do **identifikátory URI pro přesměrování autorizovaní** pole (například: `https://localhost:44320/signin-google`).</span><span class="sxs-lookup"><span data-stu-id="fba21-131">Enter your development URI with `/signin-google` appended into the **Authorized redirect URIs** field (for example: `https://localhost:44320/signin-google`).</span></span> <span data-ttu-id="fba21-132">Ověřování Google později v tomto kurzu konfiguruje automaticky zpracovává požadavky na `/signin-google` trasy, která má implementovat tok OAuth.</span><span class="sxs-lookup"><span data-stu-id="fba21-132">The Google authentication configured later in this tutorial will automatically handle requests at `/signin-google` route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="fba21-133">Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Google.</span><span class="sxs-lookup"><span data-stu-id="fba21-133">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="fba21-134">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování Google přes zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) třídy.</span><span class="sxs-lookup"><span data-stu-id="fba21-134">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

* <span data-ttu-id="fba21-135">Stiskněte klávesu TAB pro přidání **identifikátory URI pro přesměrování autorizovaní** položka.</span><span class="sxs-lookup"><span data-stu-id="fba21-135">Press TAB to add the **Authorized redirect URIs** entry.</span></span>

* <span data-ttu-id="fba21-136">Klepněte na **vytvořit ID klienta**, což vás přesměruje na třetí krok **nastavení OAuth 2.0 obrazovkami pro vyjádření souhlasu**:</span><span class="sxs-lookup"><span data-stu-id="fba21-136">Tap **Create client ID**, which takes you to the third step, **Set up the OAuth 2.0 consent screen**:</span></span>

![Stránka přihlašovací údaje správce rozhraní API: nastavení obrazovce pro vyjádření souhlasu OAuth 2.0](index/_static/GoogleConsoleAddCred.png)

* <span data-ttu-id="fba21-138">Zadejte vaše veřejné **e-mailová adresa** a **název produktu** uvedené pro vaši aplikaci při Google + se zobrazí výzva k přihlášení.</span><span class="sxs-lookup"><span data-stu-id="fba21-138">Enter your public facing **Email address** and the **Product name** shown for your app when Google+ prompts the user to sign in.</span></span> <span data-ttu-id="fba21-139">Další možnosti jsou k dispozici v rámci **další možnosti vlastního nastavení**.</span><span class="sxs-lookup"><span data-stu-id="fba21-139">Additional options are available under **More customization options**.</span></span>

* <span data-ttu-id="fba21-140">Klepněte na **pokračovat** k přejděte k poslednímu kroku **stáhnout přihlašovací údaje**:</span><span class="sxs-lookup"><span data-stu-id="fba21-140">Tap **Continue** to proceed to the last step, **Download credentials**:</span></span>

![Stránka přihlašovací údaje správce rozhraní API: Stáhněte si přihlašovací údaje](index/_static/GoogleConsoleFinish.png)

* <span data-ttu-id="fba21-142">Klepněte na **Stáhnout** uložit soubor JSON s tajných klíčů aplikací a **provádí** nezbytných k dokončení vytvoření nové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fba21-142">Tap **Download** to save a JSON file with application secrets, and **Done** to complete creation of the new app.</span></span>

* <span data-ttu-id="fba21-143">Při nasazování webu budete potřebovat revidovat **konzoly Google** a zaregistrujte novou veřejnou adresu url.</span><span class="sxs-lookup"><span data-stu-id="fba21-143">When deploying the site you'll need to revisit the **Google Console** and register a new public url.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="fba21-144">Store Google ClientID a ClientSecret</span><span class="sxs-lookup"><span data-stu-id="fba21-144">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="fba21-145">Propojit citlivá nastavení, jako je Google `Client ID` a `Client Secret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="fba21-145">Link sensitive settings like Google `Client ID` and `Client Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="fba21-146">Pro účely tohoto kurzu se název tokeny `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret`.</span><span class="sxs-lookup"><span data-stu-id="fba21-146">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`.</span></span>

<span data-ttu-id="fba21-147">Hodnoty pro tyto tokeny najdete v souboru JSON si stáhli v předchozím kroku v části `web.client_id` a `web.client_secret`.</span><span class="sxs-lookup"><span data-stu-id="fba21-147">The values for these tokens can be found in the JSON file downloaded in the previous step under `web.client_id` and `web.client_secret`.</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="fba21-148">Konfigurace ověřování Google</span><span class="sxs-lookup"><span data-stu-id="fba21-148">Configure Google Authentication</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fba21-149">Přidat služby Google v `ConfigureServices` metoda *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="fba21-149">Add the Google service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Authentication:Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Authentication:Google:ClientSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="fba21-150">Šablona projektu použité v tomto kurzu zajistí, že [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) instalaci balíčku.</span><span class="sxs-lookup"><span data-stu-id="fba21-150">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) package is installed.</span></span>

* <span data-ttu-id="fba21-151">K instalaci tohoto balíčku pomocí sady Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="fba21-151">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="fba21-152">Instalace s .NET Core CLI, spusťte v adresáři projektu následující:</span><span class="sxs-lookup"><span data-stu-id="fba21-152">To install with .NET Core CLI, execute the following in your project directory:</span></span>

`dotnet add package Microsoft.AspNetCore.Authentication.Google`

<span data-ttu-id="fba21-153">Přidat v middlewaru Google `Configure` metoda *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="fba21-153">Add the Google middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

::: moniker-end

<span data-ttu-id="fba21-154">Zobrazit [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování Google.</span><span class="sxs-lookup"><span data-stu-id="fba21-154">See the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="fba21-155">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="fba21-155">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-google"></a><span data-ttu-id="fba21-156">Přihlásit se přes Google</span><span class="sxs-lookup"><span data-stu-id="fba21-156">Sign in with Google</span></span>

<span data-ttu-id="fba21-157">Spusťte aplikaci a klikněte na tlačítko **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="fba21-157">Run your application and click **Log in**.</span></span> <span data-ttu-id="fba21-158">Zobrazí se možnost přihlásit se přes Google:</span><span class="sxs-lookup"><span data-stu-id="fba21-158">An option to sign in with Google appears:</span></span>

![Webové aplikaci běžící v Microsoft Edge: uživatel nebyl ověřen](index/_static/DoneGoogle.png)

<span data-ttu-id="fba21-160">Když kliknete na Googlu, budete přesměrováni do Googlu pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="fba21-160">When you click on Google, you are redirected to Google for authentication:</span></span>

![Dialog ověřování Google](index/_static/GoogleLogin.png)

<span data-ttu-id="fba21-162">Jakmile zadáte svoje přihlašovací údaje Google, pak budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.</span><span class="sxs-lookup"><span data-stu-id="fba21-162">After entering your Google credentials, then you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="fba21-163">Nyní jste přihlášeni pomocí svých přihlašovacích údajů Google:</span><span class="sxs-lookup"><span data-stu-id="fba21-163">You are now logged in using your Google credentials:</span></span>

![Webové aplikaci běžící v Microsoft Edge: uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="fba21-165">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="fba21-165">Troubleshooting</span></span>

* <span data-ttu-id="fba21-166">Pokud se zobrazí `403 (Forbidden)` chybovou stránku z vaší vlastní aplikace při spuštění v režimu pro vývoj (nebo přerušení do ladicího programu ke stejné chybě), ujistěte se, že **rozhraní API Google +** byla povolena v **správce rozhraní API knihovny** pomocí následujících kroků uvedených v tomto [starších na této stránce](#create-the-app-in-google-api-console).</span><span class="sxs-lookup"><span data-stu-id="fba21-166">If you receive a `403 (Forbidden)` error page from your own app when running in development mode (or break into the debugger with the same error), ensure that **Google+ API** has been enabled in the **API Manager Library** by following the steps listed [earlier on this page](#create-the-app-in-google-api-console).</span></span> <span data-ttu-id="fba21-167">Pokud nebude fungovat přihlašování a nezobrazuje se nějaké chyby, přepněte do režimu vývoj usnadnit problém ladit.</span><span class="sxs-lookup"><span data-stu-id="fba21-167">If the sign in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="fba21-168">**ASP.NET Core 2.x pouze:** pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: musí být Zadaná možnost "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="fba21-168">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="fba21-169">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="fba21-169">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="fba21-170">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="fba21-170">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="fba21-171">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="fba21-171">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fba21-172">Další kroky</span><span class="sxs-lookup"><span data-stu-id="fba21-172">Next steps</span></span>

* <span data-ttu-id="fba21-173">V tomto článku jsme si ukázali, jak můžete ověřit s Google.</span><span class="sxs-lookup"><span data-stu-id="fba21-173">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="fba21-174">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="fba21-174">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="fba21-175">Po publikování webu do webové aplikace Azure, měli byste resetovat `ClientSecret` v konzole rozhraní API Google.</span><span class="sxs-lookup"><span data-stu-id="fba21-175">Once you publish your web site to Azure web app, you should reset the `ClientSecret` in the Google API Console.</span></span>

* <span data-ttu-id="fba21-176">Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="fba21-176">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="fba21-177">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="fba21-177">The configuration system is set up to read keys from environment variables.</span></span>
