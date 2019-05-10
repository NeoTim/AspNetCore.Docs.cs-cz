---
title: Nastavení externí přihlášení pomocí ASP.NET Core na twitteru
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Twitter do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 5/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 486d58b600ca5326a0728de40bb386fbb9440f67
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516874"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="6a0ec-103">Nastavení externí přihlášení pomocí ASP.NET Core na twitteru</span><span class="sxs-lookup"><span data-stu-id="6a0ec-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="6a0ec-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6a0ec-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6a0ec-105">Tento příklad ukazuje, jak uživatelům umožnit [Přihlaste se pomocí svého účtu Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) ukázkový projekt v ASP.NET Core 2.2 využitím vytvořené na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6a0ec-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="6a0ec-106">Vytvořit aplikaci na Twitteru</span><span class="sxs-lookup"><span data-stu-id="6a0ec-106">Create the app in Twitter</span></span>

* <span data-ttu-id="6a0ec-107">Přejděte do [ https://apps.twitter.com/ ](https://apps.twitter.com/) a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="6a0ec-108">Pokud ještě nemáte účet na Twitteru, použijte **[zaregistrujte se hned teď](https://twitter.com/signup)** odkaz pro vytvoření.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="6a0ec-109">Klepněte na **vytvořit novou aplikaci** a vyplnit **název**, **popis** a veřejné **webu** identifikátoru URI (může to být dočasný dokud Tento název domény zaregistrujte):</span><span class="sxs-lookup"><span data-stu-id="6a0ec-109">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="6a0ec-110">Zadejte identifikátor URI vývoje s `/signin-twitter` připojí do **identifikátory URI pro přesměrování platný OAuth** pole (například: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="6a0ec-110">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="6a0ec-111">Schéma ověřování Twitteru nakonfigurovat později v tomto příkladu bude automaticky zpracovávat požadavky na `/signin-twitter` trasy, která má implementovat tok OAuth.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-111">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6a0ec-112">Segment identifikátoru URI `/signin-twitter` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Twitteru.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-112">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="6a0ec-113">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování služby Twitter prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) Třída.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-113">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="6a0ec-114">Vyplňte zbývající části formuláře a klepněte na **vytvoření aplikace Twitter**.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-114">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="6a0ec-115">Podrobnosti o nové aplikace se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="6a0ec-115">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="6a0ec-116">Ukládání rozhraní Twitter API pro příjemce klíče a tajného kódu</span><span class="sxs-lookup"><span data-stu-id="6a0ec-116">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="6a0ec-117">Spusťte následující příkazy zabezpečeně ukládat `ClientId` a `ClientSecret` pomocí [manažera tajných](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="6a0ec-117">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

<span data-ttu-id="6a0ec-118">Propojit citlivá nastavení, jako je Twitter `Consumer Key` a `Consumer Secret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="6a0ec-118">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="6a0ec-119">Pro účely tohoto příkladu název tokeny `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret`.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-119">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="6a0ec-120">Tyto tokeny můžete najít na **klíče a přístupové tokeny** kartu po vytvoření nové aplikace pro Twitter:</span><span class="sxs-lookup"><span data-stu-id="6a0ec-120">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="6a0ec-121">Konfigurace ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="6a0ec-121">Configure Twitter Authentication</span></span>

<span data-ttu-id="6a0ec-122">Přidat službu Twitter `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="6a0ec-122">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="6a0ec-123">Zobrazit [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování Twitteru.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-123">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="6a0ec-124">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-124">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="6a0ec-125">Přihlaste se pomocí Twitteru</span><span class="sxs-lookup"><span data-stu-id="6a0ec-125">Sign in with Twitter</span></span>

<span data-ttu-id="6a0ec-126">Spusťte aplikaci a vyberte **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-126">Run the app and select **Log in**.</span></span> <span data-ttu-id="6a0ec-127">Zobrazí se možnost přihlásit se přes Twitter:</span><span class="sxs-lookup"><span data-stu-id="6a0ec-127">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="6a0ec-128">Kliknutím na **Twitteru** přesměruje na Twitter pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="6a0ec-128">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="6a0ec-129">Po zadání vaše přihlašovací údaje k Twitteru, budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-129">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="6a0ec-130">Nyní jste přihlášeni pomocí svých přihlašovacích údajů Twitter:</span><span class="sxs-lookup"><span data-stu-id="6a0ec-130">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="6a0ec-131">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="6a0ec-131">Troubleshooting</span></span>

* <span data-ttu-id="6a0ec-132">**ASP.NET Core 2.x pouze:** Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: Musí být Zadaná možnost "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-132">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="6a0ec-133">Šablona projektu používané v tomto příkladu zajistí, že se to.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-133">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="6a0ec-134">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-134">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="6a0ec-135">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-135">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6a0ec-136">Další kroky</span><span class="sxs-lookup"><span data-stu-id="6a0ec-136">Next steps</span></span>

* <span data-ttu-id="6a0ec-137">V tomto článku jsme si ukázali, jak ověřování pomocí Twitteru.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-137">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="6a0ec-138">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6a0ec-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="6a0ec-139">Po publikování webu do webové aplikace Azure, měli byste resetovat `ConsumerSecret` na portálu pro vývojáře Twitter.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-139">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="6a0ec-140">Nastavte `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-140">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="6a0ec-141">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="6a0ec-141">The configuration system is set up to read keys from environment variables.</span></span>