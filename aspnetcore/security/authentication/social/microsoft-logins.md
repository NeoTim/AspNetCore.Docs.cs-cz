---
title: Nastavení Microsoft Account externí přihlášení pomocí ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Microsoft do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 4909a0084994654777ad7a6ebda866ac727f0528
ms.sourcegitcommit: 3e94d192b2ed9409fe72e3735e158b333354964c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735749"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="c83a9-103">Nastavení Microsoft Account externí přihlášení pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c83a9-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="c83a9-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c83a9-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c83a9-105">V tomto kurzu se dozvíte, jak povolit vašim uživatelům přihlašovat se pomocí svého účtu Microsoft použitím ukázkového projektu ASP.NET Core 2.0 na vytvoří [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c83a9-105">This tutorial shows you how to enable your users to sign in with their Microsoft account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="c83a9-106">Vytvoření aplikace portálu společnosti Microsoft pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="c83a9-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="c83a9-107">Přejděte do [ https://apps.dev.microsoft.com ](https://apps.dev.microsoft.com) a vytvořte nebo přihlášení účtem Microsoft:</span><span class="sxs-lookup"><span data-stu-id="c83a9-107">Navigate to [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) and create or sign into a Microsoft account:</span></span>

![Přihlaste se dialogové okno](index/_static/MicrosoftDevLogin.png)

<span data-ttu-id="c83a9-109">Pokud ještě nemáte účet Microsoft, klepněte na  **[vytvořte si ho!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span><span class="sxs-lookup"><span data-stu-id="c83a9-109">If you don't already have a Microsoft account, tap **[Create one!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span></span> <span data-ttu-id="c83a9-110">Po přihlášení budete přesměrováni na **Moje aplikace** stránky:</span><span class="sxs-lookup"><span data-stu-id="c83a9-110">After signing in you are redirected to **My applications** page:</span></span>

![Portál pro vývojáře Microsoft otevřít v Microsoft Edge](index/_static/MicrosoftDev.png)

* <span data-ttu-id="c83a9-112">Klepněte na **přidat aplikaci** v pravém horním rohu, zadejte vaše **název_aplikace** a **E-mail kontaktu**:</span><span class="sxs-lookup"><span data-stu-id="c83a9-112">Tap **Add an app** in the upper right corner and enter your **Application Name** and **Contact Email**:</span></span>

![Dialogové okno nové registrace aplikace](index/_static/MicrosoftDevAppCreate.png)

* <span data-ttu-id="c83a9-114">Pro účely tohoto kurzu, zrušte **instalační program s asistencí** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="c83a9-114">For the purposes of this tutorial, clear the **Guided Setup** check box.</span></span>

* <span data-ttu-id="c83a9-115">Klepněte na **vytvořit** a pokračujte **registrace** stránky.</span><span class="sxs-lookup"><span data-stu-id="c83a9-115">Tap **Create** to continue to the **Registration** page.</span></span> <span data-ttu-id="c83a9-116">Zadejte **název** a poznamenejte si hodnotu **Id aplikace**, který použijete jako `ClientId` dále v tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="c83a9-116">Provide a **Name** and note the value of the **Application Id**, which you use as `ClientId` later in the tutorial:</span></span>

![Stránka pro registraci do](index/_static/MicrosoftDevAppReg.png)

* <span data-ttu-id="c83a9-118">Klepněte na **přidat platformy** v **platformy** a vyberte **webové** platformy:</span><span class="sxs-lookup"><span data-stu-id="c83a9-118">Tap **Add Platform** in the **Platforms** section and select the **Web** platform:</span></span>

![Přidat dialog platformy](index/_static/MicrosoftDevAppPlatform.png)

* <span data-ttu-id="c83a9-120">Na novém **webové** platformy části, zadejte adresu URL svého vývoj s `/signin-microsoft` připojí do **adresy URL pro přesměrování** pole (například: `https://localhost:44320/signin-microsoft`).</span><span class="sxs-lookup"><span data-stu-id="c83a9-120">In the new **Web** platform section, enter your development URL with `/signin-microsoft` appended into the **Redirect URLs** field (for example: `https://localhost:44320/signin-microsoft`).</span></span> <span data-ttu-id="c83a9-121">Schéma ověřování Microsoft později v tomto kurzu konfiguruje automaticky zpracovává požadavky na `/signin-microsoft` trasy, která má implementovat tok OAuth:</span><span class="sxs-lookup"><span data-stu-id="c83a9-121">The Microsoft authentication scheme configured later in this tutorial will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow:</span></span>

![Části webové platformy](index/_static/MicrosoftRedirectUri.png)

> [!NOTE]
> <span data-ttu-id="c83a9-123">Segment identifikátoru URI `/signin-microsoft` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c83a9-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="c83a9-124">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middlewaru ověřování společnosti Microsoft prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) třídy.</span><span class="sxs-lookup"><span data-stu-id="c83a9-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

* <span data-ttu-id="c83a9-125">Klepněte na **přidat adresu URL** aby adresa URL byla přidána.</span><span class="sxs-lookup"><span data-stu-id="c83a9-125">Tap **Add URL** to ensure the URL was added.</span></span>

* <span data-ttu-id="c83a9-126">V případě potřeby zadejte další nastavení aplikace a klepněte na **Uložit** v dolní části stránky a uložit změny konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="c83a9-126">Fill out any other application settings if necessary and tap **Save** at the bottom of the page to save changes to app configuration.</span></span>

* <span data-ttu-id="c83a9-127">Při nasazování webu budete potřebovat revidovat **registrace** stránku a nastavit novou veřejnou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c83a9-127">When deploying the site you'll need to revisit the **Registration** page and set a new public URL.</span></span>

## <a name="store-microsoft-application-id-and-password"></a><span data-ttu-id="c83a9-128">Store aplikace Microsoft Id a heslo</span><span class="sxs-lookup"><span data-stu-id="c83a9-128">Store Microsoft Application Id and Password</span></span>

* <span data-ttu-id="c83a9-129">Poznámka: `Application Id` zobrazené na **registrace** stránky.</span><span class="sxs-lookup"><span data-stu-id="c83a9-129">Note the `Application Id` displayed on the **Registration** page.</span></span>

* <span data-ttu-id="c83a9-130">Klepněte na **generovat nové heslo** v **tajných klíčů aplikací** oddílu.</span><span class="sxs-lookup"><span data-stu-id="c83a9-130">Tap **Generate New Password** in the **Application Secrets** section.</span></span> <span data-ttu-id="c83a9-131">Zobrazí pole, kde můžete kopírovat heslo aplikace:</span><span class="sxs-lookup"><span data-stu-id="c83a9-131">This displays a box where you can copy the application password:</span></span>

![Dialogové okno Nový vygenerované heslo](index/_static/MicrosoftDevPassword.png)

<span data-ttu-id="c83a9-133">Propojit citlivá nastavení, jako je Microsoft `Application ID` a `Password` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c83a9-133">Link sensitive settings like Microsoft `Application ID` and `Password` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c83a9-134">Pro účely tohoto kurzu se název tokeny `Authentication:Microsoft:ApplicationId` a `Authentication:Microsoft:Password`.</span><span class="sxs-lookup"><span data-stu-id="c83a9-134">For the purposes of this tutorial, name the tokens `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password`.</span></span>

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="c83a9-135">Konfigurace ověřování pomocí účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="c83a9-135">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="c83a9-136">Šablona projektu použité v tomto kurzu zajistí, že [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) balíček je už nainstalovaný.</span><span class="sxs-lookup"><span data-stu-id="c83a9-136">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) package is already installed.</span></span>

* <span data-ttu-id="c83a9-137">K instalaci tohoto balíčku pomocí sady Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="c83a9-137">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="c83a9-138">Instalace s .NET Core CLI, spusťte v adresáři projektu následující:</span><span class="sxs-lookup"><span data-stu-id="c83a9-138">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="c83a9-139">Přidat službu Microsoft Account v `ConfigureServices` metoda *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="c83a9-139">Add the Microsoft Account service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="c83a9-140">Přidat middlewaru Microsoft Account v `Configure` metoda *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="c83a9-140">Add the Microsoft Account middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

::: moniker-end

<span data-ttu-id="c83a9-141">I když terminologii používané v portálu pro vývojáře Microsoft názvy těchto tokenů `ApplicationId` a `Password`, jsou vystaveny jako `ClientId` a `ClientSecret` v konfiguraci rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c83a9-141">Although the terminology used on Microsoft Developer Portal names these tokens `ApplicationId` and `Password`, they're exposed as `ClientId` and `ClientSecret` to the configuration API.</span></span>

<span data-ttu-id="c83a9-142">Zobrazit [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem Account Microsoft ověřování.</span><span class="sxs-lookup"><span data-stu-id="c83a9-142">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="c83a9-143">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="c83a9-143">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="c83a9-144">Přihlaste se pomocí účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="c83a9-144">Sign in with Microsoft Account</span></span>

<span data-ttu-id="c83a9-145">Spusťte aplikaci a klikněte na tlačítko **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="c83a9-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="c83a9-146">Zobrazí se možnost přihlásit se účtem Microsoft:</span><span class="sxs-lookup"><span data-stu-id="c83a9-146">An option to sign in with Microsoft appears:</span></span>

![Webovou aplikaci protokolu na stránce: Uživatel nebyl ověřen](index/_static/DoneMicrosoft.png)

<span data-ttu-id="c83a9-148">Po kliknutí na Microsoft, budete přesměrováni do společnosti Microsoft pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="c83a9-148">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="c83a9-149">Po přihlášení pomocí Account Microsoftu (pokud ještě nejste přihlášení) se výzva k povolení přístup k informacím:</span><span class="sxs-lookup"><span data-stu-id="c83a9-149">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

![Microsoft dialog ověřování.](index/_static/MicrosoftLogin.png)

<span data-ttu-id="c83a9-151">Klepněte na **Ano** a budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.</span><span class="sxs-lookup"><span data-stu-id="c83a9-151">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="c83a9-152">Nyní jste přihlášeni pomocí svých přihlašovacích údajů společnosti Microsoft:</span><span class="sxs-lookup"><span data-stu-id="c83a9-152">You are now logged in using your Microsoft credentials:</span></span>

![Webové aplikace: Uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="c83a9-154">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="c83a9-154">Troubleshooting</span></span>

* <span data-ttu-id="c83a9-155">Pokud poskytovatel Account Microsoft vás přesměruje na přihlašovací stránce chyby, vezměte na vědomí chyba nadpis a popis parametrů řetězce dotazu přímo po `#` (hashtag) v identifikátoru Uri.</span><span class="sxs-lookup"><span data-stu-id="c83a9-155">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="c83a9-156">I když chybová zpráva vypadá to, že indikovat problém s ověřováním Microsoft, Nejběžnější příčinou je identifikátor Uri neodpovídá žádné z vaší aplikace **identifikátory URI přesměrování** zadaný pro **webové** platformy .</span><span class="sxs-lookup"><span data-stu-id="c83a9-156">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="c83a9-157">**ASP.NET Core 2.x pouze:** Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: Musí být Zadaná možnost "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="c83a9-157">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="c83a9-158">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="c83a9-158">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="c83a9-159">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="c83a9-159">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="c83a9-160">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="c83a9-160">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c83a9-161">Další kroky</span><span class="sxs-lookup"><span data-stu-id="c83a9-161">Next steps</span></span>

* <span data-ttu-id="c83a9-162">V tomto článku jsme si ukázali, jak můžete ověřit s Microsoftem.</span><span class="sxs-lookup"><span data-stu-id="c83a9-162">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="c83a9-163">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c83a9-163">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="c83a9-164">Po publikování webu do webové aplikace Azure, měli byste vytvořit nový `Password` na portálu pro vývojáře společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c83a9-164">Once you publish your web site to Azure web app, you should create a new `Password` in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="c83a9-165">Nastavte `Authentication:Microsoft:ApplicationId` a `Authentication:Microsoft:Password` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c83a9-165">Set the `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password` as application settings in the Azure portal.</span></span> <span data-ttu-id="c83a9-166">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="c83a9-166">The configuration system is set up to read keys from environment variables.</span></span>
